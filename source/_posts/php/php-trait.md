title: php性状(trait)  
date: 2016-02-12 19:30:48  
categories: php    
tags: php  

---

>Trait是php5.4.0引入的新概念，是类的部分实现，可以混入一个或多个现有的php类中。

性状有两个作用：

* 表明类可以做什么（接口）
* 提供模块化实现（类）

## 创建性状

```php
	trait TestTrait
	{
	    public function getSomething()
	    {
	        return $this->something;
	    }
	}
```

## 使用性状

```php
	class Test
	{
	    use TestTrait;
	    protected $something = 'something';
	}

	$test = new Test();
	var_dump($test->getSomething());
	
	/**
	 * result: something
	 */
```

## 抽象方法,静态方法与静态属性

Trait跟类一样，也支持抽象方法的定义，还有静态方法，静态属性

```php
	trait TestTrait
	{
	    static $num;
	    
	    public function getSomething()
	    {
	        return $this->something;
	    }
	
	    abstract public function showHelloWorld();
	    
	    static public function getNew()
	    {
	        return date('Y-m-d H:i:s');
	    }
	
	    public function addNum()
	    {
	        self::$num ++;
	        return self::$num;
	    } 
	}
	
	class Test
	{
	    use TestTrait;
	
	    protected $something = 'something';
	
	    public function showHelloWorld()
	    {
	        var_dump('hello world');
	    }
	}

	$test = new Test();
	
	$test->showHelloWorld();
	
	var_dump(Test::getNew());
	
	var_dump($test->addNum());
	var_dump($test->addNum());
	var_dump($test->addNum());
	var_dump($test->addNum());
	
	
	/** 
	 *result:
	 * string(11) "hello world"
	 * string(19) "2016-02-12 21:11:27"
	 * int(1)
	 * int(2)
	 * int(3)
	 * int(4)
	 */
```

## Trait方法优先级

性状定义的方法的优先级是：

```
当前类中的方法 > Trait的方法 > 基类中的方法
```

## 使用多个Trait

使用多个trait时，需要用`,`分隔开不同的性状

```php
	class Test
	{
	    use TestTrait,OtherTrait;
	    protected $something = 'something';
	    ...
	}
```

## insteadof

两个Trait在同一个类中命名冲突，可以使用insteadof来指定方法名的使用来源

Trait1::method insteadof Trait2

insteadof是将trait2中的方法method代替成trait1中的method


```php
	trait OtherTrait
	{
	    
	    public function getSomething()
	    {
	        return $this->something . ' from other';
	    }
	    
	    public function showOther()
	    {
	        return 'this is other';
	    }
	}
	
	
	class Test
	{
	    use TestTrait, OtherTrait {
	        OtherTrait::getSomething insteadof TestTrait;
	    }
	
	    protected $something = 'something';
	}
	
	$test = new Test();
	var_dump($test->getSomething());
	
	/**
	 * result: 
	 * something from other
	 */
```

## as

trait::method as public|protected|private otherMethodName

as 是给方法取一个别名,同时还可以声明方法的访问控制

```
	class Test
	{
	    use TestTrait {
	        TestTrait::getSomething as public getSomethingPart2;
	    }
	
	    protected $something = 'something';
	}
	
	$test = new Test();
	var_dump($test->getSomething());
	var_dump($test->getSomethingPart2());
	
	/**
	 * result: 
	 * something  
	 * something
	 */
```

## 组合

trait同样可以引用其他trait,用法跟类一致.

```php
	trait ThreeTrait
	{
	    use OtherTrait;
	}
```
