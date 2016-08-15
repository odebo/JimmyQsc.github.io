---
title: 理解Object Oriented Javascript, 用最丝滑的方式(part 1)
subtitle: 1 Scope and closure 作用域和闭包
date: 2016-08-13
categories: 笔记
author: Jimmy Q
permalink: o-o-js-p1
---

接触JavaScript一年多以来，总能听到别人用奇怪来形容它，它实现面向对象编程的方式确实比较独特，他的很多概念也使人困惑。在使用了它一年之后我也进入了所谓的 comfort zone（爽区），为了使我的读者（如果我有的话）在学习这门语言时少一点困惑，我特意把我一年中学到的知识总结了一下，希望能把它讲清楚，帮助你理解这门语言.

## Intro

Scope 和closure处理的是程序中变量访问（variable access）的问题，JavaScript中的Scope和closure就是JavaScript程序在运行时所要遵循的变量访问的规则。要比较清楚的理解这两个概念，我们首先要知道一个JS文件是如何运行的（从比较宏观的角度）。

## Javascript的运行时编译

首先JavaScript是一门解释型语言（interpreted language），但是在多数当代浏览器（modern browser，区别于ie 678）中，javascript程序运行时都要经过Just-in-time Compiler([JIT编译器](https://en.wikipedia.org/wiki/Just-in-time_compilation))的编译。

我们把JavaScript程序运行分成两个阶段

1. 编译阶段（compile phase）：此阶段的主要工作是把是把变量“注册”到它所在的作用域；

2. 执行阶段（excution phase）：一行一行的执行程序。

我们以一段简单的代码为例来走一遍这个流程：

```javascript
var a = 1;    //js引擎：我认识 a ，它在global scope里，现在我要对它赋值
b();    //js引擎：我认识 b ，它是一个函数，我现在就去执行它，跳到第五行
c();    //js引擎：我认识 c ，它是undefined，后面跟个括号干嘛？报错！

function b() {  //js引擎：我得先让编译器编译这个函数，先创建一个scope，把`value`这个变量放倒scope里面来
    var value = 2;  //js引擎：我认识value，它在这个函数的scope里，现在我对他赋值
    return value;   //返回value
}
//在第三行报错后就不会执行下面的代码了，我还是演示一下执行的过程
var c = function() { //js引擎：我认识c，我现在对它赋值,但我不关心赋的值是什么
    return 3;
};
```
在编译阶段：编译器会先创建一个Scope（可能是一个对象），编译器查看所有的变量声明，包括`var a`, `function b`, `var c`，把声明的变量注册到这个Scope中，所有`=`右边的和函数的内容都被忽略。在此阶段不做任何运算。

在执行阶段：js引擎已经知道了有`a`, `b`, `c`三个变量，`a, c`的值都是`undefined`，`b`是一个函数，但不知道函数的内容。执行过程看代码注释。

通过上面的例子，关于scope我们可以总结出两点：

1. 除了global scope以外，每次**执行**函数体时都会为这个函数创建独立的scope，请记住，每次执行时都会创建，只有在执行这个函数时，JS引擎才回关心它的内容，这是由javascript的运行方式决定的。

2. 函数和变量声明是在编译阶段完成的，即使你把他们写在程序的最末尾，他们也是最先被JS引擎知道的（这就是hoist的概念）。

## JavaScript中的local scope只有function scope

scope在编程语言中是个重要的概念，一个变量是否可以被使用取决于他所在的scope。如果你把scope看作对象的话，就会很容易理解。每个程序的执行，都会创建一个唯一的global scope对象，然后每次调用一个函数，都会为这次调用创建一个scope 对象。所以如果你想要在global scope中嵌套的local scope，唯一的方法就是声明并执行一个函数。local scope中当然也可以嵌套scope。

程序在执行阶段，特定一段代码如果要访问一个变量，js引擎会从离他最近的scope开始向外查找，依次访问包含他的scope object，直到查找到这个变量，如果找到最外面的global scope还未找到这个变量，就会把这个变量注册到global scope中（它的初始值是undefined），继续使用它。

* NOTE: ES6 中加入了用`let`关键字创建block scope。

## 总结

理解了scope才能理解closure，scope已经够长了，下回在写closure吧。









