---
title: std::move 到底做了什么？C++ 移动到底是什么？move 后的对象还可以使用吗？
date: 2024-05-02 08:35:01
categories:
- languages
- cpp
- realize
tags:
- cpp
---

最近几天听到了一些对 C++ 不太对的看法，如下：
1. move 后的对象不能用了
2. 因为 move 移动了 C++ 对象的所有权，所以原本的对象不能再使用了
3. 函数返回值 T 类型时候 return std::move(T {});

<!-- more -->

本文暂时不考虑 pod 类型，pod 类型的做法通常直接进行拷贝。所以基于 class T 类型进行讨论：

## move 的实现

强制转化成了 && 类型

## 移动函数

为什么要调用 std::move，因为需要你强制转为 && 类型，调用移动相关的函数。

但是请注意，C++ 20 前的定义是这样，这就意味着如果你不加上 move 函数，则会调用拷贝相关的函数。
然而 C++ 20 修改了标准，更符合直觉的调用


## 所有权是什么？

这是书的作者为了易于读者理解而引入的概念，本质 C++ 没有这个概念，强烈依赖于 class T 中移动函数的实现。

例如 C++ 标准库中的大部分类类型，都支持 move 后的对象赋值后重新使用。
这和 Rust 语言有本质的区别，可以认为这是 C++ 的一种设计哲学，为了减少系统调用分配内存的消耗。
