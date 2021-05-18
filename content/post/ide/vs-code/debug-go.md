+++
title = "VS Code golang debug 配置"
date = 2019-03-25T18:24:21+08:00
tags = ["IDE", "工欲善其事，必先利其器"]
categories = ["IDE", "工欲善其事，必先利其器"]
draft = false
commentId="ide-vs-code-debugging-go"
+++
# 实现效果
![](http://pic.artacode.com/18.gif)
# 安装 delve

## windows
`go get -u github.com/go-delve/delve/cmd/dlv`

## linux 
方式一：  
`go get -u github.com/go-delve/delve/cmd/dlv`  
方式二  
```
$ git clone https://github.com/go-delve/delve.git $GOPATH/src/github.com/go-delve/delve
$ cd $GOPATH/src/github.com/go-delve/delve
$ make install
```
> 注意: 若果你go版本为1.5需要设置`GO15VENDOREXPERIMENT=1`

## OSX
```
$ go get -u github.com/go-delve/delve/cmd/dlv
```
执行上述代码前，确保你电脑上有编译工具

# 设置 launch.json 配置文件
`ctrl+shift+p` 输入 `Debug: Open launch.json` 打开 `launch.json` 文件，如果第一次打开,会新建一个配置文件，默认配置内容如下
```
{
	"version": "0.2.0",
	"configurations": [
		{
			"name": "Launch",
			"type": "go",
			"request": "launch",
			"mode": "auto",
			"program": "${fileDirname}",
			"env": {},
			"args": []
		}
	]
}
```
常见属性如下  

|属性|介绍|
|---|---|
|name|调试界面下拉选择项的名称|
|type|设置为go无需改动，是 vs code 用于计算调试代码需要用哪个扩展|
|mode|可以设置为 `auto`, `debug`, `remote`, `test`, `exec` 中的一个|
|program|调试程序的路径（绝对路径）|
|env|调试时使用的环境变量。例如:`{ "ENVNAME": "ENVVALUE" }`|
|envFile|包含环境变量文件的绝对路径，在 `env` 中设置的属性会覆盖 `envFile` 中的配置|
|args|传给正在调试程序命令行参数数组|
|showLog|布尔值，是否将调试信息输出|
|logOutput|配置调试输出的组件（`debugger`, `gdbwire`, `lldbout`, `debuglineerr`, `rpc`）,使用,分隔， `showLog` 设置为 `true` 时，此项配置生效|
|buildFlags|构建 go 程序时传给 go 编译器的标志|
|remotePath|远程调试程序的绝对路径，当 `mode ` 设置为 `remote` 时有效|

## 在 debug 配置中使用 VS Code 变量
- ${workspaceFolder} 调试 VS Code 打开工作空间的根目录下的所有文件
- ${file} 调试当前文件
- ${fileDirname} 调试当前文件所在目录下的所有文件

## 使用构建标记(build tags)
如果需要使用构建标记（e.g. go build -tags=whatever_tag）在参数 `buildFlags ` 里写入 `-tags=whatever_tag"` 即可，支持多标签，使用单引号将标签包围,例如： `"-tags='first_tag second_tag third_tag'"`

# debug 配置代码片段
可以在 `launch.json` 文件中，使用 `Go` 关键词调出 `debug` 配置项的代码片段。

## 调试当前文件配置片段
```
{
	"name": "Launch file",
	"type": "go",
	"request": "launch",
	"mode": "auto",
	"program": "${file}"
}
```
## 调试单个测试用例配置片段
```
{
	"name": "Launch test function",
	"type": "go",
	"request": "launch",
	"mode": "test",
	"program": "${workspaceFolder}",
	"args": [
		"-test.run",
		"MyTestFunction"
	]
}
```

## 调试包中所有的测试用例配置片段
```
{
	"name": "Launch test package",
	"type": "go",
	"request": "launch",
	"mode": "test",
	"program": "${workspaceFolder}"
}
```

## 调试构建二进制文件配置片段
```
{
	"name": "Launch executable",
	"type": "go",
	"request": "launch",
	"mode": "exec",
	"program": "absolute-path-to-the-executable"
}
```

# 远程调试
远程调试需要在服务器上期一个无头的  `Delve`  服务  
例如：
```
$ dlv debug --headless --listen=:2345 --log --api-version=2
```
如果需要传给参数到程序中则需要将参数传入到 `Delve` 服务中
例如：
```
dlv debug --headless --listen=:2345 --log -- -myArg=123
```
`launch.json` 配置如下：
```
{
	"name": "Launch remote",
	"type": "go",
	"request": "launch",
	"mode": "remote",
	"remotePath": "服务器调试文件的目录（绝对路径）",
	"port": 2345,
	"host": "127.0.0.1",
	"program": "本地调试文件所在的目录（绝对路径）",
	"env": {}
}
```

- 将上述配置中的 `host` 和 `port` 修改 `Delve` 服务所在的服务器ip和其监听的端口号。
- `remotePath`  应该配置为调试文件的绝对路径
- `program `  需要配置本地机器文件所在的绝对路径，是 `remotePath` 的一个副本
  
配置完成后，点击调试按钮，`VS Code` 将会项远程的 `dlv` 服务发出调试指令，而不是使用本机的 `dlv` 实例。 

# 参考链接
[Debugging Go code using VS Code](https://github.com/Microsoft/vscode-go/wiki/Debugging-Go-code-using-VS-Code#sample-configuration-to-debug-a-single-test)  
[Delve Installation](https://github.com/go-delve/delve/tree/master/Documentation/installation)