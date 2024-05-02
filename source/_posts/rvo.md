---
title: C++ rvo 和 nrvo 到底是什么？
date: 2024-05-02 08:39:59
tags:
---

众所周知，rvo 和 nrvo 是在 C++ 11 之前就提供的一种优化方式。并在 C++ 17 写入标准，作为编译器默认优化方式。

rvo 全称 return value optimization 返回值优化，nrvo 全称 named return value optimization 具名对象返回值优化。
旨在当函数返回值为 T 类型时候，优化拷贝的消耗或者减少调用构造函数和拷贝函数的次数，nrvo 特指有名字对象的优化方式。
