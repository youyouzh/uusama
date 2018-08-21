---
title: Java Mybatis的一些坑
tags:
  - Java
url: 356.html
id: 356
categories:
  - 程序开发语言
  - Java
date: 2017-09-12 18:00:47
---

Mybatis是Java中一个非常好用的数据库框架，这儿记录一下在使用过程中遇到的坑。官方中文文档地址：[http://www.mybatis.org/mybatis-3/zh/getting-started.html](http://www.mybatis.org/mybatis-3/zh/getting-started.html)

## 在Mybatis `mapping.xml`映射配置文件中使用大于`>`号小于号`<`

由于Mybatis的映射文件遵循xml文件的格式，所以不能使用像大于号或者小于号这样的xml文件特殊字符，需要使用转义字符代替。

| HTML转义字符序列 | 字符 | 含义 |
| :--  | :--  | :-- |
| &amp;lt; | < | 小于号  |
| &amp;gt; | > | 大于号  |
| &amp;amp; | & | 和  |
| &amp;apos; | ’ | 单引号  |
| &amp;quot; | “ | 双引号  |

可以使用符号进行说明，将此类符号不进行解析。

```sql
SELECT * FROM test WHERE 1 = 1 AND start_date  &amp;lt;= CURRENT_DATE AND end_date &amp;gt;= CURRENT_DATE
<![CDATA[ when min(starttime)<='12:00' and max(endtime)<='12:00' ]]>
```

## Mybatis中使用OGNL表达式test比较字符串

在Mybatis映射配置文件中，使用OGNL表达式test的时候，比较字符串时，需要调用 toString()方法保证 == 两边的值都是 String 类型。

```xml
<!-- 以下为错误写法,会抛NumberFormatException异常 -->
<if test="username == 'U'">
<!-- 正确写法如下两种 -->
<if test="username == 'U'.toString()">
<if test='username == "U"'>
```

## Mybatis实现WHERE IN查询

WHERE IN查询中，IN的参数是一个列表，需要传送一个列表参数，使用 foreach 实现。

```xml
<select id="selectPostIn" resultType="domain.blog.Post">
 SELECT * FROM POST P
 WHERE ID in
 <foreach item="item" index="index" collection="list"  open="(" separator="," close=")">
    #{item}
 </foreach>
</select>
```

当使用可迭代对象或者数组时，index是当前迭代的次数，item的值是本次迭代获取的元素。当使用字典（或者Map.Entry对象的集合）时，index是键，item是值。 \[v\_blue\]你可以将任何可迭代对象（如列表、集合等）和任何的字典或者数组对象传递给foreach作为集合参数。\[/v\_blue\]

## Mybatis插入数据的时候返回插入记录的主键id

在进行输入库插入的时候，如果我们需要使用已经插入的记录的主键，则需要返回刚才插入的数据的主键id。通过设置 insert 标签的 useGeneratedKeys 属性为 true 可以返回插入的记录的主键的id。

```xml
<insert id="User" useGeneratedKeys="true" keyProperty="id"> </insert>
```