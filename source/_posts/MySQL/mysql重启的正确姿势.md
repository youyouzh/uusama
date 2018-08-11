---
title: MySQL重启的正确姿势
tags:
  - MySQL
url: 807.html
id: 807
categories:
  - 服务端
  - MySQL
date: 2018-06-02 18:54:36
---

## Linux不宜使用kill强制关闭
首先直接 kill 肯定是不行的。直接kill会可能会导致一些临时数据没有保存，甚至表损坏，而且可能会使得进程僵死掉。

在关闭MySQL之前，首先登录下，查看但是是否有正在跑的任务：
```bash
# 登录mysql
mysql -uroot -hlocalhost -P3306 -p

# 查看当前的查询任务
show processlist;
```

## Linux下MySQL安全关闭
确定没问题之后，使用`mysqladmin`安全关闭MySQL
```
mysqladmin -uroot -p shutdown

# 有时候需要指定sock文件
mysqladmin -uroot -p shutdown --socket=/home/work/mysql/mysqld.sock
```

## Linux下MySQL安全启动
关闭后使用`safe_mysqld`启动服务。通过mysql的守护进程进行启动。
```
## 启动可以指定配置文件
safe_mysqld --defaults-file=/home/work/mysql/my.cnf &
```

## 其他的重启方式
### windows平台
windows除了可以在用户交互界面启动外，还可以cmd在DOS命令行启动，命令行启动需要安装mysql服务，注意相应更新mysql服务名称。
```bash
# 停止mysql服务
net stop mysqld

# 启动mysql服务
net start mysqld
```
### Linux平台
service平台启动可以有下面三种方式：

- 使用`service`启动：service mysqld start
- 使用`mysqld`脚本启动：/etc/init.d/mysqld start
- 使用`safe_mysql`启动：safe_mysqld &

相应的停止和重启只需要把上面是三个命令中的`start`换成`stop`或者`restart`就可以了。