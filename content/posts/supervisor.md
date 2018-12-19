+++
title = "Supervisor 安装与使用"
date = 2018-09-04T11:22:14+08:00
tags = ["linux"]
categories = ["linux"]
commentId="supervisor"
+++
# 简介
supervisor管理进程，是通过fork/exec的方式将这些被管理的进程当作supervisor的子进程来启动，所以我们只需要将要管理进程的可执行文件的路径添加到supervisor的配置文件中就好了。此时被管理进程被视为supervisor的子进程，若该子进程异常中断，则父进程可以准确的获取子进程异常中断的信息，通过在配置文件中设置autostart=ture，可以实现对异常中断的子进程的自动重启。
# 安装
`sudo apt-get install supervisor`
# 配置
- 进入配置文件目录 : `cd /etc/supervisor/conf.d`
- 新建配置文件 `sudo vim test.conf`

配置内如如下：
```
[program:cow] 
command=/home/argericy/workspace/test #要执行的命令
autostart=true #自动启动
autorestart=true #自动重启
user=root #执行命令用户
log_stderr=true
logfile=/var/log/test.log #日志文件
stopsignal=INT
[supervisord]
```
# 加入开机启动（centos）
创建文`/usr/lib/systemd/system/supervisord.service`

    $ sudo vim /usr/lib/systemd/system/supervisord.service

插入如下内容:
```
[Unit] 
Description=Supervisor

[Service] 
Type=forking 
ExecStart=/usr/bin/supervisord -c /etc/supervisor/supervisord.conf  // 启动命令
ExecStop=/usr/bin/supervisorctl shutdown  //关闭命令
ExecReload=/usr/bin/supervisorctl reload  //重载命令
KillMode=process 
Restart=on-failure 
RestartSec=42s

[Install] 
WantedBy=multi-user.target
```

启动服务

	$ systemctl enable supervisord
验证服务是否未开机启动

	$ systemctl is-enabled supervisord //返回 enable 表示已启动


# 常见问题
## supervisor Error: .ini file does not include supervisord section
在配置文件中增加  `[supervisord]` 配置项


