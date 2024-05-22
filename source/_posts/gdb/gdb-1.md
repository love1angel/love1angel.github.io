---
title: gdb debug 情形讨论一
date: 2024-05-21 07:59:59
categories:
- gdb
tags:
---

- 断在变量特殊值下
- 我的变量值被谁偷偷改了？

<!-- more -->

-O2 过度优化可能会导致 No symbol "i" in current context.

## 调试循环中特定变量的值

break point 要打到 brace 里面

```cpp
#include <iostream>

int main()
{
    for (int i { 0 }; i <= 10; ++i) {     // line 5
        std::cout << "test" << std::endl; // line 6
    }
    return 0;
}
```

condition <breakpoint number>

```cpp
(gdb) b test.cpp:6
Breakpoint 1 at 0x11b5: file test.cpp, line 5.
(gdb) condition 1 i == 5
```

等同于

```cpp
b test.cpp:6 if i == 5
```

---

example 2

```cpp
#include <iostream>

int main()
{
    int i = 0;

    while (i <= 10) { // line 7
        std::cout << "hello, world" << std::endl;
        ++i; // line 9
    }
    return 0;
}
```

```cpp
(gdb) b test.cpp:9 if i == 6
Breakpoint 1 at 0x555555555160: file test.cpp, line 9.
(gdb) r
Starting program: /home/qxz2lsp/cpp/test 
hello, world
hello, world
hello, world
hello, world
hello, world
hello, world

Breakpoint 1, main () at test.cpp:7
7           while (i <= 10) {
(gdb) p i
$1 = 6
```
