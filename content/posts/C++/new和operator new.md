+++
title = 'new 和 operator new'
date = '2025-04-20T21:04:32+08:00'
categories = ["C/C++"]
tags = ["C/C++"]
# draft = true
+++

## new

new是C++中的关键字，其行为总是一致的，先调用operator new分配内存，然后调用构造函数初始化那段内存；

## operator new

operator new是一个函数，就像重载任何一个符号如operator +，它用来分配内存（只不过new除了调用它还有其他步骤）。它可以被重载，通过重载它，可以改变new操作符的功能。它的功能介意类比c语言中的malloc，如果类中没有重载operator new，那么调用的就是全局的::operator new来从堆中分配内存。

如果想重载operator new需要注意以下几点：

- 重载时，返回类型必须声明为void*；
- 重载时，第一个参数类型必须为表达要求分配空间的大小（字节），类型为size_t；
- 重载时，可以带其它参数；
- 分配函数为类成员函数或全局函数，如果分配函数在全局范围之外的名称空间范围中声明，或者在全局范围中声明为静态，则程序是病态的；

## placement new

placement new 是c++中对operator new 的一个标准、全局的重载版本。它并不分配内存，只是返回指向已经分配好的某段内存的一个指针，placement new允许你在一个已经分配好的内存中（栈或者堆中）构造一个新的对象。

placement new的使用步骤如下：

1. 分配内存

```C++
char* buff = new char[ sizeof(Foo) * N ];
memset( buff, 0, sizeof(Foo)*N );
```

2. 构建对象

```C++
Foo* pfoo = new (buff)Foo;
```

3. 使用对象

```C++
pfoo->print();
pfoo->set_f(1.0f);
pfoo->get_f();
```

4. 析构对象，显式的调用类的析构函数。

```C++
pfoo->~Foo();
```

5. 销毁内存

```C++
delete [] buff;
```

```C++
#include <iostream>

// 重载全局的operator new
void* operator new(size_t size)
{
    std::cout << "::operator new, size = " << size << std::endl;
    return malloc(size);;
};

void operator delete(void* pdead, size_t size)
{
    std::cout << "::operator delete, pdead = " << pdead << " size = " << size << std::endl;
    free(pdead);
}

void* operator new[](size_t size)
{
    std::cout << "::operator new[], size = " << size << std::endl;
    return malloc(size);;
};

void operator delete[](void* pdead, size_t size)
{
    std::cout << "::operator delete[], pdead = " << pdead << " size = " << size << std::endl;
    free(pdead);
}

class Foo {
public:
    int id_;
    double data_;

public:
    Foo() : id_(0) { std::cout << "default Ctor.this = " << this << " id = " << id_ << std::endl; }

    Foo(int id) : id_(id) { std::cout << "Ctor.this = " << this << " id = " << id_ << std::endl; }

    virtual ~Foo() { std::cout << "Dtor.this = " << this << " id = " << id_ << std::endl; }

    static void *operator new(size_t size);

    static void operator delete(void *pdead, size_t size);

    static void *operator new[](size_t size);

    static void operator delete[](void *pdead, size_t size);
};

void *Foo::operator new(size_t size) {
    Foo *p = (Foo *) malloc(size);
    std::cout << "operator new, size = " << size << std::endl;
    return p;
}

void Foo::operator delete(void *pdead, size_t size) {
    std::cout << "operator delete, pdead = " << pdead << " size = " << size << std::endl;
    free(pdead);
}

void *Foo::operator new[](size_t size) {
    Foo *p = (Foo *) malloc(size);
    std::cout << "operator new[], size = " << size << std::endl;
    return p;
}

void Foo::operator delete[](void *pdead, size_t size) {
    std::cout << "operator delete[], pdead = " << pdead << " size = " << size << std::endl;
    free(pdead);
}

int main() {
    // if the ~Foo is virtual, sizeof(Foo) is 24
    // if the ~Foo is no virtual, sizeof(Foo) is 16
    std::cout << sizeof(Foo) << std::endl;

    Foo *p1 = new Foo(7);
    delete p1;

    Foo *pArray1 = new Foo[5];
    delete[] pArray1;

    puts("----------------------");

    // 因调用全局的 ::operator new，所以不执行类内的operator new
    Foo *p2 = ::new Foo(7);
    ::delete p2;

    Foo *pArray2 = ::new Foo[5];
    ::delete[] pArray2;
}
```

```C++
#include <iostream>

class Foo
{
public:
	int id_;
	double data_;

public:
	Foo() :id_(0) { std::cout << "default Ctor.this = " << this << " id = " << id_ << std::endl; }
	Foo(int id) :id_(id) { std::cout << "Ctor.this = " << this << " id = " << id_ << std::endl; }
	~Foo() { std::cout << "Dtor.this = " << this << " id = " << id_ << std::endl; }

	static void* operator new(size_t size, void* phead);
	static void operator delete(void*, void*) { puts("displacement operator delete"); };
	static void* operator new[](size_t size, void* phead);
	static void operator delete[](void*, void*) {puts("displacement operator delete[]"); };
};

void* Foo::operator new(size_t size, void* phead)
{
	puts("displacement new");
	return phead;
}

void* Foo::operator new[](size_t size, void* phead)
{
	puts("displacement new[]");
	return phead;
}

int main()
{
	// 分配内存
	char* buff = new char[5 * sizeof(Foo)];
	memset(buff, 0, 5 * sizeof(Foo));

	Foo* pFoo = new(buff)Foo;
	pFoo->~Foo();
	delete[] buff;
}

```