title: PHP的自动加载
date: 2014-12-28 18:13
categories: php  
tags: php

---

以前php的加载是通过`include()`,`require()`等方法来加载外部文件，之后再通过实例调用方法或直接调用静态方法，而这样子写引入语句实在很麻烦，有的框架会将特定路径的文件全部引入，直接实例化就能使用，但这样一来有的类包不一定用到，写的类包越多的时候，加载的东西就不少了，影响程序的性能。

这里介绍一下php自带的标准库SPL(Standard PHP Library)的自动加载:

```
function __autoload($classname)
{
	$classpath = './' . $classname . '.php';
	if (file_exists($classpath)) {
	    require_once($classpath);
	}else {
	    echo 'class file'.$classpath.'not found!';
	}
}
 
var_dump(spl_autoload_functions());
spl_autoload_register('newAutoload');
var_dump(spl_autoload_functions());
 
$testObj1 = getInstance('test');
$testObj2 = getInstance('test');
$testObj3 = getInstance('test');
 
function getInstance($class, $returnInstance = false)
{
    return new $class();
}
 
function newAutoload($classname)
{ 
    $classpath = './' . $classname . '.php';
    if (file_exists($classpath)) {
        var_dump('require success');
        require_once($classpath);
    } else {
        echo 'class file ' . $classpath . ' not found!';
    }
}
//array
//  0 => string '__autoload' (length=10)
//array
//  0 => string 'newAutoload' (length=11)
//string 'require success' (length=15)
```

`spl_autoload_functions()` 方法是用来查看当前自动加载的方法，当前有个`__autoload`魔术方法，所以返回了函数名，若没定义自动加载方法的话，返回的是false,而 `spl_autoload_register()` 方法是通过方法名将一个方法注册到自动加载方法，这里用`newAutoload`方法来替换`__autoload`方法。

`newAutoload`方法中，我们定义一下，每执行成功一次，打印一句'require success'，这里只打印了一次，说明了虽然实例了3次`test`，但因为`test`类已经加载过一次，就不会再执行自动加载的方法。通过`getInstance()`这种加载类的方法，比以前的`include()`之类的方便多了，只需要加载这个写了`getInstance()`方法的文件就可以了。

重写的自动加载方法可以根据需要，通过判断类的名字，定义不同的文件路径。
