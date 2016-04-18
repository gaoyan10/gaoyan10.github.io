---
layout: post
title: javascript与类
categories: [语言学习]
tags: [javascript, 类, object]
---
Javascript没有class关键字，不支持类。但是我们依然可以很方便的使用对象。在JavaScript里可以简单的把{}理解为一个对象。

## prototype（原型）
javascript是prototype based programming语言，与通常的class based programming有很大的区别。

1. 函数与对象有相同的语言地位
2. 没有类，只有对象
3. 函数也是一种对象，所谓的函数对象，这就不难理解window.onload = functionname，这种语法了。
4. 对象按照引用来传递

测试以下代码：

```
<!DOCTYPE html>
<html>
	<head>
		<title>测试prototype</title>
		<script type="text/javascript">
		function test (a, b, c) {
			return a * b * c;
		}
		alert(test.length);
		alert(typeof test.constructor);
		alert(typeof test.call);
		alert(typeof test.apply);
		alert(typeof test.prototype);
		</script>
	</head>
	<body></body>
</html>
```
我们会发现length是三，length也就是形参的个数
接下来三个都是function。
但是prototype是个对象，**是个对象！**

接下来试试这段代码：

```
<!DOCTYPE html>
<html>
	<head>
		<title>测试prototype</title>
		<script type="text/javascript">
		function test (a, b, c) {
			return a * b * c;
		}
		alert(test.length);
		alert(typeof test.constructor);
		alert(typeof test.call);
		alert(typeof test.apply);
		alert(typeof test.prototype);
		test.prototype.name="I am test function";
		alert(test.prototype.name);
		test.prototype.getName = function(){
			return this.name;
		}
		alert(test.prototype.getName());
		</script>
	</head>
	<body></body>
</html>
```
代码中我们为prototype添加了name属性和getName()方法。
所以到目前为止我们了解到<h3>**prototype是一个object，每个function都有，并且能动态的添加property**</h3>

继续试验以下代码：

```
<!DOCTYPE html>
<html>
	<head>
		<title>测试prototype</title>
		<script type="text/javascript">
		function test (a, b, c) {
			return a * b * c;
		}
		alert(test.length);
		//alert(typeof test.constructor);
		//alert(typeof test.call);
		//alert(typeof test.apply);
		//alert(typeof test.prototype);
		test.prototype.name="I am test function";
		//alert(test.prototype.name);
		test.prototype.getName = function(){
			return this.name;
		}
		alert(test.prototype.getName());

		//继续测试
		var testObj = new test(1, 2, 3);
		alert(testObj.name);
		alert(testObj.getName());
		</script>
	</head>
	<body></body>
</html>

```
new了一个对象后，可以直接使用prototype动态添加的property。有人在这里理解为<h3>**创建的对象，可以继承prototype创建的属性和方法**</h3>

接着看以下代码：

```
<!DOCTYPE html>
<html>
	<head>
		<title>测试prototype</title>
		<script type="text/javascript">
		function test (a, b, c) {
			return a * b * c;
		}
		//alert(test.length);
		//alert(typeof test.constructor);
		//alert(typeof test.call);
		//alert(typeof test.apply);
		//alert(typeof test.prototype);
		test.age = 10;
		test.sex = "男";
		test.prototype.age = 12;
		test.prototype.name="I am test function";
		//alert(test.prototype.name);
		test.prototype.getName = function(){
			return this.name;
		}
		alert(test.prototype.getName());
		alert(test.age); //输出10 
		//继续测试
		var testObj = new test(1, 2, 3);
		alert(testObj.name); 
		alert(testObj.getName());
		alert(testObj.age); //输出12
		alert(test.sex); //输出男
		alert(testObj.sex); //输出undefined
		</script>
	</head>
	<body></body>
</html>
```
1. **所以function自身的方法和属性是静态的，声明function后再添加，由该function 生成的对象（new）并不能获得那些属性和方法，没有继承特点。**

2. <h3>prototype动态添加的方法或者修改的方法是动态的，父函数创建的对象自动继承这些prototype的属性</h3>

当function本身和prototype都有这个属性时怎么处理呢？
既然有函数对象本身的属性, 也有prototype的属性, 那么是由其创建的对象是如何搜索相应的属性的呢?

1. **先去搜索函数对象本身的属性,如果找到立即执行**
2. **如果1没有找到,则会去搜索prototype属性,有2种结果,找到则直接执行,否则继续搜索 父对象 的 父对象 的prototype, 直至找到,或者到达 prototype chain 的结尾(结尾会是Object对象)**

## 关于两种perproty
[廖雪峰的博客Javascript 面向对象编程（一）：封装](http://www.ruanyifeng.com/blog/2010/05/object-oriented_javascript_encapsulation.html)，对prototype动态添加和function自带的属性描述较好。
总结为以下：

1. 每个function new出来的对象的属性都有可能不同的，就直接使用function自带的属性，new的时候初始化
2. 每个function new出来的对象属性都相同的就使用prototype添加属性，内存优化。

比如

```
　　function Cat(name,color){
　　　　this.name = name;
　　　　this.color = color;
　　}
　　Cat.prototype.type = "猫科动物";
　　Cat.prototype.eat = function(){alert("吃老鼠")};
	//然后，生成实例。
　　var cat1 = new Cat("大毛","黄色");
　　var cat2 = new Cat("二毛","黑色");
　　alert(cat1.type); // 猫科动物
　　cat1.eat(); // 吃老鼠
```