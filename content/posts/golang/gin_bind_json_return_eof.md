+++
title= "Gin_bind_json_return_eof"
date= 2019-10-23T12:03:46+08:00
draft= true
tags = ["golang"]
categories = ["golang"]
commentId="golang-Gin-bind-json-return-eof"
+++
在一次开发中,通过中间件访问请求中的post参数,于是使用 `ioutil.ReadAll` 来读取body中的内容并做相应的验证,代码如下:

```
	var pj map[string]interface{}
	body := c.Request.Body
	data, _ := ioutil.ReadAll(body)
```

一开始一切都挺美好,但等道测试的时候发现接口中调用 `BindJSON`(项目使用的框架是 gin) 返回错误,错误信息如下:
```
bind multipart: NextPart: EOF
```
经过一番排查发现是因为`ioutil.ReadAll()` 方法会将,会在你读取之后,就没有了内容,既然知道了原因,解决方法也随之出现,只需要在获取之后,从新将 body 的内容写入,代码如下:
```
c.Request.Body = ioutil.NopCloser(bytes.NewBuffer(data))
```

至此问题得到了解决

# 参考链接
[Golang: Read from an io.ReadWriter without losing its content](https://medium.com/@xoen/golang-read-from-an-io-readwriter-without-loosing-its-content-2c6911805361)



