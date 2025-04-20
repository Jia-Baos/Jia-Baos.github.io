+++
title = 'C++语言数组'
date = '2025-04-20T21:04:32+08:00'
categories = ["C/C++"]
tags = ["C/C++"]
# draft = true
+++

仅在以下3种情况中，数组不会退化成指针
- 使用`sizeof(a)`的时候；
- 对数组名取地址操作：`&a`。`&a`的类型为pointer to array of 6 chars；
- 使用字符串初始化数组的时候也不会退化。

其它情况都会退化成“指向数组首元素的指针”
- 数组名做函数参数时，a都会退化成&a[0]指针；
- 二维数组，char s[10][8]数组的首元素（得理解这个首元素，从一维数组的角度看的）是一维数组s[0]，因此退化成&s[0]指针。

数组定义字符串和指针定义字符串的区别
- 使用数字定义的字符串只分配字符串所需的空间，数字名没有单独的存储空间；使用指针定义字符串，除了字符串占用的空间，还要为指针变量分配对应的空间。
- 数组退化成指针为“指针常量”，不可更改，而直接指向字符串的指针可修改。

```C++
#include <iostream>

int mysize(int arr[] )
{
	return sizeof(arr);
}

int main(int argc, char* argv[])
{
	int arr1[10]{ 0,1,2,3,4,5,6,7,8,9 };
	using type1 = decltype(arr1);	// int [10]
	using type2 = decltype(arr1[0]);	// int &
	using type3 = decltype(&arr1[0]);	// int *

	int* ptr1 = arr1;
	int* ptr2 = &arr1[0];

	puts("----------------");
	std::cout << sizeof(arr1) << std::endl;	// 40
	std::cout << mysize(arr1) << std::endl;	// 8
	std::cout << sizeof(ptr1) << std::endl;	// 8
	std::cout << sizeof(ptr2) << std::endl;	// 8

	puts("----------------");
	std::cout << *ptr1++ << std::endl;	// 0
	std::cout << *ptr1 << std::endl;	// 1
	std::cout << *ptr2++ << std::endl;	// 0
	std::cout << *ptr2 << std::endl;	// 1
	

	puts("----------------");
	type1 arr2 = { 1,2,3 };	// 
	for (rsize_t i = 0; i < 10; i++)
	{
		std::cout << arr2[i] << std::endl;
	}

	return 0;
}
```

二维数组

- 二维数组本质上是一位数组，它的每个元素都是一维数组；
- 当数组名单独出现时，一般指首元素的地址；
- 在C语言中，数组与指针有着密不可分的关系：对于二维数组，它的数组名单独出现指一个二级指针的值

```C++
#include <iostream>

int main(int argc, char* argv[])
{
	int a[3][4]{ {1, 2, 3, 4}, {5, 6, 7, 8}, {9, 10, 11, 12} };

	int val1 = a[0][2 * 1];
	int val2 = a[1][3];
	int val3 = a[4 - 2][0];
	int val4 = a[0][2 + 2];

	std::cout << val1 << std::endl;	// 3
	std::cout << val2 << std::endl;	// 8
	std::cout << val3 << std::endl;	// 9
	std::cout << val4 << std::endl;	// 5

	using type1 = decltype(a);	// int [3][4]
	using type2 = decltype(*a);	// int (&)[4]
	using type3 = decltype(a[0]);	// int (&)[4]
	using type4 = decltype(*a[0]);	// int &
	using type5 = decltype(&a[0]);	// int (*)[4]
	using type6 = decltype(a[0][0]);	// int &
	using type7 = decltype(&a[0][0]);	// int *

	std::cout << *a[0] << std::endl;	// 1
}
```