---
layout: post
title: Go语言基础1
categories: [语言学习]
tags: [go, golang]
---

## Go语言基础篇1
#### go语言的变量生命与赋值的三种方式
```
var a int = 1;
var a = 1;
a:= 1;
var a, b int = 1, 2
```
###多值返回
```
package main

import(
	'fmt'
)

func swap(x, y string) (string, string){
	return y, x
}
func main() {
	a, b := swap("hello", world)
}
```
###命名返回值
```
package main

import(
	'fmt'
)
func split(sum int)(x, y int) {
	x = sum * 4 /9;
	y = sum - x;
	return
}
func main() {
	a, b := split(100)
}
```
###fmt格式的占位符
####定义示例如下的类型和变量
```
type Human struct {
	Name string
}
var people = Human{Name:"zhangsan"}
```  

| 占位符 | 说明 | 举例 | 输出 | 
|:-----——:|:-——---:|:----—--:|:---——:|
|%v | 相应值的默认格式 | Printf("%v", people)| {zhangsan}|

