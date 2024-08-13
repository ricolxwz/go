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

Go语言中, 切片不存储任何数据本身, 它们只是对底层数组的引用. 

## 数据结构

指针的数据结构由三部分组成: 指针, 长度, 容量.

![](/assets/basic/55bcd6b0a01cdfa93bec2224db71c9d3.jpg)

- 指针指向数组中切片首个元素的地址
- 长度表示切片中当前包含的元素数量. 这是切片的实际使用部分. 在使用`len`函数的时候, 你会得到切片中元素的实际个数
- 容量是从切片第一个元素开始到其底层数组末端的元素数量. 这个数字表示切片可以增长到的最大长度(不重新分配内存的前提下). 如果新的长度超过了原始容量, 必须分配一个新的底层数组, 并将旧元素复制到新数组中, 这是由Go自动实现的, 这会产生性能成本. 使用`cap`函数可以得到切片容量