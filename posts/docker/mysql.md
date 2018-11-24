+++
title = "docker mysql启动报错"
date = 2018-09-15T16:22:17+08:00
tags = ["docker"]
categories = ["docker"]
+++

## 错误信息
```
2018-09-15T08:11:26.620005077Z 2018-09-15T08:11:26.619857Z 0 [ERROR] [FATAL] InnoDB: Table flags are 0 in the data dictionary but the flags in file ./ibdata1 are 0x4800!
2018-09-15T08:11:26.620023933Z 2018-09-15 08:11:26 0x7f7203c4b740  InnoDB: Assertion failure in thread 140127666222912 in file ut0ut.cc line 942

```

## 解决方法
原因是宿主机data目录不为空照成;
删除默认data目录：/var/lib/mysql