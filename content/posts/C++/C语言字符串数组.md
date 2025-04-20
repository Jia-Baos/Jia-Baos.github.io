+++
title = 'C语言字符串数组'
date = '2025-04-20T21:04:32+08:00'
categories = ["C/C++"]
tags = ["C/C++"]
# draft = true
+++

```C++
#include <bits/stdc++.h>

int main() {
    char str1[] = "abc";
    char str2[] = "abc";
    const char str3[] = "abc";
    const char str4[] = "abc";
    const char *str5 = "abc";
    const char *str6 = "abc";
    char *str7 = "abc";
    char *str8 = "abc";

    // 因地址向下增长，所以内存均开辟在栈上
    std::cout << "str1's Address: " << &str1 << std::endl;
    std::cout << "str2's Address: " << &str2 << std::endl;
    std::cout << "str3's Address: " << &str3 << std::endl;
    std::cout << "str4's Address: " << &str4 << std::endl;

    // 字符串变量比较不能直接用==，但是可以用变量地址和字符串用==比较，如果地址相同，字符串会相等
    std::cout << std::boolalpha << (str1 == str2) << std::endl;
    std::cout << std::boolalpha << (str3 == str4) << std::endl;

    // 字符串变量比较的正确方式
    if (strcmp(str1, str2) == 0) {
        std::cout << "str1 == str2" << std::endl;
    }

    // str5、str6、str7和str8指向常量存储区的同一块内存
    // 所以其相等
    std::cout << std::boolalpha << (str5 == str6) << std::endl;
    std::cout << std::boolalpha << (str7 == str8) << std::endl;
    std::cout << std::boolalpha << (str5 == str7) << std::endl;
    std::cout << std::boolalpha << (str6 == str8) << std::endl;
}
```