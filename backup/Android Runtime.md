1. runtime（art/runtime/）
运行逻辑：
ART 虚拟机主控层，负责 Java 类加载、对象创建、内存管理、线程管理、方法执行（解释、JIT、AOT）、异常捕获、同步、GC 协调等所有执行流的核心。
启动时加载 boot image，初始化 ClassLoader、JNI 环境、GC 策略、启动主线程，fork 子线程运行用户代码。

关键算法：

对象分配采用 TLAB（Thread Local Allocation Buffer）加速小对象分配。

方法调用多层 inline cache 与调用链预测。

线程管理基于 pthread 映射与调度优化。

优化点：

合理配置 GC 策略、内存池参数。

启动流程预加载常用类，减少冷启动开销。

多线程调度避免长时间锁争用与死锁。

2. dex2oat（art/dex2oat/）
运行逻辑：
DEX 到 OAT（本地代码）的 AOT 编译器，安装或 OTA 升级时将应用 DEX 字节码批量编译为特定平台的机器码，提高启动和运行效率。
支持多种编译策略（全部编译、按需编译、Profile 引导优化等）。

关键算法：

静态单赋值（SSA）中间表示，便于后续优化。

通用编译优化（常量传播、死代码消除、循环展开、内联、寄存器分配）。

指令选择与平台专用调度（x86/arm/arm64）。

优化点：

Profile Guided Compilation：利用运行时采集的热代码数据只编译热点，降低安装时延和空间消耗。

按需编译（Just-in-Time）与 AOT 策略结合。

支持“增量编译”减少 OAT 刷新压力。

3. odrefresh（art/odrefresh/）
运行逻辑：
负责 OAT/DEX 优化文件的检查、刷写、增量重编译，监控 DEX、OAT、VDEX 之间的一致性。用于系统升级、补丁、硬件变更后自动修复编译产物，保证兼容性和性能。

关键算法：

文件校验与元数据比对。

最小集增量编译策略（只编译真正需要更新的部分）。

优化点：

提升检测效率（文件哈希、时间戳比对）。

合理安排后台编译时机，降低用户感知时延。

4. oatdump（art/oatdump/）
运行逻辑：
OAT 文件分析与调试工具，用于解析 OAT 文件内部结构（类、方法、本地码、映射关系）、反汇编本地机器码，支持开发调试和安全分析。

关键算法：

ELF/OAT 文件头解析。

指令反汇编、符号地址映射。

优化点：

提升大文件解析与搜索速度。

支持多架构和指令集动态适配。

5. dalvikvm（art/dalvikvm/）
运行逻辑：
历史兼容进程入口，解析 dalvikvm 参数后实际调起 ART runtime，保证老版本应用或脚本兼容性。

关键算法：

启动参数和环境适配映射。

优化点：

精简兼容路径，保证兼容性同时减少维护负担。

6. libartbase / libartpalette（art/libartbase/, art/libartpalette/）
运行逻辑：
ART 虚拟机的基础工具与平台适配层。libartbase 提供数据结构、日志、调试、错误处理。libartpalette 提供不同硬件/OS 下的抽象实现（如文件、线程、锁等）。

关键算法：

平台无关的 STL 优化与错误处理。

优化点：

针对目标平台定制内存池、线程同步等底层细节。

7. libdexfile（art/libdexfile/）
运行逻辑：
负责 DEX 文件格式的解析、验证、接口抽象、Class 查找、指令流读取，是 ART 和 Dalvik 运行时字节码操作的核心。

关键算法：

DEX 头部和 classdef 查找加速索引。

指令流快速遍历与解码表驱动。

优化点：

多线程安全的类索引缓存。

大型 DEX 多索引文件的解析效率优化。

8. libart-compiler（art/compiler/）
运行逻辑：
ART 专用编译器，提供字节码到本地代码的 JIT/AOT 转换核心，包含前端解析、中端优化、后端机器码生成，供 dex2oat/JIT 使用。

关键算法：

SSA 变换、中间表示（IR）、数据流分析。

LLVM 风格的优化 passes（循环优化、常量传播、寄存器分配）。

平台特定指令选择与调度。

优化点：

热点函数优先、冷热分区优化。

交叉平台指令生成的调优。

9. libart-disassembler（art/disassembler/）
运行逻辑：
用于将 DEX 字节码或 OAT 本地指令反汇编成人类可读格式，方便开发调试、逆向分析和安全审计。

