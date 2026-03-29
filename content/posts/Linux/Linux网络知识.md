+++
title = 'Linux常用命令'
date = '2025-09-04T13:26:47+08:00'
categories = ["Linux"]
tags = ["Linux"]
# draft = true
+++

## ```Linux```常用命令

- 文件加减权限
```
sudo chmod -R 777 [file]

// u    用户user，表现文件或目录的所有者
// g    用户组group，表现文件或目录所属的用户组
// o    其他用户other
// a    所有用户all

// +    添加权限
// -    减少权限
// =    直接给定一个权限

// r    读
// w    写
// x    运行

sudo chmod -R[u,g,o,a][+,-,=][r,w,x] [file]
```

- 查找文件
```
sudo find /usr/lib/ -name "file-name*"
```

- 动态库问题，如果使用 cp 命令拷贝动态库目录，默认会复制源文件而非软链接本身，导致链接断裂，所以应当使用下面的命令来拷贝：

```

# -a 保留所有属性（包括软链接），直接使用次命令即可
# -d 仅拷贝链接本身，不拷贝源文件
# -R 递归拷贝文件
cp -a 或 cp -d 源路径 目标路径
```

- 查看文件内容
```
cat main.cpp | grep main
strings main.cpp | grep main
```

- 创建软链接

```
# ln -s 源地址 目标地址
ln -s /usr/lib/file1 /usr/lib/file2
```


- 将动态库压缩
```
tar -zcvf nvidia.tar.gz nvidia/
```

- 库依赖关系查询
```
# 执行重定位和报告任何丢失的对象
ldd -d [name]

# 执行数据对象和函数的重定位，并且报告任何丢失的对象和函数
ldd -r [name]

# 查询当前库的依赖库中哪些没有被找到
ldd -d [name] | grep not

// for aarch64
readelf -d [name]
objdump -p [name]
```

- 查询C++编译后的函数名
```
C++filt 符号链接
```

- 编译信息输出到文件
```
make -j4 >build.log 2>&1
```

- 查看进程
```
# 显示所有进程信息
ps -A

# 查找指定进程格式
ps -ef | grep [name]
```

## 其他常用命令
- ssh连接电脑

```
# 查询电脑无线ip
ifconfig    # ubuntu
ipconfig    # windows

# ssh连接
ssh usrname@192.168.xxx.xxx
```

- 查看电脑硬盘内存

```
# 快捷指令
df -h

# 不一定可以用
sudo fdisk -l
```

- ```aarch64```下查询依赖
```
aarch64-linux-gnu-readelf -d ./libpercep_infer.so | grep NEEDED
```

- ```scp```上传文件到远程
```
scp ./libTest.so xxx@192.168.192.5:/home/xxx

// 本地文件传输到远程服务器
scp /path/to/local/file username@remote_host:/path/to/remote/directory

// 远程服务器文件传输到本地
scp username@remote_host:/path/to/remote/file /path/to/local/director
```

- ```Git```拉取指定分支并拉取```submodule```
```
# 拉取指定分支并拉起submodle
git clone --recurse-submodules  -b linux-x86 https://github.com/Jia-Baos/CmakeCpp.git
```

- 查看显卡型号
```
lspci | grep -i vga
```

- ```Docker```命令
```
docker create    # 创建容器
docker run    # 创建并启动容器
docker ps    # 查看当前运行的容器
docker ps -a    # 查看所有容器，包括停止的
exit    # 容器退出
docker stop [NAME]/[CONTAINER ID]    # 将容器退出
docker kill [NAME]/[CONTAINER ID]    # 强制停止一个容器
docker container start [NAME]    # 启动长期容器
docker attach [NAME]    # 进入一个正在运行的容器
xhost +    # 使所有用户都能够访问X server（用于关联本机和容器的文件）
docker exec -it [docker name] bash    # 在启动的docker中执行命令
```

## 串口调试相关

1. 安装串口调试工具
```
sudo apt-get install cutecom
```

2. 打开串口文件，如果提示没有权限，则执行下面的代码
```
sudo chmod 777 /dev -R
```

3. 通过串口调试工具 ```cutecom``` 发送数据
4. 通过命令行向串口发送数据 ```sudo echo 123 > /dev/tty*```
5. 在设备中执行 ```sudo cat /dev/tty*```，如果消息发送成功，则可以看到数据