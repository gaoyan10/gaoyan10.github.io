---
layout: post
title: IOS学习入门1
categories: [移动开发]
tags: [ios, xcode, Object-c]
---
## ViewController和Scene
一个Scene对应一个ViewController。
对于SingleView的Project，StoryBoard里只有一个View Controller。

View Controller Scene由以下部分组成

1. 一个第一响应器占位符对象（First Responser）,运行时它是第一个接收各种事件的对象。
2. Exit的占位符对象,默认情况下，当用户使子场景消失时，该场景的视图控制器展开（返回）父场景，exit对象使视图控制器能够展开任意一个场景。
3. ViewController对象，串联图载入一个场景时，会创建一个视图控制器的实例来管理该场景。
4. 一个视图，在ViewController对象内。

#### 添加用户界面元素
storyboard添加UIView。按住Control拖动组件到AssistantEditor，加入Action或者Outlet。

1. 添加Action的Touch Up Inside到代码中。方法如下
	
	```
	- (IBAction)functionName:(id)sender;
	``` 
	并将方法添加到实现区域，从而在按钮和视图控制器之间建立了连接。
	IBAction用于告诉Xcode，将一个方法作为目标-操作连接的操作部分来处理，IBAction被定义为void。
	sender参数指向发送操作消息的对象（按钮），在按钮和视图之间创建了连接。