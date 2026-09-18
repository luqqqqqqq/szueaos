# 编译、运行与调试

本文以 Windows 和仓库根目录作为工作目录。原有配置来自 Bochs 2.7，包含 Windows 的显示、配置界面与声音后端；其他平台或版本需要按安装情况调整。

## 准备环境

| 工具 | 用途 |
| --- | --- |
| [NASM](https://www.nasm.us/) | 将 `boot/*.asm` 汇编成平坦二进制 |
| [Bochs](https://bochs.sourceforge.io/) | 模拟 x86、BIOS 与 ATA 磁盘；调试版提供逐指令执行 |
| FixVhdWr 或同类 VHD 扇区写入工具 | 将二进制写入工作磁盘的指定 LBA |

原有工具包保存在 [`tools/tool_pkg.rar`](../tools/tool_pkg.rar)，相关说明见 [工具索引](../tools/README.md)。查看预置样例只需要 Bochs；修改源码后才需要 NASM 和写盘工具。

## 运行已有样例

将 Bochs 的可执行文件目录加入 `PATH`，然后执行：

```powershell
New-Item -ItemType Directory -Force build | Out-Null
Copy-Item images/hard_disk.vhd build/hard_disk.vhd
bochs -f config/bochsrc.bxrc
```

`images/hard_disk.vhd` 是保留的样例，`build/hard_disk.vhd` 是运行和写盘使用的工作副本。再次复制样例会覆盖工作副本里的实验修改。

配置中的 `$BXSHARE` 是 Bochs 的资源目录变量，目录下需要有 `BIOS-bochs-latest` 和 `VGABIOS-lgpl-latest`。如果安装包未自动提供这个变量，在当前 PowerShell 中设置它，再启动 Bochs，例如：

```powershell
# 改为本机实际包含 BIOS ROM 文件的目录。
$env:BXSHARE = 'C:\Program Files\Bochs-2.7'
bochs -f config/bochsrc.bxrc
```

在配置界面选择开始模拟。程序先显示引导信息并等待键盘输入，按键后继续读取 Loader。需要自定义配置时，可复制为 `config/bochsrc.local.bxrc`；该文件已被 Git 忽略。

## 汇编源码

在仓库根目录执行：

```powershell
New-Item -ItemType Directory -Force build | Out-Null
nasm -f bin boot/mbr.asm -o build/mbr.bin -l build/mbr.lst
nasm -f bin boot/loader.asm -o build/loader.bin -l build/loader.lst
```

两条汇编命令都成功后再写盘。当前布局约定为：

| 文件 | 写入位置 | 加载地址 | 大小约束 |
| --- | --- | --- | --- |
| `mbr.bin` | LBA 0，偏移 0 | `0x7C00` | 恰好 512 字节，以 `55 AA` 结尾 |
| `loader.bin` | LBA 1，偏移 512 | `0x0900` | 不超过 512 字节 |

仓库内的样例 `mbr.bin` 为 512 字节，`loader.bin` 为 261 字节，分别与样例 VHD 对应位置的内容一致。样例尺寸不代表修改后汇编结果的尺寸。

## 写入工作磁盘

1. 关闭正在使用工作磁盘的 Bochs。
2. 如果尚未创建工作副本，将 `images/hard_disk.vhd` 复制到 `build/hard_disk.vhd`。
3. 在写盘工具中选择 **`build/hard_disk.vhd` 文件**。
4. 将 `build/mbr.bin` 写入 LBA 0。
5. 将 `build/loader.bin` 写入 LBA 1。
6. 用 `bochs -f config/bochsrc.bxrc` 再次启动。

当前样例为固定大小 VHD，数据区为 64 MiB，文件末尾还有 512 字节 VHD footer。Bochs 配置采用 `flat`，磁盘几何为 963 cylinders / 8 heads / 17 sectors，与样例 footer 记录一致。使用新的磁盘镜像时应重新核对几何参数；写入引导扇区时保留 VHD footer。

## 调试入口

如果安装包提供 `bochsdbg.exe`，可执行：

```powershell
bochsdbg -f config/bochsrc.bxrc
```

| 命令 | 用途 |
| --- | --- |
| `r` | 查看通用寄存器 |
| `sreg` | 查看段寄存器 |
| `creg` | 查看控制寄存器 |
| `s` | 执行一条指令 |
| `n` | 单步越过调用或循环 |
| `b 0x7c00` | 在 MBR 入口设置断点 |
| `b 0x900` | 在 Loader 入口设置断点 |
| `c` | 继续执行 |
| `q` | 退出模拟器 |
| `help` | 查看当前 Bochs 调试器命令说明 |

源码对应的阶段和地址见 [引导流程](boot-flow.md)。本仓库保留的学习笔记见 [参考资料索引](../references/README.md)。
