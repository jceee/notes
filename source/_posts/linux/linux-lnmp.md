title: Ubuntu下配置lnmp  
date: 2016-06-19 22:10:25  
tags: linux   
categories: linux  
 
---

>环境：Ubuntu Linux 14.04

# 安装软件仓库管理器

```
	$ sudo apt-get install python-software-properties;
```

# 安装PHP

## 安装php及扩展

```
	$ sudo apt-get install python-software-properties;
	$ sudo add-apt-repository ppa:ondrej/php5-5.6;
	$ sudo apt-get update
	$ sudo apt-get install php5-fpm php5-cli php5-curl php5-gd php5-json php5-mcrypt php5-mysqlnd php5-memcache php5-redis php5-xdebug;
```

查看版本信息

```
   $ php5-fpm -vPHP 5.6.22-1+donate.sury.org~trusty+1 (fpm-fcgi)Copyright (c) 1997-2016 The PHP GroupZend Engine v2.6.0, Copyright (c) 1998-2016 Zend Technologies    with Zend OPcache v7.0.6-dev, Copyright (c) 1999-2016, by Zend Technologies    with Xdebug v2.3.3, Copyright (c) 2002-2015, by Derick Rethans
```

## 常用命令

### 关闭，开启，重启

```
	$ sudo service php5-fpm stop 
	$ sudo service php5-fpm start
	$ sudo service php5-fpm restart  
```

## 安装composer

```
	$ sudo apt-get install curl
	$ sudo curl -sS https://getcomposer.org/installer | php
	$ sudo mv composer.phar /usr/local/bin/composer
```


# 安装Nginx

## 安装Nginx

```
	$ sudo add-apt-repository ppa:nginx/stable;
	$ sudo apt-get update;
	$ sudo apt-get install nginx;
```

查看版本信息

```
	$ sudo nginx -v
```

## 常用命令

启动 ，关闭，重启，配置重载

```
	$ sudo service nginx start
	$ sudo service nginx stop
	$ sudo service nginx restart
	$ sudo service nginx reload  
```

# 安装mysql

```
	$ sudo add-apt-repository -y ppa:ondrej/mysql-5.6
	$ sudo apt-get update
	$ sudo apt-get -y install mysql-server
```

登陆mysql创建账号

```
	$ mysql -u root 
	mysql>create database wordpress;
	mysql>grant all privileges on *.* to jceee identified by '123456';
	mysql>flush privileges;
	mysql>exit;
```

# 使用wordpress测试环境

## 安装git

```
	$ apt-get install git
```

## 创建项目文件夹

```
	$ sudo mkdir -p /opt/htdocs/
```

## 下载wordpress

```
	$ sudo git clone https://github.com/WordPress/WordPress /opt/htdocs/wordpress
	$ sudo chown -R www-data /opt/htdocs
```

## 增加Nginx配置

```
	$ sudo vi /etc/nginx/conf.d/wordpress.conf
```

写入以下内容，保存并重载Nginx配置

```
server {    listen 80;    server_name www.wordpress.com;    root /opt/htdocs/wordpress;    index index.html index.php;    location / {        index index.html index.php;    }    location ~ \.php$ {
            include fastcgi.conf;                  
            fastcgi_pass unix:/var/run/php5-fpm.sock;    }}
```

如果php-fpm是以TPC/IP	方式监听,如127.0.0.1:9000，则把上方

```
    fastcgi_pass unix:/var/run/php5-fpm.sock;
```

改为

```
	fastcgi_pass 127.0.0.1:9000;
```

## 修改hosts

```
	$ sudo vi /etc/hosts
```

增加以下内容并保存退出

```
127.0.0.1 www.wordpress.com
```

## 测试

在浏览器输入

```
http://www.wordpress.com
```

看访问结果









