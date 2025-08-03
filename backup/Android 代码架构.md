### Android 架构
Refer https://source.android.com/docs/core/architecture?hl=zh-cn
![Image](https://github.com/user-attachments/assets/d62a8932-05dc-40d3-b406-57c9005daad9)

### 代码框架整理
### Linux Kernel 层
| 大类             | 子模块              | 主要功能                                 | 代码路径                                         |
|------------------|---------------------|------------------------------------------|--------------------------------------------------|
| GKI 与厂商驱动   | GKI 核心            | 通用内核，简化配置，支撑主线生态         | kernel/                                          |
| GKI 与厂商驱动   | Vendor 驱动         | 厂商专用硬件驱动，模块化加载             | vendor/ 、 hardware/                             |
| GKI 与厂商驱动   | KGSL                | 高通 Adreno GPU 驱动，管理 GPU 资源/功耗/命令 | drivers/gpu/msm/kgsl/                        |
| Android专有模块  | Binder              | 主 IPC 机制，进程间对象/RPC通信          | drivers/android/binder/                          |
| Android专有模块  | Ashmem              | 匿名共享内存（高效进程间共享）           | drivers/staging/android/ashmem.c                 |
| Android专有模块  | Lowmemorykiller     | 低内存回收（LMK，内核/用户空间）         | drivers/staging/android/lowmemorykiller.c / system/memory/lmkd/ |
| Android专有模块  | IOCTL 框架          | 标准 I/O 控制接口                        | drivers/                                         |
| Android专有模块  | 电源管理            | CPU 频率调节、wakelock、休眠唤醒         | drivers/cpufreq/ , kernel/power/                 |
| Android专有模块  | SELinux             | 强制访问控制，保障系统安全               | security/selinux/                                |
| Android专有模块  | Vendor Modules      | 各类厂商专用硬件驱动                     | vendor/ 、 hardware/                             |
| Android专有模块  | RIL                 | 无线通信接口                             | system/rild/                                     |
| 内存管理         | LMK                 | 低内存回收机制（内核/用户空间）          | drivers/staging/android/lowmemorykiller.c / system/memory/lmkd/ |
| 内存管理         | ZRAM                | 压缩内存，提升低端设备内存利用           | drivers/staging/zram/                            |
| 内存管理         | Buddy System        | 物理内存分配与合并                       | mm/compaction.c                                  |
| 内存管理         | Slab/SLUB 分配器    | 内核对象缓存池式分配                     | mm/slab.c , mm/slub.c                            |
| 内存管理         | ION                 | 多媒体/SoC物理内存分配与共享             | drivers/staging/android/ion/                     |
| 内存管理         | DMA-BUF             | 跨驱动共享物理内存标准接口               | drivers/dma-buf/                                 |
| 文件系统与存储   | EXT4                | 默认文件系统，高性能高可靠               | fs/ext4/                                         |
| 文件系统与存储   | F2FS                | 针对 Flash 优化的文件系统                | fs/f2fs/                                         |
| 文件系统与存储   | 加密文件系统        | FSCRYPT/LUKS 文件系统加密                | fs/crypto/                                       |
| 文件系统与存储   | Vold                | 存储管理守护进程，挂载/卸载/OTG管理      | system/core/vold/                                |
| 文件系统与存储   | DM-Verity           | 只读分区完整性校验，防篡改               | drivers/md/                                      |
| 文件系统与存储   | Keymaster HAL       | 密钥管理/安全生成功能                    | hardware/interfaces/security/keymint/aidl/       |
| 网络             | TCP/IP 协议栈       | IPv4/IPv6/TCP/UDP 网络通信               | net/ipv4/ , net/ipv6/                            |
| 网络             | Wi-Fi 驱动          | 无线网卡、Wi-Fi 管理                     | drivers/net/wireless/                            |
| 网络             | 蓝牙驱动            | 蓝牙协议栈驱动                           | drivers/bluetooth/                               |
| 网络             | Netfilter           | 防火墙/NAT/数据包过滤                    | net/netfilter/                                   |
| 安全性           | SELinux             | 强制访问控制                             | security/selinux/                                |
| 安全性           | HSM                 | 硬件安全模块，加密运算加速               | drivers/crypto/                                  |
| 安全性           | AppArmor（可选）    | 可选安全沙箱策略                         | security/apparmor/                               |
| 中断/硬件管理    | 中断管理            | 硬件/软件中断分发和处理                  | kernel/irq/                                      |
| 中断/硬件管理    | GPU 驱动            | Adreno/Mali 等 GPU 内核驱动              | drivers/gpu/ , drivers/gpu/msm/kgsl/             |
| 中断/硬件管理    | 传感器驱动          | 各类传感器（加速度计、陀螺仪等）         | drivers/iio/                                     |
| 中断/硬件管理    | 输入设备驱动        | 触摸屏、键盘等输入设备                   | drivers/input/                                   |

### Native Libraries
| 类别       | 名称             | 主要功能                           | 代码路径                                         |
|------------|------------------|------------------------------------|--------------------------------------------------|
| 基础库     | Bionic libc      | C 标准库，系统调用、内存分配       | bionic/                                          |
| 基础库     | libc++           | C++ 标准库                         | external/libcxx/                                 |
| 基础库     | libm             | 数学函数库                         | bionic/libm/                                     |
| 系统基础   | liblog           | 日志接口                           | system/core/liblog/                              |
| 系统基础   | libbinder        | Binder IPC 支持                    | frameworks/native/libs/binder/                    |
| 系统基础   | libutils         | 字符串、Mutex、引用计数等工具      | system/core/libutils/                            |
| 系统基础   | libandroid       | Native 和 Java 桥接接口            | frameworks/native/libs/android/                   |
| 系统基础   | libnativehelper  | JNI 辅助库                         | frameworks/native/libs/nativehelper/              |
| 安全      | libselinux        | SELinux 支持库                     | external/selinux/libselinux/                     |
| 安全      | libcrypto         | 加密算法库                         | external/openssl/                                |
| 安全      | libssl            | SSL/TLS 支持                       | external/openssl/                                |
| 算法/压缩 | libz              | 压缩算法库（zlib）                 | external/zlib/                                   |
| 多媒体     | libjpeg           | JPEG 图像解码                      | external/libjpeg-turbo/                          |
| 多媒体     | libpng            | PNG 图像解码                       | external/libpng/                                 |
| 多媒体     | libwebp           | WebP 图像处理                      | external/libwebp/                                |
| 多媒体     | libskia           | 2D 图形渲染                        | external/skia/                                   |
| 图形       | libEGL/libGLES    | OpenGL ES 支持                     | frameworks/native/opengl/                        |
| 图形       | libvulkan         | Vulkan 支持                        | external/vulkan/                                 |
| 摄像头     | libcamera         | 摄像头 HAL 通用库                  | external/libcamera/                              |
| 输入       | libinput          | 输入设备支持                       | system/core/libinput/                            |
| 多媒体     | libstagefright    | 多媒体编解码                       | frameworks/av/media/libstagefright/              |
| 多媒体     | libmediandk       | 多媒体原生接口                     | frameworks/av/media/ndk/                         |
| 序列化     | libprotobuf       | Protocol Buffers 序列化库          | external/protobuf/                               |
| ART        | libdexfile        | DEX 字节码解析与支持               | art/libdexfile/                                  |
| ART        | libart            | ART 虚拟机核心库                   | art/runtime/                                     |

### Native Deamons
| 类别           | 名称             | 主要功能                                    | 代码路径                                       |
|----------------|------------------|---------------------------------------------|------------------------------------------------|
| 启动/管理      | init             | 系统启动初始化，启动守护进程                | system/core/init/                              |
| 调试           | adbd             | ADB 调试守护进程                            | system/core/adb/                               |
| 存储           | vold             | 存储设备管理（挂载、加密、OTG等）           | system/vold/                                   |
| 网络           | netd             | 网络配置、DNS、防火墙等                      | system/netd/                                   |
| 日志           | logd             | 系统日志服务                                 | system/core/logd/                              |
| 管理           | servicemanager   | Binder 服务管理器                            | frameworks/native/cmds/servicemanager/         |
| 应用孵化       | zygote           | 应用进程孵化器，启动 Java 应用               | frameworks/base/cmds/zygote/                   |
| 图形           | surfaceflinger   | 图形合成服务，负责屏幕显示                   | frameworks/native/services/surfaceflinger/      |
| 内存           | lmkd             | Low Memory Killer Daemon 内存回收守护进程    | system/memory/lmkd/                            |
| 无线           | rild             | 无线通信守护进程（Radio Interface Layer）    | hardware/ril/                                  |
| 电池           | healthd          | 电池和健康状态监控                           | system/core/healthd/                           |
| 安全           | keystore         | 密钥/证书安全管理                            | system/security/keystore/                      |
| 安装           | installd         | APK 安装、数据目录管理                        | system/extras/installd/                        |
| DRM            | drmserver        | 数字版权管理（DRM）服务                      | frameworks/av/drm/drmserver/                   |
| 安全           | gatekeeperd      | 屏幕解锁验证（Gatekeeper HAL）               | system/gatekeeperd/                            |
| 统计           | statsd           | 统计与遥测收集服务                           | frameworks/base/cmds/statsd/                   |
