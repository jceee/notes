title: GO学习笔记--defer篇
date: 2019-04-23 22:33:16 
tags: [golang,defer]  
categories: golang
---

# 什么是defer

# defer特别

栈，后进先出

# 什么场景下用defer

程序员在编程的时候，经常需要打开一些资源，比如数据库连接、文件、锁等，这些资源需要在用完之后释放掉，否则会造成内存泄漏。

```
func main (){
	f,err := os.Open("test.txt")
	if err != nil {
		fmt.Print(err, "\n")
		return
	}
	defer func() {
		fmt.Print("do close \n")
		closeErr := f.Close()
		if closeErr != nil {
			fmt.Print("close file err \n")
			return
		}
	}()
	fmt.Print("main end \n")
}
```

# defer原理

每次defer语句执行的时候，会把函数“压栈”，函数参数会被拷贝下来；当外层函数（非代码块，如一个for循环）退出时，defer函数按照定义的逆序执行；如果defer执行的函数为nil, 那么会在最终调用函数的产生panic.
defer语句并不会马上执行，而是会进入一个栈，函数return前，会按先进后出的顺序执行。也说是说最先被定义的defer语句最后执行。先进后出的原因是后面定义的函数可能会依赖前面的资源，自然要先执行；否则，如果前面先执行，那后面函数的依赖就没有了。
