title: GO环境篇-从零开始的Docker环境搭建
date: 2021-11-27 10:33:16 
tags: [golang,docker]  
categories: golang
---

> 从Dockerfile开始的环境搭建

# 编写dockerfile

## step1 明确dockerfile要做的事：

* 选择环境包，如centos
* 安装基本软件binutils/vim/gdb/git/wget等
* 选择golang版本，下载，安装
* 安装调试工具delve

## step2 把清单编写出来

```
FROM centos
RUN yum install binutils -y \
    && yum install vim -y \
    && yum install gdb -y \
    && yum install git -y \
    && yum install wget -y

RUN wget https://dl.google.com/go/go1.14.12.linux-amd64.tar.gz  \
    && tar -C /usr/local -zxvf go1.14.12.linux-amd64.tar.gz \
    && rm /go1.14.12.linux-amd64.tar.gz

ENV PATH /usr/local/go/bin:$PATH
ENV GOPATH /home
ENV GOROOT /usr/local/go
ENV GOPROXY goproxy.cn

RUN git clone https://github.com/go-delve/delve \
&& cd delve/ \
&& go build ./cmd/dlv/ \
&& cp ./dlv /usr/local/bin
```


# build镜象

## 给dockerimage想个名字，如go14dev:

```
docker build -t go14dev .
```

## 过程中可能会遇到问题:

* 如`git clone delve`超时，这时可以在外部把delve下载到当前目录，copy进去执行。
最后一句换成：

```
#RUN git clone https://github.com/go-delve/delve \delve/cmd/dlv
COPY delve ./delve
RUN  cd delve/ \
&& go build ./cmd/dlv/ \
&& cp ./dlv /usr/local/bin
```

* build dlv报错

```
 > [6/7] RUN go build ./cmd/dlv:
#10 0.150 qemu-x86_64: Could not open '/lib64/ld-linux-x86-64.so.2': No such file or directory
```

可以在build命令加上`--platform linux/x86_64`

变成：

```
docker build --platform linux/x86_64 -t go14dev .
```

# run

## 把镜象跑起来

```
docker run -it --rm go14dev bash
```

写个main.go测试一下：

```
package main

import (
	"fmt"
)

func main() {
	fmt.Println("hello world")
}

```

```
$go run main.go
>hello world
```

## 把本地文件挂载进去

docker环境里的文件重启就会消失，所以我们项目一般是挂载上去，加上`-v localpath:dockerpath`

如：

```
docker run -v ~/code/web:/data/web  -it  --rm go14dev bash
```

进入环境后，`ls -lah /data/web`就能看到挂载进去的文件了


# 推送镜象到docker.hub.com

## login

```
docker login -u {username}
```

## 打tag:

```
docker tag go14dev:latest go14dev
```

## push

```
docker push {username}/go14dev:latest
```