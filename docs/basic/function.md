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
一般来说, 地址拷贝更为高效, 因为值拷贝的时候传入的值太大的话, 会导致性能降低.
:::