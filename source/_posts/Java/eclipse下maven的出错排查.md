---
title: Eclipse下Maven的出错排查
tags:
  - Eclipse
  - Java
  - Maven
url: 355.html
id: 355
categories:
  - 程序开发语言
  - Java
date: 2017-09-12 17:17:26
---

## Maven工程JDK版本和本地JDK版本不一致

### 问题描述

在导入，构建或者新建Maven工程的时候，会出现Maven工程的JDK版本和本地安装的JDK版本不一致的情况。

### 修复方法

可以通过修改Maven的配置文件 ：settings.xml 的构建JDK版本配置。位于Maven安装路径的 conf 文件夹中。 修改profiles 节点，指定构建的jdk版本。

```xml
<profile>
    <id>jdk-1.8</id>
    <activation>
        <activeByDefault>true</activeByDefault>
        <jdk>1.8</jdk>
    </activation>
    <properties>
        <maven.compiler.source>1.8</maven.compiler.source>
        <maven.compiler.target>1.8</maven.compiler.target>
        <maven.compiler.compilerVersion>1.8</maven.compiler.compilerVersion>
    </properties>
</profile>
```

也可以指定当前Maven配置文件中的构建JDK版本实现。在Maven配置文件 pom.xml 中添加以下构建配置

```xml
<build>
    <plugins>
        <plugin>
            <!--指定构建版本 -->
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-compiler-plugin</artifactId>
            <version>3.0</version>
            <configuration>
                <source>${jdk.version}</source>
                <target>${jdk.version}</target>
                <encoding>utf-8</encoding>
            </configuration>
        </plugin>
    </plugins>
</build>
```

## 导入Maven工程时，查看Build Path，相关依赖包 missing

### 问题描述

在导入Maven工程的时候，命名lib中有相关的依赖包，但是项目的依赖工程报错，包jar包missing。

### 解决办法

查看 Navigator，菜单 Window -> Show Views -> Navitator，显示文件导航列表，删除项目的 .classpath 文件和 .project 文件，重新构建Maven工程，右键选择项目，Run As, Maven install，重新构建。该问题我遇到的情况是因为classpath中指定的jar包版本和Maven中指定的jar包版本不一致导致的。 因为Maven导致的问题，可以考虑使用Maven clean，然后Maven install，或者重启Eclipse，它们喜欢是不是抽风。

## Maven完整配置

