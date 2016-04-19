 ---
layout: post
title: Effective Object C 2.0——对象、消息和运行期
categories: [阅读笔记]
tags: [Object-C, ios]
---

### 理解属性

property，用于封装对象中的数据。setter，getter和点语法（dot syntax）。
定义实例变量的不足

```
@interface EOCPerson: NSObject {
@public
	NSString *_firsetName;
	NSString *_lastName;
@private
	NSString *_someInternalData;

}
@end
```

添加新的变量后，必须重新编译，否则偏移量都是错的。OC里的做法把存储偏移量所用的特殊变量交由类对象保管，偏移量在运行期查找，类定义变了，存储的偏移量也就变了。当在运行期向类新增实例变量，就是“应用程序二进制接口”（Application Binary Interface, ABI）。通过ABI，可以在class-continuation分类或者实现文件中定义实例变量。不在接口中把全部实例变量都声明好，可以将某些变量从接口public区段移走，可以保护与类实现有关的内部信息。

```
@property NSString *firstName;
#等效于
-（void）setFirsetName:(NSString *)firstName;
- (NSString *)firstName;
```

访问属性，可以直接使用点语法。使用property，编译器会自动编写访问这些属性所需的方法，在编译期执行，这个过程叫做自动合成。除了生成方法代码外，默认会生成属性名前加下划线的实例变量名。可以通过

```
@synthesize firstName = _myFirstName;
```

来指定实例变量的名字。

在实现文件里，可以用过dynamic关键字告诉编译器，不自动创建实例变量和存取方法。

##### 属性特质

```
@property (nonatomic, readwrite, copy) NSString *firstName;
```

1. 原子性 nonatomic，不使用同步锁，默认是atomic，效率低
2. readwrite，读写方法。如果属性是@synthesize，会自动生成这两个方法。readonly，当属性由@synthesize实现时，自动合成获取方法。可以对外公开为只读属性，在class-continuation分类里重新定义为读写属性。
3. 内存管理语义。assign,设置方法只会执行简单的赋值操作（比如NSInteger）; strong, 表示该属性定义了一种拥有关系，为该属性赋值时，会先保留新值，释放旧值，然后设置新值；weak，表示该属性定义了一种非拥有关系，当属性所指的对象遭到摧毁时，属性值会变成nil; unsafe_unretained,与assign相同，但适用于对象类型，但释放时不会自动清空，不安全；copy, 特质所表达的所属关系与strong类似，然而设置方法并不保留新值，而是将其copy，深拷贝，可以用来保护其封装性。retain，浅拷贝，引用计数加一，但是内容没有复制。

##### 设置方法名

```
@property (nonatomic, getter=isOn, setter=setOn) BOOL on;
#setter的方式不太常见.
```

可以微调编译器所合成的存取方法。如果自己实现这些方法，要注意保证其具备相关属性所声明的特质比如copy等。
这里有一篇关于内存管理语义的blog
[http://www.cnblogs.com/iCocos/p/4462744.html](http://www.cnblogs.com/iCocos/p/4462744.html)

### 对象内部尽量直接访问实例变量

点语法和直接访问实例变量（下划线+属性名）的区别：

1. 不经过OC的方法派发，效率高，会直接访问到实例变量保存的那块内存。
2. 不会调用其设置方法，绕过了相关属性所定义的内存管理语义（这是不好的）
3. 不会触发KVO通知，可能会出现问题（好不好没法说）
4. 无法断点，比如无法再setter和getter方法里新增断点（不好的）

折中方案：写入变量时，采用其设置方法来完成，读取变量时直接访问。**初始化的方法中，如何设置属性值，应该直接访问实例变量，因为子类可能会覆盖（override）设置方法**
比如对于一个copy的属性值

```
-(id) initWithFirstName: (NSString *)firstName
		lastName: (NSString *)lastName{
	if ((self = [super init])) {
		_firstName = [firstName copy];
		_lastName = [lastName copy];
	}
	return self;		
}
```

**如果使用了惰性初始化（lazy initialization）, 则必须用获取方法来访问属性，否则实例变量就永远不会初始化**

### 理解对象等同性

==比较的是两个指针本身，或者说比较的是内存地址。
NSObject的isEqual方法是用来判断两个对象的等同性的。而且两个类型不同的对象总是不相等的。

```
NSString *foo = @"Badger 123";
NSString *bar = [NSString stringWithFormat:@"Badger %i" 123];
BOOL equalA = (foo == bar); //equalA = NO
BOOL equalB = [foo isEqual:bar]; // equalB = YES
BOOL equalC = [foo isEqualToString:bar]; //equalC = YES
```

注意isEqualToString, 传递给该方法的对象必须是NSString，否则结果是undefined;

NSObject中有两个用于判断等同性的关键方法：

1. -（BOOL）isEqual:(id)object;
2. -(NSUInteger)hash;

isEqual相等，那么hash返回同一个值，但是返回同一个值，isEqual方法未必认为两者相等。
NSArray, NSDictionary，也有类似的判定方法。

同时可以自己定义isEqual和isEqualToClassName。

##### 等同性判断的执行深度问题

比如数据库的数据创建的实例，只比较primary key就好。

##### 容器内可变类的等同性

比如NSMutableSet中，collection按照哈希吗分配到不同的容器内，之后某个对象的哈希吗变了，容器内就出现错误找不到该对象了。确保哈希吗不是根据对象的可变部分计算出来的，或者保证放入容器后不再改变对象的内容。
如果把某个对象放入set后又修改其内容，那么后面的行为就很难预料，比如出现内容重复的数据。

总结：

1. 想检测对象的等同性，需要提供isEqual:和hash方法
2. 相同的对象比如具有相同的哈希吗，反之不一定（hash碰撞）
3. 不要盲目逐个检查每条属性，根据具体需求来指定监测方案
4. 编写hash方法，要使用计算速度快而且哈希码碰撞几率低的算法。

