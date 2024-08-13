---
title: Go变量和常量
layout: doc
navbar: true
sidebar: true
aside: true
outline: deep
lastUpdated: true
editLink: true
footer: true
---

# 变量和常量

## 变量

Go是一种强类型语言, 意味着每个变量必须经过类型声明才可以进行使用.同一作用域内不支持重复声明. 

Go语言的变量声明格式为:

```go
var <name> <type>
```

### 批量声明

每声明一个变量就需要写一个`var`会比较繁琐, Go还支持批量变量声明.

::: details
```go
var (
    a string
    b int
    c bool
    d float32
)
```
:::

### 变量初始化

Go语言在声明变量的时候, 会自动对变量对应的内存区域进行初始化操作. 每个变量会被初始化成其类型的默认值. 如, 整型和浮点型变来概念的默认值为`0`, 字符串变量的默认值为空字符串. 布尔类型变量的默认值为`false`. 切片, 函数, 指针变量的默认值为`null`.

当然, 我们也可以在声明变量的时候为其指定初始值:

```go
var <name> <type> = <expression>
```

### 类型推导

有时候我们会将变量的类型省略, 这个时候编译器会根据等号右边的值来推导变量的类型完成初始化.

::: details
```go
var name = "ricolxwz"
var sex = 1
```
:::

### 短变量声明

在函数内部, 可以使用更简单的`:=`的方式声明并初始化变量.

::: details
```go
func main() {
    n := 10
    m := 20
}
```
:::

::: warning
- 使用短变量声明无法显式指定类型
- 不能在函数外使用短变量声明
:::

### 匿名变量

详情见[下划线](/basic/underline).

## 常量

常量是恒定不变的值.

### 常量初始化

常量定义的时候必须赋值.

::: details
```go
const pi = 3.1415
```
:::

### 批量初始化

::: details
```go
const (
    pi = 3.1415
    e = 2.7182
)
```
:::

::: tip
在批量初始化中, 若省略了值则表示和上面一行的值相同:
::: details
```go
const (
    n1 = 100
    n2
    n3
)
```
:::

### `iota`

`iota`是Go语言的常量计数器, 只能在常量的表达式中使用. `iota`在`const`关键字出现的时候被重置为`0`. `const`声明中每增加一行常量声明将会使`iota`计数一次. 

::: details EXAMPLE
一个基础的例子:
```go
const (
    n1 = iota //0
    n2        //1
    n3        //2
    n4        //3
)
```
可以使用`_`跳过某些值:
```go
const (
    n1 = iota //0
    n2        //1
    _
    n4        //3
)
```
中间还可以插队:
```go
const (
    n1 = iota //0
    n2 = 100  //100
    n3 = iota //2
    n4        //3
)
const n5 = iota //0
```
:::

## 作用域

Go语言变量和常量的作用域主要有全局和局部两种. 

- 全局作用域: 若变量/常量声明在在当前文件的最外层, 则为全局变量/常量
- 局部作用域: 若变量/常量明在函数内/块内, 则为局部变量常量/常量

::: warning
全局变量/常量可以在整个包内使用, 即a.go中定义个全局变量/常量能直接在b.go中使用, 只要a.go和b.go属于同一个包. 这不同于Python, Python中一个模块(即一个文件)的全局变量/常量要在另一个模块使用, 需要用`import`导入.
:::
