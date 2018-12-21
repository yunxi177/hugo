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
# 参数解释
```
;[program:theprogramname]
;command=/bin/cat              ; 程序执行命令，需要
;process_name=%(program_name)s ; process_name expr (default %(program_name)s)
;numprocs=1                    ; 进程启动数量，默认为1
;directory=/tmp                ; 工作目录，如果配置了工作目录，command 配置项 可以直接执行工作目录里的命令
;umask=022                     ; 设置进程权限 默认为：none
;priority=999                  ; 设置优先级 默认：999
;autostart=true                ; 在 supervisor 启动时启动程序，默认为：true
;startsecs=1                   ; 程序启动 startsecs 秒 后视为程序启动成功， 默认为：1
;startretries=3                ; 当程序启动失败后最大尝试次数,默认为：3
;autorestart=unexpected        ; 其值为可以设置为 true 、false 、expected 设置为 true 无论什么情况下都自动重启， 设置为 false 则不自动重启，设置 unexpected 则配合 exitcodes 使用，只有在 exitcodes 配置的中的退出代码 才会自动重启
;exitcodes=0,2                 ; autorestart 设置为 unexpected 此项生效，默认为： 0,2
;stopsignal=QUIT               ; 用于 kill 进程的信号 默认为： TERM
;stopwaitsecs=10               ; 停止进程等待秒数，默认为：10
;stopasgroup=false             ; 发送停止信号给 UNIX 进程组 默认为：false
;killasgroup=false             ; 发送立刻中止信号给 UNIX 进程组，默认为：false
;user=chrism                   ; 设置启动程序的用户
;redirect_stderr=true          ; redirect proc stderr to stdout (default false)
;stdout_logfile=/a/path        ; 输入日志配置
;stdout_logfile_maxbytes=1MB   ; 日志容量大与1m则分割日志
;stdout_logfile_backups=10     ; 备份文件数量，默认为10
;stderr_logfile=/a/path        ; 标准错误日志输出
;stderr_logfile_maxbytes=1MB   ; 日志容量大与1m则分割日志
;stderr_logfile_backups=10     ; 备份文件数量，默认为10
;serverurl=AUTO                ; override serverurl computation (childutils)
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



