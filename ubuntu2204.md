---
title: ubuntu2204
date: 2026-01-25 12:55:59
tags:
---

# ubuntu 22.04安装PX4环境

## 双系统安装

直接参考链接[windows11安装ubuntu22.04双系统教程](https://zhuanlan.zhihu.com/p/644425528)

## 需要的软件

### 更新软件源、基本软件安装

- 更新软件源(可以手动修改`/etc/apt/sources.list`文件，也可以在Software Updater内设置软件源，推荐前者)，并在Software Updater/Settings/Additional Drivers选择一个NVIDIA的驱动。
- 安装[搜狗拼音](https://shurufa.sogou.com/linux/guide)，[clash verge](https://github.com/clash-verge-rev/clash-verge-rev/releases/download/v2.3.1/Clash.Verge_2.3.1_amd64.deb), [vscode](https://code.visualstudio.com/Download), chorme，typora，docky，wechat, gparted

typora, spark-store, docky, gparted，

## 主题美化

第一步，安装gnome的桌面环境等插件，下载好后出现Tweaks与Extensions两个软件

```bash
sudo apt install gnome-tweaks gnome-shell-extensions -y
```

第二步，在浏览器安装GNOME Shell integration插件，在https://extensions.gnome.org/搜索User Themes插件并安装，然后便可以在https://www.gnome-look.org/browse/寻找喜欢的主题并下载。

第三步，在用户主目录下创建**.themes**（GTK 应用程序主题和 shell/桌面主题）与**.icons**（图标集）文件夹，然后将下载好的安装包放入文件夹后，即可在Tweaks中选择对应的主题(如[orchi主题](https://www.pling.com/p/1357889/))。

第四步，同时我个人还喜欢安装**docky**配合ubuntu的dock使用，安装方法如下：

```bash
cd ~/Downloads/docky

wget -c http://archive.ubuntu.com/ubuntu/pool/universe/g/gnome-sharp2/libgconf2.0-cil_2.24.2-4_all.deb
wget -c http://archive.ubuntu.com/ubuntu/pool/main/g/glibc/multiarch-support_2.27-3ubuntu1_amd64.deb
wget -c http://archive.ubuntu.com/ubuntu/pool/universe/libg/libgnome-keyring/libgnome-keyring-common_3.12.0-1build1_all.deb
wget -c http://archive.ubuntu.com/ubuntu/pool/universe/libg/libgnome-keyring/libgnome-keyring0_3.12.0-1build1_amd64.deb
wget -c http://archive.ubuntu.com/ubuntu/pool/universe/g/gnome-keyring-sharp/libgnome-keyring1.0-cil_1.0.0-5_amd64.deb

sudo apt-get install ./*.deb
wget -c http://archive.ubuntu.com/ubuntu/pool/universe/d/docky/docky_2.2.1.1-1_all.deb
sudo apt-get install ./docky_2.2.1.1-1_all.deb
```

也可以安装[dash-to-dock](https://extensions.gnome.org/extension/307/dash-to-dock/)与[hide-top-bar](https://extensions.gnome.org/extension/545/hide-top-bar/)插件来优化dock。

## ROS2+PX4_1.16.0

### 安装ros2+Vscode+git

ubuntu下安装ros2使用鱼香ROS2的一键脚本即可，git可直接使用pat安装即可：

```bash
wget http://fishros.com/install -O fishros && . fishros # 安装ros2的脚本
sudo apt install git
```

VsCode直接使用[官网链接](https://code.visualstudio.com/Download)下载即可；

### 安装PX4 1.16.0稳定版本

PX4当前稳定的固件版本为V1.16.0(2025.8.7)，因此我们参考[user_guide](https://docs.px4.io/v1.16/en/ros2/user_guide)，从[仓库](https://github.com/PX4/PX4-Autopilot)下载源码：

```bash
git clone https://github.com/PX4/PX4-Autopilot.git
cd PX4-Autopilot/
git describe --tags  # 查看当前tag
git checkout -b rcir/custom-v1.16.0 v1.16.0 # 根据1.16.0的tag建立新分支rcir/custom-v1.16.0
git submodule update --init --recursive
bash ./Tools/setup/ubuntu.sh  # 这个脚本可以看到一些依赖的版本信息
make px4_sitl # 编译这个sitl
```

---

安装一些相关的 Python 依赖项（使用 **`pip`** 或者 **`apt`**）：

```bash
pip install --user -U empy==3.3.4 pyros-genmsg setuptools # 官网命令,可能出现版本问题
pip install --user -U empy==3.3.4 pyros-genmsg # 不装setuptools 
```

> 注意可能出现setuptools版本冲突，colcon-core需要 setuptools<80，但 pip 试图安装 80.9.0 版本；可以pip install --user "setuptools<80"来进行回退，但是后面还是会出现setuptools工具的版本问题，因此直接删除了用户的安装pip uninstall setuptools -y，使用使用系统自带的版本59.6.0。

---

安装Micro XRCE-DDS代理和客户端， [ uXRCE-DDS 客户端 ](https://docs.px4.io/v1.16/en/modules/modules_system#uxrce-dds-client)必须在 PX4 上运行，并连接到在配套计算机上运行的微型 XRCE-DDS 代理，源代码构建“独立”代理程序如下：

```bash
git clone -b v2.4.2 https://github.com/eProsima/Micro-XRCE-DDS-Agent.git # 官方指定版本
git clone https://github.com/eProsima/Micro-XRCE-DDS-Agent.git # 使用v3.0.1测试无问题
cd Micro-XRCE-DDS-Agent
mkdir build
cd build
cmake ..
make
sudo make install
sudo ldconfig /usr/local/lib/
MicroXRCEAgent udp4 -p 8888 # 使用连接到模拟器上运行的 uXRCE-DDS 客户端的设置启动代理
```

---

PX4模拟器会自动启动UXRCE-DDS客户端，并在本地主机上连接到UDP端口8888。在PX4 Autopilot  Repo的根部打开一个新终端,运行如下命令打开一个PX4模拟器，并启动代理并设置以连接运行在模拟器上的  uXRCE-DDS客户端(Client)，由于ROS2没有MAVROS（实际上有mavros2），因此只有开启了agent才可以发出话题。

```bash
make px4_sitl gz_x500
MicroXRCEAgent udp4 -p 8888 # 此时可以看到相关的话题
```

---

参考[qgc-user-guide](https://docs.qgroundcontrol.com/master/zh/qgc-user-guide/getting_started/download_and_install.html)下载ubuntu下的QGC，执行相关命令并下载下载 [QGroundControl-x86_64.AppImage ](https://d176tv9ibo4jno.cloudfront.net/latest/QGroundControl-x86_64.AppImage)。Ubuntu 自带一个串口调制解调器管理器，它会干扰串口（或USB 转串口）在任何与机器人相关方面的使用。 在安装 *QGroundControl* 之前，您应该删除调制解调器管理器并授予自己访问串行端口的权限。 您还需要安装_GStreamer_以支持视频流。 

```bash
sudo usermod -aG dialout "$(id -un)" # 启用串口访问
sudo apt install gstreamer1.0-plugins-bad gstreamer1.0-libav gstreamer1.0-gl -y
sudo apt install libfuse2 -y
sudo apt install libxcb-xinerama0 libxkbcommon-x11-0 libxcb-cursor-dev -y
chmod +x QGroundControl.AppImage
```

---

进行完整的一个offboard例程的测试，首先克隆 [px4_msgs](https://github.com/PX4/px4_msgs)与 [px4_ros_com ](https://github.com/PX4/px4_ros_com)这两个仓库，然后编译其中的ros代码：

```bash
mkdir -p ~/ws_uav/src/
cd ~/ws_uav/src/
git clone https://github.com/PX4/px4_msgs.git
git clone https://github.com/PX4/px4_ros_com.git
cd ..
source /opt/ros/humble/setup.bash
colcon build
```

接着就可以进行完整的offboard控制流程了：我们只需要打开（1）QGC地面站（2）运行sitl仿真实例（3）启动XRCE-DDS代理（4）运行ros2节点

```bash
./QGroundControl-x86_64.AppImage
make px4_sitl gz_x500
MicroXRCEAgent udp4 -p 8888
ros2 run px4_ros_com offboard_control
```



## 常见问题

#### 1.关机重启等1min30s

打开`/etc/systemd/system.conf`并修改参数

```bash
sudo gedit /etc/systemd/system.conf
DefaultTimeoutStopSec=90s # 将其改为1s
sudo systemctl daemon-reload #应用修改
```

#### 2.重启桌面环境

有时候GNOME桌面可能会卡死，参考https://ask.csdn.net/questions/8491730可以重启GNOME Shell

```bash
gnome-shell --replace &
```

还可以，按下 `Alt + F2`，然后输入 `r`并按回车。这会使 GNOME Shell 重新启动。

#### 3.ROS2在zsh下无法自动补全

1. 首先检查`python3-argcomplete`插件是否安装，

```bash
sudo apt install python3-argcomplete
```

2. 在文件/opt/ros/${ROS-VERSION}/share/rosidl_cli/environment/rosidl-argcomplete.zsh中，注释掉下面内容：

```bash
# autoload -U +X compinit && compinit
```

3. 在文件~/.zshrc中添加下面内容：

```bash
eval "$(register-python-argcomplete3 colcon)"
```

