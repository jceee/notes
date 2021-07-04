title: php魔术方法__call
date: 2015-04-11 17:34  
categories: php  
tags: php

---

>_call是魔术方法中的一个，当程序调用到当前类中未声明或没权限调用的方法时，就会调用__call方法.


```
	class test
	{
		public function emptyFunc()
		{
			$getArgs = func_get_args();
			$funcName = $getArgs[0];
			//$params = array_slice($getArgs, 1);
			//var_dump($params);  // this is params                
			return $funcName . ' function is not exists';
		}
		 
		public function __call($m, $params)
		{
			$arr[] = $m;
			$arr = array_merge($arr, $params);
			return call_user_func_array(array($this, 'emptyFunc'), $arr);
		}
 
		protected function nowToTest()
		{ 
			return 'this is nowToTest';
		}
	}
	 
	$testObj = new test();
	var_dump($testObj->nowToTest('params1','params1'));
	//var_dump result => string(29) "nowToTest function is not exists"
```

如上`test`类中，`nowToTest`方法是存在的，但修饰这方法的是`protected`(保护)，所以实例出来的对象没权限执行,这时就跑到`__call`中去了.

`_call()`有2个参数，第一个`$m`是当前调用方法的名字，这里是`nowToTest`,第二个`$params`是调用`nowToTest`方法时传入的参数。以数组的形式组合在$params中。 

`call_user_func_array($method, $params)`这个php方法的作用是调用 `$method`方法，参数为`$params`,如果方法是在类中的话，就用上面那种数组形式调用就可以了，这里调用的是`emptyFunc`方法。 

`func_get_args()`的作用是以数组形式获取传入的所有参数。而这些参数在`__call`中传入的，第一个参数就是方法名。所以最后返回的结果是


```
	"nowToTest function is not exists"
```