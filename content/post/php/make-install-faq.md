+++
title = "编译安装php常见问题"
date = 2019-02-24T15:04:42+08:00
tags = ["php"]
categories = ["php"]
draft = false
commentId="php-make-install-faq"
+++
# configure: error: libxml2 not found. 

详细错误如下:
```
configure: error: libxml2 not found. Please check your libxml2 installation.
make: *** No targets specified and no makefile found.  Stop.
make: *** No rule to make target 'install'.  Stop.
```
解决方法：
```
$ sudo apt-get install libxml2-dev
```

# configure: error: png.h not found.
详细错误如下:
```
configure: error: png.h not found.
make: *** No targets specified and no makefile found.  Stop.
make: *** No rule to make target 'install'.  Stop.
```

解决方法：
```
$ sudo apt-get install libpng-dev
```

# configure: error: freetype-config not found.

详细错误如下:

```
configure: error: freetype-config not found.
make: *** No targets specified and no makefile found.  Stop.
make: *** No rule to make target 'install'.  Stop.
```

解决方法:
```
$ sudo apt-get install libfreetype6-dev
```
# configure: error: xslt-config not found.

详细错误如下:
```
configure: error: xslt-config not found. Please reinstall the libxslt >= 1.1.0 distribution
make: *** No targets specified and no makefile found.  Stop.
make: *** No rule to make target 'install'.  Stop.
```

解决方法:
```
$ sudo apt-get install libxslt-dev
```

# 总结
上述出现的一些问题，是我在此次编译安装php时遇到的问题，根本原因是缺少一些依赖的组件，安装好之后，就可以正常编译php了，如果有什么疑问，欢迎在文章下方留言。
