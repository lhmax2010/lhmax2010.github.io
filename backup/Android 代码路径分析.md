| 目录              | 主要功能/内容                                            | 隶属框架/层级                    |
|-------------------|---------------------------------------------------------|-----------------------------------|
| art               | ART 运行环境，字节码编译、JIT/AOT、GC、解释器等          | Native 层（运行时/虚拟机）         |
| bionic            | C/C++ 标准库（libc、libm、libdl）、动态链接器等          | Native 层（基础库/运行时）         |
| bootable          | 各类启动加载器、recovery、fastboot、boot image 工具      | 平台启动/引导                     |
| build             | 构建系统主目录（Make、Soong、Blueprint、工具等）         | 构建系统/工具链                   |
| cts               | 兼容性测试套件 CTS                                       | 测试/兼容性/平台测试               |
| dalvik            | Dalvik VM 相关遗留代码                                   | Native 层（早期虚拟机/历史）        |
| developers        | 开发者文档、示例、工具                                   | 开发辅助/文档/示例                 |
| development       | 开发辅助工具、样例、IDE插件、脚本等                      | 开发工具/辅助层                   |
| device            | 设备/芯片/厂商适配配置与脚本                             | 硬件适配/厂商层                    |
| external          | 第三方/外部依赖库与项目集合（如 openssl、skia 等）       | 第三方依赖/外部库                  |
| frameworks        | Android 框架层主干（Java API、系统服务、媒体、AI等）     | Framework 层（核心API/服务）        |
| hardware          | 硬件抽象层（HAL）、通用硬件接口和库                     | HAL 层/硬件适配                    |
| kernel            | 内核源码/补丁/配置（完整内核需独立下载）                 | Kernel 层/平台基础                 |
| libcore           | Java 标准库实现（java.lang、java.util、io等）            | Framework 层（Java标准库）         |
| libnativehelper   | Java 与 Native 层桥接辅助库（JNI等）                    | Native 层/桥接                     |
| packages          | 系统应用、服务、演示、输入法等                           | 应用层/系统服务                    |
| pdk               | 平台开发套件相关资源与工具                               | 平台兼容/移植/开发工具             |
| platform_testing  | 平台级测试与自动化工具                                   | 测试/平台兼容                      |
| prebuilts         | 预编译工具链、SDK、第三方库等                            | 工具链/外部依赖                    |
| sdk               | Android SDK 工具、API、文档生成等                        | SDK/开发工具                      |
| system            | 系统服务、守护进程、核心库（如 init、vold、netd等）      | Native 层/系统服务                 |
| test              | 各类通用测试用例与测试框架                               | 测试/辅助                          |
| toolchain         | 工具链源码与构建工具                                      | 工具链/开发工具                    |
| tools             | 构建/开发/调试/模拟器/分析等通用工具集                   | 开发/构建/调试/测试工具            |
| vendor            | 各芯片/厂商专用驱动、闭源库、系统扩展                    | 厂商扩展/硬件适配                  |

art
| 目录/模块            | 说明                                                     |
|---------------------|--------------------------------------------------------|
| adbconnection       | ART 远程调试/连接相关实现（ARTD/调试器配合）                |
| artd                | ART Daemon，ART 专用守护进程（如后台编译等）                   |
| benchmark           | 性能基准测试/性能评估用例                                   |
| build               | 构建脚本和配置                                             |
| cmdline             | 命令行工具实现（如 profman、dex2oat 入口等）                 |
| **compiler**        | ART AOT/JIT 编译器核心（前端、优化、后端、SSA、寄存器分配等）  |
| dalvikvm            | 兼容早期 dalvikvm 启动器/模拟器                              |
| **dex2oat**         | DEX 到 OAT 编译工具主程序与流程                              |
| dexdump             | DEX 文件内容反汇编/可视化工具                                |
| dexlayout           | DEX 文件布局优化工具                                        |
| dexlist             | DEX 文件内容列举工具                                        |
| dexoptanalyzer      | DEX 优化分析工具                                            |
| disassembler        | 汇编/反汇编相关实现                                         |
| dt_fd_forward       | 文件描述符转发工具（调试、远程相关）                         |
| imgdiag             | OAT/Image 文件诊断工具                                      |
| **libartbase**      | ART 基础工具库                                             |
| libartpalette       | 平台抽象库（线程、内存、时钟等接口封装）                      |
| libartservice       | ART 服务端组件（ART-D 专用服务）                             |
| libarttools         | ART 工具集合库                                             |
| **libdexfile**      | DEX 文件解析/操作库                                         |
| libelffile          | ELF 文件解析相关库                                          |
| **libnativebridge** | Native Bridge（支持跨ABI运行本地代码）                       |
| **libnativeloader** | Native 库加载桥接库（JNI 动态加载）                          |
| **libprofile**      | 运行时性能分析与配置数据管理                                 |
| **oatdump**         | OAT 文件内容查看与反汇编工具                                 |
| odrefresh           | OAT/DEX 刷新维护工具                                        |
| openjdkjvm          | OpenJDK JVM 相关实现                                       |
| openjdkjvmti        | JVM TI（调试接口）实现                                      |
| perfetto_hprof      | Perfetto HPROF 支持工具（内存分析）                           |
| **profman**         | ART Profile 管理/优化分析工具                               |
| **runtime**         | ART 运行时核心（解释器、GC、线程管理、JNI、类加载等）         |
| sigchainlib         | 信号链库（信号处理相关）                                    |
| simulator           | ART 虚拟机模拟器/测试平台                                   |
| **test**            | 单元测试、集成测试和回归测试                                |
| **tools**           | 辅助开发/编译/调试工具集合                                  |

