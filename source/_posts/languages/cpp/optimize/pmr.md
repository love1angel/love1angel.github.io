---
title: pmr
date: 2024-05-22 01:46:51
categories:
- languages
- cpp
- optimize
tags:
---

C++ 17 引入了内存相关的一系列可重写的 API。 在头文件 <memory_resource>。命名空间在 std::pmr:: , polymorphic allocator，目的在于

1. 池化内存分配，减少频繁系统调用分配或释放内存，导致性能损失
2. 与已有 std 中的 allocator, container 结合，方便使用者自定义相关的内存分配策略

<!-- more -->

标准库的主要大致结构如下
[](https://github.com/love1angel/love1angel.github.io/blob/hexo/source/_posts/languages/cpp/optimize/pmr_1.png)

## 标准库实现的内存分配类

1. std::pmr::monotonic_buffer_resource
2. std::pmr::unsynchronized_pool_resource
3. std::pmr::synchronized_pool_resource

std::pmr::get_default_resource()
std::pmr::set_default_resource()
std::pmr::new_delete_resource()
std::pmr::null_memory_resource()

## std 库封装的

std::pmr::vector<T> 实际上是 std::list<T, std::pmr::polymorphic_allocator<T>>

这时候可以直接给他传入你的 buf

## 测试

计时函数

``` cpp
template <typename T, typename... Args>
void calc(T&& name, Args&&... args)
{
    const auto before = std::chrono::steady_clock::now();
    name(args...);
    const auto after = std::chrono::steady_clock::now();

    std::chrono::duration<double> duration = after - before;

    std::cout << duration.count() << "s. " << std::endl;
}
```

优化 list

``` cpp
std::array<std::byte, 65536 * 24> buf;

int main()
{
    auto list = [] {
        std::list<int> list;
        for (int i { 0 }; i < 65536; ++i) {
            list.push_back(i);
        }
    };
    calc(list);

    auto pmr_list = [] {
        std::pmr::monotonic_buffer_resource resouce { buf.data(), 65536 * 24 };
        std::pmr::list<int> list { &resouce };
        for (int i { 0 }; i < 65536; ++i) {
            list.push_back(i);
        }
    };
    calc(pmr_list);

    auto vector = [] {
        std::vector<int> list;
        for (int i { 0 }; i < 65536; ++i) {
            list.push_back(i);
        }
    };
    calc(vector);

    return 0;
}
```

0.00803458s
0.00369133s
0.00206471s

## 注意点

如果用 set_default_resource 修改了默认的分配池，该 memory_resource 的生命期需要注意
