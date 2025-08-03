定制 ROM
Corvus OS: 这是一个以游戏为核心的定制 ROM 项目。您可以从其官方 GitHub 组织页面开始探索：

GitHub 主页: https://github.com/Corvus-AOSP

DerpFest: 以其丰富的功能和稳定性而受到游戏玩家的欢迎。以下是其 GitHub 组织页面：

GitHub 主页: https://github.com/DerpFest-AOSP

Evolution X: 这是一个功能非常丰富的 ROM，其中许多功能都可用于游戏优化。

GitHub 主页: https://github.com/Evolution-X

Project Elixir: 一个专注于提供流畅、稳定体验的 ROM。

GitHub 主页: https://github.com/Project-Elixir

定制内核
Hydra Kernels: 一个旨在提供高性能和良好电池续航的内核项目。

GitHub 主页: https://github.com/Hydra-Kernels

Konabess Kernel: 这是一个流行的定制内核，尤其受高通骁龙 855/860 等 SoC 用户的欢迎。

GitHub 主页: https://github.com/Konabess-Kernel

Magisk 模块和工具
Android-Enhancer: 这是一个通过 Magisk 模块和脚本来优化系统性能的工具。

GitHub 主页: https://github.com/iamlooper/Android-Enhancer-Module

NZTSapp: 这是一款用于性能优化的工具，旨在提高游戏帧数。

GitHub 主页: https://github.com/nezhatweaks/nztsapp

请记住，这些 GitHub 组织通常包含多个与不同设备和 Android 版本相关的仓库。如果你想要为特定设备寻找 ROM 或内核，可能需要在这些组织页面内搜索你的设备型号。






专门针对性能优化的


在 Android 开源社区中，有一些项目专门针对性能优化而生，它们通常通过系统级的修改来提升设备整体的响应速度、流畅度和电池续航。这些项目并不局限于游戏，而是旨在改善所有应用的运行体验。

1. 定制内核
定制内核是提升性能最有效的方式之一，因为它直接控制着 CPU、GPU 和内存的调度策略。

Viper Kernels：这个项目不仅为游戏进行了优化，也包含了多种针对日常性能和续航的调度器。它允许用户在性能和功耗之间进行灵活切换，以适应不同场景的需求。

GitHub 主页：https://github.com/Viper-Kernels

Konabess Kernel：这个内核特别受欢迎，因为它在提供强大性能的同时，也兼顾了电池续航。它通常会集成先进的调度算法，确保系统在需要时能迅速响应，在空闲时则保持低功耗。

GitHub 主页：https://github.com/Konabess-Kernel

2. Magisk 模块和脚本
Magisk 模块可以在不修改系统分区的情况下，对系统进行各种性能优化。

LKT（Leancode Kernel Tweaks）：这是一个强大的 Magisk 模块，通过修改内核参数和调度器，来提升设备的性能和续航。它提供了多种预设模式，例如“性能模式”、“均衡模式”和“省电模式”，用户可以根据需要自由切换。

GitHub 主页：https://github.com/leancode/lkt

FDE.AI：这个项目是一个基于 AI 算法的优化工具，它会根据你的使用习惯，动态调整系统参数，包括 CPU 频率、内存管理和 I/O 调度，从而在不影响稳定性的前提下提升性能。

GitHub 主页：https://github.com/magisk-modules-repo/FDE.AI

3. 定制 ROM
虽然大多数定制 ROM 都致力于提供流畅的体验，但也有一些特别注重性能，会集成上述内核或脚本来提供更好的开箱即用体验。

Evolution X：这是一个功能非常丰富的 ROM，其内置的“性能模式”和多种定制选项，允许用户对 CPU、GPU 进行细致的调校，以获得最佳性能。

GitHub 主页：https://github.com/Evolution-X

Project Elixir：这个 ROM 同样致力于提供流畅的用户体验。它通过精简系统服务和优化系统架构，确保系统资源的有效利用，从而提升应用的启动速度和运行流畅度。

GitHub 主页：https://github.com/Project-Elixir







除了这几个还有么


确实，Android 性能优化的开源项目远不止那几个。在社区中，还有许多更具体、更小众但同样有效的项目。它们通常以 Magisk 模块或通用脚本的形式存在，旨在通过微调系统参数来榨取设备的每一分性能。

1. 通用性能优化脚本和模块
这些项目通常不绑定特定设备，而是通过修改通用的系统配置文件来提升性能。

FDE.AI：这是一个广受欢迎的 Magisk 模块，它使用机器学习算法动态调整系统参数，以平衡性能、功耗和内存使用。它会根据你的使用习惯，自动优化 CPU 调度、I/O 调度和内存管理。

GitHub 主页：https://github.com/magisk-modules-repo/FDE.AI

LKT (Leancode Kernel Tweaks)：这是一个用于优化内核的 Magisk 模块，提供了多种预设模式，比如“性能模式”、“均衡模式”和“省电模式”。它通过修改内核参数，让用户可以根据自己的需求自由切换。

GitHub 主页：https://github.com/leancode/lkt

2. 内存和 I/O 优化
这些模块专注于解决 Android 设备常见的内存和存储性能瓶颈。

Leancode Dalvik Hyperthreading：这是一个有趣的 Magisk 模块，旨在通过激活 lmkd (lowmemorykiller daemon) 中的 psi 模式来改善 RAM 管理。它声称比传统的 minfree_levels 模式更高效、更稳定。

GitHub 主页：https://github.com/LeanxModulostk/DalvikHyperthreading

Performance Tweak：这个模块专注于对 I/O 子系统进行优化，通过修改 I/O 调度器和缓存策略，来提升文件读写速度，从而加快应用启动和加载时间。

GitHub 主页：https://github.com/R-v-d/Performance-Tweak

3. 特定硬件优化
这些项目通常针对特定的硬件平台（例如高通或联发科芯片）进行深度优化。

PerfMTK：这是一个专门为联发科 (MediaTek) 芯片设计的 Magisk 模块。它提供了多种配置文件和高级设置，可以对 CPU、GPU 的频率、调度器和热管理进行精细控制，从而在游戏和日常使用中获得更好的性能。

GitHub 主页：https://github.com/JUANIMAN/PerfMTK

Magnetar：这是一个通用的内核微调器，旨在通过机器学习来自动适应用户的不同使用场景，从而在性能和功耗之间找到最佳平衡。

GitHub 主页：https://github.com/Kyliekyler/MAGNETAR