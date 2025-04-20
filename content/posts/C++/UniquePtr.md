+++
title = 'unique ptr'
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
#include <concepts>

// 为UniquePtr提供删除器
template <typename Type>
struct DefaultDeleter
{ // 默认使用 delete 释放内存
    void operator()(Type *p) const
    {
        delete p;
    }
};

template <typename Type>
struct DefaultDeleter<Type[]>
{ // 偏特化
    void operator()(Type *p) const
    {
        delete[] p;
    }
};

template <>
struct DefaultDeleter<FILE>
{ // 全特化
    void operator()(FILE *p) const
    {
        fclose(p);
    }
};

template <typename T, typename U>
T exchange(T &obj, U &&new_val)
{ // 同标准库的 std::exchange
    T tmp = std::move(obj);
    obj = std::forward<U>(new_val);
    return tmp;
}

template <typename T, typename Deleter = DefaultDeleter<T>>
struct UniquePtr
{
private:
    T *m_p;

    template <typename U, typename UDeleter>
    friend struct UniquePtr;

public:
    UniquePtr(std::nullptr_t dummy = nullptr)
    { // 默认构造函数
        m_p = nullptr;
    }

    explicit UniquePtr(T *p)
    { // 自定义构造函数
        m_p = p;
    }

    // template <typename U, typename UDeleter, typename = std::enable_if_t<std::is_convertible_v<U *, T *>>> // 没有 C++20 的写法
    template <typename U, typename UDeleter>
        requires(std::convertible_to<U *, T *>) // 有 C++20 的写法
    UniquePtr(UniquePtr<U, UDeleter> &&that)
    { // 从子类型U的智能指针转换到T类型的智能指针
        m_p = exchange(that.m_p, nullptr);
    }

    ~UniquePtr()
    { // 析构函数
        if (m_p)
        {
            Deleter{}(m_p);
        }
    }

    UniquePtr(UniquePtr const &that) = delete;            // 拷贝构造函数
    UniquePtr &operator=(UniquePtr const &that) = delete; // 拷贝赋值函数

    UniquePtr(UniquePtr &&that)
    { // 移动构造函数
        m_p = exchange(that.m_p, nullptr);
    }

    UniquePtr &operator=(UniquePtr &&that)
    { // 移动赋值函数
        if (this != &that) [[likely]]
        {
            if (m_p)
            {
                Deleter{}(m_p);
            }

            m_p = exchange(that.m_p, nullptr);
        }
        return *this;
    }

    T *get() const
    {
        return m_p;
    }

    T *release()
    {
        return exchange(m_p, nullptr);
    }

    void reset(T *p = nullptr)
    {
        if (m_p)
        {
            Deleter{}(m_p);
        }

        m_p = p;
    }

    T &operator*() const
    {
        return *m_p;
    }

    T *operator->() const
    {
        return m_p;
    }
};

template <typename T, typename Deleter>
struct UniquePtr<T[], Deleter> : UniquePtr<T, Deleter>
{
};

template <typename T, typename... Args>
UniquePtr<T> makeUnique(Args &&...args)
{
    return UniquePtr<T>(new T(std::forward<Args>(args)...));
}

template <typename T>
UniquePtr<T> makeUniqueForOverwrite()
{
    return UniquePtr<T>(new T);
}

#endif // !FUNC_H
```

```C++
#include <iostream>
#include <vector>

#include "./Func.hpp"

struct Animal
{
    virtual void speak() = 0;
    virtual ~Animal() = default;
};

struct Dog : Animal
{
    int age;

    Dog(int age_) : age(age_)
    {
    }

    virtual void speak()
    {
        printf("Bark! I'm %d Year Old!\n", age);
    }
};

struct Cat : Animal
{
    int &age;

    Cat(int &age_) : age(age_)
    {
    }

    virtual void speak()
    {
        printf("Meow! I'm %d Year Old!\n", age);
    }
};

struct Test
{
    Test() { std::cout << 12 << std::endl; }
    void operator()(int val) const
    {
        std::cout << "Hello " << val << std::endl;
    }
};

int main(int argc, char *argv[])
{
    Test{}(3);

    std::vector<UniquePtr<Animal>> zoo;
    int age = 3;
    zoo.push_back(makeUnique<Cat>(age));
    zoo.push_back(makeUnique<Dog>(age));
    for (auto const &a : zoo)
    {
        a->speak();
    }
    age++;
    for (auto const &a : zoo)
    {
        a->speak();
    }

    // Output
    // 12
    // Hello 3
    // Meow! I'm 3 Year Old!
    // Bark! I'm 3 Year Old!
    // Meow! I'm 4 Year Old!
    // Bark! I'm 3 Year Old!

    return 0;
}
```