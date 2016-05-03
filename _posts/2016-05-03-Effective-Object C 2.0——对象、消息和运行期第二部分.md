---
layout: post
title: Effective Object C 2.0——对象、消息和运行期第二部分
categories: [阅读笔记]
tags: [Object-C, ios]
---

### 以类族模式（class cluster）隐藏实现细节

class cluster有点类似Factory模式
比如创建按钮的类方法

```
+ (UIButton *)buttonWithType:(UIButtonType) type;
```

由于Object-c中没有指明某个基类是抽象的，所以开发者通常在文档中写明类的用法。基类接口一般都没有名为init的成员方法。表示该类的实例不应该由用户直接创建。还有一种方法就是在基类的空方法（子类要分别实现这个方法实现重载），抛出异常，这种做法很极端，很少有人用。

另一个要注意的问题就是如果对象所属的类在某个类族中，查询类型信息时要注意，**子类一定是父类的类型，但实际上已经和父类不相同了**，比如

```
[employee isMemberOfClass:[EOCEmployee class]],返回YES，但实际是NO

```

Cocoa里的类族。大部分collection类都是类族，比如NSArray和NSMutableArray，属于一个类族。NSArray的alloc方法来获取实例时，会先分配一个属于某类的实例，充当占位数组。该数组稍后会转为另一个类的实例，那个类则是NSArray的实体子类。

所以下面的代码永远为NO，因为由NSArray初始化方法返回的那个实例其类型是隐藏在**类族公共接口**后面的某个内部类型。

```
id maybeAnArray = ..
if ([maybeAnArray class] == [NSArray class]) {
}

```

不过可以查询某个对象是否位于类族中。

```
id maybeAnArray = ...
if ([maybeAnArray isKindOfClass:[NSArray class]]) {
}
```

向类族（NSArray）增加实体子类，需要遵循下面的原则

1. 子类应该继承自类族的抽象基类，比如要编写NSArray类族的子类，需要继承自不可变数组的基类或者可变数组的基类。
2. 子类应该定义自己的数据存储方式，子类必须用一个实例变量来存放数组中的对象。
3. 子类应该覆写超类文档中指明需要覆写的方法，比如count，objectAtIndex。像lastObject方法无须实现，因为基类用前面两个方法来实现出这个方法。

#### 总结

1. 类族模式可以把实现细节隐藏在一套简单的公共接口后面
2. 系统框架中经常使用类族
3. 从类族的公共抽象基类中继承子类时要当心，如有开发文档，则要遵循开发文档。