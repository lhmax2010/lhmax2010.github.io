1. APP启动播放视频有Scaler资源管理限制么？
没有，HWComposer在没有资源的情况下会全部送去Client Composer去合成，只有HW Decoder限制，但是手机上，HW Decoder资源不够情况会走SW Decoder，测试Multiview情况下启动了4个PIP都能播放
2. HW Composer的选定走Device Layer的条件
硬约束 / 先决条件（满足越多越容易被选为 DEVICE）：

像素格式：视频（YUV）优先于 UI（RGB）。

变换能力：该 plane 是否支持你的缩放/裁剪/旋转组合（很多 plane 不支持 90/270° 或复杂裁剪/非均匀缩放）。

透明度：不透明层更容易 overlay；有 per-pixel alpha/圆角/阴影时常被打回 GPU。

色彩与 HDR：是否需要 GPU 侧 tone-map/色域转换；secure/HDR 有专用受保护 plane 限制。

资源预算：可用 plane 条数、读写带宽、像素时钟、刷新率、分辨率上限。

策略/heuristics（常见打分）：

收益最大优先：画面面积大、缩放代价高的那几层（通常是视频）。

必须硬件路径优先：secure/DRM、某些 HDR 链路。

避免“夹心结构”：HWC只有一张 ClientTarget 可用。如果 Z 顺序是 OverlayA –（很多 UI）– OverlayB，为满足全局 Z 通路，其中一条 overlay 常被降级到 GPU，把“中间那堆 UI + 降级的视频”先预合成成 ClientTarget，最后再与另一个 overlay 混合（单 ClientTarget 约束）。

工程侧如何“增加命中率”（APP 能做的）：

用 SurfaceView(YUV/opaque) 承载视频；必要时 holder.setFormat(PixelFormat.OPAQUE)。

避免视频本身做旋转/奇裁剪/非均匀缩放；让 UI尽量不透明、减少覆盖在视频上的半透明/圆角。

多路视频时，尽量让 UI 统一在最上一层，别把 UI 夹在两路视频之间。

secure/HDR 的那路基本会被优先分配 plane。

怎么确认结果：

adb shell dumpsys SurfaceFlinger：查看每个 layer 的 compositionType（DEVICE/CLIENT）、bounds、transform。

adb logcat 过滤 SurfaceFlinger/HWComposer（以及厂商 HWC 日志）。

Perfetto/Systrace 看每帧 validate/present、RenderEngine（GPU）耗时。

3. 多 APP 同播视频时的 Z-order（谁在谁上）
全局（从下到上，常见）：

后景/桌面/壁纸

普通应用 Task（最近/前台在上）

每个 app 内部：

默认：SurfaceView(视频) 在 app UI 之下（UI 盖住视频，适合播放控件/字幕在上）；

setZOrderMediaOverlay(true)：视频压到 app UI 之上（仍在 app 组内，不越权系统层）；

分屏/自由窗：各 Task 按各自容器的 Z 决定谁上谁下。

PIP（Pinned Task）：整体在普通 app 之上。

SystemUI/IME/导航栏/状态栏：更上。

多视频 + 多 UI 的 overlay 可行性：

单 Scaler（常见手机）：只有 1 路视频 plane → 另一条视频 + 所有 UI 走 GPU 预合成。

双 Scaler（TV/机顶盒/你说的 MTK）：两路视频都可 overlay，UI 汇成一个 ClientTarget，最终 OverlayA + OverlayB + ClientTarget 由 HWC 混合。

若出现 视频–UI–视频 的夹层结构，通常只能保留一条 overlay（单 ClientTarget 约束），另一条视频被打回 GPU。

4. 创建PIP的流程
  sequenceDiagram
  participant App as App(Activity)
  participant ATMS as ActivityTaskManagerService(ATMS)
  participant ATS as ActivityTaskSupervisor
  participant WMS as WindowManagerService(WMS)
  participant TOC as TaskOrganizerController
  participant Shell as WM Shell: PipTaskOrganizer
  participant SF as SurfaceFlinger
  participant HWC as HWComposer(HWC2)

  App->>ATMS: enterPictureInPictureMode(params)
  ATMS->>ATS: enterPictureInPictureMode(r, params) / 校验支持性
  ATS->>ATMS: 请求切换Task为 WINDOWING_MODE_PINNED（启动过渡）
  ATMS->>TOC: 发起 Shell 过渡（TRANSIT_ENTER_PIP）
  TOC->>Shell: onTaskAppeared(task, leash) 交由 PipTaskOrganizer 管控
  Shell->>WMS: SurfaceControl.Transaction(setPosition/setMatrix/setCrop/圆角)
  WMS->>SF: 提交事务（Pinned Root Task 下的层级/变换生效）
  SF->>HWC: validate/present（挑选 DEVICE/CLIENT）
  HWC-->>SF: 两路/一路视频 Overlay；UI 走 ClientTarget（若需）
  SF-->>App: App 原 Surface 未重建，继续产帧（只是被缩放/裁剪）
