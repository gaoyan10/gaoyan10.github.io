---
layout: post
title: Homebrew安装wine提示 Failed to download resource "libpng"
categories: [调试部署]
tags: [wine,homebrew] 
---

最近被女朋友推荐玩windows下的1个小游戏。她有安装包。考虑要不要安装虚拟机时候，在v2ex上看到个帖子。
[现在这个时候， mac 上的 wine 已经足够强大了](http://v2ex.com/t/150760#reply9)

兴起就打开终端

	homebrew install wine

得到如下错误

	==> Downloading https://downloads.sf.net/project/machomebrew/Bottles/libpng-1.6.13.mavericks.bottle.tar.gz

	curl: (7) Failed connect to downloads.sf.net:8087; Connection refused
	Error: Failed to download resource "libpng"
	
继续执行

	homebrew install libpng
	
错误依旧。查了下资料据说原因是被墙了。

最终还是万能的[stackoverflow](http://stackoverflow.com)帮忙解决了。
[Cant's install imagemagick with brew on Mac OS X mavericks](http://stackoverflow.com/questions/25460047/cants-install-imagemagick-with-brew-on-mac-os-x-mavericks)

解决方案

	$ brew update
	$ brew install imagemagick --disable-openmp --build-from-source
