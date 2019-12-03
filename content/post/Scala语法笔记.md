---
title: "Scala语法笔记"
date: 2018-11-01T01:15:09+08:00
draft: false
tags: ["编程语言"]
reward: true
---

## 函数

如果函数没参数可以不写括号
```
def three = 1 + 2
```
匿名函数
```
(x: Int) => x + 1
val addOne = (x: Int) => x + 1
```
部分应用
```
def adder(m: Int, n: Int) = m + n
val add2 = adder(2, _: Int)
add2(3)
res: Int = 5
```
科里化
```
def multiply(m: Int)(n: Int): Int = m * n
multiply(2)(3)
val timesTwo = multiply(2) _
times(3)

```
## 类
除了方法之外的代码全是构造函数

### private 
内部类可以访问private

外部类不能访问private

但是Java中都可以
```
class Outer{
    class Inner{
    private def f(){println("f")}
    class InnerMost{
        f() // 正确
        }
    }
    (new Inner).f() //错误
}
```

### protected
只有子类能访问protected

Java中同一个包中的也可以访问protected
```
package p{
class Super{
    protected def f() {println("f")}
    }
    class Sub extends Super{
        f()
    }
    class Other{
        (new Super).f() //错误
    }
}
```
## 包
### 包对象
由于JVM的限制，包可以包含类、对象、特质，但不能包含函数和变量。包对象是用来解决这个问题的语法糖，可以把函数或变量绑定到包上。每个包都可以有个包对象，在父包中定义它，如下代码：
```
package com.horstmann.impatient

package object people {

val defaultName="John Q. Public"

}

package people {

class Person {

var name=defaultName // 从包对象拿到的常置

}

…….

}

}
```
defaultName可以通过com.horstmann.impatient.people.defaultName直接访问。
底层原理：
包对象被编译成名为package.class的JVM类，位于相应的包下。上例中把包对象代码放到一下位置是等价的com/horstmann/impatient/people/package.scala
[包和引用详解](http://lib.csdn.net/article/scala/40444)

## 特质（Traits）和抽象类（Abstract Class）
抽象类定义方法但是不实现
```
abstract class Shape {
    def getArea(): Int
}
```
特质属性和方法
```
trait Car {
    val brand: String
    def run(): Unit
}
```
使用特质还是抽象类？
* 优先使用特质。一个类可以继承多个特质，但只能继承一个抽象类。
* 如果需要构造函数参数，则使用抽象类。抽象类可以定义构造函数参数，特质不行。

## apply方法
对象直接加()就会直接调用apply方法
```
class Foo {}
object FooMaker {
    def apply() = new Foo
}
val newFoo = FooMaker()
```
```
class Bar {
    def apply() = 0
}
val bar = new Bar
bar()
```

## 函数也是对象
但方法不是
## 基本数据额结构

数组和列表区别
* 数组 Array 有序可变
* 列表 List 有序不可变

#### 元组 Tuple 容纳不同类型
获取元素，从1开始 
```
tuple._1
```
元组和模式匹配结合
```
hostPort match {
    case ("localhost", port) => 
    case (host, 80) => 
    case (_, _) =>
}
```
创建两元素元组语法糖
```
1 -> 2
res0: (Int, Int) = (1, 2)
```

#### 映射 Map
初始化Map传递key和value元组即可
```
Map(1 -> "one")
Map(1 -> "one", 2 -> "two")
Map((1, "one"), (2, "two"))
```
map.get(key)会返回下面特质的子类Some[T]或None
```
trait Option[T] {
    def isDefined: Boolean
    def get: T
    def getOrElse(t: T): T
}
```
获取value的方式
```
val res = map.get(key)
val value = if (res.isDefined) {
    res.get
} else {
    0 //key没有匹配到
}
```
第二种
```
val value = res.getOrElse(key)
```
第三种模式匹配
```
val value = res match {
    case Some(n) => n
    case None => 0 //没匹配到的返回值
}
```
## 函数组合子
flatMap = map + flatten

## 模式匹配
---
`a`会让变量模式变成常量模式，下面输出“没有匹配”
```
object Demo {
    val a = 2

    def main(args: Array[String]) = {
        println(testPatternMatching(1))
    }

    def testPatternMatching(e: Int) = e match {
        case `a` => "haha: " + a
        case _ => "没有匹配"
    }
}
```
```
foo match {
    case List(0, _*) =>
    case _ =>
}
```
类型擦除
```

```
变量绑定
```
foo match {
    case Bar("test", e @ 12) => e
    case _ =>
}
```
12则赋值给e，e作为匹配对象
## 语法糖
* 如果只传一个参数可以用花括号代替小括号。
```
println("haha")
println{ "haha" }
```
* 传名函数
() => 可以省略 ()

这样写出来的代码就更像语言原生的了
```
def foo(bar: => Boolan) = 
    bar()
foo(5 > 3)
foo {
    5 > 3
}
```

## 资料
[Twitter Scala School](http://twitter.github.io/scala_school/zh_cn/index.html)
[Effctive Scala](http://twitter.github.io/effectivescala/index-cn.html)
[Implicit 详解](https://blog.csdn.net/l1028386804/article/details/80950393)