bionic
| 目录              | 主要功能说明                                                                 |
|-------------------|------------------------------------------------------------------------------|
| apex/             | 存放 APEX 包配置与描述文件，支持 libc 等以 APEX 形式升级                      |
| benchmarks/       | 性能基准测试代码，评估 libc/libm 等库效率                                     |
| build/            | 构建脚本和编译配置（Android.bp/Android.mk）                                   |
| cpu_target_features/ | 定义和检测各 CPU 平台目标特性，用于优化和条件编译                         |
| docs/             | 项目文档、API 说明、兼容性文档                                               |
| libc/             | libc 主体实现（C 标准库、系统调用、字符串/IO/内存操作等）                     |
| libdl/            | 动态库加载器实现（dlopen/dlsym/dlclose等）                                   |
| libfdtrack/       | 文件描述符跟踪与调试支持库                                                    |
| libm/             | 数学库实现（如三角、指数、平方根等函数）                                      |
| libstdc++/        | C++ 标准库部分实现，保证 ABI 兼容                                             |
| linker/           | 动态链接器核心，ELF 加载、重定位、符号解析                                    |
| tests/            | 单元测试和集成测试，保障库的稳定与兼容性                                      |
| tools/            | 构建、调试、符号处理、版本兼容等开发/维护工具                                 |

bootable
| 目录                | 主要功能说明                                                                 |
|---------------------|------------------------------------------------------------------------------|
| libbootloader/      | 启动加载器通用库，实现启动相关的通用逻辑与接口，供多种 bootloader 复用         |
| recovery/           | Recovery 模式实现，负责刷机、恢复出厂、数据清除等系统恢复操作，包括 UI、脚本处理等 |

build 
| 目录                 | 主要功能说明                                                        |
|----------------------|---------------------------------------------------------------------|
| bazel/               | Bazel 构建系统配置与集成文件，支持 Android 项目使用 Bazel 构建流程   |
| bazel_common_rules/  | Bazel 构建通用规则与扩展模块，便于共享与维护                         |
| blueprint/           | Blueprint 元构建系统（Soong 前身/子系统），用于模块定义和依赖管理     |
| make/                | 传统 Android.mk/Makefile 构建系统的规则、模板和脚本                  |
| pesto/               | 构建系统辅助模块和工具（如依赖追踪、构建优化等）                      |
| release/             | 构建产物发布相关脚本与流程                                            |
| soong/               | Soong 构建系统主引擎，Android 现代模块化构建体系                      |

cts
| 目录                | 主要功能说明                                                                 |
|---------------------|------------------------------------------------------------------------------|
| .prebuilt_info/     | 预编译信息与元数据，描述预编译测试包相关内容                                  |
| apps/               | 用于 CTS 测试的示例和演示应用                                                 |
| backported_fixes/   | 回溯修复和补丁，用于兼容性相关的回退修正                                      |
| build/              | CTS 构建脚本和相关配置                                                        |
| common/             | 公共代码和工具类，被多套 CTS 测试用例共享                                      |
| development/        | 开发辅助工具和脚本，支持 CTS 测试开发流程                                      |
| flags/              | 测试运行时和特性的 Flag 配置                                                  |
| helpers/            | 各类测试辅助工具库和测试基类                                                  |
| hostsidetests/      | 主机侧测试用例（在测试服务器/PC 上运行的测试）                                 |
| libs/               | 公共库，供 CTS 测试用例和工具调用                                              |
| suite/              | CTS 测试套件的集合和整体组织结构                                              |
| tests/              | 各类具体的 CTS 测试项/测试用例主目录                                          |
| tools/              | 测试辅助工具和脚本（如打包、执行、结果分析等）                                |

dalvik
| 目录         | 主要功能说明                                                        |
|--------------|---------------------------------------------------------------------|
| dexgen/      | DEX 文件生成相关工具和实现，用于生成和操作 Dalvik 字节码             |
| docs/        | Dalvik 虚拟机相关开发和实现文档                                     |
| dx/          | DEX 文件编译、生成和转换工具（如 dx 工具链，jar→dex 转换）           |
| opcode-gen/  | Dalvik 字节码指令(opcode)相关的生成脚本和工具                        |
| tools/       | 其他 Dalvik 相关开发、测试、分析工具                                 |

