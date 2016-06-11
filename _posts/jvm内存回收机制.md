---
layout: post
title: Android内存管理——GC总结
categories: [阅读笔记]
tags: [Android, GC]
---

#### JVM内存回收机制

1. 标记回收算法（Mark-Sweep）
2. 复制算法（Copying）-把内存分为多块
3. 标记-压缩算法（Mark-Compat）
4. 分代：老年代采用标记-压缩算法，新生代采用复制算法。

### Dalvik虚拟机

#### Java堆

实际上有一个Active堆和一个Zygote堆组成，其中Zygote堆用来管理Zygote堆组成的。Zygote堆用来管理Zygote进程在启动过程中预加载和创建的各种对象。Active堆是在Zygote进程fork第一个子进程之前创建的。以后启动的应用程序是被Zygote进程fork出来的，并持有自己一个Dalvik虚拟机。Dalvik虚拟机采用Cow策略复制Zygote进程的地址空间。

Cow策略：在未复制好Zygote进程地址空间时，应用程序和Zygote进程共享了同一个同来分配对象的堆。当Zygote进程或者应用程序进程对该堆进行写操作时，内核执行真正的拷贝操作，使得Zygote进程和应用程序分别拥有自己的一份拷贝。

为了实现Cow拷贝，当创建第一个应用程序时，会将那部分堆内存划分为一分部，这就是Zygote堆，另一个部分未使用的就是Active堆。无论Zygote进程还是应用程序进程，要分配内存时，都是在Active堆上进行。这样只需要把Zygote堆的内容进行拷贝就行了，减少了Zygote堆执行写操作。Zygote堆里面分配的对象主要是Zygote进程在启动过程中预加载的类、资源和对象，这些可以在Zygote进程和应用程序进程长期共享。

St art ing Size, Dalvik虚拟机启动的时候，会先分配一块初始的堆内存给虚拟机。

Growth Limit：系统给每个程序最大的堆上限，超过就会OOM

Maximum Size：不受控情况下最大堆内存大小，在manifest中使用了largeHeap属性的时候可以从系统获取最大堆大小。

堆最小空闲值（Min Free）,堆最大空闲值（Max Free）和堆目标利用率（Target Utilization）。堆理想的大小是LiveSize/U，每次GC后都会尽量让堆的利用率往目标利用率上靠拢。所有有可能申请了几百K对象，反而造成频繁的GC。（待确认这种说法对不对）

#### GC类型

GC_FOR_MALLOC,堆上分配对象时内存不足触发的GC。

GC_CONCURRENT，堆内存达到一定量，快要满的时候，系统自动触发GC操作来释放内存。

GC_EXPLICIT，应用程序调用System.gc VMRuntime.gc接口或者收到SIGUSR1信号时触发的GC。

GC_BEFORE_OOM，表示是在准备OOM时最后进行GC的尝试


#### ART内存回收机制

Java堆包括Image Space, Zygote Space, Allocation Space和Large Space四部分。Image Space 

[http://www.tuicool.com/articles/BJfAbmf](http://www.tuicool.com/articles/BJfAbmf)

