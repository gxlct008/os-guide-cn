# 二，引导扇区

## 本章目的

**创建一个文件，BIOS 将文件识别为一个可引导的磁盘。**

这是非常令人兴奋的，我们将创建自己的引导扇区！ 

### 理论基础

计算机通电启动后，首先运行主板 BIOS 程序， BIOS 并不知道如何加载操作系统，所以 BIOS 把加载操作系统的任务交给引导扇区。

所以，放在一个已知的标准位置，这个位置就是磁盘的第一个扇区 `(cylinder 0, head 0, sector 0)`，该扇区一共占用 512 字节。

为了确保可以引导，BIOS 会检查扇区的第 511 和 512 字节是不是 `0xAA55`。

```nasm
e9 fd ff 00 00 00 00 00 00 00 00 00 00 00 00 00
00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
[ 29 more lines with sixteen zero-bytes each ]
00 00 00 00 00 00 00 00 00 00 00 00 00 00 55 aa
```

开头的三个字节表示进入无限循环，中间使用`0`填充，最后以16位的`0xAA55`结束。

> 提醒： 注意字节顺序， x86是小端结构。

### 制作自己的引导扇区

嗯，我们需要写512个字节的数据：

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
nasm -f bin 1.asm -o boot.bin
```

mac 机器上使用以下命令运行：

```bash
qemu boot.bin
```

Linux 机器上运行：

```bash
qemu-system-x86_64 boot.bin
```

<img width="1217" alt="image" src="https://user-images.githubusercontent.com/92664048/166098930-5ca3653d-385f-46b3-a61c-cbacbd6b499f.png">

窗口打印`Booting from Hard Disk...`，然后就没有然后了。。。。

你最后一次看到无限循环还非常的兴奋是什么时候？ ））
