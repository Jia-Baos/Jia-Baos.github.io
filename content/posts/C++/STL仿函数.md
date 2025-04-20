+++
title = 'STL仿函数'
date = '2025-04-20T21:04:32+08:00'
categories = ["C/C++"]
tags = ["C/C++"]
# draft = true
+++

函数对象（也称 函子）是实现 operator() 的任何类型。 此运算符被称为 调用运算符 （有时称为 应用程序运算符）。 C++ 标准库主要使用函数对象作为容器和算法内的排序条件。

相对于直接函数调用，函数对象有两个优势。 第一个是函数对象可包含状态。 第二个是函数对象是一个类型，因此可用作模板参数。

```C++
#include <bits/stdc++.h>

class Functor {
public:
    int operator()(int a, int b) {
        return a < b;
    }
};

int main() {
    std::cout << __cplusplus << std::endl;

    Functor f;
    int a = 5;
    int b = 7;
    std::cout << f(a, b) << std::endl;  // 1

    return 0;
}
```

函数指针。

不足：无法利用函数指针指向模板函数。

```C++
#include <iostream>

int cal(int val) {
	return val * val;
}

int main(int argc, char* argv[]) {
	// 函数指针定义方式
	// 函数返回值类型 (* 指针变量名) (函数参数列表)
	int (*ptr_func)(int) = nullptr;
	ptr_func = cal;

	int val1 = cal(5);  // 通过函数调用
	int val2 = (*ptr_func)(5);  // 通过指针调用，推荐
	int val3 = ptr_func(5); // 不推荐

	std::cout << "val1 " << val1 << std::endl;
	std::cout << "val2 " << val2 << std::endl;
	std::cout << "val3 " << val3 << std::endl;
}
```