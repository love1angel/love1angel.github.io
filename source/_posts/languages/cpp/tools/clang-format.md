---
title: clang-format tool usage
date: 2022-08-20 21:50:01
categories:
- languages
- cpp
- tools
tags:
- llvm
- done
---

clang-format tools cheat sheet, this tool aim at

- keep code style consistent
- auto check and format code

<!-- more -->

## docs

<https://clang.llvm.org/docs/ClangFormat.html>

## download

``` bash
# mac
brew install llvm
```

## usage

``` bash
# generate style file .clang-format, style can be [gnu, llvm, google, Mozilla, WebKit]
clang-format -style=WebKit -dump-config > .clang-format

# modify file directly
clang-format -i main.cpp

# modify git current staged changes
git clang-format

# modify most recently git commit
git clang-format HEAD~1

# in CI compare
clang-format --diff <before commit> <after commit>
```
