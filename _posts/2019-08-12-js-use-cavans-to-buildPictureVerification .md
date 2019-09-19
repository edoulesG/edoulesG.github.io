---
layout: post
title: "Js 使用cavans生成简单的图片验证码"
author: "Martube"
categories: technology
tags: [technology,js]
image: home-bg-art.jpg
---
#### 基于cavans 的纯前端验证码

参考文档：
[JS 验证码功能的三种实现方式](https://www.jb51.net/article/151432.htm)


## 实现效果
![基于cavans 的纯前端验证码](/assets/img/technology/20190812ep1.png)


## 环境

js + jQuery (纯js)

## 代码

CSS: 设置画布

```css
    .input-val {
      width: 290px;
      height: 50px;
      border: 1px solid #cccccc;
      box-sizing: border-box;
      }
      #canvas {
      margin-left: 5px;
      width: 124px;
      height: 50px;
      vertical-align: middle;
      box-sizing: border-box;
      border: 1px solid #ff9900;
      cursor: pointer;
      }
```

html:

```html
          <p class="login_p2" style="width: 500px;">
              <input type="text" value="" placeholder="图片验证码（不区分大小写）" class="input-val">
              <canvas id="canvas" width="100" height="30"></canvas>
          </p>
```

js:逻辑部分，判断输入的验证码是否和图片中的一样

```js
      //图形验证码
      var show_num = [];
      draw(show_num);
      $("#canvas").on('click',function(){
          draw(show_num);
      });

        //在这加一个点击事件 来判断图形验证码正确与否
          //判断图形验证码是否正确
          var val = $(".input-val").val().toLowerCase();
         var num = show_num.join("");
         if(val==''){
          alert('请输入验证码！');
          return;
         }else if(val == num){
          //do nothing continue
          //$(".input-val").val('');
          // draw(show_num);
         }else{
          alert('验证码错误！请重新输入！');
          $(".input-val").val('');
          return;
          // draw(show_num);
         }
```


js:画布主要部分,生成验证码图片

```js
//生成并渲染出验证码图形
  function draw(show_num) {
      var canvas_width=$('#canvas').width();
      var canvas_height=$('#canvas').height();
      var canvas = document.getElementById("canvas");//获取到canvas的对象，演员
      var context = canvas.getContext("2d");//获取到canvas画图的环境，演员表演的舞台
      canvas.width = canvas_width;
      canvas.height = canvas_height;
      var sCode = "a,b,c,d,e,f,g,h,i,j,k,m,n,p,q,r,s,t,u,v,w,x,y,z,A,B,C,E,F,G,H,J,K,L,M,N,P,Q,R,S,T,W,X,Y,Z,1,2,3,4,5,6,7,8,9,0";
      var aCode = sCode.split(",");
      var aLength = aCode.length;//获取到数组的长度
      for (var i = 0; i < 4; i++) { //这里的for循环可以控制验证码位数（如果想显示6位数，4改成6即可）
       var j = Math.floor(Math.random() * aLength);//获取到随机的索引值
       // var deg = Math.random() * 30 * Math.PI / 180;//产生0~30之间的随机弧度
       var deg = Math.random() - 0.5; //产生一个随机弧度
       var txt = aCode[j];//得到随机的一个内容
       show_num[i] = txt.toLowerCase();
       var x = 10 + i * 20;//文字在canvas上的x坐标
       var y = 20 + Math.random() * 8;//文字在canvas上的y坐标
       context.font = "bold 23px 微软雅黑";
       context.translate(x, y);
       context.rotate(deg);
       context.fillStyle = randomColor();
       context.fillText(txt, 0, 0);
       context.rotate(-deg);
       context.translate(-x, -y);
      }
      for (var i = 0; i <= 5; i++) { //验证码上显示线条
       context.strokeStyle = randomColor();
       context.beginPath();
       context.moveTo(Math.random() * canvas_width, Math.random() * canvas_height);
       context.lineTo(Math.random() * canvas_width, Math.random() * canvas_height);
       context.stroke();
      }
      for (var i = 0; i <= 30; i++) { //验证码上显示小点
       context.strokeStyle = randomColor();
       context.beginPath();
       var x = Math.random() * canvas_width;
       var y = Math.random() * canvas_height;
       context.moveTo(x, y);
       context.lineTo(x + 1, y + 1);
       context.stroke();
      }
   }
   //得到随机的颜色值
   function randomColor() {
    var r = Math.floor(Math.random() * 256);
    var g = Math.floor(Math.random() * 256);
    var b = Math.floor(Math.random() * 256);
    return "rgb(" + r + "," + g + "," + b + ")";
   }
```


