---
title: Linux日期时间处理-date
tags:
  - Linux命令
url: 855.html
id: 855
categories:
  - 服务端
  - Linux
date: 2018-07-30 16:36:29
---

## 几个示例
在Linux命令行，使用`date`命令可以很方便处理日期时间字符串。
```shell
# 今天的日期：20180730，+号前面有空格，后面没有空格
date +"%Y%m%d"

# 昨天的日期
date -d "1 day ago" +"%Y%m%d"

# 在字符串中使用
echo $(date +"%Y%m%d")
```

## date命令
### 使用方式
date命令的两种使用方式如下：
```shell
date [OPTION]... [+FORMAT]
date [-u|--utc|--universal] [MMDDhhmm[[CC]YY][.ss]]
```

### 选项

- `-d`<字符串> 　显示字符串所指的日期与时间。字符串前后必须加上双引号。
- `-s`<字符串> 　根据字符串来设置日期与时间。字符串前后必须加上双引号。
- -u` 　显示GMT。
- `–help` 　在线帮助。
- `–version` 　显示版本信息

### 可用格式

可用日期格式：

- %a : 星期几 (Sun..Sat)
- %A : 星期几 (Sunday..Saturday)
- %b : 月份 (Jan..Dec)
- %B : 月份 (January..December)
- %c : 直接显示日期和时间
- %d : 日 (01..31)
- %D : 直接显示日期 (mm/dd/yy)
- %h : 同 %b
- %j : 一年中的第几天 (001..366)
- %m : 月份 (01..12)
- %U : 一年中的第几周 (00..53) (以 Sunday 为一周的第一天的情形)
- %w : 一周中的第几天 (0..6)
- %W : 一年中的第几周 (00..53) (以 Monday 为一周的第一天的情形)
- %x : 直接显示日期 (mm/dd/yy)
- %y : 年份的最后两位数字 (00.99)
- %Y : 完整年份 (0000..9999)

可用时间格式：

- % : 印出
- % %n : 换行
- %t : Tab
- %H : 小时(00..23)
- %I : 小时(01..12)
- %k : 小时(0..23)
- %l : 小时(1..12)
- %M : 分钟(00..59)
- %p : 显示本地 AM 或 PM
- %r : 直接显示时间 (12 小时制，格式为 hh:mm:ss [AP]M)
- %s : 从 1970 年 1 月 1 日 00:00:00 UTC 到目前为止的秒数 %S : 秒(00..61)
- %T : 直接显示时间 (24 小时制)
- %X : 相当于 %H:%M:%S
- %Z : 显示时区

## 示例
```shell
# -s 参数使用
date -s 200180523
date -s 00:00:00
date -s "20018-05-23 00:00:00"

# -d 参数使用
# 下个月的时间
date -d 'next monday'
# 明天的日期
date -d next-day +%Y%m%d
# 昨天是号数
date -d last-day +%d
# 30天前的日期
date -d '30 days ago' +%Y%m%d
# 100天前的日期
date -d '-100 days' +%Y%m%d
# 去年的日期
date +%Y%m%d –date=”-1 year”
# 明年的日期
date +%Y%m%d –date=”+1 day”
```