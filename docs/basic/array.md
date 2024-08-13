---
title: Go数组
layout: doc
navbar: true
sidebar: true
aside: true
outline: deep
lastUpdated: true
editLink: true
footer: true
---

# 数组

Go语言的数组较其他语言有很大不同.

## 特点

### 固定长度

Go数组的长度在声明的时候就是固定的, 不能在运行的时候改变. 数组的产度是数组类型的一部分, 这意味着`[5]int`和`[10]int`是两种不同的类型.

### 值类型

Go数组是值类型, 不是引用类型. 这意味着当你将一个数组赋值给另一个数组的时候, 会复制整个数组的内容, 而不是仅仅复制一个引用. 这也意味着传递数组给函数的时候, 函数接收到的是数组的副本, 而不是原数组的引用.

::: details
```go
arr1 := [3]int{1, 2, 3}
arr2 := arr1    // 复制数组
arr2[0] = 10    // 修改arr2不会影响arr1
```
:::

如果要确保修改的是原数组, 可以使用数组指针.

::: details
```go
// 函数接受一个指向包含3个元素的int数组的指针
func modifyArray(arr *[3]int) {
    (*arr)[0] = 10  // 修改数组的第一个元素
    (*arr)[1] = 20  // 修改数组的第二个元素
    (*arr)[2] = 30  // 修改数组的第三个元素
}

func main() {
    arr := [3]int{1, 2, 3}  // 声明并初始化一个数组
    fmt.Println("Before modification:", arr)

    modifyArray(&arr)  // 将数组的指针传递给函数
    fmt.Println("After modification:", arr)
}
```
:::

### 数组的比较

Go数组可以用`==`运算符进行比较, 但前提是两个数组的类型(注意长度是类型的一部分)必须相同. 数组的比较是逐元素比较, 只有所有元素都相等的时候, 两个数组才被认为相等.

### 内存布局

Go数组中的元素会连续存储在内存中.

### 访问越界

访问越界, 会触发panic.

## 初始化

Go数组初始化的方式有多种.

### 一维数组

::: details
```go
// 全局
var arr0 [5]int = [5]int{1, 2, 3}
var arr1 = [5]int{1, 2, 3, 4, 5}
var arr2 = [...]int{1, 2, 3, 4, 5, 6}
var str = [5]string{3: "hello world", 4: "tom"}
// 局部
a := [3]int{1, 2}           // 未初始化元素值为 0
b := [...]int{1, 2, 3, 4}   // 通过初始化值确定数组长度
c := [5]int{2: 100, 4: 200} // 使用索引号初始化元素
d := [...]struct {
    name string
    age  uint8
}{
    {"user1", 10}, // 可省略元素类型
    {"user2", 20}, // 别忘了最后一行的逗号
}
```
:::

### 多维数组

::: details
```go
// 全局
var arr0 [5][3]int
var arr1 [2][3]int = [...][3]int{{1, 2, 3}, {7, 8, 9}}
// 局部
a := [2][3]int{{1, 2, 3}, {4, 5, 6}}
b := [...][2]int{{1, 1}, {2, 2}, {3, 3}} // 第 2 纬度不能用 "..."
```
:::

## 遍历

Go数组主要使用两种方法遍历:

- 使用索引遍历
- 使用`range`遍历

### 使用索引遍历

以二维数组为例, 需要使用两层`for`循环, 通过索引访问这个元素.

::: details
```go
arr := [2][3]int{
    {1, 2, 3},
    {4, 5, 6},
}

for i := 0; i < len(arr); i++ {  // 遍历每一行
    for j := 0; j < len(arr[i]); j++ {  // 遍历每一行的每一个元素
        fmt.Println(arr[i][j])  // 打印每个元素
    }
}
```
:::

### 使用`range`遍历

使用`range`关键字可以使代码更加简洁. `range`返回的是每次迭代的索引和该索引处的值. 以二维数组为例, 需要使用两层`range`结构.

::: details
```go
arr := [2][3]int{
    {1, 2, 3},
    {4, 5, 6},
}

for i, row := range arr {  // 遍历每一行
    for j, value := range row {  // 遍历每一行的每一个元素
        fmt.Printf("arr[%d][%d] = %d\n", i, j, value)
    }
}
```
:::