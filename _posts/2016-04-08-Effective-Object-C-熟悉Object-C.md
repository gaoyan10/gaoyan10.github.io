---
layout: post
title: Effective Object C 2.0——熟悉Object-C
categories: [阅读笔记]
tags: [Object-C, ios]
---
##### Object-C的起源和特性

1. 消息结构语言，所执行代码由运行环境来决定而不是编译器
2. 由运行期组件（Runtime Component）而非编译器来完成。
3. C的超集，所有的Object-C的对象必须分配在堆上。

##### 在类的头文件中尽量少引入其他头文件

1. 向前声明，参考C++的向前声明， 在头文件中@class ClassName.但在实现类中需要#import "class.h",因为实现类中要使用这个类，必须知道其所有接口细节。
2. 向前声明解决了相互引用问题。也要注意的是，使用#import而非#include不会导致死循环编译，但是类中有一个类会无法被正确编译。

总结： <br>
1. 除非有必要，否则不要引入头文件，一般来说，在某个类的头文件中使用向前声明来提及别的类，在实现文件中引入那些类的头文件，这样可以降低类之间的耦合。
2. 当要声明一个类遵循一项协议时，尽量把该类遵循某协议（SubClass: SuperClass<protocal>）这条声明移到class-continuation分类，在实现文件里写。如果不行就把协议单独放到一个头文件里，然后将其引入。

#### 多用字面量语法，少用与之等价的方法
NString的两种初始化方式
1. NSString *test = @"test";
2. NSString *test = [[NSString alloc] init];

尽量使用第一种，第一种叫做字符串字面量，代码短而且易读

同理，对于NSNumber两种初始化方式

1. NSNumber *someNumber = @1; NSNumber *floatNumber = @1.5f;
2. NSNumber *someNumber = [NSNumber numberWithInit:1];

也是推荐第一种
NSNumber *boolNumber = @YES;
NSNumber *charNumber = @'a';

字面量语法也适合表达式 NSNumber *express = @(x * y);

对于NSArray，也有两种创建方式

1. NSArray *animals = [NSArray arrayWithObjects:@"cat", @"dog", nil];
2. NSArray *animals = @[@"cat", @"dog"];

第二种更利于数组操作，比如取数据时
第一种 NSString *dog = [animals objectAtIndex:1];
而第二种是 NSString *dog = animals[1];

**注意** 字面量语法实际上是一种语法糖，NSArray操作等效于先创建一个数组，然后把方括号所有对象加入数组，所以当数组元素有nil时会抛出异常。arrayWithObjects会依次处理各个参数，直到遇到nil，所以nil后面的会无法加入，所以相对于抛异常，更不安全。

字面量字典，NSDictionary

1. NSDictionary *person = [NSDictionary  dictionaryWithObjectsAndKeys:@"Matt", @"firstName", @"Green", @"lastname", [NSNumber numberWithInit: 28], @"age", nil];
2. NSDictionary *person = @{@"firstname": @"Matt", ...};

同样的问题，值不能有nil；

访问的时候，第一种NSString *lastName = [person objectForKey:@"lastname"];
第二种 NSString *lastname = person[@"lastname"];

**可变数组与字典**

传统的修改方式如下

```
[array replaceObjectAtIndex:1 withObject: @"dog"];
[dict setObject:@"Green" forKey:@"lastname"];

```
字面量修改方式

```
array[1] = @"dog";
dict[@"lastname"] = @"Green";
```
局限性，必须使用Foundation框架内的类，比如以上几种类，其他类或者它们的子类都不行。

#### 多用类型常量，少用#define预处理指令
以前C或者Object-C定义常量如下

```
#define ANIMATION_DURATION 0.4
```
问题是编译器会把所有的遇到的ANIMATION_DURATION替换为0.3

所以定义时间间隔如下：

