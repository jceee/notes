title: linux-curl
date: 2016-04-10 22:26:50
tags: linux
categories: linux

---

>curl是利用URL语法在命令行方式下工作的开源文件传输工具。它被广泛应用在Unix、多种Linux发行版中，并且有DOS和Win32、Win64下的移植版本。

## 获取url内容

```
	$ curl url
```

## 保存内容

```
	$ curl -o filename url
	$ curl url >> filename
```

## 显示下载进度条`-#`

```
	$ curl -# -o filename url
```

## 获取response header

```
	$ curl -i url
```

## POST数据

```
	$ curl -d "key1=val1&key2=val2" url
```

## PUT文件

```
	$ curl -F "fileUpload=@/Users/jceee/Pictures/default_avatar.jpeg" \
	 http://local.creed.com/curl
```

## UserAgent

```
	$ curl -A "USER AGENT" URL
```

## COOKIE

Cookie是服务器经常使用的一种记忆客户信息的方法。如果cookie被记录在了文件中，那么使用命令：

```
	$ curl -b stored_cookies_in_file \
	http://local.creed.com
```

可以通过请求登陆接口保存cookie文件：

```
	$ curl -c stored_cookies_in_file \
	-d  "user=username&password=password" \
	http://local.creed.com/login
```

## REFERER

```
	$ curl -e http://www.baidu.com http://local.creed.com/curl
```

## 代理

```
	$ curl -x ip:port http://local.creed.com/curl

```

## 伪造Ip

仅限于用X-FORWARDED-FOR获取IP的地址

```
	$ curl -H "X-FORWARDED-FOR:202.103.229.11"\
	 -H "CLIENT-IP:202.103.229.11"\
	 "http://local.creed.com/curl"
```