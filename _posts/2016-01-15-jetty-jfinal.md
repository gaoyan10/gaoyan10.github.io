---
layout: post
title: JFinal jetty maven配置jsp支持
categories: [技术博客]
tags: [jetty, JFinal, maven, github, 博客]
---

## JFinal jetty maven配置jsp支持

#### 最近在折腾Jfinal框架，尝试用maven构建了JFinal项目，主要构建步骤参考这篇博文
[http://blog.dreamlu.net/blog/27](http://blog.dreamlu.net/blog/27)

[http://blog.dreamlu.net/blog/28](http://blog.dreamlu.net/blog/28)

[http://blog.dreamlu.net/blog/29](http://blog.dreamlu.net/blog/29)

#### 这一块不是解决jsp支持的重点，大概整理一下步骤如下
IDE为Eclipse javaEE，自带了maven plugin

 1. 选择新建一个Maven Project，选择workspace，选择webapp，具体为**org.apache.maven.archetypes, maven-archetype-webapp**，然后添加相关的GroupId等等。
 2. 为工程添加jar包，jar包可以来自maven仓库，也可以是本地的，当时maven库最新的Jfinal jar包为2.1，我选择使用了本地的jfinal-with-src.jar的jar包，maven里还要依赖jetty-server:8.18，为了方便看一下源码（maven应该也可以使用这种带源码的jar包吧，这里没有试）
 3. 选择项目的properties--Project Facets，勾选**Dynamic Web Module**
 4. 上面推荐的博文里说，这时会出现webContent，用tomcat时会使用这个目录，这里要删除掉该目录，在Deploy Path下添加webapp的目录。我构建时可能因为Eclipse版本问题，没有遇到这些问题，这里附一张我构建的工程目录截图吧。![这里写图片描述](http://img.blog.csdn.net/20160115132921100)
 ![这里写图片描述](http://img.blog.csdn.net/20160115132949632)
 
 5. 代码和配置文件对应在java和resources目录下。新建一个类加入main方法，使用Jfinal配置后run project as Maven install，然后run project as JavaApplication就可以了。
 

```
package com.yan.commonserver;

import com.jfinal.core.JFinal;

public class Main {
	public static void main(String[] args) {
		JFinal.start("src/main/webapp", 8080, "/", 10);
	}
}

```


----------

#### 接下来可以说我在运行后遇到的问题了。上面的博客示例中webapp下面是一个index.html，访问8080端口后一切成功，但我的目录下默认是一个index.jsp，运行后提示如下：

![这里写图片描述](http://img.blog.csdn.net/20160115133722550)

#### google后得知jetty-server需要配置jsp，单独添加一个jar包。

#### 具体是这篇文章，其中被选择的答案支持数是0，但确实有效。
> http://stackoverflow.com/questions/26217143/embedded-jetty-server-no-jsp-support-for-did-not-find-org-apache-jasper-ser

在pom.xml中添加jetty-jsp，发现运行不过。

```
<dependency>
    	<groupId>jetty</groupId>
    	<artifactId>jsp</artifactId>
    	<version>2.1-6.0.2</version>
</dependency>
```
因为jetty-jsp依赖其他的包，他们的依赖关系如下

> http://www.findjar.com/jar/jetty/jsp/2.1-6.0.0/jsp-2.1-6.0.0.jar.html;jsessionid=66D9E28B6032045C93773B7AE1227F43#

![这里写图片描述](http://img.blog.csdn.net/20160115134256544)
除了core，依次在maven中添加依赖的jar包。
终于获得运行通过。
最终的pom.xml配置如下

```
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
  <modelVersion>4.0.0</modelVersion>
  <groupId>com.yan.commonserver</groupId>
  <artifactId>server</artifactId>
  <packaging>war</packaging>
  <version>0.0.1-SNAPSHOT</version>
  <name>server Maven Webapp</name>
  <url>http://maven.apache.org</url>
  <dependencies>
    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>3.8.1</version>
      <scope>test</scope>
    </dependency>
    <dependency>
    	<groupId>com.jfinal</groupId>
    	<artifactId>jfinal</artifactId>
    	<version>2.1</version>
    	<scope>system</scope>
    	<systemPath>${basedir}/lib/jfinal-2.1-bin-with-src.jar</systemPath>
    </dependency>
    <dependency>
    	<groupId>com.jfinal</groupId>
    	<artifactId>jetty-server</artifactId>
    	<version>8.1.8</version>
    </dependency>
    <dependency>
    	<groupId>jetty</groupId>
    	<artifactId>jsp</artifactId>
    	<version>2.1-6.0.2</version>
    </dependency>
    <dependency>
    	<groupId>jetty</groupId>
    	<artifactId>jsp-api</artifactId>
    	<version>2.1-6.0.2</version>
    </dependency>
    <dependency>
    	<groupId>jetty</groupId>
    	<artifactId>jetty-util</artifactId>
    	<version>6.0.2</version>
    </dependency>
    <dependency>
    	<groupId>jetty</groupId>
    	<artifactId>servlet-api</artifactId>
    	<version>2.5-6.0.2</version>
    </dependency>
    <dependency>
    	<groupId>ant</groupId>
    	<artifactId>ant</artifactId>
    	<version>1.7.0</version>
    </dependency>
    
  </dependencies>
  <build>
    <finalName>server</finalName>
  </build>
</project>

```
这篇文章里可以提供了一条解决思路，还没看完，留存一下。

> https://wiki.eclipse.org/Jetty/Howto/Configure_JSP#Jetty_Maven_Plugin