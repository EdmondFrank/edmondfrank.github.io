---
layout: post
title: "Go语言var/init/main的执行顺序"
date: 2016-06-23 18:04:40 +0800
comments: true
categories: 问题集
---

#Go语言var/init/main的执行顺序
执行顺序(降序):

1. var
2. init
3. main

大家也可以亲自动手把下面的代码复制到[Go Playground](https://play.golang.org/)上执行看看!
####示例代码

```go
package main

var someVar = defaultVar()

func init() {
    println("main.init was called")
}

func main() {
    println("main.main was called")
}

func defaultVar() int {
    println("main.var was initialized")
    return 0
}

```

####输出结果如下:

> main.var was initialized

> main.init was called

> main.main was called
