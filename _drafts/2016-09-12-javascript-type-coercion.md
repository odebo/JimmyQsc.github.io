---
title: JavaScript Type Coercion
subtitle: 类型转换
date: 2016-09-22
categories: 日记
author: Jimmy Q
permalink: js-coercion
---

在JavaScript中使用`==`经常会得到令人意想不到的结果，因为使用它时，js会执行一种颇具争议的行为：**type coercion**。
这种行为用一句话描述就是：在运行时根据值被使用的context转换该值的类型(converting a value from one type to another
 based on how the value is used)。
程序语言是没有歧义的，他一定会按照特定规则运行，但是这种 **implicit** 类型转换有时会让人摸不着头脑。
作为开发者，我们应该在充分理解这些规则的前提下使用它，以避免（人为理解上的）歧义。



## Data types in JavaScript

#### ECMAScript 标准中定义的数据类型：

primitive: `undefined`, `string`, `number`, `boolean`, `null`, `symbol`

composite( reference ): `object`, (`array`)

在浏览器中用 `Object.prototype.toString` 来判断类型会给你更丰富的结果：

```javascript
//ENV: Chrome console
var getClass = Object.prototype.toString;

function a() {
    return getClass.call(arguments);
}

a();
// -> "[object Arguments]"
getClass.call(a);
// -> "[object Function]"
getClass.call(null);
// -> "[object Null]"
getClass.call(undefined);
// -> "[object Undefined]"
getClass.call(2);
// -> "[object Number]"
getClass.call('');
// -> "[object String]"
getClass.call(true);
// -> "[object Boolean]"
getClass.call({});
// -> "[object Object]"
getClass.call(new Error());
// -> "[object Error]"
getClass.call(new Date());
// -> "[object Date]"
getClass.call([]);
// -> "[object Array]"
getClass.call(/.*/);
// -> "[object RegExp]"
getClass.call(JSON);
// -> "[object JSON]"
getClass.call(Math);
// -> "[object Math]"
```

有一种说法是：任何其他类型(not object)都是 _subtype of object_ 或者 _native object_。
上面的方法返回的是这个 _native object_ 的 `[[class]]` 属性。


## 从 *toString()* 说起

>Every object has a toString() method that is automatically called when the object is 
>to be represented as a text value or when an object is referred to in a manner in which 
>a string is expected. By default, the toString() method is inherited by every object 
>descended from Object. If this method is not overridden in a custom object, toString() 
>returns "[object type]", where type is the object type.           ————MDN

在浏览器这个运行环境中，数据只能以text的形式出现在页面中，所以 implicitly calling toString 是一个可取的做法，这是 coercion
 的一种形式。上面判断类型的方法运用了 `Object.prototype.toString` 的特性，在网页上表现一个object太难了，一个object可能会非常复杂
 ，所以 `Object.prototype.toString` 的实现也很合理（given the context of a web page）。

除了`Object.prototype.toString`之外，还有：
    `Symbol.prototype.toString()`, `Number.prototype.toString()`, `Function.prototype.toString()`, 
    `Boolean.prototype.toString()`, `String.prototype.toString()`, `Date.prototype.toString()`, 
    `RegExp.prototype.toString()`, `Array.prototype.toString()`, `Error.prototype.toString()`


## Strict and type-converting comparitions