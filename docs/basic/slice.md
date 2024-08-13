---
title: Go切片
layout: doc
navbar: true
sidebar: true
aside: true
outline: deep
lastUpdated: true
editLink: true
footer: true
---

# 切片

Go语言中, 切片不存储任何数据本身, 它们只是对底层数组的引用和一些元数据, 也就是说所有对切片的读写操作实际上是对底层数组的读写操作. 

## 数据结构

指针的数据结构由三部分组成: 指针, 长度, 容量.

![](/assets/basic/55bcd6b0a01cdfa93bec2224db71c9d3.jpg)

- 指针指向数组中切片首个元素的地址
- 长度表示切片中当前包含的元素数量. 这是切片的实际使用部分. 在使用`len`函数的时候, 你会得到切片中元素的实际个数
- 容量是从切片第一个元素开始到其底层数组末端的元素数量. 这个数字表示切片可以增长到的最大长度(不重新分配内存的前提下). 如果新的长度超过了原始容量, 必须分配一个新的底层数组, 并将旧元素复制到新数组中, 这是由Go自动实现的, 这会产生性能成本. 使用`cap`函数可以得到切片容量

## 值类型

切片实现本质上是包含了一个指向底层数组的指针. 切片仍是值类型, 即在赋值的时候, 仍然是创建了一个新的副本, 但是这个副本里面的指针是不会改变的, 所以可以影响原始数据, 实现了类似引用的效果.

::: details
```go
func main() {
    s1 := make([]int, 1, 5)
    fmt.Printf("%p\n", &s1)
    s2 := s1
    fmt.Printf("%p\n", &s2)
    fmt.Println(s1, s2)
    s1[0] = 2
    fmt.Println(s1, s2)
    fmt.Println(&s1[0], &s2[0]) // 检查所指向底层数组的头元素地址
}
```
执行结果为:
```
0xc000010018
0xc000010030
[0] [0]
[2] [2]
0xc0000201e0 0xc0000201e0
```
可以看到两个切片实际上是不同的, 存储在内存中的两个地方. 但是它们所指向的是同一个底层数组.
:::

## 初始化

可以通过以下方法初始化一个切片:

- 未注明大小的数组
- `make`函数
    ```
    make(<type>, <len>, <cap>)
    ```
- 现存数组的切片
    ```
    <arr>[<start>:<end>:<cap>]
    ```

::: details
```go
func main() {
   //1.声明切片
   var s1 []int
   // 2.:=
   s2 := []int{}
   // 3.make()
   var s3 []int = make([]int, 0) // 省略容量, 表示和长度相等
   s7 := make([]int, 0, 8) // 0表示长度, 8表示容量
   // 4.初始化赋值
   var s4 []int = make([]int, 0, 0)
   s5 := []int{1, 2, 3}
   // 5.从数组切片
   arr := [5]int{1, 2, 3, 4, 5}
   var s6 []int
   s6 = arr[1:4]
}
```
:::

## 追加

可以使用`append`内置函数追加切片.

```
append(<slice>, <item1>, <item2>, ...)
```

::: details
```go
func main() {
    var a = []int{1, 2, 3}
    fmt.Printf("slice a : %v\n", a)
    var b = []int{4, 5, 6}
    fmt.Printf("slice b : %v\n", b)
    c := append(a, b...)
    fmt.Printf("slice c : %v\n", c)
    d := append(c, 7)
    fmt.Printf("slice d : %v\n", d)
    e := append(d, 8, 9, 10)
    fmt.Printf("slice e : %v\n", e)
}
```
:::

::: tip
- 可以使用`...`运算符将某个切片展开
    ::: details
    ```go
    s2 := make([]int, 10)
    s3 := []int{1, 2, 3}
    fmt.Printf("slice s3 : %v\n", s3)
    s3 = append(s3, s2...)
    ```
- 若超出原切片的容量, 会重新分配底层数组, 即使原数组并未填满.
    ::: details
    ```go
    func main() {
        data := [...]int{0, 1, 2, 3, 4, 10: 0}
        s := data[:2:3] // s的容量为3
        fmt.Println(s)
        fmt.Println(&s[0], &data[0]) // 比对底层数组起始指针
        s = append(s, 100, 200) // 一次 append 两个值, 超出 s.cap 限制
        fmt.Println(s, data)         // 重新分配底层数组, 与原数组无关
        fmt.Println(&s[0], &data[0]) // 比对底层数组起始指针
    }
    ```
    执行结果为:
    ```
    [0 1]
    0xc00001a180 0xc00001a180
    [0 1 100 200] [0 1 2 3 4 0 0 0 0 0 0]
    0xc0000201e0 0xc00001a180
    ```
    可以看到超出容量的扩容后指向的底层数组不是同一个了.
    :::
- 容量经过重新分配之后会变成原来的两倍
:::

## 拷贝

`copy`函数用于在两个切片之间复制数据. 复制部分的长度以长度较小的切片为准. 

```
copy(<dst_slice>, <src_slice>)
```

::: details
```go
func main() {
    data := [...]int{0, 1, 2, 3, 4, 5, 6, 7, 8, 9}
    fmt.Println("array data : ", data)
    s1 := data[8:]
    s2 := data[:5]
    fmt.Printf("slice s1 : %v\n", s1)
    fmt.Printf("slice s2 : %v\n", s2)
    copy(s2, s1)
    fmt.Printf("copied slice s1 : %v\n", s1)
    fmt.Printf("copied slice s2 : %v\n", s2)
    fmt.Println("last array data : ", data)
}
```
执行结果为:
```
array data :  [0 1 2 3 4 5 6 7 8 9]
slice s1 : [8 9]
slice s2 : [0 1 2 3 4]
copied slice s1 : [8 9]
copied slice s2 : [8 9 2 3 4]
last array data :  [8 9 2 3 4 5 6 7 8 9]
```
再来个例子:
```go
func main() {
    s1 := []int{1, 2, 3}
    s2 := []int{4, 5, 6, 7}
    copy(s2, s1)
    fmt.Println(s1)
    fmt.Println(s2)
    s3 := []int{1, 2, 3}
    s4 := []int{4, 5, 6, 7}
    copy(s3, s4)
    fmt.Println(s3)
    fmt.Println(s4)
}
```
执行结果为:
```
[1 2 3]
[1 2 3 7]
[4 5 6]
[4 5 6 7]
```
:::

::: tip
- 应及时将所需数据`copy`到较小的切片, 以便释放超大号底层数组内存
- `copy`函数中的两个切片可以指向同一底层数组, 也可以指向不同的底层数组
:::

## 遍历

和[数组的遍历](/basic/array#遍历)相同.

## 调整大小

切片可以进行伸缩调整大小.

::: details
```go
func main() {
    var a = []int{1, 3, 4, 5}
    fmt.Printf("slice a : %v , len(a) : %v\n", a, len(a))
    b := a[1:2]
    fmt.Printf("slice b : %v , len(b) : %v\n", b, len(b))
    c := b[0:3]
    fmt.Printf("slice c : %v , len(c) : %v\n", c, len(c))
}
```
执行结果:
```
slice a : [1 3 4 5] , len(a) : 4
slice b : [3] , len(b) : 1
slice c : [3 4 5] , len(c) : 3
```
:::

## 字符串切片

字符串底层就是一个byte数组, 因此, 也可以进行切片操作.

::: details
```go
func main() {
    str := "hello world"
    s1 := str[0:5]
    fmt.Println(s1)

    s2 := str[6:]
    fmt.Println(s2)
}
```
执行结果:
```
hello
world
```
:::

修改字符串详情见[这里](/basic/type#修改字符串).