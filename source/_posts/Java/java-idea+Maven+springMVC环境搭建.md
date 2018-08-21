---
title: IDEA+Maven+SpringMVC环境搭建
tags:
  - Java
url: 334.html
id: 334
categories:
  - 程序开发语言
  - Java
date: 2017-08-20 16:52:38
---

Eclipse下配置的文章参考：[http://uusama.com/323.html](http://uusama.com/323.html)

## IntelliJ IDEA下载安装和激活

### 下载

Java主流的开发IDE除了Eclipse意外，还有IntelliJ IDEA，个人来说，比较喜欢IDEA，官网下载地址：[https://www.jetbrains.com/idea/download/#section=windows](https://www.jetbrains.com/idea/download/#section=windows)，

选择下载 Ultimate 的 EXE版本用于开发Java Web，因为ZIP下载解压后还需要手动配置环境变量等。大概有500多M左右。 下载完成之后一路安装下一步，填写安装路径即可。

### 激活

其实可以购买正版，不算太贵，激活码获取地址：[http://idea.lanyus.com/](http://idea.lanyus.com/)。 

当然也可以使用奇怪的方法激活，打开软件以后，需要填写激活方式，选择输入下面其中的激活网址： http://intellij.mandroid.cn/ http://idea.imsxm.com/ http://idea.iteblog.com/key.php

## IntelliJ IDEA配置Tomcat

IDEA菜单选择：Run -> Edit Configurations，展开Default，选择 Tomcat Server -> Local，在右边的 Application Server 点击Configure，选择Tomcat安装的根目录，还有Tomcat的名称，点击右下角的Apply即可。

[![IntelliJ IDEA Tomcat配置](http://uusama.com/wp-content/uploads/2017/08/2017082006494825.png)](http://uusama.com/wp-content/uploads/2017/08/2017082006494825.png) 

在配置界面中，注意到Deployment的那一栏，点击 + ，选择要部署的项目。

## IntelliJ IDEA配置JDK

在菜单中选择：File -> Project Structure -> Platform Setting -> SDKs，点击 + 号，选择JDK，在弹出的对话框中选择DK的安装根目录，点击OK即可。可以重复这个步骤配置多个JDK。 

[![IntelliJ IDEA配置JDK](http://uusama.com/wp-content/uploads/2017/08/2017082006561675.png)](http://uusama.com/wp-content/uploads/2017/08/2017082006561675.png)

## IntelliJ IDEA配置Maven

从菜单选择：File -> Setting，Build, Execution, Deployment展开，Build Tools 展开，点击Maven，配置Maven home directory，选择Maven的安装根目录即可配置成功。 

[![IntelliJ IDEA配置Maven](http://uusama.com/wp-content/uploads/2017/08/2017082007050439.png)](http://uusama.com/wp-content/uploads/2017/08/2017082007050439.png)

## IntelliJ IDEA创建SpringMVC项目

### 创建Maven项目

从菜单选择：File -> New -> Maven，选择 org.apache.maven.archetypes:maven-archetype-webapp 这个脚手架，然后输入 GroupID 和 ArtifactID 后下一步，选择本地Maven环境。 

[![IntelliJ IDEA上创建Maven Spring MVC项目](http://uusama.com/wp-content/uploads/2017/08/2017082007133535.png)](http://uusama.com/wp-content/uploads/2017/08/2017082007133535.png) 

点击创建，需要联网下载脚手架相关的文件，看网络情况，所以需要等一会儿。 创建完之后，需要标记src目录为source，选择菜单：File -> Project Structure -> Project Settings -> Modules，标记src目录为source。 

[![IDEA标记SRC文件夹](http://uusama.com/wp-content/uploads/2017/08/2017082008074858.png)](http://uusama.com/wp-content/uploads/2017/08/2017082008074858.png)

- Sources 一般用于标注类似 src 这种可编译目录。只有 Sources 这种可编译目录才可以新建 Java 类和包
- Tests 一般用于标注可编译的单元测试目录。
- Test Resources 一般用于标注单元测试的资源文件目录。
- Resources 一般用于标注资源文件目录。资源目录下的文件是会被编译到输出目录下的。
- Excluded 一般用于标注排除目录。。被排除的目录不会被 IntelliJ IDEA 创建索引，相当于被 IntelliJ IDEA 废弃，该目录下的代码文件是不具备代码检查和智能提示等常规代码功能。

标注完成之后，建立如下图所示的目录，在这儿创建包就相当于创建文件夹。 [![IDEA SpringMVC项目结构](http://uusama.com/wp-content/uploads/2017/08/2017082008184588.png)](http://uusama.com/wp-content/uploads/2017/08/2017082008184588.png)

### 配置Maven

修改Maven配置文件poe.xml添加SpringMVC依赖。
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

更新完pom.xml文件后，idea应该会自动下载相应的jar包（可能需要vpn），如果没有自动下载的话，可以右键poe.xml选择：Maven ->Reimport 按钮进行项目的重新载入。

载入之后，查看External Libaraies目录中是否有SpringMVC添加的依赖。如果有，则说明Maven依赖配置成功。

### 配置 web.xml

maven默认生成的 web.xml 版本是2.3的，所以有些配置节点 IDEA 会识别不出来，可以重新添加一个3.0版本或者3.1版本的。
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
还需要在WEB-INF目录下新建一个 spring-mvc.xml 文件指定spring servlet的配置：
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

### 创建controller

在刚才创建的包：com.uusama.controller 下新建一个控制器类 HelloWorldController。
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
### 创建视图

在WEB-INF的views目录下新建一个jsp视图文件：hello.jsp
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

## 配置Tomcat运行环境

在菜单中选择：Run -> Edit Configurations，点击左上角的 + ，添加Maven和Tomcat，输入相应的名称 

[![IDEA添加运行时配置](http://uusama.com/wp-content/uploads/2017/08/2017082008430244.png)](http://uusama.com/wp-content/uploads/2017/08/2017082008430244.png) 

另外还需要在 Deployment 那一栏，将我们的项目 SpringMVCDemo添加到 Deploy at the server startup。 

[![IDEA配置SpringMVC运行Deployment](http://uusama.com/wp-content/uploads/2017/08/2017082008505470.png)](http://uusama.com/wp-content/uploads/2017/08/2017082008505470.png) 

最后点击运行，如果没有出错，IDEA会自动在浏览器中打开构建的首页。将会输出首页的HelloWorld！至此，IDEA+Maven+SpringMVC配置完成。