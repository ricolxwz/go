---
title: Go类型
layout: doc
navbar: true
sidebar: true
aside: true
outline: deep
lastUpdated: true
editLink: true
footer: true
---

# 类型

| 类型       | 长度(字节) | 默认值 | 说明                                              |
|------------|------------|--------|---------------------------------------------------|
| bool       | 1          | false  |                                                   |
| byte       | 1          | 0      | uint8                                             |
| rune       | 4          | 0      | Unicode Code Point, int32                         |
| int, uint  | 4或8      | 0      | 32 或 64 位                                       |
| int8, uint8| 1          | 0      | -128 ~ 127, 0 ~ 255, byte是uint8 的别名          |
| int16, uint16| 2        | 0      | -32768 ~ 32767, 0 ~ 65535                         |
| int32, uint32| 4        | 0      | -21亿~ 21亿, 0 ~ 42亿, rune是int32 的别名        |
| int64, uint64| 8        | 0      |                                                   |
| float32    | 4          | 0.0    |                                                   |
| float64    | 8          | 0.0    |                                                   |
| complex64  | 8          |        |                                                   |
| complex128 | 16         |        |                                                   |
| uintptr    | 4或8      |        | 以存储指针的 uint32 或 uint64 整数                |
| array      |            |        | 值类型                                            |
| struct     |            |        | 值类型                                            |
| string     |            | ""     | UTF-8 字符串                                      |
| slice      |            | nil    | 引用类型                                          |
| map        |            | nil    | 引用类型                                          |
| channel    |            | nil    | 引用类型                                          |
| interface  |            | nil    | 接口                                              |
| function   |            | nil    | 函数                                              |

## 字符串

Go语言里的字符串的内部实现使用UTF-8编码. 字符串的值为双引号`"`中的内容, 可以在Go语言的源码中直接添加非ASCII码的字符. 

### 多行字符串

Go语言定义一个多行字符串, 可以用到反引号.

### 修改字符串

字符串的底层是一个`byte`数组, 使用的是UTF-8编码. 可以和`[]byte`类型相互转化. 字符串是无法修改的, 如果要修改字符串, 必须将其转换成可以修改的`[]byte`或`rune[]`. 无论是哪种转换, 都会重新分配内存, 并复制字节数组.

::: details
```go
func changeString() {
    s1 := "hello"
    // 强制类型转换
    byteS1 := []byte(s1)
    byteS1[0] = 'H'
    fmt.Println(string(byteS1))

    s2 := "博客"
    runeS2 := []rune(s2)
    runeS2[0] = '狗'
    fmt.Println(string(runeS2))
}
```
:::


## byte和rune类型

组成每个字符串的元素叫作"字符", 可以通过遍历或者单个获取字符串元素得到字符. 字符用单引号`'`包裹. Go语言的字符有以下两种:

- uint8/byte类型: 表示了一个ASCII字符
- rune类型: 表示了一个UTF-8字符

::: warning
- 不能按照字节去遍历一个包含中文的字符串
    ::: details EXAMPLE
    例如, 遍历一段含有中文的字符串:
    ```go
    // 遍历字符串
    func traversalString() {
        s := "pprof.cn博客"
        for i := 0; i < len(s); i++ { //byte
            fmt.Printf("%v(%c) ", s[i], s[i])
        }
        fmt.Println()
    }
    ```
    输出:
    ```
    112(p) 112(p) 114(r) 111(o) 102(f) 46(.) 99(c) 110(n) 229(å) 141() 154() 229(å) 174(®) 162(¢)
    ```
    可以看到, 这里乱码了. 因为我们按照字节的方式取遍历一个包含中文的字符串, 而UTF-8下一个中文汉字由3-4个字节组成, 所以出现了乱码. 我们需要使用`range`遍历, 因为它默认按照rune类型处理:
    ```go
    // 遍历字符串
    func traversalString() {
        s := "pprof.cn博客"
        for _, r := range s { //rune
            fmt.Printf("%v(%c) ", r, r)
        }
        fmt.Println()
    }
    ```
    输出:
    ```
    112(p) 112(p) 114(r) 111(o) 102(f) 46(.) 99(c) 110(n) 21338(博) 23458(客)
    ```
    :::
:::

## 类型转换

Go语言中只有强制类型转换, 没有隐式类型转换. 

```go
T<expression>
```