---
title: pmr
date: 2024-05-22 01:46:51
categories:
- languages
- cpp
- optimize
tags:
---

C++ 17 引入了内存相关的一系列可重写的 API。 在头文件 memory_resource 中。命名空间在 std::pmr:: ，目的在于

1. 池化内存分配，减少频繁系统调用分配或释放内存，带来的性能损失
2. 与已有 std 中的 allocator, container 结合，方便使用者自定义相关的内存分配策略类

<!-- more -->

标准库的主要结构如下

![uml](https://github.com/love1angel/love1angel.github.io/blob/hexo/source/_posts/languages/cpp/optimize/pmr_1.png?raw=true)

## 标准库实现的内存分配类

1. std::pmr::monotonic_buffer_resource
@love1angel
2. std::pmr::unsynchronized_pool_resource
@love1angel
3. std::pmr::synchronized_pool_resource
@love1angel

获取默认的内存分配策略类，不修改则是使用继承自 memory_resource 的类（clang++ 下为 __new_delete_memory_resource_imp），其中的方法最终就是调用了 new 和 delete 
std::pmr::get_default_resource()

设置默认的内存分配策略类
std::pmr::set_default_resource(memory_resource*)

获取默认的 new delete 内存分配策略，作为基准测试
std::pmr::new_delete_resource()

内部调用 allocate 会直接抛出异常 std::bad_alloc()，通常用来禁止向 upstream 索要内存，从而禁止从默认的内存分配类索取内存
std::pmr::null_memory_resource()

## std 库封装的接口

封装了 polymorphic_allocator 满足了 std 容器中需要满足的条件，如定义了 value_type，operaotr== 比较两个分配器是否相等。

封装了一些数据结构，如std::pmr::vector<T> 实际上是 std::list<T, std::pmr::polymorphic_allocator<T>>

这时候可以直接给他传入你的 buf, std::pmr::list{&buf, buf_len};

## 测试

计时函数

``` cpp
template <typename F, typename... Args>
void calc(F f, Args... args)
{
    auto before = std::chrono::steady_clock::now();
    f(args...);
    auto after = std::chrono::steady_clock::now();

    std::chrono::duration<double> duration = after - before;
    std::cout << duration << std::endl;
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
        std::pmr::monotonic_buffer_resource resouce { buf.data(), buf.size() };
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
