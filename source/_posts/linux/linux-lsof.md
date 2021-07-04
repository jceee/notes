title: linux-lsof  
date: 2016-02-04 22:18:46  
tags: linux    
categories: linux  

---

>lsof（list open files）是一个列出当前系统打开文件的工具。在linux环境下，任何事物都以文件的形式存在，通过文件不仅仅可以访问常规数据，还可以访问网络连接和硬件。

## lsof显示示例

example:以查看mongod进程为例子

```
	$ lsof /usr/local/bin/mongod
```

结果：

```
	COMMAND   PID       USER   FD   TYPE DEVICE SIZE/OFF     NODE NAME  
	mongod  50995 jceee  txt    REG    1,4 27409336 17133981 /usr/local/bin/../Cellar/mongodb/3.0.7/bin/mongod
```

|COMMAND |PID |USER| FD |TYPE |DEVICE |SIZE |NODE| NAME|
|---|---|---|---|---|---|---|---|---|
|进程名称|进程标识符|进程所有者|文件描述符|文件类型|指定磁盘名|文件大小|索引节点|打开文件的确切名称|
|	mongod|  50995| jceee|  txt|    REG|    1,4 |27409336| 17133981| /usr/local/bin/../Cellar/mongodb/3.0.7/bin/mongod|

## lsof参数

|命令|功能|
|---|---|
|lsof filename |显示打开指定文件的所有进程|
|lsof -a| 表示两个参数都必须满足时才显示结果|
|lsof -c string| 显示COMMAND列中包含指定字符的进程所有打开的文件|
|lsof -u username |显示所属user进程打开的文件|
|lsof -g gid |显示归属gid的进程情况|
|lsof +d /DIR/| 显示目录下被进程打开的文件|
|lsof +D /DIR/| 同上，但是会搜索目录下的所有目录，时间相对较长|
|lsof -d FD |显示指定文件描述符的进程
|lsof -n |不将IP转换为hostname，缺省是不加上-n参数
|lsof -i |用以显示符合条件的进程情况
|lsof -i[46] [protocol][@hostname / hostaddr][:service / port]|参数功能在下面|
||46 --> IPv4 or IPv6|
||protocol --> TCP or UDP|
||hostname --> Internet host name|
||hostaddr --> IPv4地址|
||service --> /etc/service中的 service name (可以不只一个)|
||port --> 端口号 (可以不只一个)|
