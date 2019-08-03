+++
title = "申请 let's encrypt 通配符 ssl 证书"
date = 2019-08-03T19:37:51+08:00
tags = ["WEB开发"]
categories = ["WEB开发"]
draft = false
commentId="web-wildcard-https"
+++
在<[使用 let's encrypt 为网站开启 https](https://www.artacode.com/posts/web/enable-https/)>一问中,讲述了,如何用 let's encrypt 为网站开启 `https` 协议,这篇内容讲一下如何申请 let's encrypt 通配符证书


# 什么是通配符证书
通配符 SSL 可以保护网站的 URL 及其所有子域（数量不限）。例如，一个单独的通配符证书就可以保护 www.artacode.com、blog.artacode.com 和 store.artacode.com。

通配符证书可以保护通用域名和您在提交申请时指定的级别下的所有子域。只需在通用域名左侧的子域区域添加星号 (*) 即可。

举例
如果为 *.artacode.com 申请证书，则可以保护：

artacode.com
www.artacode.com
photos.artacode.com
blog.artacode.com
如果为 *.www.artacode.com 申请证书，则可以保护：

www.artacode.com
mail.www.artacode.com
photos.www.artacode.com
blog.www.artacode.com
通配符证书可以像常规 SSL 证书一样为网站提供保护，并且申请是使用同一种验证方法进行处理的。不过，一些 Web 服务器可能要求使用通配符证书的每个子域都有唯一的 IP 地址。

# 如何申请 let's encrypt 通配符证书
## 安装 Certbot
```
$ wget https://dl.eff.org/certbot-auto
$ sudo mv certbot-auto /usr/local/bin/certbot-auto
$ sudo chown root /usr/local/bin/certbot-auto
$ sudo chmod 0755 /usr/local/bin/certbot-auto
```
## 申请证书
```
certbot-auto --server https://acme-v02.api.letsencrypt.org/directory -d "*.artacode.com" -d "artacode.com" --manual --preferred-challenges dns-01 certonly
//这里多加了一个 `-d "artacode.com"` 是因为 `-d "*.artacode.com"` 不包含 artacode.com

1. 填写接受通知的邮箱
Saving debug log to /var/log/letsencrypt/letsencrypt.log
Plugins selected: Authenticator manual, Installer None
Enter email address (used for urgent renewal and security notices) (Enter 'c' to
cancel): test@test.com

2. 同意协议
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
Please read the Terms of Service at
https://letsencrypt.org/documents/LE-SA-v1.2-November-15-2017.pdf. You must
agree in order to register with the ACME server at
https://acme-v02.api.letsencrypt.org/directory
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
(A)gree/(C)ancel: (A)gree/(C)ancel: a

3. 同意证书服务器记录本机IP
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
NOTE: The IP of this machine will be publicly logged as having requested this
certificate. If you're running certbot in manual mode on a machine that is not
your server, please ensure you're okay with that.

Are you OK with your IP being logged?
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
(Y)es/(N)o: y

- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
4. 设置域名解析
Please deploy a DNS TXT record under the name
_acme-challenge.artacode.cn with the following value:

xxxxxxxxxxxxxx

Before continuing, verify the record is deployed.
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
Press Enter to Continue 回车继续

5. 生成证书成功
Waiting for verification...


Cleaning up challenges

IMPORTANT NOTES:
 - Congratulations! Your certificate and chain have been saved at:
   /etc/letsencrypt/live/artacode.com/fullchain.pem
   Your key file has been saved at:
   /etc/letsencrypt/live/artacode.com/privkey.pem
   Your cert will expire on 2019-11-01. To obtain a new or tweaked
   version of this certificate in the future, simply run certbot-auto
   again. To non-interactively renew *all* of your certificates, run
   "certbot-auto renew"
 - Your account credentials have been saved in your Certbot
   configuration directory at /etc/letsencrypt. You should make a
   secure backup of this folder now. This configuration directory will
   also contain certificates and private keys obtained by Certbot so
   making regular backups of this folder is ideal.
 - If you like Certbot, please consider supporting our work by:

   Donating to ISRG / Let's Encrypt:   https://letsencrypt.org/donate
   Donating to EFF:                    https://eff.org/donate-le


```

# 配置 Nginx

```
server {
    server_name xx.artacode.com;
    listen 443 http2 ssl;
    ssl_certificate /path/.acme.sh/domain/fullchain.cer;
    ssl_certificate_key /path/.acme.sh/domain/domain.key;
    ssl_trusted_certificate  /path/.acme.sh/domain/ca.cer;
}
```

# 总结
除了上述方法之外也有一些自动生成的脚本比如[acme](https://github.com/Neilpang/acme.sh/wiki/How-to-issue-a-cert),就是在 github start 挺多的一个项目,除了手动申请之外也可以借助脚本快速申请 ssl 通配符证书.

# 参考连接
[certbot instructions](https://certbot.eff.org/lets-encrypt/centosrhel7-nginx)