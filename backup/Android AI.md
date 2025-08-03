### AOSP 原生内置的 AI/ML 框架
| 体系/模块      | 是否AOSP原生 | 框架主要用途             | 框架归属/分布                      | 端侧用法                  | 云端用法                  | 典型依赖          |
|----------------|:------------:|--------------------------|-------------------------------------|---------------------------|---------------------------|-------------------|
| NNAPI          | ✔️           | 端侧AI推理标准接口        | `frameworks/ml/nn/` (AOSP)         | TFLite/MediaPipe等可调用   | -                         | 硬件加速/HAL      |
| TensorFlow Lite| ❌           | 端测AI模型推理            | Google官方独立SDK（外部依赖）       | App/服务端推理、可对接NNAPI| -                         | TFLite SDK/so     |
| MediaPipe      | ❌           | AI管道/多模态推理         | Google官方独立工程（外部依赖）      | CV/音频等管道，可对接NNAPI | -                         | MediaPipe AAR/so  |
| ML Kit         | ❌           | 高层AI功能SDK（OCR/翻译等）| Google Play Services 或独立SDK      | App端AI能力封装           | -                         | Google Play Services |
| Google Assistant| ❌          | 语音助手/对话/多模态推理  | Google App/Play服务/云端服务        | 用TFLite/MediaPipe/NNAPI等| 云端TensorFlow/大模型等   | Google App/GMS    |

| 框架        | 是否 Framework 层原生模块 | 对 Framework 的集成方式             | 典型使用场景         |
| ----------- | :----------------------: | ----------------------------------- | -------------------- |
| TFLite      | 否                       | App 层/JNI/NNAPI delegate           | App 端 AI 推理       |
| MediaPipe   | 否                       | App 层/JNI                          | 实时多模态处理       |
| ML Kit      | 否                       | App 层/Google Play Service/SDK      | 高级AI封装、OCR等    |
| NNAPI       | 是                       | Framework 层唯一AI标准接口          | TFLite/厂商AI等底层  |

### 1. NNAPI（Neural Networks API）
是什么
Android 官方定义的端侧神经网络推理标准接口，为应用和 ML 框架（如 TFLite）提供统一的本地 AI 加速入口，适配硬件 NPU/GPU/DSP/CPU。

主要用于
各类深度学习模型（CV/NLP/音频等）的推理加速，面向图片识别、语音识别、翻译、AI拍照等端测场景。

支持模型
支持 CNN/RNN/Transformer 等标准神经网络，兼容 ONNX、TFLite、Caffe、PyTorch（经转换）。

接口是什么
Java: android.neuralnetworks.*（API 29+），
C/C++: NeuralNetworks.h NDK API，
TFLite/MediaPipe等可用 NNAPI Delegate。

在哪一层/Android源码路径
Framework层/Native，主路径：frameworks/ml/nn/
HAL: hardware/interfaces/neuralnetworks/

