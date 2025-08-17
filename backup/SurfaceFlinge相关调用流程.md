端到端
graph TD
  subgraph App_Producer["App / Producer"]
    A1["App / MediaCodec: 写入 GraphicBuffer"]
    A2["IGraphicBufferProducer.queueBuffer()\nframeworks/native/libs/gui/IGraphicBufferProducer.cpp"]
    A1 --> A2
  end

  subgraph SurfaceFlinger
    S0["Frame dispatch / vsync 驱动\nframeworks/native/services/surfaceflinger/Scheduler/"]
    S1["Layer/Buffer latch 流程\nframeworks/native/services/surfaceflinger/SurfaceFlinger.cpp"]
    S2["OutputLayer::writeStateToHWC()\n.../CompositionEngine/src/OutputLayer.cpp"]
    S0 --> S1 --> S2
  end

  subgraph HWComposer["HWComposer / Composer HAL"]
    H1["Composer::validateDisplay()\n.../DisplayHardware/ComposerHal.h"]
    H2["getChangedCompositionTypes()/acceptDisplayChanges()\n.../DisplayHardware/ComposerHal.h"]
    H3["setClientTarget() (GPU 合成时)\n.../DisplayHardware/ComposerHal.h"]
    H4["presentDisplay()/presentOrValidateDisplay()\n.../DisplayHardware/ComposerHal.h"]
    H5["getReleaseFences()/getPresentFence()\n.../DisplayHardware/HWComposer.h"]
    H1 --> H2 --> H3 --> H4 --> H5
  end

  subgraph Display["显示硬件"]
    D1["硬件合成 & 扫描输出\n(hardware/interfaces/graphics/composer/aidl)"]
  end

  A2 --> S0
  S2 --> H1
  H5 --> D1

逐帧提交
flowchart TD
  F0["Vsync/调度触发\nframeworks/native/services/surfaceflinger/Scheduler/"]
  F1["遍历可见 Layer\nSurfaceFlinger::doComposition()\n.../SurfaceFlinger.cpp"]
  F2["OutputLayer::writeStateToHWC()\n设置 per-layer 属性\n.../CompositionEngine/src/OutputLayer.cpp\n调用 ComposerHal 接口"]
  F3["Composer::validateDisplay()\n.../DisplayHardware/ComposerHal.h"]
  F4["getChangedCompositionTypes()/acceptDisplayChanges()\n.../DisplayHardware/ComposerHal.h"]
  F5{"是否存在 CLIENT 合成?"}
  F6["RenderEngine::drawLayers()\n.../services/surfaceflinger/RenderEngine/"]
  F7["Composer::setClientTarget()\n.../DisplayHardware/ComposerHal.h"]
  F8["presentDisplay()/presentOrValidateDisplay()\n.../DisplayHardware/ComposerHal.h"]
  F9["getReleaseFences()/getPresentFence()\n.../DisplayHardware/HWComposer.h"]
  F10["最终合成提交到面板\nhardware/interfaces/graphics/composer/aidl/"]

  F0 --> F1 --> F2 --> F3 --> F4 --> F5
  F5 -- 是 --> F6 --> F7 --> F8
  F5 -- 否 --> F8
  F8 --> F9 --> F10

