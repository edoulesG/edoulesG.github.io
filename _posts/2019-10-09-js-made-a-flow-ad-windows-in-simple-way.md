---
layout: post
title: "js 广告漂浮窗口"
author: "Martube"
categories: technology
tags: [technology,js,css,html]
image: cutting.jpg
---

# 无插件 js 实现自定义广告漂浮窗口


### html 代码自定义样式

```html

    <style>
        .tx-piaofuchuang{
            position: fixed;
            top: 0px;
            left: 0px;
            width: 400px;
            /*height: 160px;*/
            background: black;
            z-index: 999999;
        }
    </style>
    <div class="tx-piaofuchuang">
        <a href="/page/article?articleId=51&type=newsnotice" target="_blank">
            <img src="/static/images/ad.png">
        </a>
    </div>


```


### 样式效果图


！[效果图](/assets/img/technology/20191009ep2.png)


### js代码

```js

//广告漂浮窗口
        function FloatAd(selector) {
            var obj = $(selector);
            // if (obj.find(".item").length == 0) return;//如果没有内容，不执行
            var windowHeight = $(window).height();//浏览器高度
            var windowWidth = $(window).width();//浏览器宽度
            var dirX = -1.5;//每次水平漂浮方向及距离(单位：px)，正数向右，负数向左，如果越大的话就会看起来越不流畅，但在某些需求下你可能会需要这种效果
            var dirY = -1;//每次垂直漂浮方向及距离(单位：px)，正数向下，负数向上，如果越大的话就会看起来越不流畅，但在某些需求下你可能会需要这种效果

            var delay = 30;//定期执行的时间间隔，单位毫秒
            obj.css({ left: windowWidth / 2 - obj.width() / 2 + "px", top: windowHeight / 2 - obj.height() / 2 + "px" });//把元素设置成在页面中间
            obj.show();//元素默认是隐藏的，避免上一句代码改变位置视觉突兀，改变位置后再显示出来
            var handler = setInterval(move, delay);//定期执行，返回一个值，这个值可以用来取消定期执行

            obj.hover(function() {//鼠标经过时暂停，离开时继续
                clearInterval(handler);//取消定期执行
            }, function() {
                handler = setInterval(move, delay);
            });

            obj.find(".close").click(function() {//绑定关闭按钮事件
                close();
            });
            $(window).resize(function() {//当改变窗口大小时，重新获取浏览器大小，以保证不会过界（飘出浏览器可视范围）或漂的范围小于新的大小
                windowHeight = $(window).height();//浏览器高度
                windowWidth = $(window).width();//浏览器宽度
            });
            function move() {//定期执行的函数，使元素移动
                var currentPos = obj.position();//获取当前位置，这是JQuery的函数，具体见：http://hemin.cn/jq/position.html
                var nextPosX = currentPos.left + dirX;//下一个水平位置
                var nextPosY = currentPos.top + dirY;//下一个垂直位置

                // if (nextPosX >= windowWidth - obj.width()) {//这一段是本站特有的需求，当漂浮到右边时关闭漂浮窗口，如不需要可删除
                //  close();
                // }

                if (nextPosX <= 0 || nextPosX >= windowWidth - obj.width()) {//如果达到左边，或者达到右边，则改变为相反方向
                    dirX = dirX * -1;//改变方向
                    nextPosX = currentPos.left + dirX;//为了不过界，重新获取下一个位置
                }
                if (nextPosY <= 0 || nextPosY >= windowHeight - obj.height() - 5) {//如果达到上边，或者达到下边，则改变为相反方向。
                    dirY = dirY * -1;//改变方向
                    nextPosY = currentPos.top + dirY;//为了不过界，重新获取下一个位置
                }
                obj.css({ left: nextPosX + "px", top: nextPosY + "px" });//移动到下一个位置
            }

            // function close() {//停止漂浮，并销毁漂浮窗口
            //  clearInterval(handler);
            //  obj.remove();
            // }
        }

        //自执行函数
        $(function() {
            FloatAd(".tx-piaofuchuang");
        });
```

以上.