关键算法：

指令流映射与模式匹配。

优化点：

提升跨指令集架构适配能力。

输出格式丰富化（JSON、XML、汇编代码）。

10. gc（art/runtime/gc/）
运行逻辑：
ART 的垃圾回收（GC）核心，实现对象内存回收、堆整理、弱引用管理。支持多种 GC 策略：CMS、标记-清除、标记-整理、并发 GC。

关键算法：

分代 GC、并发标记-清除、增量 GC、卡表（Card Table）写屏障。

并发/增量回收，最小化停顿时间（STW）。

优化点：

分配速率/垃圾产生速率监控，动态调整 GC 策略。

TLAB 优化减少锁竞争。

背景 GC 与前台业务解耦。

11. jit（art/runtime/jit/）
运行逻辑：
即时编译（JIT）模块，在应用运行时将热点方法/代码段动态编译为本地代码，提升执行效率。JIT 编译后的结果可通过 profile 反馈给下次 AOT 编译。

关键算法：

热度计数与采样、逃逸分析、方法内联、OSR（On-Stack Replacement）。

Inline cache 预测与去虚拟化。

优化点：

动态调整编译阈值，防止过多 JIT 导致卡顿。

优化编译缓存管理与回收。

12. interpreter（art/runtime/interpreter/）
运行逻辑：
DEX 字节码解释器，负责非热点代码和首次运行代码的执行，支撑 JIT、AOT 切换和调试。

关键算法：

表驱动和 switch-case 优化指令分发。

直接/间接线程机制提升解释器性能。

优化点：

热路径内联解释。

指令流预测减少分支误判。

13. imgdiag（art/imgdiag/）
运行逻辑：
Image 文件分析工具，用于 system image（如 boot.art、system.art）的校验、结构解析、故障排查。

关键算法：

ELF/Image 文件结构解析。

优化点：

边缘 case 的鲁棒性提升。

14. profile（art/profile/）
运行逻辑：
采集方法、类、代码块的执行热度与调用信息，支撑 Profile Guided Compilation（PGC），为 JIT/AOT 提供数据驱动优化依据。

关键算法：

采样、热点识别、Profile 数据合并与压缩。

优化点：

Profile 数据持久化和跨版本兼容。

合理冷热分区，提升命中率。

15. quick（art/runtime/quick/）
运行逻辑：
快速方法调用实现，支持内联、调用链优化、fast-path 路径。

关键算法：

方法表缓存、快速分派表（QuickInvoke Table）。

优化点：

结合 JIT 热方法实时优化。

缓存回收与命中率提升。

16. signal_catcher（art/runtime/signal_catcher.cc）
运行逻辑：
捕获 native 信号（如 SIGSEGV、SIGABRT）、处理崩溃转储、ANR 事件，协助生成堆栈、日志、dump 文件，支撑开发调试和线上排障。

关键算法：

信号链管理、线程上下文保存与恢复。

优化点：

dump 信息脱敏与采样降噪。

限制 dump 频率，防止资源耗尽。

17. monitor（art/runtime/monitor.cc）
运行逻辑：
ART 的 Java 对象锁与同步机制，支持 synchronized、wait/notify、条件变量。

关键算法：

轻量级锁、偏向锁、锁膨胀与自旋锁优化。

优化点：

避免锁竞争和死锁，合适使用细粒度锁和无锁结构。

18. verifier（art/runtime/verifier/）
运行逻辑：
DEX 字节码静态校验，检查类型安全、栈平衡、分支有效性，预防恶意代码和崩溃。

关键算法：

数据流分析，类型流跟踪与校验。

优化点：

大型 DEX 并行校验。

更丰富的静态检查规则。

19. debugger（art/runtime/debugger/）、jdwp（art/runtime/jdwp/）
运行逻辑：
ART Java 调试支持（JDWP 协议），实现断点、单步执行、变量观察、远程调试等，配合 Android Studio。

关键算法：

调试信息映射表、事件分发。

优化点：

调试时性能损耗降至最低。

动态启停调试会话。

20. trace（art/runtime/trace/）
运行逻辑：
方法调用、线程事件、性能追踪，支持 systrace、method trace、call graph 等多种追踪模式，便于性能瓶颈定位。

关键算法：

环形缓冲、事件压缩、异步采集。

优化点：

动态采样率调整，减少性能干扰。

