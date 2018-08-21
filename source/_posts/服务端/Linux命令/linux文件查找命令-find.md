---
title: Linux文件查找命令-find
tags:
  - Linux命令
url: 685.html
id: 685
categories:
  - 服务端
  - Linux
date: 2018-03-25 01:39:52
---

Linux 文件查找命令find

## 语法
Linux find命令用来在指定目录下查找文件。find命令的语法如下：

```shell
find path -option [ -print ] [ -exec -ok command ] {} \;
```

- path 指定查找的路径
- 任何位于参数之前的字符串都将被视为欲查找的目录名
- path默认为当前路径，expression默认为-print，即显示所有结果到标准输出
- -exec command {} \; 将查到的文件执行command操作，注意要有空格
- -ok 和-exec相同，只不过在操作前要询用户

## 查询路径
path选项表示查询的起始路径，可以查找path下面的子路径。默认为当前路径。

**需要注意：需要对查询的路径永远访问权限才可以查询，遇到没有权限访问的文件时会自动跳过**

参考下面的实例
```shell
# 列出当前目录及子目录下所有文件和文件夹
find .

# 列出个人文件夹 /home/username 下的所有文件
find ~

# 列出/home文件下的所有文件
find /home
```

## 基于文件属性搜索
find命令提供了丰富的文件属性查找选项，比如指定文件类型，文件修改时间，文件大小等。
### 根据文件名正则进行搜索
最基本的，find命令提供能基于文件名的正则表达式的搜索：
```shell
find path -[i]name|-[i]lname|-[i]path|-[i]regex pattern
```

其中可用的选项的含义如下：

- `-name`指定字符串作为寻找文件或目录的范本样式
- `-iname`效果和`-name`一样但是忽略字符大小写
- `-lname`指定字符串作为寻找符号连接的范本样式
- `-ilname`效果和`-lname`一样但是忽略字符大小写
- `-path`指定字符串作为寻找目录的范本样式
- `-ipath`效果和`-path`一样但是忽略字符大小写
- `-regex`指定基于正则表达式匹配，需要注意正则表达式中特殊字符的转义
- `-iregex`效果和`-regex`一样但是忽略字符大小写
- `-prune`指定字符串作为寻找文件或目录的不匹配范本样式

常用的文本匹配选项有：

- `*`表示通配符匹配，如"*Controller.php"
- {1,3}表示匹配1或者3
- [0-10]表示匹配0到10的数值

```shell
# 查找当前目录下的所有log文件
find . -name ".log"

# 查找/home目录下所有的.php文件并且忽略大小写
find /home -iname ".php"

# 查找当前目录及子目录下所有以.png和.jpg结尾的文件
find . -name "*.png" -o -name "*.jpg" 
# 可以写成下面的形式
find . \( -name "*.png" -o -name "*.jpg" \)

# 基于正则表达式查找
find /home -regex ".*\(\.png\|\.jpg\)$"

# 查找/etc目录下的所有文件以及log结尾的目录
find /etc -path "*log"

查找当前目录或者子目录下所有.txt文件，但是跳过子目录import
find . -path "./import" -prune -o -name "*.txt
```

### 根据文件类型进行搜索
find命令可以根据文件类型来进行查找：
```shell
find . -type [b/d/c/p/l/f]
```
参数类型列表含义如下：

- `f` 普通文件
- `l` 符号连接
- `d` 目录
- `c` 字符设备
- `b` 块设备
- `s` 套接字
- `p` Fifo

```shell
# 查询当前目录下的所有文件夹
find -type d
```

### 根据文件大小进行匹配
find命令可以根据大小进行查找：
```shell
find path -size [+-][n][u]
```

- `+-` 表示大于还是小于，值可为：
    - `-`表示小于指定大小
    - `+`表示大于指定大小
    - 空值不填表示等于指定大小
- `n` 表示数值，如100等
- `u` 表示单位，可为：
    - `b` 块（512字节）
    - `c` 字节
    - `w` 字（2字节）
    - `k` 千字节
    - `M` 兆字节
    - `G` 吉字节

```shell
# 查询当前目录下大于10K的文件
find -type f -size +10k

# 查询当前目录下等于10K的文件
find -type f -size 10k

# 另外，可以使用以下命令来查找当前目录下所有长度为零的文件和目录：
find . -empty
```

