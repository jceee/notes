title: GO语言IDE篇--Vscode (Mac)  
date: 2018-11-20 23:26:46
tags: [go,ide]
---

>工欲善其事，必先利其器
>----《论语·卫灵公》

## 前言

当我们开始学习一个编程语言时，第一步就是选择一款编码工具，我们使用工具的主要目的是提高我们的效率： 
 
* 语法高亮(基本都有)
* 支持DEBUG
* 插件丰富
* 集成终端
* 颜值（这个也很重要啊喂！）

对比多款式工具后，jetbrains系列收费，LiteIDE略丑，Vim学习曲线高...最后我选择了满足了我的所有要求的Vscode，下面开始撸之～

## 安装GO环境

这里假设我们已经安装完基础环境并配置好了环境变量，略过...

## 安装vscode

[vscode官网](https://code.visualstudio.com/)下之

## 安装go插件

### 基础插件

打开vscode，打开插件搜索，输入go，安装之:
![插件安装](/images/go-vscode/go-extend.png)

### 安装依赖包支持

```
$ mkdir -p $GOPAH/src/golang.org/x
$ cd $GOPAH/src/golang.org/x
$ git clone https://github.com/golang/tools.git tools

$ go get -u  github.com/ramya-rao-a/go-outline
$ go get -u  github.com/acroca/go-symbols
$ go install golang.org/x/tools/cmd/guru
$ go install golang.org/x/tools/cmd/gorename
$ go get -u github.com/rogpeppe/godef
$ go get -u github.com/sqs/goreturns
$ go get -u github.com/ramya-rao-a/go-outline
$ go get -u github.com/josharian/impl
$ go get -u golang.org/x/lint/golint
$ go get -u github.com/cweill/gotests/gotests
```

### 安装dlv

go的debug依赖于dlv

安装dlv有2种途径:

#### brew安装dlv

这个帮你解决了证书等问题

```
$ brew install go-delve/delve/delve

```

#### go get安装dlv

安装dlv

```
$ go get github.com/derekparker/delve/cmd/dlv

-- 查看是否安装成功
$ $GOPATH/bin/dlv version
```

把dlv从`GOPATH`移动到`GOROOT`

```
$ sudo mv $GOPATH/bin/dlv $GOROOT/bin/dlv
```

创建系统证书

```
1 打开"钥匙串访问"
2 打开菜单 钥匙串访问/证书助理/创建证书...
3 名称: dlv-cert 身份类型: 自签名证书 证书类型: 代码签名 并 选择"让我覆盖这些默认值"
4 单击"继续", 有效期(天数): 365 这里你可以自己修改, 我改为3650
5 一路继续下去, 直到看到"指定用于该证书的位置" 钥匙串 选择 "系统" 并单击"创建"按钮
6 重启系统之后, 再打开"钥匙串访问", 选择"系统", 就会看到创建好的"dlv-cert"证书.
7 右键"dlv-cert"证书, 选择"显示简介"->"信任"->"代码签名" 修改为: 始终信任
```

分配证书

```
$ sudo codesign -s "dlv-cert" $GOROOT/bin/dlv
```

如果调试时遇到下面问题：

```
could not launch process: debugserver or lldb-server not found: install XCode's command line tools or lldb-server
```

可以安装一下xcode-select:

```
xcode-select --install
```

## 开始调试

创建个必写的hello world文件：

```
package main

import (
	"fmt"
)

func main() {
	var h string = "こんにちは"
	var w string = "世界"
	fmt.Println(h, w)
}

```

然后开始debug
![开始debug](/images/go-vscode/start-debug.png)

如果是第一次调试，会让你编辑launch.json文件

我们加一个配置：
```
{
    "name": "helloword",
    "type": "go",
    "request": "launch",
    "mode": "debug",
    "env": {},
    "program": "${workspaceRoot}/hello/helloworld.go"
}
```
program是调试文件的地址，${workspaceRoot}是项目的根目录，根据实际情况改

保存，再跑一下debug：
![开始debug](/images/go-vscode/debug.png)

这样就完成了～


