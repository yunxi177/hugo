+++
title = "freeRADIUS搭建"
date: 2018-08-19T10:56:28+08:00  
tags = ["计算机网络"]
categories = ["计算机网络"]
+++


# 系统要求

## 操作系统
CentOS 7.0 x86_64
网址：http://www.centos.org/
## 所需软件
1. FreeRADIUS 3.0
网址：http://freeradius.org/
目前最新版本为3.0.6
2. MariaDB
网址：https://mariadb.com/
目前最新版本为10.3.9
注：MariaDB数据库管理系统是MySQL的一个分支，主要由开源社区在维护，采用GPL授权许可。开发这个分支的原因之一是：甲骨文公司收购了MySQL后，有将MySQL闭源的潜在风险，因此社区采用分支的方式来避开这个风险。 MariaDB的目的是完全兼容MySQL，包括API和命令行，使之能轻松成为MySQL的代替品。MariaDB虽然被视为MySQL数据库的替代品，但它在扩展功能、存储引擎以及一些新的功能改进方面都强过MySQL。

## 注意事项
本文提到的所有操作，需要以root身份执行。
# FreeRADIUS 3.0的安装
根据官网上的说明，建议采用二进制安装包的形式安装，而不推荐从源代码编译的方式。因此，本次采用二进制安装包的形式安装。.
## 安装FreeRADIUS 3.0 服务器程序
1. 以root身份登录到终端，执行以下命令：`yum install freeradius`
2. 输入“y”，继续安装。
3. 安装完毕。

## 安装FreeRADIUS工具包（freeradius-utils）
这个工具包是用于测试freeradius的，后面会用到。安装方法同前，执行一下命令：
`yum install freeradius-utils`

# FreeRADIUS 3.0的测试
## 修改用户管理配置文件
该文件是`/etc/raddb/users`，使用vi工具打开，命令如下:
`sudo vim /etc/raddb/users`
打开文件后，查找 `steve Cleartext-Password :="testing"` （76-84行）, 取消该段内容的注释。
## 修改防火墙配置
修改防火墙配置，允许freeradius所使用的端口1812，1813通过。命令如下：
```
iptables -A INPUT -p udp --dport 1812 -j ACCEPT
iptables -A INPUT -p udp --dport 1813 -j ACCEPT
```
## 以调试方式启动freeradius
执行以下命令：`radiusd –X`
## 使用测试工具测试
再打开一个新的终端，以root身份登录，执行如下命令：
`radtest steve testing localhost 0 testing123`

如果输出`"Access-Accept packet" `表示成功了，`"Access-Reject" `表示失败了。

# docker中安装MariaDB
## 启动docker
[root@test01 ~]# docker run -it --name="mysql_server" centos /bin/bash
## 安装mariadb服务
[root@e8126d0481d2 /]# yum -y install mariadb-server net-tools

## 初始化mariadb
[root@e8126d0481d2 /]# mysql_install_db --user=mysql

## 后台启动mariadb服务
[root@e8126d0481d2 /]# mysqld_safe &

# FreeRADIUS与MariaDB的对接
## 安装freeradius-mysql组件
`yum install freeradius-mysql`
## 创建数据库
### 进入数据库
`mysql -u root –p`
### 创建radius数据库
```
mysql>create database radius;
mysql>grant all on radius.* to radius@localhost identified by "radpass"; 
mysql>exit;
```
### 重启数据库
我这里是在docker中安装的数据库因此我重启数据库只需要重启docker即可，你可以google你系统方式下的重启命令
## 导入表结构
`mysql -u root radius < /etc/raddb/mods-config/sql/main/mysql/schema.sql`

## 建立组信息和用户信息
输入mysql -u root radius，打开数据库，在 mysql> 提示符下，执行如下命令：

### 建立组信息：（在此新建组名称为user）

```
insert into radgroupreply (groupname,attribute,op,value) values ('user','Auth-Type',':=','Local'); 
insert into radgroupreply (groupname,attribute,op,value) values ('user','Service-Type',':=','Framed-User'); 
insert into radgroupreply (groupname,attribute,op,value) values ('user','Framed-IP-Address',':=','255.255.255.255'); 
insert into radgroupreply (groupname,attribute,op,value) values ('user','Framed-IP-Netmask',':=','255.255.255.0');

```

### 建立用户信息：（在此新建用户名为test，密码为testpwd）
```
insert into radcheck (username,attribute,op,value) values ('test','Cleartext-Password',':=','testpwd');
```
### 将用户加入组中
```
insert into radusergroup (username,groupname) values ('test','user');
```
## 修改freeRADIUS配置
输入 `sudo vim /etc/raddb/radiusd.conf`修改下列配置
```
auth = yes
auth_badpass = yes
auth_goodpass = yes
```

## 修改 FreeRADIUS中的mysql 认证配置
执行如下命令：  
```
cd /etc/raddb/mods-enabled
ln -s ../mods-available/sql
```
## 修改 FreeRADIUS中的mysql 配置文件
该配置文件位于`/etc/raddb/mods-available`目录，名称为sql  
输入 `sudo vim /etc/raddb/mods-available/sql`修改如下配置 
```
 dialect = "sqlite" -> dialect = "mysql"
 //下列配置前的注释去掉
 server = "localhost" //mysql服务器地址 
 port = 3306 //mysql 端口号
 login = "radius" //myqsl 登录用户名
 password = "radpass" //mysql 登录密码
 read_clients = yes
```
## 重启frreRADIUS
重新调用`radiusd -X`运行freeRADIUS
重新开启一个终端输入命令`radtest test testpwd localhost 1812 testing123`
返回如下信息
```
[root@9ae6be8b906d mods-enabled]# radtest test testpwd localhost 1812 testing123 
Sent Access-Request Id 227 from 0.0.0.0:56905 to 127.0.0.1:1812 length 74
	User-Name = "test"
	User-Password = "testpwd"
	NAS-IP-Address = 172.17.0.2
	NAS-Port = 1812
	Message-Authenticator = 0x00
	Cleartext-Password = "testpwd"
Received Access-Accept Id 227 from 127.0.0.1:1812 to 0.0.0.0:0 length 38
	Service-Type = Framed-User
	Framed-IP-Address = 255.255.255.255
	Framed-IP-Netmask = 255.255.255.0

```
配置成功
