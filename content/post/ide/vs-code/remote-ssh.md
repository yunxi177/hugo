+++
title = "vs code 调试远程代码"
date = 2019-06-19T20:31:23+08:00
tags = ["IDE","工欲善其事，必先利其器"]
categories = ["IDE","工欲善其事，必先利其器"]
draft = false
commentId="ide-vscode-remote-ssh"
+++
# 简介
**Visual Studio Code Remote - SSH** 可以打开远程计算机或容器中的文件(计算机或容器中需要运行 `SSH Server`),而且可已充分利用 `VS Code` 的一切特性.一旦连接上服务器,可以与任意位置的文件进行交  由于 `Remote-SSH` 扩展运行命令行和其他扩展是在远程主机上,因此本地不需要代码,就可以正常运行.  
![Remote-SSH示意图](http://pic.artacode.com/architecture-ssh.png)  

# 入门
1. 确保本机已经安装 `VS CODE`
2. 确保本机已安装 `SSH client`, 并且确保远程主机已安装 `SSH server`
3. 为本机 `VS CODE` 安装 ` Remote Development` 扩展


# 连接远程主机
## 连接配置
`Ctrl + Shift + p` 输入 `Configure SSH Hosts...`   回车,设置远程主机信息,配置信息如下

```
# Read more about SSH config files: https://linux.die.net/man/5/ssh_config
Host oa-online
    HostName 远程主机Ip
    User root 
```
- `Host` 远程主机名称
- `HostName` 远程主机IP
- `User` 连接用户名

## 用户名密码连接
在配置文件 `settings.json` 加入 `"remote.SSH.showLoginTerminal": true`  
`Ctrl + Shift + p` 输入 `Remote-SSH:Connect to Host...`   回车, 选择配置的远程主机,在命令行处输入远程主机密码,即可连接远程主机  
此方式会导致每次都需要输入密码,相对来说比较麻烦,推荐方式二(SSH 密钥连接)

## 密钥连接
检查 `c盘->用户->自己的用户名->.ssh` 是否有**id_rsa** **id_rsa.pub**文件,如果没用使用 `ssh-keygen` 命令生成密钥文件
将 `id_rsa.pub` 里的内容复制到远程主机的'~/.ssh/authorized_keys' 文件中
`Ctrl + Shift + p` 输入 `Remote-SSH:Connect to Host...`   回车, 选择配置的远程主机,在命令行处输入远程主机密码,即可连接远程主机.


# 插件安装
`VS Code` 支持安装插件到远程主机,以增加远程代码调试的流畅性. `VS Code` 支持两种方式安装插件到远程主机
1. 同步本地已安装插件到远程主机
2. 搜索插件直接安装到远程主机

需要注意安装插件到远程主机,需要在与远程主机建立好连接后,才可以进行远程主机插件的安装  
搜索插件安装到远程主机  
![安装插件到远程主机](http://pic.artacode.com/安装推荐插件到远程主机.png)
同步本地插件到远程主机  
![同步本地插件到远程主机](http://pic.artacode.com/指定本地安装的扩展安装到远程服务器.png)
本地插件列表及远程主机插件列表
![本地插件列表及远程主机插件列表](http://pic.artacode.com/扩展.png)

# 远程主机命令行使用
`Ctrl + ~` 打开终端窗口,点 `+` 新建一个
`VS Code` 连接到远程主机后,就可以直接使用终端窗口在远程主机上执行终端命令

# 总结
随着 `Remote Development` 插件的上线标志者远程开发的时代到来,通过一些简单的了解 `VS Code` 对远程开发的实现已经是一个比较完善的阶段, 对于一个 `VS Code` 的重度使用者来说, 这也将一定程度减少对`Xshell` 与 `XFTP` 的依赖.  
目前 `Remote Development` 还不支持 32 位系统, 据官方信息应该在很快的版本更新中就可以添加对 32 为远程主机的支持,期待 `VS Code` 将此功能越做越好.

# 参考连接
[VS Code Remote Developmen](https://code.visualstudio.com/docs/remote/remote-overview)  
[Remote Development using SSH](https://code.visualstudio.com/docs/remote/ssh)