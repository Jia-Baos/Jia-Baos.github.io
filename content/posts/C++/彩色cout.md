+++
title = '彩色 cout'
date = '2025-04-20T21:04:32+08:00'
categories = ["C/C++"]
tags = ["C/C++"]
# draft = true
+++

```C++
#ifndef CONSOLE_COLOR_H
#define CONSOLE_COLOR_H

#include <iostream>
#include <windows.h>

inline std::ostream &red(std::ostream &s)
{
    HANDLE hStdout = GetStdHandle(STD_OUTPUT_HANDLE);
    SetConsoleTextAttribute(hStdout,
                            FOREGROUND_RED | FOREGROUND_INTENSITY);
    return s;
}

inline std::ostream &blue(std::ostream &s)
{
    HANDLE hStdout = GetStdHandle(STD_OUTPUT_HANDLE);
    SetConsoleTextAttribute(hStdout,
                            FOREGROUND_BLUE | FOREGROUND_GREEN | FOREGROUND_INTENSITY);
    return s;
}

inline std::ostream &green(std::ostream &s)
{
    HANDLE hStdout = GetStdHandle(STD_OUTPUT_HANDLE);
    SetConsoleTextAttribute(hStdout,
                            FOREGROUND_GREEN | FOREGROUND_INTENSITY);
    return s;
}

inline std::ostream &yellow(std::ostream &s)
{
    HANDLE hStdout = GetStdHandle(STD_OUTPUT_HANDLE);
    SetConsoleTextAttribute(hStdout,
                            FOREGROUND_GREEN | FOREGROUND_RED | FOREGROUND_INTENSITY);
    return s;
}

inline std::ostream &white(std::ostream &s)
{
    HANDLE hStdout = GetStdHandle(STD_OUTPUT_HANDLE);
    SetConsoleTextAttribute(hStdout,
                            FOREGROUND_RED | FOREGROUND_GREEN | FOREGROUND_BLUE);
    return s;
}

struct color
{
    WORD m_color;
    color(WORD attribute) : m_color(attribute){};
};

template <typename _Elem, typename _Traits>
std::basic_ostream<_Elem, _Traits> &
operator<<(std::basic_ostream<_Elem, _Traits> &i, const color &c)
{
    HANDLE hStdout = GetStdHandle(STD_OUTPUT_HANDLE);
    SetConsoleTextAttribute(hStdout, c.m_color);
    return i;
}

#endif
```

```C++
#include <bits/stdc++.h>
#include "console_color.hpp"

int main(int argc, char *argv[])
{
    std::cout << red << "Colored hello world for windows!" << std::endl;
    std::cout << blue << "Colored hello world for windows!" << std::endl;
    std::cout << green << "Colored hello world for windows!" << std::endl;
    system("pause");
}
```