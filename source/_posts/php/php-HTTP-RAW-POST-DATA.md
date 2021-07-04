title: $GLOBALS ["HTTP_RAW_POST_DATA"]为空
date: 2015-07-29 22:28
categories: php
tags: php

---

## `$GLOBALS["HTTP_RAW_POST_DATA"]` `$_POST` `file_get_contents('php://input')`    

`$GLOBALS["HTTP_RAW_POST_DATA"]`跟`$_POST`,`file_get_contents('php://input')`的效果差不多，用 `$GLOBALS ["HTTP_RAW_POST_DATA"]`或`file_get_contents('php://input')`的情况大多是为了获取`$_POST`无法接收的数据类型（如XML数据） 

## 获取`$GLOBALS ["HTTP_RAW_POST_DATA"]`的值

在`$GLOBALS ["HTTP_RAW_POST_DATA"]`取不到值的情况下可以按以下方式排查：

* 用`file_get_contents('php://input')`获取数据。如果获取不到，则可能是数据传输错误，对请求进行捉包，分析数据。

* 如果`file_get_contents('php://input')`有数据。那有可能是配置没开启，查看php.ini配置文件，找到always_populate_raw_post_data并设置为On:
   
```
	always_populate_raw_post_data = On
```