VIdeo Surface创建流程
**Traditional Mode**
sequenceDiagram
    autonumber
    participant App as App 线程
    participant VRI as ViewRootImpl（App）
    participant WMS as WindowManagerService（system_server）
    participant SV as SurfaceView（App）
    participant SC as SurfaceControl + BLAST（App/SF）
    participant SF as SurfaceFlinger（native）
    participant BQ as BufferQueue IGBP/IGBC（native）

    App->>VRI: setContentView / addView
    VRI->>VRI: setView → requestLayout → performTraversals
    VRI->>WMS: relayoutWindow 首次
    Note right of VRI: ViewRootImpl.relayoutWindow<br/>frameworks/base/core/java/android/view/ViewRootImpl.java
    Note right of WMS: WindowManagerService.relayoutWindow<br/>frameworks/base/services/core/java/com/android/server/wm/WindowManagerService.java

    VRI-->>SV: 调用 SurfaceView.updateSurface
    SV->>SC: SurfaceControl.Builder.build（创建子层 + 设置属性）
    Note right of SV: SurfaceView.updateSurface / getSurfaceControl<br/>frameworks/base/core/java/android/view/SurfaceView.java
    Note right of SC: SurfaceControl.Builder.build<br/>frameworks/base/core/java/android/view/SurfaceControl.java

    SC->>SC: nativeCreate
    Note right of SC: android_view_SurfaceControl.cpp nativeCreate<br/>frameworks/base/core/jni/android_view_SurfaceControl.cpp

    SC->>SF: SurfaceComposerClient.createSurface（Binder）
    Note right of SF: SurfaceComposerClient.cpp createSurface<br/>frameworks/native/libs/gui/SurfaceComposerClient.cpp

    SF->>SF: SurfaceFlinger.createLayer（建立 Layer 实体）
    Note right of SF: SurfaceFlinger.createLayer<br/>frameworks/native/services/surfaceflinger/SurfaceFlinger.cpp

    SF->>BQ: BufferQueue.createBufferQueue（配对 IGBP/IGBC）
    Note right of BQ: BufferQueue.cpp createBufferQueue<br/>frameworks/native/libs/gui/BufferQueue.cpp

    SC->>SC: SurfaceControl.Transaction.apply
    SC->>SF: setTransactionState 提交事务（Z 顺序 透明度 裁剪等）
    Note right of SC: SurfaceControl.Transaction.apply<br/>frameworks/base/core/java/android/view/SurfaceControl.java
    Note right of SF: SurfaceFlinger.setTransactionState<br/>frameworks/native/services/surfaceflinger/SurfaceFlinger.cpp

    SF-->>SC: 返回 SurfaceControl 引用（子层就绪）
    SV-->>App: SurfaceHolder.Callback.surfaceCreated / changed（拿到 Surface）
    Note right of SV: SurfaceHolder 回调发出可用 Surface<br/>frameworks/base/core/java/android/view/SurfaceHolder.java

    App-->>App: 至此 Video Surface 已创建（可供后续绑定解码器）

**TV Tunnel Mode**
sequenceDiagram
    autonumber
    participant App as App 线程
    participant VRI as ViewRootImpl（App）
    participant WMS as WindowManagerService（system_server）
    participant SV as SurfaceView（App）
    participant SC as SurfaceControl + Transaction
    participant SF as SurfaceFlinger（native）

    App->>VRI: setContentView / addView
    VRI->>VRI: setView → requestLayout → performTraversals
    VRI->>WMS: relayoutWindow 首次
    Note right of VRI: ViewRootImpl.relayoutWindow<br/>frameworks/base/core/java/android/view/ViewRootImpl.java
    Note right of WMS: WindowManagerService.relayoutWindow<br/>frameworks/base/services/core/java/com/android/server/wm/WindowManagerService.java

    VRI-->>SV: 调用 SurfaceView.updateSurface
    SV->>SC: SurfaceControl.Builder.build（创建可见子层）
    Note right of SV: SurfaceView.updateSurface / getSurfaceControl<br/>frameworks/base/core/java/android/view/SurfaceView.java
    Note right of SC: SurfaceControl.Builder.build<br/>frameworks/base/core/java/android/view/SurfaceControl.java

    SC->>SC: nativeCreate（JNI）
    Note right of SC: android_view_SurfaceControl.cpp nativeCreate<br/>frameworks/base/core/jni/android_view_SurfaceControl.cpp

    SC->>SF: SurfaceComposerClient.createSurface（Binder）
    Note right of SF: SurfaceComposerClient.cpp createSurface<br/>frameworks/native/libs/gui/SurfaceComposerClient.cpp

    SF->>SF: SurfaceFlinger.createLayer（创建 Layer 支持 sideband）
    Note right of SF: SurfaceFlinger.createLayer<br/>frameworks/native/services/surfaceflinger/SurfaceFlinger.cpp

    SC->>SC: SurfaceControl.Transaction.apply
    SC->>SF: setTransactionState 提交事务（Z 顺序 透明度 裁剪等）
    Note right of SC: SurfaceControl.Transaction.apply<br/>frameworks/base/core/java/android/view/SurfaceControl.java
    Note right of SF: SurfaceFlinger.setTransactionState<br/>frameworks/native/services/surfaceflinger/SurfaceFlinger.cpp

    SF-->>SC: 返回 SurfaceControl 引用（子层就绪）
    SV-->>App: SurfaceHolder.Callback.surfaceCreated / changed（拿到 Surface）
    Note right of SV: SurfaceHolder 回调发出可用 Surface<br/>frameworks/base/core/java/android/view/SurfaceHolder.java

    App-->>App: 至此 Tunnel Mode 的 Video Surface 已创建

