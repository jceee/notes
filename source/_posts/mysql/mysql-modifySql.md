title: mysql修改表结构语句
date: 2015-09-24 23:02
categories: mysql
tags: mysql

---

## 首先，创建一个测试表 tableA

```
	CREATE TABLE `tableA` (
	 `createTime` int(10) unsigned NOT NULL default 0 COMMENT 'createTime'
	) ENGINE=MyISAM AUTO_INCREMENT=4 DEFAULT CHARSET=utf8;
```

## 增加字段

格式：

```
	ALTER TABLE tableName ADD newField TYPE; 
```
  
增加主键粟子:

```
	ALTER TABLE tableA ADD id INT(10) UNSIGNED NOT NULL AUTO_INCREMENT  COMMENT 'id' ,ADD PRIMARY KEY(id);
```

增加元素粟子:

```
	ALTER TABLE tableA ADD nameStr VARCHAR(255) NOT NULL DEFAULT 'default' COMMENT 'name'
```

执行结果

```
	CREATE TABLE `tableA` (
	  `createTime` int(10) unsigned NOT NULL DEFAULT '0' COMMENT 'tmp',
	  `id` int(10) unsigned NOT NULL AUTO_INCREMENT COMMENT 'id',
	  `nameStr` varchar(255) NOT NULL DEFAULT 'default' COMMENT 'name',
	  PRIMARY KEY (`id`)
	) ENGINE=MyISAM AUTO_INCREMENT=4 DEFAULT CHARSET=utf8;
```


## 修改字段

格式：

```
	ALTER TABLE tableName CHANGE oldField newField TYPE; 
```

粟子：

```
	ALTER TABLE tableA CHANGE nameStr newNameStr VARCHAR(255) NOT NULL DEFAULT 'default' COMMENT 'name'
```

执行结果：

```
	CREATE TABLE `tableA` (
	  `createTime` int(10) unsigned NOT NULL DEFAULT '0' COMMENT 'tmp',
	  `id` int(10) unsigned NOT NULL AUTO_INCREMENT COMMENT 'id',
	  `newNameStr` varchar(255) NOT NULL DEFAULT 'default' COMMENT 'name',
	  PRIMARY KEY (`id`)
	) ENGINE=MyISAM AUTO_INCREMENT=4 DEFAULT CHARSET=utf8;
```

## 删除字段

格式：

```
	ALTER TABLE tableName DROP COLUMN removeField;  
```

粟子：

```
	ALTER TABLE tableA DROP COLUMN createTime;
```

执行结果：

```
	CREATE TABLE `tableA` (
	  `id` int(10) unsigned NOT NULL AUTO_INCREMENT COMMENT 'id',
	  `newNameStr` varchar(255) NOT NULL DEFAULT 'default' COMMENT 'name',
	  PRIMARY KEY (`id`)
	) ENGINE=MyISAM AUTO_INCREMENT=4 DEFAULT CHARSET=utf8;
```

## 创建索引

格式：

```
	CREATE INDEX KeyName ON tableName(fieldOneName,fieldTwoName);
```

粟子：

```
	CREATE INDEX `newNameStr` ON `tableA`(newNameStr);
```
   
执行结果：

```
	CREATE TABLE `tableA` (
	  `id` int(10) unsigned NOT NULL AUTO_INCREMENT COMMENT 'id',
	  `newNameStr` varchar(255) NOT NULL DEFAULT 'default' COMMENT 'name',
	  PRIMARY KEY (`id`),
	  KEY `newNameStr` (`newNameStr`)
	) ENGINE=MyISAM AUTO_INCREMENT=4 DEFAULT CHARSET=utf8;
```

## 删除索引

格式：

```
	DROP INDEX KeyName ON tableName;
```

粟子：

```
	DROP INDEX `newNameStr` ON `tableA`;
```
   
执行结果：

```
	CREATE TABLE `tableA` (
	  `id` int(10) unsigned NOT NULL AUTO_INCREMENT COMMENT 'id',
	  `newNameStr` varchar(255) NOT NULL DEFAULT 'default' COMMENT 'name',
	  PRIMARY KEY (`id`)
	) ENGINE=MyISAM AUTO_INCREMENT=4 DEFAULT CHARSET=utf8;
```