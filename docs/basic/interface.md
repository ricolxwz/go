---
title: Go接口
layout: doc
navbar: true
sidebar: true
aside: true
outline: deep
lastUpdated: true
editLink: true
footer: true
---

# 接口

::: info
默认省略例子框
:::

在计算机科学中, 接口是计算机系统在多个组件共享的边界, 不同的组件能够在边界上交换信息. 接口的本质是引入一个中间层, 调用方可以通过接口和具体实现分离, 解除上下游耦合, 上层的模块不再需要依赖下层的具体模块, 只需要依赖一个约定好的接口.

可移植操作系统接口(Portable Operating SYstem Interface, POSIX)就是一个典型的例子, 它定义了应用程序接口和命令行等标准, 为计算机软件带来了可移植性, 只要操作系统实现了POSIX, 计算机软件就可以直接在不同操作系统上运行. SQL也是接口的一个例子, 当我们使用SQL语句查询数据的时候, 其实不需要关系底层数据库的具体实现, 我们只在乎SQL返回的结果是否符合预期.

## 隐式接口

很多面向对象的语言都有接口这个概念, 例如Java和C#. Java的接口不仅可以定义方法签名, 还可以定义变量, 这些定义的变量可以直接在实现接口的类里面使用. 这里假单介绍一下Java的接口:

```java
public interface MyInterface {
    public String hello = "Hello";
    public void sayHello();
}
```

上述代码定义了一个必须实现的方法`sayHello`和一个会注入到实现类的变量`hello`. 在下面的代码中, `MyInterfaceImpl`实现了`MyInterface`接口:

```java
public class MyInterfaceImpl implements MyInterface {
    public void sayHello() {
        System.out.println(MyInterface.hello);
    }
}
```

Java中的类必须通过上述方式显式地声明实现的接口, 但是在Go语言中实现接口就不是类似的方式. 首先, 我们简单地了解一下在Go语言中如何定义接口. 定义接口需要用到`interface`关键字, 在接口中我们只定义方法签名, 不能包含成员变量, 一个常见的Go语言的接口是这样的:

```go
type error interface {
    Error() string
}
```

如果一个类型需要实现`error`接口, 那么它只需要实现`Error() string`方法, 下面的`RPCError`结构体就是`error`接口的一个实现:

```go
type RPCError struct {
	Code    int64
	Message string
}

func (e *RPCError) Error() string {
	return fmt.Sprintf("%s, code=%d", e.Message, e.Code)
}
```

可以发现, 上述代码根本就没有`error`接口的影子, 这是为什么呢? Go语言中接口的实现都是隐式的, 我们只需要实现接口定义的所有方法就是实现了接口, 在上述代码中, 我们实现了`error`接口定义的`Error() string`方法, 就实现了`error`接口.

我们使用上述`RPCError`结构体的时候并不关系它实现了哪些接口, Go语言只会在传递参数, 返回参数以及变量赋值的时候才会对某个类型是否实现接口进行检查, 这里举几个例子来演示发生接口类型检查的时机.

```go
func main() {
	var rpcErr error = NewRPCError(400, "unknown err") // typecheck1
	err := AsErr(rpcErr) // typecheck2
	println(err)
}

func NewRPCError(code int64, msg string) error {
	return &RPCError{ // typecheck3
		Code:    code,
		Message: msg,
	}
}

func AsErr(err error) error {
	return err
}
```

Go 语言在编译期间对代码进行类型检查，上述代码总共触发了三次类型检查：

- 将`*RPCError`类型的变量赋值给`error`类型的变量`rpcErr`
- 将`*RPCError`类型的变量`rpcErr`传递给签名中参数类型为`error`的`AsErr`函数
- 将`*RPCError`类型的变量从函数签名的返回值类型为`error`的`NewRPCError`函数中返回

从类型检查的过程来看, 编译器仅在需要的时候才检查类型, 类型实现接口时只需要实现接口中的全部方法, 不需要像Java等编程语言中一样显式声明.