21. hidden_api（art/runtime/hidden_api/）
运行逻辑：
实现对 Android Framework 隐藏/内测 API 的访问控制，阻止三方 App 滥用系统内部接口，支撑灰名单、黑名单管控。

关键算法：

白名单/黑名单判定。

调用路径追踪。

优化点：

兼容老应用的灰名单例外处理。

动态 API 调用记录与审计。

22. hprof（art/runtime/hprof/）
运行逻辑：
Java 堆快照与分析工具，采集 heap dump 支持内存泄漏、对象分析。

关键算法：

堆遍历与对象图构建、分代采样。

优化点：

dump 性能提升，文件压缩存储。

大堆场景下分批次 dump。

23. libcore（libcore/）
运行逻辑：
Java 标准库和基础 API 实现，涵盖集合、IO、并发、网络、安全、ICU、XML、JSON、加密等所有 Java 应用基础功能。ART/Dalvik 运行时调用。

关键算法：

各类通用算法（排序、hash、同步、SSL/加密等）。

优化点：

定期合并 OpenJDK 新算法和安全补丁。

针对移动端内存、性能裁剪与定制。

24. OAT 相关（art/dex2oat/、art/runtime/oat_file.h、art/oatdump/、art/odrefresh/）
运行逻辑：
OAT 文件作为 ART DEX AOT 编译产物，存储优化机器码、方法元数据、vtable 等。运行时按需映射到内存直接执行。

关键算法：

机器码布局优化、热路径排序。

优化点：

合理分区提升热代码命中率。

支持增量 patch 与兼容校验。

25. JNI（art/runtime/jni/、libcore/ojluni/src/main/native/include/jni.h）
运行逻辑：
Java/Native 层交互桥梁，实现方法互调、对象传递、native 注册、数据交换。

关键算法：

本地引用管理、线程 attach/detach。

优化点：

避免跨 JNI 边界高频调用。

用 DeleteLocalRef 管理大批量局部引用。

26. libnativebridge（art/libnativebridge/）
运行逻辑：
Native Bridge 机制，支持不同 ABI native so 的仿真/兼容运行（如 ARM64 跑 x86 so），供厂商（如 Intel Houdini）扩展。

关键算法：

ABI 转换、符号重定位、函数转发。

优化点：

提升仿真性能，减少兼容层 overhead。

限制高危系统调用，增强安全性。

27. libnativeloader（art/libnativeloader/）
运行逻辑：
Native so 动态加载和 namespace 隔离，支撑 System.loadLibrary、ClassLoader 加载和多用户分区安全。

关键算法：

so 路径搜索、dlopen 隔离、命名空间映射。

优化点：

防 so 劫持与依赖冲突，优化加载路径和缓存策略。


### 三星在 ART 层的主要修改/扩展点
1. 安全/加固与 Knox 集成
hidden_api、verifier、JNI

增强隐藏 API 管控：为 Knox/企业/系统 App 加白名单、实现更细粒度的 API 权限。

DEX 校验/反作弊：可能在 verifier 里强化防篡改、反动态注入的校验逻辑。

native 层安全：JNI 侧接入 Knox 审计、增强 native 调用日志/行为监控。

libcore

对核心 Java 类库中的加密、网络、文件系统 API 加入企业定制（如 Knox 审计接口、国密算法兼容）。

2. 性能与平台适配优化
GC/JIT/Profile

优化 GC 策略和参数，适配自家硬件（如大内存、多核、特定 CPU/GPU 组合），可能有自定义的 profile 或特殊内存管理钩子。

dex2oat / libart-compiler

编译器参数和优化 pass 调整，兼容三星自研/供应链芯片的最佳运行表现。

3. 多媒体/显示/硬件协同增强
JNI/so 加载

加载自有图形、多媒体、S Pen、显示等 native 扩展库时，引入自有 namespace 隔离或注册流程，防止依赖冲突。

libnativeloader

定制 so 加载路径和隔离策略，保证三星自有硬件驱动、Knox 加固库优先/独立加载。

4. 企业/Knox/特权 App 支持
hidden_api、monitor、JNI

特权 App 通过企业白名单直接访问部分隐藏 API，或注入自有 debug/hook 能力。

可能定制 monitor（锁/同步）行为，实现安全隔离或企业需求（如强隔离/加密对象）。

signal_catcher/debugger

增强 crash/anr dump，采集更丰富的日志供企业或 Knox 安全分析。

