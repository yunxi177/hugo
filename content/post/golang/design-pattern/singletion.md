+++
title = "golang设计模式之单例模式"
date = 2021-06-19T17:35:46+08:00
tags = ["golang", "设计模式"]
categories = ["golang", "设计模式"]
draft = false
commentId="golang-design-pattern-singletion"

+++

日常开发中经常会遇到单例模式的使用场景，单例模式可以保证我们初始化出来的结构体只有一个，在一些请求上下文，mysql 连接池..场景经常有着不可估量的作用。  

在 golang 开发中，我们应当如何去设计单例模式呢？

# 常见的错误书写方式

相信如果你对 PHP 写的比较多的情况下经常会写出下面代码一样的单例模式

```go
package main

type Singleton struct {
}

var ins *Singleton

func GetIns() *Singleton {
	if ins == nil {
		ins = &Singleton{}
	}

	return ins

}

```

那上述代码，在正常开发存在什么样的问题呢？上述代码如果用于 PHP 项目是没有任何问题的，因为 PHP 本事是请求隔离的，因而也不会存在并发的问题，但是如果放在常驻内存类型的语言中就会出现并发性不安全问题。

接下来考虑一个场景，在高并发场景下，同时又两个业务都执行到 `ins == nil` 而此时的 ins 确实没有实例化，那程序将会如何执行呢？答案显而易见，两个业务都会实例化出自己的 ins 结构体，这显然不符合我们的程序预期。

# 如何解决

golang 标准库`sync`中找到了`Once`类型。它能保证某个操作仅且只执行一次。有了他我们就可以很方便的解决并发的问题了接下看看代码示例：

```go
package main

import "sync"

type Singleton struct {
}

var ins *Singleton
var once sync.Once

func GetIns() *Singleton {
	once.Do(func() {
		ins = &Singleton{}
	})
	return ins

}
```

这样就可以利用 go 语言的特性，比较方便的实现单例模式了



# Conclusion

到此，golang 的单例模式就完成了，在 golang 的开发中，我们最常考虑的就是,并发安全问题，是需要我们经常考虑的问题，编程是一件非常复杂的事情，只有深入的了细节，又能把控的了全局，才可以在开发过程中无往不利。

