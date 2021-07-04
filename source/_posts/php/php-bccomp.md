title: php高精确度数值比较-bccomp  
date: 2016-02-16 23:26:30  
categories: php   
tags: php

---

## bccomp

bccomp是php的bcmath库中的方法，使用前先用phpinfo()确认一下有没有开启bcmatch库,没有则需要进行对应的编译操作。

`bccomp($left_operand, $right_operand [, $scale = 0]);`

参数

|参数|参数描述|属性|
|---|---|---|
|$left_operand |左边字符串|string|
| $right_operand |右边字符串|string|
| $scale(可不填，默认0) |返回值小数点后所需的位数|int|

`bccomp`函数用于比较二个高精确度的数字。输入二个字符串，若二个字符串一样大则返回 0；若左边的数字字符串 (left operand) 比右边 (right operand) 的大则返回 +1；若左边的数字字符串比右边的小则返回 -1。scale 是一个可有可无的选项，表示返回值小数点后所需的位数:

```
echo '<pre>';
var_dump(bccomp('1', '2'));   // int(-1)
var_dump(bccomp('1.00001', '1', 3)); // int(0)
var_dump(bccomp('1.00001', '1', 5)); // int(1)
```

## 整数与浮点数对比

举个例子：

```
echo '<pre>';
var_dump(8160 == '81.6' * 100);          //false
var_dump('81.6' * 100);                  //float(8160)
var_dump('81.6' * 100 - 8160);           //float(-9.0949470177293E-13)
```

第一个值是整数8160，第二个值是浮点81.6 跟 100相乘，虽然都是8160，但并不相等，减下来有差。这时，我们可以用bccomp来做判断：

```
var_dump(bccomp(8160, '81.60' * 100) === 0);  //bool(true)
```

如果没有bcmath库，也可以把第一个整数除以100，得到浮点数再进行对比

```
var_dump(8160 / 100 == '81.6');               //bool(true)
```


