---
title: GTest
date: 2022-04-11 16:49:46
categories:
tags:
---

# example
https://cmake.org/cmake/help/latest/module/FetchContent.html

https://cmake.org/cmake/help/git-stage/module/GoogleTest.html

xUnit architecture
[International Software Testing Qualifications Board](http://www.istqb.org/)

ctest 是 命令
```
$ cmake -S . -B build
$ cmake --build build
$ cd build && ctest
```

1. Test

Google 以前用来代表 test case 的术语

2. Test Case

TEST() 代表 test case, Exercise a particular program path with specific input values and verify the results

3. Test Suite

Google 以前吧关联的测试用例叫 test case, 现在逐步采用 suite API refactor，deprecate 老的 API，A test suite contains one or many tests.

# assertion 三种结果，成功，非致命性错误(nonfatal failure)，致命性错误(fatal failure)，本质是函数

测试崩溃或 expect 错误会失败

ASSERT_* 会产生致命性错误，中止当前的函数往下走，EXPECT_* 则不会产生致命性错误，还会继续走，一般使用 expect 来发现更多错误，没有意义往下走使用 assert

assert 会直接返回，所以不执行 clean-up 代码，会导致泄漏，如果发生 heap check error 的时候，要考虑是否是 assert 导致的
Since a failed ASSERT_* returns from the current function immediately, possibly skipping clean-up code that comes after it, it may cause a space leak. Depending on the nature of the leak, it may or may not be worth fixing - so keep this in mind if you get a heap checker error in addition to assertion errors.

自定义 log ostream，会被自动转为 UTF-8 编码

ASSERT_EQ(x.size(), y.size()) << "Vectors x and y are of unequal length";

TEST(TestSuiteName, TestName) {
  ... test body ...
}
有问题就会失败

测试全名是 suite + test，两个名字不应该加下划线，全名是 identifier，相同的第一个名字应该相同

