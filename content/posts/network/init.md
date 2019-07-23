+++
title = "Uninx 网络编程环境搭建"
date = 2019-07-24T06:37:53+08:00
tags = ["网络编程"]
categories = ["网络编程"]
draft = false
commentId="network-init"

+++

**1，安装编译器，为了齐全还是安装一下build-essential**

```
sudo apt-get install build-essential  
```

**2，下载书中的头文件及示例源码**

[**Source Code**](http://www.unpbook.com/src.html)



**3，解压unpv13e.tar.gz后进入目录查看README，然后可以按照里面提示操作，不过会遇到问题**

```
第一步：在终端中进入upnv13e目录，然后执行代码：
1. chmod u+x configure
2. ./configure  
第二步：进入lib目录下执行make：
1. cd lib  
2. make
第三步：建立基础类库：

1. cd ../libfree  # continue building the basic library  
2. make  
第四步：编译函数库：

1. cd ../libgai   # the getaddrinfo() and getnameinfo() functions  
2. make  
```

在执行上述代码的第三步的时候会如果遇到如下错误

```
gcc -I../lib -g -O2 -D_REENTRANT -Wall   -c -o in_cksum.o in_cksum.c
gcc -I../lib -g -O2 -D_REENTRANT -Wall   -c -o inet_ntop.o inet_ntop.c
inet_ntop.c: In function ‘inet_ntop’:
inet_ntop.c:60:9: error: argument ‘size’ doesn’t match prototype
  size_t size;
         ^~~~
In file included from inet_ntop.c:27:
/usr/include/arpa/inet.h:64:20: error: prototype declaration
 extern const char *inet_ntop (int __af, const void *__restrict __cp,
                    ^~~~~~~~~
make: *** [<builtin>: inet_ntop.o] Error 1

```

解决方法在inet_ntop.c中加入以下代码：

```
#define size_t socklen_t
```



**4，将生成的libunp.a静态库复制到/usr/lib/和/usr/lib64/中。**

```
cd ..   //回到unpv12e目录  

1. sudo cp libunp.a /usr/lib  
2. sudo cp libunp.a /usr/lib64  
```



**5，修改unp.h并将其和config.h拷贝到/usr/include中，为了以后include方便**

```
gedit lib/unp.h   //将unp.h中#include "../config.h"修改为#include "config.h"  
1. sudo cp lib/unp.h /usr/include  
2. sudo cp config.h /usr/include  
```

**6，编译源代码**

```
1. cd ./intro  
2. gcc daytimetcpcli.c -o daytimetcpcli -lunp  
```

