---
layout: post
title: Go语言基础3
categories: [语言学习]
tags: [go, golang]
---
## Go语言基础3
### 指针
与C语言没有什么不同
简单类型是传值，而不是传址

```
a := 1
p := &a
*p = 2 //修改a值为2
```
### 结构体
**结构体struct理解为一个字段的集合**

```
package main
import "fmt"
type Vertex struct{
	X int
	Y int
}
func main(){
	fmt.Println(Vertex{1,2})
	a := Vertex{1,2}
	fmt.Println(a.X)
	b := &a //b是结构体指针
	fmt.Println(b.X)
	
}

```
#### 结构体语法
```
package main

import "fmt"

type Vertex struct {
	X, Y int
}

var (
	v1 = Vertex{1, 2}  // 类型为 Vertex
	v2 = Vertex{X: 1}  // Y:0 被省略
	v3 = Vertex{}      // X:0 和 Y:0
	p  = &Vertex{1, 2} // 类型为 *Vertex
)

func main() {
	fmt.Println(v1, p, v2, v3)
}
//输出结构为{1 2} &{1 2} {1 0} {0 0}
```
### 数组
**类型[n] T， 有n个类型为T的值的数组**

```
var a [10]int
```
**slice会指向一个序列的值，并且包含了长度信息， []T是元素类型为T的slice**

```
s := []int{1,2,3,4}
len(s)
```
**slice可以包含任意类型，比如另一个slice**

```
package main

import (
	"fmt"
	"strings"
)	

func main() {
	// Create a tic-tac-toe board.
	game := [][]string{
		[]string{"_", "_", "_"},
		[]string{"_", "_", "_"},
		[]string{"_", "_", "_"},
	}

	// The players take turns.
	game[0][0] = "X"
	game[2][2] = "O"
	game[2][0] = "X"
	game[1][0] = "O"
	game[0][2] = "X"

	printBoard(game)
}

func printBoard(s [][]string) {
	for i := 0; i < len(s); i++ {
		fmt.Printf("%s\n", strings.Join(s[i], " "))
	}
}

```
**slice切片， s[low:high], high不包含**

```
package main

import "fmt"

func main() {
	s := []int{2, 3, 5, 7, 11, 13}
	fmt.Println("s ==", s)
	fmt.Println("s[1:4] ==", s[1:4])

	// 省略下标代表从 0 开始
	fmt.Println("s[:3] ==", s[:3])

	// 省略上标代表到 len(s) 结束
	fmt.Println("s[4:] ==", s[4:])
}
```
**slice的构造，make**

```
a := make([]int, 5) //len(a) = 5
b := make([]int, 0, 5) //len(b) = 0, cap(b) = 5
var z []int //z是nil.
```
**向slice添加元素**

```
func append(s []T, vs ...T) []T
//第一个参数为一个元素类型为T的slice，vs为类型为T的值将附加到该slice末尾，将添加了vs的slice返回
```
_如果 s 的底层数组太小，而不能容纳所有值时，会分配一个更大的数组。 返回的 slice 会指向这个新分配的数组。_
#### range
**for循环中使用range格式，可以对slice或者map进行迭代循环**

```
package main

import "fmt"

var pow = []int{1, 2, 4, 8, 16, 32, 64, 128}

func main() {
	for i, v := range pow {
		fmt.Printf("2**%d = %d\n", i, v)
	} //i是slice的下标，v是对应元素的一个拷贝
}
```
**可以通过赋值给 _ 来忽略序号和值。
如果只需要索引值，去掉 “ , value ” 的部分即可**

```
package main

import "fmt"

func main() {
	pow := make([]int, 10)
	for i := range pow {
		pow[i] = 1 << uint(i)
	}
	for _, value := range pow {
		fmt.Printf("%d\n", value)
	}
}
```

### map

```
var m map[string]Vertex //声明 Vertex是一个struct
m = make(map[string]Vertex)
```
```
package main

import "fmt"

type Vertex struct {
	Lat, Long float64
}

var m = map[string]Vertex{
	"Bell Labs": Vertex{
		40.68433, -74.39967,
	},
	"Google": Vertex{
		37.42202, -122.08408,
	},  //与结构体类似，但是有键名
}

func main() {
	fmt.Println(m)
}
```
#### 修改map

```
m[key] = elem //map m中插入或者修改一个元素
elem = m[key] //获得一个元素
delete(m, key) //删除元素
elem, ok = m[key] //如果key在m中，ok为true,否则ok为false，并且elem是map的元素类型的零值。同样的，当从 map 中读取某个不存在的键时，结果是 map 的元素类型的零值
```
### 函数传值
**函数值作为函数的参数**
```
package main
import (
	"fmt"
	"math"
)
func compute(fn func(float64, float64) float64) float64{
	return fn(3, 4）;
}
func main(){
	hypot:= func(x, y float64) float64 {
		return math.Sqrt(x * x + y * y);
	}
	fmt.Println(hypot(5, 12))
	fmt.Println(compute(hypot))
	fmt.Println(compute(math.Pow))
}
```
### 函数值闭包
**函数可以是一个闭包，闭包是一个函数值，它引用了函数体之外的变量。可以对这个引用那个的变量进行访问和赋值，可以理解为这个函数被绑定在这个变量上**

```
package main

import "fmt"

func adder() func(int) int {
	sum := 0
	return func(x int) int {
		sum += x
		return sum
	}
}

func main() {
	pos, neg := adder(), adder()
	for i := 0; i < 10; i++ {
		fmt.Println(
			pos(i),
			neg(-2*i),
		)
	}
} //adder返回一个闭包，每个返回的闭包都被绑定到各自的sum变量上。
```