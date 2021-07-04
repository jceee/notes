title: mysql 字符串函数  
date: 2016-05-22 22:58:20  
categories: mysql  
tags: mysql  

---

# test table

使用一个测试表：

```
mysql> select * from news ;
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

# concat

使用方法：

```
	CONCAT(str1,str2,...)
```

返回连接参数产生的字符串，如果任何一个参数为null，则返回值为NULL.

EXAMPLE:

```
mysql> select concat(id, '-', title, '-', body) from news ;
+-----------------------------------+
| concat(id, '-', title, '-', body) |
+-----------------------------------+
| NULL                              |
| 2-title2-body2                    |
| 12-title1-body3                   |
| 13-title1-body4                   |
| NULL                              |
+-----------------------------------+
```

# concat_ws

使用方法：

```
	CONCAT_WS(separator,str1,str2,...)
```

concat_ws代表concat with separator通过分隔符separator将其他参数链接起来.当分隔符为NULL时，返回值为空；而当某个参数为空时，则跳过

EXAMPLE:

```
mysql> select concat_ws('-', title, body) from news ;
+-----------------------------+
| concat_ws('-', title, body) |
+-----------------------------+
| title1                      |
| title2-body2                |
| title1-body3                |
| title1-body4                |
| body5                       |
+-----------------------------+
```

# group_concat

使用方法

```
	GROUP_CONCAT([DISTINCT] Field [ORDER BY ASC/DESC SortField] [separator Separator])
```

group_concat是把结果数据按字段Field连接起来，默认分隔符是`,`,可选排重[DISTINCT],可选排序[ORDER BY ASC/DESC SortField]按SortField字段排序,可选分隔符[separtor Separator]按Separator分隔。

EXAMPLE:

```
mysql> select group_concat(title) from news ;
+-----------------------------+
| group_concat(title)         |
+-----------------------------+
| title1,title2,title1,title1 |
+-----------------------------+
```

```
mysql> select group_concat(distinct title order by id desc separator ';') from news ;
+-------------------------------------------------------------+
| group_concat(distinct title order by id desc separator ';') |
+-------------------------------------------------------------+
| title2;title1                                               |
+-------------------------------------------------------------+
```

```
mysql> select title,group_concat(distinct body order by id desc separator ';') from news group by title;
+--------+------------------------------------------------------------+
| title  | group_concat(distinct body order by id desc separator ';') |
+--------+------------------------------------------------------------+
| NULL   | body5                                                      |
| title1 | body4;body3                                                |
| title2 | body2                                                      |
+--------+------------------------------------------------------------+
```

# repeat

使用方法

```
repeat(str, repeatTime)
```

将str循环repeatTime次

EXAMPLE

```
mysql> select repeat('test', 4);
+-------------------+
| repeat('test', 4) |
+-------------------+
| testtesttesttest  |
+-------------------+
```

```
mysql> select repeat(body, 4) from news;
+----------------------+
| repeat(body, 4)      |
+----------------------+
| NULL                 |
| body2body2body2body2 |
| body3body3body3body3 |
| body4body4body4body4 |
| body5body5body5body5 |
+----------------------+
```