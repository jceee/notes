title: 根据某字段对多维数组进行排序
date: 2015-01-09 21:06:35
categories: code
tags: php

---

根据某字段对多维数组进行排序，在看到array_multisort方法的作用时突然想到，可以用来做这个方法

```php
	function sortArrByField(&$array, $field, $isDesc = false)
	{
	 
	    $fieldArr = array();
	    foreach ($array as $k => $v) {
	 
	       $fieldArr[$k] = $v[$field];
	    }
	    $sort = $isDesc == false ? SORT_ASC : SORT_DESC;
	    array_multisort($fieldArr, $sort, $array);
	}
```














