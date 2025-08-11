+++
title = 'uv-包管理工具的使用'
date = '2025-08-11T15:05:47+08:00'
categories = ["Rust"]
tags = ["Rust"]
# draft = true
+++

## 使用 uv 管理 Python 项目

### 初始化项目和创建虚拟环境

```
# --seed 在创建虚拟环境时，预装基础依赖包；-p 指定python版本
uv venv --seed -p 3.11

# ubuntu
source .venv/bin/activate

# windows
.venv\Scripts\activate
```

### 执行 Python 脚本

```
# 激活环境后运行
python your_script.py

# When used with a file ending in .py or an HTTP(S) URL,
# the file will be treated as a script and run with a Python interpreter,
# i.e., uv run file.py is equivalent to uv run python file.py.

uv run your_script.py

uv run python your_script.py
```

### 安装依赖包

```
# 安装最新版本的 aiohttp
uv pip install aiohttp

# 安装特定版本的 aiohttp
uv pip install aiohttp==3.9.1

# 同时安装多个包
uv pip install fastapi uvicorn

# 从 requirements.txt 文件安装
uv pip install -r requirements.txt

# 将依赖同步到 requirements.txt
uv pip freeze > requirements.txt

# 列出当前环境中已安装的所有包
uv pip list

# 显示特定包的详细信息（版本、依赖等）
uv pip show package_name

# 以开发模式安装当前目录下的项目
# 适用于开发自己的包时，修改代码后无需重新安装
uv pip install -e .
```

### 更新依赖包

```
# 更新单个包
uv pip compile --upgrade-package fastapi

# 更新所有包
uv pip compile --upgrade

# 更新到最新的兼容版本
uv pip compile --upgrade-compatible

# 生成带哈希值的依赖文件以确保安全性
uv pip compile --generate-hashes requirements.in -o requirements-hashes.txt

```

### 卸载依赖包

```
# 卸载 aiohttp
uv pip uninstall aiohttp
```

## 使用 pyproject.toml 进行现代化管理

对于新项目，更推荐使用 ```pyproject.toml``` 来管理依赖。```uv``` 提供了类似 ```Poetry``` 的工作流。

### 初始化项目

这将创建一个基础的 pyproject.toml 文件。

```
uv init
```

### 添加依赖包

使用 ```uv add``` 命令可以添加依赖，并自动将其写入 ```pyproject.toml```。

```
# 添加一个生产依赖
uv add requests

# 添加一个开发依赖 (例如 ruff)
uv add --dev ruff
```


### 同步依赖

当 ```pyproject.toml``` 文件发生变化时（例如手动编辑或 ```git pull``` 更新后），使用 ```uv sync``` 命令来安装或移除包，使其与文件中声明的依赖保持一致。

```
uv sync
```

## 参考链接

[uv主页](https://docs.astral.sh/uv/)

[拥抱未来：下一代 Python 打包工具 uv 使用指南](https://www.cnblogs.com/wgb1234/p/18939881#:~:text=%E6%9C%AC%E6%96%87%E5%B0%86%E4%BD%9C%E4%B8%BA%E4%B8%80%E4%BB%BD%E5%85%A8%E9%9D%A2%E7%9A%84%E4%BD%BF%E7%94%A8%E8%AF%B4%E6%98%8E%EF%BC%8C%E5%B8%A6%E4%BD%A0%E6%B7%B1%E5%85%A5%E4%BA%86%E8%A7%A3%20uv%EF%BC%8C%E4%BB%8E%E5%AE%89%E8%A3%85%E5%88%B0%E6%97%A5%E5%B8%B8%E4%BD%BF%E7%94%A8%EF%BC%8C%E5%B9%B6%E9%98%90%E8%BF%B0%E4%B8%BA%E4%BD%95%E5%AE%83%E5%8F%AF%E8%83%BD%E6%88%90%E4%B8%BA%E4%BD%A0%E4%B8%8B%E4%B8%80%E4%B8%AA%20Python%20%E9%A1%B9%E7%9B%AE%E7%9A%84%E9%A6%96%E9%80%89%E3%80%82%20%E4%BB%80%E4%B9%88%E6%98%AF%20uv%EF%BC%9F%20%E4%B8%BA%E4%BD%95%E9%80%89%E6%8B%A9%E5%AE%83%EF%BC%9F%20uv,%E5%8C%85%E5%92%8C%E9%A1%B9%E7%9B%AE%E7%AE%A1%E7%90%86%E5%99%A8%E3%80%82%20%E5%AE%83%E7%94%B1%20Ruff%20%EF%BC%88%E4%B8%80%E4%B8%AA%E5%B9%BF%E5%8F%97%E6%AC%A2%E8%BF%8E%E7%9A%84%20Python%20Linter%EF%BC%89%E7%9A%84%E5%88%9B%E5%BB%BA%E8%80%85%20Astral%20%E5%85%AC%E5%8F%B8%E5%BC%80%E5%8F%91%E3%80%82)

[UV 包管理器使用指南](https://blog.niuhemoon.win/posts/tech/uv-guide/)
