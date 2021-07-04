title: mysql case的用法  
date: 2016-01-14 23:15:47  
categories: mysql  
tags: mysql  

---

>mysql中的case是类似于if的功能，通过这功能我们的统计结果更灵活 

现在我们先建一个公司职员表来试一下：

|id|department|name|wages|sex|
|---|---|---|---|---|
|1|人事部|aimer|8500|1|
|2|人事部|jack|6000|2|
|3|销售部|tom|15000|2|
|4|销售部|tony|9000|2|
|5|人事部|supercell|8500|1|
|6|销售部|lisa|18000|1|

## 在查询结果中用case

sex字段中，1是女士，2是男士  
wages字段中，薪酬的级别分布是这样：  
t10: 5000 < wages <= 6500  
t9: 6500 < wages <= 8500  
t8: 8500 < wages <= 11000  
t7: 11000 < wages <= 14000  
t6: 14000 < wages <= 17500
t5: 17500 < wages <= 22000
t4: 22000 < wages <= 27000
  
如果我们要在查询结果中清析地表现出来可以这样：

```
	SELECT 
		department,name,
			CASE 
			WHEN 5000<wages AND wages<=6500 THEN 't10'
			WHEN 6500 < wages AND wages <= 8500 THEN 't9'
			WHEN 8500 < wages AND wages <= 11000 THEN 't8'
			WHEN 11000 < wages AND wages <= 14000 THEN 't7'
			WHEN 14000 < wages AND wages <= 17500 THEN 't6'
			WHEN 17500 < wages AND wages <= 22000 THEN 't5'
			WHEN 22000 < wages AND wages <= 27000 THEN 't6'
            ELSE '未定义级别'
            END as wages
			,
			CASE  
			WHEN sex = 1 THEN '女'
			WHEN sex = 2 THEN '男'
			ELSE '未知性别'
			END as sex
	FROM
		`cdb_member`
```
结果如下:

|department|name|wages|sex|
|---|---|---|---|
|人事部|aimer|t9	|女|
|人事部|jack|t10|	男|
|销售部|tom|	t6|	女|
|销售部|tony|t8	|女|
|人事部|supercell|t9|	男|
|销售部|lisa|t5	|男|

## 在统计结果中用case

如果我们要统计每个部门的男女分布情况，薪酬级别人数可以这么来写：

```
	SELECT 
		department,
		SUM(CASE WHEN sex = 1 THEN 1 ELSE 0 END) as 'man',
		SUM(CASE WHEN sex = 2 THEN 1 ELSE 0 END) as 'woman',
		SUM(CASE WHEN 5000 < wages AND wages <= 6500 THEN 1 ELSE 0 END ) AS 't10',
		SUM(CASE WHEN 6500 < wages AND wages <= 8500 THEN 1 ELSE 0 END ) AS 't9',
		SUM(CASE WHEN 8500 < wages AND wages <= 11000 THEN 1 ELSE 0 END ) AS 't8',
		SUM(CASE WHEN 11000 < wages AND wages <= 14000 THEN 1 ELSE 0 END ) AS 't7',
		SUM(CASE WHEN 14000 < wages AND wages <= 17500 THEN 1 ELSE 0 END ) AS 't6',
		SUM(CASE WHEN 17500 < wages AND wages <= 22000 THEN 1 ELSE 0 END ) AS 't5',
		SUM(CASE WHEN 22000 < wages AND wages <= 27000 THEN 1 ELSE 0 END ) AS 't4'
	FROM
		`cdb_member`
    GROUP BY 
    department
```

|department|man|woman|t10|t9|t8|t7|t6|t5|t4|
|---|---|---|---|---|---|---|---|---|---|
|人事部	|1|	2|	1|	2|	0|	0	|0|	0|	0|
|销售部	|2	|1|	0	|0|	1|	0	|1	|1|	0|
