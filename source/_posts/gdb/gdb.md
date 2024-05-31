---
title: gdb
date: 2022-05-11 17:16:51
categories:
- gdb
tags:
---

gdb cheatsheet

<!-- more -->

## common

### usage

1. add -g option when compile
2. <Ctrl + l> refresh window

### docs

> 1. https://sourceware.org/gdb/onlinedocs/gdb/
> 2. https://developer.apple.com/library/archive/documentation/DeveloperTools/gdb/gdb/gdb_6.html

### set gdb auto run commands when execute gdb

gdb main -x .gdbinit

write .gdbinit like this

``` text
b main.c:4
r
p c
```

### gdb command

`file main`: read file main's symbol

`start`: enter main function and break

`b <line number, function name>`, `breakpoint`: add break point

`c`: continue run

`n`: next step

`s`: step into

`list`: list current running several lines

`p <variable name, expression>`: print variable values, expression values

`bt`: print back trace, function call stack information

---

`info breakpoints`: print break points information

`info locals`: print local variable values

`info proc map`: program memory map @todo

---

`help layout`: check layout related command
`layout split`: split layout in different window
`layout src`: show source window
`layout asm`: show assemble window
`layout regs`: show registers window

## debug when running

`ps -elf | grep <executable file>`
`gdb attach <pid>`

## debug in cross compile environment

`set sysroot /opt/cross/sysroots/aarch64-poky-linux`: set the path of your cross compile system root

### use gdbserver debug remotely or in different arch

@todo

## debug when crash using coredump

we need three things before debugging

1. executable file
2. coredump file
3. sdk when cross compile environment

### generate coredump file, take `Ubuntu` environment as an example

Ubuntu default configuration(ulimit -c equal 0) will not generate coredump file. we can check by running this command

``` bash
ulimit -a
```

output:

``` text
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
```

then we can change ulimit -c temporarily. **only take effect in the current running shell**

``` shell
ulimit -c unlimited
```

### then system will generate coredump file when program crash, but where will we get this file?

we can check this

``` shell
cat /proc/sys/kernel/core_pattern
```

default setting is here. system will execute apport program report remote when coredump. the coredump file is in the directory `/var/lib/apport/coredump/`, you can find according to the executable file name, timestamp and so on.

``` text
|/usr/share/apport/apport -p%p -s%s -c%c -d%d -P%P -u%u -g%g -- %E
```

we can change coredump output path by using this command.

``` shell
sysctl -w kernel.core_pattern=/tmp/core_%e_%p
```

debug command

``` shell
gdb ~/main -c /tmp/core_main_1859038
```

## break point in practice

``` txt
(gdb) b main
Breakpoint 1 at 0x243d: file test.cpp, line 57.

(gdb) b test.cpp:35
Breakpoint 2 at 0x2c6c: file test.cpp, line 35.
```

delete break point

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

watch point is also a break point

`watch` command is used to set a watchpoint for writing

`rwatch` for reading

`awatch` for reading/writing.