**Traditional的视频+UI渲染流程**
sequenceDiagram
    autonumber
    participant HWUI as HWUI RenderThread（App）
    participant MC as MediaCodec（App 或 mediaserver）
    participant BQ as BufferQueue IGBP IGBC（native）
    participant SF as SurfaceFlinger（native）
    participant RE as RenderEngine（SF 内）
    participant HWC as Composer HAL（AIDL）
    participant DISP as 显示控制器 Panel

    %% ---------- UI 路径：绘制并投递 ----------
    HWUI->>BQ: Surface::queueBuffer
    Note right of BQ: 将当前 UI 帧 GraphicBuffer 提交到队列<br/>输入 buffer acquireFence dataspace damage timestamp<br/>输出 releaseFence 供上层复用<br/>frameworks/native/libs/gui/Surface.cpp

    %% ---------- Video 路径：解码并投递 ----------
    MC->>BQ: MediaCodec releaseOutputBuffer render → queueBuffer
    Note right of MC: 标记渲染并将解码输出投递到目标 Surface 的 IGBP<br/>底层走 ANativeWindow queueBuffer<br/>frameworks/av/media/libstagefright/MediaCodec.cpp
    Note right of BQ: BufferQueueProducer::queueBuffer 将帧入队并唤醒消费者<br/>维护帧序号 time 等元数据<br/>frameworks/native/libs/gui/BufferQueueProducer.cpp

    %% ---------- SF 消费与闩取 ----------
    BQ-->>SF: onFrameAvailable
    Note right of SF: Consumer 侧通知有新帧可取 触发合成调度

    SF->>SF: SurfaceFlinger handleMessageRefresh
    Note right of SF: Vsync 周期内聚合待更新 Layer 并驱动一次合成<br/>frameworks/native/services/surfaceflinger/SurfaceFlinger.cpp

    SF->>SF: Layer::latchBuffer
    Note right of SF: 等待并消费 acquireFence 选取本帧 buffer<br/>解析 transform crop dataspace surfaceDamage 等<br/>决定是否丢弃过期帧 并生成合成脏区<br/>frameworks/native/services/surfaceflinger/Layer.cpp

    %% ---------- 写入 HWC 层属性 ----------
    SF->>HWC: setLayer* 系列
    Note right of HWC: 写每层静态与每帧属性 到 HWC<br/>setLayerZOrder 叠放次序<br/>setLayerDisplayFrame 目标显示矩形 dst 空间 影响缩放与位置<br/>setLayerSourceCrop 源裁剪 src 空间 指定采样区域<br/>setLayerBlendMode 无 预乘 覆盖 等<br/>setLayerPlaneAlpha 层透明度 0..1<br/>setLayerDataspace 色域与传输曲线 SRGB DisplayP3 BT2020PQ 等<br/>setLayerSurfaceDamage 局部更新 region 便于增量合成<br/>setLayerBuffer 绑定本层 buffer 与其 acquireFence<br/>frameworks/native/services/surfaceflinger/DisplayHardware/ComposerHal.h

    %% ---------- HWC 决策：Device vs Client ----------
    SF->>HWC: validateDisplay
    Note right of HWC: 预校验资源与能力 评估每层是否可硬件合成<br/>返回需要改为 CLIENT 的层集合

    HWC-->>SF: getChangedCompositionTypes → acceptDisplayChanges
    Note right of HWC: 读取被要求切换到 CLIENT 的层类型变化<br/>acceptDisplayChanges 确认变更 进入最终路径

    alt 需要 Client 合成（混合模式）
      SF->>RE: RenderEngine::drawLayers
      Note right of RE: 在 GPU 侧按 Z 序 混合 过滤 颜色管理合成一张输出图<br/>输入为 CLIENT 层 以及其属性 输出为 ClientTarget buffer 与 fence<br/>frameworks/native/libs/renderengine/*
      SF->>HWC: setClientTarget buffer fence dataspace damage
      Note right of HWC: 将客户端合成结果作为一层提交给 HWC<br/>HWC 将其与其余 Device 层一起合成<br/>ComposerHal.h
    else 全部 Device 合成
      SF-->>HWC: 不需要 RenderEngine 与 setClientTarget<br/>全部层由 HWC 直接合成
    end

    %% ---------- 提交与呈现 ----------
    SF->>HWC: presentOrValidateDisplay 或 presentDisplay
    Note right of HWC: 最终提交 布局平面 进行缩放 CSC 设定时序<br/>presentOrValidate 可在一次调用内完成校验与提交

    HWC-->>SF: getReleaseFences per-layer / getPresentFence
    Note right of HWC: 每层 releaseFence 告知何时可复用该 buffer<br/>presentFence 表示整帧进入显示管线的时序点<br/>frameworks/native/services/surfaceflinger/DisplayHardware/HWComposer.h

    HWC->>DISP: 配置硬件平面 缩放 CSC 光标等
    DISP-->>DISP: 扫描出屏 完成显示

**TV Tunnel Mode的视频+UI渲染流程**
sequenceDiagram
    autonumber
    participant HWUI as HWUI RenderThread（App）
    participant MC as MediaCodec 隧道模式（App 或 mediaserver）
    participant SF as SurfaceFlinger（native）
    participant RE as RenderEngine（SF 内）
    participant HWC as Composer HAL（AIDL）
    participant DISP as 显示控制器 Panel

    %% ---------- UI 路径（仍走 BQ，到此图只展示从 SF 开始） ----------
    HWUI->>SF: Surface::queueBuffer → BufferQueue → SF 取帧
    SF->>SF: Layer::latchBuffer（UI 层）
    Note right of SF: Layer.cpp latchBuffer<br/>消费 UI acquireFence, 解析 transform/crop/dataspace, 计算 damage

    %% ---------- 视频路径：隧道直通 ----------
    MC->>MC: ACodec configureTunneledVideoPlayback
    Note right of MC: ACodec.cpp 配置隧道和音频会话，解码 HAL 返回 sideband 句柄
    MC->>SF: SurfaceControl.Transaction.setSidebandStream（视频层）
    Note right of SF: 将 sideband 流挂到视频 Layer 的 Surface

    %% ---------- 写入 HWC 层属性 ----------
    SF->>HWC: setLayer*（UI 层属性：Z、DisplayFrame、SourceCrop、Blend、Alpha、Dataspace 等）
    SF->>HWC: setLayerSidebandStream（视频层）
    Note right of HWC: ComposerHal.h<br/>UI 层常规 setLayer*；视频层用 setLayerSidebandStream 不绑定 layer buffer

    %% ---------- HWC 决策：UI 层可能走 CLIENT ----------
    SF->>HWC: validateDisplay
    HWC-->>SF: getChangedCompositionTypes → acceptDisplayChanges
    Note right of HWC: 若 UI/字幕等层含 HWC 不支持特性或资源不足 → 标为 CLIENT；视频 sideband 保持 Device

    alt UI 存在 CLIENT 层（混合模式）
      SF->>RE: RenderEngine::drawLayers（只合成 CLIENT 层）
      Note right of RE: libs/renderengine/*<br/>按 Z 序 混合 颜色管理 GPU 合成出 ClientTarget
      SF->>HWC: setClientTarget（buffer, fence, dataspace, damage）
      Note right of HWC: ClientTarget 与视频 sideband + 其余 Device 层一起由 HWC 合成
    else UI 全部 Device 合成
      SF-->>HWC: 无需 RenderEngine 与 setClientTarget<br/>HWC 直接合成 sideband + UI 设备层
    end

    %% ---------- 提交与呈现 ----------
    SF->>HWC: presentOrValidateDisplay 或 presentDisplay
    HWC-->>SF: getReleaseFences（UI 层）/ getPresentFence（整帧）
    Note right of HWC: UI 有 per-layer releaseFence；视频 sideband 一般无 BQ fence 由硬件时序驱动

    HWC->>DISP: 叠加 sideband 视频 与 UI 平面（必要时缩放/CSC）
    DISP-->>DISP: 扫描出屏

**Traditional AV同步流程**
sequenceDiagram
    autonumber
    participant P as NuPlayerRenderer / MediaSync（播放器渲染器）
    participant AT as AudioTrack（音频主时钟）
    participant MC as MediaCodec Video（解码）
    participant BQ as BufferQueue（IGBP/IGBC）
    participant SF as SurfaceFlinger
    participant RE as RenderEngine
    participant HWC as Composer HAL
    participant DISP as 显示控制器 Panel

    %% 音频主时钟 & 持续对时循环
    P->>AT: AudioTrack::start / write
    Note right of AT: frameworks/av/media/libaudioclient/AudioTrack.cpp<br/>启动并持续写入音频数据

    loop 持续对时（主循环）
      AT-->>P: AudioTrack::getTimestamp / getPosition
      Note right of AT: 获取播放头位置或系统时钟对齐的时间戳<br/>用于推导媒体当前时间 t_now

      P->>P: MediaClock::updateAnchor / setPlaybackRate / getMediaTimeUs
      Note right of P: frameworks/av/media/libstagefright/MediaClock.cpp<br/>updateAnchor 用音频锚点校正时间轴<br/>setPlaybackRate 处理倍速/不变调<br/>getMediaTimeUs 生成当前媒体时间 t_now

      P->>P: NuPlayerRenderer::onDrainVideo_l（调度视频）
      Note right of P: frameworks/av/media/libmediaplayerservice/nuplayer/NuPlayerRenderer.cpp<br/>比较帧 PTS 与 t_now：早等→等待；晚到→丢帧；准点→发渲染
    end

    %% 视频按 PTS 投递
    P-->>MC: MediaCodec::releaseOutputBuffer(render=true, ts)
    Note right of MC: frameworks/av/media/libstagefright/MediaCodec.cpp<br/>要求在 ts 附近呈现该帧（底层走 ANativeWindow::queueBuffer）

    MC->>BQ: Surface::queueBuffer
    Note right of BQ: frameworks/native/libs/gui/Surface.cpp / BufferQueueProducer.cpp<br/>提交解码帧（携带 timestamp/dataspace/surfaceDamage/HDR）

    %% SF 消费 & 对齐 vsync
    BQ-->>SF: onFrameAvailable
    SF->>SF: SurfaceFlinger::handleMessageRefresh（vsync 驱动）
    SF->>SF: Layer::latchBuffer
    Note right of SF: frameworks/native/services/surfaceflinger/Layer.cpp<br/>等待 acquire fence；选最接近显示窗口的帧；必要时丢弃迟到帧

    %% 写入 HWC 层属性
    SF->>HWC: setLayerZOrder / setLayerDisplayFrame / setLayerSourceCrop
    SF->>HWC: setLayerBlendMode / setLayerPlaneAlpha / setLayerDataspace
    SF->>HWC: setLayerPerFrameMetadata(Blobs) / setLayerSurfaceDamage / setLayerBuffer
    Note right of HWC: frameworks/native/services/surfaceflinger/DisplayHardware/ComposerHal.h<br/>把几何/混合/色彩/HDR 与 buffer+fence 写入 HWC

    %% 决策：Device vs Client
    SF->>HWC: validateDisplay
    HWC-->>SF: getChangedCompositionTypes → acceptDisplayChanges
    alt 存在 CLIENT 层
      SF->>RE: RenderEngine::drawLayers
      Note right of RE: frameworks/native/libs/renderengine/*<br/>GPU 合成 CLIENT 层 → 产出 ClientTarget
      SF->>HWC: setClientTarget(buffer, fence, dataspace, damage)
    else 全部 Device 合成
      SF-->>HWC: 无需 RenderEngine / setClientTarget
    end

    %% 提交 & 栅栏
    SF->>HWC: presentOrValidateDisplay / presentDisplay
    HWC-->>SF: getReleaseFences（per-layer） / getPresentFence（per-frame）
    HWC->>DISP: 平面分配 / 缩放 / CSC
    DISP-->>DISP: 按 vsync 扫描出屏（贴近 PTS）

**TV Tunnel Mode AV同步流程**
sequenceDiagram
    autonumber
    participant P as NuPlayerRenderer / MediaSync（播放器渲染器）
    participant AT as AudioTrack（音频主时钟）
    participant MC as MediaCodec 隧道
    participant SF as SurfaceFlinger
    participant RE as RenderEngine
    participant HWC as Composer HAL
    participant DISP as 显示控制器 Panel

    %% 音频主时钟 & 持续对时循环（硬件对时为主）
    P->>AT: AudioTrack::start / write
    loop 持续对时（用于快进/校正）
      AT-->>P: AudioTrack::getTimestamp / getPosition
      P->>P: MediaClock::updateAnchor / setPlaybackRate / getMediaTimeUs
      Note right of P: 仍维护全局媒体时间；但视频对时由硬件完成
    end

    %% 隧道配置 & sideband
    P->>MC: ACodec::configureTunneledVideoPlayback(audioSessionId)
    Note right of MC: frameworks/av/media/libstagefright/ACodec.cpp<br/>请求解码 HAL 返回 sideband 句柄 与音频会话对齐
    MC-->>SF: SurfaceControl::Transaction::setSidebandStream（视频层）
    Note right of SF: frameworks/native/libs/gui/SurfaceComposerClient.cpp 等<br/>将 sideband 挂到视频 Layer；视频不走 BQ

    %% UI 层（如有）仍走常规路径（此处从 SF 往下）
    SF->>HWC: setLayer*（UI 层）
    SF->>HWC: setLayerSidebandStream（视频层）
    Note right of HWC: ComposerHal.h<br/>视频层使用 sideband 源；HDR 元数据由硬件管线处理

    %% 决策：UI 可能 CLIENT，视频固定 Device
    SF->>HWC: validateDisplay
    HWC-->>SF: getChangedCompositionTypes → acceptDisplayChanges
    alt UI 存在 CLIENT 层
      SF->>RE: RenderEngine::drawLayers（仅 UI/字幕等 CLIENT 层）
      SF->>HWC: setClientTarget（与 sideband 叠加）
    else UI 全部 Device 合成
      SF-->>HWC: 直接合成 sideband + UI 设备层
    end

    %% 提交 & 硬件对时
    SF->>HWC: present*
    HWC-->>SF: getReleaseFences（UI 层） / getPresentFence
    HWC->>DISP: 叠加 sideband 视频 + UI；缩放 / CSC
    Note right of DISP: 以音频会话为基准在硬件中对时视频输出（更稳）

Vsync流程以及HWC相关流程（借鉴drm_composer）
sequenceDiagram
    autonumber
    participant HWC as HWComposer(Composer3↔drm_hwcomposer)
    participant VS as Vsync回调(ComposerCallback)
    participant SCH as SF Scheduler/EventThread
    participant SF as SurfaceFlinger
    participant UQ as UI BufferQueue
    participant VQ as Video BufferQueue
    participant CE as CompositionEngine(Output/Display)
    participant RE as RenderEngine(Client)
    participant GL as GLESRE
    participant SK as SkiaGLRE
    participant VK as VulkanRE
    participant KMS as DRM/KMS(Display Ctrl)

    %% 0) Vsync 获取与派发（一次性初始化 + 周期回调）
    SF->>HWC: registerCallback(ComposerCallback)
    SF->>HWC: setVsyncEnabled(display, ENABLE)
    HWC-->>VS: onVsync(display, timestamp)（硬件vsync/flip）
    VS-->>SCH: VSyncSchedule/EventThread::onVsync(timestamp)
    SCH-->>SF: MessageQueue::invalidate() → handleMessageRefresh()

    %% 1) 本帧开始：取UI/视频帧
    UQ-->>SF: onFrameAvailable(UI)
    VQ-->>SF: onFrameAvailable(Video)
    SF->>SF: Layer::latchBuffer(UI)
    SF->>SF: Layer::latchBuffer(Video)

    %% 2) 选择合成策略（与HWC协同）
    SF->>CE: Output::prepareFrame()
    CE->>HWC: validateDisplay()
    HWC-->>CE: getChangedCompositionTypes()（标记CLIENT层）
    HWC-->>CE: getDisplayRequests()（显示级请求/色彩）
    CE->>CE: applyChangedTypesToLayers()

    %% 3) 若存在CLIENT层 → ClientTarget
    alt 存在 CLIENT 层
      CE->>RE: buildLayerSettings[](CLIENT层集合)
      RE->>GL: drawLayers（GLES） ; GL->>GL: bindFrameBuffer→setupLayerBlending→setupLayerTexturing→drawMesh
      RE->>SK: drawLayers（SkiaGL） ; SK->>SK: 色彩/滤镜/裁剪/阴影
      RE->>VK: drawLayers（Vulkan） ; VK->>VK: 合成/色彩(EOTF)转换
      RE-->>CE: 产出 ClientTarget(buffer,fence,dataspace,damage)
      CE->>HWC: setClientTarget(...)
    else 全部 Device 合成
      CE-->>HWC: 无 ClientTarget
    end

    %% 4) 为各层写属性/缓冲并发起提交
    CE->>HWC: setLayerZ/DisplayFrame/SourceCrop/Blend/Alpha/Dataspace/SurfaceDamage
    CE->>HWC: setLayerBuffer（仅 DEVICE 层）
    CE->>HWC: presentOrValidateDisplay()/presentDisplay()

    %% 5) HWC(drm_hwcomposer) 内部：原子检查→回退→原子提交
    HWC->>HWC: HwcDisplay::ValidateDisplay()（入口）
    HWC->>HWC: Backend::ValidateDisplay()（策略后端）
    HWC->>HWC: DrmDisplayComposition::Plan()（生成计划）
    HWC->>HWC: Planner::ProvisionPlanes()（映射层→DRM planes）
    HWC->>HWC: DrmDisplayCompositor::CommitFrame(test_only)（atomic_check）
    alt test_only 失败
      HWC->>HWC: 回退：更多层→CLIENT / squash
      CE->>HWC: 若需则携带 ClientTarget 重试
    else test_only 通过
      HWC->>HWC: DrmAtomicStateManager::AtomicCommit(real)（atomic_commit）
    end

    %% 6) 栅栏与扫描出屏
    HWC-->>CE: getReleaseFences（per-layer） / getPresentFence（per-frame）
    HWC->>KMS: 编程 CRTC/Plane/Scaler/CSC（atomic）
    KMS-->>KMS: Scanout → 面板/HDMI
