title: hexo gitpage 404  
date: 2021-10-03 21:30:20  
categories: node  
tags: hexo  

---



## 一个奇怪的问题

* Step 1 更新了一篇新的post;

* Step 2 使用`hexo s`本地页面显示正常 ;

* Step 3 使用`hexo g -d`命令执行正确;

* Step 4 打开对应的Git page，发现404

## 为什么会出现

* 首先从最后环节开始排查，使用`hexo d --debug`查看部署是否有问题，没看到报错，把`.deploy_git`下的文件清理掉，重新跑几次也没解决
* 接着看部署问题，`hexo g --debug`，文件生成也正常，无报错
* 继续看生成的入口文件，发现`index.html`是空的，问题可能在这
* 搜索`hexo index.html empty` 这回找到别人也有一样的问题，找到问题：

```
 node 版本太高了，hexo 还不支持
```

## 解决

查看`hexo`版本：

```
$ hexo -v                                                                            
hexo: 3.9.0
hexo-cli: 4.3.0
os: darwin 20.5.0 11.4
```

参考[hexo官网](https://hexo.io/news/2021/07/09/hexo-renderer-pug-2-0-0-hexo-cli-4-3-0-hexo-paginaiton-2-0-0-hexo-clean-css-2-0-0/)推荐，支持的node版本是`8`以上或者`12`

```
drop node 8 and add node 12
```

查看本地`node -v`:`v16.6.1`确实不符合，重新下载了`node12`，再部署解决～

## 后记

版本不兼容也没有提示，对使用者还是有些不友好，把错误都吞掉了
