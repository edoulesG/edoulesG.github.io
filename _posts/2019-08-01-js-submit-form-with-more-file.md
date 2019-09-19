---
layout: post
title: "Js提交表单时同步上传多文件"
author: "Martube"
categories: technology
tags: [technology,js]
image: spools.jpg
---

#### 前言

文件上传时经常用到的功能之一，最近正好做到了这个功能，在提交表单的同时将文件一起上传，这里做一个记录。


#### 环境

js + ajax + php + mysql


#### 实现思路及代码

这里就说几个关键的地方

* CSS 点击 +  增加文件上传按钮

```js
function addmorefile(){
    var morefile = $('.addfile');

    var html = '';
    html += '<form name="fileForm" target="fs" action="/admin/activity/submitFile" method="POST" enctype="multipart/form-data">';
    html += '<div class="row">';
    html += '   <div class="col-xs-4">';
    html += '       <div style="display: flex;">';
    html += '           <div style="">';
    html += '               <a href="javascript:;" class="n-file">';
    html += '                   选择文件';
    html += '                   <input name="file" type="file">';
    html += '               </a>';
    html += '           </div>';
    html += '           <div style="flex: 1;">';
    html += '               <div class="showFileName" style="line-height: 40px; padding-left: 15px;">[未选择文件...]</div>';
    html += '           </div>';
    html += '       </div>';
    html += '   </div>';
    html += '   <div class="col-xs-6">';
    html += '       <div style="padding: 5px 15px;">';
    html += '           <button type="button" class="btn bg-navy btn-default md-remove">删除</button>';
    html += '       </div>';
    html += '   </div>';
    html += '</div>';
    html += '</form>';
    var element = $(html);
    element.appendTo(morefile);
    element.find('[type="file"]').change(function(e) {
        var name = this.files[0].name;
        $(this).closest('.row').find('.showFileName').html(name);
    });
    element.find('.md-remove').click(function(e) {
        $(this).closest('form').remove();
    });
          

}
```

给拥有 .addfile 这个class 的控件内 添加元素，由于内容相对复杂，但是每次添加的都是重复的内容，所以先定义html 字符串

这里文件上传控件的名字name = file 没有进行调整 `<input name="file" type="file">`,所以之后使用循环来分批进行upload,
也就是有多少个 `type = file` 的 input ,就调用多少次后台方法。

...截图丢了，算了我当个灵魂画师吧，看效果图：

![多文件和表单一起提交效果图](/assets/img/technology/20190801ep1.png)


至于怎么做到循环调用的，下面会说到


* 循环上传

```js
function onSave() {
    var ID = 0;
    var task = new Chain();
    task.method(function(next, _) {

        var parameter = formValueById('form-create');
        var form = $('#form-create');

        // 校验
        var result = true;
        var elements = [];
        form.find('.form-group').removeClass('has-error');

        if (!result) {
            elements.forEach(function(name) {
                form.find('[name="' + name + '"]').closest('.form-group').addClass('has-error');
            });
            return;
        } 

        console.log($('[name="actiontype"]', form).val());

        if ($('[name="actiontype"]', form).val() == 'update') {
            //UPDATE
            Apis.updateActivity(parameter)
            .then(function(response) {
                alert('已保存');
                form[0].reset();
                $('.md-append-panel').hide();
                list();
            });
        } else {
            //CREATE

            Apis.createActivity(parameter)
            .then(function(response) {
                alert('已保存');
                form[0].reset();
                $('.md-append-panel').hide();
                ID = response.data;
                next();
            });
        }
    });

    var formIndex = 0;
    var fileForms = [];
    $('[name="fileForm"]').each(function(i, form) {
        fileForms.push(form);
    });
    fileForms.forEach(function(_) {
        task.method(function(next, _) {
            var form = fileForms[formIndex];
            fileUpload(form, ID, function(oReq, oEvent) {
                formIndex ++;
                if (oReq.status == 200) {
                    console.log('ok');
                  next();
                } else {
                  alert("Error " + oReq.status + " occurred uploading your file.<br \/>")
                }
            });
        });
    });
    task.method(function(next, _) {
        console.log('done');
 
        location.href = '/admin/GeneralInformation/activitycontent/?activityid=' + ID;
    });
    task.start();




}
```

基本思路：点击保存按钮，先将普通表单数据用ajax异步处理，再循环处理file

由于一套流程下来要使用多个ajax异步（这里ajax经过封装，默认异步），并且处理file时需要用到上一步ajax提交表单数据后的返回值(ID)，所以又套用了一个 `var task = new Chain();`。
这个是保证链内的东西能一步一步执行下来，不会前一个ajax没跑完，后边已经开始执行了。


普通表单内容没什么好说的了，重要的看这块：

```js
    var formIndex = 0;
    var fileForms = [];
    $('[name="fileForm"]').each(function(i, form) {
        fileForms.push(form);
    });// 将name = fileForm 的所有表单内容都丢进 fileForms里
    fileForms.forEach(function(_) {//再取出来一个一个执行上传
        task.method(function(next, _) {//这里每一个循环都涉及到上传，所以用了链
            var form = fileForms[formIndex];
            fileUpload(form, ID, function(oReq, oEvent) {//上传的具体方法
                formIndex ++;//index++ 
                if (oReq.status == 200) {
                    console.log('ok');
                  next();
                } else {
                  alert("Error " + oReq.status + " occurred uploading your file.<br \/>")
                }
            });
        });
    });
    task.method(function(next, _) {
        console.log('done');
 
        location.href = '/admin/GeneralInformation/activitycontent/?activityid=' + ID;
    });
```

上传的具体方法fileUpload 类似 ajax 但有不同 

```js
function fileUpload(form, ID, callback) {
  var oData = new FormData(form);
  oData.append('id', ID);
  var oReq = new XMLHttpRequest();
  oReq.open("POST", "/admin/activity/submitFile", true); //XMLHttpRequest
  oReq.onload = function(oEvent) {
    callback(oReq, oEvent);
    // if (oReq.status == 200) {
    //   alert("Uploaded!");
    // } else {
    //   alert("Error " + oReq.status + " occurred uploading your file.<br \/>")
    // }
  };
  oReq.send(oData);
}
```
<!-- password 20190801 -->
<!-- ![源码下载戳这里](/assets/data/20190801epfile.rar) -->

<a href="/assets/data/20190801epfile.rar">源码下载戳这里</a>

以上。