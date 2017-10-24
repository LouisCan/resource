---
title: redis基本操作命令
date: 2017-09-11 15:44:32
tags: redis
categories: 大数据
thumbnail: http://osewdhh4j.bkt.clouddn.com/redis.png
keywords: redis,redis基本操作命令
description: redis是一个key-value存储系统。和Memcached类似，它支持存储的value类型相对更多，包括string(字符串)、list(链表)、set(集合)、zset(sorted set --有序集合)和hash（哈希类型）。这些数据类型都支持push/pop、add/remove及取交集并集和差集及更丰富的操作，而且这些操作都是原子性的。在此基础上，redis支持各种不同方式的排序。与memcached一样，为了保证效率，数据都是缓存在内存中。区别的是redis会周期性的把更新的数据写入磁盘或者把修改操作写入追加的记录文件，并且在此基础上实现了master-slave(主从)同步。
---

## redis基本操作命令

### 一：redis键值对的管理和操作
```
1 DEL key
该命令用于在 key 存在时删除 key。
2 DUMP key
序列化给定 key ，并返回被序列化的值。
3 EXISTS key
检查给定 key 是否存在。
4 EXPIRE key seconds
为给定 key 设置过期时间。
5 EXPIREAT key timestamp
EXPIREAT 的作用和 EXPIRE 类似，都用于为 key 设置过期时间。 不同在于 EXPIREAT 命令接受的时间参数是 UNIX 时间戳(unix timestamp)。
6 PEXPIRE key milliseconds
设置 key 的过期时间以毫秒计。
7 PEXPIREAT key milliseconds-timestamp
设置 key 过期时间的时间戳(unix timestamp) 以毫秒计
8 KEYS pattern
查找所有符合给定模式( pattern)的 key 。
9 MOVE key db
将当前数据库的 key 移动到给定的数据库 db 当中。
10 PERSIST key
移除 key 的过期时间，key 将持久保持。
11 PTTL key
以毫秒为单位返回 key 的剩余的过期时间。
12 TTL key
以秒为单位，返回给定 key 的剩余生存时间(TTL, time to live)。
13 RANDOMKEY
从当前数据库中随机返回一个 key 。
14 RENAME key newkey
修改 key 的名称
15 RENAMENX key newkey
仅当 newkey 不存在时，将 key 改名为 newkey 。
16 TYPE key
返回 key 所储存的值的类型。
```

### 二：redis哈希的操作

```
HDEL key field2 [field2] 
删除一个或多个哈希表字段
HEXISTS key field 
查看哈希表 key 中，指定的字段是否存在。
HGET key field 
获取存储在哈希表中指定字段的值。
HGETALL key 
获取在哈希表中指定 key 的所有字段和值
HINCRBY key field increment 
为哈希表 key 中的指定字段的整数值加上增量 increment 。
HINCRBYFLOAT key field increment 
为哈希表 key 中的指定字段的浮点数值加上增量 increment 。
HKEYS key 
获取所有哈希表中的字段
HLEN key 
获取哈希表中字段的数量
HMGET key field1 [field2] 
获取所有给定字段的值
HMSET key field1 value1 [field2 value2 ] 
同时将多个 field-value (域-值)对设置到哈希表 key 中。
HSET key field value 
将哈希表 key 中的字段 field 的值设为 value 。
HSETNX key field value 
只有在字段 field 不存在时，设置哈希表字段的值。
HVALS key 
获取哈希表中所有值HSCAN key cursor [MATCH pattern] [COUNT count] 迭代哈希表中的键值对。
```

### 三：redis列表操作

```
BLPOP key1 [key2 ] timeout 
移出并获取列表的第一个元素， 如果列表没有元素会阻塞列表直到等待超时或发现可弹出元素为止。
BRPOP key1 [key2 ] timeout 
移出并获取列表的最后一个元素， 如果列表没有元素会阻塞列表直到等待超时或发现可弹出元素为止。
BRPOPLPUSH source destination timeout 
从列表中弹出一个值，将弹出的元素插入到另外一个列表中并返回它； 如果列表没有元素会阻塞列表直到等待超时或发现可弹出元素为止。
LINDEX key index 
通过索引获取列表中的元素
LINSERT key BEFORE|AFTER pivot value 
在列表的元素前或者后插入元素
LLEN key 
获取列表长度
LPOP key 
移出并获取列表的第一个元素
LPUSH key value1 [value2] 
将一个或多个值插入到列表头部
LPUSHX key value 
将一个或多个值插入到已存在的列表头部
LRANGE key start stop 
获取列表指定范围内的元素
LREM key count value 
移除列表元素
LSET key index value 
通过索引设置列表元素的值
LTRIM key start stop 
对一个列表进行修剪(trim)，就是说，让列表只保留指定区间内的元素，不在指定区间之内的元素都将被删除。
RPOP key 
移除并获取列表最后一个元素
RPOPLPUSH source destination 
移除列表的最后一个元素，并将该元素添加到另一个列表并返回
RPUSH key value1 [value2] 
在列表中添加一个或多个值
RPUSHX key value 
为已存在的列表添加值
```
 

