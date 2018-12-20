+++
title = "Centos 服务配置详解"
date = 2018-12-20T17:11:12+08:00
tags = ["linux"]
categories = ["linux"]
draft = false
commentId = "linux-centos-service"
+++

# systemd 介绍
CentOS 7的服务systemctl脚本存放在：/usr/lib/systemd/，有系统（system）和用户（user）之分，即：/usr/lib/systemd/system ，/usr/lib/systemd/user

# 创建 Service 文件
以 Supervisor 加入服务为例
```
[Unit] 
Description=Supervisor

[Service] 
Type=forking 
ExecStart=/usr/bin/supervisord -c /etc/supervisor/supervisord.conf
ExecStop=/usr/bin/supervisorctl shutdown
ExecReload=/usr/bin/supervisorctl reload
KillMode=process 
Restart=on-failure 
RestartSec=42s

[Install] 
WantedBy=multi-user.target
```

# 参数介绍
Unit：

| NAME| DESCRIPTION|
|:-|:-|
| ExecStartPre|在单元（Unit）开始前执行命令|
| ExecStart|在单元（Unit）开始时执行主命令|
| ExecStartPost|在 ExecStart 命令执行完毕之后执行|
| ExecReload|在单元（Unit）Reload 时执行主命令|
| ExecStop|在单元（Unit） Stop 时执行命令|
| ExecStopPost|在 ExecStop 配置命令执行完毕后执行|
| RestartSec|服务重启失败重试间隔时间|

Type : 启动类型simple、forking、oneshot、notify、dbus
 > Type=simple（默认值）：systemd认为该服务将立即启动。服务进程不会fork。如果该服务要启动其他服务，不要使用此类型启动，除非该服务是socket激活型。 Type=forking：systemd认为当该服务进程fork，且父进程退出后服务启动成功。对于常规的守护进程（daemon），除非你确定此启动方式无法满足需求，使用此类型启动即可。使用此启动类型应同时指定 PIDFile=，以便systemd能够跟踪服务的主进程。 Type=oneshot：这一选项适用于只执行一项任务、随后立即退出的服务。可能需要同时设置 RemainAfterExit=yes 使得 systemd 在服务进程退出之后仍然认为服务处于激活状态。 Type=notify：与 Type=simple 相同，但约定服务会在就绪后向 systemd 发送一个信号。这一通知的实现由 libsystemd-daemon.so 提供。 Type=dbus：若以此方式启动，当指定的 BusName 出现在DBus系统总线上时，systemd认为服务就绪。

PIDFile ： pid文件路径 
ExecStartPre ：启动前要做什么，上文中是测试配置文件 －t  
ExecStart：启动 
ExecReload：重载 
ExecStop：停止 
PrivateTmp：True表示给服务分配独立的临时空间

install ：
[Install]部分是服务安装的相关设置，可设置为多用户的

# 启动服务
	$ systemctl enable supervisord
    
# 验证服务是否启动
	$ systemctl is-enabled supervisord //返回 enable 表示已启动