5. 自有模块/补丁
libartbase/自有扩展

三星可能引入一些自有的 runtime 扩展模块（如企业安全日志、远程管理接口、特殊加密实现等），位于 vendor/samsung/、system/knox/ 或补丁方式 patch 到 ART 目录。

libnativebridge

如有异构 CPU 支持、兼容特殊硬件架构（如自研 SoC），会定制 Native Bridge 实现。


### 电视领域厂商在 ART 层的定制与扩展
1. 系统资源与性能适配优化
GC 策略和堆参数调整：

厂商常针对电视 SoC 的内存配置、分辨率、后台进程数量等，定制垃圾回收（GC）参数，比如增加最大堆大小、调整分代比例、GC 触发阈值，使得 TV App/Launcher/UI 流畅不卡顿。

有的电视（如索尼、TCL）会 patch ART 启动参数，设置更高的“Large Heap”模式，支持长时间大分辨率视频流与多 App 共存。

JIT/AOT/编译策略：

某些厂商根据开机速度、冷启动/热启动体验，动态调整 dex2oat/JIT 策略，如优先 Profile Guided Compilation，或仅对 Launcher、媒体解码等核心 App 采用全编译，非核心应用用解释/JIT，降低系统负担。

odrefresh 增量编译/后台静默优化：

电视厂商常在“待机/屏保”时段后台触发 OAT 优化，利用空闲时间减少对用户体验的影响。

部分定制电视固件还会 patch odrefresh，支持低温、休眠后自动增量刷新。

2. 多媒体/显示/输入扩展支持
JNI/Native so 加载机制定制：

电视厂商会在 libnativeloader 层 patch，使自有媒体/显示/解码/输入等 native 库优先在特定 namespace/路径加载，确保自家定制的解码器、PQ（Picture Quality）算法、遥控/输入驱动、CI+ 智能卡接口不会被第三方 so 劫持或冲突。

如 TCL/海信/长虹会把自有 PQ/AQ、图像处理、HDMI/CEC/遥控等 so 作为“厂商专用库”，由 ART 动态加载机制单独隔离。

JNI/hidden_api 放宽/灰名单：

厂商会给自有 Launcher、设置、遥控、PQ/AQ 等核心 App 开放部分隐藏 API 访问权，常通过 patch hidden_api、JNI 注册逻辑实现。

3. 专有硬件/外设支持
JNI、libnativebridge 扩展：

支持如 CI+ 智能卡、DVB/ATSC 调谐器、远场麦克风、语音芯片等专有硬件，厂商通过 JNI 和 native bridge 支持跨架构 native so 加载（如 x86 电视/仿真器上跑 arm so）。

信号捕捉、trace、debugger 定制：

为了更好地支持电视专有输入设备、音视频同步和大屏交互，厂商会 patch trace/debugger 模块，加入遥控/HDMI/CEC/按键等特有事件和统计。

4. 安全与 DRM/认证集成
libcore、hidden_api、JNI 定制：

各大 TV 厂商对 DRM、CA（Conditional Access）、智能卡认证等做扩展，会 patch libcore 的加密/SSL/CA 相关实现，或在 JNI 桥接厂商自有 DRM/解密库。

DRM 安全机制会在 ART、JNI 层加黑/白名单、完整性校验和日志采集接口，防止内容泄漏和逆向。

5. 厂商专有模块/补丁
代码结构：

多数 patch、扩展会以 patch/overlay 方式作用于 art/、libcore/、frameworks/base/ 等主目录，或以专有模块（如 vendor/<brand>/、hardware/<brand>/）方式注入。

部分“Launcher/PQ/遥控/音效”等会有独立的厂商 runtime 扩展库或 so。

具体实现通常闭源，仅有部分开源或文档（如索尼、TCL 海外版部分开源，AOSP Gerrit 有相关记录）。

6. 实际应用场景举例
索尼 BRAVIA：为其自研图像增强（X-Reality Pro）、多分区 Launcher、Voice Control 提供专用 so 隔离加载，JNI 直接桥接自有硬件。

TCL/海信/长虹：专有 PQ/AQ、CI+、遥控/AI 语音、输入法、图像增强全部在 ART 层打 patch、JNI 桥接，并控制 so 加载命名空间。

小米电视：Launcher、PatchWall、大屏互动专有功能，通过 hidden API 灰名单和 JNI、Profile 优化 ART 执行。