### 四：redis集合
 
```
SADD key member1 [member2] 
向集合添加一个或多个成员
SCARD key 
获取集合的成员数
SDIFF key1 [key2] 
返回给定所有集合的差集
SDIFFSTORE destination key1 [key2] 
返回给定所有集合的差集并存储在 destination 中
SINTER key1 [key2] 
返回给定所有集合的交集
SINTERSTORE destination key1 [key2] 
返回给定所有集合的交集并存储在 destination 中
SISMEMBER key member 
判断 member 元素是否是集合 key 的成员
SMEMBERS key 
返回集合中的所有成员
SMOVE source destination member 
将 member 元素从 source 集合移动到 destination 集合
SPOP key 
移除并返回集合中的一个随机元素
SRANDMEMBER key [count] 
返回集合中一个或多个随机数
SREM key member1 [member2] 
移除集合中一个或多个成员
SUNION key1 [key2] 
返回所有给定集合的并集
SUNIONSTORE destination key1 [key2] 
所有给定集合的并集存储在 destination 集合中
SSCAN key cursor [MATCH pattern] [COUNT count] 
迭代集合中的元素
```

### 五：有序集合

```
ZADD key score1 member1 [score2 member2] 
向有序集合添加一个或多个成员，或者更新已存在成员的分数
ZCARD key 
获取有序集合的成员数
ZCOUNT key min max 
计算在有序集合中指定区间分数的成员数
ZINCRBY key increment member 
有序集合中对指定成员的分数加上增量 increment
ZINTERSTORE destination numkeys key [key ...] 
计算给定的一个或多个有序集的交集并将结果集存储在新的有序集合 key 中
ZLEXCOUNT key min max 
在有序集合中计算指定字典区间内成员数量
ZRANGE key start stop [WITHSCORES] 
通过索引区间返回有序集合成指定区间内的成员
ZRANGEBYLEX key min max [LIMIT offset count] 
通过字典区间返回有序集合的成员
ZRANGEBYSCORE key min max [WITHSCORES] [LIMIT] 
通过分数返回有序集合指定区间内的成员
ZRANK key member 
返回有序集合中指定成员的索引
ZREM key member [member ...] 
移除有序集合中的一个或多个成员
ZREMRANGEBYLEX key min max 
移除有序集合中给定的字典区间的所有成员
ZREMRANGEBYRANK key start stop 
移除有序集合中给定的排名区间的所有成员
ZREMRANGEBYSCORE key min max 
移除有序集合中给定的分数区间的所有成员
ZREVRANGE key start stop [WITHSCORES] 
返回有序集中指定区间内的成员，通过索引，分数从高到底
ZREVRANGEBYSCORE key max min [WITHSCORES] 
返回有序集中指定分数区间内的成员，分数从高到低排序
ZREVRANK key member 
返回有序集合中指定成员的排名，有序集成员按分数值递减(从大到小)排序
ZSCORE key member 
返回有序集中，成员的分数值
ZUNIONSTORE destination numkeys key [key ...] 
计算给定的一个或多个有序集的并集，并存储在新的 key 中
ZSCAN key cursor [MATCH pattern] [COUNT count] 
迭代有序集合中的元素（包括元素成员和元素分值）
```

### 六：redis事物的实现
 
```
DISCARD 
取消事务，放弃执行事务块内的所有命令。
EXEC 
执行所有事务块内的命令。
MULTI 
标记一个事务块的开始。
UNWATCH 
取消 WATCH 命令对所有 key 的监视。
WATCH key [key ...] 
监视一个(或多个) key ，如果在事务执行之前这个(或这些) key 被其他命令所改动，那么事务将被打断。
```


-------------------------------------------------------

## Redis 事务

### 事务相关命令

**MULTI**

> 自1.2.0可用。

> 时间复杂度：O(1)。

```
语法：MULTI
```

说明：

标记一个事务块的开始。

事务块内的多条命令会按照先后顺序被放进一个队列当中，最后由 EXEC 命令原子性(atomic)地执行。

返回值：

总是返回 `OK` 。

示例：

```
# 下面命令在 客户端1 中执行
coderknock> MULTI
OK
coderknock> SET testMULTI 0
QUEUED
coderknock> INCR testMULTI
QUEUED
coderknock> INCR testMULTI
QUEUED
coderknock> INCR testMULTI
QUEUED

# 这个时候从另一个 客户端（我们称呼为客户端2） 执行以下：
coderknock> GET testMULTI
(nil)
# 此时我们可以看到 客户端1 的命令还没有执行
# 下面切换到 客户端1 执行 EXEC
coderknock> EXEC
1) OK
2) (integer) 1
3) (integer) 2
4) (integer) 3
# 此时在 客户端2 中就可以访问到 该数据了
coderknock> GET testMULTI
"3"
```

