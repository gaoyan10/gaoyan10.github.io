---
layout: post
title: emacs24在Mac OS X安装emacs w3m浏览网页
categories: [emacs]
tags: [emacs]
---

## 为什么要学习emacs

       做程序员有一些年头了。断断续续的学习过很多语言。发现每种语言大家遇到的1个困惑就是有什么好用的IDE。每种语言都有1套。各不相同。cs有cs的，bs有bs的。			
       另外还有各种版本管理，任务管理，命令行操作。调试集成等。在实际使用过程当中会花费大量精力在学习这些不同的IDE上。尤其是多种语言协同工作时候。				
       随着编程年份的增加，对开发效率越来越注重。对linux/unix，各种好用的app越来越喜欢。机缘巧合了解了emacs编辑器。瞬间就产生了学习了解的兴趣，不为别的，就为了效率和lisp。


## 加载emacs-w3m步骤

### 1.环境
本文的系统主要在Mac OS X 10.10.3 beta1下测试。emacs版本为24.4。

### 2.配置

#### 1. 系统配置
1. 安装XCode
2. 安装xCode命令行工具  
	```xcode-select --install```
3. 安装homebrew   
	```ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"```
4. 安装w3m  
	```brew install w3m```
5. 安装emacs	  
	*省略*		
6. emacs配置  
	*使用[Steve Purcell的配置](https://github.com/purcell/emacs.d)*	
7. emacs-w3m安装	  
	从[官网](http://emacs-w3m.namazu.org)和网上资料来看。直接下载的打包文件是不行的。需要从cvs上下载。
	具体命令是		

		% cvs -d :pserver:anonymous@cvs.namazu.org:/storage/cvsroot login
		CVS password: # No password is set.  Just hit Enter/Return key.
		% cvs -d :pserver:anonymous@cvs.namazu.org:/storage/cvsroot co emacs-w3m
		
	下载以后配置时候需要注意，因为我用的是别人打包好的Emacs for Mac App程序。因此需要指定emacs路径
	**配置configure文件**
 
		autoconf
		
	编译安装
	
		./configure --prefix=/Applications/Emacs.app/Contents/MacOS/Emacs-x86_64-10_9
		make
		sudo make install		
		
	编译成功以后会发现emacs-w3m安装在```/Applications/Emacs.app/Contents/share/emacs/site-lisp/w3m```路径下

	修改emacs.d的加载配置

		vim ~/.emacs.d/init.el
		
	在

		(add-to-list 'load-path (expand-file-name "lisp" user-emacs-directory))
		
	下添加2行

		(add-to-list 'load-path "/Applications/Emacs.app/Contents/share/emacs/site-lisp/w3m")
		(require 'init-w3m)

	另外执行

		vim ~/.emacs.d/lisp/init-w3m.el

	插入数据

		(require 'w3m-load)
		(setq w3m-home-page "http://www.google.com")
		(provide 'init-w3m)			
		
	保存退出，打开emacs后执行```m-x w3m``` 会提示加载成功。并且显示google首页。加载成功



## 3. 错误解决方法

#### 1. emacs-w3m of this version does not support emacs 24 try the development version
这个错误主要是emacs-w3m代码的版本不是最新版本。应该从csv中下载。

#### 2. error in process sentinel symbol's function definition is void --post-body--40818
产生这个错误主要是emacs编译没有指定路径，导致编译错误，外加不指定路径会把emacs-w3m拷贝到/usr/share/emacs/路径下。而这个路径是无写入权限的。会导致编译出问题。如果指定路径还存在错误。按照[如下链接](http://comments.gmane.org/gmane.emacs.w3m/8902)，删除预便宜的elc文件，在emacs下用命令进行编译。
