title: linux-man
date: 2016-02-13 22:18:58
tags: linux    
categories: linux  

---

>man是manual的缩写，是系统中提供相关条目参考信息的命令。


如果需要参考命令的用法，只需要在man后加上命令名就可以得到详细说明,如

```
	$ man ls
```

会得到下面结果：

```
LS(1)                     BSD General Commands Manual                    LS(1)

NAME
     ls -- list directory contents

SYNOPSIS
     ls [-ABCFGHLOPRSTUW@abcdefghiklmnopqrstuwx1] [file ...]

DESCRIPTION
     For each operand that names a file of a type other than directory, ls displays its name as well as any requested, associated information.  For each operand that names a file of type directory, ls displays the names of files con-
     tained within that directory, as well as any requested, associated information.

     If no operands are given, the contents of the current directory are displayed.  If more than one operand is given, non-directory operands are displayed first; directory and non-directory operands are sorted separately and in lexi-
     cographical order.

     The following options are available:

     -@      Display extended attribute keys and sizes in long (-l) output.

     -1      (The numeric digit ``one''.)  Force output to be one entry per line.  This is the default when output is not to a terminal.

     -A      List all entries except for . and ...  Always set for the super-user.
     
     ...
```

我们可以看到结果中显示了LS(1),(1)代表的是`一般用户可使用的命令`。在查询数据后面的数字是有意义的，它可以帮助我们了解或者直接查询相关的资料。每个数字具体含义如下：

|数字|意义|
|---|---|
|1| 用户在shell环境中可以操作的命令或可执行文件|
|2| 系统内核可调用的函数与工具等|
|3| 一些常用的函数（function）与函数库（library），大部分为C的函数库（libc）|
|4| 设备文件的说明，通常是在/dev下的文件|
|5| 配置文件或者是某些文件的格式|
|6| 游戏（games）|
|7| 惯例与协议等，例如Linux文件系统、网络协议、ASCII code等说明|
|8| 系统管理员可用的管理命令|
|9| 跟kernel有关的文件|

如

```
	$ man 2 write
```

```
WRITE(2)                    BSD System Calls Manual                   WRITE(2)

NAME
     pwrite, write, writev -- write output

LIBRARY
     Standard C Library (libc, -lc)

SYNOPSIS
     #include <unistd.h>

     ssize_t
     pwrite(int fildes, const void *buf, size_t nbyte, off_t offset);

     ssize_t
     write(int fildes, const void *buf, size_t nbyte);

     #include <sys/uio.h>

     ssize_t
     writev(int fildes, const struct iovec *iov, int iovcnt);

```

