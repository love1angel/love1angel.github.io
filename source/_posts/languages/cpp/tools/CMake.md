---
title: CMake
date: 2022-04-13 14:09:27
categories:
- languages
- cpp
- tools
tags:
---

cmake cheatsheet

<!-- more -->

[](https://cmake.org/install)

[](https://cmake.org/cmake/help/latest/guide/tutorial/index.html)

## 命令行指定

set(CMAKE_C_COMPILER /usr/bin/clang CACHE PATH "" FORCE)
set(CMAKE_CXX_COMPILER /usr/bin/clang++ CACHE PATH "" FORCE)

cmake -S . -B build -DCMAKE_CXX_COMPILER=/opt/homebrew/opt/llvm/bin/clang++ -DCMAKE_CXX_FLAGS="-I/usr/local/bin -I/opt/homebrew/opt/llvm/include" -DCMAKE_EXE_LINKER_FLAGS="-L/opt/homebrew/opt/llvm/lib -lpthread"
