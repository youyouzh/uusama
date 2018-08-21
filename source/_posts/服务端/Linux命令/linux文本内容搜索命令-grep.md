---
title: Linux文本内容搜索命令-grep
tags:
  - Linux命令
url: 687.html
id: 687
categories:
  - 服务端
  - Linux
date: 2018-03-25 17:32:20
---

## 语法
Linux中grep命令的全称为：global search regular expression(RE) and print out the line, 全面搜索正则表达式并把行打印出来。是一种强大的文本搜索工具，它能使用正则表达式搜索文本，并把匹配的行打印出来。

下面是它的语法：
```shell
grep [OPTION]... PATTERN [FILE]...
```
grep可以从每个文件和标准输出中进行搜索。

匹配模式Pattern默认为一个基本的正则表达式（basic regular expression，BRE）

```shell
# 从给出的nginx日志文件中查找500错误
grep "500" access.log access-2.log access-3.log
```

可以指定多个文件，需要注意文件时反正匹配模式后面的。grep后面的匹配Pattern如果是简单字符串，可以不加引号。

## 正则表达式行为控制选项
grep命令提供了丰富的选项用来控制正则表达式的行为：

|选项|选项全称|选项含义|
|:-- |:-- |:-- |
|-E |--extended-regexp |使用扩展的正则表达式进行匹配 |
|-F |--fixed-strings |使用一套新行分离固定的字符串进行匹配 |
|-G |--basic-regexp |使用基本的正则表达式进行匹配 |
|-P |--perl-regexp |使用Perl的正则表达式进行匹配 |
|-e |--regexp=PATTERN |使用Pattern进行匹配 |
|-f |--file=FILE |从指定文件中读取匹配模式 |
|-i |--ignore-case |忽略大小写区别 |
|-w |--word-regexp |强制Pattern只匹配单个词 |
|-x |--line-regexp |强制Pattern只匹配一行 |
|-z |--null-data |空行算一行，匹配时不换行 |

`grep -F`相当于`fgrep`；`grep -E`相当于`egrep`
```shell
# 按照扩展的正则表达式匹配
grep -E "[1-9]+"
```

## 输出控制选项
grep命令提供了很多选项用来控制如何显示搜索到的结果：

|选项|选项全称|选项含义|
|:-- |:-- |:-- |
|-m |--max-count=NUM |只输出匹配的NUM行 |
|--color |--color=auto |标记匹配颜色 |
|-b |--byte-offset |输出匹配结果相对于所在行的偏移 |
|-n |--line-number |在匹配文本前输出行号 |
|-H |--with-filename |输出匹配的文件名 |
|-h |--no-filename |不输出匹配的文件名 |
|-o |--only-matching |只输出匹配的字符串 |
|-v |--invert-match |输出不匹配的行 |
|-A |--after-context=[LINE] |同时输出匹配行和匹配行之后[LINE]行的内容 |
|-B |--before-context=[LINE] |同时输出匹配行和匹配行之前[LINE]行的内容 |
|-C |--context=[LINE] |同时输出匹配行和匹配行前后[LINE]行的内容 |
|-q |--quiet |不输出任何结果 |
|-c |--count |只输出每个文件中匹配的**行数** |
|-Z |--null |输出匹配的文件名时，不在文件名后面输出':'等分隔符 |
|-L |--files-without-match |只输出不包含匹配的文件名 |
|-l |--files-with-matches |只输出包含匹配的文件名 |
|-T |--initial-tab |添加标签使得输出对齐 |

```shell
# 匹配'is'并标记出来，显示匹配行号和偏移
grep is demo.txt --color=auto -n -o -b -H

# 匹配'is'并统计匹配的行数
grep "is" demo.txt -c

# 搜索多个文件并查找匹配文本在哪些文件中
grep -l "text" file1 file2 file3...

# 计算不匹配的行
grep -v -v http access.log
```

上面的例子中， -b 和 -o 一般配合使用，标出匹配的字符和偏移。

## 匹配控制选项
grep中还有一些命令用于控制匹配时的行为，如是否匹配二进制文件等：

|选项|选项全称|选项含义|
|:-- |:-- |:-- |
|-a |--text |不要忽略二进制数据 |
|-r |--directories=recurse |递归搜索所有文件和子目录 |

当指定要查找的是目录而非文件时，必须使用`-d`参数，否则grep指令将回报信息并停止动作。

`-d`参数的值可为`read`, `skip`, `recurse`

- `read`表示读取
- `skip`表示跳过
- `recurse`表示递归