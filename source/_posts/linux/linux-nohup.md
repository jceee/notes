title: linux-nohup
date: 2016-07-02 17:33:25
tags: linux
categories: linux

---

>nohup(no hang up) 即不挂机，在关闭终端或退出账户时，仍不关闭进程

## 功能

nohup 命令运行由 Command参数和任何相关的 Arg参数指定的命令，忽略所有挂断（SIGHUP）信号。在注销后使用 nohup 命令运行后台中的程序。要运行后台中的 nohup 命令，添加 & （ 表示“and”的符号）到命令的尾部。
如果不将 nohup 命令的输出重定向，输出将附加到当前目录的 `nohup.out` 文件中。如果当前目录的 nohup.out 文件不可写，输出重定向到 `$HOME/nohup.out` 文件中。如果没有文件能创建或打开以用于追加，那么 Command 参数指定的命令不可调用。如果标准错误是一个终端，那么把指定的命令写给标准错误的所有输出作为标准输出重定向到相同的文件描述符。

## 语法 

```
	$ nohup Command [ Arg ... ] [　& ]
```

example:

```
	$ nohup php test.php &
	$ nohup php test.php >~/outFile.out 2>&1 &
```

* 0 – stdin (standard input)
* 1 – stdout (standard output)
* 2 – stderr (standard error)  

2>&1是将标准错误（2）重定向到标准输出（&1），标准输出（&1）再被重定向输入到myout.file文件中。

## 查看任务列表

用`jobs`可以看到正在运行的任务

```
	$ jobs                                                                                                                   
[1]  + running    nohup php test.php > ~/outFile.out 2>&1
```

## 关闭任务

n为任务号

```
	$ fg %n
```



