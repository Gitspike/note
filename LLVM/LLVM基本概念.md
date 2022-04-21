# 安装

（该方法仅适用于Ubuntu系统）

由于容器里只有ubuntu内核，要安装需要的工具,在容器下执行以下命令

```bash
apt-get install wget
apt install lsb-release wget software-properties-common
```

之后安装Clang和LLVM

```bash
apt-get clang
apt-get LLVM
```

使用官方脚本

```bash
wget https://apt.llvm.org/llvm.sh
chmod +x llvm.sh
sudo ./llvm.sh 13
```

最后把老师的例子拷进来，就可以编译运行