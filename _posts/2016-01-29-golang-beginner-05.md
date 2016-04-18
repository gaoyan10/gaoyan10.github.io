---
layout: post
title: Go语言基础5
categories: [语言学习]
tags: [go, golang]
---
## Go语言基础5——并发
#### goroutine
**goroutine是由Go运行时环境管理的轻量级线程**

```
go functionName(x, y, z)
```
**goroutine在相同的地址空间运行，因此访问共享内存必须进行同步。sync提供了同步的可能**
#### channel
**channel是有类型的管道，可以用<-进行发送或者接收**

```
ch <- v //将v送入channel ch
v := <-ch //从ch接收，并且赋值给v
```
**与map和slice一样，channel在使用前必须创建**

```
ch := make(chan int)
```
**channel可以是带缓冲的，ch := make(chan int, 100)**

**close，来关闭一个channel**

**select语句会使得一个goroutine在多个通讯操作上等待**
**当 select 中的其他条件分支都没有准备好的时候，default 分支会被执行。
为了非阻塞的发送或者接收，可使用 default 分支：**

```
select {
case i := <-c:
    // 使用 i
default:
    // 从 c 读取会阻塞
}
```
#### sync.Mutex
**提供互斥锁，sync.Mutex有两个方法，Lock和UnLock，在互斥代码前调用Lock，在代码后调用Unlock**

```
package main

import (
	"fmt"
	"sync"
	"time"
)

// SafeCounter 的并发使用是安全的。
type SafeCounter struct {
	v   map[string]int
	mux sync.Mutex
}

// Inc 增加给定 key 的计数器的值。
func (c *SafeCounter) Inc(key string) {
	c.mux.Lock()
	// Lock 之后同一时刻只有一个 goroutine 能访问 c.v
	c.v[key]++
	c.mux.Unlock()
}

// Value 返回给定 key 的计数器的当前值。
func (c *SafeCounter) Value(key string) int {
	c.mux.Lock()
	// Lock 之后同一时刻只有一个 goroutine 能访问 c.v
	defer c.mux.Unlock()
	return c.v[key]
}

func main() {
	c := SafeCounter{v: make(map[string]int)}
	for i := 0; i < 1000; i++ {
		go c.Inc("somekey")
	}

	time.Sleep(time.Second)
	fmt.Println(c.Value("somekey"))
}

```