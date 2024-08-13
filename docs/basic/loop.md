---
title: Go循环
layout: doc
navbar: true
sidebar: true
aside: true
outline: deep
lastUpdated: true
editLink: true
footer: true
---

# 循环

Go的`for`循环支持三种循环方式, 包括其他语言中`while`的语法.

- `for <init>; <condition>; <post> {}`: 类似于其他语言的`for`循环
- `for <condition> {}`: 类似于其他语言的`while`循环
- `for {}`: 类似于其他语言的`while (true) {}`循环

`for`语句的执行过程如下: 

1. `<init>`初始赋值
2. 判别赋值表达式`<init>`是否满足给定`<condition>`条件
    - 若其值为真, 满足循环条件, 则执行循环体内语句, 然后执行`<post>`, 进入下轮循环
    - 若其值非真, 不满足循环条件, 则终止循环

::: details
```go
s := "abc"
for i, n := 0, len(s); i < n; i++ { // 常见的 for 循环，支持初始化语句。
    println(s[i])
}
n := len(s)
for n > 0 {                // 替代 while (n > 0) {}
    println(s[n])        // 替代 for (; n > 0;) {}
    n-- 
}
for {                    // 替代 while (true) {}
    println(s)            // 替代 for (;;) {}
}
```
:::

::: warning
`<init>`内初始化的变量只在块内有效, 是一个局部变量, 在块内会覆盖同名的全局变量.
::: details
```go
func main() {

   var b int = 15
   var a int

   numbers := [6]int{1, 2, 3, 5}

   /* for 循环 */
   for a := 0; a < 10; a++ {
      fmt.Printf("a 的值为: %d\n", a)
   }

   for a < b {
      a++
      fmt.Printf("a 的值为: %d\n", a)
      }

   for i,x:= range numbers {
      fmt.Printf("第 %d 位 x 的值 = %d\n", i,x)
   }   
}
```
执行结果:
```
a 的值为: 0
a 的值为: 1
a 的值为: 2
a 的值为: 3
a 的值为: 4
a 的值为: 5
a 的值为: 6
a 的值为: 7
a 的值为: 8
a 的值为: 9
a 的值为: 1
a 的值为: 2
a 的值为: 3
a 的值为: 4
a 的值为: 5
a 的值为: 6
a 的值为: 7
a 的值为: 8
a 的值为: 9
a 的值为: 10
a 的值为: 11
a 的值为: 12
a 的值为: 13
a 的值为: 14
a 的值为: 15
第 0 位 x 的值 = 1
第 1 位 x 的值 = 2
第 2 位 x 的值 = 3
第 3 位 x 的值 = 5
第 4 位 x 的值 = 0
第 5 位 x 的值 = 0
```
:::

## `range`

Go的`range`类似迭代器操作, 每次迭代返回一个`<index>, <value>`或`<key>, <value>`. `for`循环的`range`格式可以对切片, 映射, 数组, 字符串等进行迭代循环, 格式如下:

```go
for <key>/<index>, <value> := range <container> {
    ...
}
```

| 类型 | 第一个值    | 第二个值         | 
|-----------|---------------|-------------------|
| 字符串    | `index`         | `s[index]`  |
| 数组/切片 | `index`       | `s[index]`          |
| 映射       | `key`           | `m[key]`            |
|  通道  | `element`       |                   |

::: tip
可用下划线省略不想要的值.
::: details
```go
func main() {
    s := "abc"
    // 忽略 2nd value, 支持 string/array/slice/map
    for i := range s {
        println(s[i])
    }
    // 忽略 index
    for _, c := range s {
        println(c)
    }
    // 忽略全部返回值, 仅迭代
    for range s {

    }
    m := map[string]int{"a": 1, "b": 2}
    // 返回 (key, value)
    for k, v := range m {
        println(k, v)
    }
}
```
:::

::: warning
请注意值类型/引用类型使用`range`之间的区别, `range`会复制一个相同的变量然后从复制之后的变量提取`<index>/<key>`, `<value>`用于遍历. 循环内部的同名变量不是复制的, 是原始的变量; 循环内部的`<index>/<key>`, `<value>`来自复制之后的变量.
::: details
对于引用类型使用`range`:
```go
func main() {
    s := []int{1, 2, 3, 4, 5}
    for i, v := range s { // 复制 struct slice { pointer, len, cap }用于提取i, v
        if i == 0 {
            s = s[:3]  // 对原始切片的修改
            s[2] = 100 // 对底层数据的修改
        }
        println(i, v)
    }
}
```
执行结果:
```
0 1
1 2
2 100
3 4
4 5
```
对于值类型使用`range`:
```go
func main() {
    a := [3]int{0, 1, 2}
    for i, v := range a { // index, value 都是从复制品中取出
        if i == 0 { // 在修改前, 我们先修改原数组
            a[1], a[2] = 999, 999
            fmt.Println(a) // 确认修改有效, 输出 [0, 999, 999]
        }
        a[i] = v + 100 // 使用复制品中取出的 value 修改原数组
    }
    fmt.Println(a) // 输出 [100, 101, 102]
}
```
执行结果:
```
[0 999 999]
[100 101 102]
```
:::

## `goto`/`break`/`continue`

- 三个语句都可以配合标签使用
- 标签名区分大小写, 定义后不使用会造成编译错误
- `continue`和`break`配合标签可用于多层循环跳出
- `goto`是调整执行位置, 一般不常用