---
title: Eclipse+Maven+SpringMVC环境搭建
tags:
  - Java
  - 工具
url: 323.html
id: 323
categories:
  - 程序开发语言
  - Java
date: 2017-08-19 23:42:19
---

关于Java环境配置和Eclipse的配置在这篇文章里面：[Java开发环境搭建](http://uusama.com/299.html)

## Tomcat服务器安装
### 下载和安装
Apache Tomcat官网：[http://tomcat.apache.org/](http://tomcat.apache.org/)，这儿选择Tomcat8.5.20版本，它支持JDK7.0之后的版本。 [![Tomcat下载](http://uusama.com/wp-content/uploads/2017/08/2017081912464788.png)](http://uusama.com/wp-content/uploads/2017/08/2017081912464788.png) 这儿下载Windows64位版本的zip文件，解压缩到 C:\\Devlope\\Java\\apache-tomcat-8.5.20 目录下。

### 文件目录结构
可以看到有一个RUNNING.txt的文件，这个文件里面详细的讲解了怎么启动tomcat。 [![Tomcat文件列表](http://uusama.com/wp-content/uploads/2017/08/2017081912575722.png)](http://uusama.com/wp-content/uploads/2017/08/2017081912575722.png)

- bin：存放一些启动运行Tomcat的可执行程序和相关内容。
- conf：存放关于Tomcat服务器的全局配置。
- lib：存放Tomcat运行或者站点运行所需的jar包，所有Tomcat上的站点共享这些jar包。
- wabapps：默认的站点根目录，存放网址的相关内容，可以更改。
- work：存放Tomcat服务器运行时需要的资源，比如Java文件编译后的class文件等。

在bin目录下面有一个 startup.bat 文件，如果正确的配置了JDK JAVA\_HOME运行环境，则双击即可启动Tomcat服务器，如果没有配置JDK的JAVA\_HOME变量，则弹出来的窗口就会一闪而过。Tomcat启动以后，在浏览器里面输入网址：http://localhost:8080/，可以看到Tomcat的介绍页面。 [![Tomcat服务启动后示例页面](http://uusama.com/wp-content/uploads/2017/08/2017081912553429.png)](http://uusama.com/wp-content/uploads/2017/08/2017081912553429.png) 注意到conf文件夹里面的配置项： server.xml，这个文件中可以配置网站的监听端口，主机名，网站根目录和添加虚拟主机等很多网站相关的信息。在Host节点中可以添加虚拟主机配置，可以参照文件里面的注释进行配置。 \[v\_blue\]在Tomcat里，可用于作为站点的文件夹必须有如下特点：拥有一个名为WEB-INF的子文件夹，该子文件夹下必须有一个名为web.xml的文件，而且该xml文件必须受约束与特定的DTD。\[/v\_blue\]

### 配置环境变量
最后还需要在Windows中添加环境变量： 变量名：CATALINA\_HOME 变量值：C:\\Devlope\\Java\\apache-tomcat-8.5.20 （Tomcat解压的目录） 然后在Path的后面添加下面一行：;%CATALINA\_HOME%\\bin

### Eclipse中配置Tomcat服务器
在Eclipse中选择：Window -> Preferences -> Server -> Runtime Environments -> Add，添加Tomcat8.5，选择Tomcat解压的目录还有JRE即可添加。 [![Eclipse Tomcat配置](http://uusama.com/wp-content/uploads/2017/08/2017081913413866.png)](http://uusama.com/wp-content/uploads/2017/08/2017081913413866.png)

## Maven配置
**一般来说，Eclipse里面已经自带了Maven，不需要再额外安装，下面下载安装的是Maven独立的构建工具！**

### Maven下载和安装
官方Maven下载地址：[http://maven.apache.org/download.cgi](http://maven.apache.org/download.cgi) 下载支持当前JDK的版本，下载编译过的bin Zip文件，解压到目录：C:\\Devlope\\Java\\apache-maven-3.5.0 添加Maven的环境变量： 变量名：MAVEN\_HOME 变量值：C:\\Devlope\\Java\\apache-maven-3.5.0 （刚才解压的目录） 然后在Path的后面添加下面一行：;%MAVEN\_HOME%\\bin

### 在Eclipse中配置Maven
在Eclipse中选择：Window -> Preference -> Maven -> Installation -> Add ，输入Maven安装的路径，添加安装的Maven。 在Eclipse中选择：Help -> Eclipse Marketplace 搜索Maven，选择哪个安装最多的。然后点击 Install，安装Maven插件。 [![Eclipse安装Maven](http://uusama.com/wp-content/uploads/2017/08/2017081914004448.png)](http://uusama.com/wp-content/uploads/2017/08/2017081914004448.png) 然后选择：Window -> Show View -> Other，在弹出来的对话款中查找 Maven，如果有，则说明安装成功。

### Maven联网在线添加依赖
为了能够通过Maven直接从网络上添加依赖，需要选择：Window -> Preferences -> Maven，勾选 Download repository index updates on startup。 然后选择：Window -> show View -> others，选择Maven Repositories，双击，会在Eclipse下方看到：Global Repositories，然后右键 rebuild index，更新索引，此过程需要联网更新。 右键Maven工程，选择Maven -> Add Dependicy 可以添加依赖。如果JRE版本不是当前版本，则需要remove然后再add。

### 创建Maven工程
在Eclipse中选择：File -> New -> Others -> Maven Project 新建 Maven 工程。 [![Maven创建项目](http://uusama.com/wp-content/uploads/2017/08/2017081914254985.png)](http://uusama.com/wp-content/uploads/2017/08/2017081914254985.png) group Id: 定义了项目属于哪个组，这个组往往和项目所在组织或公司相关联。 artifact Id: 定义了当前Maven项目在组中的唯一ID。 新建Maven项目之后，会需要等一会儿，项目结构如下图。 [![Maven项目结构](http://uusama.com/wp-content/uploads/2017/08/2017081914303193.png)](http://uusama.com/wp-content/uploads/2017/08/2017081914303193.png) 目录结构的含义如下：

- src/main/java：该目录主要放置java源代码；
- src/test/java：该目录主要用来存放测试代码；
- Maven Dependencies：这里主要放Maven管理的jar文件；
- target：用来存放Maven编译好的字节码文件；
- pom.xml：全称为Project Object Model，项目对象模型，定义了项目的基本信息，用于描述项目如何构建，声明项目依赖等。
- src：用来存放main和test中会使用到的其他文件等资源。

## 新建Maven SpringMVC项目
### 创建动态网页项目
在Eclipse中选择：File -> New -> Others -> Maven Project 新建 Maven 工程。注意这儿选择：org.apache.maven.archetypes maven-archetype-webapp [![Maven新建SpringMVC项目](http://uusama.com/wp-content/uploads/2017/08/2017081914400480.png)](http://uusama.com/wp-content/uploads/2017/08/2017081914400480.png) 右键新建的目录，选择Properties，可以设置项目相关的各种属性。 [![Maven-project-Properties](http://uusama.com/wp-content/uploads/2017/08/2017081914490129.png)](http://uusama.com/wp-content/uploads/2017/08/2017081914490129.png) Deployment Assembly：设置发布目录 Project Facets：设置项目类型，这儿勾选Dynamic Web Module，Java，JavaScript 添加Tomcat运行支持：配置项目属性 Java Build Path：Libraries -> Add Library -> Server Runtime -> Apache Tomcat v8.5

### Maven配置添加Spring依赖
在项目根目录下打开 poe.xml 文件，配置Maven依赖的包。这儿可以右键项目，选择Maven -> Add Dependency然后输入 org.springframework 查找，添加 spring 和 spring-mvc 两个依赖包，添加Spring依赖。Plugins节点的配置用于修改Maven默认构建JDK版本是1.5，修改为1.8.
```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4\_0\_0.xsd">
<modelVersion>4.0.0</modelVersion>
<groupId>com.uusama</groupId>
<artifactId>SpringMVCDemo</artifactId>
<packaging>war</packaging>
<version>0.0.1-SNAPSHOT</version>
<name>SpringMVCDemo Maven Webapp</name>
<url>http://maven.apache.org</url>
<dependencies>
  <dependency>
    <groupId>junit</groupId>
    <artifactId>junit</artifactId>
    <version>3.8.1</version>
    <scope>test</scope>
  </dependency>
  <dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-web</artifactId>
    <version>4.3.10.RELEASE</version>
  </dependency>
  <dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-webmvc</artifactId>
    <version>4.3.10.RELEASE</version>
  </dependency>
</dependencies>
<build>
  <finalName>SpringMVCDemo</finalName>
  <plugins>
    <plugin>
      <groupId>org.apache.maven.plugins</groupId>
      <artifactId>maven-compiler-plugin</artifactId>
      <version>3.6.0</version>
      <configuration>
        <source>1.8</source>
        <target>1.8</target>
        <encoding>${project.build.sourceEncoding}</encoding>
        <compilerArguments>
          <verbose />
          <bootclasspath>${java.home}/lib/rt.jar;${java.home}/lib/jce.jar</bootclasspath>
        </compilerArguments>
      </configuration>
    </plugin>
  </plugins>
</build>
</project>
```

### SpringMVC配置
添加SpringMVC特性需要配置 web.xml 文件，主要配置两处，一个是ContextLoaderListener，一个是DispatcherServlet，这两项都需要制定特定的 xml 配置文件。 ContextLoaderListener指定了IOC容器初始化的方法 DispatcherServlet则定义了mvc的相关内容，并配置拦截的url，web.xml文件配置如下

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://java.sun.com/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://java.sun.com/xml/ns/javaee
          http://java.sun.com/xml/ns/javaee/web-app\_3\_0.xsd"
         version="3.0">

<!--配置springmvc DispatcherServlet-->
<servlet>
  <servlet-name>springMVC</servlet-name>
  <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
  <init-param>
    <!-- 手动制定配置文件为  spring-mvc.xml -->
    <param-name>contextConfigLocation</param-name>
    <param-value>/WEB-INF/spring-mvc.xml</param-value>
  </init-param>
  <load-on-startup>1</load-on-startup>
  <async-supported>true</async-supported>
</servlet>

<servlet-mapping>
  <servlet-name>springMVC</servlet-name>
  <url-pattern>/</url-pattern>
</servlet-mapping>

<!-- 配置首页 -->
<welcome-file-list>
  <welcome-file>index.jsp</welcome-file>
</welcome-file-list>
</web-app>
```

另外，还需要在WEB-INF目录下新建一个文件：mvc-dispatcher-servlet.xml，配置内容如下：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:p="http://www.springframework.org/schema/p"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:mvc="http://www.springframework.org/schema/mvc"
       xmlns:task="http://www.springframework.org/schema/task"
       xsi:schemaLocation="
        http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans-4.2.xsd
        http://www.springframework.org/schema/context
        http://www.springframework.org/schema/context/spring-context-4.2.xsd
        http://www.springframework.org/schema/mvc
        http://www.springframework.org/schema/mvc/spring-mvc-4.2.xsd
        http://www.springframework.org/schema/task
        http://www.springframework.org/schema/task/spring-task-4.2.xsd">
    <!--指明 controller 所在包，并扫描其中的注解-->
    <context:component-scan base-package="com.uusama.controller" >
        <context:include-filter type="annotation" expression="org.springframework.stereotype.Controller"/>
    </context:component-scan>

    <!-- 静态资源(js、image等)的访问 -->
    <mvc:default-servlet-handler/>

    <!-- 激活基于注解的配置 @RequestMapping, @ExceptionHandler,数据绑定 ,@NumberFormat ,
    @DateTimeFormat ,@Controller ,@Valid ,@RequestBody ,@ResponseBody等  -->
    <mvc:annotation-driven />

    <!--ViewResolver 视图解析器-->
    <!--用于支持Servlet、JSP视图解析-->
    <bean id="jspViewResolver" class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <property name="prefix" value="/WEB-INF/views/"/>
        <property name="suffix" value=".jsp"/>
    </bean>
</beans>
```

### 创建简单的Helloworld示例
创建一个控制器，File -> New -> Class：包名和 mvc-dispatcher-servlet.xml 中指定的包名相同，这儿使用 uusama。创建一个HelloWorldController的类。

```java
package com.uusama.controller;

import org.springframework.stereotype.Controller;  
import org.springframework.web.bind.annotation.RequestMapping;  
import org.springframework.web.servlet.ModelAndView; 

// 告诉DispatcherServlet相关的容器， 这是一个Controller
@Controller 
public class HelloWorldController {
	
	// // 告诉DispatcherServlet相关的容器， 这是一个Controller
	@RequestMapping("/hello")
	public ModelAndView HelloWorld() {  
		
		String message = "Hello World, Spring MVC!";
		ModelAndView modelAndView = new ModelAndView();
		// 在视图中添加 message 的参数
		modelAndView.addObject("message", message);
		// 制定视图名称
		modelAndView.setViewName("hello");
		return modelAndView;
		// 还可以用下面的语句替代
		// return new ModelAndView("hello", "message", message);
	}
}
```
在WEB-INF目录下创建视图文件夹 views，新建一个视图：hello.jsp

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Maven-spring-demo</title>
</head>
<body>
<h2> This is my message: ${message} </h2>
</body>
</html>
```

## 运行效果
先右键项目，配置 Properties，查看Java Build Path 配置，Libraries是否添加正确的JRE和 Apache Tomcat v8.5，如果没有，则需要添加 Add Library，添加之后，还需要在 Order and Export 一栏勾选添加的库。 

然后右键项目， Run as -> Run on Server，运行跑起来，如果报错，很有可能是 web.xml 文件等配置不正确，或者 Build Path 配置不正确。检查一遍。 

最后可以在浏览器中看到亲切地 Hello World！输入http://localhost:8080/SpringMVCDemo/hello 可以看到：This is my message: ${message} Maven + SpringMVC + Tomcat 最简单的例子就完成了！