developers
| 目录      | 主要功能说明                                          |
|-----------|-------------------------------------------------------|
| build/    | 开发者相关的构建脚本、配置、辅助工具                  |
| demos/    | 开发者演示工程，展示新特性或 API 用法                  |
| samples/  | 官方开发示例代码集合，涵盖常用 API、最佳实践等         |

development
| 目录              | 主要功能说明                                                                 |
|-------------------|------------------------------------------------------------------------------|
| apps/             | 各类开发样例和测试 App                                                        |
| build/            | 构建脚本、辅助构建工具与配置                                                  |
| cmds/             | 命令行工具和脚本                                                              |
| docs/             | 开发相关文档和说明                                                             |
| gki/              | Generic Kernel Image（GKI）相关开发和验证工具                                 |
| gsi/              | Generic System Image（GSI）开发、构建与测试                                   |
| host/             | 主机端（PC 端）辅助工具和测试脚本                                             |
| ide/              | IDE 插件和开发环境集成支持                                                     |
| python-packages/  | Python 开发/构建/测试相关包                                                    |
| samples/          | 各类开发示例代码                                                               |
| scripts/          | 各类辅助开发、构建、测试的脚本                                                 |
| sdk/              | Android SDK 相关开发/工具/构建支持                                             |
| sdk_overlay/      | SDK Overlay 支持及相关实现                                                     |
| sys-img/          | 系统镜像构建和配置                                                             |
| tools/            | 各类开发、构建、测试辅助工具                                                   |
| treble/           | Project Treble 相关开发与工具                                                  |

device
| 目录          | 主要功能说明                                                              |
|---------------|---------------------------------------------------------------------------|
| amlogic/      | Amlogic 芯片平台的设备适配、配置与驱动                                      |
| common/       | 通用设备适配模板、公共配置和脚本                                           |
| generic/      | 通用 Generic 设备适配（适用于通用虚拟设备、x86、arm64 等）                  |
| google/       | Google 设备适配（如 Pixel 系列手机/平板等），包含配置和定制                 |
| google_car/   | Google 汽车平台（Android Automotive/Car）的设备适配                        |
| linaro/       | Linaro 平台（ARM 架构联盟）的开发板和 SoC 适配                             |
| sample/       | 示例设备适配模板，供新设备或新平台定制时参考                                |

