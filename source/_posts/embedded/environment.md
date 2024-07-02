---
title: 嵌入式环境
date: 2024-07-02 10:05:53
categories:
tags:
---

- [x] compile
- [x] qemu
- [x] gdb

<!-- more -->

通常来说，供应商或者公司内部一般会提供交叉编译环境的 sdk，用来统一库版本、开发环境。Yocto 是其中一个可以客制化配置 linux 比较通用的工具，其中也制作了交叉环境下的工具链，下面提到的 poky 环境就是用 Yocto 制作出来的。不特别说明的环境则是在 Ubuntu 20.04.6 LTS 环境下

## cross compile environment

交叉环境下的命名前缀一般遵循 arch-vender-os-

``` shell
$ whereis riscv64-linux-gnu-gcc
riscv64-linux-gnu-gcc: /usr/bin/riscv64-linux-gnu-gcc /usr/share/man/man1/riscv64-linux-gnu-gcc.1.gz
$ riscv64-linux-gnu-gcc main.c -o main -g

# 在 poky 环境下
$ whereis aarch64-poky-linux-g++
/opt/cross/sysroots/x86_64-pokysdk-linux/usr/bin/aarch64-poky-linux/aarch64-poky-linux-g++
```

## qemu

-L 指定 sysroot

``` shell
qemu-riscv64 -L /usr/riscv64-linux-gnu ./main
```

## gdb cross debuging

``` shell
# 1. 启动 gdbserver 等待远程连接, 1234 为默认端口
$ qemu-riscv64 -L /usr/riscv64-linux-gnu -g 1234 ./main

# 2. 使用本地的 gdb 去远程连接，gdb-multiarch 是支持多种架构的 gdb
$ gdb-multiarch ./main
(gdb) set architecture riscv:rv64
(gdb) target remote localhost:1234

# 2. 在 poky 环境下
# 一般来说 poky 环境下的 sdk 都已经配置好了，如下
# This GDB was configured as "--host=x86_64-pokysdk-linux --target=aarch64-poky-linux".
$ aarch64-poky-linux-gdb ./main
(gdb) target remote localhost:1234

```
