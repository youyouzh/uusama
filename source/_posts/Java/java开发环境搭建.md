---
title: Java开发环境搭建
tags:
  - Java
url: 299.html
id: 299
categories:
  - 程序开发语言
  - Java
date: 2017-08-12 22:27:26
---

Windows平台Java开发环境的搭建比较简单，总的来时就是JDK安装，环境变量配置，验证安装以及相关IDE安装。

## Java JDK安装

官网JDK下载地址：[http://www.oracle.com/technetwork/java/javase/downloads/index.html](http://www.oracle.com/technetwork/java/javase/downloads/index.html) 选择适合自己Windows的包，看64位还是32位，下载完之后一路下一步即可安装完成。 

[![JavaJDK下载页面](http://uusama.com/wp-content/uploads/2017/08/2017081907345146.png)](http://uusama.com/wp-content/uploads/2017/08/2017081907345146.png)

## 配置环境变量

配置位置：右键桌面我的电脑 -> 属性 -> 高级系统设置 -> 环境变量，在打开的窗口中系统变量那一个区域下面添加变量，变量名大小写都可以 

- 变量名：JAVA_HOME 
    变量值：C:\Devlope\Java\jdk1.8.0_121 （实际Java安装的位置） 
- 变量名：CLASSPATH 
    变量值：.;%JAVA_HOME%\lib;%JAVA_HOME%\lib\tools.jar （注意：其中的 . 表示当前目录。; 表示或者） 
- 变量名：Path （在该该变量的最后面加上下面的值） 
    变量值：;%JAVA_HOME%\bin 

[![Java环境变量配置示意图](http://uusama.com/wp-content/uploads/2017/08/2017081907373886.png)](http://uusama.com/wp-content/uploads/2017/08/2017081907373886.png) 

**注意区分Path变量，需要配置的是系统变量那一栏里面的Path，而不是用户变量那一栏的Path**
 
 在命令行中输入命令 java -version; 如果正确显示当前的JDK版本，则说明安装成功。

## IDE安装

常用的Java开发IDE就是eclipse了， 下载地址：[http://www.eclipse.org/ide/](http://www.eclipse.org/ide/) 下载解压缩既可以使用。店家安装根目录下面的Eclipse.exe即可打开Eclipse，第一次需要设置工作空间路径和主题。选择Dark主题。

## Eclipse配置JDK

在Eclipse中选择菜单：Window -> Preferences -> Java -> Installed JREs，
点击添加，选择 Standard VM,点击Next，配置JRE的路径和名称，
点击 Finish 即可配置JDK，一般来说，如果已经安装了JDK，并且配置了JAVA_HOME环境变量的话，Eclipse会自动添加JDK。 

[![Eclipse配置JDK环境](http://uusama.com/wp-content/uploads/2017/08/2017082109504276.png)](http://uusama.com/wp-content/uploads/2017/08/2017082109504276.png)

## Eclipse字体大小设置

打开Eclipse选择工作空间目录之后，首先设置工作空间的字体编码和字体： Window -> Preferences -> General -> Workspace -> Text file encoding 设置编码为UTF-8. 

Window -> Preferences -> General -> Apearance -> Colors and Fonts -> Basic -> Edit 选择字体大小为自己喜欢的大小，这儿选择16.

[![eclipse设置字体大小](http://uusama.com/wp-content/uploads/2017/08/2017081907321177.png)](http://uusama.com/wp-content/uploads/2017/08/2017081907321177.png) 

设置完之后，可以创建一个简单的Helloword程序。 

File -> new -> Java Project， 输入Project 名称，比如test。点击Finish，则创建了一个test的Java项目。 

右键test项目，选择 New -> Class ，输入类名HelloWorld，并勾选 Which method stubs would you like to create 栏目下面的 public static void main(String\[\] srgs),点击Finish，即可创建一个包含main函数的类。

## Eclipse

Eclipse设置自动补全，一般来说，只有用户输入 . 才能出候选，或者使用快捷键 Alt+?，但是，有的时候，希望Eclipse能够实时地显示候选。 

设置方法：Eclipse菜单选择： windows -> Preferences -> Java -> Editor -> Content Asist，在Auto activation triggers for Java后面的文本框里只有一个“.”。

现在你将其改为 .abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ 即可。意思就是输入所有的字符都会触发候选。很方便呢！