external
| 目录                        | 主要功能简介                                                                                 |
|-----------------------------|---------------------------------------------------------------------------------------------|
| AFLplusplus                 | 高级模糊测试工具（AFL）升级版，自动化安全漏洞挖掘                                           |
| ComputeLibrary              | ARM 计算/神经网络加速库                                                                     |
| FP16                        | Half-precision (16-bit) 浮点数计算库                                                        |
| FXdiv                       | 高效除法优化库（常用图像/信号处理）                                                          |
| MPAndroidChart              | 安卓常用开源图表库                                                                          |
| OpenCL-CLHPP                | OpenCL C++ 头文件                                                                            |
| OpenCL-CTS                  | OpenCL 兼容性测试套件                                                                        |
| OpenCL-Headers              | OpenCL 标准头文件                                                                            |
| OpenCL-ICD-Loader           | OpenCL ICD 加载器                                                                            |
| OpenCSD                     | ARM CoreSight 调试追踪解码库                                                                 |
| TestParameterInjector       | JUnit 参数化测试框架                                                                         |
| XNNPACK                     | 高性能神经网络推理内核库（TensorFlow Lite 用）                                               |
| aac                         | AAC 音频编解码库                                                                             |
| abseil-cpp                  | Google C++ 基础库合集                                                                        |
| accessibility-test-framework | 无障碍/辅助功能自动化测试框架                                                               |
| accompanist                 | Jetpack Compose 拓展库                                                                       |
| android-key-attestation     | Android 密钥证明/认证相关库                                                                  |
| android-nn-driver           | Android 神经网络驱动示例                                                                     |
| androidplot                 | 安卓平台数据绘图库                                                                           |
| angle                       | OpenGL ES 转译为 Vulkan/D3D 的兼容层                                                         |
| apache-commons-*            | Apache Java 实用库集合                                                                       |
| arm-neon-tests              | ARM NEON 指令集测试用例集                                                                    |
| arm-optimized-routines      | ARM 高性能常用函数实现                                                                       |
| arm-trusted-firmware        | ARM 平台受信任启动固件                                                                       |
| armnn                       | ARM 神经网络库                                                                               |
| autotest                    | 自动化测试框架                                                                               |
| avb                         | Android Verified Boot 安全启动                                                               |
| bazel*                      | Google Bazel 构建系统核心和规则                                                              |
| bc, bcc                     | 字节码相关库，编译与验证工具                                                                 |
| boringssl                   | Google 改进版 OpenSSL 库                                                                    |
| bouncycastle                | Java 加密算法库                                                                              |
| brotli                      | 高压缩率通用压缩算法库                                                                       |
| bzip2                       | 通用压缩算法库                                                                               |
| capstone                    | 多平台反汇编引擎                                                                             |
| cblas                       | 基础线性代数运算库                                                                           |
| clang, compiler-rt, llvm    | Clang/LLVM 工具链与运行时支持                                                                |
| conscrypt                   | Android 用 TLS/SSL 加密库                                                                   |
| cpu_features, cpuinfo       | CPU 特性检测和分析库                                                                         |
| curl                        | 网络/HTTP 客户端库                                                                           |
| dagger2                     | Google Java 依赖注入框架                                                                     |
| deqp                        | 图形 API 兼容性和性能测试框架                                                                |
| dexmaker                    | Java 动态字节码生成库                                                                        |
| dlmalloc, jemalloc_new      | 多平台高性能内存分配器                                                                       |
| doclava, dokka              | Java/Kotlin API 文档生成工具                                                                 |
| double-conversion           | 高性能浮点转换库                                                                             |
| eigen                       | 通用矩阵和线性代数运算库                                                                     |
| expat, libxml2              | XML 解析库                                                                                   |
| fbjni                       | Facebook JNI 辅助库                                                                         |
| flatbuffers                 | 高性能序列化库                                                                              |
| flac                        | FLAC 音频编解码库                                                                            |
| fonttools, freetype         | 字体解析与处理库                                                                             |
| giflib                      | GIF 动画图片库                                                                              |
| glib                        | 跨平台工具/数据结构库                                                                       |
| go-cmp, golang-protobuf     | Go 语言比较和协议库                                                                         |
| google-breakpad             | 崩溃分析/堆栈回溯工具                                                                       |
| googletest, hamcrest, junit | 单元测试和断言框架                                                                           |
| grpc*                       | Google RPC 通信框架                                                                          |
| guava                       | Google Java 实用库合集                                                                      |
| harfbuzz_ng, harfbuzz       | OpenType 字体排版引擎                                                                       |
| icing                       | Android 搜索/索引引擎                                                                       |
| icu                         | 国际化与 Unicode 支持库                                                                     |
| iptables, iproute2, iputils | Linux 网络协议栈工具                                                                        |
| jackson*, gson, moshi       | JSON 解析与序列化库                                                                         |
| jemalloc_new                | 高性能内存分配库                                                                             |
| jsoncpp, jsoup              | JSON/CSS/HTML 解析库                                                                        |
| kotlin*, kotlinx*           | Kotlin 编译器和相关扩展库                                                                   |
| leakcanary2                 | Java 内存泄露检测工具                                                                       |
| leveldb                     | Google K-V 存储数据库                                                                       |
| libaom, libdav1d, libvpx    | 视频编解码库（AV1、VPX等）                                                                  |
| libdrm, mesa3d              | 显卡驱动与 3D 渲染库                                                                        |
| libjpeg-turbo, libpng, libwebp, libyuv | 图片编解码/处理库                                                          |
| libxml2                     | XML 解析库                                                                                   |
| minijail                    | 安全隔离与容器化工具                                                                        |
| open-dice                   | 可信计算与 Root of Trust 相关库                                                             |
| openthread                  | Thread 协议栈实现                                                                           |
| perfetto                    | 系统级性能跟踪与分析工具                                                                    |
| protobuf, tflite-support    | Google Protocol Buffers 和 TensorFlow Lite 支持库                                            |
| quickjs, lua                | 脚本语言解释器                                                                              |
| roboto-fonts, noto-fonts    | Google 字体库                                                                               |
| sqlite                      | 嵌入式 SQL 数据库                                                                           |
| tensorflow, pytorch, XNNPACK| 机器学习/深度学习框架与加速库                                                              |
| toybox, busybox             | 常用 Linux 命令集合                                                                         |
| wayland, wayland-protocols  | Linux 显示协议与桌面支持                                                                    |
| webp, webrtc                | 图片压缩格式、实时音视频通讯库                                                              |
| wpa_supplicant_8            | Wi-Fi 无线连接与加密支持                                                                    |
| xz-embedded, zlib, zopfli, zstd | 各类压缩解压库                                                                        |
| zlib                        | 通用压缩库                                                                                  |
| zstd                        | 快速压缩算法库                                                                              |
| zucchini   | 差分补丁生成与应用库，主要用于高效构建二进制补丁（如 OTA 升级、Chrome 更新）|

