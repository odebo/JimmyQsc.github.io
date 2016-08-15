---
title: (Responsive images)响应式图片
subtitle: 优化网页上的图片元素，获得更好的用户体验
date: 2016-08-13
categories: 笔记
author: Jimmy Q
permalink: responsive-images
---

我们讨论网页上的图片，就是在研究用户看到图片要花费的流量和时间。当前人们可以在手机，平板，电脑甚至手表和电视上消费网页内容，它们的尺寸和像素密度（PPI）都不尽相同，响应式图片（Responsive Images）的目标就是保证各种设备显示效果的同时，尽量减少用户加载图片的时间。

## 第一个要考虑的问题：我是否真的需要一张图片？

如果要用到社交网络图标或者别的常见图标，可以使用*图标字体*（Icon font），就是在一个字体文件，字符被换成了图标。可以像使用任何字体一样使用它，而且是矢量的。网上有很多免费的库可以用。比如：[weloveiconfonts.com](http://weloveiconfonts.com/)

而且很多图标都能在*unicode符号表*中找到，去找找看吧，you will be impressed!

## 我需要什么类型的图片？

如果是图标或是简单形状组成的图片可以选择 svg 格式的矢量图，你可以直接把svg代码写到html中，用css添加样式，不仅体积小而且不失真。

## 如何优化非矢量图片？

### 1. 优化图片的思路

Fact 1: 一张图片的大小（total bits）＝ 像素数（pixels）* 每个像素的大小（bits per pixel），像素数是由图片的分辨率（resolution）决定的，BPP是由图片压缩率决定的。

策略：尽量减小图片的分辨率，增大它的压缩率

Fact 2: 一张图片要获得好的显示效果，由于不同的设备有着不同的宽高比、分辨率和像素密度，它们对这张图片的分辨率和宽高比的要求也不同

策略：一张图片（html中的一个`<img>`元素）提供不同的图片源，让设备选择适合自己的下载

### 2. 尽量减小图片的分辨率，增大它的压缩率

* 确保一张图片的分辨率不要超过它在设备上显示的实际最大分辨率
* 使用工具压缩图片。如果你使用自动化工具（gulp或grunt）的话，这件事就很简单了，有很多npm包可供使用。比如：[gulp-imagemin](https://www.npmjs.com/package/gulp-imagemin), [grunt-image](https://www.npmjs.com/package/grunt-image)

### 3. 为一个图片元素提供不同的图片源

#### 1. 使用`<img>`的 `srcset`属性

`srcset`是html5中`<img>`元素的新属性，它的值是一个或多个字符串，由逗号分隔开，代表这个元素可能的图片源

```html
<img src="images/photo.jpg" srcset="images/photo.jpg 1x, images/photo-2x.jpg 2x,
images/photo-hd.jpg 3x">
```
浏览器是怎样通过以上代码决定下载哪张图片的呢？：

* 首先在不支持`srcset`的浏览器上直接下载`src`确定的图片源，srcset被忽略
* 浏览器查看`srcset`属性，每个字符串包含由空格分开的两个字符串，前一个代表图片源后一个代表像素密度（pixel density），相当于告诉浏览器：如果你的像素密度是1，就下载`images/photo.jpg`；如果是2就下载`images/photo-2x.jpg`..以此类推
* 浏览器查看设备信息，下载设备的像素密度所对应的图片

再看一个例子

```html
<img src="images/photo.jpg" srcset="images/photo.jpg 500w, images/photo-2x.jpg 1000w,
images/photo-hd.jpg 1500w" alt="photo description">
```
* 与上面不同的是，`srcset`中的`500w`, `1000w`, `1500w`代表了每张图片的宽度
* 浏览器现在并不知道图片的显示宽度，它假定图片的显示宽度是浏览器窗口的宽度
* 浏览器根据自己窗口的宽度选择下载合适的图片

#### 2. 使用`<img>`的 `sizes`属性

上面的第二个例子有明显的缺点，因为它并不知道图片的显示宽度。幸好我们可以通过`sizes`属性来指定图片的显示宽度。

```html
<img src="images/photo.jpg" srcset="images/photo.jpg 500w, images/photo-2x.jpg 1000w,
images/photo-hd.jpg 1500w" sizes="50vw" alt="photo description">
```

上面的代码告诉浏览器，这张图片的宽度始终是viewport宽度的一半，请根据viewport宽度和图片显示宽度下载合适的图片。

`sizes`属性和media query配合使用更加强大

```html
<img src="images/photo.jpg" srcset="images/photo.jpg 500w, images/photo-2x.jpg 1000w,
images/photo-hd.jpg 1500w" sizes="(max-width: 500px) 50vw, 25vw" alt="photo description">
```
上面的代码告诉浏览器，这张图片的宽度在viewport宽度小于500px时是viewport宽度的一半，在其他情况下是viewport宽度的四分之一，请根据viewport和图片显示宽度下载合适的图片。所以在更改浏览器宽度的时候浏览器会下载不同的图片

#### 3. `<picture>`元素

> The HTML <picture> element is a container used to specify multiple <source> elements for a specific <img> contained in it. The browser will choose the most suitable source according to the current layout of the page (the constraints of the box the image will appear in) and the device it will be displayed on (e.g. a normal or hiDPI device.)

它的给了浏览器更多的选项，比如图片的格式，让支持某种格式的浏览器优先选择这种格式，不支持的默认使用`<img>`中的图片

```html
​<picture>
    <source srcset="mdn-logo.svg" type="image/svg+xml">
    <img src="mdn-logo.png" alt="MDN">
</picture>
```

## 最后

现在浏览器越来越智能，我们以上的所有方法都是在做一件事：告诉浏览器更多的关于图片的信息。这样浏览器就可以选择最佳的图片，提供最佳用户体验。
