lmkd流程图
sequenceDiagram
autonumber
participant AMS as AMS
participant PL as ProcessList
participant KPSI as Kernel PSI
participant KVM as Kernel vmpressure
participant LMK as lmkd main
participant CTRL as ctrl handler
participant PSI as psi handler
participant VMP as vmpressure handler
participant PRIO as procprio handler
participant KILL as killer

%% Startup
AMS->>LMK: main - lmkd.c - entry start lmkd
LMK->>LMK: init - lmkd.c - init data and minfree
LMK->>LMK: init_sock - lmkd.c - create ctrl socket
LMK->>LMK: init_cgroup - lmkd.c - setup memcg paths
LMK->>LMK: epoll_main_loop - lmkd.c - register fds and loop

%% AMS & ProcessList scoring
AMS->>PL: computeOomAdjLocked - ProcessList.java - compute oom_score_adj
PL-->>AMS: return oom_score_adj
AMS->>LMK: send procprio via ctrl socket
LMK->>PRIO: procprio_handler - lmkd.c - handle priority message
PRIO->>PRIO: proc_setprio - lmkd.c - update procprio table

%% Control commands
AMS-->>LMK: ctrl command minfree or procprio or kill
LMK->>CTRL: ctrl_handler - lmkd.c - receive command
CTRL->>CTRL: ctrl_command_handler - lmkd.c - parse command
alt set minfree
  CTRL->>CTRL: cmd_target - lmkd.c - update minfree thresholds
else update procprio
  CTRL->>PRIO: cmd_procprio - lmkd.c - refresh priority table
else force kill
  CTRL->>KILL: cmd_kill - lmkd.c - trigger kill path
end

%% PSI event with decision gates
KPSI-->>LMK: PSI event from /proc/pressure/memory
LMK->>PSI: psi_event_handler - lmkd_psi.c - psi event entry
PSI->>PSI: handle_psi_event - lmkd_psi.c - read psi metrics
PSI->>PSI: evaluate_thrashing - lmkd_psi.c - compute thrashing ratio
alt Gate1 threshold reached
  PSI->>PSI: compute distance_to_threshold - hysteresis
  alt Gate2 distance small enough
    PSI->>PSI: check_backoff - rate limit
    alt Gate3 backoff expired
      alt Gate4 thrashing high
        PSI->>KILL: do_kill - lmkd.c - enter kill path
      else thrashing low
        PSI-->>LMK: return to loop
      end
    else backoff not expired
      PSI-->>LMK: return to loop
    end
  else distance too large
    PSI-->>LMK: return to loop
  end
else threshold not reached
  PSI-->>LMK: return to loop
end

%% vmpressure path
KVM-->>LMK: vmpressure event
LMK->>VMP: vmpressure_handler - lmkd_vmpressure.c - entry
VMP->>VMP: handle_vmpressure - lmkd_vmpressure.c - process pressure level
VMP->>VMP: update_reclaim_targets - lmkd_vmpressure.c - tune reclaim
VMP-->>LMK: return to loop

%% Kill path using AMS scores
KILL->>KILL: kill_some_processes - lmkd.c - collect meminfo and psi
KILL->>KILL: find_victim_process - lmkd.c - build candidate list
KILL->>KILL: proc_adj_sort - lmkd.c - sort by adj rss swapin refault
KILL->>KILL: select_and_kill_process - lmkd.c - pick victim
KILL->>KILL: send_sigkill - lmkd.c - send SIGKILL
KILL->>KILL: reclaim_pages_wait - lmkd.c - wait reclaim
KILL->>AMS: stats_write - lmkd_stats.c - report to statsd and AM
KILL-->>LMK: back to epoll loop

lmkd的优化思路
0. 目标与总览

目标：将 lmkd 从“只会杀”升级为动作阶梯（限流→降温→杀），支持cgroup 保护/处置、多信号融合（PSI+IO/CPU+内核统计）、自适应 backoff、可观测性完善，并在低端/无 PSI内核上有兜底。