**DISCARD**

> 自2.0.0可用。

> 时间复杂度：O(1)。

```
语法：DISCARD
```

说明：

取消事务，放弃执行事务块内的所有命令。

如果正在使用 WATCH 命令监视某个(或某些) key，那么取消所有监视，等同于执行命令 UNWATCH 。

返回值：

总是返回 `OK` 。

示例：

```
# 当没有事务开启时
coderknock> DISCARD
(error) ERR DISCARD without MULTI

coderknock> MULTI
OK
# ping 用来查询状态 测试该客户端处于队列状态（开启事务时命令只是装入队列并不会执行）
coderknock> PING
QUEUED
coderknock> SET testDISCARD
(error) ERR wrong number of arguments for 'set' command
coderknock> SET testDISCARD a
QUEUED
# 退出事务
coderknock> DISCARD
OK
# 状态恢复正常状态
coderknock> PING
PONG
# 事务中的命令没有执行
coderknock> GET testDISCARD
(nil)
```

**WATCH**

> 自2.2.0可用。

> 时间复杂度：O(1)。

```
语法：WATCH key [key ...]
```

说明：

监视一个(或多个) key ，如果在事务执行之前这个(或这些) key 被其他命令所改动，那么事务将被打断。

返回值：

总是返回 `OK` 。



**UNWATCH**

> 自2.2.0可用。

> 时间复杂度：O(1)。

```
语法：UNWATCH
```

说明：

取消 WATCH 命令对所有 key 的监视。

如果在执行 WATCH 命令之后， EXEC 命令或 DISCARD 命令先被执行了的话，那么就不需要再执行 UNWATCH 了。

因为 EXEC 命令会执行事务，因此 WATCH 命令的效果已经产生了；而 DISCARD 命令在取消事务的同时也会取消所有对 key 的监视，因此这两个命令执行之后，就没有必要执行 UNWATCH 了。

返回值：

总是返回 `OK` 。



**EXEC**

>自1.2.0可用。

>时间复杂度：事务块内所有命令的时间复杂度的总和。

```
语法：EXEC
```

说明：

执行所有事务块内的命令。

假如某个(或某些) key 正处于 WATCH 命令的监视之下，且事务块中有和这个(或这些) key 相关的命令，那么 EXEC 命令只在这个(或这些) key 没有被其他命令所改动的情况下执行并生效，否则该事务被打断(abort)。

返回值：

事务块内所有命令的返回值，按命令执行的先后顺序排列。

当操作被打断时，返回空值 `nil` 。

示例：

```
# 在 MULTI 命令的实例中我们演示了事务正常执行的情况
# 客户端1
# 使用 WATCH 监视 key 且正常执行成功
coderknock> WATCH testWATCH
OK
coderknock> MULTI
OK
coderknock> SET testWATCH 2123
QUEUED
coderknock> EXEC
1) OK
# 使用 WATCH 监视 key 且未正常执行
coderknock> WATCH testWATCH
OK
coderknock> SET testWATCH a
OK
coderknock> GET test WATCH
(error) ERR wrong number of arguments for 'get' command
coderknock> GET testWATCH
"a"
coderknock> DEL testWATCH
(integer) 1
coderknock> MUTLI
(error) ERR unknown command 'MUTLI'
coderknock> MULTI
OK
coderknock> SET testWATCH a
QUEUED
coderknock> SET testWATCH ccc
QUEUED
# 客户端2
coderknock> SET testWATCH abc
OK
# 客户端1
coderknock> EXEC
(nil)
coderknock> GET testWATCH
"abc"
# 只要有在 WATCH 之后在非事务中对 key 有操作就不可以，无论是你哪个客户端
coderknock> WATCH testWATCH
OK
coderknock> SET testWATCH a
OK
coderknock> MULTI
OK
coderknock> SET testWATCH qwe
QUEUED
coderknock> EXEC
(nil)
# 语法错误会造成整个事务无法执行
coderknock> WATCH testWATCH
OK
coderknock> MULTI
OK
coderknock> SET testWATCH aaa
QUEUED
coderknock> EXEC \
    (error) ERR unknown command 'EXEC\'
coderknock> EXEC
(error) EXECABORT Transaction discarded because of previous errors.
```

### 事务中错误处理

 - 语法错误会造成整个事务无法执行（示例中 EXEC 命令错误）
 - 运行时错误：非语法错误，只是使用命令方式不正确比如使用 SADD 操作字符类型等等，只是错误部分报错，其他正常执行，且最后不会回滚事务。

Redis 提供了简单的事务，之所以说它简单，主要是因为它不支持事务中的回滚特性，同时无法实现命令之间的逻辑关系计算，当然也体现了 Redis 的 “keep it simple” 的特性。




