# 二，引导扇区

## 需要了解的概念

- assembler (汇编)
- BIOS (Basic Input Output System) 基本输入输出系统。是一种业界标准的固件接口，是个人电脑启动时加载的第一个软件。
它是一组固化到计算机内主板上一个 ROM 芯片上的程序，它保存这计算机最重要的基本输入输出的程序、开机后自检程序和系统自启动程序，它可以从 CMOS 中读写系统设置的具体信息。

其主要功能是为计算机提供最底层的、最直接的硬件设置和控制。

## 本章目的

**创建一个被 BIOS 解释为可引导磁盘的文件。**

这是非常令人兴奋的，我们将创建自己的引导扇区！ 

### 理论基础

当计算机通启动时，首先运行主板 BIOS 程序，BIOS 并不知道如何加载操作系统，所以 BIOS 把加载操作系统的任务委托给引导扇区。

因此，引导扇区必须放在一个已知的标准位置，这个位置就是磁盘的第一个扇区 `(cylinder 0, head 0, sector 0)` (柱面0，磁头0，扇区0)，占用 512 字节。

为了确保“磁盘可引导”，BIOS 会检查该扇区的第 511 和 512 字节是不是 `0xAA55`。

这是有史以来最简单的引导扇区：

```nasm
e9 fd ff 00 00 00 00 00 00 00 00 00 00 00 00 00
00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
[ 29 more lines with sixteen zero-bytes each ]
00 00 00 00 00 00 00 00 00 00 00 00 00 00 55 aa
```

开头的三个字节表示进入无限循环，中间使用`0`填充，最后以16位的`0xAA55`结束。

> 提醒： 注意字节顺序， x86是小端结构。

### 制作自己的引导扇区

我们可以用二进制编辑器写 512 个字节的数据，或者使用汇编语言写一个非常简单的代码：

```nasm
; 开始无限循环 (e9 fd ff)
loop:
    jmp loop 

; 填充510个0然后减去前面代码的大小
times 510-($-$$) db 0

dw 0xaa55 
```

编译：

```bash
nasm -f bin boot_sect_simple.asm -o boot_sect_simple.bin
```

mac 机器上使用以下命令运行：

```bash
qemu boot.bin
或
qemu boot_sect_simple.bin
```

> OSX 警告：如果出现错误，请再次阅读 00 章。

Linux 机器上运行：

```bash
qemu-system-x86_64 boot.bin
或
qemu-system-x86_64 boot_sect_simple.bin
```

> 在一些系统上，你也许必须用以下命令执行，如果出现 SDL 错误，请尝试使用参数 `--nographic and/or --curses flag(s)`
```bash
qemu-system-x86_64 boot_sect_simple.bin
```

<!-- <img width="1217" alt="image" src="https://user-images.githubusercontent.com/92664048/166098930-5ca3653d-385f-46b3-a61c-cbacbd6b499f.png"> -->

![](https://user-images.githubusercontent.com/92664048/166098930-5ca3653d-385f-46b3-a61c-cbacbd6b499f.png)

窗口打印`Booting from Hard Disk...`，然后就没有然后了。。。。

你最后一次看到无限循环还非常的兴奋是什么时候？ ））
