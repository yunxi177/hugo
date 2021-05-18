+++
title = "CSV 中文解析异常"
date = 2020-06-06T14:42:50+08:00
tags = ["php"]
categories = ["php"]
draft = false
commentId="php-csv-parse-error"
+++
最近在做导入 csv 数据的时候出现了，未按预期导出的情况，具体情况见下图：
![数据](http://pic.artacode.com/cseparse-error-resource.png)  

![解析](http://pic.artacode.com/cseparse-error-res.png)
如图所示，商家地址部分和商家电话部分，没有正常解析。后来在PHP官网找到如下解释：
> 该函数对区域设置是敏感的。比如说 LANG 设为 en_US.UTF-8 的话，单字节编码的文件就会出现读取错误。
我系统语言设置的 en, 当解析 utf8 字节编码的文件，就出现了上述问题。  

既然找到了是因为区域的问题，就号解决了，在导入前先设置一下区域：
```
setlocale(LC_ALL, 'us');
```
如果有疑问欢迎留言交流。

# Reference
[fgetcsv](https://www.php.net/manual/zh/function.fgetcsv.php)