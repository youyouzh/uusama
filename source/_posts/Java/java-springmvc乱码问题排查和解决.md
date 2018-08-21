---
title: Java SpringMVC乱码问题排查和解决
tags:
  - Java
url: 416.html
id: 416
categories:
  - 程序开发语言
  - Java
date: 2017-10-30 21:55:59
---

Java是 Unicode 编码的，稍微不注意，就会出现乱码的问题，乱码的根本原因就是对文本进行编码的时候和解码的时候，所使用的编码字符集不一致导致的。

像SpringMVC搭建的Web服务这类事，输入和输出在两个完全不同的环境中的情况，稍微不注意就会出现问题，这种MVC模式的乱码问题，一定要**先定位乱码出现的位置，然后针对出现位置前后两个环境的编码配置差异，检查问题**。

页面乱码情况
------

如果页面出现乱码，一般情况下是页面没有设置正确的编码格式，请操作下面第 3 点。

Controller层接受数据乱码
-----------------

可以使用 `System.out.println(request.getCharacterEncoding());` 在控制处打印接收到的参数的编码格式是否正确。解决方案如下：

1.  `ii：request.setCharacterEncoding(``"UTF-8"``);` 设置编码格式。
2.  `String str=newString((request.getParameter(``"bigQuestionTypeName"``)).getBytes(``"iso-8859-1"``),``"utf-8"``)；` 强行改变编码，这种方法治标不治本，不推荐。
3.  检查Spring配置文件中是否添加编码过滤，详细参照一下第 2 条。
4.  重新启动服务器，重新启动IDE，重新在IDE上配置服务器。

数据库保存乱码
-------

无论是Mybatis还是Hibernate，他们连接数据库的方式还是JDBC，所以要首先检查JDBC连接的URL是否指定正确的编码：
```config
useUnicode=true&amp;characterEncoding=UTF-8

<property name="url" value="jdbc:mysql://${ip}:${port}/${database}?useUnicode=true&amp;characterEncoding=UTF-8&amp;zeroDateTimeBehavior=convertToNull&amp;autoReconnect=true&amp;failOverReadOnly=false" />
```

另外，还需要检查数据库的相关配置，已经使用的表的相关配置，是否编码正确，详细参考第 5 点。

Spring Boot 使用RedisTemplate 存储键值出现乱码
------------------------------------

最近使用spring-data-redis RedisTemplate 操作redis时发现存储在 redis 中的 value 不是设置的string值，前面还多出了许多类似\\xac\\xed\\x00\\x05t\\x00这种字符串。 spring-data-redis的RedisTemplate<K, V>模板类在操作redis时默认使用JdkSerializationRedisSerializer来进行序列化，如下
```java
private static RedisTemplate<String, Object> redisTemplate;

@Autowired(required=false)
public void setRedisTemplate(RedisTemplate<String, Object> redisTemplate) {
     RedisSerializer<String> stringSerializer = new StringRedisSerializer();
     redisTemplate.setKeySerializer(stringSerializer);
     redisTemplate.setValueSerializer(stringSerializer);
     redisTemplate.setHashKeySerializer(stringSerializer);
     redisTemplate.setHashValueSerializer(stringSerializer);
     this.redisTemplate = redisTemplate;
}
```
或者可以使用 StringRedisTemplate 代替 RedisTemplate。

乱码问题需要注意的点
----------

### 1. 确认文件的编码

如果使用的是 Eclipse 软件进行开发，建议把工作空间的字符集设为 UTF-8。IDEA默认就是UTF-8。 

菜单导航栏上Window-->Preferences 打开"首选项"对话框，在左侧导航树，导航到 General-->Workspace，默认是GBK，设置为UTF-8。 确认工作空间的编码以后，还需要注意文件的编码是UTF-8。 

导航栏window-->preferences，打开"首选项"对话框，左侧导航树，导航到 Genera-->Content Types，右边找到要修改的文件的类型，如 *.java，在下面的Default encoding，输入框中输入UTF-8->Update->OK

### 2. 确认添加编码过滤

确认在SpringMVC的配置文件中是否添加编码字符拦截配置，建议把该配置放在 xml 文件开头，以免因为拦截顺序没有生效。如在 web.xml 中添加如下配置：
```xml
<filter>
    <filter-name>CharacterEncodingFilter</filter-name>
    <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
    <init-param>
        <param-name>encoding</param-name>
        <param-value>UTF-8</param-value>
    </init-param>
    <init-param>
        <param-name>forceEncoding</param-name>
        <param-value>true</param-value>
    </init-param>
</filter>
<filter-mapping>
    <filter-name>CharacterEncodingFilter</filter-name>
    <url-pattern>/*</url-pattern>
</filter-mapping>
```

### 3. 确认 jsp 文件头部指定编码

对于jsp的页面，一定要在开头制定编码格式，如果没有制定编码，非常容易导致道Controller的数据解析乱码。
```jsp
<%@page language="java" contentType="text/html; charset=utf-8"pageEncoding="utf-8"%>
```
### 4. 确认Tomcat服务器指定编码

如果是Tomcat服务器，需要检查所使用的Tomcat服务器配置文件： server.xml 文件中是否指定相同的编码格式。 在 server.xml 文件中，找到节点 <Connector port="8080" ...>，添加编码格式如下：

```xml
<Connector port="8080" protocol="HTTP/1.1" connectionTimeout="20000" redirectPort="8443" URIEncoding="UTF-8" useBodyEncodingForURI="true"/>
```
**注意：如果没有生效，需要重启Tomcat服务器，甚至重启IDE。**

### 5. 确认数据库编码

另外，还需要确认数据库的编码是否正确，如果使用的是MySQL，一般默认是 UTF-8，如果出现保存进入数据库之后乱码的情况，就要检测数据库的编码是否正，包括全局MySQL数据库编码设置，当前数据库配置，当前数据表配置，当前数据表字段配置。 下面给出MySQL的配置：

```sql
# 设置连接，查询结果等编码
SET character_set_client='utf-8'; 
SET character_set_connection='utf-8';
SET character_set_results='utf-8';
setnames 'utf-8';   # 设置数据库编码

# 查看数据库编码格式
show variables like 'character_set_%';
```