### 根据文件权限进行匹配
find命令可以根据文件权限和所有者进行查找：
```shell
find path -perm|-user|-group value
```

其中各项选项含义如下：

- `-perm`表示指定权限值，此时`value`可为如777的权限代码
- `-user`表示指定用户，此时`value`为用户名
- `-group`表示指定用户组，此时`value`为用户组名
- `-group`表示指定用户组，此时`value`为用户组名
- `-nouser`表示不属于本地主机用户
- `-nogruop`表示不属于本地主机用户组

```shell
# 搜索当前目录下出权限为777的php文件
find . -type f -name "*.php" -perm 777

# 找出当前目录下权限不是644的文件
find . -type f ! -perm 644

# 找出当前目录用户uusama拥有的所有文件
find . -type f -user uusama

# 找出当前目录用户组nginx拥有的所有文件
find . -type f -group nginx
```

### 根据文件时间戳进行查找
find命令提供了基于诸如访问时间等时间戳来搜索文件：
```shell
find path -atime|-mtime|-ctime|-newer [+-]time
```

其中各个可选选项的含义如下：

- `-atime`用户最近一次访问时间，按照天算
- `-amin`用户最近一次访问时间，按照分钟算
- `-mtime`文件最后一次修改时间，按照天算
- `-mmin`文件最后一次修改时间，按照分钟算
- `-ctime`文件数据元（例如权限等）最后一次修改时间，按照天算
- `-atime`文件数据元（例如权限等）最后一次修改时间，按照分钟算
- `-newer`表示更改时间较指定文件或目录的更改时间更接近现在

```shell
# 搜索当前目录下最近三天内被访问过的所有文件
find . -type f -atime -3

# 搜索当前目录下最近一个小时修改过的.log文件
find . -type f -name "*.log" -mmin 60

# 找出当前目录下在login.php修改之后的文件或者目录
find . -newer login.php
```

## 指定搜索结果行为
find命令还可以对查找的文件进行诸如删除等操作。

### 对查询到的文件进行删除
可以使用`-delete`选项来对查询到的文件删除：
```shell
# 删除当前目录下所有.tmp文件
find . -type f -name "*.tmp" -delete
```

### 对查询结果执行简单命令
可以使用`-exec`和`-ok`来对查询结果执行简单命令，`-ok`和`-exec`效果一样，只不过`-ok`会给出询问提示是否要执行想要操作。

需要注意，这些命令的格式，结尾为命令加上空格加上大括号加上空格加上反斜杠，最后的空格加反斜杠一定不能少，否则会报缺少参数的错误。
```shell
find path -exec command {} \;
```

看下面的例子：
```shell
# 找出当前目录下所有root用户的文件和目录并修改拥有者为uusama
find . -user root -exec chown uusama {} \;

# 将30天前的.log文件移动到old目录中
find . -type f -mtime +30 -name "*.log" -exec mv old {} \;

# 查找系统中所有文件长度为0的普通文件，并列出它们的完整路径
find / -type f -size 0 -exec ls -l {} \;

# 查找/var/logs目录中更改时间在7日以前的普通文件，并在删除之前询问它们
find /var/logs -type f -mtime +7 -ok rm { } \;

# 找出当前用户目录下所有的.tmp文件并且删除
find $HOME/. -name "*.tmp" -ok rm {} \;

# 找出当前目录下所有.log文件并以“File:文件名”的形式打印出来
find . -type f -name "*.log" -exec printf "File: %s\n" {} \;

# 查找当前目录下所有access*.log文件并把他们拼接起来合并到access.log文件中
find . -type f -name "access*.log" -exec cat > access.log {} \;
```

如果想要对查询结果执行复杂的命令，可以预先把多个命令写入到.sh文件中，然后调用这个.sh文件
```shell
# 查找当前目录下所有.txt文件并调用filter.sh文件处理
find . -type f -name "*.txt" -exec ./filter.sh {} \;
```

## 其他的一些选项

### 指定查找深度
可以用`-depth`从指定目录下最深层的子目录开始查找。
```shell
# 从当前目录下最深目录开始查找
find -depth
```