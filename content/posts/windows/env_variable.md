+++
title = "windows 命令行设置环境变量"
date = 2019-07-24T15:36:24+08:00
tags = ["windows"]
categories = ["windows"]
draft = true
commentId=""
+++
**powershell**

```
#Powershell设置环境变量

#查看所有环境变量  
ls env:

#搜索环境变量   
ls env:NODE*

#查看单个环境变量 
$env:NODE_ENV

#添加/更新环境变量 
$env:NODE_ENV=development

#删除环境变量        
del evn:NODE_ENV
```

**cmd**

```
#cmd设置环境变量

#查看所有环境变量     
set

#查看单个环境变量     
set NODE_ENV

#添加/更新环境变量     
set NODE_ENV=development

#删除环境变量         
set NODE_ENV=
```