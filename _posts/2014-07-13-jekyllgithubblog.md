---
layout: post
title: 利用jekyll在github中搭建博客
categories: [博客]
tags: [jekyll, github, 博客]
---

###简介
断断续续花了将近30小时终于把watsy0007.github.io部署起来了。  
部署的过程其实还是挺麻烦的。下面记录一下部署经历，新人可以少走弯路。	
欢迎各位[fork本博客代码][forkwatsy0007github]参考修改	

###支持内容
1. 命令行支持
	1. 创建一篇博客内容 	rake post title="watsy" category=codedevelop
	2. 上传代码			rake deploy msg="上传代码"
	3. 发布站点			rake publish msg="新增1篇blog"
2. 多说评论
3. 百度统计(google老被墙掉) 	

###安装步骤
#####1. 安装环境
mac系统  
linux系列也可以  
如果是windows，sorry，没有测试过  

#####0. 建议
直接fork我的代码，把中心放在写blog上，后期在慢慢修改自己喜欢的样式和添加功能

#####1. 安装jekyll
Mac下安装命令行

	xcode-select --install

如果是debian系的linux
	
	sudo apt-get install ruby
	
如果是CentOS系的linux

	sudo yum install ruby
	
安装好ruby以后终端执行

	gem install jekyll
	

#####2. 新建本地博客
终端执行

	jekyll new watsy0007.github.io

自动生成博客的基本样式表
目录结构
.  
|--_config.yml      
|--_includes    
|&nbsp;&nbsp;|--footer.html     
|&nbsp;&nbsp;|--head.html   
|&nbsp;&nbsp;|--header.html     
|--_layouts    
|&nbsp;&nbsp;|--default.html            
|&nbsp;&nbsp;|--page.html   
|&nbsp;&nbsp;|--post.html   
|--_posts       
|&nbsp;&nbsp;|--2014-07-12-welcome-to-jekyll.markdow      		
|--about.md     	
|--css      
|&nbsp;&nbsp;|--main.css          
|--feed.xml     
|--index.html   
	
可以参考[jekyllcn.com][jekyllcn.com] 或者官方[jekyllrb.com][jekyllrb.com]了解基础。  

#####3. 修改_config.yml

参考[_config.yml][_config.yml] 配置网站的基本信息。		
现在的配置有些地方可以后续优化，不过网站在此配置上已经可用。	

#####4. 修改基本样式
修改样式也包括修改目录结构		
在根目录执行
	
	mkdir public;cp -R css public;cd public/css
	
打开[我的css][watsy0007css]复制hyde.css,poole.css, syntax.css 到目录下	
修改_includes/head.html
替换

	<link rel="stylesheet" href="{{ "/css/main.css" | prepend: site.baseurl }}">
	
为

	<link rel="stylesheet" href="{{ site.assetsurl }}public/css/poole.css">
    <link rel="stylesheet" href="{{ site.assetsurl }}public/css/syntax.css">
    <link rel="stylesheet" href="{{ site.assetsurl }}public/css/hyde.css">	

#####5. 增加自动生成分类插件

终端执行

	mkdir _plugins;vim category_page_generator.rb

拷贝[category_page_generator][category_page_generator]代码到文件中

执行以下命令保存

	:wq



#####6. 上传到github
登陆[github.com][github.com]新建1个仓库
	
	个人昵称.github.io

在watsy0007.github.io目录下执行

	touch README.md
	git init
	git add .
	git commit -m "first commit"
	git remote add origin https://github.com/个人账号/个人昵称.git
	git push -u origin master

上传代码到github服务器

#####4. github不支持自定义jekyll plugin解决办法

因为github不支持自定义插件，默认的个人站点必须保存在代码的master分支下。	
因此换一种思路	
把代码保存到source分支下	
本地编译扣发布编译好的站点到master分支可以完美支持jekyll的自定义插件	
具体方法如下：  
终端执行
	
	$ git checkout -b source master
	$ git push -u origin source

切换github的代码主要分支	
	![切换github代码分支](http://watsy0007githubio.qiniudn.com/4FECEF2A-3E48-4F2D-9D08-EA175FC87C08.png?imageView/2/w/640/q/90)

接下来执行

	vim Rakefile
	
拷贝[我的Rakefile代码][watsy0007githubrakefile]替换

#####5. 测试
终端执行
	
	rake post title="hello jekyll" category=codedevelop
	
用[Mou][mouapp.com]所见所得编写blog内容

终端执行

	rake deploy msg="上传最新代码"

终端执行

	rake publish msg="发布最新博客"	
	
	
#####6. 添加多说和百度统计
此处比较简单，就不写了，如果有需要，可以参见我的代码，或者评论留言   


###引用链接说明
学习了解jekyll基础知识[jekyllcn.com][jekyllcn.com] 和 [jekyllrb.com][jekyllrb.com]  
 
jekyll插件[jekyll plugin][jekyllplugin]
   
在github页面上使用自定义插件[ixti'spersonal scratchpad][usingjekyllpluginongithubpages]    
      	
category显示中文解决办法 [mytharcher.github.com][mytharcher.github.com]     



###收尾
实际操作过程中有人换问题欢迎各位在下面留言。	

[forkwatsy0007github]:https://github.com/watsy0007/watsy0007.github.io
[jekyllcn.com]:http://jekyllcn.com/
[jekyllrb.com]:http://jekyllrb.com/
[_config.yml]:https://github.com/watsy0007/watsy0007.github.io/blob/source/_config.yml
[watsy0007css]:https://github.com/watsy0007/watsy0007.github.io/tree/source/public/css
[category_page_generator]:https://github.com/watsy0007/watsy0007.github.io/blob/source/_plugins/category_page_generator.rb
[github.com]:https://github.com
[watsy0007githubrakefile]:https://github.com/watsy0007/watsy0007.github.io/blob/source/Rakefile
[mouapp.com]:http://mouapp.com
[usingjekyllpluginongithubpages]:http://ixti.net/software/2013/01/28/using-jekyll-plugins-on-github-pages.html
[jekyllplugin]:https://github.com/recurser/jekyll-plugins
[mytharcher.github.com]:https://github.com/mytharcher/mytharcher.github.com