frameworks
| 目录          | 主要功能说明                                                                                  |
|---------------|-----------------------------------------------------------------------------------------------|
| av            | 多媒体框架和服务，包括音视频编解码、媒体会话、相机等 (MediaCodec, MediaRecorder, Camera等)     |
| base          | Android Framework 层核心（Java API、系统服务、AMS、PMS、ContentProvider、UI、权限等）          |
| compile/      | Java 字节码和 dex 相关的编译与工具（如 dx、bytecode 编译辅助）                                 |
| ex            | Android 扩展包支持库（如文档、邮箱、日历等扩展）                                               |
| hardware/     | 与硬件相关的框架封装（如传感器、输入、Vibrator、相机等抽象接口/服务）                          |
| layoutlib     | 布局渲染库（Android Studio 预览、UI 设计时所用的 layout 渲染引擎）                             |
| libs/         | 框架层通用 Java 库与工具库（如 android-common、android-compat 等）                             |
| minikin       | 字体布局与排版引擎                                                                             |
| multidex      | MultiDex 支持库，实现超 65536 方法限制时的多 DEX 支持                                          |
| native        | Framework 层 Native (C++) 服务、JNI 桥、核心底层服务                                           |
| opt/          | 各类可选框架和实验性/优化模块（如 telephony, emoji, net, mediaextractor 等）                    |
| proto_logging | ProtoBuf 日志结构定义与日志框架（服务进程统一日志采集）                                        |
| rs            | RenderScript 相关框架，异构并行计算（已弃用/兼容保留）                                         |
| wilhelm       | OpenSL ES、OpenMAX AL 多媒体音频标准接口实现                                                   |

hardware
| 目录                  | 主要功能说明                                                            |
|-----------------------|-------------------------------------------------------------------------|
| broadcom/             | Broadcom 芯片相关的 HAL 适配、硬件支持代码                              |
| google/               | Google 参考设备/硬件适配、Google 自有硬件 HAL 支持                      |
| interfaces/           | AIDL/HIDL 硬件抽象接口定义，所有主要 HAL 模块接口的标准声明              |
| invensense/           | InvenSense 传感器（如陀螺仪、加速度计）相关的 HAL 适配                  |
| libhardware/          | HAL 层通用兼容库，C 接口适配、底层硬件抽象                              |
| libhardware_legacy/   | 旧版硬件 HAL 兼容库，为老设备和接口提供支持                              |
| nxp/                  | NXP 芯片相关（如 NFC、Secure Element 等）硬件适配                        |
| qcom/                 | Qualcomm（高通）平台相关 HAL 适配、芯片驱动代码                         |
| ril/                  | Radio Interface Layer，无线通信基带/射频模块的 HAL 适配和接口            |
| samsung/              | 三星平台/芯片/设备的 HAL 适配、传感器支持等                              |
| st/                   | STMicroelectronics（意法半导体）设备适配及相关 HAL 支持                  |
| synaptics/            | Synaptics 触控板、传感器、指纹等设备的 HAL 支持                         |
| ti/                   | Texas Instruments（德州仪器）设备/芯片适配、相关 HAL 实现                |

kernel
P.S.: kernel源码不在platform里面需要下载kernel分支
| 目录            | 主要功能说明                                                                 |
|-----------------|------------------------------------------------------------------------------|
| bootable/       | 内核启动加载器、ramdisk、引导相关工具与脚本                                  |
| build/          | 内核构建脚本、配置文件、构建系统集成辅助                                     |
| common/         | 通用主线内核代码（GKI/主线通用分支参考实现，支持多平台）                     |
| common-modules/ | 通用主线内核模块（如文件系统、设备驱动、网络等的标准模块化实现）              |
| external/       | 第三方内核模块、外部驱动与扩展（如 zstd、lz4、wireguard 等）                  |
| kernel/         | 具体平台/SoC 内核实现（部分平台有单独的 kernel/<vendor>/<chip> 目录）         |
| prebuilts/      | 预编译的内核镜像、模块、头文件等，方便直接集成使用                           |
| system/         | 内核态系统相关代码，如安全、内存管理、调度器等                               |
| test/           | 内核测试用例、验证工具和测试框架（如 kselftest、LTP 等）                      |
| tools/          | 内核构建、分析、调试等辅助工具（如 scripts、perf、bpf、tracing、objtool 等）   |

libcore
| 目录            | 主要功能说明                                                                |
|-----------------|-----------------------------------------------------------------------------|
| api/            | Java API 定义和 API 版本管理（API 文档、API 差异比对等）                    |
| benchmarks/     | Java 标准库相关性能基准测试                                                 |
| dalvik/         | Dalvik VM 相关 Java 底层类和兼容实现                                        |
| dom/            | XML DOM 解析和相关类实现                                                    |
| expectations/   | 测试期望、黑名单/白名单管理等                                               |
| harmony-tests/  | 兼容 Apache Harmony 项目的 Java 标准库测试                                 |
| json/           | JSON 解析和处理相关库实现                                                   |
| jsr166-tests/   | JSR-166 并发包（java.util.concurrent）的兼容和测试                         |
| libart/         | ART 虚拟机相关 Java 层桥接与接口实现                                        |
| luni/           | java.lang、java.util、java.io 等核心标准库源码（主力实现）                  |
| metrictests/    | 度量/指标相关的测试用例                                                     |
| mmodules/       | 模块化相关支持与实现                                                        |
| ojluni/         | OpenJDK “luni”类（java.lang, java.util, java.io等）的同步移植及支持         |
| support/        | 兼容性支持代码、工具与适配层                                                |
| test-rules/     | 测试运行规则、辅助类与测试基类                                              |
| toolchainapi/   | 构建和工具链相关 Java API                                                   |
| tools/          | 相关开发、构建、分析和辅助工具                                              |