```
static const NSTimeInterval kAnimationDuration = 0.3;
```
方便阅读，方便理解含义。**另外如果常量局限于某个编译单元（也就是某个实现文件内），则前面加k，若常量在类之外可见，则通常以类名为前缀**。

定义常量的位置，不应该像预处理文件一样放到头文件，这样引入头文件的都会出现这个define，static const的常量也不应该出现在头文件，因为OC没有**名称空间？？**概念,这就等于声明了一个全局变量。若不打算公开这个常量，就放在实现文件里，放在@implementation之前。**static修饰符意味着变量仅在定义此变量的编译单元可见**

对于外界可见的常值变量，一般需放到**全局符号表**
定义方式如下

```
#header file
extern NSString *const EOCStringConstant;
# implementation file
NSString *const EOCStringConstant = @"value";
#在头文件声明，在实现文件中定义
```
只能有一个，为了避免名称冲突，最好是用与之相关的类名做前缀

1. 不要用预处理指令定义常量，因为这样的常量不含类型信息。编译器只是查找，替换。
2. 在实现文件使用static const定义只在编译单元内可见的常量，这类常量不在全局符号表中，所以无须为其名称添加前缀。
3. 头文件中使用extern来声明全局常量，并在相关实现文件中定义其值，会出现在全局符号表中，所以通常用类名做前缀。

#### 用枚举表示状态、选项、状态码

Object-C采用C++11标准，枚举是强类型（strong type）

示例如下：

```
enum EOCConnectionState{
	EOCConnectionStateDisconnected,
	EOCConnectionStateConnecting,
	EOCConnectionStateConnected,
};
typedef enum EOCConnectionState EOCConnectionState;
#使用
EOCConnectionState state = EOCConnectionStateDisconnected;
```
可以指明用何种底层数据来保存枚举类型的变量，所以可以使用向前声明枚举变量了。
用法如下:

```
enum EOCConnectionState: NSInteger {...}
#使用向前声明时指定数据类型
enum EOCConnectionState: NSInteger;
#不使用编译器所分配的序号，手工指定某个枚举成员所对应的值
enum EOCConnectionState{
	EOCConnectionStateDisconnected = 1,
	EOCConnectionStateConnecting,
	EOCConnectionStateConnected,
};//接下来的几个枚举值会在上一个基础上递增1
```
另一个例子就是定义选项，如果选项之间可以组合，则考虑用**按位或操作符**
具体可以参考UIViewAutoresizing

**Foundation框架定义了一些辅助宏，用这些宏可以指定枚举值的底层数据类型，并且具备向后兼容能力。**
用法如下：

```
typedef NS_ENUM(NSUInteger, EOCConnectionState) {
	EOCConnectionStateDisconnected,
	EOCConnectionStateConnected,
	EOCConnectionStateConnecting,
};
typedef NS_OPTIONS(NSUInteger, EOCPermittedDirection) {
	EOCPermittedDirectionUp = 1 << 0,
	EOCPermittedDirectionDown = 1 << 1,
	EOCPermittedDirectionLeft = 1 << 2,
	EOCPermittedDirectionRight = 1 << 3,
};
```
**凡是需要按位或操作来组合的枚举都应使用NS_OPTIONS宏，不需要互相组合的，使用NS_ENUM来定义**

**另外，switch来处理枚举时，最好不要用default，这样当加入新的状态时，编译器会发出警告，保证switch处理了所有的样式**

**总结**

1. 用枚举来表示状态机的状态、传递给方法的选项以及状态码等值，给这些值起个易懂的名字。
2. 如果传递给某个方法的选项表示为枚举，而多个选项可以同时使用，那么就将各选项的值定义为2的幂，以便可以通过按位或操作将其组合起来。
3. 用NS_ENUM，NS_OPTIONS宏来定义枚举类型，并指明其底层数据类型，这样保证了向后兼容。
4. 在处理枚举类型的switch语句中，不要实现default分支，这样可以保证能处理所有的类型。