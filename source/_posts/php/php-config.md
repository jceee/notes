title: php-config
date: 2016-10-14 17:39:00
tags: php
category: php

---

>php配置

# PHP config

## 隐藏php版本号

通常没经验修改配置的php服务，在请求结束时返回的Response头中会带有php的版本信息，如：

```
cache-control:no-cache
Connection:keep-alive
Content-Type:text/html; charset=UTF-8
date:Fri, 14 Oct 2016 09:44:22 GMT
Server:nginx
Transfer-Encoding:chunked
X-Powered-By:PHP/5.6.16
```

我们想隐藏掉`X-Powered-By:PHP/5.6.16`只需修改一个地方：php.ini中的`expose_php`,把`On`改为`Off`即可

```
expose_php = Off
```

效果如下

```
cache-control:no-cache
Connection:keep-alive
Content-Type:text/html; charset=UTF-8
date:Fri, 14 Oct 2016 09:46:31 GMT
Server:nginx
Transfer-Encoding:chunked
```
