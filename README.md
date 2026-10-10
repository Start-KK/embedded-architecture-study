# 嵌入式软件架构学习

面向通用嵌入式系统，围绕 C/C++、RTOS、存储、通信、固件升级与平台移植，研究模块职责、接口设计、状态管理和可测试性。可用于传感器设备、工业控制器、通信终端、可穿戴设备及带屏产品。

本仓库收集 8 个开源项目的 **Git 子模块**，并提供中文阅读路线。源码已下载到本地；GitHub 中的源码目录会跳转到上游的固定提交。每次学习都可以使用同一个版本，之后也能显式更新。

## 从哪里开始

通用学习顺序：**模块接口 → 平台隔离 → 任务通信 → 状态机 → 固件升级 → 工程验证**。用模拟传感器和通信终端的小练习逐步验证设计。

| 阶段 | 学习问题 | 主要参考 | 阶段成果 |
| --- | --- | --- | --- |
| 1. 数据与接口 | 谁拥有状态，接口约定什么？ | littlefs | 设备状态与只读快照、可替换接口 |
| 2. 平台隔离 | 如何替换硬件与 OS 实现？ | littlefs、Zephyr 示例 | 模拟设备与板卡适配分离 |
| 3. 任务通信 | 谁运行、谁等待、如何共享数据？ | FreeRTOS、InfiniTime | 执行上下文、队列容量与所有权约定 |
| 4. 状态机 | 如何管理命令、超时与恢复？ | QP/C 示例 | 一条完整异步流程的状态图 |
| 5. 固件升级 | 如何校验、安装、启动与恢复？ | MCUboot | 安装流程、持久状态与故障恢复表 |
| 6. 工程验证 | 如何控制依赖与验证变化？ | Zephyr 示例、QP/C | 构建装配、主机验证与修改清单 |

固件升级是主线内容，使用 MCUboot 学习镜像处理、平台适配和恢复设计。网络专项可读 lwIP，图形界面专项可读 LVGL。每个项目先读一个边界或一条完整链路。

| 项目 | 源码目录 | 重点学习 | 可迁移的设计经验 |
| --- | --- | --- | --- |
| [littlefs](https://github.com/littlefs-project/littlefs) | [sources/littlefs](sources/littlefs) | C 函数指针接口、实例上下文、存储适配 | 串口与存储接口、替换硬件实现 |
| [QP/C 示例](https://github.com/QuantumLeaps/qpc-examples) | [sources/qpc-examples](sources/qpc-examples) | 状态机、事件处理、BSP 隔离、主机测试 | 设备连接、异步命令、故障恢复 |
| [FreeRTOS-Kernel](https://github.com/FreeRTOS/FreeRTOS-Kernel) | [sources/FreeRTOS-Kernel](sources/FreeRTOS-Kernel) | 内核与 CPU 移植层、任务/队列、内存策略 | 执行上下文、资源管理、平台适配 |
| [MCUboot](https://github.com/mcu-tools/mcuboot) | [sources/mcuboot](sources/mcuboot) | Bootloader 核心、Flash 适配、升级与恢复、模拟器 | 可验证的升级流程、持久状态设计 |
| [lwIP](https://github.com/lwip-tcpip/lwip) | [sources/lwip](sources/lwip) | 协议栈、网卡接口、OS 适配、数据缓冲区 | 通信边界、缓冲区所有权、线程模型 |
| [Zephyr 示例应用](https://github.com/zephyrproject-rtos/example-application) | [sources/zephyr-example-application](sources/zephyr-example-application) | 应用、板卡、驱动、构建配置分离 | 多平台、多型号工程组织 |
| [InfiniTime](https://github.com/InfiniTimeOrg/InfiniTime) | [sources/InfiniTime](sources/InfiniTime) | 完整产品的任务、控制器、页面协作 | 功能模块组织、依赖装配、任务通信 |
| [LVGL](https://github.com/lvgl/lvgl) | [sources/lvgl](sources/lvgl) | UI 组件、显示与输入适配、事件与生命周期 | UI 边界、主题与页面管理 |

- [分阶段学习路线与完成标准](docs/roadmap.md)
- [通用固件升级学习路线](docs/firmware-upgrade-study.md)
- [各项目源码入口与练习](docs/reading-guide.md)
- [学习笔记模板](docs/notes-template.md)
- [固定版本清单](sources.lock.json)

学习每个模块时，先回答：它负责什么？谁拥有可写状态？它依赖哪些接口？替换硬件后需要修改哪些文件？离开真实硬件能验证哪些行为？

## 下载源码

```sh
git clone --recurse-submodules --shallow-submodules https://github.com/Start-KK/embedded-architecture-study.git
cd embedded-architecture-study
```

已经普通克隆过本仓库时：

```sh
git submodule update --init --recursive --depth 1
```

通过 GitHub 的 Download ZIP 下载主仓库时，不会包含子模块源码。需要使用上面的 Git 命令，或进入各上游仓库分别下载。

## 固定版本与更新

主仓库保存每个项目的提交 SHA；`.gitmodules` 保存上游地址和跟踪分支。日常同步使用 `git submodule update --init --recursive`，恢复到主仓库指定的版本。

有意升级到上游分支最新版本时，可以选择一个项目：

```sh
git submodule update --init --remote sources/littlefs
git diff --submodule
```

检查变化后，更新 `sources.lock.json` 中该项目的 SHA，并提交新的子模块引用及版本清单。学习笔记可以记录对应 SHA，方便回看。

## 阅读与运行

本仓库提供源码阅读集合，各项目的构建环境按它们自己的 README 配置。

- QP/C 示例依赖 [QP/C 框架](https://github.com/QuantumLeaps/qpc)，部分主机示例需要放在框架要求的目录结构中。
- FreeRTOS-Kernel 包含内核与移植代码，运行示例需要配置对应 port、`FreeRTOSConfig.h` 和工具链。
- MCUboot 的模拟器使用 Rust/C 构建工具及依赖；设备端构建按相应平台文档配置。
- lwIP 运行需要相应的网卡、OS 适配或裸机轮询集成。
- InfiniTime 固件需要其指定的工具链与依赖；本地首次下载只初始化本仓库的 8 个直接子模块。构建前在该项目目录执行其文档要求的依赖初始化。
- Zephyr 示例需要 Zephyr SDK、west 及其工作区依赖；这些由上游文档指导配置。
- LVGL 的模拟运行需要选择相应的模拟器或移植项目。

可以先阅读接口和完整功能链路，再挑一个小示例运行。学习得到的边界设计，可以用 C 的结构体、函数指针及少量消息队列实现。

## 源码与笔记的关系

`sources/` 中保留上游源码及许可证；本仓库的中文说明位于 `docs/`。各项目按自己的许可证使用，子模块不会被统一改成一种许可证。新增笔记优先写到 `docs/`，使源码与自己的思考保持清晰。
