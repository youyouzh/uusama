---
title: Linux文本内容处理命令-awk
tags:
  - Linux命令
url: 727.html
id: 727
categories:
  - 服务端
  - Linux
date: 2018-04-02 17:21:11
---

## 概述
`awk`是一种处理文本文件的语言，是一个强大的文本分析工具。命令`awk`是逐行进行处理的。

之所以叫AWK是因为其取了三位创始人 Alfred Aho，Peter Weinberger, 和 Brian Kernighan 的Family Name的首字符。

和`awk`相似的文本处理命令还有：`grep`和`sed`，它们的使用区别如下；

- `grep`更适合单纯的文本内容查找和匹配
- `sed`更适合对匹配到的文本进行编辑
- `awk`更适合文本格式化，对文本进行较复杂的处理

## 语法
`awk`的使用语法如下：
```shell
awk [option] 'script' [var=value] file(s)

# 或者下面的形式
awk [option] -f scriptfile [var=value] file(s)
```

其中每一项的说明如下：

- [option] : 这部分可选，是`awk`命令的选项，如指定分隔符等
- script : 该部分为`awk`的脚本，用于指定如何处理文本
- [var=value] : 这部分可选，用于定义变量，使用是需要设置`-v`选项
- file(s) : 该部分用于指定需要处理的文件地址，可以是多个
- -f scriptfile : 当使用`-f`选项时，可以使用`awk`脚本文件处理

## 选项
可以在`awk`命令的`[option]`部分指定选项，用于设置`awk`命令的行为。常用的有`-f`, `-F`, `-v`。

下面的例子中用到的文本文件 log.txt 的内容如下：
```shell
1 client sends a tcp,syn, ack
3 server get,test, name
5 initial,sequence,number connection
```

### `-f`选项
有时候我们需要对文件进行很复杂的处理，这个时候把处理脚本写在命令里面不方便，就可以使用`-f`选项，指定处理脚本：
```shell
# scriptfile 指定脚本文件地址
-f scripfile

# 还可以有下面的形式
--file scriptfile
```

```shell
# 使用 operate.sh 文件处理 log.txt 文件
awk -f operate.sh log.txt
```

### `-F`选项
该选项指定输入文件的分隔符，fs是一个字符串或者是一个正则表达式。分隔符默认为空格或者tab，指定分隔符以后，可以把文本中的每一行分成几个部分，可以分别访问这几个部分。
```shell
# fs 指定分隔符
-F fs

# 还可以有下面的形式
--field-separator fs
```
看下面的例子：
```shell
#  每行按空格或TAB分割，输出文本中的1、2项
awk '{print $1,$2}' log.txt
---------------------------------------------
1 client
3 server
5 initial,sequence,number
---------------------------------------------

# 使用逗号分隔
awk -F, '{print $1,$2}' log.txt
---------------------------------------------
1 client sends a tcp syn
3 server get test
5 initial sequence
---------------------------------------------

# 可以使用多个符号分隔，下面使用空格和逗号分隔
awk -F '[ ,]' '{print $1,$2,$5}' log.txt
---------------------------------------------
1 client tcp
3 server 
5 initial connection
---------------------------------------------
```

### `-v`选项
该选项用于设置变量，设置的变量可以在`awk`脚本中使用。
```shell
-v var=value
```
看下面的例子
```shell
awk -v a=1 -v b=bbb '{print $1+a,$2b}' log.txt
---------------------------------------------
2 clientbbb
4 serverbbb
6 initial,sequence,numberbbb
---------------------------------------------
```

## `awk`脚本部分
在`awk`的定义中，`option`后面就是`script`部分，该部分的脚本用来处理每一行的文本。

### 基本模式
最简单的`script`部分定义如下：
```shell
awk '[pattern] {action}' {filenames}
```

其中：

- [pattern] 部分定义匹配规则
- action 部分定义处理规则

看下面的例子：
```shell
# 过滤第一列大于2的行
awk '$1>2' log.txt
---------------------------------------------
3 server get,test, name
5 initial,sequence,number connection
---------------------------------------------

# 过滤第一列大于2并且第2列等于'server'的行，并且只打印出第1,2,3项
awk '$1>2 && $2=="server" {print $1,$2,$3}' log.txt
---------------------------------------------
3 server get,test,
---------------------------------------------
```

这里可以使用的运算符如下：

| 运算符 | 描述  |
| :--- | :---  |
| = += -= *= /= %= ^= **= | 赋值  |
| ?: | C条件表达式  |
| &#124;| | 逻辑或  |
| && | 逻辑与  |
| ~ ~! | 匹配正则表达式和不匹配正则表达式  |
| < <= > >= != == | 关系运算符  |
| 空格 | 连接  |
| + – | 加，减  |
| * / % | 乘，除与求余  |
| + – ! | 一元加，减和逻辑非  |
| ^ *** | 求幂  |
| ++ — | 增加或减少，作为前缀或后缀  |
| $ | 字段引用  |
| in | 数组成员  |

### 使用正则表达式
在匹配的时候，我们还可以使用正则表达式来进行匹配，正则表达式使用连个斜杠包围起来。
```shell
# 输出第二列包含 "ser"，并打印第1列与第3列
awk '$2 ~ /ser/ {print $1,$3}' log.txt
---------------------------------------------
3 get,test,
---------------------------------------------

# 输出第二列不包含 "ser"，并打印第1列与第3列
awk '$2 !~ /ser/ {print $1,$3}' log.txt
---------------------------------------------
1 sends
5 connection
---------------------------------------------
```

