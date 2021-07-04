title: 多条insert语句合并  
date: 2014-09-14 22:37  
categories: mysql  
tags: mysql  

---

表a君Article内容如下：

|key|value|
|---|---|
|id | int|
|name|string|
|title|strit|


现在，我们有一批数据需要插入表A,那么来了，按照我们普通的方式，排队1条1条来：

```
INSERT INTO `Article`(`name`,`title`)VALUES ('name1','title1');
INSERT INTO `Article`(`name`,`title`)VALUES ('name2','title2');
INSERT INTO `Article`(`name`,`title`)VALUES ('name3','title3');
INSERT INTO `Article`(`name`,`title`)VALUES ('name4','title4');
...
```

之后发现了sql的insert语句可以含多个参数:

```
INSERT INTO `Article`(`name`,`title`)
VALUES ('name1','title1'),
VALUES ('name2','title2'),
VALUES ('name3','title3'),
VALUES ('name4','title4'),
...;
```

这样一变，我们的多条语句变成了一个，好处是调用mysql的api次数减少了，开销降低，提高了性能和速度。

所以，在多条语句插入同个表时，我们尽量地用一条语句插入，提高性能，速度。

