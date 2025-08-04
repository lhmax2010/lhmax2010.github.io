art    Android Runtime (ART) 虚拟机和编译、解释、GC等相关实现  
  -- benchmark    ART 性能基准测试  
  -- cmdline    ART 命令行工具  
  -- compiler    AOT/JIT 编译器实现  
  -- dalvik    Dalvik 兼容支持  
  -- dex2oat    DEX 到 OAT 转换工具  
  -- dexlayout    DEX 文件布局优化  
  -- dexlist    DEX 内容浏览工具  
  -- dexoptanalyzer    DEX 优化分析工具  
  -- imgdiag    OAT/Image 文件诊断工具  
  -- libartbase    ART 基础库  
  -- libartpalette    平台相关抽象接口  
  -- libdexfile    DEX 解析与操作库  
  -- libprofile    运行时分析配置与管理  
  -- oatdump    OAT 文件查看与反汇编  
  -- odrefresh    OAT/DEX 刷新维护工具  
  -- openjdkjvmti    JVM TI 实现  
  -- patchoat    OAT 文件补丁升级  
  -- profile    运行时分析配置/处理  
  -- runtime    ART 运行时核心  
  -- sigchainlib    信号链库  
  -- test    单元与集成测试  
  -- tools    辅助开发工具  

bionic    Android C 标准库和运行时库（libc、libm、libdl等）  
  -- android    平台相关实现  
  -- benchmarks    libc 性能测试  
  -- bionic    libc 主体源码  
  -- docs    文档  
  -- dynamic_linker    动态链接器  
  -- gtest    Google Test 集成  
  -- include    头文件  
  -- libc    libc 实现  
  -- libdl    动态库加载器  
  -- libm    数学库  
  -- libstdc++    C++ 标准库兼容实现  
  -- linker    链接器源码  
  -- locale    本地化实现  
  -- scripts    构建脚本  
  -- tests    测试用例  
  -- tools    辅助工具  
  -- upstream    上游兼容代码  

bootable    各类启动加载程序与恢复相关实现  
  -- bootloader    旧版 bootloader  
  -- bootloader_lk    Little Kernel (LK)  
  -- bootloader_message    bootloader 与 Android 间消息协议  
  -- diskinstaller    磁盘安装器  
  -- dovetail    Dovetail 启动流程  
  -- dtbo_img_utils    DTBO 镜像工具  
  -- edk2    UEFI 启动相关  
  -- fastboot    fastboot 工具及协议  
  -- mkbootimg    Boot 镜像打包  
  -- recovery    Recovery 恢复模式实现  
  -- second_stage    Recovery 二阶段  
  -- tools    启动辅助工具  

build    构建系统相关  
  -- blueprint    Blueprint 构建系统模块  
  -- make    Makefile、通用构建规则  
  -- soong    Soong 构建系统主引擎  
  -- tools    构建辅助工具  
  -- kati    Kati 构建工具  
  -- support    构建辅助脚本  

cts    兼容性测试套件（CTS）  
  -- apps    测试App  
  -- common    公共基类、工具  
  -- development    CTS 开发辅助  
  -- docs    文档  
  -- hostsidetests    主机端测试  
  -- lib    公共库  
  -- tests    各类测试项  
  -- tools    测试工具  
  -- verifier    CTS Verifier  

dalvik    早期 Dalvik VM 相关遗留代码  
  -- dexgen    DEX 生成工具  
  -- doc    文档  
  -- libdex    DEX 操作库  
  -- tools    工具  
  -- vm    Dalvik VM 核心  

developers    面向开发者的文档、工具及演示（部分 AOSP 版本有）  

development    各类开发辅助工具和示例  
  -- apps    演示App和测试App  
  -- build    构建工具  
  -- cmdline    命令行工具  
  -- host    主机端工具  
  -- ide    IDE 插件/支持  
  -- samples    示例代码  
  -- scripts    辅助脚本  
  -- tools    辅助开发工具  

device    各设备/芯片/厂商适配与配置  
  -- google    Google 设备（Pixel等）适配  
  -- mediatek    MTK 设备适配  
  -- sony    索尼设备适配  
  -- samsung    三星设备适配  
  -- ...    其他设备/厂商适配  

external    第三方开源库与外部项目集合  
  -- ...    各类第三方依赖库（如 openssl、skia、libpng、zlib 等）  

frameworks    Android 框架层代码（Java与Native）  
  -- av    多媒体框架与服务  
  -- base    Java 框架主干，系统服务/核心API  
  -- ml    机器学习相关（NNAPI等）  
  -- native    Native服务层  
  -- opt    可选优化/实验性模块  

hardware    硬件抽象层与通用硬件库  
  -- interfaces    HAL 层接口（AIDL/HIDL）  
  -- libhardware    HAL 兼容库  
  -- libhardware_legacy    旧版 HAL 兼容库  
  -- ...    其他硬件适配  

