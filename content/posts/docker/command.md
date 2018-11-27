+++
title = "docker常用命令"
date = 2018-09-15T09:06:07+08:00
tags = ["docker"]
categories = ["docker"]
gitmentId="docker-command"
+++

# docker 查看日志
`sudo docker logs -f -t --tail 20 mysql`

# docker拷贝内容到宿主机
`docker cp [OPTIONS] CONTAINER:SRC_PATH DEST_PATH`
例如：`docker cp  容器名:容器路径 宿主机路径`
