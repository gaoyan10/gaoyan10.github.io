---
layout: post
title: Scala基础教程总结1
categories: [语言学习]
tags: [scala]
---

##Scala基础教程总结1
### scala的变量
```
/**
* 声明一个可变变量
*/
var myVar : String = "Foo"

/**
* 声明一个常量
*/
val myVal : String = "Foo"

```
**val or var VariableName: DataType [= Initial Value]**

###多重任务
```
//返回一个元组
val (myVal1: Int, myVar2: String) = Pair(40, "Foo")
```
###访问限定符，与Java类似
### if-else语句与Java相同
### for循环的<-生成器
```
for (var x <- Range) {
	statement(s);
}
```
```
object Test {
   def main(args: Array[String]) {
      var a = 0;
      // for loop execution with a range
      for( a <- 1 to 10){
         println( "Value of a: " + a );
      }
   }
}
C:/>scalac Test.scala
C:/>scala Test
value of a: 1
value of a: 2
value of a: 3
value of a: 4
value of a: 5
value of a: 6
value of a: 7
value of a: 8
value of a: 9
value of a: 10

```
```
object Test {
   def main(args: Array[String]) {
      var a = 0;
      // for loop execution with a range
      for( a <- 1 until 10){
         println( "Value of a: " + a );
      }
   }
}
C:/>scalac Test.scala
C:/>scala Test
value of a: 1
value of a: 2
value of a: 3
value of a: 4
value of a: 5
value of a: 6
value of a: 7
value of a: 8
value of a: 9

C:/>
```
```
object Test {
   def main(args: Array[String]) {
      var a = 0;
      var b = 0;
      // for loop execution with a range
      for( a <- 1 to 3; b <- 1 to 3){
         println( "Value of a: " + a );
         println( "Value of b: " + b );
      }
   }
}
C:/>scalac Test.scala
C:/>scala Test
Value of a: 1
Value of b: 1
Value of a: 1
Value of b: 2
Value of a: 1
Value of b: 3
Value of a: 2
Value of b: 1
Value of a: 2
Value of b: 2
Value of a: 2
Value of b: 3
Value of a: 3
Value of b: 1
Value of a: 3
Value of b: 2
Value of a: 3
Value of b: 3

C:/>
```
###for循环使用过滤器
```
for( var x <- List
      if condition1; if condition2...
   ){
   statement(s);
}
```
###for循环采用yield
```
var retVal = for { var x <- List
		if condition1; if condition2...
}yield x
for (x <- retVal) {
	println(x);
}
```
###break语句
```
import scala.util.control._

object Test {
   def main(args: Array[String]) {
      var a = 0;
      val numList = List(1,2,3,4,5,6,7,8,9,10);

      val loop = new Breaks;
      loop.breakable {
         for( a <- numList){
            println( "Value of a: " + a );
            if( a == 4 ){
               loop.break;
            }
         }
      }
      println( "After the loop" );
   }
}
```
**可以通过使用多个break达到嵌套的目的**