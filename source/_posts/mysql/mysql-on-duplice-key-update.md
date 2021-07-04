title: mysql[ON DUPLICE KEY UPDATE]  
date: 2016-06-10 17:37:51  
categories: mysql
tags: mysql

---

## ON DUPLICE KEY UPDATE

[ON DUPLICE KEY UPDATE] 用于INSERT语句后面，作用是在执行INSERT时，若UNIQUE索引或者PRIMARY KEY出现重复时，则在出现重复值的行执行UPDATE，否则按正常INSERT语句执行.

## 测试表

表结构：

```
CREATE TABLE `news` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `title` varchar(70) COLLATE utf8_unicode_ci DEFAULT NULL,
  `body` longtext COLLATE utf8_unicode_ci,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COLLATE=utf8_unicode_ci;
```

其中`id`为PRIMARY KEY
表数据：

```
	mysql> SELECT * FROM news;
	+----+--------+-------+
	| id | title  | body  |
	+----+--------+-------+
	|  1 | title1 | NULL  |
	|  2 | title2 | body2 |
	| 12 | title1 | body3 |
	| 13 | title1 | body4 |
	| 14 | NULL   | body5 |
	+----+--------+-------+
```

## 插入一条重复数据

```
mysql> INSERT INTO `news` (`id`,`title`,`body`) VALUES ('1','titleTest','bodyTest') ON DUPLICATE KEY UPDATE `title`= VALUES(`title`),`body`=VALUES(`body`);
Query OK, 2 rows affected (0.00 sec)
```

show data:

```
	mysql> SELECT * FROM news;
	+----+-----------+----------+
	| id | title     | body     |
	+----+-----------+----------+
	|  1 | titleTest | bodyTest |
	|  2 | title2    | body2    |
	| 12 | title1    | body3    |
	| 13 | title1    | body4    |
	| 14 | NULL      | body5    |
	+----+-----------+----------+
```

## 插入多条重复数据

```
mysql> INSERT INTO `news` (`id`,`title`,`body`) VALUES ('1','titleTest','bodyTest'),('2','titleTest2','bodyTest2'),('3','titleTest3','bodyTest3') ON DUPLICATE KEY UPDATE `title`= VALUES(`title`),`body`=VALUES(`body`);
Query OK, 3 rows affected (0.00 sec)
Records: 3  Duplicates: 1  Warnings: 0
```

show data:

```
	mysql> SELECT * FROM news;                                                                                                                                                           +----+------------+-----------+
	| id | title      | body      |
	+----+------------+-----------+
	|  1 | titleTest  | bodyTest  |
	|  2 | titleTest2 | bodyTest2 |
	|  3 | titleTest3 | bodyTest3 |
	| 12 | title1     | body3     |
	| 13 | title1     | body4     |
	| 14 | NULL       | body5     |
	+----+------------+-----------+
	6 rows in set (0.00 sec)
```

因为id3不存在，所以正常INSERT了1条数据，UPDATE了2条数据