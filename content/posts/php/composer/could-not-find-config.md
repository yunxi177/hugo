+++
title = "Composer Could Not Find Config"
date = 2019-04-05T20:36:29+08:00
tags = ["php"]
categories = ["php"]
draft = true
commentId="php-composer-could-not-find-config"
+++

在执行 `composer require xxx` 发现始终无法执行成功  
执行 `composer require -vvv xxx` 打印详细信息
发现报错信息如下:
```
failed to initialize global composer composer could not find the config file
```
最后发现在系统里有两个 `composer` 配置，删除掉非安装目录下的环境变量（我这边是`C:\Users\Administrator\AppData\Roaming\Composer\vendor\bin`  
注销电脑，重新执行 `composer require -vvv xxx` 安装成功
