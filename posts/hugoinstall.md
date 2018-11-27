+++
title = "hugo安装及部署"
date = 2018-08-06T19:27:54+08:00
tags = ["golang"]
categories = ["golang"]
+++
# 前言
Hugo是一个用Go编写的快速而现代的静态站点生成器，旨在使网站创建再次变得有趣。  
Hugo的优势就在于其生成的站点是静态的，因此网站的访问速度是非常快的，其次使用Hugo，可以让你专注于写文章，至于主题和seo Hugo已经很好的解决了这些问题。下面就来讲一下如何用Hugo生成一个站点并用github page部署
<!--more-->
# 安装Hugo
Hugo的安装方式  
- [源码安装](https://gohugo.io/getting-started/installing/#source "Hugo下载地址")
- [二进制安装](https://github.com/gohugoio/hugo/releases "Hugo下载地址")  

本篇文章旨在让小白也能用Hugo搭建出来一个站点，所以选择最简单的方式（二进制）安装，下载地址：
[Hugo 二进制下载](https://github.com/gohugoio/hugo/releases "Hugo下载地址")
选择你对应系统的文件下载即可。  
将下载文件放在系统的PATH目录中，这样你可以在系统的任意位置实用Hugo命令。

`在 OS X，如果你有 Homebrew ，安装过程就更简单了：只需要运行 brew install hugo 。`

如果没有问题的话在命令行输入`hugo version`就可以显示出hugo的版本信息。

# 生成站点
使用Hugo快速生成站点，比如希望生成到 /path/to/site 路径：
`hugo new site /path/to/site`
这样就在 /path/to/site 目录里生成了初始站点，进去目录：`cd /path/to/site`
```
  ▸ archetypes/
  ▸ content/ 
  ▸ layouts/
  ▸ resources/
  ▸ static/
  ▸ themes/
    config.toml
```
# 创建文章
创建一个 about 页面：`$ hugo new about.md`
about.md 自动生成到了 content/about.md ，打开 about.md 看下：
```
+++
date = "2015-10-25T08:36:54-07:00"
draft = true
title = "about"

+++

正文内容
```
内容是 Markdown 格式的，+++ 之间的内容是 TOML 格式的，根据你的喜好，你可以换成 YAML 格式（使用 --- 标记）或者 JSON 格式。  
创建第一篇文章，放到 post 目录，方便之后生成聚合页面。
`$ hugo new post/first.md`
打开编辑 post/first.md ：
```
---
date: "2015-10-25T08:36:54-07:00"
title: "first"
 
---

### Hello Hugo

 1. aaa
 1. bbb
 1. ccc
```
# 安装皮肤
到 [皮肤列表](https://themes.gohugo.io/ "皮肤列表")   挑选一个心仪的皮肤，比如你觉得 Hyde 皮肤不错，找到相关的 GitHub 地址，进入themesmulu，在 themes 目录里把皮肤 git clone 下来：
```
# 进入 themes 目录
$ cd themes
$ git clone https://github.com/spf13/hyde.git
```
# 运行Hugo
在你的站点根目录执行 Hugo 命令进行调试：
```
$ hugo server --theme=hyde --buildDrafts、
```
（注明：v0.15 版本之后，不再需要使用 --watch 参数了）

浏览器里打开： http://localhost:1313
# 部署
假设你需要部署在 GitHub Pages 上，首先在GitHub上创建一个Repository，命名为：login_name.github.io （login_name替换为你的github登录用户名）。

在站点根目录执行 Hugo 命令生成最终页面：
```
$ hugo --theme=hyde --baseUrl="http://login_name.github.io/"
```
（注意，以上命令并不会生成草稿页面，如果未生成任何文章，请去掉文章头部的 draft=true 再重新生成。）

如果一切顺利，所有静态页面都会生成到 public 目录，将pubilc目录里所有文件 push 到刚创建的Repository的 master 分支。

```
$ cd public
$ git init
$ git remote add origin https://github.com/coderzh/login_name.github.io.git
$ git add -A
$ git commit -m "first commit"
$ git push -u origin master
```

浏览器里访问：`http://login_name.github.io/`

# 绑定域名
## 解析域名
### 解析www
在域名服务商后台添加一条CNAME的解析记录：
```
记录类型：CNAME
主机记录：www
记录值：login_name.github.io
```
### 解析顶级域名
使用DNS提供程序配置记录
```
185.199.108.153
185.199.109.153
185.199.110.153
185.199.111.153
```
可以手动ping上面地址，找一个延迟最低的配置域名解析
```
记录类型：A
主机记录：@
记录值：185.199.109.153
```

`警告：除非您的DNS提供商支持展CNAME平，否则请勿CNAME为自定义顶点域创建记录。这样做可能会导致该域上的其他服务（例如电子邮件）出现问题。`
# 配置github pages的custom domain
进入你的github pages的仓库，然后在设置里面将的你的域名的地址，添加到custom domain中，然后保存即可。如下图所示：
![设置custom domain](http://o8ei86anm.bkt.clouddn.com/github.jpg '设置custom domain')


