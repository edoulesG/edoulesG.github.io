---
layout: post
title: "Js计算字符串中某一个字符的数量"
author: "Martube"
categories: technology
tags: [technology,js]
image: cutting.jpg
---

# Js计算字符串中某一个字符的数量

做个记录~

```js
var reg = /[A]/g;
var str = "a1Ab2c3dqq4";
alert(str.match(reg).length);

```

PS:当 匹配不到的时候 `.length`会报错,所以最好对 `str.match(reg)` 做一个判断。