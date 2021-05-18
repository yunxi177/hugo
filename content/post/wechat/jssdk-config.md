+++
title = "微信公众号 jssdk 报错"
date = 2019-05-10T21:02:48+08:00
tags = ["微信开发"]
categories = ["微信开发"]
draft = false
commentId="wechat-jssdk-config"

+++

引入 wx-js-sdk

```
import wx from 'weixin-js-sdk';
```

设置微信 config 部分代码：

```
 this.$axios.get("后台获取 config 地址", {}).then((response) => {
 ...
     wx.config(configData);
     wx.ready(() => {
     	this.getLocation();
 	})
 ...
  }) 
```

前端 console 中报错如下 `getLocation:fail, the permission value is offline verifying`

经过一番排查锁定为签名问题查询微信官方文档签名规则如下：  

> 签名生成规则如下：参与签名的字段包括noncestr（随机字符串）, 有效的jsapi_ticket, timestamp（时间戳）, url（当前网页的URL，不包含#及其后面部分） 。对所有待签名参数按照字段名的ASCII 码从小到大排序（字典序）后，使用URL键值对的格式（即key1=value1&key2=value2…）拼接成字符串string1。这里需要注意的是所有参数名均为小写字符。对string1作sha1加密，字段名和字段值都采用原始值，不进行URL 转义。
>
> 即signature=sha1(string1)。 示例：
>
> ```
> noncestr=Wm3WZYTPz0wzccnW
> jsapi_ticket=sM4AOVdWfPE4DxkXGEs8VMCPGGVi4C3VM0P37wVUCFvkVAy_90u5h9nbSlYy3-Sl-HhTdfl2fzFy1AOcHKP7qg
> timestamp=1414587457
> url=http://mp.weixin.qq.com?params=value
> ```



由于后台项目使用 `easywechat` SDK， `easywechat` 默认会使用 `$_SERVER['HTTP_HOST']` 和 `$_SERVER['REQUEST_URI']` 作为 url 签名参数， 这里也就是将接口地址作为签名的 url ， 而我们项目是前后端分离，实际调用者确是前端项目所部署的 url 因而导致签名不正确。  

知晓问题解决就很好办了，后台签名的时候接受 前端当前页面的 url  然后通过 `$app->jssdk->setUrl($url);` 来设置 签名的 url，这样就解决了此问题。