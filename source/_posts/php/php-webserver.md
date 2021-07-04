title: php内置http服务器  
date: 2016-01-26 21:52:27  
categories: php  
tags: php  

---

php5.4开始内置了web服务器，虽然并不建议在生产环境中使用，但就本地调试来说这是个不错的工具。

启动服务器很简单，打开终端，进入到项目根目录，执行以下命令:

```
	php -S localhost:8000
```

在浏览器中输入`localhost:8000/index.php`就可以访问到当前目录下的index.php文件了。

上面命令会启动一个php Web Server,端口是4000，端口可以根据自己需要换，但注意不要跟其他服务常用端口重复了。

如果需要对指向的php服务器指向非默认的php配置文件，可以用`-c`选项去指定对应的ini文件：

```
	php -S localhost:8000 -c app/config/php.ini
```


