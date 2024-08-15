---
title: Go下划线
layout: doc
navbar: true
sidebar: true
aside: true
outline: deep
lastUpdated: true
editLink: true
footer: true
---

# 下划线

在Go中, 下划线`_`是一个特殊标识符. 

## 在`import`中

在`import`语句中, 下划线的作用是仅仅执行该包下的所有`init`函数, 而无法通过包名来调用包中的其他函数.

## 在其他语句中

在其他语句中, 下划线的作用是忽略这个变量.  

::: details
比如导入一个文件, 普通的写法是:
```go
f, err := os.Open("/Users/***/Desktop/text.txt")
```
如果此时不需要直到返回的错误值:
```go
f, _ := os.Open("/Users/***/Desktop/text.txt")
```
此时忽略了`error`变量, 如果不适用这个占位符, 编译器会报错的, 因为`Open`函数有两个返回值.
:::