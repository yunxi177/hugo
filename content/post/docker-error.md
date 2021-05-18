+++
title = "docker常见问题"
date = 2018-09-09T15:54:18+08:00
tags = ["docker"]
categories = ["docker"]
commentId="docker-err"
+++
# docker pull 失败
## 错误信息
`Get https://registry-1.docker.io/v2/: dial tcp: lookup registry-1.docker.io: no such host`
## 解决方法
将dns修改成`8.8.8.8`就可以解决此问题
