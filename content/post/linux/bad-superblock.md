+++
title = "解决nfs挂载错误wrong fs type, bad option, bad superblock"
date = 2019-08-03T19:54:26+08:00
tags = ["linux"]
categories = ["linux"]
draft = false
commentId="linux-bad-superblock"
+++

```
[root@localhost]# mount -t nfs 192.168.0.106:/home/nfs1
mount: wrong fs type, bad option, bad superblock on 192.168.0.106:/home/nfs1,
       missing codepage or helper program, or other error
       (for several filesystems (e.g. nfs, cifs) you might
       need a /sbin/mount.<type> helper program)
       In some cases useful info is found in syslog - try
       dmesg | tail  or so
```

解决办法：
```
apt-get install nfs-common
```
或者
```
yum install nfs-utils
```