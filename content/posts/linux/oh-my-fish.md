+++
title = "Oh My Fish 一个可以让你赏心悦目的 fish shell 框架"
date = 2019-05-03T22:37:27+08:00
tags = ["linux"]
categories = ["linux"]
draft = false
commentId="linux-oh-my-fish-install"
+++


# 安装 fish shell
以 ubuntu 为例
```
sudo apt-add-repository ppa:fish-shell/release-3
sudo apt-get update
sudo apt-get install fish
```

其他系统安装[点击链接](https://github.com/fish-shell/fish-shell)

# 安装 oh my fish

```
curl -L https://get.oh-my.fish | fish
```
运行上述命令，就尅安装 oh my fish 了，接下来来讲解一下 oh my fish 的基本使用方式

# 操作说明

`omf update [omf] [<package>...]`  
更新 oh my fish 包仓库以及所有安装过的包
- 执行该命令未加任何参数的时候则更新自身和所有安装过的包
- 仅仅更新 oh my fish `omf update omf.`
- 对于选择更新，你仍然可以在更新包的列表中加入 “omf” 来更新 oh my fish 自身  

`omf install [<name>|<url>]`  
安装一个或更多的包  

- 执行 `omf install URL` 可以直接通过 URL 安装包
- 如何没传入参数，则冲 bundle 中安装缺少的包  
  
 `omf repositories [list|add|remove]`  

管理用户安装包的仓库。包仓库是用户 `omf install` 等命令使用包的来源.默认情况下官方包总是安装后好并且可用的。  

`omf list`  
已安装包的列表  
`omf theme <theme>`'  
使用主题，如果想列出可使用主题列表使用 `omf theme` , 你也可已在安装主题前 [预览主题](https://github.com/oh-my-fish/oh-my-fish/blob/master/docs/Themes.md)

`omf remove <name>`  
删除主题或者已经安装的包  

`omf reload`  
使用 `exec` 用一个全新的 sell 进程代替当前进程，以重载 oh my fish 和 所有插件  

`omf new plugin | theme <name>`  
构建新的插件或主题  

`omf search -t|--theme / -p|--package <name>`  

可以按照 主题名或包名或包名+主题名 搜索。 支持模糊查询，如果不确定保命可以直接搜搜  `omf search simple.`'  

`omf channel`  
获取或更改更新管道  
有两个可以用的管道 
1. `stable` 管道使用最新标记版本的 oh my fish 提供稳定版更新　　
2.  `dev` 提供正在开发中的最新更改

根据设置的更新通道则决定　`omf uodate` 更新到什么版本  

`omf destroy`  
卸载　oh my fish


＃　参考链接
[oh-my-fish](https://github.com/oh-my-fish/oh-my-fish)