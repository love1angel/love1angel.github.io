---
title: inside-main
date: 2024-04-29 21:53:08
tags:
---

# main 函数的启动流程

## 环境搭建

测试环境为 arch linux，使用 docker 新建 Ubuntu 容器
``` shell
sudo docker run -it --name test ubuntu
```

在容器中
``` shell
apt update
apt install build-essential vim gdb
```

编写 main.c 源文件 vim main.c
``` c
#include <stdio.h>

int main(void)
{
    printf("hello, world\n");
    return 0;
}

```

以下我们可以发现 main 被链接到 glibc-6 的版本
```
root@2f211165438e:/# gcc main.c -o main
root@2f211165438e:/# ./main
hello, world
root@2f211165438e:/# ldd main
        linux-vdso.so.1 (0x00007fff7f3f1000)
        libc.so.6 => /lib/x86_64-linux-gnu/libc.so.6 (0x00007fb59a074000)
        /lib64/ld-linux-x86-64.so.2 (0x00007fb59a290000)
```

此时我们需要 glibc-6 的源码，下载源码，在 /etc/apt/sources.list.d/ubuntu.sources 添加以下内容

```
Types: deb-src
URIs: http://archive.ubuntu.com/ubuntu/
Suites: noble noble-updates noble-backports
Components: main universe restricted multiverse
Signed-By: /usr/share/keyrings/ubuntu-archive-keyring.gpg
```

更新源码
```shell
apt update
apt source libc6
```

发现当前文件夹有了 glibc-2.39 的源码文件


.gdbinit 文件
```
dir /glibc-2.39
```


gdb main -x .gdbinit

```
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
