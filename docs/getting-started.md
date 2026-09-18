# 快速开始

[返回项目首页](../README.md) · [文档导航](README.md)

本文以 Windows 和仓库根目录作为工作目录。原有配置来自 Bochs 2.7，包含 Windows 的显示、配置界面与声音后端；其他平台或版本需要按安装情况调整。

## 准备环境

| 工具 | 用途 |
| --- | --- |
| [NASM](https://www.nasm.us/) | 将 `src/boot/*.asm` 汇编成平坦二进制 |
| [Bochs](https://bochs.sourceforge.io/) | 模拟 x86、BIOS 与 ATA 磁盘；调试版提供逐指令执行 |
| FixVhdWr 或同类 VHD 扇区写入工具 | 将二进制写入工作磁盘的指定 LBA |

原有工具包保存在 [`tools/tool_pkg.rar`](../tools/tool_pkg.rar)，相关说明见 [工具索引](../tools/README.md)。查看预置样例只需要 Bochs；修改源码后才需要 NASM 和写盘工具。

## 运行已有样例

将 Bochs 的可执行文件目录加入 `PATH`，然后执行：

```powershell
New-Item -ItemType Directory -Force build | Out-Null
Copy-Item releases/v1.0.0/hard_disk.vhd build/hard_disk.vhd
bochs -f config/bochsrc.bxrc
```

`releases/v1.0.0/hard_disk.vhd` 是保留的样例，`build/hard_disk.vhd` 是运行和写盘使用的工作副本。再次复制样例会覆盖工作副本里的实验修改。

配置中的 `$BXSHARE` 是 Bochs 的资源目录变量，目录下需要有 `BIOS-bochs-latest` 和 `VGABIOS-lgpl-latest`。如果安装包未自动提供这个变量，在当前 PowerShell 中设置它，再启动 Bochs，例如：

```powershell
# 改为本机实际包含 BIOS ROM 文件的目录。
$env:BXSHARE = 'C:\Program Files\Bochs-2.7'
bochs -f config/bochsrc.bxrc
```

在配置界面选择开始模拟。程序先显示引导信息并等待键盘输入，按键后继续读取 Loader。需要自定义配置时，可复制为 `config/bochsrc.local.bxrc`；该文件已被 Git 忽略。

## 下一步

修改源码、写入 VHD 或逐指令调试时，继续阅读 [开发与验证](development.md)。
