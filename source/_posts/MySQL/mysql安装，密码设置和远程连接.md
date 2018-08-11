---
title: MySQL安装，密码设置和远程连接
tags:
  - MySQL
url: 759.html
id: 759
categories:
  - 服务端
  - MySQL
date: 2018-05-17 20:22:53
---

本文将会结束MySQL的安装，密码设置，服务启动，远程链接等内容。
## MySQL安装
### Linux|Unix|Centos
Linux平台上推荐使用RPM包来安装Mysql，使用`rpm`命令或者`apt-get`命令，安装下面模块：

- MySQL - MySQL服务器
- MySQL-client - MySQL 客户端程序，用于连接并操作Mysql服务器
- MySQL-devel - 库和包含文件，如果你想要编译其它MySQL客户端，例如Perl模块，则需要安装该RPM包
- MySQL-bench - MySQL数据库服务器的基准和性能测试工具
- 

下面演示用`yum`命令进行安装，首先检查当前系统是否已经安装低版本的mysql，如果安装就略过，如果你觉得版本太低，则可以卸载重新安装。
```
# 检测系统是否自带安装 mysql
rpm -qa | grep mysql

# 下面命令可以强力卸载mysql
rpm -e --nodeps mysql
```
最后使用`yum`命令安装Mysql组件：
```
yum install mysql-server mysql mysql-devel
```

另外也可以到官网下载安装包：<https://dev.mysql.com/downloads/repo/yum/>
选择合适版本的安装包，wget之后安装即可。
```shell
# 获取mysql源
wget http://dev.mysql.com/get/mysql57-community-release-el7-8.noarch.rpm
# 安装Mysql源
yum localinstall mysql57-community-release-el7-8.noarch.rpm
# 检测源是否安装成功
yum repolist enabled | grep "mysql.*-community.*"
# 安装mysql
yum install mysql-community-server
```

### Windows平台安装MySQL
Windows 上安装 MySQL 相对来说会较为简单，可以直接下载安装包，安装即可：<https://dev.mysql.com/downloads/installer/>

### MySQL的基本配置
在安装完MySQL之后，需要修改配置文件。不同的系统不一样，Centos为：`/etc/my.cnf`，Windows为安装目录下的`my.ini`，可以修改`default.ini1`。配置信息如下：
```conf
[mysql]
# 设置mysql客户端默认字符集
default-character-set=utf8
 
[mysqld]
# 设置3306端口
port = 3306

# 设置mysql的安装目录
basedir=D:\wamp\mysql-5.7.13

# 设置mysql数据库的数据存放目录
datadir=D:\wamp\sqldata

# 允许最大连接数
max_connections=20

# 服务端使用的字符集默认为8比特编码的latin1字符集
character-set-server=utf8

# 创建新表时将使用的默认存储引擎
default-storage-engine=INNODB
```

在Windows平台，需要在DOS窗口中输入以下命令（需要加环境变量或者去到安装目录的bin目录下）：
```bat
# 初始化MySQL
mysqld --initialize-insecure

# 安装MySQL服务
mysqld -install

# 启动mysql服务
net start mysqld

# 进入mysql
mysql -u root -p

# 设置密码
mysqladmin -u root password *****
```

在Linux平台也是类似，需要注意有的版本安装完之后，会给root生成一个默认密码或者不使用密码就可以登录。默认密码一在日志文件中：`/var/log/mysqld.log`

## mysql设置密码（找回密码）
可以在命令行设置密码，不用登录mysql，前提是知道旧密码，使用下面的命令：
```bash
mysqladmin -u root -p oldpassword newpasswd
```

有时我们可能忘记密码，还有可能遇到下面的问题：

Access denied for user 'root'@'localhost' to database 'mysql'

这时就可以进入安全模式强制修改密码。Linux平台步骤如下
```shell
# 关闭服务
service mysqld stop
# 绕过密码进入安全模式
mysqld_safe --skip-grant-table

# 下面进入mysql，修改密码
mysql -u root mysql
mysql> UPDATE user SET password=PASSWORD('123456') where user='root'
mysql> FLUSH PRIVILEGES;
```
需要特别注意修改密码后要刷新权限。

## mysql远程连接
一般来说，mysql安装后是默认不允许远程连接的，这个时候在其他地方使用mysql就会报下面的错误：
Access denied for user 'root'@'localhost' to database 'mysql'
Access denied for user ''@'localhost' to database 'mysql'

### 关于user表
mysql中默认有一个数据库，名称为`mysql`，其中有一张表`user`，记录数据的所有用户和权限。
```mysql
use mysql;
select host,user,password from user;
```
其中user表示用户名，host表示允许该用户访问的主机，password为该用户访问密码。

可以直接对这个表进行增删改查，从而添加用户。

### 允许远程访问
如果需要允许某个用户远程访问数据库，可以有两个办法

- 直接编辑mysql.user表，修改host字段
- 使用授权语句对用户进行授权

比如下面运行root用户可以远程访问，可以有如下操作：
```mysql
# 直接修改表
update user set host = '%' where user = 'root';

# 使用授权语句
grant all privileges  on *.* to root@'%' identified by "123456";
# 使用授权语句可以直接新增一个用户
grant all on *.* to 'uusama'@'192.169.127.44' identified by '123456';

# 最后都需要刷新权限
flush privileges;
```
在grant语句中，*.*部分，坐边的*表示所有数据库，右边的*表示所有数据表

### 远程不能访问时的问题
有时会遇到访问数据库的出现Access denied的问题，这个问题的原因可能有：

- 用户名密码错误，比如多了空格等，此时改正即可
- 该用户不允许远程主机访问，参照上面的方法配置远程访问
- 防火墙没有开发指定的端口，开放相应的端口即可
- 所使用服务器对数据库访问做了限制，比如阿里云的组策略，需要开发相应端口，有的公司内网机器也不允许外部访问
- root用户的限制，有的访问终端会对root用户进行限制，这时可以添加一个新用户
- 绑定地址有误，如果mysql配置中绑定ip问127.0.0.1，可能外部机器访问不了，这时可以修改配置文件，注释掉：bind-address = 127.0.0.1