改动涉及文件：

lmkd.c（事件循环 / ctrl / kill / 选择器 / backoff）

lmkd_psi.c（PSI 读取与 Gate 判定，融合 IO/CPU PSI）

lmkd_vmpressure.c（阈值动态调整）

lmkd_stats.c（统计扩展）

新增：lmkd_cgroup.c/.h（cgroup v2 写入与组操作）、lmkd_actions.c/.h（动作阶梯与 pre-actions）、lmkd_config.c/.h（属性/设备策略装载）

编译脚本：修改 Android.bp 增加新源文件与 LOCAL_CFLAGS 开关。

1) 动作阶梯（pre-actions hook）

思路：触发 Gate 后先进行「温和动作」：对候选的低优先级进程/组写 memory.high、对其地址空间做 process_madvise(MADV_COLD/PAGEOUT) 或 Compaction；仅在压力未缓解时才进入 kill。

新增：lmkd_actions.h/.c
``
// lmkd_actions.h
#pragma once
#include <stdbool.h>
#include <sys/types.h>

typedef enum {
  ACT_NONE = 0,
  ACT_SET_MEMORY_HIGH,
  ACT_MADVISE_COLD,
  ACT_MADVISE_PAGEOUT,
  ACT_GROUP_THROTTLE,
} action_t;

typedef struct {
  bool   enable;
  float  memory_high_ratio;   // 相对进程RSS或cgroup current的比例(如0.8)
  size_t cold_bytes;          // MADV_COLD/PAGEOUT 的目标字节数(0=全地址空间)
  int    cooldown_ms;         // pre-action后观察窗口
} preaction_policy_t;

bool preactions_try_throttle_pid(pid_t pid, const preaction_policy_t* pol);
bool preactions_try_group_throttle(const char* cg_path, const preaction_policy_t* pol);
bool preactions_try_madvise_cold(pid_t pid, size_t bytes);
``

``
// lmkd_actions.c (核心片段)
#include "lmkd_actions.h"
#include "lmkd_cgroup.h"
#include <unistd.h>
#include <sys/syscall.h>
#include <linux/membarrier.h>

// 简化：通过 /proc/<pid>/cgroup 解析出 cgroup v2 路径
static bool throttle_pid_cgroup(pid_t pid, float ratio) {
  char cg[PATH_MAX];
  if (!cg2_path_by_pid(pid, cg, sizeof(cg))) return false;
  // 读取当前使用量 memory.current
  long cur = cg2_read_long(cg, "memory.current");
  long high = (long)(cur * ratio);
  // 写 memory.high 节流
  return cg2_write_long(cg, "memory.high", high);
}

bool preactions_try_throttle_pid(pid_t pid, const preaction_policy_t* pol) {
  if (!pol || !pol->enable) return false;
  return throttle_pid_cgroup(pid, pol->memory_high_ratio);
}

bool preactions_try_group_throttle(const char* cg, const preaction_policy_t* pol) {
  if (!pol || !pol->enable) return false;
  long cur = cg2_read_long(cg, "memory.current");
  long high = (long)(cur * pol->memory_high_ratio);
  return cg2_write_long(cg, "memory.high", high);
}

static int sys_process_madvise(int pidfd, void* addr, size_t len, int advice, unsigned long flag) {
#ifdef SYS_process_madvise
  return syscall(SYS_process_madvise, pidfd, addr, len, advice, flag);
#else
  return -1;
#endif
}

bool preactions_try_madvise_cold(pid_t pid, size_t bytes) {
  // 通过 pidfd_open + process_madvise 对整个进程做 MADV_COLD/PAGEOUT
#ifdef SYS_pidfd_open
  int pidfd = syscall(SYS_pidfd_open, pid, 0);
  if (pidfd < 0) return false;
  // 简化：对 NULL 地址传递内核扩展（若不支持则需要遍历maps）
  int rc = sys_process_madvise(pidfd, NULL, bytes, 20 /*MADV_COLD*/, 0);
  close(pidfd);
  return rc == 0;
#else
  return false;
#endif
}
``
在 lmkd.c 的 kill 流程前插入 pre-actions
``
// lmkd.c 伪代码（在 do_kill() 里开头）
#include "lmkd_actions.h"
#include "lmkd_config.h"