开发入口
[NNAPI 官方文档](https://developer.android.com/ndk/guides/neuralnetworks?hl=zh-cn)

典型用例

TFLite 调用 NNAPI delegate 实现离线图像识别/语音识别的本地推理加速

AI 相机的本地超分辨率处理

功能/性能/能力

只管模型推理，依赖硬件能力，性能受 SoC/NPU 支持影响，适合通用端测加速

### 2. TensorFlow Lite
是什么
Google 开发的轻量级端侧深度学习推理库，专为移动和嵌入式设备优化，是 TensorFlow 框架的精简版。

主要用于
在 Android/iOS/IoT/边缘设备上执行各类 AI/ML 任务。广泛用于图像分类、目标检测、人脸识别、语音识别、自然语言理解等。

支持模型
支持大部分 TensorFlow 模型，TFLite、ONNX，支持 CNN、RNN、Transformer、量化/剪枝模型。

接口是什么
Java: org.tensorflow.lite.Interpreter、org.tensorflow.lite.support.*
Native: libtensorflowlite.so、libtensorflowlite_jni.so
支持与 NNAPI/GPU Delegate 配合

在哪一层/Android源码路径
App 层、Native 层
非AOSP，独立工程：[GitHub TensorFlow](https://github.com/tensorflow/tensorflow/tree/master/tensorflow/lite)

开发入口
[TensorFlow Lite 官方](https://ai.google.dev/edge/litert?hl=zh-cn)

典型用例

App端离线识别：图像分类（如花草识别）、语音命令识别、翻译模型

Google Assistant/相机/键盘等内置AI能力

功能/性能/能力

端测模型种类广，推理速度快，硬件可用性高（支持NNAPI/GPU），适合大多数本地 AI 场景

### 3. MediaPipe
是什么
Google 开源的端侧跨平台多模态 AI 流式管道框架，擅长视觉、音频、手势、姿态等实时分析。

主要用于
实时 CV（如人脸/手势/姿态/对象追踪）、音频/多模态任务（如动作识别），常用于 AR、美颜、健身等。

支持模型
支持 TFLite/ONNX/Caffe2 模型，pipeline 可嵌入多AI节点，如检测、分割、追踪、关键点提取等。

接口是什么
Java: com.google.mediapipe.framework.*
Native: libmediapipe_jni.so、C++ Core
Pipeline 通过 Calculator Graph 配置，支持 TFLite 互操作

在哪一层/Android源码路径
App/Native层，独立工程：[MediaPipe GitHub](https://github.com/google/mediapipe)
Java: mediapipe/java/src/main/java/com/google/mediapipe/

开发入口
[MediaPipe 官方](https://ai.google.dev/edge/mediapipe/solutions/guide?hl=zh-cn)

典型用例

AR 相机：实时人脸、手势、身体姿态检测

健身、视频会议、滤镜、AI 美颜等

功能/性能/能力

支持高性能流式多 AI 节点，低延迟实时分析，擅长 CV 端侧 Pipeline，部分场景性能优于单独模型推理

### 4. ML Kit
是什么
Google Play 服务提供的移动端 AI 能力高层 SDK，封装 OCR、翻译、人脸检测等常见功能，对开发者“即插即用”，不要求 ML 经验。

主要用于
手机 App 快速集成 OCR、文本识别、条码识别、对象检测、语言翻译、智能回复等 AI 能力。

支持模型
内置 Google 训练好的 TFLite、CNN、Transformer 等，自动下发和优化，部分可自定义导入自有模型。

接口是什么
Java/Kotlin: com.google.mlkit.*（如 vision、nlp、barcode）
大部分能力基于 Google Play 服务/SDK调用

在哪一层/Android源码路径
App 层/Google Play 服务，不在AOSP。SDK：[ML Kit 官方](https://developers.google.com/ml-kit?hl=zh-cn)

开发入口
[ML Kit 官方](https://developers.google.com/ml-kit?hl=zh-cn)

典型用例

扫码识别、名片OCR、相册智能文字提取、实时翻译、图片标签、场景检测

功能/性能/能力

高度封装，免模型管理、即插即用，适合中轻度AI应用，推理速度适中但灵活性有限

### 5. Google Assistant
是什么
Google 生态中的全功能语音助手平台，集成自然语言理解、语音识别、对话管理、知识问答、视觉分析等多模态AI能力。属于Google App/服务，不是AOSP部分。

主要用于
智能语音助手（手机、TV、IoT、汽车等），语音唤醒、指令执行、对话、翻译、视觉识别（如“看图识物”）。

支持模型
云端大模型（BERT、LaMDA、Gemini等）、端侧唤醒词/命令识别用TFLite、视觉用MediaPipe

接口是什么
对外以Intent/Activity/VoiceInteractionService等Android标准接口提供能力，API多为Google自有

在哪一层/Android源码路径
主要为Google App（com.google.android.googlequicksearchbox）、Play服务和云端，非AOSP开源模块。

开发入口
[Google Assistant开发者中心](https://developers.google.com/assistant?hl=zh-cn)

典型用例

语音唤醒手机、智能家居控制、语音搜索、AI对话、实时翻译、拍照识物

功能/性能/能力

能力最丰富，支持自然语言理解/多轮对话/多模态推理，端云结合，依赖云端大模型，智能性和复杂度远超一般本地AI能力

### 五个对比区别
| 框架/服务         | 是否AOSP原生 | 定位                | 主要AI能力                     | 性能与灵活性              | 适用场景             | 端/云          |
|-------------------|:------------:|---------------------|-------------------------------|---------------------------|----------------------|----------------|
| NNAPI             | ✔️           | AI加速标准接口       | 任意推理模型（依赖实现）       | 性能好（硬件适配度决定）  | TFLite等模型推理     | 端侧           |
| TensorFlow Lite   | ❌           | 端侧AI推理框架       | 图像/语音/NLP推理，灵活性强    | 高性能（配NNAPI/GPU）     | 定制/通用AI应用      | 端侧           |
| MediaPipe         | ❌           | AI多模态管道框架     | CV/音频/手势/姿态实时分析      | 高性能流式分析、Pipeline  | AR/滤镜/健身         | 端侧           |
| ML Kit            | ❌           | 高层AI能力SDK        | OCR/翻译/检测等高层AI能力      | 易用性高，性能适中        | 快速集成AI场景       | 端侧+云        |
| Google Assistant  | ❌           | 语音助手AI平台       | 语音/NLU/对话/视觉/家居控制等  | 最强AI，端云协同          | 智能助手/多模态      | 端+云          |

### Android TV 厂商的亮点技术或功能
1. TFLite、NNAPI 方向
多通道 AI 画质增强与超分辨率

代表厂商：索尼（X1/XR 芯片）、TCL（AiPQ）、海信（ULED Pro）、小米

技术亮点：厂商将自研超分/降噪/锐化等 AI 模型用 TFLite 格式部署到电视端，通过 NNAPI 驱动 NPU/GPU 实时对每一帧视频图像进行智能处理，实现 4K/8K 超分辨率、动态场景识别、AI 明暗对比提升。

低延迟本地语音助手

代表厂商：小米（PatchWall AI）、海信、TCL、飞利浦

技术亮点：本地唤醒词、命令识别模型全部用 TFLite 部署，NNAPI 驱动 SoC 的 DSP/NPU，实现极低延迟的语音遥控、家居命令、内容搜索（离线可用、保护隐私）。

多模态场景检测/智能推荐

代表厂商：TCL、海信、索尼

技术亮点：实时分析观看内容和环境信息（亮度、噪音、屏幕距离等），用 TFLite 多模态模型动态优化音量、色温、HDR 等参数。

2. MediaPipe 方向
端侧姿态/人脸/手势检测驱动大屏交互

代表厂商：TCL、海信、小米、康佳

技术亮点：将 MediaPipe 部署在电视本地，实现摄像头下的远程手势控制（挥手换台、点赞暂停等）、家庭健身（动作识别/评分）、老人看护（摔倒检测）。

虚拟健身教练/体感游戏

代表厂商：TCL、海信、小米

技术亮点：MediaPipe Pipeline + TFLite 加速实时分析用户姿态，配合大屏健身/娱乐，响应速度极快，体验丰富。

3. ML Kit 方向
大屏内容 OCR 与自动整理

代表厂商：小米、TCL

技术亮点：用 ML Kit 的 OCR/文字识别，在电视端实现屏幕文字抓取、信息推送、名片识别、扫码登录等场景。

内容推荐与智能摘要

代表厂商：TCL、海信、小米

技术亮点：ML Kit 结合厂商自有大数据做智能内容标签、摘要、观影推荐（如 PatchWall 推荐引擎）。

4. 端侧AI的系统级协同
AI 驱动画面自适应与多屏互动

代表厂商：索尼（多屏互动）、TCL（多场景画质）、海信（智慧屏/家庭助手）

技术亮点：用 NNAPI + TFLite 实现电视和手机、音响、灯光等 IoT 设备联动，自动感知观影环境、调整模式、同步通知。

电视专用 AI 安全看护

代表厂商：海信、TCL

技术亮点：端侧部署 AI 行为/人脸检测，保障儿童/老人安全，自动识别危险动作、及时提醒。

5. 性能优化和厂商自研
AI 算法端侧加速/定制 Delegate

代表厂商：索尼、TCL、海信、小米

技术亮点：深度优化自家 SoC 的 NNAPI/HAL delegate，部分支持专有硬件指令集（如索尼的 X1 AI/超分，TCL 的 AiPQ），实现远高于通用安卓设备的推理速度与能效比。