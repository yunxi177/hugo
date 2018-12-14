+++
title = "redis实现分布式锁"
date = 2018-12-14T12:33:08+08:00
tags = ["分布式"]
categories = ["分布式"]
draft = true
commentId="distributed-redis-lock"
+++
# 起因
消息队列处理任务中，由于消息队列中有相同的处理请求并且消费端存在多个，因此出现了并发消费的问题，于是产生了利用分布式锁来锁定消费，保证同一时间只有一个请求在处理，找了一下分布式锁的实现方式常见的有：
- 基于数据库
- 基于Zookeeper
- 基于redis

