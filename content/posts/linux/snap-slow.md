+++
title = "snapcraft 下载软件慢，解决办法"
date = 2020-05-01T08:39:20+08:00
tags = ["linux"]
categories = ["linux"]
draft = false
commentId="linux-snap-slow"
+++

由于国内网络原因，snap 安装软件会非常的慢，而且会有中断的情况，可以通过给 snap 设置代理的设置代理的方式解决此问题，具体操作如下：

`$ sudo systemctl edit snapd` 编辑 snapd 文件， 加入如下代码：
```
[Service]
Environment="http_proxy=http://127.0.0.1:port"
Environment="https_proxy=http://127.0.0.1:port"
```
`ctrl + o` 写入文件， 回车确认
`ctrl + x` 退出编辑

重启服务
`$ sudo systemctl daemon-reload`
`$ sudo systemctl restart snapd`

再次执行安装操作，会发现速度已经有了大幅度提升。