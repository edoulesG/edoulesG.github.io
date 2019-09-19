---
layout: post
title: "Js用倒计时限制获取手机短信验证码"
author: "Martube"
categories: technology
tags: [technology,js]
image: forest.jpg
---

### 前言

发短信是需要向运营商付费的，所以一般在发送短信验证码的界面会对发送短信验证码做出限制，以免过多的恶意发送

一个是类似图片验证码的防脚本机器人刷短信验证码

一个是前端做的倒计时 一般是1min-2min的限制

除了前端，后台发短信的模块也要做一个防刷的机制，一般是检测前后调用时间

这里只对前端的倒计时实现做记录

> 参考文档：[JS实现注册登录发送短信验证码动态显示60S倒计时完整案例](https://blog.csdn.net/guobinhui/article/details/82946767)


#### 页面发送短信验证码的表单

```html
<p class="login_p2" style="width: 500px;">
    <input id="code" class="input-val" name="code" class="login_text1" tabindex="3"  type="text" value="" autocomplete="off" placeholder="短信验证码"/>
    <button type="button" class="btn btn-primary btn-flat" style="height: 49px;margin-left: 5px" id="send">获取短信验证码</button>
</p>
```

## 发送短信验证码的JS处理逻辑

```js
//点击：发送短信验证码
$('#send').click(function (){
        
        var formData = document.getElementById("formData");
        if (formData.telephone.value=="") {
          document.getElementById("div11").style.display = 'block';
          formData.telephone.focus();
          return ;
        }

        var telephone = formData.telephone.value;

        var parameter = {
          telephone:telephone,
          mode:'login'
        };  

        //开始倒计时
        setTime($('#send'));
        //传递手机号给后台发短信的方法，关于php实现发送短信，另做记录
        Apis.sendmessage(parameter)
          .then(function(response){
              if(response.data == 2){
                alert('发送过于频繁，请稍后再试');
                return;
              }else if(response.data == 'noexist'){//检测手机用户是否存在，不存在就别发了 能省就省
                alert('用户不存在，请先注册');
                // location.href = '/login/register';
                return;
              }else if(response.data){
                alert('短信验证码已发送');
                return;
              }else{
                alert('短信验证码发送失败');
                return;
              }
          });

      });

  var countdown = 60;
  function setTime(obj) {
    if (countdown == 0) {
        obj.prop('disabled', false);
        obj.text("获取验证码");
        countdown = 60;//60秒过后button上的文字初始化,计时器初始化;
        return;
    } else {
        obj.prop('disabled', true);
        obj.text("("+countdown+"s)后重新发送") ;
        countdown--;
    }
    setTimeout(function() { setTime(obj) },1000) //每1000毫秒执行一次
  }
```


以上就是js端对于短信验证码倒计时的处理逻辑