kernel    内核源码（一般仅补丁/配置，完整内核需单独获取）  
  -- ...    内核补丁、配置、参考实现等  

libcore    Java 基础类库实现（如 java.lang, java.util等）  
  -- dalvik/src    Dalvik VM 相关基础类  
  -- dex/src    DEX 相关基础类  
  -- dom/src    DOM 相关基础类  
  -- luni/src    java.lang/java.util实现  
  -- xml/src    XML 相关基础类  

libnativehelper    Native 层与 Java 层桥接辅助库  
  -- include    头文件  
  -- JNIHelp    JNI 辅助实现  
  -- ...  

packages    各类系统应用与服务  
  -- apps    系统应用（如 Settings、Launcher 等）  
  -- providers    各类 ContentProvider  
  -- services    系统服务App  
  -- experimental    实验性/演示 App  
  -- inputmethods    输入法相关App  

pdk    平台开发套件相关资源  

platform_testing    平台级测试工具集  

prebuilts    预编译三方库/SDK/工具链  
  -- clang    Clang 预编译工具链  
  -- sdk    预编译SDK  
  -- tools    预编译工具  
  -- ...    其他预编译库  

sdk    Android SDK 工具、API、文档生成等  

system    系统服务、守护进程与核心库  
  -- core    init、vold、healthd等守护进程  
  -- libhidl    HIDL 支持库  
  -- sepolicy    SELinux 策略  
  -- netd    网络守护进程  
  -- suspend    电源管理  
  -- timezone    时区数据  
  -- ...  

test    各类通用测试用例与框架  

toolchain    工具链源码与构建工具  

tools    构建、开发、调试、模拟器、API、静态分析、分区等通用工具集

  -- apicheck           API 变更检查工具（API 兼容性审查）
  -- apkzlib            APK 压缩与解压相关工具
  -- appbundle          Android App Bundle 构建与分析工具
  -- art                ART 相关开发和测试工具
  -- bugreport          bugreport 文件生成和分析工具
  -- build              各类构建辅助工具和脚本
  -- checkapi           检查 API 兼容性和变化
  -- codecoverage       代码覆盖率统计工具
  -- customlint         自定义 Lint 检查工具
  -- dalvik             Dalvik 相关开发工具
  -- emulator           Android 模拟器核心及相关工具
  -- lint               Java/Kotlin 源码静态检查工具
  -- loganalysis        日志分析工具
  -- lunch              lunch/build 相关脚本
  -- profman            ART 性能分析工具
  -- repo               AOSP 多仓库源码管理工具（repo 脚本）
  -- testutils          单元测试/集成测试辅助工具
  -- treble             Treble 相关工具（分区、接口稳定性等）
  -- vndk               VNDK（Vendor Native Development Kit）相关工具
  -- apksig             APK 签名验证与生成工具
  -- apkanalyzer        APK 结构和资源分析工具
  -- droiddoc           Android API 文档生成工具
  -- external           外部依赖库及工具
  -- layoutlib          布局渲染与分析库（Android Studio 预览用）
  -- libcore_stubdoc    libcore API 文档工具
  -- lint-checks        Lint 检查规则集合
  -- metalava           Android 官方 API 生成与文档工具
  -- proguard           ProGuard 代码混淆与优化工具
  -- signapk            APK/ZIP 签名工具
  -- tradefed           测试框架 Trade Federation
  -- aapt               Android Asset Packaging Tool (AAPT)
  -- aapt2              AAPT 新一代资源打包工具
  -- dexdeps            DEX 依赖分析工具
  -- dx                 DEX 文件生成工具
  -- hprof-conv         Java Heap Profile 转换工具
  -- systrace           系统级 trace/性能追踪工具
  -- sqlite3            sqlite3 命令行工具
  -- mkbootfs           boot.img 打包辅助工具
  -- mkyaffs2image      YAFFS2 镜像生成工具
  -- mksquashfs         squashfs 镜像打包工具
  -- mklinuxramdisk     Linux ramdisk 生成工具
  -- fs_config_generator  文件系统权限配置生成器
  -- dumpkey            公钥导出工具
  -- etc1tool           ETC1 纹理压缩与解压
  -- mkdtboimg          DTBO 镜像打包工具
  -- minijail           容器化与安全隔离工具
  -- microdroid         Microdroid 相关工具和脚本
  -- perfprofd          性能分析守护进程
  -- selinux            SELinux 策略工具和分析器
  -- simg2img           sparse image 转 raw image 工具
  -- simg2simg          raw image 转 sparse image 工具
  -- split-select       资源拆分与选择工具
  -- test_apks          用于测试的 APK 包集合
  -- vendor_hook        厂商自定义构建钩子脚本/工具

vendor    各芯片/厂商专用驱动、闭源库、系统扩展  
  -- ...    具体厂商定制目录  
