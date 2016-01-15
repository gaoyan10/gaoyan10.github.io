---
layout: post
title: 《Metaprogramming Ruby》感悟
categories: [读书笔记]
tags: [读书笔记, ruby, DSL]
---

<!--
	核心越简洁越好
	保持开放性，为以后扩展保持可能性
	象自然界以后，提供了分子以后无限叠加
	-->

### 前言

最近工作上有点变动，又把自己清零，从0开始学习ruby on rails写程序，工作。			
其实在早在2013年时候，就开始学习python和rails了。也用python写过点程序，后来来北京以后因为工作使用了php。现在想来当初应该遵从自己内心的想法，做自己想要，喜欢做的事情。	

有很多朋友很不理解我最近的选择，明明人生已经有1个很好的规划了，为什么要清零从新开始，为什么要选择ruby,ruby on rails,小众的以后都很难找工作。	
首先感谢周围朋友们的关心。我这个人不善于表达，有些事情其实已经有了想法，但是表述出来总不知道怎么说比较好。对外只好说我有点程序员情怀。			
具体在这里就不展开了，本篇只考虑看本书的一些心得。		

### 感悟

很早以前看《黑客与画家》里面提过，语言其实会影响思维的，不同的语言有不同的思维方式，人以类聚，会吸引相似的人。	
那么在“近朱者赤，近墨者黑“指导下，选择更适合自己的？		
在早期，对我来说这其实是1个没得选择的问题。	
但是经过asm,c,c++,pascal,vb,易语言,objective-c,python,php这些语言的磨练以后，我可以按照我的想法进行选择。	
首先每种语言都是为了解决特定的问题而存在流行的。所以每种语言都有自己擅长的领域。这个是先天的。		
但是后天的是这种语言的爹当初设计语言的水平，出发点和后期的对语言的把控也决定了语言的高度。会直接影响到吸引什么样的人群？而什么样的人群也直接决定了工作方式和生活方式。	

那么我选择ruby的原因是什么？		
简单列一下有以下5点

1. 核心足够简洁
2. 对语言的使用者持开放态度
3. 为了工业使用，提供大量语法糖
4. 提供了简洁的第三方类库使用机制
5. 丰富的第三方类库

其中1，2最核心。不象很多其他语言，为了防止使用者出错而做了大量限定。语言的设计原则过多，在实际使用过程中，慢慢的我觉得我思维在僵化，很多解决方案看起来就很搓，但是不得不这样实现，因为只能这样实现。	而在ruby里面，通过本书的学习，解放了我以前僵化的思维，原来语言还可以这样设计。还可以这样简洁，原来很多不得不为之的方案在ruby里面很容易就解决，甚至不是问题。	
剩下3点保证了社群的质量，简单点类比，可以类比言论自由。在其他语言里面，不同框架甚至模块之间有着各自的言论，不符合本团体价值观的代码很难兼容。但是在ruby里面，不同的模块，甚至不同的框架之间可以一起愉快的组合解决实际问题。	这可以形成滚雪球效应，越来越多的类库吸引更多的优秀人才使用，更多的优秀人才写出更高质量的类库。	
对很多人来说，这并没有什么卵用，如果在深层次的考虑一下，采用这样的语言和社区，可以更好更快的解决问题。这才是核心。而且对于开发者来说，时间得到了极大的解放，提升生活质量。	

如果在说的简单点，ruby给我带来的感觉可以类比先秦诸子百家和欧洲文艺复兴。

### 书摘

**元编程是编写在运行时操纵语言构件的代码**		
**类自身也是对象**		

### 技术手册

#### 1. 法术集

#####  1. 数组参数
把一组参数压入到一个数组中。		

```ruby
def my_method(*args)
	args.map { |arg| arg.reverse }
end
my_method('abc','xyz','123')	#=>	['cba', 'zyx', '321']
```
#####  2. 环绕别名
从一个重新定义的方法中调用原始的，被重命名的版本。

```ruby
class String
	alias :old_reverse :reverse
	def reverse
		"x#{old_reverse}x"
	end
end
"abc".reverse #=> "xcbax"
```

#####  3. 白板
移除一个对象中的所有方法，以便把它们转换成幽灵方法。

