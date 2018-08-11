---
title: MySQL状态检测命令
tags:
  - MySQL
url: 760.html
id: 760
categories:
  - 服务端
  - MySQL
date: 2018-05-13 20:50:42
---

本文记录一些检测MySQL性能，配置的命令
## status命令
`status`命令可以列出MySQL数据库的基本参数。比如编码信息，MySQL版本，查询次数等等。
```shell
mysql> status
--------------
mysql  Ver 14.14 Distrib 5.1.61, for redhat-linux-gnu (x86_64) using readline 5.1

Connection id:		790
Current database:	snapchat
Current user:		root@localhost
SSL:			Not in use
Current pager:		stdout
Using outfile:		''
Using delimiter:	;
Server version:		5.1.61 Source distribution
Protocol version:	10
Connection:		Localhost via UNIX socket
Server characterset:	utf8
Db     characterset:	utf8
Client characterset:	utf8
Conn.  characterset:	utf8
UNIX socket:		/var/lib/mysql/mysql.sock
Uptime:			1 day 4 hours 19 min 19 sec

Threads: 1  Questions: 5796  Slow queries: 0  Opens: 282  Flush tables: 1  Open tables: 50  Queries per second avg: 0.56
```

## `show status`命令
该语句相对于`status`列出了更多的数据库配置信息。

查询本次MySQL启动后执行的SELECT语句的次数
```mysql
show status like 'com_select';
```
查看MySQL服务器的线程信息
```mysql
show status like 'Thread_%';
```

## `show variables`命令
`show variables`命令用于列出MySQL的所有配置信息，按照字母升序排列。可以使用模糊匹配进行查询，如下语句查询编码信息：
```mysql
show variables like 'char%';
```
查看端口
```mysql
show global variables like 'port';
```

## MySQL系统表information_schema
`information_schema`库是MySQL的系统库，里面的表都是只读表，这些表实际是视图，MySQL自己进行维护，该数据库中存放了其他数据库的相关信息。

在使用下面的sql语句之前，需要切换数据库：
```mysql
use information_schema;
```

`information_schema`系统表的基本字段如下：
```mysql
desc information_schema.tables;

+-----------------+---------------------+------+-----+---------+-------+
| Field           | Type                | Null | Key | Default | Extra |
+-----------------+---------------------+------+-----+---------+-------+
| TABLE_CATALOG   | varchar(512)        | YES  |     | NULL    |       |
| TABLE_SCHEMA    | varchar(64)         | NO   |     |         |       |
| TABLE_NAME      | varchar(64)         | NO   |     |         |       |
| TABLE_TYPE      | varchar(64)         | NO   |     |         |       |
| ENGINE          | varchar(64)         | YES  |     | NULL    |       |
| VERSION         | bigint(21) unsigned | YES  |     | NULL    |       |
| ROW_FORMAT      | varchar(10)         | YES  |     | NULL    |       |
| TABLE_ROWS      | bigint(21) unsigned | YES  |     | NULL    |       |
| AVG_ROW_LENGTH  | bigint(21) unsigned | YES  |     | NULL    |       |
| DATA_LENGTH     | bigint(21) unsigned | YES  |     | NULL    |       |
| MAX_DATA_LENGTH | bigint(21) unsigned | YES  |     | NULL    |       |
| INDEX_LENGTH    | bigint(21) unsigned | YES  |     | NULL    |       |
| DATA_FREE       | bigint(21) unsigned | YES  |     | NULL    |       |
| AUTO_INCREMENT  | bigint(21) unsigned | YES  |     | NULL    |       |
| CREATE_TIME     | datetime            | YES  |     | NULL    |       |
| UPDATE_TIME     | datetime            | YES  |     | NULL    |       |
| CHECK_TIME      | datetime            | YES  |     | NULL    |       |
| TABLE_COLLATION | varchar(32)         | YES  |     | NULL    |       |
| CHECKSUM        | bigint(21) unsigned | YES  |     | NULL    |       |
| CREATE_OPTIONS  | varchar(255)        | YES  |     | NULL    |       |
| TABLE_COMMENT   | varchar(80)         | NO   |     |         |       |
+-----------------+---------------------+------+-----+---------+-------+
```

### 查询总体信息
```mysql
# 查询所有数据的大小
select concat(round(sum(data_length/1024/1024),2),'MB') as data 
from tables;

# 查看指定数据库的大小
select concat(round(sum(data_length/1024/1024),2),'MB') as data 
from tables 
where table_schema='databse_name';

# 查看指定数据库的某个表的大小
select concat(round(sum(data_length/1024/1024),2),'MB') as data 
from tables 
where table_schema='databse_name' and table_name='table_name';
```
### 查询各表存储空间大小
倒序显示某个数据库中所有表数据和索引所占空间大小，sql如下，其中database_name为查询的数据库：
```mysql
select 
TABLE_NAME, 
concat(truncate(data_length/1024/1024,2),' MB') as data_size,
concat(truncate(index_length/1024/1024,2),' MB') as index_size
from information_schema.tables
where TABLE_SCHEMA = 'databse_name'
group by TABLE_NAME
order by data_length desc;
```

### 查看数据库中所有表的记录数
查看数据库中所有表的记录数
```mysql
select table_name,table_rows 
from information_schema.tables
where TABLE_SCHEMA = 'snapchat'
order by table_rows desc;
```
