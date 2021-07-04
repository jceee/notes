title: linux-find
date: 2016-07-04 15:02:12
tags: linux
categories: linux

---

>find是linux 命令，它将档案系统内符合 expression 的档案列出来。你可以指要档案的名称、类别、时间、大小、权限等不同资讯的组合，只有完全相符的才会被列出来。

## 语法

```
	$ find path -option [ -print ] [ -exec -ok command ] {} \;
```

下边列举常用的一些条件

## path

查找单个目录：

```
	$ find /tmp   
```

查找多个目录:

```
	$ find /tmp /opt /etc
```

## option

### 按档案名

查找后缀为`.md` 的档案

```
	$ find path -name "*.md"
```

查找后缀为`.md`的档案，并不区分大小写(可以是`.MD`,`.Md`,`.mD`)

```
	$ find path -iname "*.md"
```

### 按档案类型

文件夹

```
	$ find path -type d
```

符号连接

```
	$ find path -type l
```

一般档案

```
	$ find path -type f
```

字形装置

```
	$ find path -type c
```

区块装置

```
	$ find path -type b
```

具名贮列

```
	$ find path -type p
```

socket

```
	$ find path -type s
```

### 时间

类型

* a (access) 访问时间
* c (change) 状态改变时间(如修改,chmod,chown)
* m (modification) 修改时间

单位

* min 以分钟为单位  
* time 以天为单位  

量

* -n  在n单位时间内  
* +n  在n单位时间前  
* n   在n单位时间中  


在n分钟前被访问|修改|改变过的档案

```
	$ find path -amin +n
	$ find path -mmin +n
	$ find path -cmin +n
```

在n分钟内被访问|修改|改变过的档案

```
	$ find path -amin -n
	$ find path -mmin -n
	$ find path -cmin -n
```

在前n分钟被访问|修改|改变过的档案

```
	$ find path -amin n
	$ find path -mmin n
	$ find path -cmin n
```


最近[n天内|n天前|前n天]被访问|修改|改变过的档案

```
	$ find path -atime [-n|+n|n]
	$ find path -mtime [-n|+n|n]
	$ find path -ctime [-n|+n|n]
```

比file档案更晚被访问|修改|改变过的档案

```
	$ find path -anewer file
	$ find path -mnewer file
	$ find path -cnewer file
```

### 路径

路径中含有path

```
	$ find path -path "*path*"
```

路径含有path并不区分大小写

```
	$ find path -ipath "*PATH*"
```

### 大小

查找大于nk,小于nk的档案

```
	$ find path -size +nk
	$ find path -size -nk
```

空档案

```
	$ find path -empty
```

### 权限

mode的值 对应3种用户(所属用户，所属组，其他)的rwx权限 

* -mode 3种用户的权限都跟mode值对上，则成功（如644对444|644）
* +mode 3种用户的权限只要有一个对上，则成功（如644能对622等）
* mode 3种用户的权限都要跟mode准确对上才成功（如222对222）

```
	$ find path -perm -mode
	$ find path -perm +mode
	$ find path -perm mode
```

## exec

需要对搜索结果执行shell命令则用`-exec`,如

```
	$ find / -type f -exec ls -lah {} \;
```

## example

把小于2k的文件搬到/tmp目录

```
	$ find path -size -2k -exec mv {} /tmp \;
```

找出档案名含有'abc' 的普通档案

```
	$ find path -name '*abc*' -type f 
	$ find -type f | grep abc
```