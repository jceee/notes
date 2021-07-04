title: distinct 排重查询
date: 2015-1-11 21:06:35
categories: mysql
tags: [mysql,distinct]

---

>distinct 的作用是根据某字段或某些字段去除重复数据  


 example,student表结构数据如下    

id | class | name   
--- | --- | ---
1 | 1 | jacky
2 | 1 | jacky
3 | 1 | tom
4 | 2 | alen
5 | 2 | jacky
6 | 4 | jacky


# 查询  

## 查询粟子1:单个排重

```
select distinct name from student
```
搜索结果：

| name |   
| --- |
| jacky |
| tom |
| alen |

对name进行去重，但搜索结果只有name这个字段

## 查询粟子2：组合排重

```
select distinct name, class from student

```
则是对 (name, class) 组合起来去重。搜索结果是：

| name | class |
| --- | --- |
| jackey |	1 |
| tom |	1 |
| alen	| 2 |
| jackey	| 2 |
| jackey	| 4 |

# 统计  

## 统计粟子1：单个排重统计

```
select count(distinct name) from student
```

结果：3 (去重后的name只有jackey,tom,alen)  
同理也可以进行组合去重统计

## 统计粟子2： 组合排重统计

```
select count(distinct name, class) from student

```

结果： 5

---






