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
