+++
title = "xorm将表生成实体"
date = 2018-09-16T17:24:20+08:00
tags = ["golang"]
categories = ["golang"]
commentId="golang-xorm-reverse"
+++

# xorm 工具
xorm 是一组数据库操作命令行工具。

# 源码安装
`go get github.com/go-xorm/cmd/xorm`

# 使用
## 使用帮助
查看帮助命令： `xorm help reverse`
帮助详情 
```
usage: xorm reverse [-s] driverName datasourceName tmplPath [generatedPath] [tableFilterReg]

according database's tables and columns to generate codes for Go, C++ and etc.

    -s                Generated one go file for every table
    driverName        Database driver name, now supported four: mysql mymysql sqlite3 postgres
    datasourceName    Database connection uri, for detail infomation please visit driver's project page
    tmplPath          Template dir for generated. the default templates dir has provide 1 template
    generatedPath     This parameter is optional, if blank, the default value is models, then will
                      generated all codes in models dir
    tableFilterReg    Table name filter regexp

```
## 使用实例（以mysql为例）
1. 切换项目目录`cd project1`
2. 讲模板配置文件移动到项目目录`cp $GOPATH/src/github.com/go-xorm/cmd/xorm/templates ./template`
3. 执行生成实体命令`xorm reverse mysql root:root@tcp\(127.0.0.1:3306\)/test\?charset=utf8 templates/goxorm` 
4. `ls models`实体生成成功

# 常见问题
## no matches found: root:root@tcp(127.0.0.1:3306)/test?charset=utf8
解决方法：将mysql 链接中的（）和？转义，正确链接为
`root:root@tcp\(127.0.0.1:3306\)/nideshop\?charset=utf8`

# 参考链接
[go-xorm/cmd](https://github.com/go-xorm/cmd)
