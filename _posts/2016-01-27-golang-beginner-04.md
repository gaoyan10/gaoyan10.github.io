---
layout: post
title: Go语言基础4
categories: [语言学习]
tags: [go, golang]
---
## Go语言基础4

##### Go语言竟然没没没没有类，不过有方法的概念，可以在struct的基础上定义方法。go语言中方法和函数的区别：方法的接收者为值类型时，可以用指针类型的变量调用，反之也可以；普通函数接收者（参数）为值类型时，不能将指针类型的数据直接传递过去，反之也不能。[参考这里](http://www.jb51.net/article/60887.htm)

### 方法

**方法接收者出现在func关键字和方法名之间的参数中**

```
type Vertex struct {
	X, Y float64
}

func (v *Vertex) Abs() float64 {
	return math.Sqrt(v.X*v.X + v.Y*v.Y)
}
```
**不仅仅是结构体，可以对任意类型定义任意方法**

```
type MyFloat float64

func (f MyFloat) Abs() float64 {
	if f < 0 {
		return float64(-f)
	}
	return float64(f)
}

func main() {
	f := MyFloat(-math.Sqrt2)
	fmt.Println(f.Abs())
}
```
**接收者为指针的方法**

**刚刚看到的两个 Abs 方法。一个是在 \*Vertex 指针类型上，而另一个在 MyFloat 值类型上。 有两个原因需要使用指针接收者。**

1. **首先避免在每个方法调用中拷贝值（如果值类型是大的结构
体的话会更有效率）。**
2. **其次，方法可以修改接收者指向的值。_
尝试修改 Abs 的定义，同时 Scale 方法使用 Vertex 代替 *Vertex 作为接收者。**

**当 v 是 Vertex 的时候 Scale 方法没有任何作用。Scale 修改 v。当 v 是一个值（非指针），方法看到的是 Vertex 的副本，并且无法修改原始值。**

### 接口
**接口是一组方法定义的集合**
**挺诡异的，[看链接](https://tour.go-zh.org/methods/4)**
### 隐式接口
**类型通过实现那些方法来实现接口，没有implements关键字**
**下面为person struct实现了Stringer接口**

```
//Stringer是fmt包中定义的接口
type Stringer interface{
	String() string
}
```

```
package main

import "fmt"

type Person struct {
	Name string
	Age  int
}

func (p Person) String() string {
	return fmt.Sprintf("%v (%v years)", p.Name, p.Age)
}

func main() {
	a := Person{"Arthur Dent", 42}
	z := Person{"Zaphod Beeblebrox", 9001}
	fmt.Println(a, z)
}

```