下面给出我的一个SpringMVC+Mybatis的后台项目的完整Maven配置以供参考。其中properties节点定义变量，一般可以定义版本号，便于修改和升级。
```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
	<modelVersion>4.0.0</modelVersion>
	<groupId>com.idreamsky.api</groupId>
	<artifactId>ConfigService</artifactId>
	<packaging>war</packaging>
	<version>1.0</version>
	<name>ConfigService Maven Webapp</name>
	<url>http://maven.apache.org</url>

	<properties>
		<project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
		<spring.version>4.0.4.RELEASE</spring.version>
		<spring.redis.version>1.0.6.RELEASE</spring.redis.version>
		<slf4j.version>1.6.1</slf4j.version>
		<junit.version>4.11</junit.version>
		<jdk.version>1.8</jdk.version>

	</properties>

	<dependencies>
		<!-- 导入导出Excel -->
		<!-- https://mvnrepository.com/artifact/org.apache.poi/poi -->
		<dependency>
			<groupId>org.apache.poi</groupId>
			<artifactId>poi</artifactId>
			<version>3.10-FINAL</version>
		</dependency>
		<!-- https://mvnrepository.com/artifact/org.apache.poi/poi-ooxml -->
		<dependency>
			<groupId>org.apache.poi</groupId>
			<artifactId>poi-ooxml</artifactId>
			<version>3.10-FINAL</version>
		</dependency>
		<!-- https://mvnrepository.com/artifact/org.springframework.integration/spring-integration-rmi -->
		<dependency>
			<groupId>org.springframework.integration</groupId>
			<artifactId>spring-integration-rmi</artifactId>
			<version>4.0.4.RELEASE</version>
		</dependency>

		<!-- Spring Security Artifacts - START -->
		<!-- http://mvnrepository.com/artifact/org.springframework.security/spring-security-web -->
		<dependency>
			<groupId>org.springframework.security</groupId>
			<artifactId>spring-security-web</artifactId>
			<version>${spring.version}</version>
		</dependency>

		<!-- http://mvnrepository.com/artifact/org.springframework.security/spring-security-config -->
		<dependency>
			<groupId>org.springframework.security</groupId>
			<artifactId>spring-security-config</artifactId>
			<version>${spring.version}</version>
		</dependency>
		<!-- JSP API -->
		<!-- http://mvnrepository.com/artifact/javax.servlet.jsp/jsp-api -->
		<dependency>
			<groupId>javax.servlet.jsp</groupId>
			<artifactId>jsp-api</artifactId>
			<version>2.2</version>
			<scope>provided</scope>
		</dependency>
		<!-- MyBatis -->
		<dependency>
			<groupId>org.mybatis</groupId>
			<artifactId>mybatis</artifactId>
			<version>3.4.1</version>
		</dependency>
		<dependency>
			<groupId>org.mybatis</groupId>
			<artifactId>mybatis-spring</artifactId>
			<version>1.3.0</version>
		</dependency>
		<!-- MySQL JDBC driver -->
		<!-- http://mvnrepository.com/artifact/mysql/mysql-connector-java -->
		<dependency>
			<groupId>mysql</groupId>
			<artifactId>mysql-connector-java</artifactId>
			<version>5.1.34</version>
		</dependency>
		<!-- Apache Commons -->
		<dependency>
			<groupId>commons-lang</groupId>
			<artifactId>commons-lang</artifactId>
			<version>2.6</version>
		</dependency>
		<dependency>
			<groupId>commons-collections</groupId>
			<artifactId>commons-collections</artifactId>
			<version>3.2.1</version>
		</dependency>
		<dependency>
			<groupId>commons-pool</groupId>
			<artifactId>commons-pool</artifactId>
			<version>1.6</version>
		</dependency>
		<dependency>
			<groupId>com.alibaba</groupId>
			<artifactId>druid</artifactId>
			<version>0.2.12</version>
		</dependency>

		<!-- FlexJSON -->
		<dependency>
			<groupId>net.sf.flexjson</groupId>
			<artifactId>flexjson</artifactId>
			<version>2.1</version>
		</dependency>
		<dependency>
			<groupId>com.alibaba</groupId>
			<artifactId>fastjson</artifactId>
			<version>1.2.5</version>
		</dependency>

		<!-- Spring Framework -->
		<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>spring-beans</artifactId>
			<version>${spring.version}</version>
		</dependency>
		<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>spring-web</artifactId>
			<version>${spring.version}</version>
		</dependency>
		<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>spring-webmvc</artifactId>
			<version>${spring.version}</version>
		</dependency>
		<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>spring-core</artifactId>
			<version>4.1.1.RELEASE</version>
		</dependency>
		<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>spring-context-support</artifactId>
			<version>${spring.version}</version>
		</dependency>
		<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>spring-context</artifactId>
			<version>${spring.version}</version>
		</dependency>

		<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>spring-tx</artifactId>
			<version>${spring.version}</version>
		</dependency>
		<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>spring-jdbc</artifactId>
			<version>${spring.version}</version>
		</dependency>
		<dependency>
			<groupId>org.springframework.data</groupId>
			<artifactId>spring-data-redis</artifactId>
			<version>${spring.redis.version}</version>
		</dependency>

		<dependency>
			<groupId>commons-fileupload</groupId>
			<artifactId>commons-fileupload</artifactId>
			<version>1.3.1</version>
		</dependency>

		<dependency>
			<groupId>cglib</groupId>
			<artifactId>cglib</artifactId>
			<version>2.2.2</version>
		</dependency>
		<dependency>
			<groupId>junit</groupId>
			<artifactId>junit</artifactId>
			<version>${junit.version}</version>
			<scope>test</scope>
		</dependency>
		<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>spring-test</artifactId>
			<version>${spring.version}</version>
			<scope>test</scope>
		</dependency>
		<!-- Logging dependencies -->
		<dependency>
			<groupId>org.slf4j</groupId>
			<artifactId>slf4j-api</artifactId>
			<version>${slf4j.version}</version>
		</dependency>
		<dependency>
			<groupId>org.slf4j</groupId>
			<artifactId>slf4j-log4j12</artifactId>
			<version>${slf4j.version}</version>
		</dependency>
		<dependency>
			<groupId>log4j</groupId>
			<artifactId>log4j</artifactId>
			<version>1.2.16</version>
		</dependency>

		<dependency>
			<groupId>javax.servlet</groupId>
			<artifactId>servlet-api</artifactId>
			<version>3.0-alpha-1</version>
		</dependency>

		<dependency>
			<groupId>javax.servlet</groupId>
			<artifactId>jstl</artifactId>
			<version>1.2</version>
			<scope>runtime</scope>
		</dependency>

		<dependency>
			<groupId>taglibs</groupId>
			<artifactId>standard</artifactId>
			<version>1.1.2</version>
		</dependency>

		<dependency>
			<groupId>org.codehaus.jackson</groupId>
			<artifactId>jackson-mapper-asl</artifactId>
			<version>1.9.13</version>
		</dependency>
		<dependency>
			<groupId>org.codehaus.jackson</groupId>
			<artifactId>jackson-core-asl</artifactId>
			<version>1.9.13</version>
		</dependency>
		<dependency>
			<groupId>com.fasterxml.jackson.core</groupId>
			<artifactId>jackson-core</artifactId>
			<version>2.8.0</version>
		</dependency>
		<dependency>
			<groupId>com.fasterxml.jackson.core</groupId>
			<artifactId>jackson-databind</artifactId>
			<version>2.8.0</version>
		</dependency>
		<dependency>
			<groupId>org.aspectj</groupId>
			<artifactId>aspectjweaver</artifactId>
			<version>1.8.9</version>
		</dependency>
	</dependencies>
	<build>
		<finalName>ConfigService</finalName>
		<resources>
			<!--编译之后包含xml -->
			<resource>
				<directory>src/main/java</directory>
				<includes>
					<include>**/*.xml</include>
				</includes>
			</resource>
			<resource>
				<directory>src/main/resources</directory>
				<includes>
					<include>**/*</include>
				</includes>
			</resource>
		</resources>
		<plugins>
			<plugin>
				<groupId>org.apache.maven.plugins</groupId>
				<artifactId>maven-compiler-plugin</artifactId>
				<version>3.0</version>
				<configuration>
					<source>${jdk.version}</source>
					<target>${jdk.version}</target>
					<encoding>utf-8</encoding>
				</configuration>
			</plugin>
		</plugins>
	</build>
</project>

```