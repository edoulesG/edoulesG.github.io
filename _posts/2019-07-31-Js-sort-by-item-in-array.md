---
layout: post
title: "Js按照数组内元素某一属性进行大小排序"
author: "Martube"
categories: technology
tags: [technology,php]
image: post-bg-kuaidi.jpg
---

### 前言

排序是经常会使用到的，但多维数组的排序在js种并没有原生的方法,这里记录一下。
注：sort是原生的

### 案例代码

```js
var data = [
    {
        name:"jiang",
        age:22
    },
    {
        name:"pander",
        age:21
    },
    {
        name:"krome",
        age:25
    },
    {
        name:"Koges",
        age:20
    }
];

//定义一个比较器 现在是从小到大
function compare(propertyName){
    return function(object1,object2){
        var value1 = object1[propertyName];
        var value2 = object2[propertyName];
        if(value2 < value1){
            return 1;
        }else if(value2 > value1){
            return -1;
        }else{
            return 0;
        }
    }
}

//使用方法
data.sort(compare("age"));
```
### 输出结果

![输出结果-升序](/assets/img/technology/20190731ep1.png)









