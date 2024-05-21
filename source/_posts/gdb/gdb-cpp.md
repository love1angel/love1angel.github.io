---
title: gdb-cpp
date: 2024-05-21 08:09:38
categories:
- gdb
tags:
---

gdb 调试 cpp 数据结构的特殊情形

- [x] std::string
- [x] std::vector

<!-- more -->

对于数据结构更好的调试信息

`info pretty-printer`

## std::string

``` bash
# 查看元素值
## gcc
(gdb) p (char *)str
(gdb) p (char)str[0]
```

## std::vector

```bash
# 查看元素值
## gcc
(gdb) p vec._M_impl._M_start[0]
```
