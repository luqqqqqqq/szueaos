# SZUEAOS v1.0.0

<div align="center">

**从 BIOS 引导到 x86 保护模式的操作系统学习项目**<br>
*An x86 bootloader and protected-mode learning project*

[![License](https://img.shields.io/badge/license-GPL--3.0-7c3aed.svg)](LICENSE)

[快速开始](#快速开始) · [文档导航](#文档导航) · [更新日志](CHANGELOG.md)

</div>

## 项目简介

SZUEAOS 使用 NASM 汇编和 Bochs 模拟器，记录从 BIOS 启动、读取磁盘到进入 32 位保护模式的学习过程。仓库提供 MBR、二级加载程序、可供实验的固定大小 VHD，以及 BIOS、实模式内存和 x86 指令集相关资料。

The repository contains a two-stage x86 boot experiment, a sample VHD, and study notes. The current code covers bootstrapping and a protected-mode display demo; kernel services are still to be implemented.

## 主要特性

| 功能 | 说明 |
| --- | --- |
| MBR | 512 字节引导扇区，BIOS 文本输出与键盘等待 |
| 磁盘读取 | ATA PIO / LBA，读取 LBA 1 的一个扇区至 `0x0900` |
| Loader | 开启 A20、装载 GDT、设置 `CR0.PE` 并进入 32 位保护模式 |
| 显示实验 | 通过 VGA 文本显存输出字符 |
| 运行环境 | Windows 上的 NASM 与 Bochs，附 64 MiB 固定大小 VHD 样例 |
| 学习资料 | 引导流程说明、原始学习笔记、处理器手册和工具归档 |

## 快速开始

准备 [Bochs](https://bochs.sourceforge.io/)，并将其安装目录加入 `PATH`。在仓库根目录打开 PowerShell：

```powershell
New-Item -ItemType Directory -Force build | Out-Null
Copy-Item releases/v1.0.0/hard_disk.vhd build/hard_disk.vhd
bochs -f config/bochsrc.bxrc
```

在 Bochs 中选择开始模拟。MBR 输出 `szuea` / `booting...` 后会等待按键；按键后才会继续加载 Loader。首次启动前，按 [运行与调试指南](docs/getting-started.md) 检查 BIOS ROM 路径和 Bochs 的 Windows 显示配置。

修改源码时，用 [NASM](https://www.nasm.us/) 重新汇编，再将 MBR 和 Loader 分别写入工作磁盘的 LBA 0、LBA 1。完整步骤见 [开发与验证](docs/development.md)。

## 目录结构

```text
.
├── src/boot/                   # mbr.asm 与 loader.asm
├── config/                     # Bochs 配置，从仓库根目录启动
├── releases/v1.0.0/             # VHD 与配套的引导二进制样例
├── docs/
│   ├── README.md               # 文档导航
│   ├── getting-started.md      # 环境准备与样例运行
│   ├── development.md          # 汇编、写盘与调试
│   ├── boot-flow.md            # 与当前源码对应的引导流程
│   ├── notes/                  # 内存、环境与 BIOS 学习笔记
│   └── assets/                 # 8086 寄存器与复位状态参考图
├── references/                 # 手册、补充文档及汇编清单
├── tools/                      # 原有辅助工具归档
├── README.md
├── CHANGELOG.md
├── VERSION
├── .gitignore
└── LICENSE
```

`build/` 用于本地汇编结果、工作磁盘和运行日志，已加入忽略规则。资料入口见 [参考资料索引](references/README.md)。

## 文档导航

| 文档 | 内容 |
| --- | --- |
| [文档首页](docs/README.md) | 按使用目的查找资料 |
| [快速开始](docs/getting-started.md) | 配置 Bochs 并运行已有样例 |
| [开发与验证](docs/development.md) | NASM 汇编、VHD 写盘与调试 |
| [引导流程](docs/boot-flow.md) | MBR、Loader 与保护模式的实现范围 |
| [参考资料](references/README.md) | 学习笔记、处理器手册与汇编清单 |

## 开发与验证

- 引导源码入口：[mbr.asm](src/boot/mbr.asm)、[loader.asm](src/boot/loader.asm)；构建命令见 [开发指南](docs/development.md)。
- 实现范围：MBR、二级加载及保护模式显示实验；尚未提供中断管理、分页、任务调度和文件系统。
- 当前 MBR 只读取一个扇区，Loader 必须保持在 512 字节以内；扩展 Loader 时需要同时调整读取逻辑。
- 已核对磁盘样例、引导签名和文件引用；现有验证不包含 Bochs 启动或真机测试。中断处理等已知限制见 [引导流程](docs/boot-flow.md)。

## 致谢

感谢 [NASM](https://www.nasm.us/) 与 [Bochs](https://bochs.sourceforge.io/) 项目提供汇编与模拟工具，以及参考资料原作者提供的处理器与系统编程资料。

## 许可证

项目代码采用 [GNU GPL-3.0](LICENSE)。收录的处理器手册、教材、图片和工具归档保留各自作者的版权与许可，项目许可证不替代这些资料原有的授权条件。
