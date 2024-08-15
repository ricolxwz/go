---
title: Go函数
layout: doc
navbar: true
sidebar: true
aside: true
outline: deep
lastUpdated: true
editLink: true
footer: true
---

# 函数

## 特点

- 支持不定参数
- 支持多返回值
- 支持命名返回参数
- 支持匿名函数和闭包
- 支持函数嵌套
- 函数是"一等公民", 支持将函数赋值给变量
- 不能在同一个包内定义两个同名函数
- 不支持重载
- 不支持默认参数

## 声明

函数声明包含一个函数名, 参数列表, 返回值和函数体.

::: details
```go
func test(x, y int, s string) (int, string) {
    // 类型相同的相邻参数, 参数类型可合并. 多返回值必须用括号.
    n := x + y          
    return n, fmt.Sprintf(s, n)
}
```
:::

## 参数

函数定义时候的参数是函数的局部变量. 

函数传递有两种方式, 值传递和引用传递, 无论是哪种传递方式, 传递给函数的都是变量的副本. 只不过, 值传递是值的拷贝, 引用传递是地址的拷贝.

::: tip
- 一般来说, 地址拷贝更为高效, 因为值拷贝的时候传入的值太大的话, 会导致性能降低.
- 函数是第一类公民, 可以作为参数传递
    ::: details
    ```go
    func test(fn func() int) int {
        return fn()
    }
    func main() {
        s1 := test(func() int { return 100 })
        println(s1) // 100
    }
    ```
    :::
- 可以将具有复杂签名的函数定义为函数类型, 传参的时候声明类型就用这个函数类型
    ::: details
    以下代码中, 函数签名`func (s string, x, y int) string`过于复杂, 将该函数作为参数传递声明类型的不怎么好写, 所以将其定义为一个类型.
    ```go
    type FormatFunc func(s string, x, y int) string
    func format(fn FormatFunc, s string, x, y int) string {
        return fn(s, x, y)
    }
    func main() {
        s2 := format(func(s string, x, y ing) string {
            return fmt.Sprintf(s, x, y)
        }, "%d, %d", 10, 20)
    }
    println(s2) // 10, 20
    ```
:::

### 变长参数

一个函数的最后一个参数可以是变长参数. 一个函数最多可以有一个变长参数, 一个变长参数在函数内部被视为一个切片. 变长参数在声明的时候必须在它声明的元素类型前面前置三个点`...`, 表示这是一个变长参数. 这个`...`既可以表示解包又可以表示打包, 在参数中表示的是将传入的参数打包成一个切片.

::: details
```go
func Sum(values ...int64) (sum int64) {
	sum = 0
	for _, v := range values {
		sum += v
	}
	return
}

func main() {
	a0 := Sum()
	a1 := Sum(2)
	a3 := Sum(2, 3, 5)
	// 上面三行和下面三行是等价的, 下面是现将切片解包, 然后传入
	b0 := Sum([]int64{}...) // <=> Sum(nil...)
	b1 := Sum([]int64{2}...)
	b3 := Sum([]int64{2, 3, 5}...)
	fmt.Println(a0, a1, a3) // 0 2 10
	fmt.Println(b0, b1, b3) // 0 2 10
}
```
:::

## 返回值

::: tip
- 返回值不能用容器对象接受, 只能用多个变量, 或者使用下划线忽略
:::

