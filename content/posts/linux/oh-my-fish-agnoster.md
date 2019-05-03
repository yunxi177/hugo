+++
title = "Ubuntu 终端zsh的agnoster主题乱码"
date = 2019-05-04T07:33:28+08:00
tags = ["linux"]
categories = ["linux"]
draft = true
commentId="linux-oh-my-fish-agnoster"
+++

执行以下命令来安装缺失的字体：  
`wget https://raw.githubusercontent.com/powerline/powerline/develop/font/10-powerline-symbols.conf`  
`wget https://raw.githubusercontent.com/powerline/powerline/develop/font/PowerlineSymbols.otf`  

`sudo mkdir /usr/share/fonts/OTF`  

`sudo cp 10-powerline-symbols.conf /usr/share/fonts/OTF/ `  

`sudo mv 10-powerline-symbols.conf /etc/fonts/conf.d/`  
`sudo mv PowerlineSymbols.otf /usr/share/fonts/OTF/`  

然后重启终端,恢复正常