+++
title = '莫用 tuple 作为函数返回类型'
date = '2025-04-20T21:04:32+08:00'
categories = ["C/C++"]
tags = ["C/C++"]
# draft = true
+++

## 莫用 std::tuple 作为函数返回类型

假设我们要返回性别、年龄、名字，可以利用 `std::tuple`，但是我们无法知道我们获取值的属性。

```C++
#include <bits/stdc++.h>

auto func() -> std::tuple<std::string, std::string, int> {
    return {"Losser", "male", 101};
}

int main() {
    auto result = func();
    std::cout << "Name: " << std::get<0>(result)
              << ", Gender: " << std::get<1>(result)
              << ", Age: " << std::get<2>(result) << std::endl;
}
```

优化版本一（使用枚举）：

```C++
#include <bits/stdc++.h>

enum people{
    Name,
    Gender,
    Age
};

auto func() -> std::tuple<std::string, std::string, int> {
    return {"Losser", "male", 101};
}

int main() {
    auto result = func();
    std::cout << "Name: " << std::get<people::Name>(result)
              << ", Gender: " << std::get<people::Gender>(result)
              << ", Age: " << std::get<people::Age>(result) << std::endl;
}
```

优化版本二（使用 `std::tie`）：

```C++
#include <bits/stdc++.h>

auto func() -> std::tuple<std::string, std::string, int> {
    return {"Losser", "male", 101};
}

int main() {
    int age{};
    std::string name{};
    std::string gender{};
    std::tie(name, gender, age) = func();
    std::cout << "Name: " << name
              << ", Gender: " << gender
              << ", Age: " << age << std::endl;
}
```

优化版本三（结构化绑定）：

```c++
#include <bits/stdc++.h>

auto func() -> std::tuple<std::string, std::string, int> {
    return {"Losser", "male", 101};
}

int main() {
    auto[name, gender, age] = func();
    std::cout << "Name: " << name
              << ", Gender: " << gender
              << ", Age: " << age << std::endl;
}
```

优化版本四（结构体）：

```C++
#include <bits/stdc++.h>

struct people {
    std::string name;
    std::string gender;
    int age;
};


auto func() -> people {
    return {"Losser", "male", 101};
}

int main() {
    auto result = func();
    std::cout << "Name: " << result.name
              << ", Gender: " << result.gender
              << ", Age: " << result.age << std::endl;
}
```
