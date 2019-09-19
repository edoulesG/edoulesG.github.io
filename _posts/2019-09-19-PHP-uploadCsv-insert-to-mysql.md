---
layout: post
title: "php cl 环境下上传csv文件并写入数据库"
author: "Martube"
categories: technology
tags: [technology,php]
image: mountains.jpg
---

### 前言

前端如何上传csv文件我就不多做介绍了，这里主要说一下我这个案例后台依赖的环境以及实际操作

### 环境

php codeigniter框架

[这里是官网](https://codeigniter.org.cn/)


### 代码

一段一段放吧。下面的代码都在一个方法里。

1. 首先是文件接收。

用php原生的方法`$_FILES`接收,说到这里我想提一句php这个cl框架使用也有一段时间了，感觉上来说常用的功能都有封装，但是有时候又不如php原生的方法用起来简明顺手。

php果然不适合过度封装。

```php
        //获取上传过来的文件
        $file=$_FILES['file'];

        $max_size="2000000"; //最大文件限制（单位：byte）
        $fname=$file['name'];
        //提取文件后缀，判断文件类型
        $ftype=strtolower(substr(strrchr($fname,'.'),1));
        //文件格式
        $uploadfile=$file['tmp_name'];//获取缓存文件

        //仅接收POST
        if($_SERVER['REQUEST_METHOD']=='POST'){
         if(is_uploaded_file($uploadfile)){
                //文件过大
            if($file['size']>$max_size){
                $this->response(false, 200, 'Import file is too large!');
                return;
            }
                //文件名不对
            if($ftype!='csv'){
                $this->response(false, 200, 'Import file type is error!');
                return;
            }
        }else{
            $this->response(false, 200, 'The file is not empty!');
            return;
        }
    }

```


2. 接收之后对文件内容的处理，注意几个关键方法 `fopen`、 `fgetcsv`

获取每一行的数据,将每一个字段对应的数据插入数据库

这些没什么好说的，只有一点，csv文件如果使用excel打开，如果内容包含中文，但编码不是GBK的话，就会导致乱码，所以 **需要对每一条可能包含中文的数据进行UTF8转GBK的转码**

```php
    //load数据库
    $this->load->database();

    $row=0;//记录行数
    $filename=$file['tmp_name'];

    //使用 fopen打开文件
    $handle=fopen($filename,'r');//打开文件

    //使用 fgetcsv 获取每一行的数据
    while(!feof($handle) && $data=fgetcsv($handle,1000,',')){
      $arr_result=array();
      if($row==0){//跳过第一行
         $row++;
         continue;
     }
     if($row>0 && !empty($data)){
        $num=count($data);
        for($i=0;$i<$num;$i++){
            array_push($arr_result,$data[$i]);
        }

        $sql = "INSERT INTO `company_pre_registration`
        (ordinal,account,companyName,address,legalRepresentative,representativeTel,contactMan,contactPhone,email)
        VALUES (?,?,?,?,?,?,?,?,?)";

        //csv 内容的数据顺序都是一开始规定好的0-8

        //GBK转码问题 
        $ordinal = iconv('gb2312','utf-8',$arr_result[0]);
        $account = iconv('gb2312','utf-8',$arr_result[1]);
        $companyName = iconv('gb2312','utf-8',$arr_result[2]);
        $address = iconv('gb2312','utf-8',$arr_result[3]);
        $legalRepresentative = iconv('gb2312','utf-8',$arr_result[4]);
        $representativeTel = iconv('gb2312','utf-8',$arr_result[5]);
        $contactMan = iconv('gb2312','utf-8',$arr_result[6]);
        $contactPhone = iconv('gb2312','utf-8',$arr_result[7]);
        $email = iconv('gb2312','utf-8',$arr_result[8]);

        $result = $this->db->query($sql, array($ordinal,$account,$companyName,$address,$legalRepresentative,$representativeTel,$contactMan,$contactPhone,$email));

        // $result = $this->db->query($sql, array($arr_result[0],$arr_result[1],$arr_result[2],$arr_result[3],$arr_result[4],$arr_result[5],$arr_result[6],$arr_result[7],$arr_result[8]));
      //$name = iconv('gb2312','utf-8',$arr_result[1]);
      //$sex = iconv('gb2312','utf-8',$arr_result[2]);
      //echo $sql;

        if(!$result){
            $this->response(false, 200, 'insert error!');
            return;
        }
    }
    $row++;
    }
    fclose($handle);

    $this->response(true, 200, null);
    return;

```

以上。