::: warning
带有表达式的`return`语句不是原子指令! 如`return <expression>`, 会先给返回值赋予`<expression>`的结果, 然后再返回. 这会影响到延迟调用, 因为在延迟调用中, 有可能会修改返回值的值. 在[延迟调用](#延迟调用)中, 先给返回值赋值, 然后再调用`defer`表达式, 最后才是真正的返回. 具体的例子见[这里](#延迟调用返回例子). 
:::

### 无返回值

这是最简单的形式, 函数不返回任何值, 也不用在函数内写`return`语句.

::: details
```go
func greet(name string) {
    fmt.Println("Hello,", name)
}
```
:::

### 单个返回值

函数返回一个单一的返回值. 返回值类型在函数签名中声明, `return`语句用来返回这个值, 必须写.

::: details
```go
func square(x int) int {
    return x * x
}
```
:::

### 多个返回值

函数返回多个值, `return`语句看情况用来返回值.

#### 无命名返回值

必须用`return`语句返回无命名返回值.

::: details
```go
func divide(a, b float64) (float64, error) {
    if b == 0 {
        return 0, fmt.Errorf("division by zero")
    }
    return a / b, nil
}
```
:::

#### 命名返回值

函数返回值可以命名, 命名的返回值在函数体内可以直接使用(类似于局部变量), 可以借此简化`return`语句, 返回各个命名返回值的当前值, 这种返回称为"裸返回", 但是`return`是必须写的.

::: details
```go
func divide(a, b float64) (quotient, remainder float64) {
    quotient = a / b
    remainder = a - quotient*b
    return // 返回 quotient 和 remainder
}
```
:::

::: warning
命名返回参数可被同名局部变量隐蔽, 此时需要显式返回.
::: details
```go
func add(x, y int) (z int) {
    { // 不能在一个级别, 引发 "z redeclared in this block" 错误
        var z = x + y
        // return   // Error: z is shadowed during return
        return z // 必须显式返回
    }
}
```
:::

## 匿名函数

匿名函数是指不需要定义函数名的一种函数实现方式. Go中的匿名函数由一个不带函数名的函数声明和函数体组, 然后可以将其赋值给一个变量, 直接调用或者作为参数传递.

::: details
```go
func main() {
    getSqrt := func(a float64) float64 {
        return math.Sqrt(a)
    }
    fmt.Println(getSqrt(4))
}
```
:::

## 延迟调用

Go中, 延迟调用是一种在函数执行结束时, 延迟执行某些代码的机制. 延迟调用通常用于在函数返回前执行清理工作, 比如关闭文件, 解锁资源或者输出调试信息.

`defer`关键字用于处理延迟调用, 后面应该跟着一个函数, 当包含多个`defer`的函数被调用时, 所有的`defer`语句将按照后进先出(LIFO)的顺序执行.

::: details
```go
func main() {
    fmt.Println("Start")

    defer fmt.Println("Middle")
    defer fmt.Println("End")

    fmt.Println("Done")
}
```
执行结果:
```
Start
Done
End
Middle
```
:::

`defer`语句执行的时间非常关键. 当程序遇到`defer`语句时, `defer`后面的函数将被注册, 但是不会立即执行. 相反, 它会被推入一个栈中, 等待函数即将返回时执行. 当函数即将返回的时候(无论是正常返回还是异常情况返回), 会按照LIFO的顺序执行已经注册的延迟执行函数. 

::: warning 
前面提到了, 带有表达式的`return`语句不是原子指令! 如`return <expression>`, 会先给返回值赋予`<expression>`的结果, 然后再返回, 这会影响到延迟调用. 在延迟调用中, 先给返回值赋值, 然后再调用`defer`表达式, 最后才是真正的返回.
::: details {#延迟调用返回例子}
先来看例子1:
```go
func f() (result int) {
    defer func() {
        result++
    }()
    return 0
}
```
含有表达式的`return`语句不是原子指令, 会先将`0`赋值给`result`, 然后再执行`defer`, 执行`defer`的过程中, 修改了返回值`result`, 然后最终才是真正的返回, 所以结果应该是`1`. 实际上, 可以将上面的`return`语句拆成两个部分, 来理解它:
```
返回值 = xxx
调用defer函数
空的return
```
再来看例子2:
```go
func f() (r int) {
     t := 5
     defer func() {
       t = t + 5
     }()
     return t
}
```
含有表达式的`return`语句不是原子指令, 会先将`t`的值赋值给`r`, 即`5`, 然后再执行`defer`, 执行`defer`的过程中, 修改的是局部变量`t`, 而不是返回变量`r`, 所以结果应该是`5`.

再来看例子3:
```go
func f() (r int) {
    defer func(r int) {
          r = r + 5
    }(r)
    return 1
}
```
含有表达式的`return`语句不是原子指令, 会先将`1`赋值给`r`. 然后再执行`defer`, 执行`defer`的过程中, 传入了返回值`r`的值, 即`1`, 由于传递的是值, 所以`defer`调用函数内部的`r`和外面的`r`不是同一个, 只是一个拷贝, 所以实际上无法修改返回值`r`的值, 最终返回的是`1`.
:::

## 闭包

Go语言是支持闭包的, 这里这是简单讲一下在Go语言中是如何实现闭包的.

```go
func f(i int) func() int {
    return func() int {
        i++
        return i
    }
}
```

函数`f`返回了一个函数, 返回的函数就是一个闭包. 这个函数中本身是没有定义变量`i`的, 而是引用了它所在的环境(函数`f`)中的变量`i`.

```
c1 := f(0)
c2 := f(0)
c1()    // reference to i, i = 0, return 1
c2()    // reference to another i, i = 0, return 1
```

`c1`和`c2`引用的是不同的环境, 在调用`i++`时修改的不是同一个`i`, 因此两次的输出都是`1`. 即函数`f`每被调用一次, 就会形成一个新的环境, 对应的闭包中, 函数是同一个函数, 环境却是引用不同的环境.

一般在函数中的局部变量都是在栈中分配的, 当函数被调用的时候, 会在栈中创建一个新的帧, 当函数执行完毕的时候, 函数的栈帧会弹出, 所有在这个栈帧中分配的局部变量都会被销毁. 因此, 如果函数`f`返回一个闭包, 这个闭包引用了函数`f`中的局部变量`i`, 那么在`f`返回后, 闭包中引用的`i`将指向一个已经失效的内存位置, 这个位置属于已经被销毁的栈帧. 为了避免上述问题, 闭包中引用的变量通常会被分配到堆上而不是栈熵. 这样, 即使`f`返回, 变量`i`依然存在.