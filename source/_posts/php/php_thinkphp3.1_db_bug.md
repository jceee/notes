title: ThinkPHP3.1 事务BUG
date: 2020-02-11 18:13
categories: php  
tags:  [php,mysql]  

---

>  久远的框架不一定就安全，可能有一些隐藏的BUG

## 发现问题

影响：开启事务，rollback后，再执行update/delete/insert等写操作，数据没有发生变化

```php

$UserModel = new UserModel();
$UserModel->startTrans();
$uid = 1;
try {
  //dosomething.....
  $UserModel->where(array("uid"=>$uid))->save(array("status"=>1));
  $UserModel->commit();
} catch (\Exception $e) {
  $UserModel->rollback();
}
//更新无效
$UserModel->where(array("uid"=>$uid))->save(array("login_time"=>time()));

```


## 原因：

事务启动时会关闭mysql的自动提交，事务正常提交后会重新打开mysql的自动提交,但rollback时没重新打开

```
    /**
     * 启动事务
     * @access public
     * @return void
     */
    public function startTrans() {
	$this->error = '';//清空错误信息
        $this->initConnect(true);
        //数据rollback 支持
        if ($this->transTimes == 0) {
            $this->_linkID->autocommit(false);
        }
        $this->transTimes++;
        return ;
    }
	
    /**
     * 用于非自动提交状态下面的查询提交
     * @access public
     * @return boolen
     */
    public function commit() {
        if ($this->transTimes > 0) {
            $result = $this->_linkID->commit();
            $this->transTimes = 0;
            if(!$result){
                $this->error();
				$this->_linkID->autocommit( true);
                return false;
            }
			$this->_linkID->autocommit( true);
        }
        return true;
    }

    /**
     * 事务回滚
     * @access public
     * @return boolen
     */
    public function rollback() {
        if ($this->transTimes > 0) {
            $result = $this->_linkID->rollback();
            $this->transTimes = 0;
            if(!$result){
                $this->error();
                return false;
            }
        }
        return true;
    }

```


## 修复：
在rollback里重新打开自动提交

```
    /**
     * 事务回滚
     * @access public
     * @return boolen
     */
    public function rollback() {
        if ($this->transTimes > 0) {
            $result = $this->_linkID->rollback();
			//打开自动提交
			$this->_linkID->autocommit( true);
            $this->transTimes = 0;
            if(!$result){
                $this->error();
                return false;
            }
        }
        return true;
    }
```



