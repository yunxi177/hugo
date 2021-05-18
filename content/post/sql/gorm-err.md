+++
title = "Error 1292: Incorrect datetime value: '0000-00-00' for column 'created_at' at row 1"
date = 2018-12-22T08:48:49+08:00
tags = ["mysql", "gorm"]
categories = ["mysql", "gorm"]
draft = false
commentId="sql-mysql-sql_mode"
+++

使用 gorm 插入数据时爆出 `Error 1292: Incorrect datetime value: '0000-00-00' for column 'created_at' at row 1`  的错误信息，搜索之后发现原来是因为 msyql5.7 及以上不允许时间戳字段插入 ‘0000-00-00’ 数据，修复方法也比较简单，去掉sql_model 中的 `NO_ZERO_DATE` 配置即可

# 方式一 sql 配置
使用 `select @@GLOBAL.sql_mode;` 查出原有的配置信息，将配置信息去除 `NO_ZERO_DATE` 并复制

使用 `SET GLOBAL sql_mode = '去除 NO_ZERO_DATE 后的剩余配置项'` 

经过以上步骤就可以完成设置了。

# 方式二 配置文件配置
打开 mysql 配置文件：
	$ sodo vim /etc/my.cnf
在 [mysqld] 配置项下插入 sql_mode 配置
`sql_mode=ONLY_FULL_GROUP_BY,STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_ENGINE_SUBSTITUTION
`
重启 mysql 完成配置

# sql_model 参数详解
```
ONLY_FULL_GROUP_BY：
对于GROUP BY聚合操作，如果在SELECT中的列，没有在GROUP BY中出现，那么这个SQL是不合法的，因为列不在GROUP BY从句中

NO_AUTO_VALUE_ON_ZERO：
该值影响自增长列的插入。默认设置下，插入0或NULL代表生成下一个自增长值。如果用户 希望插入的值为0，而该列又是自增长的，那么这个选项就有用了。

STRICT_TRANS_TABLES：
在该模式下，如果一个值不能插入到一个事务表中，则中断当前的操作，对非事务表不做限制

NO_ZERO_IN_DATE：
在严格模式下，不允许日期和月份为零

NO_ZERO_DATE：
设置该值，mysql数据库不允许插入零日期，插入零日期会抛出错误而不是警告。

ERROR_FOR_DIVISION_BY_ZERO：
在INSERT或UPDATE过程中，如果数据被零除，则产生错误而非警告。如 果未给出该模式，那么数据被零除时MySQL返回NULL

NO_AUTO_CREATE_USER：
禁止GRANT创建密码为空的用户

NO_ENGINE_SUBSTITUTION：
如果需要的存储引擎被禁用或未编译，那么抛出错误。不设置此值时，用默认的存储引擎替代，并抛出一个异常

PIPES_AS_CONCAT：
将"||"视为字符串的连接操作符而非或运算符，这和Oracle数据库是一样的，也和字符串的拼接函数Concat相类似
ANSI_QUOTES：
启用ANSI_QUOTES后，不能用双引号来引用字符串，因为它被解释为识别符

```

# 参考链接
[Error 1292 on update a row](https://github.com/jinzhu/gorm/issues/595)