static preaction_policy_t g_prepol;

static bool maybe_preactions(struct proc* cand) {
  if (!g_prepol.enable) return false;
  bool any = false;
  // 只对可牺牲 adj（如 >= CACHED_APP_MIN_ADJ）应用
  if (cand->oom_adj >= g_cfg.preaction_min_adj) {
    any |= preactions_try_throttle_pid(cand->pid, &g_prepol);
    if (g_cfg.enable_madvise) {
      any |= preactions_try_madvise_cold(cand->pid, g_prepol.cold_bytes);
    }
  }
  return any;
}

// do_kill() 的开头
if (maybe_preactions(best)) {
  // 观察短窗口，若 PSI 下降则短路
  if (psi_cooldown_ok(g_prepol.cooldown_ms)) {
    LMK_LOG(I, "preaction succeeded, skip kill pid=%d", best->pid);
    record_backoff(/*light*/true);
    return 0;
  }
}
``
2) 按 cgroup 的保护与处置（前台保护、组降级/杀组）
新增：lmkd_cgroup.h/.c（v2 辅助）
``
// lmkd_cgroup.h
bool cg2_path_by_pid(pid_t pid, char* dst, size_t cap);   // 解析 /proc/<pid>/cgroup
long cg2_read_long(const char* cg, const char* knob);
bool cg2_write_long(const char* cg, const char* knob, long val);
bool cg2_write_str(const char* cg, const char* knob, const char* s);
bool cg2_kill_group(const char* cg, int sig);             // 枚举 cgroup.procs 全杀
``
``
// lmkd_cgroup.c 核心片段
bool cg2_kill_group(const char* cg, int sig) {
  char path[PATH_MAX]; snprintf(path, sizeof(path), "%s/cgroup.procs", cg);
  FILE* f = fopen(path, "re"); if (!f) return false;
  pid_t p; bool ok = false;
  while (fscanf(f, "%d", &p) == 1) { kill(p, sig); ok = true; }
  fclose(f);
  return ok;
}
``
前台/关键服务保护（在 procprio_handler() 同步 cgroup knobs）
``
// lmkd.c 片段：当接收 adj 更新时，同时设置 cgroup 保护
if (adj <= g_cfg.fg_protect_adj) {
  char cg[PATH_MAX];
  if (cg2_path_by_pid(pid, cg, sizeof(cg))) {
    // 保护前台：避免回收
    cg2_write_long(cg, "memory.min", g_cfg.fg_memory_min_bytes);   // 强保护
    cg2_write_long(cg, "memory.low", g_cfg.fg_memory_low_bytes);   // 软保护
    cg2_write_str(cg, "memory.oom.group", "1");                    // 组处置一致性
  }
}
``
杀组（当候选进程有强耦合子进程）
``
// select_and_kill_process() 内
if (g_cfg.kill_by_group) {
  char cg[PATH_MAX];
  if (cg2_path_by_pid(victim->pid, cg, sizeof(cg))) {
    cg2_write_str(cg, "memory.oom.group", "1");
    cg2_kill_group(cg, SIGKILL);
    return;
  }
}
kill(victim->pid, SIGKILL);
``
3) 多信号融合（PSI + IO/CPU PSI + 内核统计）

目标：减少误杀（例如 IO 堵塞导致 PSI 高）。在 evaluate_thrashing() 融合 IO PSI、CPU PSI 与内核统计（pgscan/pgsteal/refault/swapin 速率），形成 stress_score。

