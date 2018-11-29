+++
title = "etcd入门系列三：身份验证访问控制"
date = 2018-11-24T12:05:50+08:00
tags = ["etcd"]
categories = ["etcd"]
commentId="etcd-auth"
+++
# etcd入门系列
[一. etcd在docker中的安装与使用](http://www.artacode.com/posts/etcd/install/)  
[二. etcd 开启 https](http://www.artacode.com/posts/etcd/enable-https/)  
[三. 身份验证访问控制](http://www.artacode.com/posts/etcd/enable-https/)  
[四. 开启客户端证书访问](http://www.artacode.com/posts/etcd/certificates/)

# 1. 简介
etcd 默认是没有开启访问控制的，如果我们开启外网访问的话就需要考虑访问控制的问题，etcd 提供了两种访问控制的方式：
1. 基于身份验证的访问控制
2. 基于证书的访问控制 

这节主要是选择第一种方式，进行的讲解，由于之前文章中是采用http接口的方式通讯，为了更全面的了解 `etcd` 的使用，本节使用官方提供的 `etcdctl` 工具进行与服务器之间的通讯。

# 2. root 用户
`root` 是 `etcd` 的超级管理员，拥有 `etcd` 的所有权限，在开启角色认证之前为们必须要先建立好 `root` 用户。还需要注意的是 `root` 用户必须拥有 `root` 的角色,允许在 `etcd` 的所有操作.

# 3. root 角色
`root` 角色可以赋予任何用户，拥有 `root` 角色的用户有全局读写权限和集群身份验证配置权限，此外，还具有修改集群成员身份，碎片整理，建立快照等权限。
    	
# 4. 用户操作
查看用户列表：  

    $ etcdctl --ca-file /root/cfssl/ca.pem --endpoints https://192.168.3.3:2379 user list
创建用户：

     $ etcdctl --ca-file /root/cfssl/ca.pem --endpoints https://192.168.3.3:2379 user add user1
用户可以被赋予角色，也可以被撤销角色：
	
    #赋予权限
	$ etcdctl --ca-file /root/cfssl/ca.pem --endpoints https://192.168.3.3:2379 user grant --roles root user1
    # 撤销权限
	$ etcdctl --ca-file /root/cfssl/ca.pem --endpoints https://192.168.3.3:2379 user revoke --roles root user1
修改用户密码：

    $ etcdctl --ca-file /root/cfssl/ca.pem --endpoints https://192.168.3.3:2379 user passwd user1
# 5. 角色操作
角色列表:

	$ etcdctl --ca-file /root/cfssl/ca.pem --endpoints https://192.168.3.3:2379 role list
创建角色:

	$ etcdctl --ca-file /root/cfssl/ca.pem --endpoints https://192.168.3.3:2379 role add myrolename
    
- 角色没有密码，仅仅是定义的一组访问权限
- 角色的访问权限可以被赋予read（读）,write（写）,readwrite（读和写）权限

赋予访问权限范例：
```
# 给 role1 角色赋予键 /foo 的读操作
$ etcdctl --ca-file /root/cfssl/ca.pem --endpoints https://192.168.3.3:2379 role grant role1 --path /foo --read
# 给 role1 角色赋予键 /foo 的写操作
$ etcdctl --ca-file /root/cfssl/ca.pem --endpoints https://192.168.3.3:2379 role grant role1 --path /foo --write
# 给 role1 角色赋予键 /foo 读写操作
$ etcdctl --ca-file /root/cfssl/ca.pem --endpoints https://192.168.3.3:2379 role grant role1 --path /foo --rw
# 给 role1 角色赋予键 /foo 目录读写操作
$ etcdctl --ca-file /root/cfssl/ca.pem --endpoints https://192.168.3.3:2379 role grant role1 --path /foo/* --rw
```
收回访问权限范例：
```
# 收回 role1 角色对 /foo 的读操作
$ etcdctl --ca-file /root/cfssl/ca.pem --endpoints https://192.168.3.3:2379 role revoke role1 --path /foo --read
# 收回 role1 角色对 /foo 的写操作
$ etcdctl --ca-file /root/cfssl/ca.pem --endpoints https://192.168.3.3:2379 role revoke role1 --path /foo --write
# 收回 role1 角色对 /foo 的读写操作
$ etcdctl --ca-file /root/cfssl/ca.pem --endpoints https://192.168.3.3:2379 role revoke role1 --path /foo --rw
# 收回 role1 角色对 /foo 目录的读写操作
$ etcdctl --ca-file /root/cfssl/ca.pem --endpoints https://192.168.3.3:2379 role revoke role1 --path /foo/* --rw

```
查看角色访问权限:

	$ etcdctl --ca-file /root/cfssl/ca.pem --endpoints https://192.168.3.3:2379 role get role1

删除角色 :
	
	$ etcdctl --ca-file /root/cfssl/ca.pem --endpoints https://192.168.3.3:2379 role remove role1
    
# 6. 开启身份验证
1.增加root用户:

	$ etcdctl --ca-file /root/cfssl/ca.pem --endpoints https://192.168.3.3:2379 user add root
    Password of root:
2.开启身份验证:


	$ etcdctl --ca-file /root/cfssl/ca.pem --endpoints https://192.168.3.3:2379 etcdctl auth enable
至此，身份验证已经开启，执行下面命令
```
etcdctl --ca-file /root/cfssl/ca.pem  --endpoints https://192.168.3.3:2379 set /foo bar
//返回bar
```
这里出现了一个问题，我们已经开启了身份访问验证，却仍然可以不通过任何身份进行操作，这是什么原因呢？其实是因为在 `Etcd` 开启 `Basic Auth` 之后，默认会启用两个角色 `root` 和 `guest`， `root` 和 `guest` 角色都拥有所有权限，当我们未指定身份的时候其实是通过 `guest` 角色进行的操作，这里需要注意的是两个角色都不要删除，否则你可能会遇到意想不到的Bug，既然无法删除，那么为们可以通过收回权限的方式对 `guest` 的权限进行限制，执行下面代码：
```
etcdctl --ca-file /root/cfssl/ca.pem --username root:passwod --endpoints https://192.168.3.3:2379 ro
le revoke guest --path '/*' --rw
//返回：Role guest updated

//查看guest最新的权限信息
etcdctl --ca-file /root/cfssl/ca.pem --username root:passwod --endpoints https://192.168.3.3:2379 ro
le get guest


//返回 ：
 //       Role: guest
//        KV Read:
//        KV Write:
// ok 收回权限成功
```

继续执行之前的set代码 ：
```
etcdctl --ca-file /root/cfssl/ca.pem  --endpoints https://192.168.3.3:2379 set /foo bar
//Error:  110: The request requires user authentication (Insufficient credentials) [0]
```
如我们所愿的已经不可以对 etcd 进行操作了，下面我们创建一个用户并赋予一个新建的角色试试：
```
# 创建user2用户
etcdctl --ca-file /root/cfssl/ca.pem --username root:passwod --endpoints https://192.168.3.3:2379 user add user2

New password: 
//返回 ：User user2 created

# 创建role2角色
etcdctl --ca-file /root/cfssl/ca.pem --username root:passwod --endpoints https://192.168.3.3:2379 role add role2

Role role2 created

# 赋予role2 角色权限

etcdctl --ca-file /root/cfssl/ca.pem --username root:passwod --endpoints https://192.168.3.3:2379 ro
le grant role2 --path /foo --rw
Role role2 updated


# 将用户user2赋予角色role2
etcdctl --ca-file /root/cfssl/ca.pem --username root:passwod --endpoints https://192.168.3.3:2379 us
er  grant --roles role2 user2

User user2 updated

# 设置 foo 值
etcdctl --ca-file /root/cfssl/ca.pem --username user2:passwod --endpoints https://192.168.3.3:2379:2379 set foo bar

bar
```
到此我们想要实现的功能都已实现，个人理解难免有些疏漏和不足，欢迎大神斧正。
# 7. 参考连接
[Role-based access control](https://github.com/etcd-io/etcd/blob/master/Documentation/op-guide/authentication.md)  
[Etcd安全配置之Basic Auth认证](https://juejin.im/post/5b986abff265da0ad947b52f)
