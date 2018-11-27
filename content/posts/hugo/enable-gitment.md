+++
title = "hugo集成girment插件"
date = 2018-11-27T18:57:45+08:00
tags = ["hugo"]
categories = ["hugo"]
gitmentId = "hugo-enable-gitment"
+++
# 起因
使 `hugo` 搭建博客已经有半年了，一直觉得用 `markdown` 才写博客才程序员应该有的范，所以看到 `hugo` 就觉得自己应该有一个这样的博客，后面就自己搭建起来。最近，写博客的频率越来越高，也越来越觉得交互对一个博客的重要性，于是集成评论系统的优先级变得越来越高。  
最开始也想着直接集成现有的国内评论插件，后台喜欢的评论插件死的死，不死的也半死，于是把目光放在了 `gitment` 上，对于 `gitment` 的集成相对简单，其中也有一些小坑，于是便有了这篇文章,希望能够帮到有同样想法的小伙伴。
# 1. 简介
Gitment 是一款基于 GitHub Issues 的评论系统，支持在前端直接引入，不需要任何后端代码。可以在页面进行登录、查看、评论、点赞等操作，同时有完整的 Markdown / GFM 和代码高亮支持。尤为适合各种基于 GitHub Pages 的静态博客或项目页面Gitment是一款基于 GitHub Issues 的评论系统，支持在前端直接引入，不需要任何后端代码。可以在页面进行登录、查看、评论、点赞等操作，同时有完整的 Markdown / GFM 和代码高亮支持。尤为适合各种基于 GitHub Pages 的静态博客或项目页面。

# 2. 注册
到https://github.com/settings/applications/new ,中注册一个新的`OAuth Application` 其中 `Authorization callback URL` 写你的网站域名就可以了，例如我这里填写的是：http://www.artacode.com/
注册完成后会得到 `Client ID` 和 `Client Secret` 两个值用于为们后面引入 gitment
# 3. 引入
增加评论开关：
```
sudo vim $HUGO_PATH/config.toml

...
[params]
	enableGitment = true
...

//保存退出
```
在 `themes/beautifulhugo/layouts/_default/single.html` 文件中，合适的位置增加如下代码
```
{{ if .Site.Params.enableGitment }}
<div id="git-comments"></div>
<link rel="stylesheet" href="https://imsun.github.io/gitment/style/default.css">
<script src="https://imsun.github.io/gitment/dist/gitment.browser.js"></script>
<script>
  var gitment = new Gitment({
    id: '{{ .Params.gitmentId }}',  // 可选, 默认为 location.href
    owner: '你的 GitHub ID',
    repo: '存储评论的仓库名称',
    oauth: {
      client_id: '你的 client ID',
      client_secret: '你的 client secret',
    }
  })
  gitment.render('git-comments')
</script>
{{ end }}

```
值得注意的`repo` 必须是`owner` 下的仓库名称，还有一点就是 id 的长度是有限制的，所以为们需要自己定义一个唯一的id，本站是使用了页面变量 gitmentId 来作为评论的 id 如下是本文的 meta 
```
+++
title = "hugo集成girment插件"
date = 2018-11-27T18:57:45+08:00
tags = ["hugo"]
categories = ["hugo"]
gitmentId = "hugo-enable-gitment"
+++
```

# 4. 总结
gitment 是一款非常优秀的评论插件，加上本神也支持 `markdown` 语法， 对于程序员来说可以说是量身打造。还等什么，来试以下把。

# 5. 参考链接
[gitment](https://github.com/imsun/gitment)
