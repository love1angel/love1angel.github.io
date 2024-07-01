---
title: ELF file format
date: 2024-06-06 13:34:10
categories:
- linux
tags:
- done
---

ELF (Executable and Linkable Format)

<!-- more -->

wiki: <https://en.wikipedia.org/wiki/Executable_and_Linkable_Format>

![ELF file layout](https://github.com/love1angel/love1angel.github.io/blob/hexo/source/_posts/linux/ELF_layout.jpeg?raw=true)

ELF header

``` bash
readelf -h main.o
```

An ELF file has two views:

1. the program header tables shows the segments used at run time. (runtime view)

    ``` bash
    readelf -lW main
    ```

2. the section header tables lists the set of sections. (link view)

    ``` bash
    readelf -SW main
    ```

segment is composed of several section with the same (memory flag view ???). in order to save memory since memory alignment caused.

## symbol

take following as an example

``` c
#include <stdio.h>

int global_init = 0x11111111;
const int global_const = 0x22222222;

int main()
{
    static int static_var = 0x33333333;
    static int static_var_uninit;
    int auto_var = 0x44444444;
    printf("hello world!\n");
    return 0;
}
```

`readelf -SW main.o`

``` text
There are 14 section headers, starting at offset 0x3c8:

Section Headers:
  [Nr] Name              Type            Address          Off    Size   ES Flg Lk Inf Al
  [ 0]                   NULL            0000000000000000 000000 000000 00      0   0  0
  [ 1] .text             PROGBITS        0000000000000000 000040 000022 00  AX  0   0  1
  [ 2] .rela.text        RELA            0000000000000000 000308 000030 18   I 11   1  8
  [ 3] .data             PROGBITS        0000000000000000 000064 000008 00  WA  0   0  4
  [ 4] .bss              NOBITS          0000000000000000 00006c 000004 00  WA  0   0  4
  [ 5] .rodata           PROGBITS        0000000000000000 00006c 000011 00   A  0   0  4
  [ 6] .comment          PROGBITS        0000000000000000 00007d 00002c 01  MS  0   0  1
  [ 7] .note.GNU-stack   PROGBITS        0000000000000000 0000a9 000000 00      0   0  1
  [ 8] .note.gnu.property NOTE            0000000000000000 0000b0 000020 00   A  0   0  8
  [ 9] .eh_frame         PROGBITS        0000000000000000 0000d0 000038 00   A  0   0  8
  [10] .rela.eh_frame    RELA            0000000000000000 000338 000018 18   I 11   9  8
  [11] .symtab           SYMTAB          0000000000000000 000108 000198 18     12  12  8
  [12] .strtab           STRTAB          0000000000000000 0002a0 000068 00      0   0  1
  [13] .shstrtab         STRTAB          0000000000000000 000350 000074 00      0   0  1
Key to Flags:
  W (write), A (alloc), X (execute), M (merge), S (strings), I (info),
  L (link order), O (extra OS processing required), G (group), T (TLS),
  C (compressed), x (unknown), o (OS specific), E (exclude),
  l (large), p (processor specific)
```

read symbol by

`readelf -sW main.o`

``` text
Symbol table '.symtab' contains 17 entries:
   Num:    Value          Size Type    Bind   Vis      Ndx Name
     0: 0000000000000000     0 NOTYPE  LOCAL  DEFAULT  UND
     1: 0000000000000000     0 FILE    LOCAL  DEFAULT  ABS main.c
     2: 0000000000000000     0 SECTION LOCAL  DEFAULT    1
     3: 0000000000000000     0 SECTION LOCAL  DEFAULT    3
     4: 0000000000000000     0 SECTION LOCAL  DEFAULT    4
     5: 0000000000000000     0 SECTION LOCAL  DEFAULT    5
     6: 0000000000000000     4 OBJECT  LOCAL  DEFAULT    4 static_var_uninit.2318
     7: 0000000000000004     4 OBJECT  LOCAL  DEFAULT    3 static_var.2317
     8: 0000000000000000     0 SECTION LOCAL  DEFAULT    7
     9: 0000000000000000     0 SECTION LOCAL  DEFAULT    8
    10: 0000000000000000     0 SECTION LOCAL  DEFAULT    9
    11: 0000000000000000     0 SECTION LOCAL  DEFAULT    6
    12: 0000000000000000     4 OBJECT  GLOBAL DEFAULT    3 global_init
    13: 0000000000000000     4 OBJECT  GLOBAL DEFAULT    5 global_const
    14: 0000000000000000    34 FUNC    GLOBAL DEFAULT    1 main
    15: 0000000000000000     0 NOTYPE  GLOBAL DEFAULT  UND _GLOBAL_OFFSET_TABLE_
    16: 0000000000000000     0 NOTYPE  GLOBAL DEFAULT  UND puts
```

Ndx means the idx in Nr

---
or use nm

`nm main.o`

``` text
0000000000000000 R global_const
0000000000000000 D global_init
                 U _GLOBAL_OFFSET_TABLE_
0000000000000000 T main
                 U puts
0000000000000004 d static_var.2317
0000000000000000 b static_var_uninit.2318
```

---
or use objdump

`objdump -tSw main.o`

``` text
main.o:     file format elf64-x86-64

SYMBOL TABLE:
0000000000000000 l    df *ABS* 0000000000000000 main.c
0000000000000000 l    d  .text 0000000000000000 .text
0000000000000000 l    d  .data 0000000000000000 .data
0000000000000000 l    d  .bss 0000000000000000 .bss
0000000000000000 l    d  .rodata 0000000000000000 .rodata
0000000000000000 l     O .bss 0000000000000004 static_var_uninit.2318
0000000000000004 l     O .data 0000000000000004 static_var.2317
0000000000000000 l    d  .note.GNU-stack 0000000000000000 .note.GNU-stack
0000000000000000 l    d  .note.gnu.property 0000000000000000 .note.gnu.property
0000000000000000 l    d  .eh_frame 0000000000000000 .eh_frame
0000000000000000 l    d  .comment 0000000000000000 .comment
0000000000000000 g     O .data 0000000000000004 global_init
0000000000000000 g     O .rodata 0000000000000004 global_const
0000000000000000 g     F .text 0000000000000022 main
0000000000000000         *UND* 0000000000000000 _GLOBAL_OFFSET_TABLE_
0000000000000000         *UND* 0000000000000000 puts



Disassembly of section .text:

0000000000000000 <main>:
   0: f3 0f 1e fa           endbr64
   4: 55                    push   %rbp
   5: 48 89 e5              mov    %rsp,%rbp
   8: 48 83 ec 10           sub    $0x10,%rsp
   c: c7 45 fc 44 44 44 44  movl   $0x44444444,-0x4(%rbp)
  13: 48 8d 3d 00 00 00 00  lea    0x0(%rip),%rdi        # 1a <main+0x1a>
  1a: e8 00 00 00 00        callq  1f <main+0x1f>
  1f: 90                    nop
  20: c9                    leaveq
  21: c3                    retq
```

`objdump -s -j .rodata main.o`

``` text
main.o:     file format elf64-x86-64

Contents of section .rodata:
 0000 22222222 68656c6c 6f20776f 726c6421  """"hello world!
 0010 00
```

## section

rpath: custom dynamic library path

`readelf -d build/Demo`

``` text
0x000000000000001d (RUNPATH)            Library runpath: [/home/heli/project/Demo/build/components/hello]
```

`objdump -x build/Demo | grep RUNPATH`

``` text
  RUNPATH              /home/heli/project/Demo/build/components/hello
```
