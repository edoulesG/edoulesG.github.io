---
layout: post
title: "Js上传图片直接预览"
author: "Martube"
categories: technology
tags: [technology,js]
image: home-bg.jpg
---

### 前言

要实现的功能：
 在点击`input type="file`这个控件，选择图片文件后，在没有上传到后台之前，直接把图片显示出来。

没有了后台的步骤，上传图片的体验会更加简洁迅速。

### 实现代码

使用 iframe来避免刷新

```html
 <!-- 企业照片上传开始 -->
<div style="display: none;">
    <iframe src="" name="fs"></iframe>
</div>

<form id="photoform" name="fileForm" target="fs" method="POST" enctype="multipart/form-data">
        <div style=" position: relative;">
            <span id="tip">选择图片(大小不超过1M)</span>
            <input id="photo" name="file" type="file" multiple="true" style="opacity:0;height: 130px;position: absolute;top: 0;left: 0;z-index: 10;">
            <img id="showimg" src="" style="display: none;width: 350px;height: 250px" class="preview"/>
        </div>
            <input id="photo" name="file" type="file" multiple="true"> -->
            <div class="showFileName" style="display: none;"></div>
</form>
<!-- 企业照片上传结束 -->
```


Js 部分可以说是核心，但是逻辑也相当简单

一个显示图片的方法，一个生成图片预览url的方法`window.createObjectURL`

```js
//文件名显示+图片预览
$('[name=file]').change(function(e) {
    changepic(this);
});

function changepic(obj) {
    $(obj).closest('.row').find('.showFileName').html(obj.files[0].name);
    //console.log(obj.files[0]);//这里可以获取上传文件的name
    var newsrc=getObjectURL(obj.files[0]);
    // $('#preview').src=newsrc;
    //仅设置了图片显示的上传区域才显示
    $(obj).closest('.row').find('.preview').attr('src', newsrc).show();  
    // $('#preview').attr('src', newsrc);
}

//建立一個可存取到該file的url 预览图片核心方法
function getObjectURL(file) {
    var url = null ;
    // 下面函数执行的效果是一样的，只是需要针对不同的浏览器执行不同的 js 函数而已
    if (window.createObjectURL!=undefined) { // basic
        url = window.createObjectURL(file) ;
    } else if (window.URL!=undefined) { // mozilla(firefox)
        url = window.URL.createObjectURL(file) ;
    } else if (window.webkitURL!=undefined) { // webkit or chrome
        url = window.webkitURL.createObjectURL(file) ;
    }
    return url ;
}
```

