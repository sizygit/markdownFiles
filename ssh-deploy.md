---
title: ssh deploy
date: 2023-06-07 10:29:15
tags:
---

# 配置github的ssh密钥

可见[官方链接](https://docs.github.com/zh/authentication/connecting-to-github-with-ssh/checking-for-existing-ssh-keys)，及相关[ssh配置代理转发](https://docs.github.com/zh/authentication/connecting-to-github-with-ssh/using-ssh-agent-forwarding)

输入 `ls -al ~/.ssh` 以查看是否存在现有的 SSH 密钥。

```shell
$ ls -al ~/.ssh
# Lists the files in your .ssh directory, if they exist
```

检查目录列表以查看是否已经有 SSH 公钥。 默认情况下，GitHub 的一个支持的公钥的文件名是以下之一。

- id_rsa.pub
- id_ecdsa.pub
- id_ed25519.pub

1. 设置用户名与邮箱

```
git config --global user.name ‘zhandehuang’
git config --global user.email ‘it_zdh@163.com’
git config --list
```

2. 生成新的ssh密钥(选择不同的加密方式)

```shell
ssh-keygen -t ed25519 -C "your_email@example.com"
ssh-keygen -t rsa -C "it_zdh@163.com"
```

按照提示输入密钥文件名和密码，直接输入回车选择默认名称与无密码。

3. 后台启动ssh代理，并将生成密钥加入ssh-agent

```shell
# 启动ssh代理
$ eval "$(ssh-agent -s)"
> Agent pid 59566
# id_ed25519变为你生成的本地密钥文件名称，有时候需要重新输入该命令
$ ssh-add ~/.ssh/id_ed25519
```

4. 将ssh公钥添加到github仓库

```shell
# 将生成的公钥文件内容加入到/settings/SSH and GPG keys的SSH keys中
cat ~/.ssh/id_ed25519.pub
```

5. 测试连接

```shell
$ ssh -T git@github.com
> Hi USERNAME! You've successfully authenticated, but GitHub does not
> provide shell access.
```

6. 配置ssh代理转发

使用你喜欢的文本编辑器打开位于 `~/.ssh/config` 的文件。 如果此文件不存在，则可以通过在终端中输入 `touch ~/.ssh/config` 来创建它。在文件中输入以下文本，将 `*.github.com` 替换为服务器的域名或 IP：

```shell
Host *.github.com
  ForwardAgent yes
```

如果不确定是否在使用本地密钥，还可以检查服务器上的 `SSH_AUTH_SOCK` 变量：

```shell
$ echo "$SSH_AUTH_SOCK"
# Print out the SSH_AUTH_SOCK variable
> /tmp/ssh-4hNGMk8AZX/agent.79453
```
