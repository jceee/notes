title: 使用mysql中间件：mysql proxy  
date: 2021-12-20 22:15:47  
categories: mysql  
tags: mysql  

---

>mysql-proxy是mysql官方提供的mysql中间件服务，上游可接入若干个mysql-client，后端可连接若干个mysql-server，它使用mysql协议，任何连接mysql的上游无需任何更改即可迁移至mysql-proxy上。



## 安装

前往[mysql官网](https://downloads.mysql.com/archives/proxy/)，选择对应的版本，如我选择的是Linux平台的文件，下载到服务器：

```
//解压
tar -zxf mysql-proxy-0.8.5-linux-el6-x86-64bit.tar.gz
//移动到mysql_proxy
sudo mv -f mysql_proxy/mysql-proxy-0.8.5-linux-el6-x86-64bit/* ./mysql_proxy/
//查看版本
./mysql_proxy/bin/mysql-proxy -V
```

可以看到版本，依赖的glib，libevent，lua版本等：

```
mysql-proxy 0.8.5
  chassis: 0.8.5
  glib2: 2.16.6
  libevent: 2.0.21-stable
  LUA: Lua 5.1.4
    package.path: /data1/mysqlproxy/lib/mysql-proxy/lua/?.lua
    package.cpath: /data1/mysqlproxy/lib/mysql-proxy/lua/?.so
-- modules
  proxy: 0.8.5
```

## 简单启动

mysql-proxy启动至少需要指定一个后端mysql的ip和端口号，此时带上--proxy-backend-addresses参数即可：

```
./mysql_proxy/bin/mysql-proxy --proxy-backend-addresses=127.0.0.1:3306
```

## 配置文件启动

### 配置项

我们可以配置`./mysql_proxy/rw-splitting.lua`, 来设置最小最大空闲连接


```
if not proxy.global.config.rwsplit then
    proxy.global.config.rwsplit = {
            min_idle_connections = 10,
            max_idle_connections = 20,
            is_debug = false
    }
end
```

接着配置我们使用的mysql server:
`vi ./mysql_proxy/conf/proxy_4000.conf`

```
[mysql-proxy]
proxy-address = 127.0.0.1:4000
proxy-backend-addresses = 127.0.0.1:3306  
proxy-read-only-backend-addresses = 127.0.0.1:3306
log-file = /data1/mysqlproxy/logs/mysql-proxy.log
proxy-lua-script=/data1/mysqlproxy/rw-splitting.lua
log-level = info
daemon = 1
keepalive= 1
```

|配置项|说明|
|---|---|
|proxy-address|提供给外部调用的ip和端口|
|proxy-backend-addresses|后端使用的mysql节点，必填|
|proxy-read-only-backend-addresses|后端使用的mysql只读节点，需要读写分离时填|
|log-file|日志存储地址|
|proxy-lua-script|lua配置脚本|
|log-level|日志级别|
|daemon|后台运行|
|keepalive|常驻|

### 启动

```
sudo ./mysql_proxy/bin/mysql-proxy  --defaults-file=./mysql_proxy/conf/proxy_4000.conf
```

如果遇到报错：

```
 loading config from './mysql_proxy/conf/proxy_4000.conf' failed:permissions of ./mysql_proxy/conf/proxy_4000.conf aren't secure (0660 or stricter required)
```

这个是安全要求，将配置文件权限设为660才能启动，改一下权限就可以。



