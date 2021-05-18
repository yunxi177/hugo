+++
title = "Sqlite3 stdlib.h: No such file or directory"
date = 2018-09-23T12:29:55+08:00
tags = ["golang"]
categories = ["golang"]
commentId="golang-sqlite3-error"
+++

# sqlite3/backup.go:14:20: fatal error: stdlib.h: No such file or directory
## 错误提示

```
# github.com/mattn/go-sqlite3
../../../.go/src/github.com/mattn/go-sqlite3/backup.go:14:20: fatal error: stdlib.h: No such file or directory
compilation terminated.
```
## 解决方法
`sudo apt-get install g++`
# 参考链接
[mattn/go-sqlite3/issues](https://github.com/mattn/go-sqlite3/issues/481)