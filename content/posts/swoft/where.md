+++
title = "swoft openWhere使用指南"
date = 2018-11-15T10:47:05+08:00
tags = ["swoft"]
categories = ["swoft"]
gitmentId="swoft-where"
+++
# open/close where 理解
1. `openWhere` 为where条件的左边括号即`(`  
2. `closeWhere` 为where条件的右边括号即`)`

# 实例

```
//默认连接符为AND
Query::table("order")->openWhere()->where('id','1')->where('state', 3)->closeWhere()->openWhere()->where('id', 3)->where('state', 2)->closeWhere()->one()->getResult();
//执行结果为:
SELECT * FROM `order_1` WHERE ( `id` = '1' AND  `state` = '3') AND ( `id` = '3' AND  `state` = '2') LIMIT 0,1

//设置连接符为or
Query::table("order")->openWhere()->where('id','1')->where('state', 3)->closeWhere()->openWhere(QueryBuilder::LOGICAL_OR)->where('id', 3)->where('state', 2)->closeWhere()->one()->getResult();
//执行结果
SELECT * FROM `order_1` WHERE ( `id` = '1' AND  `state` = '3') OR ( `id` = '3' AND  `state` = '2') LIMIT 0,1
```
