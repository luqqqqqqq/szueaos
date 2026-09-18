# 开发与验证

[返回项目首页](../README.md) · [文档导航](README.md)

本文从仓库根目录执行命令。运行环境及 ROM 路径设置见 [快速开始](getting-started.md)。

## 源码与输出

| 目录 | 用途 |
| --- | --- |
| `src/boot/` | MBR 与 Loader 汇编源码 |
| `config/` | Bochs 配置 |
| `releases/v1.0.0/` | 已收录的 VHD 与二进制样例 |
| `build/` | 本地构建结果与可修改的工作磁盘，已被 Git 忽略 |

## 汇编源码

在仓库根目录执行：

```powershell
New-Item -ItemType Directory -Force build | Out-Null
nasm -f bin src/boot/mbr.asm -o build/mbr.bin -l build/mbr.lst
nasm -f bin src/boot/loader.asm -o build/loader.bin -l build/loader.lst
```

两条汇编命令都成功后再写盘。当前布局约定为：

| 文件 | 写入位置 | 加载地址 | 大小约束 |
| --- | --- | --- | --- |
| `mbr.bin` | LBA 0，偏移 0 | `0x7C00` | 恰好 512 字节，以 `55 AA` 结尾 |
| `loader.bin` | LBA 1，偏移 512 | `0x0900` | 不超过 512 字节 |

仓库内的样例 `mbr.bin` 为 512 字节，`loader.bin` 为 261 字节，分别与样例 VHD 对应位置的内容一致。样例尺寸不代表修改后汇编结果的尺寸。

## 写入工作磁盘

1. 关闭正在使用工作磁盘的 Bochs。
2. 如果尚未创建工作副本，将 `releases/v1.0.0/hard_disk.vhd` 复制到 `build/hard_disk.vhd`。
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

## 验证范围

已静态核对 MBR 签名、VHD footer、磁盘几何和两个样例二进制与磁盘扇区的对应关系。现有检查不包含 NASM 重编译、Bochs 启动或真机测试。保护模式中断处理尚未完成，具体限制见 [引导流程](boot-flow.md)。
