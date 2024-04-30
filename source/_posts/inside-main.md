---
title: main 函数的启动流程
date: 2024-04-29 21:53:08
tags:
---

## 环境搭建

测试环境为 arch linux, x86 ISA，使用 docker 新建 Ubuntu 容器

``` shell
sudo docker run -it --name test ubuntu
```

在容器中

``` shell
apt update && apt install -y build-essential vim gdb less
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

## 反汇编 main 可执行程序

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

请注意 Entry point address 的地址为 0x1060，也就是可执行文件的第一行指令。记住该地址，下面会用到

``` shell
objdump -D main |less
```

查找可得

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
```

发现反汇编中和 main 相关的 label 为 _start，和 GLIBC_2.34 动态库中的桩地址

_start 的地址 0000000000001060，main 函数的地址如下 0000000000001149

``` assembly
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

我们不难发现 _start 主要将 main 函数的地址传入寄存器中，并在 glibc-6 中用到了该地址，所以我们需要安装 glibc-6 源码一探动态库里究竟做了什么

## 安装 glibc-6

以下我们可以发现 main 被链接到 glibc-6 的版本

``` shell
root@2f211165438e:/# gcc main.c -o main
root@2f211165438e:/# ./main
hello, world
root@2f211165438e:/# ldd main
        linux-vdso.so.1 (0x00007fff7f3f1000)
        libc.so.6 => /lib/x86_64-linux-gnu/libc.so.6 (0x00007fb59a074000)
        /lib64/ld-linux-x86-64.so.2 (0x00007fb59a290000)
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
dir /glibc-2.39
```

gdb main -x .gdbinit

``` gdb
(gdb) b __libc_start_main
Function "__libc_start_main" not defined.
Make breakpoint pending on future shared library load? (y or [n]) y
Breakpoint 1 (__libc_start_main) pending.
(gdb) r
Starting program: /main 
warning: Error disabling address space randomization: Operation not permitted
[Thread debugging using libthread_db enabled]
Using host libthread_db library "/lib/x86_64-linux-gnu/libthread_db.so.1".

Breakpoint 1, __libc_start_main_impl (main=0x55c48a497149 <main>, argc=1, argv=0x7ffee2997f08, init=0x0, 
    fini=0x0, rtld_fini=0x7f881795c380 <_dl_fini>, stack_end=0x7ffee2997ef8) at ../csu/libc-start.c:242
242     {
(gdb) bt
#0  __libc_start_main_impl (main=0x55c48a497149 <main>, argc=1, argv=0x7ffee2997f08, init=0x0, fini=0x0, 
    rtld_fini=0x7f881795c380 <_dl_fini>, stack_end=0x7ffee2997ef8) at ../csu/libc-start.c:242
#1  0x000055c48a497085 in _start ()
```

## 分析

1. 先调用 _start ()
2. 在调用 __libc_start_main_impl
3. 最后调用 main 函数

## 这三个函数都做了什么呢？

@todo
