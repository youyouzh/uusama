---
title: MySQL数据导入导出
tags:
  - MySQL
url: 755.html
id: 755
categories:
  - 服务端
  - MySQL
date: 2018-05-14 14:49:50
---

## 导入和导出原始格式（sql格式）数据
### 使用`mysqldump`导出本地数据库
可以使用`mysqldump`导出整个数据库，单个数据表的结构或者数据，导出的文件是sql语句集合。

需要注意：

- `mysqldump`导出数据是，导出的文件目录需要有可写权限
- 导出的sql语句中，包含建表命令和建库命令

```shell
# 导出所有数据库数据
mysqldump -h localhost -uroot -p123456 –all-databases > dump.sql

# 导出单个数据库结构和数据
mysqldump -h localhost -uroot -p123456 database_name > dump.sql

# 导出单个数据表结构和数据
mysqldump -h localhost -uroot -p123456  database_name table_name > dump.sql

# 导出整个数据库结构（不包含数据）
mysqldump -h localhost -uroot -p123456 -d database_name > dump.sql

# 导出单个数据表结构（不包含数据）
mysqldump -h localhost -uroot -p123456 -d database_name table_name > dump.sql
```
在上面的语句中，需要使用`mysqldump`登录数据库，和`mysql`登录方式是一样的。其中的一些参数说明如下：

- `-h` 在后面接上数据库主机地址，ip或者域名
- `-u` 指定数据库登录用户名
- `-p` 指定数据库登录密码
- `-P` 指定数据库端口
- `-d` 指定只导出数据表结构，不导出数据
- `–all-databases` 表示导出所有数据库数据
- `database_name` 该部分替换为你需要导出的数据库名
- `table_name` 该部分可选，替换为你需要导出的数据表名
- `dump.sql` 部分可以指定完全路径

Mysql一般不建议把密码写到命令中，可以用下面的方式：
```shell
# 先执行第一条命令，然后在交互模式输入密码
mysqldump -u root -p database_name table_name > dump.sql
password *******
```

### 使用`mysqldump`导出远程数据库
你也可以使用以下命令将导出的数据直接导入到远程的服务器上，但请确保**两台服务器是相通的并可以相互访问的，还要确保远程服务器上的数据库允许远程连接。**：
```shell
mysqldump -h localhost -u root -p database_name | mysql -h other-host.com database_name
```

如果你需要将远程服务器的数据拷贝到本地，你也可以在 mysqldump 命令中指定远程服务器的IP、端口及数据库名。
```shell
mysqldump -h other-host.com -P port -u root -p database_name > dump.sql
password ******
```

### 将sql文件数据导入数据库
以上`mysqldump`导出的数据为sql语句集合，如果需要在数据库中导入这类数据，直接执行sql文件即可，有如下三种方法在mysql中执行sql文件导入数据：
```shell
# 首先登录数据库
mysql -hlocalhost -uroot -p123456 database_name
# 然后在数据库中执行sql文件
source /home/uusama/dump.sql

# 还可以直接在命令行执行sql文件
mysql -h localhost -u root -p 123456 database_name < /dump.sql
# 或者下面的形式
mysql -h localhost -uroot -p123456 database_name -e "source /dump.sql"
```
**需要注意文dump.sql文件的路径，尽量使用绝对路径。**

## 导入导出特定格式数据
### `into outfile`命令导出查询结果集
可以通过`into outfile`命令将查询结果导出，并指定导出格式，比如CSV格式等。这样导出的数据不包含sql语句。下面的例子导出CSV格式数据：
```shell
SELECT * FROM table_name 
INTO OUTFILE '/tmp/data.txt'
FIELDS TERMINATED BY ',' ENCLOSED BY '"'
LINES TERMINATED BY '\r\n';
```
这种导出数据的方式更加灵活，可以导出查询结果，指定导出格式等。

这种导出方式默认制表符分割，换行结尾。下面的语句使用默认方式导出某个表的所有数据：
```shell
SELECT * FROM table_name
INTO OUTFILE '/tmp/data.txt'
```

这种导出方式不能像`mysqldump`那样导出整个数据库的数据，多用于部分备份。比如我们现在需要从线上环境导出部分数据到测试环境，就可以用这种方式，导出我们需要的数据即可，而不是导出整个数据库。

### 使用`load data infile`导入数据
可以使用`load data infile`导入`into outfile`导出的数据或者其他格式化的数据。比如上面导出的CSV格式数据就可以用下面的方式导入：
```shell
LOAD DATA local INFILE '/tmp/data.txt'
INTO table table_name
FIELDS TERMINATED BY ',' ENCLOSED BY '"'
LINES TERMINATED BY '\r\n';
```

这里需要注意：

- `local`用于指定文件为本机文件，文件必须存在，否者会报错：ERROR 13 (HY000): Can't get stat of '/tmp/test2.txt' (Errcode: 2)
- 导入导出需要注意数据列是否对上，以免数据错位