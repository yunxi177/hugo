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


# 背景介绍

**SETNX key value**

> 将 key 的值设为 value ，当且仅当 key 不存在。若给的 key 已经存在，则 SETNX 不做任何动作。SETNX 是『SET if Not eXists』(如果不存在，则 SET)的简写。


```
redis> EXISTS job                # job 不存在
(integer) 0

redis> SETNX job "programmer"    # job 设置成功
(integer) 1

redis> SETNX job "code-farmer"   # 尝试覆盖 job ，失败
(integer) 0

redis> GET job                   # 没有被覆盖
"programmer"

```


当 `key` 不存在时，返回 1， 如果设置的 `key` 存在时则返回 0 ，该操作本身具有原子性，因此可以利用 redis 此特性进行分布式锁实现的基础。

# 分布时锁实现
## 雏形
上面介绍了 `SETNX` 用法，我们可以使用以下步骤实现分布式锁：
1. 当 A 请求过来, 将锁的状态设置为true: $redis->setNx("lock", "true")，返回 1 设置成功
2. 在 A 请求未结束时， B 、C 请求到来获取锁的状态$redis->setNx("lock", "true")，返回 0 设置失败， 则直接返回请求结果，等待下次执行。
3. A 执行完毕，释放锁。

上面的分布式锁存在一个问题，就是 `setNx` 并没有一个过期时间的设置，这样如果在步骤 1 中 A 请求异常退出并没有释放锁就会导致死锁的情况。  

## 进一步完善
要解决上述死锁的问题，我们就需要引入一个过期时间的机制，当一段时间内锁没有进行释放，则自动将锁释放。在锁 `lock` 中将值设置为过期的时间戳，当设置 `lock` 值返回 0 时，需要多一步判断，就时将 `lock` 中的值取出来与当前时间戳对比，如果小于当前时间戳则原有锁失效，该请求成功过的锁。需要注意的时这里获取过期时间戳并不能直接用 `get` 命令，因为如果用 `get` 当 B、C 同时获取锁的时，会出现 B 和 C 同时拿到过期的时间戳，这样就会导致 B、C 都认为自己拿到了锁，导致锁失效。  
`getset` 该命令的作用时将新的默认值
操作步骤如下：

1. A 请求 $redis->setNx("lock", current timestamp) 返回 1 成功获取锁
2. B 请求 `$redis->setNx("lock", currentTimestamp)` 返回 0 未拿到锁，获取锁的过期时间， `$expireTime = $redis->getSet("lock", currentTimestamp)` ，判断锁是否过期 `$expireTime < current timestamp`, 过期时间小于当前时间，则锁过期，B 成功获取锁

 






