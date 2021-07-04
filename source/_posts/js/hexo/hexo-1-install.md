title: 使用hexo搭建个人博客 part1
date: 2015-09-18 21:06:35
categories: node
tags: hexo

---


>代码段中的"$ "指在命令行下执行

---

### 安装hexo

安装首先环境中要装好：

* node.js
* npm

这2个的安装这里不详细介绍，在安装完的情况下：


```
    $ npm install -g hexo
```

### 初始化项目

初始化blog项目


```
    $ hexo init blog
```

或者创建一个项目blog,在项目中初始化

```
    $ mkdir blog
    $ cd blog
    $ hexo init
```

### 安装依赖

```
    $ npm install
```

### 开启服务

hexo中3.0版本，在第一次开启服务的时候需要先安装一下hexo的server工具：

```
    $ npm install hexo-server --sav
```

开启服务

```
    $ hexo server -p 4000
```

-p命令是设置服务监听端口，不传默认4000，直接在浏览器输入[http://localhost:4000](http://localhost:4000)就可以看到效果了：

```
 INFO  Hexo is running at http://0.0.0.0:4000/. Press Ctrl+C to stop.
```

### 新增文章

文章的目录是在根目录下的source/_posts文件夹中，可以直接在文件夹下新建md文件，也可以直接用命令的方式：

```
   $ hexo new post example
```
这样，在source/_posts/下就多了个example.md的文件了:

```
title: example
date: 2015-09-18 21:04:03
tags:
---
```

title是文章的标题;  
date是文章的时间;  
tags是文章的标题，多个可以以数组的形式填写：[tag1,tag2,tag3,...];
categories是分类，如果你想添加到对应的分类，可以加上;  
---之后的内容就是文章的主体了，以md的格式写之就可以了

### 生成静态文件

生成的文件放在根目录的public文件夹下,每次有文件重新部署，都需要重新生成一下静态化文件，再重开服务

```
    $ hexo generate
```

### 部署项目文件

部署可以分2种，一种是直接访问静态化之后生成的public文件夹，一种是部署到其他服务器(如github)

用第一种方法的可以直接在服务器上用静态化命令就可以了，这里具体讲下第二种。

部署到git上的，需要在项目上根目录下的 _config.yml文件中加入部署配置

```yml
deploy:
  type: git
  repository: https://github.com/yourGithubRespository
  branch: master
  message: hexo deploy message
```

type是部署类型，repository是仓库地址，branch我选了主线

在部署时需要注意一点，deploy命令会直接删除仓库中的文件，把新生成的静态化文件更上去，这样的部署方式需要提交的目的仓库是单独为部署用的，我把当前hexo项目作为静态文件生成器独立成一个项目，把存放静态文件的项目独立成另一个仓库。    

之后就可以开始部署了：
hexo中3.0版本，在第一次部署的时候需要先安装一下hexo的git部署工具：

```
    $ npm install hexo-deployer-git --save
```

之后就可以开始部署了：

```
    $ hexo deploy
```

每次部署前都需要重新生成文件，2步可以合成一起：

```
    $ hexo deploy -generate
```

### 命令缩写

hexo的命令有缩写模式，有些命令中的单词可以简化成头字母：

```
    $ hexo server  =>  $ hexo s
    $ hexo deploy  =>  $ hexo d
    $ hexo generate  =>  $ hexo g
    $ hexo deploy -generate  =>  $ hexo d -g
```









