---
title: Kotlin学习笔记1
date: 2017-12-30 11:47:39
tags: [Kotlin]
categories: Kotlin
---

随着Kotlin的越来越火热，对于Android开发者而言学习Kotlin也变成了一个必要的过程了。


##### 基础语法

###### 定义一个类

```
class User(name: String, age: Int) {} //如果类没有内容则大括号可以省略掉
```

构造函数的函数体，即下列代码的init块中

```
class User(name: String, age: Int) {
	init {
		...
	}
}
```

###### 类继承

默认任何类都是继承基础类Any（类似于java中的Object），但是我们可以继承其它类。所有的类默认都是final的，因此如果我们要让别的类继承的话就需要声明open或者abstract关键字。

```
class User(name: String, age: Int)

class Max(name: String, age: Int, gender: String) : User(name, age)
```
如上所示，如果只有单个构造器时，需要从父类继承下来的构造器中指定需要的参数，类似于java super关键字


###### 函数

函数使用fun声明:

```
 fun add(x: Int, y: Int): Int {
        return x + y
    }
   
   func add(x: Int, y: Int) : Int = x+y
    
  无返回值的函数，会返回Unit，于java中的void类似，但是Unit是一个真正的对象
```

###### 构造方法和函数参数

```
toast("hell") //使用默认的Toast.LENGTH_LONG
toast("hell",Toast.LENGTH_LONG)


fun toast(msg: String, length: Int = Toast.LENGTH_LONG) {//执行函数默认值，避免方法重载
        Toast.makeText(this,msg, length).show()
    }
```