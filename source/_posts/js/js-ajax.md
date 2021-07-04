title: Ajax中的2个需要注意的地方  
date: 2014-12-28 16:49  
tags: javascript  
categories: javascript  

---


>Ajax在WEB程序中调用api的利器，这儿记录下在使用过程中发现2个容易忽略而导致错误的地方，这两个地方也是一些javascript的基础知识，也可以当作重温一下javascript的基础

## 异步获取数据

异步是指，在ajax发送请求后，会继续执行下面的方法，直到请求返回时，再回过头来执行ajax中的回调方法。这也是ajax异步的强大之处，不会因为请求而影响程序其他任务的执行时间。但如果你下面执行的代码中，有需要请求返回的数据，那就会发生错误了，因为请求还没回来，你的代码就执行了。如下:

```
	 $.ajax({
	  
	     url:'/test/test.api',
	     dataType:'json',
	     type:'get',
	      
	     success:function(data){
	          console.log(1);
	     }
	});
	console.log(2);
	//控制台结果：
	//2
	//1
```

你会发现，控制台先出现的数字是2,之后才是1。如果后面有需要用到请求返回数据的代码，必须写在回调方法success的function里面。


## ajax中的this作用域

在一个对象中，this变量代表的是自身这个对象，而在ajax中，参数就是一个对象，参数中this变量所代表的是参数本身，如下：

```
	var testObj = {
	   type : 'this is testObj.type',
	   test : function(){
	        console.log('this.type:' + this.type);
	        var _this = this;
	           
	        $.ajax({
	            url:'/test/test.php',
	            dataType:'json',
	            type:'get',
	            success:function(data){
	                console.log('this.type in ajax:' + this.type);
	                console.log('_this.type:' + _this.type);
	            }
	        });
	    }
	};
	
	testObj.test();
	//控制台出现的数据是：
	//this.type:this is testObj.type 
	//this.type in ajax:get 
	//_this.type:this is testObj.type
```

要在ajax中使用this指向ajax以外的对象，需要先在ajax外面将this赋值给别的变量，再调用。如上面的_this