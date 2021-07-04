title: 修改用brew安装的mysql配置
date: 2015-10-18 15:35:33
categories: mysql
tags: brew
---

## 用brew安装mysql

[brew](http://brew.sh/)是OS X上的包管理工具，类型linux上的apt-get, yum等

### 首先，需要先装好brew

```
	$ ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```
### mysql install

安装：

```
	$ brew install mysql
```

设置开机启动:
 
```
 	$ ln -sfv /usr/local/opt/mysql/*.plist ~/Library/LaunchAgents
 	$ launchctl load ~/Library/LaunchAgents/homebrew.mxcl.mysql.plist
```
 
## 修改mysql配置  
 
### 查看配置文件引用的路径
 
```
	$ mysqld --help --verbose | more
```
 
通过这命令，可以看出mysql默认调用的配置文件路径：

```
	Default options are read from the following files in the given order:
	/etc/my.cnf /etc/mysql/my.cnf /usr/local/etc/my.cnf ~/.my.cnf
```

### 创建配置文件 

brew安装的mysql，通常这几个路径都是没有创建配置文件的，我们自己创建一下。
先找出我们安装的mysql的配置样例文件,在安装文件中的support-files文件夹中：  

```
	$ ls $(brew --prefix mysql)/support-files/my-*
```
   
复制到引用目录：

```
	$ cp /usr/local/opt/mysql/support-files/my-default.cnf /etc/my.cnf
```
	
然后就可以按自己需求修改配置了

## mysql启动命令

重启mysql,  unload 是关闭， load是启动

```
	$ launchctl unload -w ~/Library/LaunchAgents/homebrew.mxcl.mysql.plist
	$ launchctl load -w ~/Library/LaunchAgents/homebrew.mxcl.mysql.plist
```

为了文件管理可以把命令alias下：

```
	$ vim ~/.bash_aliases
```

输入以下内容，保存

```
	alias mysql.start="launchctl load -w ~/Library/LaunchAgents/homebrew.mxcl.mysql.plist"
	alias mysql.stop="launchctl unload -w ~/Library/LaunchAgents/homebrew.mxcl.mysql.plist"
	alias mysql.restart='mysql.stop && mysql.start'  
```

保存后添加到.bash_profile文件中

```
	echo "[[ -f ~/.bash_aliases ]] && . ~/.bash_aliases" >> ~/.bash_profile     
	source ~/.bash_profile
```

这样，bash_aliases就生效了，通过以下命令就可以控制mysql开启，关闭和重启了

```
	$ mysql.start	
	$ mysql.stop
	$ mysql.restart
```




