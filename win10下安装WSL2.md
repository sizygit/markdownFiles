---
title: win10安装WSL2


---

# win10安装WSL2

## 安装WSL2

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

手动下载 [ubuntu 20.04分发版](https://aka.ms/wslubuntu2204)，
