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

### 多值返回

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

### 命名返回值

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

### fmt格式的占位符
[http://studygolang.com/articles/2644](http://studygolang.com/articles/2644)

#### 定义示例如下的类型和变量

```
type Human struct {
	Name string
}
var people = Human{Name:"zhangsan"}
```  



|占位符 | 说明 | 举例 | 输出 |
|:------|:-------|:------|:--------:|
|%v     |相应值的默认格式| Printf("%v", people)|{zhangsan}|
|%+v	|打印结构体时，会添加字段名 | Printf("%+v", people)| {Name:zhangsan}|
|%#v    |相应值的Go语法表示| Printf("%#v", people)|main.Human{Name:"zhangsan"}|
|%T     |相应值类型的Go语法表示|Printf("%T", people)|main.Human|
|%%     |百分号| Printf("%%")| %|
|%t     |true或者false| Printf("%t", true)|true|
|%b     |二进制表示 |Printf("%b", 5) |101|
|%c    |相应Unicode码点所表示的字符|Printf("%c", 0x4E2D)|中|
|%d    |十进制表示|Printf("%d", 0x12)|18|
|%o   	|八进制表示|Printf("%d", 10)|12|
|%q    |单引号围绕的字符字面值，由Go语法安全地转义|Printf("%q", 0x4E2D) |'中'|
|%x   |十六进制表示，字母形式为小写 a-f|Printf("%x", 13)|d|
|%X     |十六进制表示，字母形式为大写 A-F|Printf("%x", 13)|D|
|%U    |Unicode格式：U+1234，等同于 "U+%04X"|Printf("%U", 0x4E2D)| U+4E2D|
|%s   |输出字符串表示（string类型或[]byte)|Printf("%s", []byte("Go语言")) |Go语言|
|%q    |双引号围绕的字符串，由Go语法安全地转义|Printf("%q", "Go语言")   |"Go语言"|
|%x     |十六进制，小写字母，每字节两个字符|Printf("%x", "golang")|676f6c616e67|
|%X    |十六进制，大写字母，每字节两个字符|Printf("%X", "golang")|676F6C616E67|
|%p    |十六进制表示，前缀 0x|Printf("%p", &people)|0x4f57f0|

#####其他标记

|占位符| 说明|举例|输出|
|-------|--------|------|------|
|+|总打印数值的正负号；对于%q（%+q）保证只输出ASCII编码的字符。Printf("%+q", "中文")|"\u4e2d\u6587"|


