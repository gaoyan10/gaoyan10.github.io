---
layout: post
title: Go语言基础2
categories: [语言学习]
tags: [go, golang]
---
## Go语言基础2
### 类型转换

**T（v）**

```
var i int = 12;
var f float64 = float64(i);
```
_Go在不同类型之间的赋值时需要显式转换，不存在隐式类型转换_

#### 类型推导

```
i := 43 //int
f := 3.14 //float64
g := 0.1 + 0.5i //complex128复数
```

### 常量

```
package main

import "fmt"

const Pi = 3.14

func main() {
	const World = "世界"
	fmt.Println("Hello", World)
	fmt.Println("Happy", Pi, "Day")

	const Truth = true
	fmt.Println("Go rules?", Truth)
}
```
### 控制语句

### for

for不需要（），但循环体需要{}，其中初始化语句和后置语句是可选的

```
package main

import "fmt"

func main() {
	sum := 0
	for i := 0; i < 10; i++ {
		sum += i
	}
	fmt.Println(sum)
}
```

```
package main

import "fmt"

func main() {
	sum := 1
	for ; sum < 1000; {
		sum += sum
	}
	fmt.Println(sum)
}
```

### for --> while

```
package main
import (
	"fmt"
)
func main() {
	i := 0
	for i < 1000 {
		//do something
	}
}
```
**忽略了循环条件，也就变成了一个死循环**

### if

```
func sqrt(x float64) string{
	if x < 10 {
		//do something.
	}
}
```

### if便捷语句

if可以在条件语句之前执行一个简单语句,该语句声明的变量在else中也起作用

```
package main
import (
	"fmt"
	"math"
)
func pow(x, n, lim float64) float64 {
	if v := math.Pow(x, n); v < lim {
		return v
	}
	return lim
}
```

### switch语句

**switch的条件从上到下执行，_匹配成功时停止_** 

**没有条件的 switch 同 switch true 一样。**

```
switch i {
	case 0:
	case f():
}
```

### 神奇的defer关键字

defer 语句会延迟函数的执行直到上层函数返回。
延迟调用的参数会立刻生成，但是在上层函数返回前函数都不会被调用。

```
package main

import "fmt"

func main() {
	defer fmt.Println("world")

	fmt.Println("hello")
}

```

#### defer语句被压入一个栈，后进先出

```
package main

import "fmt"

func main() {
	fmt.Println("counting")

	for i := 0; i < 10; i++ {
		defer fmt.Println(i)
	}

	fmt.Println("done")
}

```