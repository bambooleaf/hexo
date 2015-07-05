title: "javascript变量 作用域 内存"
date: 2015-05-18 21:53:01
categories: 学习笔记
tags: javascript
---
#基本类型和引用类型

 - 只能给引用类型值动态地添加属性
 - 基本类型值在内存中占据固定大小的空间，因此被保存在栈内存中；引用类型的值是对象，保存在堆内存中；
 - 基本类型复制，是“真复制”，引用类型复制，复制的是“指针”
 - ECMAScript 中所有函数的参数都是按**值传递**的
 - 检测类型

 
 ```javascript
 var s = "Nicholas";
 var b = true;
var i = 22;
var u;
var n = null;
var o = new Object();
alert(typeof s); //string
alert(typeof i); //number
alert(typeof b); //boolean
alert(typeof u); //undefined
alert(typeof n); //object
alert(typeof o); //object
 ```
 
<!--more-->
 对于引用类型的检测，使用instanceof
 
 
 - 当代码在一个环境中执行时，会创建变量对象的一个作用域链（scope chain）。作用域链的用途，是保证对执行环境有权访问的所有变量和函数的有序访问。内部环境可以通过作用域链访问所有的外部环境，但外部环境不能访问内部环境中的任何变量和函数。
 - 延长作用域链
  当执行try-catch 语句的catch 块，with 语句，作用域链会延长。
 - 没有块级作用域
 
```javascript
for (var i=0; i < 10; i++){
doSomething(i);
}
alert(i); //10
```
对于JavaScript 来说，由for 语句创建的变量i 即使在for 循环执行结束后，也依旧会存在
于循环外部的执行环境中。

 - 垃圾收集
  标记清除，引用计数（存在循环引用的问题）

 


