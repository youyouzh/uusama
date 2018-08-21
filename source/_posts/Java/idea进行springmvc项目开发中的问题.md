---
title: IDEA进行SpringMVC项目开发中的问题
tags:
  - IDEA
  - Java
url: 369.html
id: 369
categories:
  - 程序开发语言
  - Java
date: 2017-09-26 11:13:15
---

使用Eclipse一段时间了，Eclipse各方面确实挺好用的，但是写jsp页面的时候就有些尴尬了，好些JSTL标签的解析有问题，特别是标签内嵌标签的时候，再加上以前一直使用PHPStorm进行开发，所以转用IDEA。在使用的时候，遇到的问题记录一下。

## IDEA导入Maven项目

IDEA选择菜单：File -> Open，直接打开Maven项目的根目录就可以导入Maven项目，IDEA会自动识别 poe.xml 文件，然后进行Maven构建。 

真的是很方便。而且因为我这个项目里面包含了 .svn 目录，IDEA自动帮我把项目对SVN进行了对比，对于修改了没有提交的文件，会标为蓝色。 

导入项目之后，需要配置Tomcat和Maven，才能让项目跑起来，详细可以参看：[IDEA+Maven+SpringMVC环境搭建](http://uusama.com/334.html)。 

这儿简单提一下。选择菜单：Run -> Edit Configureation，在弹出的模态框中，点击左上角的 + 号，添加Maven和Tomcat Server，然后配置Tomcat Server的 Deployment 那一项，配置发布的 war 包名称。 

[![IDEA Tomcat配置](http://uusama.com/wp-content/uploads/2017/09/2017092603172322.png)](http://uusama.com/wp-content/uploads/2017/09/2017092603172322.png) 

注意到右边的 Application context，这儿配置项目的访问上下文，也就是访问地址中包含项目名前缀。 

一般在Eclipse中，默认的访问地址是： http://localhost:8080/ProjectName/index，这种形式，会在host后面包含项目名，IDEA默认不包含项目名，如果要加上，可以配置Application context这一项为项目名即可。 

配置好之后，直接可以run了，这个时候出问题了不要紧，看一下output报错的原因，然后去进一步排查，一般都是因为程序里面错误导致的，

这儿提一下，如果测试用例中有错误，Tomcat会启动失败，修复错误即可。

## IDEA中加载Mybatis @Autowired报错

在Eclipse中，Controller中加载Mybatis的Dao层Mapper文件是不会报错的，也能运行，但是在IDEA中，形如：
```java
@Autowired
private GameConfigMapper gameConfigMappper;
```
会报错：`could not autowire`。 

一方面要检查： File -> ProjectStructure 中的Modules配置中，是否正确加载了Spring的 Application Context 配置和Bean配置，

一般这儿是不会出错的，IDEA会自动识别并加载SpringMVC的配置。 千万不要听网上所说的什么把spring的error改成warnings和将项目从spring里删除什么的，这些都是治标不治本。 

根本的原因是因为IDEA检查比Eclipse严格，在Mybatis的Dao层，需要加入注解 @Repository，只要添加这个注解，IDEA就不会再报无法加载Bean的错误了。