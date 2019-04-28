+++
title = "docker 常见错误"
date = 2019-04-28T07:22:17+08:00
tags = ["docker"]
categories = ["docker"]
commentId="docker-faq"
+++

# /var/run/docker.sock: no such file or directory
/var/run/docker.sock 的访问权限只 root 和 docker 用户组, 所以你需要把自己的用户添加到 docker 组
命令: `$ sudo gpasswd -a ${uesrname} docker`