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
一、视频与音频硬件加速
1. 专有 VPU（Video Processing Unit）/GPU 驱动
4K/8K/HDR10+/Dolby Vision/HLG 硬解，AI 画质算法（MEMC/降噪/超分）

索尼（X1/XR）、TCL（AiPQ）、海信（ULED）、小米（MTK/Amlogic）、康佳、飞利浦

多路视频流/多画面（PIP/PBP）并发处理

索尼、TCL、海信、飞利浦

专有 PQ（画质）算法驱动（动态背光/锐化/色彩校准）

索尼、TCL、海信、小米、康佳

2. 专有音频 DSP/Codec 驱动
杜比音效/DTS/3D空间音频/低延迟音频直通

索尼、TCL、海信、小米、飞利浦

AI 语音降噪/远场麦克风/声源定位

TCL、海信、小米、索尼

多声道混音/硬件后处理

索尼、TCL、海信、飞利浦

二、信号源与多外设管理
1. TV Tuner/DVB/CI+ 驱动
全球多制式 DVB/ATSC/ISDB/CI+ 智能卡适配

索尼、TCL、海信、飞利浦、康佳

多频道/时移/回看/EPG

TCL、海信、索尼、飞利浦

CA 智能卡安全解码

TCL、海信、索尼

2. 多 HDMI/AV 输入与 CEC（HDMI 控制）
多路 HDMI/AV 输入自动切换、ARC/eARC、CEC 智能链路

索尼、TCL、海信、小米、飞利浦

HDMI 快速唤醒、信号源同步、低延迟输入（ALLM）

TCL、索尼、海信、小米

3. 遥控/体感/麦克风/智能外设
多通道遥控（红外/蓝牙/2.4G）、语音/体感遥控、远场麦克风

小米（语音遥控/体感）、索尼、TCL、海信、康佳

智能摄像头/视频通话/体感交互支持

TCL、海信、康佳、飞利浦

三、存储、内存与性能优化
1. 高速存储与分区管理
eMMC/UFS/F2FS 优化、动态分区扩容、U盘/移动硬盘快速挂载

小米、TCL、海信、索尼

断电保护、低延迟 I/O、写放大控制

TCL、索尼、海信

2. 定制内存管理/多媒体缓存
LMK（LowMemoryKiller）/zRAM/ION/DMA-BUF 动态大缓存

小米、TCL、海信、索尼

多媒体硬件间零拷贝传输、高效帧缓冲

TCL、索尼、海信

四、安全性与内容保护
1. DRM/CA 安全模块
Widevine/PlayReady/CA/CI+ 模块，内容加密链路、硬件密钥隔离

索尼、TCL、海信、飞利浦

HDCP 2.2/2.3 认证、广电级智能卡支持

TCL、海信、索尼

2. 防篡改与完整性保护
DM-Verity/SELinux/安全启动、Root 检测

小米、TCL、索尼、海信、康佳

五、功耗与散热智能控制
动态电源管理/CPU-GPU/VPU 智能调频/场景自适应背光/风扇/LED 控制

TCL、索尼、海信、小米、飞利浦

待机秒开、HDMI 唤醒、低功耗遥控待命

TCL、小米、索尼、海信

六、其他亮点
1. 快速启动与 OTA 升级
内核级待机唤醒/秒开机、差分 OTA、远程诊断

小米、TCL、海信、索尼

2. 厂商专有远程调测与日志收集
自有远程维护、智能健康监控

TCL、海信、索尼