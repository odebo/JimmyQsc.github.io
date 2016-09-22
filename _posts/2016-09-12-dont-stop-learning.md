---
title: JavaScript Type Coercion
subtitle: 
date: 2016-09-22
categories: 日记
author: Jimmy Q
permalink: js-coercion
---

在JavaScript中使用`==`经常会得到令人意想不到的结果，因为使用它时，js会执行一种颇具争议的行为：
'type coercion'。这种行为用一句话描述就是：运行环境根据值被使用的context转换该值的类型。这种行为容易产生歧义（编程语言应该尽量避免歧义，程序中不允许出现歧义），
JavaScript中的 type coercion 遵循一定的规则，作为开发者，我们应该在充分理解这些规则的前提下使用它，以避免歧义。

## Data types in JavaScript

