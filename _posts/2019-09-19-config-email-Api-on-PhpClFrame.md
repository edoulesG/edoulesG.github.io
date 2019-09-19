---
layout: post
title: "PHP-CL框架下配置邮件发送接口的实现及注意点"
author: "Martube"
categories: technology
tags: [technology,php]
image: cutting.jpg
---

# CL框架下邮件发送的配置

关于参数，要注意的只有三点

1. smtp_host 每个服务器下的邮箱的host地址都不一样，一定要确定，比如 **阿里云的个人和企业邮箱是不一样的**
2. smtp_port 加密端口和非加密端口是不一样的 例子里使用的是非加密端口
3. `$config['newline'] = "\r\n";` 我也不知道为啥不加这个就不行，坑了我大把时间


自己看代码吧

```php
//发送邮件
public function sendEmail(){
    //接收前端jsonPOST
    $parameter = $GLOBALS['HTTP_RAW_POST_DATA'];
    $json = json_decode($parameter);

    $par = array(
        'ordinal' => $json->ordinal,
        'account' => $json->account,
        'companyName' => $json->companyName,
        'address' => $json->address,
        'legalRepresentative' => $json->legalRepresentative,
        'representativeTel' => $json->representativeTel,
        'contactMan' => $json->contactMan,
        'contactPhone' => $json->contactPhone,
        'email' => $json->email
    );
        //根据email发送邮件

    $config['protocol']     = 'smtp';
    $config['smtp_host']    = 'smtp.mxhichina.com';
    $config['smtp_user']    = 'xxxx@xxxxx.cn';
    $config['smtp_pass']    = 'xxxxx';//密码
    $config['smtp_port']    = '25';
    $config['charset']      = 'utf-8';
    $config['mailtype']     = 'text';
    $config['smtp_timeout'] = '5';
    $config['newline'] = "\r\n";

    $this->load->library('email', $config);

        $this->email->from('minjieapp@minjie.cn', 'TESTMAN');//发件人邮箱和名字
        $this->email->to($par['email']);//收件人邮箱
        $this->email->subject('This is my subject');//主题

        $this->email->cc('another@another-example.com');//抄送人

        $this->email->message('Hi '.$par['companyName'].' Here is the info you requested.');//邮件正文

        // $this->email->send();//发送邮件

        // echo $this->email->print_debugger();
        // $this->response($this->email->print_debugger(), 200, null);
        // return;

        if($this->email->send()){  
            $this->response('success', 200, null);
        }else{
            $this->response($this->email->print_debugger(), 200, null);

        }
    }
```

以上。
