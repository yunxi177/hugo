+++
title = "etcd入门系列一：etcd 在 docker 中使用"
date = 2018-11-19T11:03:08+08:00
tags = ["etcd"]
categories = ["etcd"]
+++
# etcd入门系列
[一. etcd在docker中的安装与使用](http://www.artacode.com/posts/etcd/install/)  
[二. etcd 开启 https](http://www.artacode.com/posts/etcd/enable-https/)  
[三. 身份验证访问控制](http://www.artacode.com/posts/etcd/enable-https/)  
[四. 开启客户端证书访问](http://www.artacode.com/posts/etcd/certificates/)

# 简介
etcd是CoreOS团队于2013年6月发起的开源项目，它的目标是构建一个高可用的分布式键值(key-value)数据库。etcd内部采用raft协议作为一致性算法，etcd基于Go语言实现。

etcd作为服务发现系统，有以下的特点

- 简单：安装配置简单，而且提供了HTTP API进行交互，使用也很简单
- 安全：支持SSL证书验证
- 快速：根据官方提供的benchmark数据，单实例支持每秒2k+读操作
- 可靠：采用raft算法，实现分布式系统数据的可用性和一致性
- etcd项目地址：https://github.com/coreos/etcd/

# 构建镜像
1.创建dockersfile```$ sudo touch Dockerfile```  
2.`$ sudo vim Dockerfile`编辑Dockerfile文件,写入内容如下:

```
FROM alpine:latest

ADD etcd /usr/local/bin/
ADD etcdctl /usr/local/bin/
RUN mkdir -p /var/etcd/
RUN mkdir -p /var/lib/etcd/

# Alpine Linux doesn't use pam, which means that there is no /etc/nsswitch.conf,
# but Golang relies on /etc/nsswitch.conf to check the order of DNS resolving
# (see https://github.com/golang/go/commit/9dee7771f561cf6aee081c0af6658cc81fac3918)
# To fix this we just create /etc/nsswitch.conf and add the following line:
RUN echo 'hosts: files mdns4_minimal [NOTFOUND=return] dns mdns4' >> /etc/nsswitch.conf

EXPOSE 2379 2380

# Define default command.
CMD ["/usr/local/bin/etcd"]
```

3.下载etcd Releases `$ wget https://github.com/etcd-io/etcd/releases/download/v3.3.10/etcd-v3.3.10-linux-amd64.tar.gz`   
4.解压文件:`$ tar -zxvf etcd-v3.3.10-linux-amd64.tar.gz`  
5.将`etcd`和`etcdctl`移动到和dockerfile同级目录:`$ mv etcd-v3.3.10-linux-amd64/etcd etcd-v3.3.10-linux-amd64/etcdctl -t ./`  
6.构建etcd镜像`docker build -t etcd .`  
7.查看构建好的镜像`docker images`
```
[root@localhost etcd]# docker images;
REPOSITORY                 TAG                 IMAGE ID            CREATED             SIZE
etcd                       latest              dd11f9fc0096        13 seconds ago      39.5 MB

```

# 启动etcd(单服务)
```
docker run -d -v /usr/share/ca-certificates/:/etc/ssl/certs -p 4001:4001 -p 2380:2380 -p 2379:2379 \
 --name etcd etcd /usr/local/bin/etcd \
 -name etcd0 \
 -advertise-client-urls http://192.168.3.3:2379,http://192.168.3.3:4001 \
 -listen-client-urls http://0.0.0.0:2379,http://0.0.0.0:4001 \
 -initial-advertise-peer-urls http://192.168.3.3:2380 \
 -listen-peer-urls http://0.0.0.0:2380 \
 -initial-cluster-token etcd-cluster-1 \
 -initial-cluster etcd0=http://192.168.3.3:2380 \
 -initial-cluster-state new
```
## ETCD参数说明
- **data-dir:**指定节点的数据存储目录，这些数据包括节点ID，集群ID，集群初始化配置，Snapshot文件，若未指定—wal-dir，还会存储WAL文件；
- **wal-dir:**指定节点的was文件的存储目录，若指定了该参数，wal文件会和其他数据文件分开存储。
- **name:** 节点名称
- **initial-advertise-peer-urls:** 告知集群其他节点url.(`对于集群内提供服务的url`)
- **listen-peer-urls:** 监听URL，用于与其他节点通讯
- **advertise-client-urls:** 告知客户端url, 也就是服务的url(`对外提供服务的utl`)
- **initial-cluster-token:** 集群的ID
- **initial-cluster:** 集群中所有节点

# 在docker中运行(集群)
## node0
```
docker run -d -p 2380:2380 -p 2479:2379 --name etcd0 etcd /usr/local/bin/etcd -name etcd0  -advertise-client-urls http://192.168.3.3:2479  -listen-client-urls http://0.0.0.0:2379 -initial-advertise-peer-urls http://192.168.3.3:2380 -listen-peer-urls http://0.0.0.0:2380  -initial-cluster-token etcd-cluster-1 -initial-cluster "etcd0=http://192.168.3.3:2380,etcd1=http://192.168.3.3:2381" -initial-cluster-state new
```
## node1
```
docker run -d -p 2381:2380 -p 2480:2379 --name etcd1 etcd /usr/local/bin/etcd -name etcd1  -advertise-client-urls http://192.168.3.3:2480  -listen-client-urls http://0.0.0.0:2379 -initial-advertise-peer-urls http://192.168.3.3:2381 -listen-peer-urls http://0.0.0.0:2380  -initial-cluster-token etcd-cluster-1 -initial-cluster "etcd0=http://192.168.3.3:2380,etcd1=http://192.168.3.3:2381" -initial-cluster-state new
```
这里模拟多端口代表多服务器,需要注意的是`-listen-client-urls http://0.0.0.0:2379`,`-listen-peer-urls http://0.0.0.0:2380`,这两个参数是服务内端口号,对应的是-p中的`2479:2379`中后面的2379,所以这里的监听不需要改变.

# 集群验证
## 验证集群members。在集群中的每台机器上查看members，得出的结果应该是相同的
```
[root@localhost ~]# curl -L http://192.168.3.3:2479/v2/members
{"members":[{"id":"4b316424559e0f9d","name":"etcd0","peerURLs":["http://192.168.3.3:2380"],"clientURLs":["http://192.168.3.3:2479"]},{"id":"6204f98420b429cc","name":"etcd1","peerURLs":["http://192.168.3.3:2381"],"clientURLs":["http://192.168.3.3:2480"]}]}
```
## 某台机器上添加数据，其他机器上查看数据，得出的结果应该是相同的
A 服务器中执行
```
[root@localhost ~]# curl -L http://192.168.3.3:2479/v2/keys/message -XPUT -d value="Hello zhenyuyaodidiao"
{"action":"set","node":{"key":"/message","value":"Hello zhenyuyaodidiao","modifiedIndex":13,"createdIndex":13},"prevNode":{"key":"/message","value":"Hello world1","modifiedIndex":11,"createdIndex":11}}
```
B 服务上执行
```
[root@localhost ~]#  curl -L http://192.168.3.3:2480/v2/keys/message
{"action":"get","node":{"key":"/message","value":"Hello zhenyuyaodidiao","modifiedIndex":13,"createdIndex":13}}
```



