---
title: 使用navicat连接mysql报错
tags:
  - MySQL
url: 850.html
id: 850
categories:
  - 服务端
  - MySQL
date: 2018-07-09 19:56:35
---

## 问题描述
首次安装完mysql之后，在命令行能够登录，但是在navicat连接是报错：

authentication plugin 'caching_sha2_password'

## 解决方法

### 修改用户密码标识
在命令行执行下面的语句
```bash
# 首先登录数据库
mysql -u root -p123456

# 修改当前用户的密码验证方式
ALTER USER 'username'@'ip_address' IDENTIFIED WITH mysql_native_password BY 'password';
# 刷新生效
FLUSH PRIVILEGES;

# 修改root用户示例
ALTER USER 'root'@'%' IDENTIFIED WITH mysql_native_password BY '123456';
```
此时重新在navicat中连接即可。

### 修改配置文件
在windows中，配置文件为安装目录下的`my.ini`，在Linux中，配置文件为`my.conf`。增加下面的配置：
```conf
[mysqld]
default_authentication_plugin=mysql_native_password
```
修改完配置之后保存，然后重启mysql即可。

