---
title: Linux文件上传下载命令-szrz
tags:
  - Linux命令
url: 817.html
id: 817
categories:
  - 服务端
  - Linux
date: 2018-06-03 20:40:52
---

一般Linux服务器上都会有rz，lz命令，在使用ssh登录的时候，可以通过这两个命令和服务器交互文件

## 安装方法
如果服务器上没有这两个命令，可以使用下面的命令进行安装：
```
# 对于Uubuntu
sudo apt-get install lrzsz

# 对于Centos可以用下面的命令
sudo yum install lrzsz

当然也可以手动下载编译安装，官网下载地址：<http://www.ohse.de/uwe/software/lrzsz.html>，下载相应版本的.tar.gz压缩包。解压编译安装即可：
```bash
# 可以参考下面的命令进行下载安装
wget https://www.ohse.de/uwe/releases/lrzsz-0.12.20.tar.gz

# 解压
tar -xzf lrzsz-0.12.20.tar.gz  

# 安装
cd lrzsz-0.12.20  
./configure --prefix=/usr/local/lrzsz  
sudo make  
sudo make install  

# 创建快捷链接
cd /usr/bin  
sudo ln -s /usr/local/lrzsz/bin/lrz rz  
sudo ln -s /usr/local/lrzsz/bin/lsz sz  
```

## 使用方法
记住：`rz`命令为**上传文件**，`sz`命令为**下载文件**
```bash
# 下载当前目录的test.txt文件
sz test.txt

# 上传文件，该命令可以打开交互见面选择需要上传的文件
rz
```
有时候我们会遇到上传的文件和当前文件夹中的文件同名，此时上传的文件会自动被重命名。如test.txt，会被重命名为test.txt.0。

可以添加选项覆盖上传，同名的文件自动覆盖：
```bash
# 上传文件，如果有同名文件则覆盖
rz -y
```