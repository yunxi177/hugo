+++
title = "nginx 单站点多证书配置"
date = 2021-05-18T16:03:21+08:00
tags = ["nginx"]
categories = ["nginx"]
draft = false
commentId="nginx-multi-cert"

+++

# 问题

在同一 server 中同时设置了， a.com, b.com.c.com 三个域名，若三个域名都需要 https 访问，应当如何配置呢？

示例配置如下：

```nginx
server {

  listen       443 ssl;

  server_name     a.com b.com c.com;



  ssl_certificate   a.crt;

  ssl_certificate_key a.key;

  ...

}
```

问题分析：如果在 `ssl_certificate` 与 `ssl_certificate_key` 的配置项中可以增加变量，此问题就可以迎刃而解。



# 变量 ssl_server_name

经过 goole 发现 nginx 在 1.15.10 及以后的版本支持 `ssl_server_name` 变量，此变量输出的便是所访问 URL 的 host 部分(不包含端口及后面的 path 部分).



| URL                   | ssl_server_name |
| --------------------- | --------------- |
| https://a.com/test    | a.com           |
| https://b.com:83/test | b.com           |
| https://c.com/test    | c.com           |



有了 ssl_server_name 就可以很好的解决我们在文章开头描述的问题参考配置如下:

``` ng
server {

  listen       443 ssl;

  server_name     a.com b.com c.com;



  ssl_certificate   /crtPath/$ssl_server_name.crt;

  ssl_certificate_key /keyPath/$ssl_server_name.key;

  ...

}
```



配置完成重启服务后，就可以用 https 访问对应的 `server_name` 了





# Summary



1. `ssl_server_name` 变量只有在 nginx 1.15.10 及以上的版本才支持
2. 官网对 `ssl_server_name` ** **说明会对性能有一定的损耗，如果对高并发比较在意的项目，需要测试能否满足项目要求







# References

[Nginx ssl_certificate](http://nginx.org/en/docs/http/ngx_http_ssl_module.html#ssl_certificate)

[URL WIKI](https://en.wikipedia.org/wiki/URL)