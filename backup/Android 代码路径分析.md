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

adbconnection
artd
benchmark
build
cmdline
compiler
dalvikvm
dex2oat
dexdump
dexlayout
dexlist
dexoptanalyzer
disassembler
dt_fd_forward
imgdiag
libartbase
libartpalette
libartservice
libarttools
libdexfile
libelffile
libnativebridge
libnativeloader
libprofile
NOTICE
oatdump
odrefresh
openjdkjvm
openjdkjvmti
perfetto_hprof
profman
runtime
sigchainlib
simulator
test
tools