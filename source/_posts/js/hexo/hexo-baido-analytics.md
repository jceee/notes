title: hexo添加百度统计  
date: 2016-01-25 21:52:27  
categories: node  
tags: hexo  

---

想为hexo添加百度统计可以分为以下几步

## 注册百度统计

### 注册

如果已经有了账号可以跳过注册这步，
注册账号的地址是
[百度统计](http://tongji.baidu.com/web/welcome/login)

## 获取百度统计代码

登陆站长后台，点击网站中心，左侧有个代码获取。基本代码都是一样的:

```
	<script>
	var _hmt = _hmt || [];
	(function() {
	  var hm = document.createElement("script");
	  hm.src = "//hm.baidu.com/hm.js?xxxxxxxxxxxxxxxxxxx";
	  var s = document.getElementsByTagName("script")[0]; 
	  s.parentNode.insertBefore(hm, s);
	})();
	</script>
```

统计代码的作用都是一样的，在页面插入一段js请求百度的统计接口。重点在于每个人得到的`//hm.baidu.com/hm.js?`后面的key值不同。

## 在页面添加百度统计代码

### 自带统计模板

大部分主题已经对百度统计功能进行了配置化抽取。以Next主题为例,在主题目录下的`layout`/`_scripts`/`analytics`统计模板文件夹下已经有了`baidu-analytics.swig`文件:

```
	{% if theme.baidu_analytics %}
	  <script type="text/javascript">
	    var _hmt = _hmt || [];
	    (function() {
	      var hm = document.createElement("script");
	      hm.src = "//hm.baidu.com/hm.js?{{ theme.baidu_analytics }}";
	      var s = document.getElementsByTagName("script")[0];
	      s.parentNode.insertBefore(hm, s);
	    })();
	  </script>
	{% endif %}
```

只要条件满足：`theme.baidu_analytics`为true便插入这段代码。统计代码的key值已经被抽取到主题`_config.yml`中的`baidu_analytics`属性了。

接下来，在主题的`_config.yml`中添加上百度统计的key就行了

```
	# Baidu Analytics ID
	baidu_analytics: your key
```

### 手动添加统计模板

若主题中没有内置百度统计模板的，可以手动添加，像上面自带统计模板一样，建立一个统计模板文件夹`layout`/`_scripts`/`analytics`，新建`baidu-analytics.swig`文件并加入上面那段代码。接着，在想添加的模板上引入这文件就行了。

```
	{% include 'analytics/baidu-analytics.swig' %}
```
