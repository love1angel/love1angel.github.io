---
title: C++ 中 RVO 和 NRVO 优化是什么？
date: 2024-05-02 08:39:59
categories:
- languages
- cpp
- optimize
tags:
- cpp
---

本文主要介绍 RVO 和 NRVO 在 C++ 的使用，为了解决了什么历史遗留问题？如何配合写出高效率的代码？

<!-- more -->

@love1angel

众所周知，RVO 和 NRVO 是在 C++ 11 之前编译器就提供的一种优化方式。并在 C++ 17 写入标准，作为编译器默认优化方式。

RVO 全称 return value optimization 返回值优化，NRVO 全称 named return value optimization 具名对象返回值优化。
旨在当函数返回值为 T 类型时候，优化拷贝的消耗或者减少调用构造函数和拷贝构造函数的次数，NRVO 特指具名对象的 RVO 优化方式。
