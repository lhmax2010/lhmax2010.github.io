### Android Linux Kernel

**1. GKI（Generic Kernel Image）**
GKI（Generic Kernel Image） 是 Android 在 Linux Kernel 基础上推出的一个标准化内核，旨在简化设备硬件驱动和系统服务的兼容性管理。

定义与功能：
GKI（Generic Kernel Image） 是一个可以支持所有 Android 设备的通用内核，简化了设备的内核配置。
通过 GKI，Android 系统将 Android-specific drivers 从内核中拆分出来，并放到 vendor 目录下，这样可以保持内核的统一，同时让设备厂商可以提供与设备硬件匹配的驱动程序。
GKI 支持 Mainline Kernel，意味着系统更新可以通过 Google Play 更新内核而不需要设备厂商干预。

GKI 结构：
GKI 核心：为 Android 系统提供通用的内核功能和驱动，保持一致性。

**代码路径：kernel/**

**Vendor 组件**：硬件相关的驱动和配件由设备厂商提供，通过 vendor kernel modules 加载。

**代码路径：vendor/ 和 hardware/**

KGSL（Kernel Graphics Support Layer）是高通（Qualcomm）平台专有的 GPU 驱动内核模块，是 Android 设备（特别是搭载高通 Snapdragon 芯片的手机/平板）上 Adreno GPU 与操作系统和用户空间（如 OpenGL ES、Vulkan、Skia 等）之间的桥梁。

Adreno GPU 的内核态驱动：负责管理 GPU 资源、上下文调度、内存分配、命令提交、功耗控制等。
为用户空间驱动和图形 HAL 提供接口：比如 libGLES、libEGL、libgralloc 以及 Android HAL。
支持多种图形 API：如 OpenGL ES、Vulkan、RenderScript、Skia、OpenCL 等。

**代码路径：drivers/gpu/msm/kgsl/**

**2. Linux Kernel 中 Android-specific 驱动与模块**
与 GKI 一起，Android 内核中还包括一些 Android-specific 驱动 和模块，这些是为了更好地支持 Android 设备的硬件和特性。

**Android-specific 驱动和功能模块：**
**Binder 驱动**：用于 进程间通信（IPC），是 Android 系统与 Linux 内核之间的主要通信机制。

**代码路径：kernel/binder/**

功能：提供跨进程的对象调用、远程过程调用（RPC），并为设备提供进程间通信的机制。

**Ashmem（Anonymous Shared Memory）**：Android 提供的一个共享内存机制，供多个进程共享数据。

**代码路径：kernel/ashmem/**

功能：为内存敏感的 Android 应用提供高效的内存共享方式。

LMK (lowmemorykiller): 系统低内存回收，保护用户前台体验，防止OOM崩溃

代码路径：system/memory/lmkd

**IOCTL（Input/Output Control）**：用于设备与用户空间的通信，通过 ioctl 系统调用管理硬件。

**代码路径：drivers/**

**Power Management（电源管理）：**

CPU 频率调节：通过 CPUFreq 框架动态调整 CPU 的频率，减少功耗。

**代码路径：drivers/cpufreq/**

**Wakelock**：控制设备是否进入低功耗模式。

**代码路径：kernel/power/**

**SELinux**：提供强制访问控制（MAC）机制，保障 Android 系统的安全性。

**代码路径：security/selinux/**

**Vendor Modules**：这些模块通常由设备厂商提供，包含专门的硬件驱动。

**代码路径：vendor/ 和 hardware/**

**RIL（Radio Interface Layer）**：为 Android 系统提供通信功能，包括电话、短信、数据服务等。

**代码路径：system/rild/**

代码路径：drivers/md/

**3. 内存管理（Memory Management）**
内存管理是 Linux 内核的核心之一，Android 系统也对其进行了定制化以提高性能和资源利用率。

**Android-specific Memory Management：**

**低内存 Killer（LMK）**：在内存不足时，内核会终止一些进程以释放内存，确保系统不会因为内存不足而崩溃。

**代码路径：kernel/sched/**

**ZRAM**：压缩内存，用于提高内存利用效率，特别是在低内存设备上。

**代码路径：drivers/staging/zram/**

**Buddy System**：Android 和 Linux 使用的 伙伴系统 分配器高效管理物理内存。

**代码路径：mm/compaction.c**

**Slab 分配器**：用于内存缓存管理，提高对象分配效率。

**代码路径：mm/slab.c**

ION 内存管理驱动：Android 平台上为多媒体和 SoC 硬件加速设计的通用内存分配与管理驱动。

它允许多个硬件模块（如 GPU、Video Codec、Camera、Display 等）高效共享物理连续内存或特定类型内存，实现跨硬件和用户空间/内核空间的零拷贝数据传递。

代码路径：drivers/staging/android/ion/
但是Android 12开始，逐步想DMA-BUF Heap 标准靠拢

DMA-BUF（Direct Memory Access Buffer）是 Linux 内核中为多个设备/驱动共享物理内存缓冲区而设计的内核标准接口和框架。
它主要用于高效的、零拷贝的数据流传递，特别适合多媒体、显示、视频解码、GPU/CPU 协同等场景。

代码路径：drivers/dma-buf/

**4. 文件系统（File System）**
Android 在 Linux 文件系统 基础上做了很多定制化，尤其是在 内存卡管理、加密文件系统 等方面。

**Android 文件系统：**

EXT4 文件系统：默认的 Android 文件系统，提供高性能和高可靠性。

**代码路径：fs/ext4/**

**F2FS**：一种优化的 Flash 文件系统，用于 NAND 闪存存储。

**代码路径：fs/f2fs/**

**加密文件系统**：Android 支持基于 LUKS 或 FSCRYPT 的文件系统加密。

**代码路径：fs/crypto/**

**Storage（存储）管理：**

**Vold**：Android 的存储管理系统，处理存储设备（如 SD 卡）的挂载与卸载。

**代码路径：system/core/vold/**

**Filesystem Encryption**：Android 在设备上启用 文件系统加密，确保用户数据安全。

**代码路径：fs/crypto/**

Keymaster HAL（Hardware Abstraction Layer）是 Android 平台用于管理加密、签名等密钥相关操作的标准接口层，是安卓安全体系和硬件安全模块（如 TEE/SE/TrustZone）之间的桥梁。

密钥管理统一接口：为 Android 系统和 App 提供统一、标准化的密钥生成、使用、存储和销毁能力。
安全隔离：把真正的密钥操作放在受信任环境（如 TEE/TrustZone/Secure Element）中，防止普通系统或恶意 App 访问明文密钥。
API 对接：上层服务如 Keystore、Gatekeeper、Biometrics 通过 Keymaster HAL 访问底层安全模块。

代码路径：
AIDL：hardware/interfaces/security/keymint/aidl/

**5. 网络（Networking）**
Android 对 Linux 网络栈 进行了定制，尤其在网络通信、连接管理和优化上。

Android 网络栈：

**TCP/IP 栈**：Android 使用 Linux 内核的 TCP/IP 协议栈进行所有网络通信。

**代码路径：net/ipv4/**

**Wi-Fi 驱动与管理**：Android 定制了与 Wi-Fi 相关的内核模块与用户空间服务。

**代码路径：drivers/net/wireless/**

**蓝牙管理**：通过 BlueDroid 提供蓝牙协议栈。

**代码路径：drivers/bluetooth/**

**Netfilter（防火墙）**：Android 使用 Netfilter 管理网络流量，提供数据包过滤与 NAT 功能。

**代码路径：net/netfilter/**

**6. 安全性（Security）**
Android 对 Linux 内核的安全性进行了增强，主要通过 SELinux 和其他 Android-specific 安全模块。

**SELinux（Security-Enhanced Linux）**：通过强制访问控制（MAC）确保系统安全。

**代码路径：security/selinux/**

**硬件安全模块（HSM）**：Android 与硬件平台集成，为敏感数据提供硬件级的加密保护。

**代码路径：drivers/crypto/**

**AppArmor（可选）**：另一种用于提供强制访问控制的安全机制。

**代码路径：security/apparmor/**

**7. 中断处理与硬件管理（Interrupt Handling & Hardware Management）**
Android 内核中有大量硬件相关的驱动和模块，确保硬件资源的高效利用。

**硬件中断（Interrupts）：**

**中断管理**：处理来自硬件的中断信号。

**代码路径：kernel/irq/**

**硬件驱动**：

**GPU 驱动**：管理图形处理单元（GPU）及其加速功能。

**代码路径：drivers/gpu/**

**传感器驱动**：Android 支持各种传感器（如加速度计、陀螺仪）。

**代码路径：drivers/iio/**

**输入设备驱动**：包括键盘、触摸屏等输入设备的管理。

**代码路径：drivers/input/**

### Samsung 特有功能
源码位置
https://opensource.samsung.com/downSrcCode

### 三星（Samsung）Linux Kernel 层常见自研机制与增强汇总
1. SLMK / SLMKD（Samsung Low Memory Killer/Daemon）
路径：drivers/android/lowmemorykiller.c、drivers/android/swap_lowmemorykiller.c

说明：自研内核态内存回收/冻结机制，细粒度管理内存，多任务体验更优。

2. SPM / PM QoS（Samsung Power Management / Quality of Service）
路径：drivers/samsung/pm_qos/、drivers/soc/samsung/pm_qos/

说明：电源性能 QoS 策略，支持按场景动态调整 CPU/GPU 频率、总线带宽等，减少卡顿和功耗。

3. EXYNOS SoC 相关驱动和控制子系统
路径：drivers/soc/samsung/、drivers/soc/exynos/、arch/arm64/boot/dts/samsung/

说明：包括 CPU/GPU 电源域、总线调度、温控、DVFS、Secure Monitor、ISP/VPU/NPU 控制等全套 SoC 控制逻辑。

4. Seclog / SELinux 扩展（Samsung Security Logger）
路径：security/samsung/、security/selinux/（有三星自定义 patch）

说明：系统安全增强日志、动态安全策略、Root 检测、Knox 支持。

5. Knox / TIMA（TrustZone-based Integrity Measurement Architecture）
路径：drivers/samsung/tima/、drivers/samsung/knox/

说明：安全启动、加密、应用容器，防 root、数据保护、企业级安全体系。部分功能通过 TrustZone/TEE 驱动实现。

6. SecCamera / SecAudio / SecDisplay（多媒体安全与增强）
路径：drivers/media/platform/samsung/、drivers/sound/samsung/、drivers/video/fbdev/exynos/

说明：摄像头、音频、显示等硬件的专有优化和安全扩展（如 secure buffer, watermark, HDR, 多摄像头切换）。

7. Samsung MMC/Storage 定制驱动（F2FS/ufs/ext4等增强）
路径：drivers/mmc/host/、fs/f2fs/（有三星定制补丁）、drivers/scsi/ufs/

说明：支持自家定制 eMMC/UFS/NAND 存储，扩展命令、智能健康管理、碎片整理和 TRIM、动态调度。

8. Samsung Debug/Diag/Log 系统
路径：drivers/samsung/debug/、drivers/samsung/log/、drivers/samsung/diag/

说明：厂测、用户态抓 log、在线诊断、调试开关。对系统异常监控和信息回溯提供专用接口。

9. Battery/Powershare/Charger 定制管理
路径：drivers/power/samsung/、drivers/power/supply/

说明：电池健康、充电协议、反向充电、快充和多电池场景专用管理，含多种安全策略。

10. SecComp/SecKmsg/Procfs 扩展
路径：security/samsung/、fs/proc/（含三星 patch）、kernel/sec/

说明：内核安全、进程/系统监控增强、日志接口拓展（比如通过 seccomp 增强进程隔离、kmsg 日志加密等）。

11. 内核冷启动/热重启/系统恢复/Watchdog 增强
路径：drivers/samsung/recovery/、drivers/watchdog/

说明：异常自动重启、系统镜像校验、OTA 恢复、厂商级看门狗逻辑。

12. Samsung Haptic/Display/Touch Driver
路径：drivers/input/touchscreen/samsung/、drivers/video/fbdev/exynos/

说明：振动马达、超声波指纹、AMOLED 显示、自适应触控优化与私有特性。

13. 专有调度、隔离与优先级机制
路径：kernel/sched/、kernel/cgroup/、drivers/samsung/sched/

说明：基于场景/AI/进程特征调整调度优先级，支持前后台自适应切换。

14. ZRAM/Zswap/Swap 扩展与优化
路径：drivers/block/zram/、mm/zswap.c（含三星 patch）

说明：压缩内存、动态 swap 策略，提升低内存设备流畅度和后台多任务能力。

15. 无线/射频/定位增强（Wi-Fi、BT、GNSS等）
路径：drivers/net/wireless/samsung/、drivers/bluetooth/samsung/、drivers/gnss/samsung/

说明：无线协议/射频链路/热点共享等多项私有增强，提升连接稳定性和速度。

| 机制/模块                       | 路径示例                                                      | 说明                                                         |
|----------------------------------|--------------------------------------------------------------|--------------------------------------------------------------|
| SLMK / SLMKD                     | drivers/android/lowmemorykiller.c<br>drivers/android/swap_lowmemorykiller.c | 自研内存回收/冻结机制，细粒度管理内存，提升多任务体验            |
| SPM / PM QoS                     | drivers/samsung/pm_qos/<br>drivers/soc/samsung/pm_qos/        | 电源性能 QoS 策略，动态调整 CPU/GPU 频率与带宽，减少卡顿与功耗   |
| EXYNOS SoC 相关驱动              | drivers/soc/samsung/<br>drivers/soc/exynos/<br>arch/arm64/boot/dts/samsung/ | SoC 全套控制，包括电源域、调度、温控、DVFS、ISP/NPU 控制等        |
| Seclog / SELinux 扩展            | security/samsung/<br>security/selinux/（有定制 patch）         | 安全日志、动态安全策略、Root 检测、Knox 支持                   |
| Knox / TIMA                      | drivers/samsung/tima/<br>drivers/samsung/knox/                | 安全启动、加密、容器、防 root、数据保护、企业级安全（含 TEE）     |
| SecCamera / SecAudio / SecDisplay| drivers/media/platform/samsung/<br>drivers/sound/samsung/<br>drivers/video/fbdev/exynos/ | 摄像头/音频/显示等硬件安全与专有优化（HDR、多摄、水印等）        |
| Samsung MMC/Storage 定制驱动     | drivers/mmc/host/<br>fs/f2fs/（有定制补丁）<br>drivers/scsi/ufs/ | 定制 eMMC/UFS，健康管理、碎片整理、动态调度等                    |
| Samsung Debug/Diag/Log           | drivers/samsung/debug/<br>drivers/samsung/log/<br>drivers/samsung/diag/ | 厂测、日志抓取、在线诊断，异常监控和信息回溯                    |
| Battery/Powershare/Charger 定制  | drivers/power/samsung/<br>drivers/power/supply/                | 电池健康、充电协议、反向充电、快充等多电池管理及安全策略          |
| SecComp/SecKmsg/Procfs 扩展      | security/samsung/<br>fs/proc/（含 patch）<br>kernel/sec/       | 内核安全、系统监控增强、日志接口拓展、进程隔离等                  |
| 冷启动/热重启/系统恢复/Watchdog  | drivers/samsung/recovery/<br>drivers/watchdog/                 | 异常重启、OTA恢复、看门狗、镜像校验等                            |
| Haptic/Display/Touch Driver      | drivers/input/touchscreen/samsung/<br>drivers/video/fbdev/exynos/ | 触控、振动、指纹、AMOLED 显示优化与私有特性                      |
| 调度/隔离/优先级机制             | kernel/sched/<br>kernel/cgroup/<br>drivers/samsung/sched/      | 场景/AI/进程特征动态调度，优先级调整，前后台切换等                |
| ZRAM/Zswap/Swap 扩展             | drivers/block/zram/<br>mm/zswap.c（含 patch）                  | 压缩内存，动态 swap 策略，提升低内存设备流畅度                   |
| 无线/射频/定位增强               | drivers/net/wireless/samsung/<br>drivers/bluetooth/samsung/<br>drivers/gnss/samsung/ | 无线协议/射频/定位专有增强，提升连接与定位性能                   |


### Android TV 厂商该层研究技术
<html>
<body>
<!--StartFragment--><html><head></head><body><p style="animation: 0s ease 0s 1 normal none running none; appearance: none; background: none 0% 0% / auto repeat scroll padding-box border-box rgba(0, 0, 0, 0); border: 0px none rgb(27, 28, 29); inset: auto; clear: none; clip: auto; color: rgb(27, 28, 29); columns: auto; contain: none; container: none; content: normal; cursor: auto; cx: 0px; cy: 0px; d: none; direction: ltr; display: block; fill: rgb(0, 0, 0); filter: none; flex: 0 1 auto; float: none; font-style: normal; font-variant: normal; font-size-adjust: none; font-kerning: auto; font-optical-sizing: auto; font-feature-settings: normal; font-variation-settings: normal; font-weight: normal; font-stretch: normal; font-size: 16px; line-height: 1.15 !important; font-family: &quot;Google Sans Text&quot;, sans-serif !important; gap: normal; hyphens: manual; interactivity: auto; isolation: auto; margin-top: 0px !important; margin-right: 0px; margin-bottom: 16px; margin-left: 0px; marker: none; mask: none; offset: normal; opacity: 1; order: 0; orphans: 2; outline: rgb(27, 28, 29) none 0px; overlay: none; padding: 0px; page: auto; perspective: none; position: static; quotes: auto; r: 0px; resize: none; rotate: none; rx: auto; ry: auto; scale: none; speak: normal; stroke: none; transform: none; transition: all; translate: none; visibility: visible; widows: 2; x: 0px; y: 0px; zoom: 1;"></p><h1 style="animation: 0s ease 0s 1 normal none running none; appearance: none; background: none 0% 0% / auto repeat scroll padding-box border-box rgba(0, 0, 0, 0); border: 0px none rgb(27, 28, 29); inset: auto; clear: none; clip: auto; color: rgb(27, 28, 29); columns: auto; contain: none; container: none; content: normal; cursor: auto; cx: 0px; cy: 0px; d: none; direction: ltr; display: block; fill: rgb(0, 0, 0); filter: none; flex: 0 1 auto; float: none; font-style: normal; font-variant: normal; font-size-adjust: none; font-kerning: auto; font-optical-sizing: auto; font-feature-settings: normal; font-variation-settings: normal; font-weight: 700; font-stretch: normal; font-size: 22px; line-height: 1.15 !important; font-family: &quot;Google Sans&quot;, sans-serif !important; gap: normal; hyphens: manual; interactivity: auto; isolation: auto; margin-top: 0px !important; margin-right: 0px; margin-bottom: 8px; margin-left: 0px; marker: none; mask: none; offset: normal; opacity: 1; order: 0; orphans: 2; outline: rgb(27, 28, 29) none 0px; overlay: none; padding: 0px; page: auto; perspective: none; position: static; quotes: auto; r: 0px; resize: none; rotate: none; rx: auto; ry: auto; scale: none; speak: normal; stroke: none; transform: none; transition: all; translate: none; visibility: visible; widows: 2; x: 0px; y: 0px; zoom: 1;">Android TV厂商Linux内核层纯软件技术优化方案</h1><p style="animation: 0s ease 0s 1 normal none running none; appearance: none; background: none 0% 0% / auto repeat scroll padding-box border-box rgba(0, 0, 0, 0); border: 0px none rgb(27, 28, 29); inset: auto; clear: none; clip: auto; color: rgb(27, 28, 29); columns: auto; contain: none; container: none; content: normal; cursor: auto; cx: 0px; cy: 0px; d: none; direction: ltr; display: block; fill: rgb(0, 0, 0); filter: none; flex: 0 1 auto; float: none; font-style: normal; font-variant: normal; font-size-adjust: none; font-kerning: auto; font-optical-sizing: auto; font-feature-settings: normal; font-variation-settings: normal; font-weight: normal; font-stretch: normal; font-size: 16px; line-height: 1.15 !important; font-family: &quot;Google Sans Text&quot;, sans-serif !important; gap: normal; hyphens: manual; interactivity: auto; isolation: auto; margin-top: 0px !important; margin-right: 0px; margin-bottom: 16px; margin-left: 0px; marker: none; mask: none; offset: normal; opacity: 1; order: 0; orphans: 2; outline: rgb(27, 28, 29) none 0px; overlay: none; padding: 0px; page: auto; perspective: none; position: static; quotes: auto; r: 0px; resize: none; rotate: none; rx: auto; ry: auto; scale: none; speak: normal; stroke: none; transform: none; transition: all; translate: none; visibility: visible; widows: 2; x: 0px; y: 0px; zoom: 1;"></p><p style="animation: 0s ease 0s 1 normal none running none; appearance: none; background: none 0% 0% / auto repeat scroll padding-box border-box rgba(0, 0, 0, 0); border: 0px none rgb(27, 28, 29); inset: auto; clear: none; clip: auto; color: rgb(27, 28, 29); columns: auto; contain: none; container: none; content: normal; cursor: auto; cx: 0px; cy: 0px; d: none; direction: ltr; display: block; fill: rgb(0, 0, 0); filter: none; flex: 0 1 auto; float: none; font-style: normal; font-variant: normal; font-size-adjust: none; font-kerning: auto; font-optical-sizing: auto; font-feature-settings: normal; font-variation-settings: normal; font-weight: normal; font-stretch: normal; font-size: 16px; line-height: 1.15 !important; font-family: &quot;Google Sans Text&quot;, sans-serif !important; gap: normal; hyphens: manual; interactivity: auto; isolation: auto; margin-top: 0px !important; margin-right: 0px; margin-bottom: 16px; margin-left: 0px; marker: none; mask: none; offset: normal; opacity: 1; order: 0; orphans: 2; outline: rgb(27, 28, 29) none 0px; overlay: none; padding: 0px; page: auto; perspective: none; position: static; quotes: auto; r: 0px; resize: none; rotate: none; rx: auto; ry: auto; scale: none; speak: normal; stroke: none; transform: none; transition: all; translate: none; visibility: visible; widows: 2; x: 0px; y: 0px; zoom: 1;">下表详细列出了主要Android TV厂商及其SoC供应商在Linux内核层面的特有功能和纯软件技术优化，这些功能通常超出AOSP的默认实现，旨在提升性能、效率和用户体验。</p><div class="horizontal-scroll-wrapper" style="animation: 0s ease 0s 1 normal none running none; appearance: none; background: none 0% 0% / auto repeat scroll padding-box border-box rgba(0, 0, 0, 0); border: 0px none rgb(27, 28, 29); inset: auto; clear: none; clip: auto; color: rgb(27, 28, 29); columns: auto; contain: none; container: none; content: normal; cursor: auto; cx: 0px; cy: 0px; d: none; direction: ltr; display: block; fill: rgb(0, 0, 0); filter: none; flex: 0 1 auto; float: none; font-style: normal; font-variant: normal; font-size-adjust: none; font-kerning: auto; font-optical-sizing: auto; font-feature-settings: normal; font-variation-settings: normal; font-weight: normal; font-stretch: normal; font-size: 16px; line-height: 1.15 !important; font-family: &quot;Google Sans Text&quot;, sans-serif !important; gap: normal; hyphens: manual; interactivity: auto; isolation: auto; margin-top: 0px !important; margin-right: 0px; margin-bottom: 0px; margin-left: 0px; marker: none; mask: none; offset: normal; opacity: 1; order: 0; orphans: 2; outline: rgb(27, 28, 29) none 0px; overlay: none; padding: 0px; page: auto; perspective: none; position: static; quotes: auto; r: 0px; resize: none; rotate: none; rx: auto; ry: auto; scale: none; speak: normal; stroke: none; transform: none; transition: all; translate: none; visibility: visible; widows: 2; x: 0px; y: 0px; zoom: 1;">
厂商/SoC供应商 | 特有功能/技术 | 描述 | 来源URL
-- | -- | -- | --
索尼 (Sony) | 快速启动待机 (Quick Start Standby) | 索尼Android TV支持“快速启动”模式，使Linux系统保持在挂起（待机）状态，以实现近乎即时的唤醒，从而在更高待机功耗下换取即时开机功能。这需要内核层面的电源管理定制。 | helpguide.sony.net, github.com/p0i5k/linux-3.10.27 1
  | 定制显示、HDCP内容保护及X1图像处理器集成驱动 | 索尼为其搭载联发科SoC（如MT5890/MT5891）的Bravia电视提供内核源码，其中包含索尼定制的显示、HDCP内容保护和X1图像处理器集成驱动，专注于快速启动、HDMI/DRM支持（4K的HDCP 2.2）和内核安全媒体播放。 | github.com/p0i5k/linux-3.10.27 1
  | 64位芯片上运行32位操作系统以实现兼容性 | 索尼的固件在某些64位芯片上运行32位操作系统以实现兼容性，这是其系统级架构选择，影响内核的配置和兼容性。 | 1
  | 默认强制执行SELinux | 索尼在Android TV中默认强制执行SELinux，这是一种比AOSP默认更严格的安全策略，通过定制SELinux规则来增强系统安全性。 | 1
TCL | 动态内存扩展技术 (专利) | TCL获得了一项专利，能够动态地将Android的数据/缓存文件重定向到外部存储（通过将“/data”绑定挂载到SD卡），有效增加了可用应用安装空间。 | (https://patents.google.com/patent/CN102831173B/zh) 1
  | 定制Linux内核 (Realtek/MediaTek SoC) | TCL的电视使用Realtek和MediaTek SoC，并搭载定制的Linux内核，包括Realtek专有的AV驱动和可能为4K视频播放和低延迟优化的定制DMA/ION内存分配器。 | (https://github.com/TCLOpenSource/TCL_Kernel_OpenSource) 1
  | 快速恢复 (网络待机) 和ALLM (HDMI驱动优化) | TCL的内核代码支持快速恢复（网络待机）和通过HDMI驱动调整实现的自动低延迟模式（ALLM），以优化游戏体验。 | reddit.com/r/bravia/comments/am6560/some_future_bravia_will_be_using_a_cortexa55/ 1
  | 默认启用zRAM压缩交换 | 所有TCL Android TV默认启用zRAM压缩交换，以在有限内存条件下改善多任务处理性能，这是一种针对特定硬件的内存优化配置。 | developer.android.com/topic/performance/memory-management 1
小米 (Xiaomi) | “HyperCore”Linux内核与定制CPU调度器 | 小米较新的Android TV（Mi TV系列）搭载澎湃OS 2，其中包含小米自研的“HyperCore”Linux内核。该内核引入了微架构级别的定制CPU调度器，通过分析CPU指令流水线实现更智能的调度和资源分配。 | sohu.com/a/821684577_99900743 1
  | 统一DVFS (动态频率缩放) 机制 | HyperCore内核引入了统一的DVFS机制，协调CPU缓存和DDR带宽，将CPU空闲时间减少约19%。 | sohu.com/a/821684577_99900743 1
  | “小米动态内存”技术 (增强zRAM) | 一种在Android zRAM压缩RAM交换基础上增强的内存管理系统，用于改善低内存条件下的性能。 | sohu.com/a/821684577_99900743 1
  | “焕新存储2.0” (存储管理大修) | 内核补丁全面改进了内存分配器、调度器和文件系统，以实现更快的应用启动和更流畅的重负载性能。 | sohu.com/a/821684577_99900743 1
  | 迅捷开机 (Fast Boot) | HyperCore内核启用了一种特殊的快速启动模式，大幅缩短启动时间，使电视几乎可以从待机状态即时唤醒。 | finance.sina.com.cn/tech/roll/2025-01-27/doc-inehkiif9649221.shtml 1
  | 全局统一渲染 (内核显示管道重建) | 小米重建了内核显示管道，支持“全局统一渲染”方法，显著降低了渲染延迟（某些情况下帧渲染速度提高12倍）。 | sohu.com/a/821684577_99900743 1
海信 (Hisense) | 安全启动链 (加密OTA、签名固件) | OTA更新包经过加密，表明存在一个安全启动链，引导加载程序只接受签名固件，以防止未经授权的内核修改。 | bananamafia.dev/post/hisensehax/ 1
  | 扩展SELinux强制执行 | 海信在其构建中强制执行Android的强制访问控制（SELinux），并可能扩展其策略以增强安全性。 | bananamafia.dev/post/hisensehax/ 1
  | 设备特定驱动 (VIDAA AI、局部调光) | 包含用于VIDAA AI增强和局部调光的设备特定驱动，这些是海信专有的图像处理功能。 | (https://github.com/HisenseOpenSource/Hisense_TV_Kernel_OpenSource) 1
  | 隐藏调试UART端口与Root Shell | 某些型号通过3.5毫米插孔提供可访问的串行控制台，激活后可显示内核中的root shell（可能用于工厂调试，通过定制设备节点将音频插孔映射到UART）。 | bananamafia.dev/post/hisensehax/ 1
  | HDMI/DRM子系统中HDCP 2.2和杜比视界定制 | 海信的内核源码显示，在SoC供应商代码基础上增加了定制，包括其专有功能（如局部调光LED控制器和T-Con接口）的驱动，以及对HDMI/DRM子系统中HDCP 2.2和杜比视界的支持。 | (https://github.com/HisenseOpenSource/Hisense_TV_Kernel_OpenSource) 1
  | 快速换台优化 | 海信在内核中专注于快速换台优化，以提升电视观看体验。 | 1
华为 (HarmonyOS) | 微内核设计 | HarmonyOS采用微内核设计，与Android基于Linux宏内核的传统架构形成根本性对比，旨在简化内核功能，将更多系统服务置于用户模式。 | promon.io/security-news/harmonyos-next 2
  | 确定性延迟引擎 | 在任务执行前预先分配系统任务的执行优先级和时间限制，确保高优先级任务获得优先资源保障，将应用响应延迟降低25.7%。 | m.cfbond.com/rdxwlb/detail/20190810/1000200033151321565399112534243571_1.html 2
  | 高性能进程间通信 (IPC) | 微内核架构显著提升了IPC性能，效率比现有系统提高五倍之多。 | m.cfbond.com/rdxwlb/detail/20190810/1000200033151321565399112534243571_1.html 2
  | “Hyperhold”内存管理引擎 | 自研的压缩体系（包括多线程压缩和定制压缩算法）以降低内核内存开销，并在闪存上开辟交换区，结合聚合换出和内存标记技术。 | developer.huawei.com/consumer/cn/forum/topic/0204714274580700228 8
  | EROFS (增强只读文件系统) | 显著提升随机读取性能（平均提升20%），并与Ext4文件系统相比，初始系统空间节省约2GB。 | developer.huawei.com/consumer/cn/forum/topic/0204714274580700228 8
  | TEE形式化验证方法 | 将微内核技术应用于可信执行环境（TEE），并通过形式化方法从源头验证系统正确性和无漏洞，显著提升安全性。 | m.cfbond.com/rdxwlb/detail/20190810/1000200033151321565399112534243571_1.html 2
  | 分布式虚拟总线技术 | 使物理上分离的设备能够集成到一个虚拟的“超级设备”中，实现设备间的无缝协作和数据共享。 | (https://consumer.huawei.com/ph/community/details/HarmonyOS-HongMeng-OS-Everything-you-need-to-know/topicId-143188/) 2

</div></body></html><!--EndFragment-->
</body>
</html>Android TV厂商Linux内核层纯软件技术优化方案下表详细列出了主要Android TV厂商及其SoC供应商在Linux内核层面的特有功能和纯软件技术优化，这些功能通常超出AOSP的默认实现，旨在提升性能、效率和用户体验。厂商/SoC供应商特有功能/技术描述来源URL索尼 (Sony)快速启动待机 (Quick Start Standby)索尼Android TV支持“快速启动”模式，使Linux系统保持在挂起（待机）状态，以实现近乎即时的唤醒，从而在更高待机功耗下换取即时开机功能。这需要内核层面的电源管理定制。[helpguide.sony.net](https://helpguide.sony.net/gbmig/14HE1741/v1/eng/c_quickstart.html), [github.com/p0i5k/linux-3.10.27](https://github.com/p0i5k/linux-3.10.27) 1定制显示、HDCP内容保护及X1图像处理器集成驱动索尼为其搭载联发科SoC（如MT5890/MT5891）的Bravia电视提供内核源码，其中包含索尼定制的显示、HDCP内容保护和X1图像处理器集成驱动，专注于快速启动、HDMI/DRM支持（4K的HDCP 2.2）和内核安全媒体播放。[github.com/p0i5k/linux-3.10.27](https://github.com/p0i5k/linux-3.10.27) 164位芯片上运行32位操作系统以实现兼容性索尼的固件在某些64位芯片上运行32位操作系统以实现兼容性，这是其系统级架构选择，影响内核的配置和兼容性。1默认强制执行SELinux索尼在Android TV中默认强制执行SELinux，这是一种比AOSP默认更严格的安全策略，通过定制SELinux规则来增强系统安全性。1TCL动态内存扩展技术 (专利)TCL获得了一项专利，能够动态地将Android的数据/缓存文件重定向到外部存储（通过将“/data”绑定挂载到SD卡），有效增加了可用应用安装空间。(https://patents.google.com/patent/CN102831173B/zh) 1定制Linux内核 (Realtek/MediaTek SoC)TCL的电视使用Realtek和MediaTek SoC，并搭载定制的Linux内核，包括Realtek专有的AV驱动和可能为4K视频播放和低延迟优化的定制DMA/ION内存分配器。(https://github.com/TCLOpenSource/TCL_Kernel_OpenSource) 1快速恢复 (网络待机) 和ALLM (HDMI驱动优化)TCL的内核代码支持快速恢复（网络待机）和通过HDMI驱动调整实现的自动低延迟模式（ALLM），以优化游戏体验。[reddit.com/r/bravia/comments/am6560/some_future_bravia_will_be_using_a_cortexa55/](https://www.com/r/bravia/comments/am6560/some_future_bravia_will_be_using_a_cortexa55/) 1默认启用zRAM压缩交换所有TCL Android TV默认启用zRAM压缩交换，以在有限内存条件下改善多任务处理性能，这是一种针对特定硬件的内存优化配置。[developer.android.com/topic/performance/memory-management](https://developer.android.com/topic/performance/memory-management) 1小米 (Xiaomi)“HyperCore”Linux内核与定制CPU调度器小米较新的Android TV（Mi TV系列）搭载澎湃OS 2，其中包含小米自研的“HyperCore”Linux内核。该内核引入了微架构级别的定制CPU调度器，通过分析CPU指令流水线实现更智能的调度和资源分配。[sohu.com/a/821684577_99900743](https://www.sohu.com/a/821684577_99900743) 1统一DVFS (动态频率缩放) 机制HyperCore内核引入了统一的DVFS机制，协调CPU缓存和DDR带宽，将CPU空闲时间减少约19%。[sohu.com/a/821684577_99900743](https://www.sohu.com/a/821684577_99900743) 1“小米动态内存”技术 (增强zRAM)一种在Android zRAM压缩RAM交换基础上增强的内存管理系统，用于改善低内存条件下的性能。[sohu.com/a/821684577_99900743](https://www.sohu.com/a/821684577_99900743) 1“焕新存储2.0” (存储管理大修)内核补丁全面改进了内存分配器、调度器和文件系统，以实现更快的应用启动和更流畅的重负载性能。[sohu.com/a/821684577_99900743](https://www.sohu.com/a/821684577_99900743) 1迅捷开机 (Fast Boot)HyperCore内核启用了一种特殊的快速启动模式，大幅缩短启动时间，使电视几乎可以从待机状态即时唤醒。[finance.sina.com.cn/tech/roll/2025-01-27/doc-inehkiif9649221.shtml](https://finance.sina.com.cn/tech/roll/2025-01-27/doc-inehkiif9649221.shtml) 1全局统一渲染 (内核显示管道重建)小米重建了内核显示管道，支持“全局统一渲染”方法，显著降低了渲染延迟（某些情况下帧渲染速度提高12倍）。[sohu.com/a/821684577_99900743](https://www.sohu.com/a/821684577_99900743) 1海信 (Hisense)安全启动链 (加密OTA、签名固件)OTA更新包经过加密，表明存在一个安全启动链，引导加载程序只接受签名固件，以防止未经授权的内核修改。[bananamafia.dev/post/hisensehax/](https://bananamafia.dev/post/hisensehax/) 1扩展SELinux强制执行海信在其构建中强制执行Android的强制访问控制（SELinux），并可能扩展其策略以增强安全性。[bananamafia.dev/post/hisensehax/](https://bananamafia.dev/post/hisensehax/) 1设备特定驱动 (VIDAA AI、局部调光)包含用于VIDAA AI增强和局部调光的设备特定驱动，这些是海信专有的图像处理功能。(https://github.com/HisenseOpenSource/Hisense_TV_Kernel_OpenSource) 1隐藏调试UART端口与Root Shell某些型号通过3.5毫米插孔提供可访问的串行控制台，激活后可显示内核中的root shell（可能用于工厂调试，通过定制设备节点将音频插孔映射到UART）。[bananamafia.dev/post/hisensehax/](https://bananamafia.dev/post/hisensehax/) 1HDMI/DRM子系统中HDCP 2.2和杜比视界定制海信的内核源码显示，在SoC供应商代码基础上增加了定制，包括其专有功能（如局部调光LED控制器和T-Con接口）的驱动，以及对HDMI/DRM子系统中HDCP 2.2和杜比视界的支持。(https://github.com/HisenseOpenSource/Hisense_TV_Kernel_OpenSource) 1快速换台优化海信在内核中专注于快速换台优化，以提升电视观看体验。1华为 (HarmonyOS)微内核设计HarmonyOS采用微内核设计，与Android基于Linux宏内核的传统架构形成根本性对比，旨在简化内核功能，将更多系统服务置于用户模式。[promon.io/security-news/harmonyos-next](https://promon.io/security-news/harmonyos-next) 2确定性延迟引擎在任务执行前预先分配系统任务的执行优先级和时间限制，确保高优先级任务获得优先资源保障，将应用响应延迟降低25.7%。[m.cfbond.com/rdxwlb/detail/20190810/1000200033151321565399112534243571_1.html](https://m.cfbond.com/rdxwlb/detail/20190810/1000200033151321565399112534243571_1.html) 2高性能进程间通信 (IPC)微内核架构显著提升了IPC性能，效率比现有系统提高五倍之多。[m.cfbond.com/rdxwlb/detail/20190810/1000200033151321565399112534243571_1.html](https://m.cfbond.com/rdxwlb/detail/20190810/1000200033151321565399112534243571_1.html) 2“Hyperhold”内存管理引擎自研的压缩体系（包括多线程压缩和定制压缩算法）以降低内核内存开销，并在闪存上开辟交换区，结合聚合换出和内存标记技术。[developer.huawei.com/consumer/cn/forum/topic/0204714274580700228](https://developer.huawei.com/consumer/cn/forum/topic/0204714274580700228) 8EROFS (增强只读文件系统)显著提升随机读取性能（平均提升20%），并与Ext4文件系统相比，初始系统空间节省约2GB。[developer.huawei.com/consumer/cn/forum/topic/0204714274580700228](https://developer.huawei.com/consumer/cn/forum/topic/0204714274580700228) 8TEE形式化验证方法将微内核技术应用于可信执行环境（TEE），并通过形式化方法从源头验证系统正确性和无漏洞，显著提升安全性。[m.cfbond.com/rdxwlb/detail/20190810/1000200033151321565399112534243571_1.html](https://m.cfbond.com/rdxwlb/detail/20190810/1000200033151321565399112534243571_1.html) 2分布式虚拟总线技术使物理上分离的设备能够集成到一个虚拟的“超级设备”中，实现设备间的无缝协作和数据共享。(https://consumer.huawei.com/ph/community/details/HarmonyOS-HongMeng-OS-Everything-you-need-to-know/topicId-143188/) 2