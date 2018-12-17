+++
title = "公众号通过api发送小程序消息"
date = 2018-12-17T18:34:50+08:00
tags = ["微信开发"]
categories = ["微信开发"]
keywords = ["微信公众号API推送小程序", "公众号API发送小程序卡片", "公众号API发送小程序链接"]
draft = false
commentId="push-mini-program"
+++

# 接口调用请求说明
```
http请求方式: POST
https://api.weixin.qq.com/cgi-bin/message/custom/send?access_token=ACCESS_TOKEN
```
## 发送普通消息的小程序链接
```
{
    "touser":"OPENID",
    "msgtype":"text",
    "text":
    {
         "content":"文本内容<a href="http://www.qq.com" data-miniprogram-appid="appId" data-miniprogram-path="pages/index/index">点击跳小程序</a"
    }
}
```
说明：
1. data-miniprogram-appid 项，填写小程序appid，则表示该链接跳小程序；
2. data-miniprogram-path项，填写小程序路径，路径与app.json中保持一致，可带参数；
3. 对于不支持data-miniprogram-appid 项的客户端版本，如果有herf项，则仍然保持跳href中的网页链接；
4. data-miniprogram-appid对应的小程序必须与公众号有绑定关系

### 返回结果
`{"errcode":0,"errmsg":"ok"}`

### 发送效果
![公众号推送微信小程序效果图](http://pic.artacode.com/msg.png)

## 公众号推送小程序卡片
```
{
    "touser":"OPENID",
    "msgtype":"miniprogrampage",
    "miniprogrampage":
    {
        "title":"title",
        "appid":"appid",
        "pagepath":"pagepath",
        "thumb_media_id":"thumb_media_id"
    }
}
```

### 返回结果
`{"errcode":0,"errmsg":"ok"}`


### 发送效果
![公众号推送微信小程序卡片效果图](http://pic.artacode.com/card-me.png)

# 参考链接
[公众号文档](https://mp.weixin.qq.com/wiki?t=resource/res_main&id=mp1444738730)

