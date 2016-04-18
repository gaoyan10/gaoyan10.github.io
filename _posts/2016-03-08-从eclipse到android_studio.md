---
layout: post
title: 从Eclipse到Android Studio
categories: [移动开发]
tags: [AndroidStudio, 移动开发, gradle]
---
1. ## migration

#### 从eclipse到Android Studio，在Android Studio中直接选择import project(Eclipse ADT, Gradle ect),并替换一些library、jar为gradle的depends即可。

![图片](http://ww3.sinaimg.cn/large/7cc2d56fjw1f1png0m2voj20rg0p041y.jpg)

2. ## proguard

#### 在eclipse中，proguard需要加入

#### -libraryjars libs/XXX.jar，来避免jar包被多次混淆

#### AndroidStudio中，因为build.gradle中加入了compile fileTree(dir:'libs', include:['*.jar'])不需要加入类似语句，加入后会提示jar包多次被避免混淆。
 
 ![图片](http://ww3.sinaimg.cn/large/7cc2d56fjw1f1png80qjaj20tc088whg.jpg)
 ![图片](http://ww3.sinaimg.cn/large/7cc2d56fjw1f1pnfloeq1j20va0diaf5.jpg)


