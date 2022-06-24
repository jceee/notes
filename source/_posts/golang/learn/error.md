title:  Golang错误处理
date: 2022-03-13 16:31:16 
tags: [golang,errors]  

categories: golang
---

> golang编程中，错误处理估计是写得最多的代码，我们总结一下使用上的问题

# 标准库 vs 第三方包pkg/errors



`error`是一个接口，实现了`Error()`方法，返回字符串

```
type error interface {
	Error() string
}
```

## 标准库errors

标准库提供了简单的错误创建与处理，我们使用官方包errors简单新建一个错误

```
package main 
import (
	"errors"
	"fmt"
)

func main() {
	err := errors.New("something error")
  fmt.Printf("%+v", err)
}
```

得到结果是把New的错误信息打印出来：

```
something error
```

除了通过`errors.New()`新建错误，标准库还支持通过`fmt.Errorf()`创建：

```
err = fmt.Errorf("this is error：%s", "bad news")
```

## 第三方包pkg/errors

pkg/errors是Go标准库的替代品，它提供了一些非常有用的操作用于封装和处理错误
获取第三方包：

`go get github.com/pkg/errors/`

同样新建错误(也支持用errors.Errorf创建)：

```
package main

import (
	"fmt"

	"github.com/pkg/errors"
)

func main() {
	err := errors.New("something error")
	fmt.Printf("%+v", err)
}

```

打印结果如下，我们可以看到除用`%+v`打印时除了本身的信息，对应的堆栈信息，错误行数也输出了，相比标准库更好用：
```
something error
main.main
	/src/error/error.go:10
runtime.main
	/src/runtime/proc.go:225
runtime.goexit
	/usr/local/go/src/runtime/asm_amd64.s:1371
```

## 总结

我们在应用程序中使用`pkg/errors`包处理错误，但注意在公共库当中，我们一般不用这个包，减少依赖

# 错误处理方式


## 哨兵错误

`Sentinel Error`哨兵错误，就是定义一些包级别的错误变量，然后在调用的时候外部包可以直接对比变量进行判定，在标准库当中大量的使用了这种方式

如io包的`io.EOF`，gorm的`gorm.ErrRecordNotFound`，底层系统调用的`syscall.ENOENT`等..

我们在使用时一般用等值或者`errors.Is`判断：

```
...
if err == gorm.ErrRecordNotFound {
	dosomething....
}

if errors.Is(err, gorm.ErrRecordNotFound) {
	dosomething....
}

```

优点：不用判断`error.Error()`返回的字符串
缺点：不灵活，将`error`当作包的API暴露给第三方，调用方必须先了解有哪些预定义的错误，并在程序中进行比较。服务提供方需要公共文档说明各种Error，导致包之间有了依赖，重构或版本升级也麻烦
总结：尽可能避免使用这种方式


## 自定义类型error

`error`是interface，只要实现了`Error()`都可以当error使用

如

```
type MyErr struct {
	Msg  string
	File string
	Line int
}

func (e *MyErr) Error() string {
	return fmt.Sprintf("%s::%d:%s", e.File, e.Line, e.Msg)
}

func doSomething() error {
	err := &MyErr{"err msg", "error.go", 12}
	return err
}

func main() {
	err := doSomething()
	fmt.Printf("%+v", err)
	switch err := err.(type) {
	case nil:
		//call success
	case *MyErr:
		fmt.Println("error occurred on line:", err.Line)
  default:
  	//unknown error
	}
}
```

得到结果是实现的`Error()`里定义的格式：`error.go::12:err msg`

优点：与错误值相比，错误类型的改进是能包装提供更多上下文，例如标准库的`os.PathError`
缺点：同样需要把错误类型暴露给调用方，而调用方需要用`switch`断言类型，还是有耦合，不利于维护
结论：尽量避免使用error types，至少避免将他们作为公共API的一部分



## 不透明处理

不透明错误是`opaque errors`，调用方捕获错误即返回上层，这样对代码耦合是最少的：

```
func dosomething() err {
	err := step1()
	if err != nil {
		return err
	}
	...
}
```

优点：对代码耦合最少
缺点：少数情况下当想对具体错误进行判断时不太够用
总结：可以用在大部分情况

## 按行为断言

当调用方需要调查错误性质，并做相应处理，可以使用行为断言，而不是断言错误是特定类型或值

如`net`包的`Error`接口，定义了`Timeout()`,`Temporary()`两种行为，调用方可以通过行为做对应处理	：

```
type Error interface {
	error
	Timeout() bool   // Is the error a timeout?
	Temporary() bool // Is the error temporary?
}
```

处理：

```
err := dosomething()
if nerr, ok := err.(net.Error); ok && nerr.Temporary() {
	time.Sleep(1e9)
	continue
}
if err != nil {
	...
}
```

优点：我们可以不导入定义错误的包，或实际上不了解error底层类型的情况下实现逻辑处理
结论：比预定义错误和自定义错误好

# 一些好习惯


## 减少缩进代码

错误处理时先判断错误，遇到错误则返回，使代码成为一条流畅的直线，而不是缩进的代码

```
err := A()

if err != nil {
	//handle error 
}

//do something
err = B()
if err != nil {
	// handle error 
}
//do something
```

## 减少重复判断

如统计`io.Reader`读取内容行数的实现：

普通实现:

```
func CountLines(r io.Reader) (int, error) {
	var (
		br    = bufio.NewReader(r)
		lines int
		err   error
	)

	for {
		// 读取到换行符就说明是一行
		_, err = br.ReadString('\n')
		lines++
		if err != nil {
			break
		}
	}

	// 当错误是 EOF 的时候说明文件读取完毕了
	if err != io.EOF {
		return 0, err
	}

	return lines, err
}

```

使用`bufio.scan`实现:

```
func CountLines(r io.Reader) (int, error) {
	var (
		sc    = bufio.NewScanner(r)
		lines int
	)

	for sc.Scan() {
		lines++
	}

	return lines, sc.Err()
}
```

`bufio.scan`实现后少了很多`if err`判断，因为`sc.Scan`做了类似的处理，需要循环读以的都可以考虑这样包装

## wrap error:错误包装

第三方包`github.com/pkg/errors`的Wrap可以追加错误信息和堆栈信息，这样我们更加方便定位错误

```
// Wrap returns an error annotating err with a stack trace
// at the point Wrap is called, and the supplied message.
// If err is nil, Wrap returns nil.
func Wrap(err error, message string) error {
	if err == nil {
		return nil
	}
	err = &withMessage{
		cause: err,
		msg:   message,
	}
	return &withStack{
		err,
		callers(),
	}
}
```

但请不要处处都使用`errors.Wrap`，因为这样会产生大量无效堆栈，一般我们只需要在调用标准库包时添加，平时遇到错误可以用无透明方式返回处理，如果想再追加Message，可以使用`errors.WithMessage`

```
err = doSomething()

if err != nil {
  err = xerrors.WithMessage(err, "dosomething fail")
  return
}
```

经过多层包装的错误没法与哨兵模式结合判断，这时可以通过`errors.Cause`获取最初的`error`来判断：

```

import (
	"errors"
	"fmt"
	"io"

	xerrors "github.com/pkg/errors"
)

err := io.EOF
err = xerrors.Wrap(err, "wrap message")

if errors.Is(xerrors.Cause(err), io.EOF) {
fmt.Print("true")
}
```