在上面的例子中，一般使用`~`和`!~`来表示正则匹配，在后面的`//`中写入标识的正则匹配表达式即可。

### 內建变量
在上面的例子中，已经可以见到使用 $n 的方式访问每一行的第n项，其实 $n 就是`awk`内部定义的变量。除了我么可自定义变量外，`awk`中预定义的变量如下表：

|变量|描述|
|:---|:---|
|**$n** |当前记录的第n个字段，字段间由FS分隔 |
|**$0** |完整的输入记录 |
|**FILENAME** |当前文件名 |
|**FNR** |各文件分别计数的行号 |
|**FS** |字段分隔符(默认是任何空格) |
|**NF** |一条记录的字段的数目 |
|**NR** |已经读出的记录数，就是行号，从1开始 |
|**OFS** |输出记录分隔符（输出换行符），输出时用指定的符号代替换行符 |
|**ORS** |输出记录分隔符(默认值是一个换行符) |
|**RS** |记录分隔符(默认是一个换行符) |
|ARGC |命令行参数的数目 |
|ARGIND |命令行中当前文件的位置(从0开始算) |
|ARGV |包含命令行参数的数组 |
|CONVFMT |数字转换格式(默认值为%.6g)ENVIRON环境变量关联数组 |
|ERRNO |最后一个系统错误的描述 |
|FIELDWIDTHS |字段宽度列表(用空格键分隔) |
|OFMT |数字的输出格式(默认值是%.6g) |
|RLENGTH |由match函数所匹配的字符串的长度 |
|IGNORECASE |如果为真，则进行忽略大小写的匹配 |
|RSTART |由match函数所匹配的字符串的第一个位置 |
|SUBSEP |数组下标分隔符(默认值是/034) |

看下面的例子：
```shell
awk 'BEGIN{printf "%4s %4s %4s %4s %4s %4s %4s %4s %4s\n","FILENAME","ARGC","FNR","FS","NF","NR","OFS","ORS","RS";printf "---------------------------------------------\n"} {printf "%4s %4s %4s %4s %4s %4s %4s %4s %4s\n",FILENAME,ARGC,FNR,FS,NF,NR,OFS,ORS,RS}'  log.txt

# 输出结果如下
FILENAME ARGC  FNR   FS   NF   NR  OFS  ORS   RS
---------------------------------------------
log.txt    2    1         6    1
log.txt    2    2         4    2
log.txt    2    3         3    3 
```

### `awk`脚本
`awk`脚本就是最开始的定义中的`script`部分。展开如下：
```shell
awk 'BEGIN{commands} pattern {commands} END{commands}' file
```

在上面的介绍中，显然script部分只包含 pattern部分和中间的{commands}部分。三个commands部分的说明如下：

- BEGIN{ 这里面放的是执行前的语句 }
- {这里面放的是处理每一行时要执行的语句}
- END {这里面放的是处理完所有的行后要执行的语句 }

假设有这样的一个学生成绩文件 score.txt：
```
Marry   2143 78 84 77
Jack    2321 66 78 45
Tom     2122 48 77 71
Mike    2537 87 97 95
Bob     2415 40 57 62
```

现在需要写一个awk脚本计算每个学生的总成绩，那么可以写下面的`awk`脚本 cal.awk ：
```shell
#!/bin/awk -f
# 运行前执行下面命令
BEGIN {
    math = 0
    english = 0
    computer = 0
 
    printf "NAME    NO.   MATH  ENGLISH  COMPUTER   TOTAL\n"
    printf "---------------------------------------------\n"
}
# 运行中，每一行执行下面命令
{
    math+=$3
    english+=$4
    computer+=$5
    printf "%-6s %-6s %4d %8d %8d %8d\n", $1, $2, $3,$4,$5, $3+$4+$5
}
# 运行后执行下面命令
END {
    printf "---------------------------------------------\n"
    printf "  TOTAL:%10d %8d %8d \n", math, english, computer
    printf "AVERAGE:%10.2f %8.2f %8.2f\n", math/NR, english/NR, computer/NR
}
```

运行下面命令：
```shell
awk -f cal.awk score.txt

# 执行结果如下：

NAME    NO.   MATH  ENGLISH  COMPUTER   TOTAL
---------------------------------------------
Marry  2143     78       84       77      239
Jack   2321     66       78       45      189
Tom    2122     48       77       71      196
Mike   2537     87       97       95      279
Bob    2415     40       57       62      159
---------------------------------------------
  TOTAL:       319      393      350
AVERAGE:     63.80    78.60    70.00
```

另外，在`awk`脚本中，可以像C语言那样使用`if`, `else`, `while`, `for`, `break`, `continue`等流程控制语法，甚至能够使用数组。
```shell
{
	users[1]="Bob"
	users[2]="Jim"
	users["three"]="Tim"
	for ( u in users ) {
		print users[u]
		if ( u == "Bob" ) {
			print "  good"
		}
	}
}
```

## 一些实用的`awk`命令

### 计算文件大小
```shell
ls -l *.txt | awk '{sum+=$6} END {print sum}'
```

### 从文件中找出长度大于80的行
```shell
awk 'length>80' log.txt
```

### 过滤出nginx日志中状态码不是200的请求
```shell
awk '$9 !~ /200/ {print $0}' access.log
```

### 统计访问时间大于5mm的URL
```shell
awk '$NF>5 {print $0}' access.log|awk '{print $12}'|awk -F? '{print $1}
```