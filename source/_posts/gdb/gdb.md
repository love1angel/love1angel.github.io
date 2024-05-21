---
title: gdb
date: 2022-05-11 17:16:51
categories:
- gdb
tags:
---

gdb cheatsheet

<!-- more -->

## docs

https://developer.apple.com/library/archive/documentation/DeveloperTools/gdb/gdb/gdb_6.html
https://sourceware.org/gdb/onlinedocs/gdb/

编译时需要增加 -g 参数，control + l 进行刷新

## common

设置默认命令，gdb 启动后自动执行

gdb main -x .gdbinit

```cpp
b main.c:4
r
p c
```

[start] 开始从 main 断点执行

[b] 打断点 break point

[c] 继续 continue

[n] 下一步 next

[s] 进入查看 step into

[list] 查看部分源码

[p var] 打印 var 变量的值，信息，或者表达式运算结果

[bt] 打印栈信息，backtrace

[info breakpoints] 查看 break point 的信息

[info locals] 查看局部变量

[info proc map]

## crash 调试

## 生成 coredump 文件

```bash
# 默认不生成 coredump 文件，大小为 0
$ ulimit -a
-t: cpu time (seconds)              unlimited
-f: file size (blocks)              unlimited
-d: data seg size (kbytes)          unlimited
-s: stack size (kbytes)             8192
-c: core file size (blocks)         0
-m: resident set size (kbytes)      unlimited
-u: processes                       255290
-n: file descriptors                1024
-l: locked-in-memory size (kbytes)  65536
-v: address space (kbytes)          unlimited
-x: file locks                      unlimited
-i: pending signals                 255290
-q: bytes in POSIX msg queues       819200
-e: max nice                        0
-r: max rt priority                 0
-N 15:                              unlimited

# 修改 -c 为 unlimited
$ ulimit -c unlimited

# 查看 coredump 文件格式
$ cat /proc/sys/kernel/core_pattern
|/usr/share/apport/apport -p%p -s%s -c%c -d%d -P%P -u%u -g%g -- %E

# 改变生成的位置
$ sysctl -w kernel.core_pattern=/tmp/core_%e_%p

# 调试
$ gdb ~/main -c /tmp/core_main_1859038
```

### 读取 core 文件

[gdb main -c .core] 读取 core 文件

## 通用读取配置

[file main] 读取 main 的 symbol

### 交叉编译工具链

[set sysroot /opt/cross/sysroots/aarch64-poky-linux] 设置系统路径，不确定

## layout

`help layout` 帮助

`layout split` 分开 src asm cmd

`layout src` 新增源码查看窗口
`layout asm` 新增汇编查看窗口
`layout regs` 查看寄存器

## 常用命令

`list` 查看部分源码

## break point

可以打在函数（展开很难看），文件行号上

``` txt
(gdb) b main
Breakpoint 1 at 0x243d: file test.cpp, line 57.

(gdb) b test.cpp:35
Breakpoint 2 at 0x2c6c: file test.cpp, line 35.
```

删除

``` txt
(gdb) info breakpoints
Num     Type           Disp Enb Address            What
1       breakpoint     keep y   0x000000000000243d in main() at test.cpp:57
2       breakpoint     keep y   0x0000000000002c6c in Solution::minWindow(std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >) at test.cpp:35

(gdb) delete 1

(gdb) info breakpoints
Num     Type           Disp Enb Address            What
2       breakpoint     keep y   0x0000000000002c6c in Solution::minWindow(std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >) at test.cpp:35
```

watch point 也是一种 break point

`watch` command is used to set a watchpoint for writing

`rwatch` for reading

`awatch` for reading/writing.
