title: mysql abs func #BIGINT UNSIGNED value is out of range  
date: 2021-06-10 23:15:47  
categories: mysql  
tags: mysql  

---

>ABS(*number*)
>
> Return the absolute value of a number



## 基本用法

返回数字参数的绝对值

```
SELECT ABS(-243.5);     ===> 243.5
```



## 无符号数字相减计算异常问题



例子：创建用户表，年龄字段为非负

```
CREATE TABLE `user` (
  `uid` int(11) unsigned NOT NULL AUTO_INCREMENT COMMENT 'uid',
  `age` int(10) unsigned NOT NULL COMMENT '年龄',
  `name` varchar(50) NOT NULL DEFAULT '' COMMENT '用户名',
  PRIMARY KEY (`uid`)
) ENGINE=InnoDB AUTO_INCREMENT=1 DEFAULT CHARSET=utf8mb4 COMMENT '用户表';
```

写入几条测试数据

```
INSERT INTO `user` ( `age`, `name`)
VALUES
	( 11, '张小明'),
	( 26, '林小红'),
	( 21, '李小狼'),
	( 18, '陈小洋');
```

当我们想找出年龄最接近25岁的用户时，会出现Mysql错误：

```
select *,ABS(age - 25) as age_sort from user order by age_sort asc limit 1;
===>BIGINT UNSIGNED value is out of range in '(`user`.`age` - 25)'
```



解决方法：使用cast把无符号age转为带符号的number

```
select *,ABS(CAST(age AS SIGNED) - 25) as age_sort 
from `user` order by age_sort asc limit 1;
```





