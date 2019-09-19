---
layout: post
title: "js + nodejs + php实现登录发送短信验证码"
author: "Martube"
categories: technology
tags: [technology,php]
image: mountains.jpg
---

### 前言

<!-- 前端主要就是传参，这里先不说了。 -->

本文主要讲一下后台的逻辑和操作。


### 环境 

1. js + jQuery
2. nodejs
3. php(CL框架)

### 后台实现


在这个方法中 不仅要实现发送短信，同时要在后台限制一定时间内短信发送的次数。

实现思路：

1. 后台接收手机号
2. 生成随机数字(一般是6位)作为验证码code
3. 在session中储存code 以及 此时的时间time time用于下次调用方法时 检查间隔时长
4. 调整参数 调用运营商接口 发送短信

```php
//发送短信验证码
    public function sendmessage()
    {   
        //接收手机号
        //接收前端jsonPOST
        $data = $GLOBALS['HTTP_RAW_POST_DATA'];
        $json = json_decode($data);
        $telephone = $json->telephone;//手机号
        $mode = $json->mode;//登录还是注册

        //生成随机6位code 
        $code = str_pad(mt_rand(10, 999999), 6, "0", STR_PAD_BOTH);
        // $code = 123456;

        //session里存 code和发送验证码的时间
        $codedata = $this->session->userdata('codedata');

        if($codedata){
            //session存在 检查时间
            if(time() - $codedata['tmptime'] < 60){
                //相差小于60秒 拒绝发送
                $this->response(2, 200, null);
                return;
            }
            $codedata['code'] = $code;
            $codedata['tmptime'] = time();

        }else{
            $codedata = array(
                'code' => $code,
                'tmptime' => time()
            );

        }
        
        $this->session->set_userdata('codedata', $codedata);

        //检查手机号是否存在
        $this->load->model ('UserModel');
        $exist = $this->UserModel->checktelexist($telephone);


        //生成cmd命令字符串
        if($mode == 'login'){
            if(!$exist){
                //不存在 先注册 不发验证码
                $this->response('noexist', 200, null);
                return; 
            }
            //登录代码 根据代码的不同 运营商发送的文本也不同
            $template = 'SMS_xxxxxxxxx';
        }else{
            //注册代码
            $template = 'SMS_xxxxxxxxx';
        }

        // php执行cmd命令的代码 
        // ..\shell\sms.js 使用相对路径 在上级目录查找 如果是同级就只需要一个点
        // 因为运营商没有直接提供php实现的接口，所以这里选择用php执行cmd命令，调用nodejs 执行 已经在运营商提供的环境中调试好的sms.js 方法
        $sendorder = 'node ..\shell\sms.js'.' phone='.$telephone.' code='.$code.' template='.$template;

        //测试数据
        // exec("node D:\project\xxxx项目名称\src\kyserver\resources\js\sms.js phone=自己的手机号 code=1234567788 template=SMS_xxxxxxxxx",$info, $val);
        // var_dump($info, $val);
        // $sendorder = 'node .\resources\js\sms.js phone=自己的手机号 code=1234567788 template=SMS_xxxxxxxxx';
        // system($sendorder,$return_status);
        // var_dump($res);
        // echo '------';
        // var_dump($return_status);

        exec($sendorder,$info, $val);

        $this->response($info, 200, null);
        return;

    }
```
## 部分sms.js 用作调试时参考

```js
if (!String.prototype.format) {
    String.prototype.format = function() {
        var args = arguments;
        return this.replace(/{(\d+)}/g, function(match, number) {
            return typeof args[number] != 'undefined' ? args[number] : match;
        });
    };
}

if (!String.prototype.startsWith) {
    (function() {
// needed to support `apply`/`call` with `undefined`/`null`
        var defineProperty = (function() {
            // IE 8 only supports `Object.defineProperty` on DOM elements
            try {
                var object = {};
                var $defineProperty = Object.defineProperty;
                var result = $defineProperty(object, object, object) && $defineProperty;
            } catch(error) {}
            return result;
        }());
        var toString = {}.toString;
        var startsWith = function(search) {
            if (this == null) {
                throw TypeError();
            }
            var string = String(this);
            if (search && toString.call(search) == '[object RegExp]') {
                throw TypeError();
            }
            var stringLength = string.length;
            var searchString = String(search);
            var searchLength = searchString.length;
            var position = arguments.length > 1 ? arguments[1] : undefined;
            // `ToInteger`
            var pos = position ? Number(position) : 0;
            if (pos != pos) { // better `isNaN`
                pos = 0;
            }
            var start = Math.min(Math.max(pos, 0), stringLength);
            // Avoid the `indexOf` call if no match is possible
            if (searchLength + start > stringLength) {
                return false;
            }
            var index = -1;
            while (++index < searchLength) {
                if (string.charCodeAt(start + index) != searchString.charCodeAt(index)) {
                    return false;
                }
            }
            return true;
        };
        if (defineProperty) {
            defineProperty(String.prototype, 'startsWith', {
                'value': startsWith,
                'configurable': true,
                'writable': true
            });
        } else {
            String.prototype.startsWith = startsWith;
        }
    }());
}

var arguments = process.argv.splice(2);
console.log(arguments);

var phone    = '';
var code     = '';
var template = '';

arguments.forEach(function(arg) {
    if (arg.startsWith('phone=')) {
        phone = arg.substring('phone='.length);
        return;
    }
    if (arg.startsWith('code=')) {
        code = arg.substring('code='.length);
        return;
    }
    if (arg.startsWith('template=')) {
        template = arg.substring('template='.length);
        return;
    }
});

if (!phone || !code || !template) {
    new Error("参数错误");
}

//调试时可把下面这部分注释掉，看是否成功进入本js中
//要使用nodejs 必须使用下面的方法安装依赖：阿里云SDK核心库
// // 安装依赖 npm install @alicloud/pop-core -S

//...省略重要参数及其他

```


## 注意点：

#### 因为运营商没有直接提供php实现的接口，所以这里选择用php执行cmd命令，调用nodejs 执行 已经在运营商提供的环境中调试好的sms.js 方法

#### sms.js中直接存入了私密信息，所以不能放在程序内，不能让外部直接通过链接访问到


## 关于php执行cmd命令：


比较常用的两个： 

system 执行外部方法 自动打印返回值 
```php
$sendorder = 'node sms.js phone=xxxxxx code=xxxxxx template=xxxxxxx';
system($sendorder,$return_status);
```

system 执行外部方法 不主动打印返回值 
```php
$sendorder = 'node ..\shell\sms.js phone=xxxxxxxxx code=xxxxxxx template=xxxxxxxxxxx';
exec($sendorder,$info, $val);
var_dump($info,$val);
```

其实就相当于在cmd里输入命令，比如 node -v ,或者xxxx.exe 