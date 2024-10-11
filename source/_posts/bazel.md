---
title: bazel
date: 2024-07-04 15:09:25
categories:
tags:
---

<!-- more -->

bazel run \<bazel options> <target> -- <executable options>

`bazel build :all` build 当前文件 BUILD.bazel 中的 target，`bazel build //proto:all`

## test

zsh 必须要加引号，bash 可以不加

./bazel-bin/hello/hello_test --gtest_filter="*2"
bazel run //hello:hello_test -- --gtest_filter="*2"

bazel test //hello:hello_test --test_arg=--gtest_filter="*2"

## clean

`bazel clean`:

`bazel clean --expunge`:

1. 外部代码 ( git override，http\_archieve 的)
    - 项目目录中的 external
    - ubuntu 中在 ~/.cache/\_bazel\_/<username>/\<build-hash\>/external 中
2. 生成代码，但不暴露出来
    - 在 bazel-out 中查找
3. 生成代码，但暴露出来
    - 在 bazel-bin 中查重

k8-fastbuild
k8-opt-exec-1E8F3B4A
