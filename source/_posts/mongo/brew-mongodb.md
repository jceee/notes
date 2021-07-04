title: brew安装mongodb  
date: 2016-01-27 22:51:27  
categories: mongodb  
tags: [brew,mongodb]

---

## 用brew安装mongodb


### mongodb install

安装：

```
	$ brew install mongodb
```

安装好会出现以下内容:
 
```
==> Downloading https://homebrew.bintray.com/bottles/mongodb-3.0.7.el_capitan.bottle.tar.gz
######################################################################## 100.0%
==> Pouring mongodb-3.0.7.el_capitan.bottle.tar.gz
==> Caveats
To have launchd start mongodb at login:
  ln -sfv /usr/local/opt/mongodb/*.plist ~/Library/LaunchAgents
Then to load mongodb now:
  launchctl load ~/Library/LaunchAgents/homebrew.mxcl.mongodb.plist
Or, if you don't want/need launchctl, you can just run:
  mongod --config /usr/local/etc/mongod.conf
==> Summary
🍺  /usr/local/Cellar/mongodb/3.0.7: 17 files, 158M
```

最后提示了如何启动mongodb：

```
	mongod --config /usr/local/etc/mongod.conf
```

--config后面的参数是启动时加载的配置文件，如果有什么想自己配置的可以复制一份备份，然后修改自定义的配置:

```
systemLog:
  destination: file
  path: /usr/local/var/log/mongodb/mongo.log
  logAppend: true
storage:
  dbPath: /usr/local/var/mongodb
net:
  bindIp: 127.0.0.1
~
~
```

### 连接mongodb

mongod启动后，在命令行输入`mongo`命令可以连接到数据库:

```
	$ mongo
	MongoDB shell version: 3.0.7
	connecting to: test
	> use blog
	switched to db blog
	> exit;
```

接下来就可以创表，增删查改等操作了





