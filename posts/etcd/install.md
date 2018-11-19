+++
title = "etcd在docker中使用"
date = 2018-11-19T11:03:08+08:00
tags = ["etcd"]
categories = ["etcd"]
+++
# 简介
etcd是CoreOS团队于2013年6月发起的开源项目，它的目标是构建一个高可用的分布式键值(key-value)数据库。etcd内部采用raft协议作为一致性算法，etcd基于Go语言实现。

etcd作为服务发现系统，有以下的特点:

- 简单：安装配置简单，而且提供了HTTP API进行交互，使用也很简单
- 安全：支持SSL证书验证
- 快速：根据官方提供的benchmark数据，单实例支持每秒2k+读操作
- 可靠：采用raft算法，实现分布式系统数据的可用性和一致性
- etcd项目地址：https://github.com/coreos/etcd/
# 设置环境变量
`export HostIP="192.168.3.3"`
# 在docker中运行(单服务)
```
docker run -d -v /usr/share/ca-certificates/:/etc/ssl/certs -p 4001:4001 -p 2380:2380 -p 2379:2379 \
 --name etcd quay.io/coreos/etcd:v2.3.8 \
 -name etcd0 \
 -advertise-client-urls http://${HostIP}:2379,http://${HostIP}:4001 \
 -listen-client-urls http://0.0.0.0:2379,http://0.0.0.0:4001 \
 -initial-advertise-peer-urls http://${HostIP}:2380 \
 -listen-peer-urls http://0.0.0.0:2380 \
 -initial-cluster-token etcd-cluster-1 \
 -initial-cluster etcd0=http://${HostIP}:2380 \
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
docker run -d -p 2380:2380 -p 2479:2379 --name etcd0 quay.io/coreos/etcd:v2.3.8 -name etcd0  -advertise-client-urls http://192.168.3.3:2479  -listen-client-urls http://0.0.0.0:2379 -initial-advertise-peer-urls http://192.168.3.3:2380 -listen-peer-urls http://0.0.0.0:2380  -initial-cluster-token etcd-cluster-1 -initial-cluster "etcd0=http://192.168.3.3:2380,etcd1=http://192.168.3.3:2381" -initial-cluster-state new
```
## node1
```
docker run -d -p 2381:2380 -p 2480:2379 --name etcd1 quay.io/coreos/etcd:v2.3.8 -name etcd1  -advertise-client-urls http://192.168.3.3:2480  -listen-client-urls http://0.0.0.0:2379 -initial-advertise-peer-urls http://192.168.3.3:2381 -listen-peer-urls http://0.0.0.0:2380  -initial-cluster-token etcd-cluster-1 -initial-cluster "etcd0=http://192.168.3.3:2380,etcd1=http://192.168.3.3:2381" -initial-cluster-state new
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



