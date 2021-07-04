title: 订阅发布者模式
date: 2016-04-04 17:21:45
categories: pattern 
tags: [pattern,php]

---

>发布/ 订阅者模式（Publish/Subscribe）有两个参与者：发布者和订阅者。发布者创建某个信息，订阅者绑定这个信道，当发布者有消息发布至该信道时，订阅者就会接收到一个通知。最重要的一点是，发布者和订阅者是完全解耦的，彼此并不知晓对方的存在一种消息模式。

## 订阅发布者

我们创建一个简单的订阅发布者，基本的4个方法：

* registerSubscriber 注册订阅者
* removeSubscriber   订阅的取消
* publish            消息推送
* sortList           消息推送排序

```

class DispatcherPattern
{
    protected $subList = array();

    public function registerSubscriber($name, $callback)
    {
        $this->subList[$name] = $callback;
    }

    public function removeSubscriber($name){
        if (isset($this->subList[$name])) {
            unset($this->subList[$name]);
        }
    }

    public function publish($params)
    {
        if (empty($this->subList) || !is_array($this->subList)) {
            return 'null';
        }
        foreach ($this->subList as $callback) {
            call_user_func_array($callback, $params);
        }
    }

    public function sortList()
    {
        ksort($this->subList);
    }
}
```

## 订阅体

接着我们创建一个用户对象,用户对象有增加积分的功能

```
class User
{
    protected $name;

    public function __construct($name)
    {
        $this->name = $name;
    }

    public function addScore($score)
    {
        printf( $this->name . ' add Score :' . $score . "\r\n");
    }
}
```

## 通过订阅发布者模式为用户增加积分

```
$score = 1;

$dispatcher = new DispatcherPattern();

$dispatcher->registerSubscriber('0', function()
{
    printf( "this is first handle \r\n");
});

$dispatcher->registerSubscriber('3', array(new User('user3'), 'addScore'));
$dispatcher->registerSubscriber('1', array(new User('user1'), 'addScore'));
$dispatcher->registerSubscriber('2', array(new User('user2'), 'addScore'));

$dispatcher->removeSubscriber('2');

$dispatcher->sortList();
$dispatcher->publish(array($score));

```

result:

```
this is first handle 
user1 add Score :1 
user3 add Score :1 
```