```ruby
class C
	def method_missing(name, *args)
		"a Ghost Method"
	end
end

obj = C.new
obj.to_s #=> "#<C:0x357258>"

class C
	instance_methods.each do |m|
		undef_method unless m.to_s =~/method_missing|respond_to?|^/
	end
end

obj.to_s #=> "a Ghost Method"
```

#####  4. 类扩展
通过向类的eigenclass中混入模块来定义类方法。（对象扩展的一个特例）

```ruby
class C;end

module M
	def my_method
		'aclass method'
	end
end

class << C
	include M
end

C.my_method #=> 'a class method'
```

#####  5. 类扩展混入
使一个模块可以通过钩子方法扩展它的包含着。

```ruby
module M
	def self.included(base)
		base.extend(ClassMethods)
	end
end

module ClassMethods
	def my_method
		'a class method'
	end
end

class C
	include M
end

C.my_method  #=> 'a class method'
```

#####  6. 类实例变量
在一个Class对象的实例变量中存储类级别的状态。

```ruby
class C
	@my_class_instance_variable = "some value"

	def self.class_attribute
		@my_class_instance_variable
	end
end

C.class_attribute #=> "some value"
```

#####  7. 类宏
在类定义中使用一个类方法。

```ruby
class C; end

class << C
	def my_macro(arg)
		"my_macro(#{arg}) called"
	end
end

class C
	my_macro :x #=> "my_macro(x) called"
end
```

#####  8. 洁净室(Clean Room)
使用对象作为执行块的上下文环境。

```ruby
class CleanRoom
	def a_useful_method(x); x * 2; end
end

CleanRoom.new.instance_eval { a_useful_method(3) } #=> 6
```

##### 9. 代码处理器
处理从外部获得的字符串代码。

```ruby
File.readlines("a_file_containing_lines_of_ruby.text").each do |line|
	puts "#{line.chomp}===>#{eval(line)}"
end

#>>1+1===>2
#>>3*2===>6
#>>Math.lo10(100)===>2.0
```

#####  10. 上下文探针
执行块来获取对象的上下文中的信息。

```ruby
class C
	def initialize
		@x = "a private instance variable"
	end
end

obj = C.new
obj.instance_eval{ @x } #=> "a private instance variable"
```

#####  11. 延迟执行
在**proc**或**lambda**中存储一段代码及其上下文，用于以后执行。

```ruby
class C
	def store(&block)
		@my_code_capsule = block
	end

	def execute
		@my_code_capsule.call
	end
end

obj = C.new
obj.store { $X = 1 }
$X = 0
obj.execute
$X #=> 1
```

##### 12. 动态派发
在运行时决定调用哪个方法。

```ruby
method_to_call=:reverse
obj="abc"

obj.send(method_to_call) #=> "cba"
```

##### 13. 动态方法
在运行时才决定如何定义一个方法。

```ruby
class C
end

C.class_eval do
	define_method:my_method do
		"a dynamic method"
	end
end

obj = C.new
obj.my_method #=> "a dynamic method"
```

##### 14. 动态代理
把不能对应某个方法名的消息转发给另外一个对象。

```ruby
class MyDynamicProxy
	def initialize(target)
		@target = target
	end

	def method_missing(name, *args, &block)
		"result:#{@target.send(name,*args,&block)}"
	end
end

obj = MyDynamicProxy.new("a string")
obj.reverse #=> "result: gnirts a"
```

##### 15. 扁平作用域
使用闭包在两个作用于之间共享变量。

```ruby
class C
	def an_attribute
		@attr
	end
end

obj = C.new
a_variable = 100

#flatscope:
obj.instance_eval do
	@attr = a_variable
end

obj.an_attribute #=> 100
```

##### 16. 幽灵方法
响应一个没有关联方法的消息

```ruby
class C
	def method_missing(name, *args)
		name.to_s.reverse
	end
end

obj = C.new
obj.my_ghost_method #=> "dohtem_tsohg_ym"
```

###### 17. 钩子方法

通过覆写某个特殊方法来截获对象模型事件。

```ruby
$INHERITORS=[]

class C
	def self.inherited(subclass)
		$INHERITORS<<subclass
	end
end

class D<C
end

class E<C
end

class F<E
end

$INHERITORS  #=>[D,E,F]
```

######  18. 内核方法
在Kernel模块中定义一个方法，使之对所有对象都可用。

```ruby
module Kernel
	def a_method
		"a kernel method"
	end
end

a_method #=> 'a kernel method'
```

