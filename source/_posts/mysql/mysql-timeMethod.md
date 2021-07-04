title: mysql的时间转换函数
date: 2015-07-05 21:42
categories: mysql
tags: mysql

---

## 格式化时间戳FROM_UNIXTIME

`FROM_UNIXTIME(unix_timestamp, format)`第一个参数是时间戳格式。 第二个是最终想转换的格式，如

```
	SELECT FROM_UNIXTIME(1436102304,'%Y年%m月%d日') as date;
```

结果:
                
```
	date     :       2015年07月05日
```


## 字符串转换unix时间戳UNIX_TIMESTAMP

`UNIX_TIMESTAMP(date)` 是将时间转化为时间戳，如 

```
	SELECT UNIX_TIMESTAMP('2015-07-05');  
```

结果是：

```
	1436068800
```

示例：找出2015-05到2015-07 log表中的记录:

```
       SELECT id, FROM_UNIXTIME(time,'%Y-%m-%d') as date 
       FROM log 
       WHERE  time BETWEEN UNIX_TIMESTAMP('2015-05-01') AND UNIX_TIMESTAMP('2015-07-01');
```       