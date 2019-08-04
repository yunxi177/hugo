+++
title = "centos 搭建 NFS"
date = 2019-08-04T21:17:55+08:00
tags = ["linux"]
categories = ["linux"]
draft = true
commentId="linux-nfs"
+++

# 查看系统是否已安装NFS
```
$ rpm -qa | grep nfs

nfs-utils-lib-1.1.5-13.el6.i686
nfs-utils-1.2.3-78.el6_10.1.i686
```
如果没有安装,执行下面命令
```
$ yum -y install nfs-utils rpcbind
```

# NFS配置文件
NFS的常用目录

- /etc/exports NFS服务的主要配置文件
- /usr/sbin/exportfs NFS服务的管理命令
- /usr/sbin/showmount 客户端的查看命令
- /var/lib/nfs/etab 记录NFS分享出来的目录的完整权限设定值
- /var/lib/nfs/xtab 记录曾经登录过的客户端信息

# 配置 NFS 服务
NFS服务的配置文件为 /etc/exports，这个文件是NFS的主要配置文件，不过系统并没有默认值，所以这个文件不一定会存在，可能要使用vim手动建立，然后在文件里面写入配置内容。
/etc/exports文件内容格式：

客户端1 选项（访问权限,用户映射,其他）  
客户端2 选项（访问权限,用户映射,其他）

例如
```
$ vim /etc/exports

写入下列语句
/home/data your_ssl_server_ip/24(rw,sync,no_root_squash)

```
常用配置项说明:

访问权限选项
设置输出目录只读：ro
设置输出目录读写：rw

用户映射选项

- all_squash：将远程访问的所有普通用户及所属组都映射为匿名用户或用户组（nfsnobody）；
- no_all_squash：与all_squash取反（默认设置）；

- root_squash：将root用户及所属组都映射为匿名用户或用户组（默认设置）；

- no_root_squash：与rootsquash取反；

- anonuid=xxx：将远程访问的所有用户都映射为匿名用户，并指定该用户为本地用户（UID=xxx）；

- anongid=xxx：将远程访问的所有用户组都映射为匿名用户组账户，并指定该匿名用户组账户为本地用户组账户（GID=xxx）；

其它选项:

- secure：限制客户端只能从小于1024的tcp/ip端口连接nfs服务器（默认设置）；
- insecure：允许客户端从大于1024的tcp/ip端口连接服务器；

- sync：将数据同步写入内存缓冲区与磁盘中，效率低，但可以保证数据的一致性；

- async：将数据先保存在内存缓冲区中，必要时才写入磁盘；

- wdelay：检查是否有相关的写操作，如果有则将这些写操作一起执行，这样可以提高效率（默认设置）；

- no_wdelay：若有写操作则立即执行，应与sync配合使用；

- subtree：若输出目录是一个子目录，则nfs服务器将检查其父目录的权限(默认设置)；

- no_subtree：即使输出目录是一个子目录，nfs服务器也不检查其父目录的权限，这样可以提高效率；

# 设置防火墙
```
$ sudo vim /etc/sysconfig/iptables

加入下面两句
-A INPUT -p tcp -s your_client_ip/32 --dport 1:65535 -j ACCEPT
-A INPUT -p udp -s your_client_ip/32 --dport 1:65535 -j ACCEPT

向客户端开放所有端口(因为 NSF 端口是随机监听的所以需要对客户端开放所有端口)
```
# 重启 NFS 服务
注意重启顺序需要严格按照下面执行

```
$ service rpcbind restart
$ service nfs restart
```
# 客户端操作
查看挂载信息
```
$ showmount -e IP
```

挂载目录

```
$ mount your_ssl_server_ip:/home/data /home/data
```
解除挂载
```
$ unmount -l /your_mount_path
```

自动挂载
```
$ sudo vim /etc/fstab
your_ssl_server_ip:/home/data /home/data                nfs     defaults        0 0
```