#####  19. 惰性实例变量
当第一次访问一个实例变量时才对之进行初始化。

```ruby
class C
	def attribute
		@attribute = @attribute || "some value"
	end
end
obj = C.new
obj.attribute #=> 'some value'
```

#####  20. 拟态方法
把一个方法伪装成另外一种语言构件。

```
def BaseClass(name)
	name=="string" ? String : Object
end

class C<BaseClass "string"		
	attr_accessor :an_attribute
end

obj = C.new
obj.an_attribute=1
```

#####  21. 猴子补丁
修改已有类的特性

```ruby
"abc".reverse  #=> "cba"

class String
	def reverse
		"override"
	end
end

"abc".reverse #=> "override"
```

#####  22. 有名参数
把方法参数收集到一个哈希表中，以便通过名字访问。

```ruby
def my_method(args)
	args[:arg2]
end

my_method(:arg1=>"A", :arg2=>"B", :arg3=>"C")  #=> "B"
```

#####  23. 命名空间
在一个模块中定义常亮，以防止命名冲突。

```ruby
module MyNamespace
	class Array
		def to_s
			"myclass"
		end
	end
end

Array.new #=> []
MyNamespace::Array.new #=> "myclass"
```

#####  24. 空指针保护
用“或”操作符覆盖一个空引用。

```ruby
x = nil
y = x || "a value"  #=> "a value"
```

#####  25. 对象扩展
通过给一个对象的gigenclass混入模块来定义单件方法。

```ruby
obj=Object.new

module M
	def my_method
		'a singleton method'
	end
end

class<<obj
	include M
end

obj.my_method  #=> "a singleton method"
```

#####  26. 打开类
修改已有的类

```ruby
class String
	def my_string_method
		"my method"
	end
end

"abc".my_string_method #=> "my method"
```

##### 27. 模式派发
根据名字来选择需要调用的方法

```ruby
$x = 0
class C
	def my_first_method
		$x += 1
	end

	def my_second_method
		$x += 2
	end
end

obj = C.new
obj.methods.each do |m|
	obj.send(m) if m.to_s =~ /^my_/
end
$x #=> 3
```

##### 28. 沙盒
在一个安全的环境中执行未授信的代码。

```ruby
def sandbox(&code)
	proc {
		$SAFE = 2
		yield
	}.call
end

begin
	sanbox{ File.delete 'a_file' }
rescue Exception => ex
	ex #=> <SecurityError:Insecure operation 'delete' at level 2>
end

##### 29. 作用域门
用class,model或def关键字来隔离作用域

```ruby
a=1
defined? a #=> "local-variable"

module MyModule
	b = 1
	defined? a #=> nil
	defined? b #=> "local-variable"
end

defined? a #=> "local-variable"
defined? b #=> nil
```

#####  30. Self Yield
把self传给当前块。

```ruby
class Person
	attr_accessor :name, :surname

	define initialize
		yield self
	end
end

joe = Persion.new do |p|
	p.name = 'Joe'
	p.surname = 'Smith'
end
```

#####  31.  共享作用域
在同一个**扁平作用域**的多个上下文中共享变量。

```ruby
lambda {
	shared = 10
	self.class.class_eval do
		define_method: couter do
			shared
		end

		define_method :down do
			shared -= 1
		end
	end
}.call

Counter #=> 10
3.times { down }
Counter #=> 7
```


#####  32. 单件方法
在一个对象上定义一个方法

```ruby
obj = "abc"

class << obj
	def my_singleton_method
		"x"
	end
end

obj.my_singleton_method #=> "x"
```

#####  33. 代码字符串
执行一段表示Ruby代码的字符串。

```ruby
my_string_of_code="1+1"
eval(my_string_of_code) #=> 2
```

#####  34. 符号到Proc
把一个符号转换为调用单个方法的代码块。

```ruby
[1,2,3,4].map(&:even?)  #=> [false, true, false, true]
```

### 短评

推荐看第一部分和第三部分。
中间讲Rails代码的部分可以跳过，因为新版本的rails代码已经变化了。

### 结尾

本篇是最近一段时间学习感悟，因为最近技术栈的切换，感悟会比较多，比较浅，我相信随着时间的推移，有些会落后，甚至推翻自己的观点，如果大家有建议，感悟或相反观点，欢迎和我一起交流。