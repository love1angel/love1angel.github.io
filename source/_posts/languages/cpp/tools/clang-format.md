---
title: clang-format
date: 2022-08-20 21:50:01
categories:
- languages
- cpp
- tools
tags:
---

本文主要记录代码格式化工具：clang-format 的使用。

<!-- more -->

## 官网链接

https://clang.llvm.org/docs/ClangFormat.html

## 使用

``` bash
# 生成 .clang-format 配置文件 [gnu, llvm, google, Mozilla, WebKit]
clang-format -style=WebKit -dump-config > .clang-format

# 加入 -i 参数，直接修改文件
clang-format -i main.cpp

# 修改当前 staged change
git-clang-format-10

# 修改准备提交的 commit
git-clang-format-10 HEAD~1

# 没看到有啥用
git diff -U0 --color=never | clang-format-diff-10 -i -p1
```
