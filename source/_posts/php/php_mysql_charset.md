title: 一个环境引发的乱码问题
date: 2021-09-11 18:13
categories: php  
tags: php mysql

---

>  每个程序员多少都遇到过一些祖传代码，当代码能跑起来的时候并不可怕，可怕的是换了个环境，代码跑不起来了，这时脑里离不开这2个问题："it can works ,why ?"  or  "it can`t works , why?"

## 发现问题

有这一段代码，在服务器A中，从Mysql查出来的中文字段是正常显示的，从服务器B中显示的却是`???`

```php

$sql = "SELECT * from m_user limit 10";
$db = new MySQLi($host, $user, $pwd, $dbname, $port);
$result = $db->query($sql);
$output = $result->fetch_assoc();
$result->free();
var_dump($output);
```



## 修复问题

`MySQLi`很少用，现在基本都是用`PDO`了，遇到这问题第一反应是查函数文档，是不是有些奇怪的环境设置。

查到`MySQLi`编码相关有`set_charset`函数，而mysql连接也有``SET NAMES {charset}`可以指定编码，在使用代码指定编码后服务器B也可以正常使用：

```php

$db = new MySQLi($host, $user, $pwd, $dbname, $port);
$db->set_charset("utf8");
// or $db->query("set names utf8");
$result = $db->query($sql);

```

指定编码是可以正常查询，那么我们再通过`get_charset`，看看各自连接默认使用的编码：

```
$db = new MySQLi($host, $user, $pwd, $dbname, $port);
var_dump($db->get_charset());
```

发现正常的机器上使用的是`utf8`，乱码机器上的是`latin1`

## 排查问题

我们发现问题在于php与mysql连接时，默认编码不一致，我们排查一下php与mysql的连接扩展有没有问题

通过`php -i` 命令查看mysql client使用的版本

```
//默认utf8的机器:
mysqli
Client API library version => 5.5.19
---------------------
//默认latin1的机器：
mysqli
Client API library version => 5.5.68-MariaDB
```

通过对比，我们发现两者的版本并不一致，有问题的机器使用的是`MariaDB`，通过wifi百科发现这是个社区开发的，兼容mysql协议的开源数据库管理系统。

`MariaDB`的API和协议兼容MySQL，使用是没有问题的，但`MariaDB`的默认编码是`latin1`，那这很可能就是问题根源。

```
In MariaDB, the default character set is latin1, and the default collation is latin1_swedish_ci
```

尝试修改php连接mysql的 Client API library，安装mysqlmysql_5.5.19 lib包，改原来使用包的软链，发现代码正常运行，问题解决～

## 后记

我们可以通过docker之类的工具，保持统一的环境，但从开发的解决还是尽量维护代码健壮性，通过代码指定连接使用的编码，这样换一个差不多的环境也能正常运行，可以少折腾一些环境

## 参考

* [MariaDB](MariaDB)
* [MariaDb Default Charset](https://mariadb.com/kb/en/setting-character-sets-and-collations/)



