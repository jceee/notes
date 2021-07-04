title: Redis有序集合做订阅列表
date: 2016-01-18 22:33
categories: redis
tags: redis

---

Redis 的有序集合和集合一样，成员(member)的值是string类型，而且成员是唯一的，并不重复,重复地保存同一个成员只会重置成员的分数，以最后一次保存为主。每一个成员有对应的分数(score)，分数可相同，通过分数来对成员进行排序。

以有序集合的特性，可以用来做订阅列表的存储

## 订阅功能

订阅的基本数据我存储在mysql中，redis用于缓存读取，当然直接用redis存储数据也是可以的。  

设计订阅表subscriber,以mysql为例 

|field|type|comment|
|---|---|---|
|uid|int|用户id|
|rUid|int|订阅目标id|
|createTime|int|创建时间|
|updateTime|int|修改时间|
|status|int|状态 枚举:`-1`删除`0`正常|

订阅表的基本功能需要满足：

* 通过`uid`查出所有已经订阅的`rUid`的信息，并按`createTime`排序
* 通过订阅目标`rUid`查出所有`uid`，并按`createTime`排序
* 查找是否已经订阅
* 取消订阅

## 添加订阅

按照基本功能的1和2，我们需要保持2个集合
一个集合以用户为中心:

```
以用户为中心的key统一一个前缀，后面加上用户的uid:
exampleKey(uid为1): subscriberUid1
```

一个集合以订阅目标为中心:

```
以订阅目录为中心的key统一一个前缀，后面加上目标用户的rUid:
exampleKey(rUid为100): subscriberRUid100
```
添加的命令是

```
	# 增加1条数据
	ZADD score member
	# 增加多条数据
	ZADD score1 member1 [score2 member2]...
```
为了以时间排序，我们可以以时间戳为分数，保存订阅信息

```
	127.0.0.1:6379>ZADD subscriberUid1 1453129638 100
	127.0.0.1:6379>ZADD subscriberRUid100 1453129638 1

	127.0.0.1:6379> ZADD subscriberUid1 1453129639 200 1453129640 300 1453129640 400 1453129641 500	1453129638 10000
	127.0.0.1:6379> ZADD subscriberRUid100 1453129639 2 1453129640 3 1453129640 4 1453129641 5 1453129611 6
```

## 获取订阅列表

获取集合的命令是

```
	# 按分数正序排列 withscore 是指附带分数
	ZRANGE key start stop [WITHSCORE]
	# 按分数倒序排列
	ZREVRANGE key start stop [WITHSCORE]
```

我们现在来获取用户1的订阅列表：

```
   # 正序获取，从0开始，-1指取到尽头，相当于limit
   127.0.0.1:6379> ZRANGE subscriberUid1 0 -1 WITHSCORES
   # 结果如下
	 1) "100"
	 2) "1453129638"
	 3) "10000"
	 4) "1453129638"
	 5) "200"
	 6) "1453129639"
	 7) "300"
	 8) "1453129640"
	 9) "400"
	10) "1453129640"
	11) "500"
	12) "1453129641"
```

这正是对应了分数的顺序，接下来试试倒序，并隐藏分数输出:

```
	# 倒序获取，从0开始，-1指取到尽头
   127.0.0.1:6379> ZREVRANGE subscriberUid1 0 -1
   # 结果如下
	1) "500"
	2) "400"
	3) "300"
	4) "200"
	5) "10000"
	6) "100"
```

## 查找是否已订阅

查找可以用一个方法来确认，查找对应成员的分数：

```
	ZSCORE key member
```

```
127.0.0.1:6379> ZSCORE subscriberUid1 100
"1453129638"
127.0.0.1:6379> ZSCORE subscriberUid1 1000
(nil)
```

每个成员我们都会分配对应的分数，通过获取分数，判断是否有分数，我们就可以判断这成员是否在集合中。

## 取消订阅

删除成员操作

```
	# 删除1个成员
	ZREM key member
	# 删除1个或多个成员
	ZREM key member [member ...]
```
因为我们的订阅数据需要同时维护2个集合，所以我们的删除是同时删除2个集合中的对应数据：

```
	# 删除用户为中心的集合
	127.0.0.1:6379> ZREM subscriberUid1 100
	(integer) 1
	127.0.0.1:6379> ZRANGE subscriberUid1 0 -1
	1) "10000"
	2) "200"
	3) "300"
	4) "400"
	5) "500"

	# 删除订阅目录为中心的集合
	127.0.0.1:6379>  ZREM subscriberRUid100 1
	(integer) 1
	127.0.0.1:6379>  ZRANGE subscriberRUid100 0 -1
	1) "6"
	2) "2"
	3) "3"
	4) "4"
	5) "5"
```

到此，我们的订阅功能就完成了。