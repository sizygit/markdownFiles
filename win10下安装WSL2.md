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

## WIN10安装WSL(Ubuntu 22.04)

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

- [参考链接](https://zhuanlan.zhihu.com/p/151853503),倘若我们不使用WSLG,首先安装XFCE4桌面环境

```bash
sudo apt update 
# 仅升级关键包（避免非必要更新占用空间）
sudo apt --trivial-only upgrade -y
sudo apt install xfce4 xfce4-goodies -y
sudo apt install gedit -y
```

配置[防火墙](https://zhuanlan.zhihu.com/p/151853503)修改bashrc的DISPLAY参数以适配XFCE4桌面环境等操作

```
export DISPLAY=$(cat /etc/resolv.conf | grep nameserver | awk '{print $2}'):0 # 这个不行就用下一个
export DISPLAY=$(ip route show | grep default | awk '{print $3}'):0.0 # 或者wsl的ip
export WAYLAND_DISPLAY=$DISPLAY
```

注意修改MobaXterm设置`Settings-Configuration-X11-X11 remote access`为`full`，即可显示有界面的应用。

- 最新版本的WSL2（需要位于 **Windows 10 内部版本 19044+** 或 **Windows 11** ）在[ Linux GUI 应用](https://learn.microsoft.com/zh-cn/windows/wsl/tutorials/gui-apps)方面有着诸多的支持（WSLG），基本上可以不连接远程桌面环境，直接在本地显示应用GUI，这种情况可以不进行`DISPLAY`参数的设置，其默认为`:0`便可正常运行WSLG，因此两种方式有着一定的[冲突](https://allenkuo.medium.com/%E5%8D%87%E7%B4%9Awsl2%E5%88%B0%E5%B8%82%E9%9B%86%E7%89%88%E6%9C%AC%E8%88%87%E6%B3%A8%E6%84%8F%E4%BA%8B%E9%A0%85-e26ce1580e63)。

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

参考[WSL 中的高级设置配置](https://learn.microsoft.com/zh-cn/windows/wsl/wsl-config)，`.wslconfig` 进行全局配置的选项仅适用于在 Windows 版本 19041 及更高版本，`wsl.conf` 文件调整每个发行版设置的功能仅适用于 Windows 版本 17093 及更高版本，因此对于win10下列部分参数无用。修改配置需要运行 `wsl --shutdow`重启wsl2来使更改生效。

```bash
#C:\Users\<UserName>\.wslconfig  (*的条目仅适用Windows11,**需要更高版本)
[wsl2]
memory=6GB               # 限制最大内存使用量
processors=4             # 指定 CPU 核心数
swap=8GB                 # 交换空间大小
swapFile=D:\\WSL\\swap.vhdx # 自定义 swap 文件路径
localhostForwarding=true # 启用 localhost 端口转发，用于指定绑定到 WSL 2 VM 中的通配符或 localhost 的端口是否应可通过 localhost:port 从主机连接。
guiApplications=true     # 启用 WSLg（GUI 应用支持）
gpu=true                 # 启用 GPU 加速（需支持的 GPU 驱动）
networkingMode=mirrored  # **启用镜像网络模式（支持 IPv6）
firewall=true            # **启用 WSL 防火墙集成
autoProxy=true           # *自动使用 Windows 代理 
nestedVirtualization=true # 启用嵌套虚拟化（适合运行 Docker 等）
[experimental]
sparseVhd = false
autoMemoryReclaim = dropCache #	检测空闲CPU使用率后，自动释放缓存的内存。gradual以慢速释，dropCache为立即释放

#/etc/wsl.conf 
[boot]
systemd=true
[network]
generateHosts=true # true 将 WSL 设置为生成 /etc/hosts包含主机名与 IP 地址对应的静态映射。
generateResolvConf=false #true 将WSL设置为生成 /etc/resolv.conf包含一个 DNS 列表，能够将给定的主机名解析为其 IP 地址。
[interop]
enabled=true #设置此键可确定 WSL 是否支持启动 Windows 进程
appendWindowsPath=true #设置此键可确定 WSL 是否会将 Windows 路径元素添加到 $PATH 环境变量。
[user]
default = thorn
[gpu]
enabled= true #允许 Linux 应用程序通过准虚拟化访问 Windows GPU
[time]
useWindowsTimezone = true # 设置此密钥将使 WSL 使用 Windows 中设置的时区并与之同步

#/etc/resolv.conf  ?似乎没啥用？
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

### 释放空间

wsl磁盘只会自动扩容不会缩容，所以卸载的文件不会释放空间，一般用不上这种方式。除非内存十分紧张时，如下

```
# 关闭 WSL2 中的 linux distributions
wsl --shutdown
# 运行管理计算机的驱动器的 DiskPart 命令
diskpart
# 选择虚拟磁盘文件
select vdisk file="C:\Users\Administrator\AppData\Local\Packages\CanonicalGroupLimited.Ubuntu_79rhkp1fndgsc\LocalState\ext4.vhdx"
# 只读
attach vdisk readonly
# 压缩文件
compact vdisk
# 压缩完毕后卸载磁盘
detach vdisk
```

## WSL2安装ROS2+PX4开发环境

### 安装vscode

只需要在本地vscode安装WSL插件~~及相关的ros插件~~即可，参考[官方链接](https://learn.microsoft.com/zh-cn/windows/wsl/tutorials/wsl-vscode)。WSL2可以利用WSLG直接在IDE中显示相关图像如（rqt等）。

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

### 安装ros2

使用鱼香ROS指令按照ROS2

```bash
wget http://fishros.com/install -O fishros && . fishros
```

按照提示即可，测试使用小乌龟例程

```bash
ros2 run turtlesim turtlesim_node
ros2 run turtlesim turtle_teleop_key
```

## 安装PX4

安装**PX4开发环境**，参考官方链接https://docs.px4.io/main/zh/ros2/user_guide.html#humble以及[阿木](https://blog.csdn.net/msq19895070/article/details/149111214)，克隆太慢可以开启clash verge的TUN模式，尽量用外国梯子，克隆结束再调回来。（注意我们选择最新稳定版本1.15.4，1.16还在开发中）

```bash
cd
git clone https://github.com/PX4/PX4-Autopilot.git --recursive
git describe --tags  # 查看当前tag
git tag -l --sort=-taggerdate # 查看tag
git checkout -b v1.15.4-branch v1.15.4 # 根据1.15.4的tag建立新分支
git submodule update --init --recursive
bash ./PX4-Autopilot/Tools/setup/ubuntu.sh
cd PX4-Autopilot/
make px4_sitl
```

> 注意在make编译可能出现Protobuf版本问题，参考[网友](https://blog.gitcode.com/12276389c3d6afe286e683d6629504bd.html)得知此问题为wsl意外一入了win10的conda内的Protobuf版本，第一种解决办法是临时修改Anaconda文件名防止索引到，第二种是在/etc/wsl.conf文件中禁用Windows路径继承。

安装**ros2-humble**(前面已安装)，以及python依赖：

```bash
pip install --user -U empy==3.3.4 pyros-genmsg setuptools
```

安装**Micro XRCE-DDS Agent & Client**，官方教程给的2.4.2版本比较老，有网友安装代码版本为v3.0.1,目前测试对与于1.15.4和2.4.2可以兼容

```bash
git clone -b v2.4.2 https://github.com/eProsima/Micro-XRCE-DDS-Agent.git
cd Micro-XRCE-DDS-Agent
mkdir build
cd build
cmake ..
make
sudo make install
sudo ldconfig /usr/local/lib/
```

> 遇到2.12.x报错修改`Micro-XRCE-DDS-Agent/CMakeLists.txt`第99行为`set(_fastdds_version 2.13) set(_fastdds_tag v2.13.x)`来匹配DDS库的更新。

PX4模拟器会自动启动UXRCE-DDS客户端，并在本地主机上连接到UDP端口8888。在PX4 Autopilot Repo的根部打开一个新终端,运行如下命令打开一个PX4模拟器，并启动代理并设置以连接运行在模拟器上的 uXRCE-DDS客户端(Client)，由于ROS2没有MAVROS，因此只有开启了agent才可以发出话题

```bash
make px4_sitl gz_x500
MicroXRCEAgent udp4 -p 8888
```

安装**WSL下的QGC**,参考[官方链接](https://docs.px4.io/main/zh/dev_setup/dev_env_windows_wsl.html#qgroundcontrol-in-wsl)我们选在安装Windows下的QGC，然后建立通信连接到WSL，点击[QGroundControl on Windows](https://docs.qgroundcontrol.com/master/en/qgc-user-guide/getting_started/download_and_install.html#windows) 下载，并按照提示安装驱动；在WSL终端运行`ip addr | grep eth0` 得到`eth0` 接口 `inet` 地址的第一部分，在QGC **Application Settings > Comm Links**中添加新的UDP连接，选择port为`18570`,ServerAddresses添加为`刚查到的ip:18570`。

安装**官方的PX4示例ROS2软件包**，创建空的workspace，下载 [px4_ros_com](https://github.com/PX4/px4_ros_com) and [px4_msgs](https://github.com/PX4/px4_msgs) 包，然后利用colcon命令编译ROS2空间：

```bash
mkdir -p ~/ws_sensor_combined/src/
cd ~/ws_sensor_combined/src/
git clone https://github.com/PX4/px4_msgs.git
git clone https://github.com/PX4/px4_ros_com.git
cd ..
source /opt/ros/humble/setup.bash
colcon build
```

> 编译PX4_Msgs会出现`canonicalize_version()...` 的[关于setuptool版本的报错](https://github.com/pypa/setuptools/issues/4501)，只需要调整版本即可：

```bash
 pip install setuptools==64
 pip install sqlmap -U
```

**运行示例软件包**，实现PX4环境内无人机的数据监听：

```bash
# terminal 1 运行监听节点或者悬停节点
cd ~/ws_sensor_combined/ && source install/local_setup.bash
ros2 launch px4_ros_com sensor_combined_listener.launch.py
ros2 run px4_ros_com offboard_control
# terminal 2 运行PX4模拟器，在PX4根目录下执行
make px4_sitl gz_x500
# terminal 3 启动XRCE-DDS代理
MicroXRCEAgent udp4 -p 8888
```

此时，基本的PX4+gazebo仿真环境已搭建完毕。

