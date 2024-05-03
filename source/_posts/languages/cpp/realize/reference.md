---
title: C++ 左值引用和右值引用的原理
date: 2024-05-02 08:07:09
categories:
- languages
- cpp
- realize
tags:
- cpp
---

不少 C++ 书上对于引用的描述是这样的：

> 引用为对象起了一个别名，当操作引用时候就像在操作引用绑定的对象。

这似乎将引用说的很高深莫测，然而引用不过是一种 C++ 相较于 C 语言的语法糖。本文简单介绍一下引用（左值引用）和右值引用的原理

<!-- more -->

让我们先来回顾一下 C 语言是如何传递操作对象的，C 语言在函数中要么就是指针传地址，要么就是浅拷贝。但是一般都是传指针，原因如下

1.  直接传递一般比较耗时，C 编译器不会强制执行 RVO 这种优化
2.  很容易犯错的一个 bug 是在拷贝后以后会修改形参并不会影响到实参。

C++ 引用的的出现，为我们解决的就是上面两个问题，是 C++ 相比于 C 提供的语言层面的语法糖。那么引用（左值引用）和 C++11 新出的右值引用的原理是什么呢？

先说结论：`引用和右值引用本质上都是指针，区别在于引用指向的可访问地址的对象，右值引用则指向匿名对象的地址`。

## 让我们进入 compiler explorer 从汇编的角度来进行理解

C++ 左值引用，右值引用 x86_64 gcc 12.2

``` Assembly
int square(int num) {
// 函数序言
square(int):
        push    rbp
        mov     rbp, rsp
        mov     DWORD PTR [rbp-84], edi

    int a = 100;
				mov     DWORD PTR [rbp-68], 100

    int *p = &a;
				lea     rax, [rbp-68]
        mov     QWORD PTR [rbp-8], rax

    int *p2 = nullptr;
				mov     QWORD PTR [rbp-16], 0

    int *p3;
// none

    int &ref = a;
				lea     rax, [rbp-68]
        mov     QWORD PTR [rbp-24], rax

    int b = ref;
				mov     rax, QWORD PTR [rbp-24]
        mov     eax, DWORD PTR [rax]
        mov     DWORD PTR [rbp-28], eax

    ref = 20;
				mov     rax, QWORD PTR [rbp-24]
        mov     DWORD PTR [rax], 20

		*p = 20;
				mov     rax, QWORD PTR [rbp-8]
        mov     DWORD PTR [rax], 20

    int c = a;
				mov     eax, DWORD PTR [rbp-68]
        mov     DWORD PTR [rbp-32], eax
    int d = *p;
				mov     rax, QWORD PTR [rbp-8]
        mov     eax, DWORD PTR [rax]
        mov     DWORD PTR [rbp-36], eax


    int &&r1 = 3;
// 右值和左值一样也是指针地址，右值先做了拷贝，指向栈上 xvalue
				mov     DWORD PTR [rbp-72], 3
        lea     rax, [rbp-72]
        mov     QWORD PTR [rbp-48], rax
    int &&r2 = 10;
				mov     DWORD PTR [rbp-76], 10
        lea     rax, [rbp-76]
        mov     QWORD PTR [rbp-56], rax

// 取地址，拿值，进行拷贝
    r2 = r1;
				mov     rax, QWORD PTR [rbp-48]
        mov     edx, DWORD PTR [rax]
        mov     rax, QWORD PTR [rbp-56]
        mov     DWORD PTR [rax], edx
// 左值可以直接取值，进行拷贝
// 但右值引用无法用左值初始化，左值是直接拿值，那是bind的是右值，自相矛盾
// 否则所有权无法判断，因为右值要先取地址，相同的地址
    r2 = a;
				mov     edx, DWORD PTR [rbp-68]
        mov     rax, QWORD PTR [rbp-56]
        mov     DWORD PTR [rax], edx

// 直接拷贝了地址
    int &&r3 = static_cast<int &&>(r1);
				mov     rax, QWORD PTR [rbp-48]
        mov     QWORD PTR [rbp-64], rax

// 总结：左值和右值，对于指针在汇编看来没有区别，但是指针不强制要求存地址，左右值强制要求存地址
// 右值会拷贝一次 xvalue，move 算法更像是改变的是编译器的限制
    return 0;
				mov     eax, 0
        pop     rbp
        ret
}
```