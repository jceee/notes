title: ajax请求时json对象数据变成字段串[object object]
date: 2015-08-14 22:41
tags: javascript
categories: javascript

---

在对一个旧项目修改时，遇到一个jquery版本引发的ajax问题。

ajax很普通地如下：

```javascript
    ...
	$.ajax({
	     type: "POST",
	     url: _url
	     dataType: "json",
	     data: _data,
	     error: function(){
	         console.log('error');
	     },
	     success: function(data){
	         console.log(data);
	     }
	});
```

ajax向后端接口发送请求时，如果请求数据_data是只有1级时，还是正常的，如下


```javascript
	var _data = {
	    data: 'test',
	    id: 1
	};
	$.ajax({
	    ....   
	});
```

当请求数据的二级数据为如下对象或数组时，form表单中的数据就变了

```javascript
	var _data = {
	    testData: {
	       test:  'test'
	    }
	    data: 'test',
	    id: 1
	}; 
	console.log(_data);
	$.ajax({
	    ....   
	});
```

控制台输出正常数据，然而在header查看请求表单的数据时，Form Data中的数据被转化成：

```
testData: [object Object]
data: 'test'
id: 1
```

正常的数据应该是这样的：

```
testData['test']: 'test'
data: 'test'
id: 1
```

后来查了一下引用的jquery版本，1.2.3 ,  罪魁祸首就是这个了，换了个高些的版本1.4就正常化了。