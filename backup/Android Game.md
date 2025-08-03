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
