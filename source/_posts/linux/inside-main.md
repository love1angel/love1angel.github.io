---
title: main 函数的启动流程
date: 2024-04-29 21:53:08
categories:
- linux
tags:
- linux
---

本文简单探讨一下 main 函数的启动流程

<!-- more -->

## 环境搭建

测试环境为 arch linux, x86 ISA，使用 docker 新建 Ubuntu 容器

``` shell
sudo docker run -it --name test ubuntu
```

在容器中

``` shell
cd ~ && apt update && apt install -y build-essential vim gdb less
```

vim main.c，编写 main.c 源文件

``` c
#include <stdio.h>

int main(void)
{
    printf("hello, world\n");
    return 0;
}
```

gcc -g main.c -o main

## 读取 main 可执行程序

``` text
root@479c0e77cdf2:~# readelf -h main
ELF Header:
  Magic:   7f 45 4c 46 02 01 01 00 00 00 00 00 00 00 00 00
  Class:                             ELF64
  Data:                              2's complement, little endian
  Version:                           1 (current)
  OS/ABI:                            UNIX - System V
  ABI Version:                       0
  Type:                              DYN (Position-Independent Executable file)
  Machine:                           Advanced Micro Devices X86-64
  Version:                           0x1
  Entry point address:               0x1060
  Start of program headers:          64 (bytes into file)
  Start of section headers:          13976 (bytes into file)
  Flags:                             0x0
  Size of this header:               64 (bytes)
  Size of program headers:           56 (bytes)
  Number of program headers:         13
  Size of section headers:           64 (bytes)
  Number of section headers:         31
  Section header string table index: 30
```

请注意 Entry point address 的地址为 0x1060，程序入口地址

``` shell
objdump -D main |less
```

查找程序入口地址 1060，可以看到如下

``` assembly
Disassembly of section .text:

0000000000001060 <_start>:
    1060:       f3 0f 1e fa             endbr64
    1064:       31 ed                   xor    %ebp,%ebp
    1066:       49 89 d1                mov    %rdx,%r9
    1069:       5e                      pop    %rsi
    106a:       48 89 e2                mov    %rsp,%rdx
    106d:       48 83 e4 f0             and    $0xfffffffffffffff0,%rsp
    1071:       50                      push   %rax
    1072:       54                      push   %rsp
    1073:       45 31 c0                xor    %r8d,%r8d
    1076:       31 c9                   xor    %ecx,%ecx
    1078:       48 8d 3d ca 00 00 00    lea    0xca(%rip),%rdi        # 1149 <main>
    107f:       ff 15 53 2f 00 00       call   *0x2f53(%rip)        # 3fd8 <__libc_start_main@GLIBC_2.34>
    1085:       f4                      hlt
    1086:       66 2e 0f 1f 84 00 00    cs nopw 0x0(%rax,%rax,1)
    108d:       00 00 00

0000000000001140 <frame_dummy>:
    1140:       f3 0f 1e fa             endbr64
    1144:       e9 77 ff ff ff          jmp    10c0 <register_tm_clones>

0000000000001149 <main>:
    1149:       f3 0f 1e fa             endbr64
    114d:       55                      push   %rbp
    114e:       48 89 e5                mov    %rsp,%rbp
    1151:       48 8d 05 ac 0e 00 00    lea    0xeac(%rip),%rax        # 2004 <_IO_stdin_used+0x4>
    1158:       48 89 c7                mov    %rax,%rdi
    115b:       e8 f0 fe ff ff          call   1050 <puts@plt>
    1160:       b8 00 00 00 00          mov    $0x0,%eax
    1165:       5d                      pop    %rbp
    1166:       c3                      ret
```

1. _start 的地址 0000000000001060
2. main 函数实现的地址如下 0000000000001149
3. __libc_start_main 为 glibc 动态库的桩代码

我们逐步分析 _start 主要做了什么

1060 启用 RAI (Return Address Indirection) 技术
1064 清零 ebp 基址寄存器
1066 将通用寄存器 rdx 拷贝到 r9 中
1069 从栈上 pop 一个值给 rsi
106a 栈顶指针 rsp 拷到 rdx 中
106d rsp 和 16 字节向下对齐
1071 push rax
1072 push rsp
1073 清零 r8d
1076 清零 rcx
1078 rdi 的值为 rip + 0xca
107f 跳到 0x2f53 + rip 的地址

很难看明白，还是用 gdb 看 glibc 到底做了什么

## 安装 glibc-6

以下我们可以发现 main 被链接到 glibc-6 的版本

``` shell
root@479c0e77cdf2:~# ldd main
	linux-vdso.so.1 (0x00007ffeb53c7000)
	libc.so.6 => /lib/x86_64-linux-gnu/libc.so.6 (0x00007f79e99b7000)
	/lib64/ld-linux-x86-64.so.2 (0x00007f79e9bea000)
```

为了更近一步分析，我们需要 glibc-6 的源码，下载源码或者在 /etc/apt/sources.list.d/ubuntu.sources 添加以下内容

``` text
Types: deb-src
URIs: http://archive.ubuntu.com/ubuntu/
Suites: noble noble-updates noble-backports
Components: main universe restricted multiverse
Signed-By: /usr/share/keyrings/ubuntu-archive-keyring.gpg
```

更新源和源码

```shell
apt update && apt source libc6
```

发现当前文件夹有了 glibc-2.39 的源码文件

编写 .gdbinit 文件，填源文件搜索路径

``` gdb
dir ~/glibc-2.39
b _start
r
```

gdb main -x .gdbinit

``` gdb
b _dl_start_user
b _dl_init
b call_init
b _init_first
b __init_misc
b __libc_start_main_impl
b __libc_start_call_main

```

_dl_start_user 是 ld 动态库

## 分析

_init_first

__init_misc

__libc_start_main_impl

__libc_start_call_main

## 这三个函数都做了什么呢？

@todo
