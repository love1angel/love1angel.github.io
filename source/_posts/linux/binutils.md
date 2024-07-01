---
title: binutils usage
date: 2024-06-06 13:16:25
categories:
tags:
---

<!-- more -->

xxd

vim %! xxd

## install

Ubuntu

``` bash
apt install binutils
```

file main

## objdump

inspect full assembly

``` bash
objdump -D <.o> |less
```

``` bash
objdump -S <.o> |less
```

对源代码和汇编一一对应，要加 -g 选项

## readelf

readelf -h hello.o 看 elf header

readelf -SW hello.o 看 section header table( W 加变宽，好看一点)

readelf -lW main

---

qemu-riscv64 -L /usr/riscv64-linux-gnu ./main

qemu-riscv64 -E LD_LIBRARY_PATH=/usr/riscv64-linux-gnu/lib ./main
