title: 使用mysqlslap回放Mysql流量
date: 2022-04-09 17:40
categories: mysql
tags: mysql

---


# 背景

## mysqlslap简介

`mysqlslap` 是 MySQL 从 5.1.4 版开始官方自带的一个压力测试工具。

功能：它通过模拟多个并发客户端访问 MySQL 来执行压力测试，我们通过结果信息可以了解数据库的性能状况。

## 适用场景

* 优化了操作系统的内核参数后，是否提升了性能
* 优化了Mysql参数，性能变化
* 服务器的硬件资源能够支持多大的访问压力

# 压测流程

## 流量录制

打开原Mysql服务的`general log`，录制一定时间的流量

注意：
* `general log`的开启会增加Mysql的io压力，造成一定的性能下降，请选择适当的时间，且注意观察服务器压力

## 回放文件梳理

### 筛选出SQL部分的内容

回放需要的文件是一行一个sql，而`general log`里我们只需要Query部分的内容，如：

```
220408 14:35:13 1150499963 Query        UPDATE `m_user` SET `status` = '1' WHERE uid = 3
                1781571137 Quit
                1781571146 Connect      root@127.0.0.1 on db_test
                1781571120 Init DB      db_test
                
```

### 去掉换行、注释等字符

筛选完Query，还可能有换行的SQL，或带注释的SQL，这种需要拼接回一行，并去掉注释

```
1792900537 Query        SELECT uid,user_name  --field注释部分
                        FROM m_user
                        WHERE uid = 2 
                        LIMIT 1
```

### 补充库名

如果测试的实例有多个库，会更加麻烦一些，需要在梳理的时候可以根据表名补充回sql

## 流量回放

### 命令与参数

我们最终得到一个sql文件，接着我们就可以开始压测了

具体命令如下：
```
sudo /usr/local/mysql/bin/mysqlslap --no-defaults -h127.0.0.1 -P3306 -utest_mysqlslap -p'123456'  --concurrency=600 --iterations=2 --create-schema=test_db --query=/data/general_out.log --number-of-queries=80000000 -T
```

参数含义

* --no-defaults   必须加在参数第一位，不然会报`unknown variable 'default-character-set=utf8`
* -h 待测试实例host
* -P 待测试实例端口
* -u 待测试实例账号
* -p 待测试实例密码
* --concurrency 并发线程数
* --iterations 重复次数，按多次计算平均数
* --create-schema  指定测试的库
* --query 可以是sql语句，也可以是个sql文件，我们这里用的是文件模式
* --number-of-queries 请求次数，所有sql请求的次数，比如sql文件里有200个sql，`number-of-queries`指定100时，会只执行到一半就停止，指定300时，会执行完200个再重新跑100

### 结果

```
Benchmark
        Average number of seconds to run all queries: 672.956 seconds
        Minimum number of seconds to run all queries: 672.341 seconds
        Maximum number of seconds to run all queries: 673.571 seconds
        Number of clients running queries: 600
        Average number of queries per client: 133333


User time 1503.60, System time 3036.06
Maximum resident set size 9077056, Integral resident set size 0
Non-physical pagefaults 4343854, Physical pagefaults 19, Swaps 0
Blocks in 4262400 out 8, Messages in 0 out 0, Signals 0
Voluntary context switches 161188899, Involuntary context switches 5020
```

从结果看，执行80000000 个sql的平均时间是672.956秒，得出qps差不多119047.6，具体的硬件cpu、io、负载，可以看对应实例的监控

### 并发写锁

因为我们设置了600个`concurrency`并发数，所以一些原本串行更新可能变为并发更新，导致产生并发锁：

```
/usr/local/mysql/bin/mysqlslap: Cannot run query UPDATE `m_user` SET `status`=1 WHERE `uid`=235 ERROR : Lock wait timeout exceeded; try restarting transaction
```

这时可以按sql类型拆分sql文件，把`select`和`update`、`insert`、`delete`分出不同文件。通过开多个mysqlslap进程来压测
