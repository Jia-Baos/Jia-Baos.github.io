+++
title = 'Conan 使用示例'
date = '2026-08-30T13:26:47+08:00'
categories = ["Conan"]
tags = ["Conan"]
# draft = true
+++

# 使用说明

## tutorial/consuming_packages/simple_cmake_project

```BASH
# uv tool install 安装 conan，路径：~/.local/share/uv/tools/conan
uv tool list --show-paths
```

```BASH
# 强制重新生成并覆盖 Conan 的配置文件，路径：~/.conan2/profiles
conan profile detect --force
```

```BASH
# 下载/缓存依赖：将 zlib/1.3.1 从远程仓库（conancenter）下载并存入本地 ~/.conan2/ 缓存。
# 生成工具链：在 build 文件夹生成了 conan_toolchain.cmake，确保 CMake 能找到 Conan 缓存的库。
# 生成环境脚本：生成了 conanbuild.sh，以便后续在终端加载环境变量。
conan install . --output-folder=build --build=missing
```

```BASH
# 配置 CMake 项目，使用 Conan 生成的工具链。
cmake -B build -DCMAKE_TOOLCHAIN_FILE=conan_toolchain.cmake -DCMAKE_BUILD_TYPE=Release
cmake --build build

# 使用 CMake 预设配置，自动应用工具链和构建类型。
cmake --preset conan-release
cmake --build build
```

## tutorial/consuming_packages/different_configurations

```BASH
# 下载/缓存依赖：将 zlib/1.3.1 从远程仓库（conancenter）下载并存入本地 ~/.conan2/ 缓存。
# 生成工具链：在 build 文件夹生成了 conan_toolchain.cmake，确保 CMake 能找到 Conan 缓存的库。
# 生成环境脚本：生成了 conanbuild.sh，以便后续在终端加载环境变量。
# 使用默认配置（default）安装依赖。
conan install . --output-folder=build --build=missing --profile=default
```

```BASH
# 下载/缓存依赖：将 zlib/1.3.1 从远程仓库（conancenter）下载并存入本地 ~/.conan2/ 缓存。
# 生成工具链：在 build 文件夹生成了 conan_toolchain.cmake，确保 CMake 能找到 Conan 缓存的库。
# 生成环境脚本：生成了 conanbuild.sh，以便后续在终端加载环境变量。
# 临时使用 Debug 配置安装依赖。
conan install . --output-folder=build --build=missing --settings=build_type=Debug
```

```BASH
# 下载/缓存依赖：将 zlib/1.3.1 从远程仓库（conancenter）下载并存入本地 ~/.conan2/ 缓存。
# 生成工具链：在 build 文件夹生成了 conan_toolchain.cmake，确保 CMake 能找到 Conan 缓存的库。
# 生成环境脚本：生成了 conanbuild.sh，以便后续在终端加载环境变量。
# 使用 shared=True 选项安装共享库版本的 zlib。
conan install . --output-folder=build --build=missing --options=zlib/1.3.1:shared=True
```

```BASH
# 配置 CMake 项目，使用 Conan 生成的工具链。
cmake -B build -DCMAKE_TOOLCHAIN_FILE=conan_toolchain.cmake -DCMAKE_BUILD_TYPE=Debug
cmake --build build

# 使用 CMake 预设配置，自动应用工具链和构建类型。
cmake --preset conan-debug
cmake --build build
```

## tutorial/consuming_packages/conanfile_py

```BASH
# 下载/缓存依赖：将 zlib/1.3.1 从远程仓库（conancenter）下载并存入本地 ~/.conan2/ 缓存。
# 生成工具链：在 build 文件夹生成了 conan_toolchain.cmake，确保 CMake 能找到 Conan 缓存的库。
# 生成环境脚本：生成了 conanbuild.sh，以便后续在终端加载环境变量。
conan install . --output-folder=build --build=missing
```

```BASH
# 加载环境变量：使用 source 命令加载 conanbuild.sh，以便在当前终端会话中使用 Conan 生成的工具链。
source ./build/conanbuild.sh
```

```BASH
# 配置 CMake 项目，使用 Conan 生成的工具链。
cmake -B build -DCMAKE_TOOLCHAIN_FILE=conan_toolchain.cmake -DCMAKE_BUILD_TYPE=Release
cmake --build build

# 使用 CMake 预设配置，自动应用工具链和构建类型。
cmake --preset conan-release
cmake --build build
```

##  tutorial/consuming_packages/cross_building

```BASH
# 交叉编译：使用不同的主机和构建配置，在 Conan 中指定 profile 来进行交叉编译。
# 下面两者等价
conan install . --build=missing --profile=someprofile
conan install . --build=missing --profile:host=someprofile --profile:build=default
```

```BASH
# 交叉编译：使用不同的主机和构建配置，在 Conan 中指定 profile 来进行交叉编译。路径：~/.conan2/profiles
[settings]
os=Linux
arch=armv8
compiler=gcc
build_type=Release
compiler.cppstd=gnu17
compiler.libcxx=libstdc++11
compiler.version=13
[buildenv]
CC=aarch64-linux-gnu-gcc
CXX=aarch64-linux-gnu-g++
LD=aarch64-linux-gnu-ld
```

```BASH
# 交叉编译：使用不同的主机和构建配置，在 Conan 中指定 profile 来进行交叉编译。
conan install . --build missing -pr:b=default -pr:h=aarch64

# 加载环境变量：使用 source 命令加载 conanbuild.sh，以便在当前终端会话中使用 Conan 生成的工具链。
source ./build/Release/generators/conanbuild.sh

# 配置 CMake 项目，使用 Conan 生成的工具链。
cmake -B build -DCMAKE_TOOLCHAIN_FILE=build/Release/generators/conan_toolchain.cmake -DCMAKE_BUILD_TYPE=Release
cmake --build build

# 使用 CMake 预设配置，自动应用工具链和构建类型。
cmake --preset conan-release
cmake --build build/Release
```
