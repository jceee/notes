title: PHP文件操作part1[文件路径]   
date: 2014-09-25 22:07  
categories: php   
tags: php

---

# 绝对路径与相对路径

>文件函数最常用的参数是什么？路径。  
而路径参数大致分两种：绝对路径和相对路径。

## 绝对路径 : 从盘开始的路径

example:

```
	/Users/jceee/code/app/www/index.php（app项目中的index.php文件）
```

## 相对路径 : 以当前文件为基准所组成的路径

example:

```
	./index.php(相对当前目录)
	../index.php(相对上级目录)
```


## 获取绝对路径

### realpath

php中有通过相对路径获得绝对路径的方法`realpath($path)`

```
var_dump(realpath('index.php'));
//"/Users/jceee/code/app/www/index.php"
```

### Magic constants: `__FILE__`

`__FILE__`可以获得当前文件的绝对路径

```
	var_dump(__FILE__);
	//"/Users/jceee/code/app/www/index.php"
```
### dirname

`dirname(path)`获取的是传入路径所在的目录,传入相对路径返回的也会是相对路径

```
	var_dump(dirname('./index.php'));
	//"."
	var_dump(dirname(realpath('./index.php')));
	//"/Users/jceee/code/app/www/"
```
### getcwd()

`getcwd`获取的是当前的工作目录,也就是index.php所在的目录。相当于`dirname(__FILE__)`

```
	var_dump(getcwd());
	//"/Users/jceee/code/app/www/"
```

### `$_SERVER['DOCUMENT_ROOT']`

`$_SERVER['DOCUMENT_ROOT']` 获取的是项目在服务器定义的根目录

```
	var_dump($_SERVER['DOCUMENT_ROOT']);
	//"/Users/jceee/code/app"
```

