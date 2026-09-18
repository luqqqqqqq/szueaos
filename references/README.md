# 学习资料索引

当前运行入口见 [编译、运行与调试](../docs/getting-started.md)，现有代码行为见 [引导流程](../docs/boot-flow.md)。本目录和下列笔记用于学习与查阅。

## 学习笔记

| 资料 | 内容 |
| --- | --- |
| [实模式与显存笔记](../docs/notes/memory-notes.md) | 常见内存布局、文本显存与引导入口 |
| [环境与调试笔记](../docs/notes/development-notes.md) | 环境搭建和学习记录 |
| [BIOS 学习笔记](../docs/notes/bios-notes.txt) | 上电、复位、POST 与启动过程记录 |
| [实模式内存文档](notes/real-mode-memory.docx) | 1 MiB 地址空间补充资料 |
| [BIOS 中断文档](notes/bios-interrupts.docx) | 中断相关补充资料 |
| [8086 标志寄存器图](../docs/assets/8086-flags.png) | 标志寄存器参考图 |
| [8086 复位状态图](../docs/assets/8086-reset-state.png) | 复位状态参考图 |

原始 BIOS 笔记含未核实的表述；实现细节以当前源码和处理器手册为准。

## 手册与教材

- [Intel 8086/8088 用户手册](manuals/1981_iAPX_86_88_Users_Manual.pdf)
- [Intel 软件开发者手册合集](manuals/325462-sdm-vol-1-2abcd-3abcd.pdf)
- [Intel 指令集参考，Volume 2A](manuals/64-ia-32-architectures-software-developer-vol-2a-manual.pdf)
- [第四代 Intel Core 桌面处理器数据手册](manuals/4th-gen-core-family-desktop-vol-2-datasheet-v1.pdf)
- [NASM 中文手册](manuals/NASM中文手册.pdf)
- [x86 汇编语言：从实模式到保护模式](manuals/x86汇编语言-从实模式到保护模式-文字版.pdf)

这些文件为仓库原有参考资料，版权与使用条件由原作者或发布者决定。最新处理器资料可从 [Intel 官方文档入口](https://www.intel.com/content/www/us/en/developer/articles/technical/intel-sdm.html) 查询。

## 汇编清单

[`listings/mbr.lst`](listings/mbr.lst) 和 [`listings/loader.lst`](listings/loader.lst) 是随原有二进制保留的汇编清单。新编译产生的 `.lst` 应放在 `build/`，便于与实际修改后的源码对应。