在 lmkd_psi.c 扩展
`
typedef struct {
  float mem_psi;   // memory PSI some or full (一段时间窗口)
  float io_psi;    // /proc/pressure/io
  float cpu_psi;   // /proc/pressure/cpu
  float refault_rate; // 通过 /proc/vmstat 计算
  float swapin_rate;  // /proc/vmstat
} signal_vec_t;

static float fuse_stress_score(const signal_vec_t* s) {
  // 权重可配置：属性或者 device overlay
  const float wm = g_cfg.w_mem_psi;
  const float wi = g_cfg.w_io_psi;
  const float wc = g_cfg.w_cpu_psi;
  const float wr = `g_cfg.w_refault;`
  const float ws = g_cfg.w_swapin;
  return wm*s->mem_psi + wi*s->io_psi + wc*s->cpu_psi + wr*s->refault_rate + ws*s->swapin_rate;
}

// evaluate_thrashing() 内
signal_vec_t sv = read_all_signals(window_ms);
float stress = fuse_stress_score(&sv);
bool thrashing_high = (stress >= g_cfg.thrashing_score_thresh);
`
4) Backoff/退火策略升级（自适应）
在 lmkd.c 维护 backoff
`
typedef struct {
  uint64_t last_action_ms;
  uint32_t cur_backoff_ms;    // 当前冷却
} backoff_state_t;

static backoff_state_t g_bk;

static void record_backoff(bool light) {
  g_bk.last_action_ms = now_ms();
  // 自适应：若过去 T 内动作多次，则指数退避
  if (!light) g_bk.cur_backoff_ms = min(g_bk.cur_backoff_ms * 2, g_cfg.backoff_ms_max);
  else g_bk.cur_backoff_ms = max(g_cfg.backoff_ms_min, g_bk.cur_backoff_ms / 2);
}

static bool backoff_expired(void) {
  return (now_ms() - g_bk.last_action_ms) >= g_bk.cur_backoff_ms;
}
`
PSI Gate3 接入 backoff
`
// lmkd_psi.c 的 Gate3
if (!backoff_expired()) return false; // 不杀，等冷却
`
5) 候选排序增强（策略表）
在 lmkd.c 的排序器替换为加权多因子
`
typedef struct {
  int   adj;           // 来自 AMS
  long  rss_kb;
  float refault_rate;
  float swapin_rate;
  int   recent_ui_score; // 最近前台历史，需从 AMS 或 input stats 注入
} victim_feat_t;

static float victim_score(const victim_feat_t* f) {
  // adj 越大越容易被杀；recent_ui_score 越大越保留
  return g_cfg.w_adj * f->adj
       + g_cfg.w_rss * (f->rss_kb/1024.0f)
       + g_cfg.w_refault * f->refault_rate
       + g_cfg.w_swapin * f->swapin_rate
       - g_cfg.w_ui * f->recent_ui_score;
}

// sort candidates
qsort(cands, n, sizeof(*cands), cmp_by_victim_score);
`
6) 可观测性：动作阶梯与原因上报
在 lmkd_stats.c 扩展事件枚举与字段
`
// 增加 PRE_THROTTLE / PRE_COLD / KILL_GROUP 等类型
stats_write_event(EVENT_PREACTION_THROTTLE, pid, stress, psi, backoff_ms);
stats_write_event(EVENT_PREACTION_COLD, pid, bytes, stress);
stats_write_event(EVENT_KILL, pid, reason_code, adj, score);
`
7) 低端机兜底（无 PSI/裁剪内核）
在 lmkd_psi.c 检测 PSI 不可用时使用 earlyoom 风格触发
`
if (!psi_available) {
  long mem_avail = read_memavailable_kb();
  long swap_free = read_swapfree_kb();
  if (mem_avail < g_cfg.low_mem_kb && swap_free < g_cfg.low_swap_kb)
      trigger_kill_path(/*low_end=*/true);
}
`
8) 配置装载与属性开关
新增：lmkd_config.h/.c
`
typedef struct {
  bool preaction_enable;
  int  preaction_min_adj;
  bool enable_madvise;
  int  preaction_cooldown_ms;
  float memory_high_ratio;

  // weights & thresholds
  float w_mem_psi, w_io_psi, w_cpu_psi, w_refault, w_swapin;
  float thrashing_score_thresh;

  // backoff
  int backoff_ms_min, backoff_ms_max;

  // protect & group
  int fg_protect_adj;
  long fg_memory_min_bytes, fg_memory_low_bytes;
  bool kill_by_group;

  // low end fallback
  long low_mem_kb, low_swap_kb;

  // victim scoring
  float w_adj, w_rss, w_ui;
} lmkd_config_t;

extern lmkd_config_t g_cfg;
bool lmkd_load_config_from_props(void);
`
`
// lmkd_config.c 片段：从 system properties 读取（示例名）
g_cfg.preaction_enable     = property_get_bool("lmkd.pre.enable", true);
g_cfg.enable_madvise       = property_get_bool("lmkd.pre.madvise", false);
g_cfg.memory_high_ratio    = property_get_float("lmkd.pre.memhigh.ratio", 0.85f);
g_cfg.preaction_min_adj    = property_get_int32("lmkd.pre.minadj", 800);
g_cfg.preaction_cooldown_ms= property_get_int32("lmkd.pre.cooldown.ms", 30);
g_cfg.w_mem_psi            = property_get_float("lmkd.w.mempsi", 1.0f);
g_cfg.w_io_psi             = property_get_float("lmkd.w.iopsi", 0.5f);
g_cfg.w_cpu_psi            = property_get_float("lmkd.w.cpupsi", 0.2f);
g_cfg.w_refault            = property_get_float("lmkd.w.refault", 0.8f);
g_cfg.w_swapin             = property_get_float("lmkd.w.swapin", 0.6f);
g_cfg.thrashing_score_thresh = property_get_float("lmkd.thrash.th", 1.0f);
g_cfg.backoff_ms_min       = property_get_int32("lmkd.backoff.min", 50);
g_cfg.backoff_ms_max       = property_get_int32("lmkd.backoff.max", 2000);
g_cfg.fg_protect_adj       = property_get_int32("lmkd.fg.adj", 200);
g_cfg.fg_memory_min_bytes  = property_get_int64("lmkd.fg.memmin", 64*1024*1024);
g_cfg.fg_memory_low_bytes  = property_get_int64("lmkd.fg.memlow", 128*1024*1024);
g_cfg.kill_by_group        = property_get_bool("lmkd.kill.group", true);
g_cfg.low_mem_kb           = property_get_int64("lmkd.fallback.mem.kb", 150000);
g_cfg.low_swap_kb          = property_get_int64("lmkd.fallback.swap.kb", 64000);
g_cfg.w_adj                = property_get_float("lmkd.w.adj", 1.0f);
g_cfg.w_rss                = property_get_float("lmkd.w.rss", 0.5f);
g_cfg.w_ui                 = property_get_float("lmkd.w.ui", 2.0f);
`
设备侧可在 vendor_prop 或 init.*.rc 中设置这些属性，快速做 A/B 调参。

9) Android.bp 修改示例
`
cc_binary {
    name: "lmkd",
    srcs: [
        "lmkd.c",
        "lmkd_psi.c",
        "lmkd_vmpressure.c",
        "lmkd_stats.c",
        "lmkd_cgroup.c",
        "lmkd_actions.c",
        "lmkd_config.c",
    ],
    cflags: [
        "-DLMKD_PREACTIONS",
        "-DLMKD_CGROUP_V2",
    ],
    shared_libs: ["liblog", "libcutils"],
    // 其它保持不变
}
`
10) 回归与观测建议（最小集）

指标（statsd/trace 或 logcat 汇聚）：

preaction.count, preaction.success.rate, kill.count/hour, psi.mem.max, psi.io.max, backoff.time.avg, fg.kill.count（应≈0）

A/B 实验：统一场景（打开多 App + 大文件下载 + 后台编译/压缩），比较卡顿率、Kill 次数、前台留存。