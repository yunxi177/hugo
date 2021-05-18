+++
title = "virtualbox 分享文件夹，无权访问问题"
date = 2019-02-24T14:26:03+08:00
tags = ["linux"]
categories = ["linux"]
draft = false
commentId="linux-virtualbox-permession"
+++
# 背景
在 windows 下使用 virtualbox 安装  linux 系统 实现 windows 下开发虚拟机里跑测试的效果
# 起因 
由于项目使用文件目录是可以在 windows 和 linux 下共享的，因此使用 virtualbox 共享文件夹功能来实现项目文件夹在两个系统间共享，配置好共享文件后，发现在虚拟机的 linux 系统中没有权限操作目录.

百度之后发现原来是因为共享文件夹挂在后的文件是属于 `vboxsf` 组下的，而登陆用户不属于该组，因而无权对目录进行操作。

# 解决方法
既然知道原因，解决办法也是非常简单的就是将当前登录的用户加入 `vboxsf` 用户组即可：  
```$ sudo usermod -aG vboxsf $(whoami)```  
需要注意的是该操作需要重启系统才能生效。


