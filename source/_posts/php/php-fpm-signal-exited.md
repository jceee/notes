title: PHP-FPM 子进程不断重启exited on signal 11   
date: 2022-03-16 20:07:01  
categories: php   
tags: php

---

# 现象

某台服务器请求状态码出现大量502，看所有php-fpm master进程不断重启

# 定位排查


## 检查发版

检查对应时间是否有发版，检查了之后确实与发版时间吻合，但检查代码没看出问题，但多台服务器一起发布，只有一台出现问题，跟代码有关的概率很低，可以先排除代码问题

## 看php-fpm 错误日志

首先从配置文件找到错误日志路径：

```
$ cat /usr/local/php/etc/php-fpm.conf | grep error_log

error_log = /data1/logs/php-fpm.log
```

从错误日志中可以看到子进程反复通过`signal 11 (SIGSEGV)`信号退出

```
WARNING: [pool www] child 25406 exited on signal 11 (SIGSEGV) after 47.465595 seconds from start
.......
```


## 追踪进程

查了`SIGSEGV`定义：

>1. SIGSEGV是在访问内存时发生的错误，它属于内存管理的范畴
>2. SIGSEGV是一个用户态的概念，是操作系统在用户态程序错误访问内存时所做出的处理。
> 3. 当用户态程序访问（访问表示读、写或执行）不允许访问的内存时，产生SIGSEGV。 
> 4. 当用户态程序以错误的方式访问允许访问的内存时，产生SIGSEGV

也就是进程访问到异常内存导致异常退出，这时我们来追踪进程执行遇到什么问题


### strace

我们通过strace追踪某个子进程，看结束前是跑了什么逻辑，这里排除master进程，head出一个子进程来追踪，因为子进程重启很快，不一定能捕获到，要多跑几次

```
ps aux | grep php-fpm | grep -v master |head -1 | awk '{print " -p " $2" -s 10000 -o /tmp/strace_"$2".log"}'|xargs sudo strace
```

看strace日志：

```
....
stat("xxx.php", {st_mode=S_IFREG|0644, st_size=142755, ...}) = 0
open("/data/eaccelerator_cache/1/5/eaccelerator-48746.27075141", O_RDONLY) = 4
flock(4, LOCK_SH)                       = 0
read(4, "EACCELER\..........
843496) = 843496
flock(4, LOCK_UN)                       = 0
close(4)                                = 0
stat("xxx.php", {st_mode=S_IFREG|0644, st_size=142755, ...}) = 0
--- SIGSEGV {si_signo=SIGSEGV, si_code=SEGV_MAPERR, si_addr=0xd21f4a20c8} 
rt_sigaction(SIGSEGV, {sa_handler=SIG_DFL, sa_mask=[SEGV], sa_flags=SA_RESTORER|SA_RESTART, sa_restorer=0x7ff5c002c400}, {sa_handler=0x7ff5b59caa30, sa_mask=[SEGV], sa_flags=SA_RESTORER|SA_RESTART, sa_restorer=0x7ff5c002c400}, 8) = 0
kill(300, SIGSEGV)                      = 0
rt_sigreturn({mask=[]})                 = 140693241590544
--- SIGSEGV {si_signo=SIGSEGV, si_code=SI_USER, si_pid=300, si_uid=1007} ---
+++ killed by SIGSEGV +++
```

看到发生`SIGSEGV`之前是执行了xxx.php，再之前有个动作是加载了编译缓存`open("/data/eaccelerator_cache/1/5/eaccelerator-48746.27075141", O_RDONLY)`，代码每台机都一样，可能是编译缓存出现问题，我们把缓存删除试试，502消失了，确实是缓存问题

# 分析

原因：根据触发的时间点与发版吻合，猜测有可能是服务器请求量大，在热更的时候编译缓存生成出现问题，导致进程异常，偶然现象
优化：把服务容器化，发版时断开流量，更新完代码重启容器，重新生成缓存
