+++
title = "Composer 私有仓库搭建"
date = 2021-04-01T18:00:46+08:00
tags = ["php"]
categories = ["php"]
draft = false
commentId="php-composer-private-repo"
+++
Composer 是 PHP 的软件包管理系统，它提供用于管理 PHP 软件和依赖库关系的标准格式。作为日常开发，Composer 能够满足我们的日常需求，但有些情况下，我们有些公司内部的扩展，不希望被检索或者放在共有库中，就需要搭建私有库来解决问题。  
在讨论搭建私有仓库前，我们先了解一下 Composer 是如何安装扩展的
# Composer 安装扩展流程
![composer下载流程图](https://pic.artacode.com/image.png)  
通过上图我们不难发现，如果要构建私有仓库，我们需要构建一个类似 [Packagist](https://packagist.org/) 的网站，来告诉 Composer 该从哪里下载扩展代码，这就需要用到 **Satis** 工具


# Satis
## 介绍
**Satis** 是一个开源的静态 Composer 仓库生成器，有了它我们可以轻松的构建出一个类似 [Packagist](https://packagist.org/) 的索引网站，来告诉 Composer 通过我们自己的私有仓库下载扩展。
## 安装
### 命令行安装
```
// 安装
composer create-project composer/satis:dev-main
// 构建索引仓库
php bin/satis build <configuration-file> <output-directory>
```
### docker 安装
下载镜像：  
`docker pull composer/satis`
运行镜像:
```
docker run --rm --init -it \
  --user $(id -u):$(id -g) \
  --volume $(pwd):/build \
  --volume "${COMPOSER_HOME:-$HOME/.composer}:/composer" \
  composer/satis build <configuration-file> <output-directory>
```
## 配置
创建一个名为：`satis.json` 的配置文件。   
内容如下:  
```
{
    "name": "passboat", 
    "homepage": "http://packages.example.org", 
    "repositories": [
        {"type": "vcs", "url": "你的仓库地址（支持git/svn）", }
    ], 
    "require-all": true
}
```
为了拉取代码更为方便将 ssh  密钥配置到代码仓库中。ssh 密钥生成参考[生成 SSH 公钥](https://git-scm.com/book/zh/v2/%E6%9C%8D%E5%8A%A1%E5%99%A8%E4%B8%8A%E7%9A%84-Git-%E7%94%9F%E6%88%90-SSH-%E5%85%AC%E9%92%A5)

## 构建
```
docker run --rm -it  -v 配置文件所在目录:/build -v 配置文件所在目录:/root/.ssh composer/satis build /build/satis.json /build/public
```

执行完上述命令后，会在`配置文件所在目录`生成一个 `public` 文件夹，该文件夹就是 **Satis** 为我们生成的静态索引网站。接下来就是通过配置 nginx 将域名解析到生成的 `public` 文件夹。

# Nginx 配置
当通过 **Satis** 生成索引网站后，我们需要通过 nginx 配置，使其能够通过外网访问到该网站.
配置完成后访问下过如下：
![composer静态网站配置](https://pic.artacode.com/composerstaticweb.png)

# Composer 配置
在需要安装私有扩展的项目里，在 `composer.json` 文件中加入如下代码:  
```
  "repositories": [
        {
          "type": "composer",
          "url": "nginx配置的静态所以网站的域名"
        }
    ],
```  
# Conclusion
经过上述步骤，整个私有仓库的搭建就已经完成了,如果你对私有仓库需要更多的配置，可以阅读[Composer 私有包文档](https://getcomposer.org/doc/articles/handling-private-packages.md),进行更细致的设置，祝进步！

# Reference
[Satis github](https://github.com/composer/satis)  
[Composer](https://getcomposer.org/doc/articles/handling-private-packages.md)