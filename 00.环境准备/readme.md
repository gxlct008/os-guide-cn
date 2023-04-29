# 一，环境准备

## 需要了解的概念

> 在开始以前，你需要了解这些概念： linux, mac, terminal, compiler, emulator, nasm, qemu

## 目标：安装本教程需要的软件

### 1，在 Macbook M1 上运行，则需要安装以下软件：

```bash
brew install qemu nasm
```

如果你的 Mac 上安装了 Xcode，不要使用开发工具里面的 `nasm`，请使用这个 `nasm`：

```bash
/usr/local/bin/nasm
```

### 2，在 Linux (Ubuntu 2204) 上运行，需要安装 `qemu` 和 `nasm`

```bash
sudo apt install qemu nasm
```

## 让我们开始吧！
