### Android 架构
Refer https://source.android.com/docs/core/architecture?hl=zh-cn
![Image](https://github.com/user-attachments/assets/d62a8932-05dc-40d3-b406-57c9005daad9)

### 代码框架整理
### Linux Kernel 层

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

Samsung 特有功能
源码位置
https://opensource.samsung.com/downSrcCode

三星（Samsung）Linux Kernel 层常见自研机制与增强汇总
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
