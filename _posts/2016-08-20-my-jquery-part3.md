---
title: My jQuery ( part 3 )
subtitle: 继续extend jQuery
date: 2016-08-20
categories: 笔记
author: Jimmy Q
permalink: my_jquery_p3
---
jQuery最有用的就是他的dom方法，大大简化了程序员对dom操作要写的代码。所以这次来实现一下jQuery操作dom的方法。
当然，思路还是从原生JS入手，利用原生JS提供的方法实现我们想要的功能，这并不代表我们能复制jQuery做的一切。

## 从dom中找元素，著名的 $

jQuery在做什么？你给他一个css选择器（string），他给你css选择器对应的dom元素（arrayLike object），写过css的同学都会顺理成章的觉得这是操作dom最自然的方式吧。然后你在使用这些被选中的的元素时，会发现他们身上多了很多十分方便好用的方法。

因此我们认为：1. jQuery是一个输入值（参数）为string，输出值（返回值）为object的函数； 2. 这个函数执行后返回的对象中，包含了jQuery封装后的dom元素.

有了以上两点，我又使劲想了一想，这和｀构造函数｀还挺像的。构造函数是：你传给他的参数，一般是你要初始化一个实例时要他具备的值，这些值成为了这个对象的属性，然后他会返回一个对象，这个对象上可能有很多方法。因此我们想jQuery也是这么个函数，你给他css选择器，他帮你选择好dom元素，然后这些dom元素只是他返回对象的属性。不出意外的话，以上的应该算是比较合理的逻辑。（想到这里顺便提一下，由于每次用`$`都要实例化一个jQuery对象，所以最好把它存放在变量中，就像缓存一样，避免浪费内存。）

现在我们姑且不管第2点，第1点的实现思路无非就是，创建一个jquery函数（一个构造函数），在这个函数当中我们要做的是：1. 利用我们知道的javascript原生方法得到dom元素，2. 创建一个arrayLike object, 把这些dom元素放在这个对象中，3. 返回这个对象。

### 先来看下原生的javascript的操作dom的方法。

```javascript
document.getElementById( id );  //-> return a sigle element, this is the fastest
document.getElementsByTagName( tag );  //-> return an array of elements
document.getElementsByClassName( class );  //-> return an array of elements

document.querySelector( cssSelector );  //-> return the first element that matches the selector
document.querySelectorAll( cssSelector );  //-> return an array of elements matches the cssSelector
```

按照我们上面的思路，我们可能会有下面的代码

```javascript
$ = function(selector) {
    var elements = document.querySelectorAll(selector);
    for(var i = 0; i < elements.length; i++){
        //假设你会用new关键字创建jquery对象
        this[i] = elements[i]; //这并不代表this是数组，这只是赋值
    }
    //Array.prototype.push.apply(this, elements);
    this.length = elements.length;
}
```
OK! 第一步完成了，不过要知道，jquery远比这个复杂。

如果你熟悉原型继承的话，就会明白，通过$(selector) 创建的对象的那些方便好用的方法，都是从`$.prototype`上继承而来的。我下次再写一些常用方法的实现。

## 最后

要理解以上的内容，要有一定的面相对象的知识，总的来说还是很容易的。

我每篇写的都不长，因为太长了反而让人没耐心看，希望你能理解上面的内容，有什么错误请在下面留言区提出来。