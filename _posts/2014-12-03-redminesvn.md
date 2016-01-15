---
layout: post
title: redmine中配置svn提交自动关联,操作问题
categories: [调试部署]
tags: [redmine,svn]
---


##1. 背景
在项目开发过程中，经常会面这样的问题

  1. 开发的功能究竟是谁负责的？
  2. 什么时候开发的功能？
  3. 功能对应的代码有哪些，在哪？（关系到工作量评估）
  4. 有那些人参与了功能的开发和bug维护？
  5. 如何减少开发，测试的沟通成本？快速反馈问题？
  6. 在上述几个问题的基础上如何做代码审查？
 
观察发现，大部分互联网小公司上述问题都处理的不是很好，导致开发比较混乱，责任不明确。沟通成本比较高，开发人员做大量与开发无关的事情。

在比较了现在流行的各种协同办公云服务，管理平台。我发现其实服务更关注的是抽象的问题，对开发具体的细节，文档，bug维护并没有很好的管理。比较适合小型精英开发团队，销售，运营的协作。针对我们公司的情况，最终我选择了采用redmine作为管理平台。原因就不说了。和主题无关。


###1.1 redmine安装与配置

####1.1.1 系统
采用的是Debian7.x系列

####1.1.2 redmine版本
从2.4.3升级到2.5.2到现在2.6.0
[安装][installredmine]和升级过程主要根据官方文档。一路很顺利。

####1.1.3 svn安装
随意google都有的。确保最后环境是
![版本库配置][subversion]


##2. redmine与svn配置

###2.1 [redmine中SVN的配置](id:redminesvnconfig)

1张图片就足够说明了。
![参考如下配置][redminesvnconfig]

###2.2 svn的post-commit配置
这里主要是为了配置svn提交代码时候自动向redmine服务器发送消息，通知redmine服务器对问题列表和代码进行关联。	在post-commit里面添加

	curl "http://<redmine url>/sys/fetch_changesets?key=<your service key>"

>此处的 ``` <your service key> ``` 是在[2.1配置](#redminesvnconfig)里面被蓝色标准的**API Key**
在实际使用当中发现效果并不理想，会导致redmine服务器访问比较慢。经过源码分析得知是对所有项目进行了分析，因此只需要在后面修改为针对指定项目进行通知就好了。把post-commit中的代码改成

	curl "http://<redmine url>/sys/fetch_changesets?key=<your service key>&id=51"
	
>``` id=51 ``` id的值可以通过在redmine的数据库中针对项目查询得到。

	select * from redmine.projects where name like '%<your project name>%'
	
>此处的``` <your project name> ``` 为你的项目的名称

##3. 使用

###3.1 [mac OS X](id:macconfig)	

我个人开发是在mac下面，因此着重以mac演示为主

####3.1.1 phpstorm中配置redmine任务管理
在phpstorm中打开配置选项参考如下配置
![phpstorm中配置redmine服务][phpstormredmineconfig]

我的个人习惯是改完1个问题以后完整的提交，因此提交信息默认我会配置成这样
![phpstorm中配置commit内容][phpstormredminecommit]

####3.1.2 phpstorm中切换到指定的任务
phpstorm中打开任务如下图

![phpstorm中open task][phpstormopentask]

在打开的关联到我的任务中选择指定的任务

![phpstorm中choose task][phpstormchoosetask]

####3.1.3 phpstorm中commit项目代码
代码写完提交时候如下图所示
![phpstorm中commit code][phpstormcommitcode]

####3.1.4 redmine相关问题状态

提交以后刷新对应编号的问题，可以看到问题详细如下

![redmine中关联svn的问题][redminerefssvn]

>蓝色部分为隐私
红色部分为关联到svn中指定代码

###3.2 windows
windows如果用phpstorm进行开发，配置和[mac OS X](#macconfig)一样。

如果采用的是小乌龟可以参考[tortoise redmine plugin][tortoiseredmineplugin]进行配置，**要翻墙才能访问**。

##4. 其他

1. [jetbrains](https://www.jetbrains.com)公司的其他产品都可以采用这个配置
2. 本篇章主要以SVN为主要，git配置其实是一样的。


[installredmine]:http://www.redmine.org/projects/redmine/wiki/RedmineInstall
[subversion]:http://watsy0007githubio.qiniudn.com/3AE3CDD3-4399-4B8D-ADBD-915E00AAB5ED.png?imageView/2/w/640/q/90
[redminesvnconfig]:http://watsy0007githubio.qiniudn.com/F58A2E25-C839-4DFB-AEFF-CC39035B4261.png?imageView/2/w/640/q/90
[phpstormredmineconfig]:http://watsy0007githubio.qiniudn.com/9C04BEB7-62B2-42AB-9CB0-24C3D29E98A9.png?imageView/2/w/640/q/90
[phpstormredminecommit]:http://watsy0007githubio.qiniudn.com/EFCF2421-E6AD-4412-8D9B-955DE84EDC98.png?imageView/2/w/640/q/90
[phpstormopentask]:http://watsy0007githubio.qiniudn.com/08DFF96E-07D9-4D74-AF7B-AA33A2771B4A.png?imageView/2/w/640/q/90
[phpstormchoosetask]:http://watsy0007githubio.qiniudn.com/74C3E571-7E11-472C-9FD3-398A7BDF3F97.png?imageView/2/w/640/q/90
[phpstormcommitcode]:http://watsy0007githubio.qiniudn.com/1ED87526-6E43-4770-A296-885BF48F5F84.png?imageView/2/w/640/q/90
[redminerefssvn]:http://watsy0007githubio.qiniudn.com/05B2BD91-F1F5-431A-87FA-3A751CBAB3F4.png?imageView/2/w/640/q/90
[tortoiseredmineplugin]:https://code.google.com/p/turtlemine/?redir=1