libnativehelper
| 目录                          | 主要功能说明                                      |
|-------------------------------|---------------------------------------------------|
| header_only_include/          | 仅头文件实现的通用辅助头（供构建加速和平台兼容）   |
| include/                      | 通用公共头文件（如 JNIHelp.h、JniConstants.h 等）  |
| include_jni/                  | JNI 相关头文件，JNI API 辅助声明                   |
| include_platform/             | 平台相关的头文件                                   |
| include_platform_header_only/ | 平台相关的仅头文件实现                             |
| tests/                        | 单元测试代码，验证 NativeHelper 功能正确性         |
| tests_mts/                    | 多平台和多场景下的测试用例                         |

packages
| 目录            | 主要功能说明                                                              |
|-----------------|---------------------------------------------------------------------------|
| apps/           | 系统应用（如 Settings、Launcher、Calendar、Contacts、Music、Gallery 等）   |
| inputmethods/   | 输入法相关应用（如 AOSP Keyboard、拼音输入法等）                          |
| modules/        | 可独立升级的系统模块（APEX 包，如 media、conscrypt、statsd 等）           |
| providers/      | 各类系统 ContentProvider（如 Downloads、MediaProvider、UserDictionary 等）|
| screensavers/   | 屏保/显示相关应用（如 Dream、Clock 等）                                   |
| services/       | 系统服务 App（如 Telephony、PrintSpooler、CarrierConfig 等）              |
| wallpapers/     | 系统壁纸应用和动态壁纸（如 LiveWallpapers、PhotoPhase 等）                |

pdk
| 目录     | 主要功能说明                                                  |
|----------|---------------------------------------------------------------|
| apps/    | 平台开发套件（PDK）相关的示例应用与测试 App                   |
| build/   | PDK 构建脚本、配置文件和辅助工具                              |
| util/    | PDK 平台开发和适配相关的通用工具、脚本与辅助库                |

platform_testing
| 目录          | 主要功能说明                                                         |
|---------------|----------------------------------------------------------------------|
| build/        | 平台级测试相关构建脚本、配置和集成支持                                |
| docs/         | 平台测试框架、流程、用例等文档                                        |
| emu_test/     | 针对模拟器（Emulator）的自动化测试用例和相关支持                      |
| host_runners/ | 主机端（PC）测试执行器和测试环境集成                                  |
| libraries/    | 平台测试用的公共类库和工具包                                          |
| robolab/      | RoboLab 自动化测试相关代码和集成                                      |
| scripts/      | 测试辅助脚本、自动化工具和批量操作脚本                                |
| tests/        | 平台级功能、性能、兼容性等自动化测试用例主目录                        |
| tools/        | 平台测试相关的辅助工具、分析与环境管理                                |
| utils/        | 平台测试环境通用工具和功能模块                                        |

prebuilts
| 目录                | 主要功能说明                                                                 |
|---------------------|------------------------------------------------------------------------------|
| abi-dumps/          | 预编译 ABI 信息和 dump 文件（ABI 兼容性检查）                                 |
| android-emulator/   | 预编译的 Android 模拟器二进制及其依赖                                         |
| asuite/             | Android Studio/测试自动化套件的预编译工具                                     |
| bazel/              | Bazel 构建工具及相关依赖的预编译包                                            |
| build-tools/        | 预编译的构建辅助工具集合                                                      |
| bundletool/         | 预编译的 Android App Bundle 打包和分析工具                                    |
| checkcolor/         | 颜色检查工具的预编译版本                                                      |
| checkstyle/         | Java 代码风格检查工具预编译版                                                 |
| clang/              | LLVM/Clang 编译器工具链的预编译包                                             |
| clang-tools/        | Clang 相关辅助工具预编译包                                                    |
| cmake/              | CMake 跨平台构建工具预编译包                                                  |
| cmdline-tools/      | 各类命令行构建和开发工具的预编译包                                            |
| devtools/           | 各类开发辅助工具的预编译包                                                    |
| gcc/                | GNU GCC 编译器及工具链的预编译包                                              |
| go/                 | Go 语言工具链的预编译包                                                       |
| gradle-plugin/      | Android Gradle 插件预编译包                                                   |
| jdk/                | Java Development Kit（JDK）的预编译包                                         |
| ktlint/             | Kotlin 代码风格检查工具预编译包                                               |
| manifest-merger/    | Android Manifest 合并工具预编译包                                             |
| maven_repo/         | 本地 Maven 仓库的依赖和预编译包                                               |
| misc/               | 杂项预编译工具和依赖                                                          |
| module_sdk/         | 各模块相关 SDK 的预编译文件                                                   |
| ndk/                | Android Native Development Kit 的预编译包                                     |
| qemu-kernel/        | QEMU 虚拟机用的预编译内核镜像                                                 |
| r8/                 | Java 字节码优化与混淆工具（R8/D8）的预编译包                                  |
| remoteexecution-client/ | 远程构建与分布式测试相关预编译客户端工具                                 |
| runtime/            | 运行时相关库的预编译包                                                        |
| rust/               | Rust 编译器和工具链的预编译包                                                 |
| sdk/                | Android SDK 的各类预编译组件和工具                                            |
| tools/              | 其他通用辅助工具的预编译包                                                    |

