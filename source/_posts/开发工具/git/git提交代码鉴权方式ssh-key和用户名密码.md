---
title: Git鉴权方式SSH key和用户名密码
tags:
  - Git
url: 488.html
id: 488
categories:
  - 其他
  - 软件工具
date: 2017-12-25 17:32:44
---

Git的鉴权方式分为 ssh 和 https，ssh 需要使用 ssh 秘钥，而https需要使用用户名和密码。有时候会每次提交都需要输入用户名和密码，下面介绍相关的Windows平台Git鉴权操作。

## 概述
使用Git之前需要安装 Git-bash 并且把它加到环境变量，git-bash[下载地址](https://git-scm.com/downloads)。

如果使用的是 git-desktop，安装的时候会自带 git-bash，需要配置到环境变量，它的地址一般在：

C:\Users\用户名\AppData\Local\GitHub\PortableGit，

在GitHub文件夹下搜索 git.exe 可以快速找到位置。一般是Portable_XXX文件夹下的cmd目录下面，有 git.exe, git-gui.exe。

在 git-bash 终端，使用 cd 命令可以跳转目录，注意这儿跳转目录的格式为：
```bash
$ cd /g/Projects/sdk_plugin
```
其中盘符为 G ,对于交互界面，可以直接拖动文件夹到 git-bash 中，会自动转成相应的文件夹路径。

对于某个项目，git clone 之后，在当前项目会拉取源代码，注意默认隐藏的 .git 文件夹，该文件夹下面有 config 文件，里面有关于该项目的配置。

另外需要配置项目的用户名和邮箱，这会用作提交代码的用户名和邮箱记录。
```bash
$ git config --global user.name "uusama"
$ git config --global user.email "uusama@uusama.com"
```

## 关于鉴权方式
git clone 的地址有下面两种

- https: https://github.com/用户名/GitProject.git 
- ssh: git@github.com:用户名/GitProject.git 

如果是 gitlab 内网服务端，这对应的 https 开头的地址或者 git 开头的地址。

第一种方式为 http 方式，需要提供用户名和密码，第二种方式是 ssh 方式，需要配置 ssh 的 RSA 秘钥。

可以通过修改项目的git配置文件： .git/config 中的url部分，从而改变鉴权方式。
```bash
[remote "origin"]
	url = https://github.com/youyouzh/admin.git
	fetch = +refs/heads/*:refs/remotes/origin/*
```

## 生成Git SSH秘钥
对于 ssh 方式，如果没有设置秘钥，向远程分支 git push 的时候就会被拒绝。本地 git commit 不影响，照常可以使用。

打开命令行 cmd，输入下面的命令开始生产 ssh 的 rsa key:
```bash
ssh-keygen -t rsa -C "用户邮箱"

# 如下面，如果没有-C选项，则使用系统的当前登录名
ssh-keygen -t rsa -C 'uusama@uusama.com'
```
接下来一直回车即可，使用默认设置，如果不是第一次生成，选择 overwrite。会在指定的目录下面产生秘钥文件，Windows下位置为：/c/Users/用户名/.ssh/id_rsa.pub，在步骤说明里面有详细的地址。 打开这个文件，里面就是 rsa key。

**对于在服务器有多个Git用户的情况，建议密钥文件加上自己的名字，而且还需要修改ssh配置**
```bash
# 编辑ssh配置
vim ~/.ssh/config
# 加上自己的私钥文件位置
Host github.com
User uusama
IdentityFile ~/.ssh/id_rsa.uusama
```
上面的配置用于告诉git使用什么私钥，保证与github端配置的公钥相匹配。

## 将 ssh 秘钥添加到版本库中
生成 ssh 秘钥以后，需要在 github 或者 gitlab 上面添加 ssh key。

添加的位置在个人设置->SSH秘钥中。添加完之后，如果你有提交权限的话，就可以 git push了。

## HTTP 方式设置保存用户名密码
对于 HTTP 方式的git项目，会在每次 git push 的时候要求你输入用户名和密码，这时我们想只输入一次就可以了，可以通过添加git配置，保存用户名和密码，这种方式用户名和密码都是明文保存，会稍微不安全。
```bash
# 设置永久保存
git config --global credential.helper store
# 设置记住密码（默认15分钟）
git config --global credential.helper cache
# 设置过期时间3600s
git config credential.helper 'cache --timeout=3600'
```
上面的命令相对于在 config 文件中添加配置：
```bash
[credential]
helper=store
```
另外还可以将直接修改配置文件中的 url 部分，将用户名和密码写到 url 中：
```bash
http://username:password@git.oschina.net/name/project.git
```