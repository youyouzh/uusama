---
title: Linux模拟HTTP请求-curl命令
tags:
  - Linux命令
url: 854.html
id: 854
categories:
  - 服务端
  - Linux
date: 2018-07-27 21:42:24
---

## 一个简单的GET请求
使用curl命令可以轻松发起一个HTTP请求：

```shell
# 使用GET凡是请求网址
curl http://uusama.com
```

可以使用`-X`选项指定请求方式

## 携带参数的POST请求
下面演示一个带头部和参数的POST请求
```shell
curl -X POST \
  'http://uusama.com/?r=SnapchatApi%2FdoCurlQuery' \
  -H 'cache-control: no-cache' \
  -H 'content-type: multipart/form-data; boundary=----WebKitFormBoundary7MA4YWxkTrZu0gW' \
  -F name=uusama \
  -F like=fruit
```
该请求方式相当于在页面提交一个表单，其中：

- `-X POST` 指定请求凡是为POST请求
- `-H` 指定请求头部
- `F` 指定请求参数

## curl命令测试请求耗时
在curl命令中，有以下几个变量反应请求时间：

- time_namelookup：DNS解析域名时间，把域名--->ipd的时间
- time_connect：TCP连接的时间，三次握手的时间
- time_appconnect：SSL|SSH等上层连接建立的时间
- time_pretransfer：从请求开始到到响应开始传输的时间
- time_redirect：从开始到最后一个请求事务的时间
- time_starttransfer：从请求开始到第一个字节将要传输的时间
- time_total：总时间

示例：
```shell
curl -o /dev/null -s -w time_namelookup:"\t"%{time_namelookup}"\n"time_connect:"\t\t"%{time_connect}"\n"time_appconnect:"\t"%{time_appconnect}"\n"time_pretransfer:"\t"%{time_pretransfer}"\n"time_starttransfer:"\t"%{time_starttransfer}"\n"time_total:"\t\t"%{time_total}"\n"time_redirect:"\t\t"%{time_redirect}"\n"  http://uusama.com

# 请求结果如下
time_namelookup:	0.000
time_connect:		0.000
time_appconnect:	0.000
time_pretransfer:	0.000
time_starttransfer:	0.001
time_total:		1.755
time_redirect:		0.000
```

其中各选项的含义如下：

- `-w`：将请求结果输入到文件而不是标准输出
- `-o`：请求完成后使用自定义格式输出
- `-s`：静默模式（不要输出任何东西）