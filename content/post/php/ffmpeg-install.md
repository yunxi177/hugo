+++
title = "PHP-FFMpeg 安装"
date = 2020-08-28T13:49:19+08:00
tags = ["php"]
categories = ["php"]
draft = false
commentId="php-ffmpeg-install"

+++

# 安装 FFMPEG

```
wget http://download.bt.cn/install/ext/ffmpeg.sh && sh ffmpeg.sh
```

安装完后可输入以下命令是否安装成功

```
ffmpeg -version
```

# 删除禁用函数

proc_open， exec, system



# 安装 PHP-FFMpeg

```
composer require php-ffmpeg/php-ffmpeg
```



# 常见问题

##  Executable not found, proposed : avprobe, ffprobe

php-fpm 在执行 FFMPEG 的时候，没有找到 `ffprobe` 和 `ffmpeg` 解决办法如下：

### 命令行解决

1. 通过 shell 确定文件位置

```
whereis ffprobe
/usr/local/bin/ffprobe
which ffmpeg
/usr/local/bin/ffmpeg
```

2. 在初始化 FFMpeg 的时候将文件位置写入

```php
 $ffmpeg = FFMpeg::create([
            'ffmpeg.binaries' => '/usr/local/bin/ffmpeg',
            'ffprobe.binaries' => '/usr/local/bin/ffprobe',
 ]);
```



### PHP 代码直接解决

```php
// 写入 WWW 用户的环境变量
putenv('PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/root/bin:$PATH');
// 通过 exec 函数找到文件对应位置
$ffmpeg = FFMpeg::create([
     'ffmpeg.binaries' => exec('which ffmpeg'),
     'ffprobe.binaries' => exec('which ffprobe'),
]);
```



虽然看似两种办法，其实解决思路是一致的，都是要指定 `ffmpeg` 和 `ffprobe` 文件的位置。理论上直接在服务器上将 www 用户的环境变量加入 `ffmpeg` 和 `ffprobe` 也可以解决此问题（没有测试，不保证有效，有兴趣的可以自行验证）。



# Reference

[PHP-FFMpeg](https://github.com/PHP-FFMpeg/PHP-FFMpeg)

[Executable not found, proposed : avprobe, ffprobe](https://github.com/PHP-FFMpeg/PHP-FFMpeg/issues/172)

[一键安装ffmpeg命令](https://www.bt.cn/bbs/thread-7766-1-1.html)