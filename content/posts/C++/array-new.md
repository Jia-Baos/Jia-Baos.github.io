+++
title = 'array-new'
date = '2025-04-20T21:04:32+08:00'
categories = ["C/C++"]
tags = ["C/C++"]
# draft = true
+++

```C++
#include <iostream>

class A
{
public:
	int id_;

	A() :id_(0) { std::cout << "default Ctor.this = " << this << " id = " << id_ << std::endl; };
	A(int id) :id_(id) { std::cout << "Ctor.this = " << this << " id = " << id_ << std::endl; };
	~A() { std::cout << "Dtor.this = " << this << " id = " << id_ << std::endl; };
};

int main()
{
	std::cout << _MSVC_LANG << std::endl;

	size_t size = 3;
	// default Ctor调用三次，[0]先于[1]先于[2]
	// A必须有 default Ctor，否则[Error] no matching function for call to 'A::A()'
	A* buf = new A[size];

	A* tmp = buf;
	std::cout << "buf=" << buf << " tmp=" << tmp << std::endl;

	for (size_t i = 0; i < 3; ++i)
	{
		// Ctor3次
		new(tmp++)A(i);
	}

	std::cout << "buf=" << buf << " tmp=" << tmp << std::endl;

	// Dtor3次（次序逆反），[2]先于[1]先于[0]
	// Dtor被唤起时，this指向不同的object
	delete[] buf;
}

```
