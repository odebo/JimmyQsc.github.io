---
title: My jQuery ( part 2 )
subtitle: 实现 $.each(), $.makeArray() 和$.proxy()
date: 2016-08-12
categories: 笔记
author: Jimmy Q
permalink: my-jquery-part2
---
------> Continue Part 1. 继续实现 jQuery Functional Utilities。

### _$.each(collection, cd)_ 遍历一个集合（不一定是数组），对其每一个元素执行cb

JavaScript数组有 forEach()函数， $.each()与之类似。

先看参数设置

```javascript
    /**
     * 描述：遍历一个集合（不一定是数组），对其每一个元素执行cb
     * @param  {object}   collection 被遍历的对象
     * @param  {Function} cb         回调函数，对集合中所有元素执行
     * @return {object}              被遍历的对象，这使得方法链接（chaining）成为可能
     */
```

实现过程：

```javascript
$.extend($, {   //$.extend, 来自 part 1
    each: function(collection, cb) {
        if ($.isArrayLike(collection)) { // 判断它是否像数组
            for (var i = 0; i < collection.length; i++) { // 遍历它
                var value = collection[i]; // 把当前值存入 value
                cb.call(value, value, i);   // 用 call() 把 this 绑定为当前的 value (第一个参数为excute context)
                                            // 然后传入value 和 value的位置
            }
        } else {
            for (var prop in collection) {
                if (collection.hasOwnProperty(prop)) { // 确保prop 是在它本身上，而不是在proto chain 上
                    var value = collection[prop];
                    cb.call(value, value, prop);
                }
            }
        }
        return collection;  // 返回这个集合，这样就可以方法链接(chaining)了
    }
})
```

### _$.makeArray(obj)_ 传入一个像数组的对象返回一个真正的数组

这个方法非常有用，因为我们会遇到很多像数组却不是数组的对象（例如：`document.body.childNodes`），然而某些方法只对数组起作用，可以
用这个方法产生一个数组

思路：

1. `$.isArrayLike(obj) == true`, obj有`length`属性，可以通过`obj[index] // index >= 0 && index < length`访问他包含的所有值
2. 新建一个数组，遍历obj，把obj的所有值推入新建的数组

```javascript
$.extend($, {
    makeArray: function( obj ) {
        var array = []; //新建的数组
        $.each(obj, function(value, index) { //后一个参数可以省略不写
            array.push(value);
        })
        return array;
    }
})
```

### _$.proxy(fn, context)_ 传入一个函数`fn`和一个`context` 对象， 返回一个把`context`作为特定执行上下文（excute context）的函数

基本上就是`fn.bind(context);`

为什么这个有用？

```javascript
//在console输入
var me = {
    name: 'Jimmy',
    speak: function() {
        console.log( this.name + ' love you!');
    }
}

me.speak(); // --> Jimmy love you!

var say = me.speak;
say(); // -->  love you!

window.name = 'Window';
say(); // --> Window love you!
```
在调用`say`时没有输出‘jimmy’，因为调用的形式不是`obj.method()`，所以第二次调用`this`指的是`global`(在浏览器中是`window`)。

实现方法：

```javascript
$.extend($, {
    proxy: function(fn, context) {
        return function() {
            return fn.apply(context, arguments); //使用apply，第一个参数就会被作为执行上下文（excute context）
        }
    }
})
```

------> 还有 Part 3