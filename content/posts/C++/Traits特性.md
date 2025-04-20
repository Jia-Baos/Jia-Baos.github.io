+++
title = 'Traits特性'
date = '2025-04-20T21:04:32+08:00'
categories = ["C/C++"]
tags = ["C/C++"]
# draft = true
+++

Think of a trait as a small object whose main purpose is to carry information used by another object or algorithm to determine “policy” or “implementation details”. - Bjarne Stroustrup

模板的全特化、偏特化。

```C++
#include <bits/stdc++.h>

// 模板一般化设计
/*
* 模板全特化和偏特化设计版本存在时，
* 优点调用
*/
template <typename T>
struct MyPrint
{
    void print()
    {
        std::cout << "模板一般化设计" << std::endl;
    }
};

// 模板全特化设计
template <>
struct MyPrint<float>
{
    void print()
    {
        std::cout << "模板全特化设计" << std::endl;
    }
};

// 模板偏特化设计
/*
 *模板特化时，可以只指定一部分而非所有模板参数，
 *或者是参数的一部分而非全部特性，这叫做模板的偏特化。
 *一个类模板的偏特化本身是一个模板，
 *使用它时用户还必须为那些在特例化版本中未指定的模板参数提供实参。
 */
template <typename T>
struct MyPrint<T *>
{
    void print()
    {
        std::cout << "模板偏特化设计" << std::endl;
    }
};

int main(int argc, char *argv[])
{
    MyPrint<int> a;
    MyPrint<float> b;
    MyPrint<float *> c;

    a.print();
    b.print();
    c.print();

    return 0;
}
```

Traits...

Traits，又被叫做特性萃取技术，说得简单点就是提取“被传进的对象”对应的返回类型，让同一个接口实现对应的功能。因为STL的算法和容器是分离的，两者通过迭代器链接。算法的实现并不知道自己被传进来什么。萃取器相当于在接口和实现之间加一层封装，来隐藏一些细节并协助调用合适的方法，这需要一些技巧（例如，偏特化）。

```C++
#include <bits/stdc++.h>

struct Base
{
};

struct Derived
{
};

// 承接各种非内置类型
template <typename non_built_in_type>
struct unknown_class
{
    // 通过 unknown_class::return_type 来获取
    // unknown_type 的类型
    using return_type = non_built_in_type;
};

// 特性萃取器，模板一般化设计
template <typename type>
struct type_traits
{
    // type_traits<type>::return_type 等价于
    // unknown_type<unknown_type>::return_type 等价于
    // unknown_type
    using return_type = typename type::return_type;
};

// 特性萃取器，模板偏特化设计，针对原始指针
template <typename type>
struct type_traits<type *>
{
    using return_type = type;
};

// 特性萃取器，模板偏特化设计，针对常量指针
template <typename type>
struct type_traits<const type *>
{
    using return_type = const type;
};

template <typename unknown_class>
inline typename type_traits<unknown_class>::return_type
__func(unknown_class, Base)
{
    std::cout << "Class Base..." << std::endl;
    return Base();
}

template <typename unknown_class>
inline typename type_traits<unknown_class>::return_type
__func(unknown_class, Derived)
{
    std::cout << "Class Derived..." << std::endl;
    return Derived();
}

template <typename unknown_class, typename T>
T __func(unknown_class, T)
{
    std::cout << "Origin ptr..." << std::endl;
    return T();
}

template <typename unknown_type>
inline typename type_traits<unknown_type>::return_type
func(unknown_type type)
{
    // 这里的 type 为待萃取对象
    // 对 unknown_class 萃取获得非内置类型
    // 对原始指针和常量指针萃取获得原始类型
    using return_type = typename type_traits<unknown_type>::return_type;
    return __func(type, return_type());
}

int main(int argc, char *argv[])
{
    unknown_class<Base> base;
    unknown_class<Derived> derived;

    int val = 1;
    int *val_ptr = &val;

    Base v1 = func(base);
    Derived v2 = func(derived);
    int v3 = func(val_ptr);

    return 0;
}
```