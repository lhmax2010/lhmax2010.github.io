### AOSP & Google 官方主要模块
| 模块/特性                      | 功能简介                                                         | 主要路径/层级                                              |
|-------------------------------|------------------------------------------------------------------|------------------------------------------------------------|
| Game Mode（游戏模式）          | App声明游戏模式，系统自动切换性能/省电策略                        | frameworks/base/services/core/java/com/android/server/gamemanager/ |
| Game Manager Service           | 管理游戏策略、性能档位、调度及通知                                | frameworks/base/services/core/java/com/android/server/gamemanager/ |
| Game Dashboard/Game Toolbar    | 游戏浮窗、录屏、截图、性能统计、免打扰、工具栏                    | Google Play Games / 系统UI                                 |
| ANGLE (Almost Native Graphics Layer Engine) | OpenGL ES 到 Vulkan 兼容层提升渲染效率               | external/angle/                                            |
| Game Driver（可选GPU驱动）     | Play新GPU驱动，指定游戏专属渲染优化                               | Google Play Services，vendor/                              |
| Game Mode APIs                 | Java/Kotlin API给开发者调整游戏体验                               | android.app.GameManager                                    |
| PerformanceHint API            | 系统性能提示（帧率等）协助资源调度                               | frameworks/base/core/java/android/os/PerformanceHintManager.java |
### Native/Kernel 层优化
| 模块/特性           | 功能简介                          | 主要路径                                     |
|--------------------|-----------------------------------|----------------------------------------------|
| SurfaceFlinger     | 显示合成优化，低延迟高帧率渲染      | frameworks/native/services/surfaceflinger/   |
| OpenGL ES/Vulkan   | 高效图形渲染API                    | frameworks/native/opengl/, external/vulkan/  |
| Binder 优化        | 进程间通信加速（减少延迟）          | drivers/android/binder.c                     |
| Scheduler/CPUFreq  | 游戏场景调度与动态频率管理          | kernel/sched/, drivers/cpufreq/              |
| InputManager       | 输入延迟优化                       | frameworks/base/services/input/              |
| AudioTrack Fast Path | 低延迟音频通道                   | frameworks/av/media/libmedia/                |
### 厂商自研/定制优化模块
| 厂商/模块名                 | 功能/亮点                                             |
|----------------------------|------------------------------------------------------|
| 三星 Game Booster / Plugins | 帧率/温控/网络/电池/GPU智能调度，AI场景识别，专用加速策略 |
| 华为 GPU Turbo              | 绘图指令重调度，异构加速，帧率提升，功耗降低              |
| 小米 Game Turbo/Acceleration| 游戏前台优先、网络优化、动态调频、内存/通知管理、性能模式   |
| OPPO Hyper Boost / 荣耀猎人  | AI资源分配，CPU/GPU/Vsync/网络多维调度                   |
| vivo Multi-Turbo/Game Mode  | 前台资源专属、低延迟链路优化、后台进程冻结                |
| ASUS ROG Armoury Crate      | 电竞模式、帧率锁定、风扇/温控/震动/独显协同               |
### 通用&高级优化能力
| 特性/模块              | 功能描述                                          |
|----------------------|---------------------------------------------------|
| 可变刷新率（VRR）    | 自适应屏幕刷新率，提升流畅性与省电                  |
| 高帧率模式支持        | 90Hz/120Hz/144Hz等高刷新率模式支持                  |
| Game SDK/Performance Tuner | 自动分析帧率、热/电/卡顿报告，开发者调优利器   |
| 录屏/直播/免打扰      | 游戏助手功能：录屏、直播、消息屏蔽、资源清理等         |
### 典型使用场景
| 场景                  | 优化内容                                                   |
|----------------------|------------------------------------------------------------|
| 启动/前台            | 自动最高性能档位、CPU/GPU全开、温控提升                    |
| 游戏过程             | 动态调频、监测帧率/发热自动降频                            |
| 网络/输入优化        | 网络加速、降低输入/显示延迟                                |
| 游戏辅助             | 录屏直播、消息管理、资源释放、适配最新GPU Driver/Shader    |

### 游戏相关的Android 模块
1. 系统服务与底层库
这些是游戏运行的基础，为应用提供核心功能。

SurfaceFlinger: Android 的图形合成器服务。它负责将所有游戏和应用的图形缓冲区（Surface）进行合成，最终显示在屏幕上。游戏通过向 SurfaceFlinger 提交自己的 Surface 来实现画面渲染。

ANativeWindow: 这是一个 Native C++ 接口，允许游戏直接操作窗口，并管理其图形缓冲区。这对于需要高帧率和低延迟的 3D 游戏至关重要。

libEGL 与 libGLES: 这些是 OpenGL ES 的实现库。OpenGL ES 是移动设备上用于 2D 和 3D 渲染的图形 API。游戏引擎（如 Unity、Unreal）通过这些库与 GPU 驱动进行通信，将图形指令发送给 GPU 执行。

libvulkan: 这是 Vulkan 图形 API 的实现库，相比 OpenGL ES 提供了更底层的 GPU 访问权限。它能让游戏开发者更精细地控制渲染管线，从而在性能和功耗方面获得更好的表现。

2. 输入与传感器
这些模块处理玩家的输入，是游戏交互的关键。

InputManagerService: 这是一个系统服务，负责从底层驱动接收所有输入事件，如触摸屏、按键、游戏手柄等。它将这些事件分发给正在运行的游戏。

libinput: Native 输入库，提供了访问输入设备事件的底层接口。

传感器框架: 包括 SensorManager 和相关的 HAL（硬件抽象层）。游戏利用这个框架获取来自加速度计、陀螺仪等传感器的数据，实现运动控制等功能。

3. 音频与多媒体
这些模块处理游戏的声音效果和背景音乐。

AudioFlinger: Android 的音频混合服务。它负责将多个应用的音频流（包括游戏音效和背景音乐）进行混合，然后发送给音频硬件进行播放。

OpenSL ES 与 AAudio: 这些是 Android 上用于音频播放的 Native API。游戏引擎通常会使用这些 API 来实现低延迟的音频播放，以确保音效与游戏画面同步。

MediaCodec: 这是用于音视频编解码的底层 API。虽然主要用于视频播放和录制，但一些游戏也会用它来处理游戏内的视频内容或流媒体。

4. 性能与功耗
这些模块帮助游戏优化性能，平衡性能与电池续航。

Thermal Service: 这是一个系统服务，用于监控设备的温度。当设备过热时，它会向应用发出警告，游戏可以据此降低性能或帧率，以防止设备因过热而自动降频。

Game Mode: Android 12 引入的 API，让游戏可以告诉系统自己正在运行，以便系统能够为游戏提供最佳的性能模式，例如优先分配 CPU 和 GPU 资源，或启用勿扰模式。

5. 应用框架层
这些是开发者直接使用的 Java/Kotlin API，简化了游戏开发。

GLSurfaceView 和 SurfaceView: 这是 Android 提供的 UI 组件，用于在应用中渲染 OpenGL ES 内容。游戏开发者通常会使用 SurfaceView 来实现高性能的渲染循环。

GameActivity: AndroidX 库中的一个组件，专为游戏设计。它简化了游戏手柄输入、传感器事件以及 ANativeWindow 的管理，让开发者可以更轻松地构建 Native 游戏。

WindowManager: 管理应用的窗口，允许游戏控制窗口的全屏模式、沉浸式模式等，以提供更好的游戏体验。