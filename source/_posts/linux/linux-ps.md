title: linux-ps  
date: 2016-02-06 22:32:53  
tags: linux    
categories: linux  

---

>ps(Process Status)，顾名思义，是在Linux中是查看进程状态的命令。ps查看正处于Running的进程，ps aux查看所有的进程。


## ps进程状态码

ps几种主要的状态码是:

* D 不可中断 uninterruptible sleep (usually IO) 
* R 运行 runnable (on run queue) 
* S 睡眠 sleeping 
* T 停止 traced or stopped 
* Z 僵死 a defunct (”zombie”) process


## ps列名说明

|列名|说明|
|---|---|
|USER|该 process 属于那个使用者账号的|
|PID |该 process 的号码|
|%CPU|该 process 使用掉的 CPU 资源百分比|
|%MEM|该 process 所占用的物理内存百分比|
|VSZ |该 process 使用掉的虚拟内存量 (Kbytes)|
|RSS |该 process 占用的固定的内存量 (Kbytes)|
|TTY |该 process 是在那个终端机上面运作，若与终端机无关，则显示 ?，另外， tty1-tty6 是本机上面的登入者程序，若为 pts/0 等等的，则表示为由网络连接进主机的程序。|
|STAT|该程序目前的状态，主要的状态可以参照上面提到的|
|START|该 process 被触发启动的时间|
|TIME |该 process 实际使用 CPU 运作的时间|
|COMMAND|该程序的实际指令|

## px参数

ps常用参数

* -e 显示所有进程
* -A 所有进程均显示，与-e具有同样效果
* -a 显示当前终端上所有进程，包括其他用户的进程
* -u 以用户来筛选进程
* x 显示没有控制终端的进程
* r 显示正在运行的进程
* -f 全格式

还有更多参数可以参考

```
	$ man ps
```

## ps使用示例

### 查看特定用户进程:

`ps -u <用户名>`，如查看root用户的进程

```
	$ ps -u root
```
result:

```
	 UID   PID TTY           TIME CMD
     0     1 ??        28:43.94 /sbin/launchd
```


### 查看所有进程

```
 $ ps -e
```

result:

```
  PID TTY           TIME CMD
    1 ??        28:43.16 /sbin/launchd
```

### 查看全格式的全进程

```
	$ ps -ef
```
result:

```
  UID   PID  PPID   C STIME   TTY           TIME CMD
    0     1     0   0 24 116  ??        28:42.63 /sbin/launchd
```

### 查看当前所有进程cpu占用情况

```
	$ ps aux 
```
result:

```
USER      PID   %CPU %MEM      VSZ    RSS   TT  STAT STARTED      TIME COMMAND
jceee    51422   0.1  0.6  3367032 107344   ??  S    四11下午   1:36.91 /Applications/Robomongo.app/Contents/MacOS/Robomongo
```

### 根据关键字查找相关进程

`ps aux | grep <进程名>`

```
	$ ps aux | grep php-fpm
```

result:

```
jceee      53756   0.0  0.0  2493396   3460   ??  S     5:53下午   0:00.02 /usr/local/opt/php56/sbin/php-fpm --fpm-config /usr/local/etc/php/5.6/php-fpm.conf
```