sdk
| 目录            | 主要功能说明                                                            |
|-----------------|-------------------------------------------------------------------------|
| annotations/    | SDK 注解相关资源和库，用于辅助开发、代码提示等                           |
| apkbuilder/     | APK 构建工具和相关资源                                                  |
| apps/           | SDK 自带的示例应用和测试程序                                            |
| avdlauncher/    | Android 虚拟设备（AVD）启动器相关资源和工具                             |
| docs/           | Android SDK 文档（开发手册、API 参考等）                                |
| dumpeventlog/   | 事件日志分析与导出工具                                                  |
| emulator/       | Android 模拟器相关工具与资源                                            |
| eventanalyzer/  | 事件分析工具，用于分析应用或系统事件流                                  |
| files/          | SDK 工具和资源的存放目录                                                |
| find_java/      | 查找并设置 Java 环境的脚本                                              |
| find_java2/     | 新一代 Java 环境查找脚本                                                |
| find_lock/      | SDK 安全/并发相关辅助脚本                                               |
| hierarchyviewer/| Android 界面层级查看工具                                                |
| icons/          | 各类开发工具和模拟器的图标资源                                          |
| sdklauncher/    | SDK 启动管理工具                                                        |
| settings/       | SDK 相关的配置文件与设置                                                |
| templates/      | 各类项目/组件/Activity 的模板文件                                       |

system
| 目录                    | 主要功能说明                                                                     |
|-------------------------|----------------------------------------------------------------------------------|
| acpi/                   | ACPI 支持相关代码（高级配置与电源管理接口，主要用于特定设备）                     |
| apex/                   | APEX 容器管理工具和相关实现                                                      |
| authgraph/              | 用于图认证与安全的相关库                                                          |
| bpf/                    | eBPF（内核扩展字节码）相关接口与管理                                              |
| bpfprogs/               | eBPF 程序与相关实现                                                              |
| ca-certificates/        | 系统 CA 证书集合                                                                 |
| chre/                   | Context Hub Runtime Environment，低功耗传感器管理框架                            |
| connectivity/           | 网络连接管理、WIFI、蓝牙等连接服务框架                                            |
| core/                   | 系统服务、守护进程和关键功能（如 init、vold、logcat、servicemanager、liblog等）    |
| cros-codecs/            | Chrome OS 音视频编解码支持                                                        |
| dmesgd/                 | dmesg 日志采集、导出工具                                                          |
| extras/                 | 扩展功能、辅助工具等                                                              |
| gatekeeper/             | 生物识别、密码、Gatekeeper 安全相关实现                                           |
| gsid/                   | Google System Image Daemon（GSI 管理服务）                                       |
| hardware/               | 底层硬件相关服务与适配代码                                                        |
| hwservicemanager/       | HIDL 服务管理器，实现 HAL 层服务的注册与发现                                      |
| incremental_delivery/   | 增量交付支持相关工具与服务                                                        |
| keymaster/              | Keymaster 密钥管理、加密和安全服务实现                                            |
| keymint/                | KeyMint 安全密钥服务与新一代加密实现                                              |
| libartpalette/          | ART 虚拟机平台适配辅助库                                                          |
| libbase/                | C++ 通用基础库（字符串、IO、文件、线程等工具）                                    |
| libcppbor/              | CBOR 编解码库，支持二进制对象表示法                                               |
| libfmq/                 | Fast Message Queue（FMQ）消息队列通信库，HAL 常用                                 |
| libhidl/                | HIDL 相关通用支持库                                                              |
| libhwbinder/            | HwBinder 驱动的 C++ 支持库                                                        |
| libprocinfo/            | 进程信息采集工具库                                                                |
| librustutils/           | Rust 语言通用工具库                                                               |
| libsysprop/             | 系统属性读取/设置辅助库                                                           |
| libufdt/                | FDT（设备树）解析库                                                               |
| liburingutils/          | io_uring（高性能异步 IO）辅助库                                                   |
| libvintf/               | VINTF (Vendor Interface) 验证和解析库                                             |
| libziparchive/          | ZIP 文件归档、解包工具库                                                          |
| linkerconfig/           | 动态链接器环境与配置管理工具                                                      |
| logging/                | 系统日志相关库与支持                                                              |
| media/                  | 多媒体相关服务、库和工具                                                          |
| memory/                 | 内存管理相关（如 ashmem、ion、lmkd 等）                                           |
| netd/                   | 网络守护进程，管理 DNS、IP 配置、网络策略等                                       |
| nfc/                    | NFC 相关支持服务和工具                                                            |
| nvram/                  | NVRAM 非易失性存储管理支持                                                        |
| secretkeeper/           | 安全密钥/凭证存储与管理服务                                                       |
| secure_element/         | SE（安全元件）相关服务及驱动                                                      |
| security/               | 系统安全性相关（访问控制、SELinux、加密等）                                       |
| see/                    | Secure Execution Environment 相关支持                                             |
| sepolicy/               | SELinux 策略定义与管理                                                            |
| server_configurable_flags | 服务器可配置标志支持和相关管理                                                  |
| teeui/                  | TEE（可信执行环境）用户界面相关支持                                               |
| testing/                | 系统服务相关的测试用例和框架                                                      |
| timezone/               | 时区数据与时区服务支持                                                            |
| tools/                  | 系统服务和内核相关辅助工具                                                        |
| unwinding/              | 栈回溯与调用栈展开支持库                                                          |
| update_engine/          | OTA 系统升级引擎                                                                  |
| usb_info_tools/         | USB 设备信息采集和管理工具                                                        |

