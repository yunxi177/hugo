+++
title = "etcd开启https"
date = 2018-11-21T13:44:07+08:00
tags = ["etcd"]
categories = ["etcd"]
+++  
上节([etcd在docker中使用](http://www.artacode.com/posts/etcd/install/))etcd已经可以正常使用,这节讲讲如何开启htpps

# 1 生成签名
## 1.1 下载 cfssl
```
mkdir ~/bin
curl -s -L -o ~/bin/cfssl https://pkg.cfssl.org/R1.2/cfssl_linux-amd64
curl -s -L -o ~/bin/cfssljson https://pkg.cfssl.org/R1.2/cfssljson_linux-amd64
chmod +x ~/bin/{cfssl,cfssljson}
export PATH=$PATH:~/bin
```
需要注意的是,这里是以linux的64位为例,如果你是其他系统,请修改成对应系统的cfssl包

## 1.2 初始化证书

使用`cfssl`模板生成`ca-config.json`,`ca-csr.json`
```
mkdir ~/cfssl
cd ~/cfssl
cfssl print-defaults config > ca-config.json
cfssl print-defaults csr > ca-csr.json
```

## 1.3 证书介绍
- 客户端证书(client certificate):用于服务器对客户端进行身份验证.例如 `etcdctl`, `etcd proxy`, `docker` 等客户端.
- 服务器证书(server certificate):服务器端使用,用于客户端验证服务器身份.例如 `docker` 服务器, `kube-apiserver`
- 对等证书(peer certificate):用于etcd集群间的双向通讯

## 1.4 配置 CA 选项
现在我在 `ca-config.json` 配置文件中配置签名选项,默认设置中已经有了如下配置项:
- profiles: :www与服务器认证(TLS Web服务器认证)X509 V3扩展和客户端与客户端认证(TLS Web客户端认证)X509 V3扩展
- expiry: 有效期,默认值为8760h

接下来将`www`改为名为server,`expiry` 改为 43800 h `ca-config.json` 修改后内容如下:
```
{
    "signing": {
        "default": {
            "expiry": "43800h"
        },
        "profiles": {
            "server": {
                "expiry": "43800h",
                "usages": [
                    "signing",
                    "key encipherment",
                    "server auth"
                ]
            },
            "client": {
                "expiry": "43800h",
                "usages": [
                    "signing",
                    "key encipherment",
                    "client auth"
                ]
            }
        }
    }
}

```
你也可以修改 `ca-csr.json` json证书签名请求
```
{
    "CN": "My own CA",
    "key": {
        "algo": "rsa",
        "size": 2048
    },
    "names": [
        {
            "C": "US",
            "L": "CA",
            "O": "My Company Name",
            "ST": "San Francisco",
            "OU": "Org Unit 1",
            "OU": "Org Unit 2"
        }
    ]
}
```
使用定义好的签名生成 CA 证书
```
cfssl gencert -initca ca-csr.json | cfssljson -bare ca -
```
将会生成如下文件:
```
ca-key.pem
ca.csr
ca.pem
```
# 1.5 生成服务器证书
```
cfssl print-defaults csr > server.json
```
得到`server.json`文件, 更改 `Common Name(CN)` 和 `hosts`值如下:
```
  "CN": "coreos1",
    "hosts": [
        "192.168.3.3",//替换成你自己的服务器地址或者域名
        "ext.example.com",
        "coreos1.local",
        "coreos1"
    ],
```
接下来生成服务器证书和私钥
```
cfssl gencert -ca=ca.pem -ca-key=ca-key.pem -config=ca-config.json -profile=server server.json | cfssljson -bare server
```
生成文件如下:
```
server-key.pem
server.csr
server.pem
```
# 2 启动etcd服务
```
docker run -v /root/cfssl:/root/cfssl -p 2379:2379 \
 --name etcd etcd /usr/local/bin/etcd \
 -name etcd0 \
 --cert-file=/root/cfssl/server.pem --key-file=/root/cfssl/server-key.pem \
 -advertise-client-urls https://192.168.3.3:2379 \
 -listen-client-urls https://0.0.0.0:2379
```

# 3 验证请求是否正确
## 3.1 不带证书请求
```
curl https://10.105.8.139:2379/v2/keys/foo -XPUT -d value=bar -v
```
输出错误如下:
```
客户端错误:
* About to connect() to 10.105.8.139 port 2379 (#0)
*   Trying 10.105.8.139...
* Connected to 10.105.8.139 (10.105.8.139) port 2379 (#0)
* Initializing NSS with certpath: sql:/etc/pki/nssdb
*   CAfile: /etc/pki/tls/certs/ca-bundle.crt
  CApath: none
* Server certificate:
* 	subject: CN=example.net,L=CA,ST=San Francisco,C=US
* 	start date: Nov 21 06:40:00 2018 GMT
* 	expire date: Nov 21 06:40:00 2019 GMT
* 	common name: example.net
* 	issuer: CN=example.net,L=CA,ST=San Francisco,C=US
* NSS error -8179 (SEC_ERROR_UNKNOWN_ISSUER)
* Peer's Certificate issuer is not recognized.
* Closing connection 0
curl: (60) Peer's Certificate issuer is not recognized.
More details here: http://curl.haxx.se/docs/sslcerts.html

curl performs SSL certificate verification by default, using a "bundle"
 of Certificate Authority (CA) public keys (CA certs). If the default
 bundle file isn't adequate, you can specify an alternate file
 using the --cacert option.
If this HTTPS server uses a certificate signed by a CA represented in
 the bundle, the certificate verification probably failed due to a
 problem with the certificate (it might be expired, or the name might
 not match the domain name in the URL).
If you'd like to turn off curl's verification of the certificate, use
 the -k (or --insecure) option.
 
 
 
 
服务端:
rejected connection from "10.105.8.139:46692" (error "remote error: tls: unknown certificate authority", ServerName "")
```

## 3.2 带证书请求
```
curl --cacert /root/cfssl/ca.pem https://10.105.8.139:2379/v2/keys/foo -XPUT -d value=bar -v
```
请求结果如下:
```
> PUT /v2/keys/foo HTTP/1.1
> User-Agent: curl/7.29.0
> Host: 192.168.3.3:2379
> Accept: */*
> Content-Length: 9
> Content-Type: application/x-www-form-urlencoded
> 
* upload completely sent off: 9 out of 9 bytes
< HTTP/1.1 200 OK
< Content-Type: application/json
< X-Etcd-Cluster-Id: cdf818194e3a8c32
< X-Etcd-Index: 5
< X-Raft-Index: 6
< X-Raft-Term: 2
< Date: Wed, 21 Nov 2018 06:49:58 GMT
< Content-Length: 163
< 
{"action":"set","node":{"key":"/foo","value":"bar","modifiedIndex":5,"createdIndex":5},"prevNode":{"key":"/foo","value":"bar","modifiedIndex":4,"createdIndex":4}}
* Connection #0 to host 192.168.3.3 left intact

```

配置HTTPS成功

# 参考文章
[security](https://github.com/etcd-io/etcd/blob/master/Documentation/op-guide/security.md)  
[generate-self-signed-certificates](https://github.com/coreos/docs/blob/master/os/generate-self-signed-certificates.md#certificate-types-which-are-used-inside-container-linux)  
