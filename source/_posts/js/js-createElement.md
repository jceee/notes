title: javascript创建DOM元素  
date: 2014-03-15 15:49  
tags: javascript  
categories: javascript

---

2种创建DOM元素的方法

## 创建一个element

```
	/**
	 * 赋值obj对象为一个创建input元素
	 * 设置obj元素属性，可以直接以`.`赋值，也可以用`setAttribute`
	 */
 	var obj = document.createElement('input');
 	
	obj.name = 'name';
	obj.value = 'value';

	obj.setAttribute('id','idname');
	obj.setAttribute('type','typename');
	
	/**
	 * 在id为before的元素后面插入obj
	 */
	document.getElementById('before').appendChild(obj);
```

## 简单粗暴地输出标签

```
	document.write("<inpt name='name' value='value' id='idname' type='typename'>");
```