test
| 子目录                  | 说明                                                    |
|------------------------|-------------------------------------------------------|
| app_compat/            | 应用兼容性测试用例和工具                                 |
| catbox                 | 通用测试运行框架与工具集                                 |
| cts-root               | 针对具有 root 权限设备的 CTS（兼容性测试套件）扩展        |
| dittosuite             | 分布式或多端一致性测试套件                               |
| mlts/                  | 机器学习相关测试套件                                     |
| mts                    | 模块化测试套件（Module Test Suite）                      |
| robolectric-extensions | Robolectric（Android 单元测试框架）扩展与适配代码         |
| suite_harness          | 测试套件的统一调度与管理框架                             |
| vts                    | VTS（Vendor Test Suite，供应商测试套件）                 |
| vts-testcase/          | VTS 测试用例集合                                         |

toolchain
| 目录           | 主要功能说明                                    |
|----------------|-------------------------------------------------|
| pgo-profiles/  | Profile-Guided Optimization（PGO）性能分析与优化用的 profile 数据集与相关工具 |

tools
| 目录                        | 主要功能说明                                                                  |
|-----------------------------|-------------------------------------------------------------------------------|
| aadevtools                  | Android Auto 开发工具和相关脚本                                               |
| acloud                      | 云端/虚拟化测试与模拟器环境管理工具                                           |
| apifinder                   | API 检索和分析工具                                                            |
| apksig                      | APK 签名和验证工具                                                            |
| apkzlib                     | APK 及 ZIP 文件处理压缩相关工具                                               |
| asuite                      | Android 测试自动化框架和集成套件                                              |
| camera                      | 摄像头相关测试和开发工具                                                      |
| carrier_settings            | 运营商设置和配置相关工具                                                      |
| content_addressed_storage/  | 基于内容寻址的存储工具库（如用于资源去重、版本控制等）                        |
| currysrc                    | Java 源代码分析、格式化与重构工具                                             |
| deviceinfra/                | 设备基础设施和自动化测试环境配置                                              |
| dexter                      | DEX 文件分析和调试工具                                                        |
| doc_generation              | 文档自动生成工具                                                              |
| external/                   | 外部工具和第三方依赖                                                          |
| external_updater            | 第三方依赖/外部工具的自动更新工具                                             |
| loganalysis                 | 日志分析与自动化问题定位工具                                                  |
| metalava                    | Android API 描述和兼容性分析工具                                              |
| ndkports                    | NDK 端口和交叉编译相关工具                                                    |
| netsim                      | 网络仿真与虚拟化测试工具                                                      |
| platform-compat             | 平台兼容性测试与适配工具                                                      |
| repohooks                   | Repo 仓库管理钩子脚本                                                        |
| rr_prebuilt                 | Record & Replay 调试工具的预编译包                                            |
| security                    | 安全分析、加固、验证等相关工具                                                |
| test/                       | 各类测试工具和测试用例主目录                                                  |
| tradefederation/            | Android Trade Federation 自动化测试与设备编排框架                             |
| treble                      | Treble 项目相关工具和测试                                                     |
| trebuchet                   | 系统 UI 启动器、开发版 Launcher 等相关工具                                    |