+++
title = '异常中 new 对象'
date = '2025-04-20T21:04:32+08:00'
categories = ["C/C++"]
tags = ["C/C++"]
# draft = true
+++

MSVC下可通过编译，GCC报错。

```C++
#include <iostream>
#include <exception>

struct MyException : std::exception {
	MyException(const char* s) : exception(s) { puts("MyException()..."); }

	~MyException() { puts("~MyException()..."); }
};

void f1() try {
	throw MyException("MyException...");
}
// 与其他临时对象不同，异常对象在初始化 catch 子句形参时被认为是左值，
// 所以它可以用左值引用捕获、修改及重抛。
catch (std::exception& e) {
	std::cerr << e.what() << std::endl;
}

void f2() try
{
	throw new MyException("new MyException...");
}
catch (std::exception* e)
{
	std::cerr << e->what() << std::endl;
	delete e;
}

int main(int argc, char* argv[]) {
	f1();
	puts("----------------");
	f2();
}
```