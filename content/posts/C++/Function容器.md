+++
title = 'Function容器'
date = '2025-04-20T21:04:32+08:00'
categories = ["C/C++"]
tags = ["C/C++"]
# draft = true
+++

注意：

原作者：双笙子佯谬

源代码链接： [std::function容器实现](https://github.com/parallel101/stl1weekend)

```C++
#ifndef FUNC_H
#define FUNC_H

#include <iostream>
#include <utility>
#include <stdexcept>
#include <memory>
#include <type_traits>
#include <functional>

template <typename FuncSign>
struct Func
{
    // 只在使用了不符合 Ret(Args...) 模式的 FuncSign 时出错
    static_assert(!std::is_same_v<FuncSign, FuncSign>, "not a valid function signature");
};

template <typename Ret, typename... Args>
struct Func<Ret(Args...)>
{
private:
    struct FuncBase
    {
        virtual Ret call(Args... args) = 0; // 纯虚函数，类型擦除后的统一接口
        virtual ~FuncBase() = default;      // 应对F可能有非平凡析构的情况
    };

    template <typename F>
    struct FuncImpl : FuncBase
    { // FuncImpl 会被实例化多次，每个不同的仿函数类都产生一次实例化
        F m_f;

        FuncImpl(F f) : m_f(std::move(f)) {}

        virtual Ret call(Args... args) override
        {
            // 完美转发所有参数给构造时保存的仿函数对象
            return m_f(std::forward<Args>(args)...);
            // 更规范的写法其实是：
            // return std::invoke(m_f, std::forward<Args>(args)...);
            // 但为了照顾初学者依然采用朴素的调用方法
        }
    };

    std::shared_ptr<FuncBase> m_base; // 使用智能指针管理仿函数对象，用shared而不是unique是为了让Func支持拷贝

public:
    Func() = default; // m_base 初始化为 nullptr

    // 此处 enable_if_t 的作用：阻止 Func 从不可调用的对象中初始化
    template <typename F, typename = std::enable_if_t<std::is_invocable_r_v<Ret, F &, Args...>>>
    Func(F f) // 没有 explicit，允许 lambda 表达式隐式转换成 Func
        : m_base(std::make_shared<FuncImpl<F>>(std::move(f)))
    {
    }

    Ret operator()(Args... args) const
    {
        if (!m_base) [[unlikely]]
        {
            throw std::runtime_error("function not initialized");
        }
        // 完美转发所有参数，这样即使 Args 中具有引用，也能不产生额外的拷贝开销
        return m_base->call(std::forward<Args>(args)...);
    }
};
#endif // !FUNC_H
```

```C++
#include "./Func.hpp"

void print_hello(int i)
{
    std::cout << "#" << i << " Hello" << std::endl;
}

struct print_nums
{
    int x;
    int y;
    void operator()(int i) const
    {
        std::cout << "#" << i << " Numbers are: " << x << " " << y << std::endl;
    }
};

void print_func(const Func<void(int)> &func)
{
    func(1);
}

int main(int argc, char *argv[])
{
    print_func(print_hello);

    Func<void(int)> func_tmp = print_hello;
    func_tmp(1);

    int x = 2;
    int y = 4;

    print_func([=](int i)
               { std::cout << "#" << i << " Numbers are: " << x << " " << y << std::endl; });

    print_nums print_nums_tmp{x, y};
    print_func(print_nums_tmp);

    // 输出
    // #1 Hello
    // #1 Hello
    // #1 Numbers are: 2 4
    // #1 Numbers are: 2 4

    return 0;
}
```