+++
title = "使用 let's encrypt 为网站开启 https"
date = 2019-07-30T20:54:18+08:00
tags = ["WEB开发"]
categories = ["WEB开发"]
draft = false
commentId="web-https"
+++

# 启用可用通道
```
$ yum -y install yum-utils
$ yum-config-manager --enable rhui-REGION-rhel-server-extras rhui-REGION-rhel-server-optional
```

# 安装 Certbot

```
$ sudo yum install certbot python2-certbot-nginx
```

# 获取 nginx 服务器证书
```
$ sudo certbot certonly --nginx
```
# 遇到问题
## certbot 未找到 nginx 二进制文件
The nginx plugin is not working; there may be problems with your existing configuration.
The error was: NoInstallationError("Could not find a usable 'nginx' binary. Ensure nginx exists, the binary is executable, and your PATH is set correctly.",)

由于我机器之前机器上已经安装 nginx 安装目录为 `/usr/local/nginx/sbin/nginx` 而 certbot 默认会到 `/usr/bin/nginx` 目录下找,因此需要建立一个软连接
```
$ sudo ln -s /usr/local/nginx/sbin/nginx /usr/bin/nginx
```
## 未找到 nginx 配置文件
Error while running nginx -c /etc/nginx/nginx.conf -t.

nginx: [emerg] open() "/etc/nginx/nginx.conf" failed (2: No such file or directory)
nginx: configuration file /etc/nginx/nginx.conf test failed

```
$ ln -s /usr/local/nginx/conf/ /etc/nginx
```
把 /usr/local/nginx/conf/ 换成你 nginx 配置文件目录

# 将自动更新证书写入定时任务
由于，Let’s Encrypt 的证书90天就过期了，所以，你还要设置上自动化的更新脚本，最容易的莫过于使用 crontab 了。使用下面命令,将自动更新证书加入定时任务
```
$ echo "0 0,12 * * * root python -c 'import random; import time; time.sleep(random.random() * 3600)' && certbot renew" | sudo tee -a /etc/crontab > /dev/null
```

确定定时任务是否加入成功
```
$ cat /etc/crontab
```
关于自动更新证书，如果你手动执行一遍的话会有如下提示
```
Cert not yet due for renewal （证书尚未过期`）
``` 
你可能会有疑问，如果有了这个限制等过期了再给我更新证书，服务不就会有一段时间无法访问了吗？以下是 Certbot 文档中的原话
```
This command attempts to renew any previously-obtained certificates that expire in less than 30 days.
```

其实 crontab 不会在证书完全到期后才允许我们更新证书，而是会更新 30 天内将要过期的证书，这样以来就可以放心的使用证书，而不用担心中断服务的问题了。

# 参考连接
[certbot instructions](https://certbot.eff.org/lets-encrypt/centosrhel7-nginx)
[renewing-certificates](https://certbot.eff.org/docs/using.html#renewing-certificates)