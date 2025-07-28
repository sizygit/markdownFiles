---
title: win10安装WSL2


---

# win10安装WSL2

## WSL2相关

​		`wsl`的官方文档点这里: [https://learn.microsoft.com/zh-cn/windows/wsl/](https://link.zhihu.com/?target=https%3A//learn.microsoft.com/zh-cn/windows/wsl/)，首先点击`应用与功能`->`程序与功能`->`启用或关闭Windows功能`，勾选打开`适用于Linux的Windows子系统功能`。

​		安装WSL2可直接参考[微软官方文档](https://learn.microsoft.com/zh-cn/windows/wsl/install#upgrade-version-from-wsl-1-to-wsl-2)，现在安装基本上均默认下载WSL2，如下是一些常见的WSL命令：

```bash
wsl --install
#安装 WSL 默认版本和默认 Linux 发行版。
wsl --list [--online]
# 列出已安装的 Linux 发行版，使用 --online 参数可显示可供安装的发行版列表。
wsl --set-default <DistributionName>
# 设置默认的 Linux 发行版。
wsl -u <UserName>
# 以指定的用户身份启动 WSL。
wsl --shutdown
# 立即终止所有运行的 WSL Linux 发行版并关闭虚拟机。
wsl --unregister <DistributionName>
# 注销并删除指定的 Linux 发行版, 同时会删除磁盘文件。
wsl --update
# 更新 WSL 的 Linux 内核。
wsl --status
# 显示 WSL 的状态以及默认的 WSL 版本和发行版。
wsl --help
# 显示所有可用的 WSL 命令和用法选项。
```

有时候会出现WSL2卡死，则需要手动关闭LxssManager服务并重启：

```bash
tasklist /svc /fi "imagename eq svchost.exe" | findstr Lxss
net start LxssManager
```

根据PID关闭相应进程并重启即可。

## WIN10安装步骤

### 打开WSL相关设及安装发布行

下载[WSL开源管理软件](https://github.com/bostrot/wsl2-distro-manager/releases)，解压后直接使用;下载windows终端，参考[官方链接](https://learn.microsoft.com/zh-cn/windows/terminal/install)。下载前注意需要检查windows系统版本，需保证win10系统在20H2以上（`winver`命令查看)。

首先我们需要进行wsl的更新，如果网络有问题，可以开启虚拟网卡TUN模式来让cmd走代理：

```bash
wsl --update --web-download
wsl --version
```

1. 按下`WIN+R`键入`appwiz.cpl`，进入`控制面板/程序/程序与功能`界面，候选**Hyper-V**、**虚拟机平台**、**WSL设置**，然后按照提示重启电脑使能修改。

2. 打开命令行将默认版本设置为WSL2：

```bash
wsl --set-default-version 2
```

3. 下载相应的WSL版本（命令行）

- 首先查看在线商店下载的可用 Linux 分发版的列表，

```bash
wsl -l -o
```

- 接着利用命令自动下载其中的版本（以ubuntu22.04为例）

```bash
wsl --install -d Ubuntu-22.04
```

4. 手动安装方式（该部分内容参考[官方链接](https://learn.microsoft.com/zh-cn/windows/wsl/install-manual)）

- 使用最新版本的 [WSL 2 Linux 内核](https://github.com/microsoft/WSL2-Linux-Kernel)，直接下载最新的包：[WSL2 Linux 内核更新包适用于 x64 计算机](https://wslstorestorage.blob.core.windows.net/wslblob/wsl_update_x64.msi)，运行下载的.msi更新包，并设置WSL2版本为2（参考上节）。
- 直接在微软商店中下载需要的Linux发行版，当网络不支持时，直接下载对应的包文件（如[Ubuntu 22.04 LTS](https://aka.ms/wslubuntu2204)）到本地路径。
- 使用 Add-AppxPackage 安装 Appx包

```bash
Add-AppxPackage .\app_name.appx
```

- 当打开分发版时会要求键入分发班的用户与密码，若需手动创建则运行如下命令：

```bash
sudo adduser accouts_mine
```

### 使用MobaXterm配置GUI

[参考链接](https://zhuanlan.zhihu.com/p/151853503),首先安装XFCE4桌面环境

```bash
sudo apt update 
# 仅升级关键包（避免非必要更新占用空间）
sudo apt --trivial-only upgrade -y
sudo apt install xfce4 xfce4-goodies -y
sudo apt install gedit -y
```

配置[防火墙](https://zhuanlan.zhihu.com/p/151853503)修改bashrc等操作

```
export DISPLAY=$(cat /etc/resolv.conf | grep nameserver | awk '{print $2}'):0
export WAYLAND_DISPLAY=$DISPLAY
```

注意修改MobaXterm设置`Settings-Configuration-X11-X11 remote access`为`full`，即可显示有界面的应用。最新版本的WSL2在[ Linux GUI 应用](https://learn.microsoft.com/zh-cn/windows/wsl/tutorials/gui-apps)方面有着诸多的支持（WSLG），基本上可以不连接远程桌面环境，直接在本地显示应用GUI。

### WSL2的卸载与迁移

WSL2 的虚拟硬盘文件（`.vhdx`）默认占用系统盘空间，默认路径为`C:\Users\<用户名>\AppData\Local\Packages\CanonicalGroupLimited...UbuntuonWindows_...`

- 导出当前发行版到`.tar`文件

```
wsl --export Ubuntu-22.04 D:\WSL\Ubuntu2204_backup.tar
```

- 注销旧版本，删除旧文件（应用中卸载）

```
wsl --unregister Ubuntu-22.04
```

- 导入到新位置，使用以下命令 `wsl --import <Distro> <InstallLocation> <FileName>`

```
wsl --import Ubuntu-22.04 D:\WSL\Ubuntu2204 D:\WSL\Ubuntu2204_backup.tar --version 2
```

### 修改相关配置文件

```bash
#C:\Users\<UserName>\.wslconfig
[wsl2]
memory=6GB               # 限制最大内存使用量
processors=4             # 指定 CPU 核心数
swap=8GB                 # 交换空间大小
swapFile=D:\WSL\swap.vhdx # 自定义 swap 文件路径
autoMemoryReclaim=true   # 自动回收未使用内存
localhostForwarding=true # 启用 localhost 端口转发
guiApplications=true     # 启用 WSLg（GUI 应用支持）
gpu=true                 # 启用 GPU 加速（需支持的 GPU 驱动）
dns=8.8.8.8              # 使用 Google DNS
networkingMode=mirrored  # 启用镜像网络模式（支持 IPv6）
firewall=true            # 启用 WSL 防火墙集成
autoProxy=true           # 自动使用 Windows 代理
nestedVirtualization=true # 启用嵌套虚拟化（适合运行 Docker 等）

#/etc/wsl.conf 
[boot]
systemd=true
[network]
generateHosts=true
generateResolvConf=false
[interop]
enabled=true
appendWindowsPath=true
[user]
default = thorn

#/etc/resolv.conf
nameserver 8.8.8.8  # 指定 Google 公共 DNS 服务器 (8.8.8.8)

# \etc\apt\sources.list
# 默认注释了源码镜像以提高 apt update 速度，如有需要可自行取消注释
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ jammy main restricted universe multiverse
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ jammy-updates main restricted universe multiverse
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ jammy-backports main restricted universe multiverse

deb https://mirrors.ustc.edu.cn/ubuntu/ focal main restricted universe multiverse
deb https://mirrors.ustc.edu.cn/ubuntu/ focal-updates main restricted universe multiverse
deb https://mirrors.ustc.edu.cn/ubuntu/ focal-backports main restricted universe multiverse
deb https://mirrors.ustc.edu.cn/ubuntu/ focal-security main restricted universe multiverse

deb http://security.ubuntu.com/ubuntu/ jammy-security main restricted universe multiverse
```

## WSL2安装ROS开发环境

### 安装vscode

只需要在本地vscode安装WSL插件及相关的ros插件即可，参考[官方链接](https://learn.microsoft.com/zh-cn/windows/wsl/tutorials/wsl-vscode)。WSL2可以利用WSLG直接在IDE中显示相关图像如（rqt等）。

### 配置git与ssh秘钥

git已默认安装，仅需要进行配置文件设置（命令行或者~/.gitconfig文件），建议使用ssh方式连接。

```bash
git config --global user.name "Your Name"
git config --global user.email "youremail@domain.com"
git config --list
```

安装ssh-server，生成秘钥并将公钥放入远程库设置中

```bash
sudo apt install openssh-server
cd .ssh
ssh-keygen
ssh -T git@github.com
```

### 安装ros

使用鱼香ROS指令按照ROS2

```bash
wget http://fishros.com/install -O fishros && . fishros
```

按照提示即可，测试使用小乌龟例程

```bash
ros2 run turtlesim turtlesim_node
ros2 run turtlesim turtle_teleop_key
```

