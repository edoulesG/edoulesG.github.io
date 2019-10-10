---
layout: post
title: "BootStrap下使用x-editable 进行美观的内容编辑"
author: "Martube"
categories: technology
tags: [technology,js,bootstrap]
image: home-bg-art.jpg
---

## BootStrap下使用x-editable 进行美观的内容编辑

> x-editable官网地址http://vitalets.github.io/x-editable/
> 这是一个input框的插件，效果图可以参照官网，适用于表格或者简单的input内容编辑,一个input对应一个提交按钮。
> 支持Bootstrap2or3、jQuery UI1.9+以及jQuery (+ poshytip plugin)。

### 个人案例

这个例子是我在做一个网站的个人中心时使用的,显示一大段信息,只有一个邮箱修改：

![图示1](/assets/img/technology/20190920ep1.png)


这种情况就很适合使用x-editable 来进行编辑，直接点击之后修改即可。

![图示2](/assets/img/technology/20190920ep2.png)



### 实现方法

1. 我使用的前端框架是bootstrap,那么首先需要在x-editable官网下载bootstrap的library，进行引用：

(对于我来说在习惯在每个第三方插件引用上加上适当的注释)

css：
```html
<!-- x-editable for bootstrap css-->
<link rel="stylesheet" href="/x-editable/bootstrap-editable.css">
```
js:
```html
<!-- x-editable for bootstrap js-->
<script src="/x-editable/bootstrap-editable.min.js"></script>
```

2. 然后是input的设置

```html
<form id="editable-form" class="editable-form"></form>
    <a href="#" id="email" data-type="text" data-pk="1" class="editable editable-click" style="background-color: rgba(0, 0, 0, 0);">1111@111.com</a>
</form>
```

3. *在Js里进行动作的设置和初始化：

发送请求默认是post，可以去官网查看文档，然后对参数进行设置

```js

if($('#editable-form').length) {
    $.fn.editable.defaults.mode = 'inline';
    //这里设置button样式
    $.fn.editableform.buttons =
          '<button type="submit" class="btn btn-primary btn-sm editable-submit">' +
          '<i class="fa fa-fw fa-check"></i>' +
          '</button>' +
          '<button type="button" class="btn btn-default btn-sm editable-cancel">' +
          '<i class="fa fa-fw fa-times"></i>' +
          '</button>';
          
          //针对 input id="email" 进行初始化和action设定
          $('#email').editable({
              type: 'text',
              name: 'email',
              url: '/admin/user/changeEamil',
              title: 'Enter email',
          });
}

```

4.最后是后台的接收(后台我使用了php的 cl框架)，注意这里接收的key 是 value

```php
public function changeEamil(){
        //拿到输入框里的最终值
        $email = $this->input->get_post('value', TRUE);

        //cl框架下获取session
        $userData = $this->session->userdata('userdata');
        $par = array(
            'username' => $userData['U_NAME'],
            'email' => $email
        );

        //把参数传进写好的方法里头改数据
        $this->load->model ( 'UserModel' );
        $result = $this->UserModel->changeEamil($par);

        if ($result) {
            $this->response($result, 200, 'success');
        }else{
            $this->response(false, 200, 'false');
        }
    }
```

以上。

