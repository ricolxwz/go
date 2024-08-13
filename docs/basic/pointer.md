---
title: Go指针
layout: doc
navbar: true
sidebar: true
aside: true
outline: deep
lastUpdated: true
editLink: true
footer: true
---

# 指针

区别于C/C++中的指针, Go中的指针不能进行偏移运算, 是安全指针. Go语言中的函数传参全部都是值拷贝, 当我们想要在函数内部修改外部的变量的时候, 可以创建一个指向该变量地址的指针变量. 传递数据时使用指针, 而不是拷贝数据. Go语言的指针非常简单, 只需要知道两个符号: `&`(取地址)和`*`(根据地址取值).

## 指针地址

每个变量在运行的时候都有一个地址, 这个地址代表存放在内存中的位置, 取变量指针的语法如下:

```
<ptr> := &v
```

::: details
```go
func main() {
    a := 10
    b := &a
    fmt.Printf("a:%d ptr:%p\n", a, &a) // a:10 ptr:0xc00001a078
    fmt.Printf("b:%p type:%T\n", b, b) // b:0xc00001a078 type:*int
    fmt.Println(&b)                    // 0xc00000e018
}
```
:::

## 指针类型

Go语言中的int, float, bool, string, array, struct都有对应的指针类型, 如*int, *int64, *string等.

## 指针取值

对于变量使用`&`操作符后可以获得这个变量的指针, 然后可以通过`*`操作, 也就是指针取值.

::: details
```go
func main() {
    //指针取值
    a := 10
    b := &a // 取变量a的地址，将指针保存到b中
    fmt.Printf("type of b:%T\n", b)
    c := *b // 指针取值（根据指针去内存取值）
    fmt.Printf("type of c:%T\n", c)
    fmt.Printf("value of c:%v\n", c)
}
```
执行结果:
```
type of b:*int
type of c:int
value of c:10
```
:::

## 空指针

当一个指针被定义后没有分配到任何变量的时候, 它的值为`nil`.

## `new`函数

`new`用于分配内存. 它的返回值是当前类型的变量的指针, 变量会被初始化为默认值.

::: details
```go
func main() {
    var a *int
    a = new(int)
    *a = 10
    fmt.Println(*a)
}
```
:::

## `make`函数

`make`也是用于内存分配的. 区别于`new`, 它只用于切片, 映射, 通道的创建, 且返回值就是这三个类型本身, 而不是它们的指针类型. 

::: details
```go
func main() {
    var b map[string]int
    b = make(map[string]int, 10)
    b["测试"] = 100
    fmt.Println(b)
}
```
:::