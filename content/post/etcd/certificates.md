+++
title = "etcd入门系列四：开启客户端证书访问"
date = 2018-11-25T14:26:14+08:00
tags = ["etcd"]
categories = ["etcd"]
commentId="etcd-certificates"
+++

# etcd入门系列
[一. etcd在docker中的安装与使用](http://www.artacode.com/posts/etcd/install/)  
[二. etcd 开启 https](http://www.artacode.com/posts/etcd/enable-https/)  
[三. 身份验证访问控制](http://www.artacode.com/posts/etcd/enable-https/)  
[四. 开启客户端证书访问](http://www.artacode.com/posts/etcd/certificates/)
# 1. 生成客户端证书
生成client.json：

	$ cfssl print-defaults csr > client.json

编辑 client.json 的修改 `CN` 值为 cliet
```
...
    "CN": "client",
    "hosts": [""],
...
```

生成客户端证书

	cfssl gencert -ca=ca.pem -ca-key=ca-key.pem -config=ca-config.json -profile=client client.json | cfssljson -bare client
将会得到如下文件
```
client-key.pem
client.csr
client.pem
```

# 2. 开启客户端证书检测

```
docker run -v /root/cfssl:/root/cfssl -p 2379:2379  --name etcd-net etcd /usr/local/bin/etcd  -name etcd-net  --client-cert-auth --trusted-ca-file=/root/cfssl/ca.pem --cert-file=/root/cfssl/server.pem --key-file=/root/cfssl/server-key.pem  -advertise-client-urls https://0.0.0.0:2379  -listen-client-urls https://0.0.0.0:2379
```

`--client-cert-auth:`当这个选项被设置时，etcd 将为受信任CA签名的客户端证书检查所有的传入的 HTTPS 请求，不能提供有效客户端证书的请求将会失败。  
`--trusted-ca-file=<path>:` 受信任的认证机构
# 3. 验证配置
输入命令：

	etcdctl --ca-file /root/cfssl/ca.pem --endpoints https://192.168.3.3:2379 set foo 1

返回结果：

``
routines:SSL3_READ_BYTES:sslv3 alert bad certificate
``
接下来，带着之前生成的证书尝试重新发送请求
```
etcdctl --ca-file /root/cfssl/ca.pem --cert-file /root/cfssl/client.pem --key-file /root/cfssl/client-key.pem --endpoints https://192.168.3.3:2379 set foo 1

//返回值：1
```
带上证书后访问可以如预期中进行，到此有关 `etcd` 的 安装，角色权限控制，客户端证书与服务端通讯已经完成，如果大家在使用过程中有什么问题，欢迎交流。