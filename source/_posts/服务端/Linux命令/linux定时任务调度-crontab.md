---
title: Linux定时任务调度-crontab
tags:
  - Linux命令
url: 827.html
id: 827
categories:
  - 服务端
  - Linux
date: 2018-06-07 19:28:35
---

## 概述
crontab命令用于设置周期性被执行的指令。该命令从标准输入设备读取指令，并将其存放于“crontab”文件中，以供之后读取和执行。

可以使用crontab定时处理离线任务，比如每天凌晨2点更新数据等，经常用于系统任务调度。

## 服务启动和关闭
一般Linux系统中都会装有`crontab`，如果没有安装可以使用包管理工具安装：
```bash
# vixie-cron 软件包是 cron 的主程序
yum -y install vixie-cron
yum -y install crontabs
```

`crontab`服务的启动和关闭命令如下：
```bash
service crond start     # 启动服务
service crond stop      # 关闭服务
service crond restart   # 重启服务
service crond reload    # 重新载入配置
service crond status    # 查看crontab服务状态

# 可以使用下面的命令加入开机启动
chkconfig --level 345 crond on
```

## 任务调度全局配置
`crontab`全局任务调度配置在如下的目录：
```bash
cron.d/       
cron.daily/   
cron.deny     
cron.hourly/  
cron.monthly/ 
crontab       
cron.weekly/
```

- `cron.daily`是每天执行一次的job
- `cron.weekly`是每个星期执行一次的job
- `cron.monthly`是每月执行一次的job
- `cron.hourly`是每个小时执行一次的job
- `cron.d`是系统自动定期需要做的任务
- `crontab`是设定定时任务执行文件
- `cron.deny`文件就是用于控制不让哪些用户使用Crontab的功能

## 用户配置文件
每个用户都有自己的`crontab`配置文件，使用`crontab -e`命令进行编辑。保存后系统会自动存放与`/var/spool/cron/`目录中，文件以用户名命名。

linux的`crontab`服务每隔一分钟去读取一次`/var/spool/cron`,`/etc/crontab`,`/etc/cron.d`下面所有的内容。

crontab命令一览：

- `crontab -e`: 编辑当前用户的定时任务列表
- `crontab -l`: 查看当前用户的定时任务列表
- `crontab -r`: 删除当前用户的定时任务列表

## crontab定时任务格式
crontab每一条记录为一个定时任务，定时人遵循相应的定义规则。
![](http://uusama.com/wp-content/uploads/2018/06/4142db2eca3d8113e1ab621795058fff.png)
其中前面的6个星号表示的含义如下：

- `minute`： 表示分钟，可以是从0到59之间的任何整数。
- `hour`：表示小时，可以是从0到23之间的任何整数。
- `day`：表示日期，可以是从1到31之间的任何整数。
- `month`：表示月份，可以是从1到12之间的任何整数。
- `week`：表示星期几，可以是从0到7之间的任何整数，这里的0或7代表星期日。
- `command`：要执行的命令，可以是系统命令，也可以是自己编写的脚本文件。

每一个星号部分可用下面的特殊符号：

- 星号（*）：通配符匹配，代表所有可能的值。
- 逗号（,）：可以用逗号隔开的值指定一个列表范围，例如，“1,2,5,7,8,9”。
- 中杠（-）：可以用整数之间的中杠表示一个整数范围，例如“2-6”表示“2,3,4,5,6”。
- 正斜线（/）：可以用正斜线指定时间的间隔频率，例如“0-23/2”表示每两小时执行一次。同时正斜线可以和星号一起使用，例如*/10，如果用在minute字段，表示每十分钟执行一次。

## crontab定时任务实例
参考下面的定时任务例子，后面可以接受要执行的脚本或者命令，这里省略。

- 00 05 * * * : 每天凌晨5点执行
- 20 12 1,10,20 * * : 每个月的1号，10号，20号的12:30执行
- 10 1 * * 6,0 : 表示每周六、周日的1:10分执行
- 0,30 18-23 * * * : 每天18:00至23:00之间每隔30分钟执行
- 0 23-7/1 * * * : 晚上11点到早上7点之间，每隔一小时执行
- 0 6-12/3 * 10 * : 每年10月的每天早上6点到12点每隔3个小时执行一次
- 30 17 * * 1-5 : 周一到周五下午5点30分执行一次
- 0 */2 * * * ： 每两个小时执行一次
