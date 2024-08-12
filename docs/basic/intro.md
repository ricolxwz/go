---
title: Go简介
layout: doc
navbar: true
sidebar: true
aside: true
outline: deep
lastUpdated: true
editLink: true
footer: true
---

# 简介

Go语言是Google开发的编程语言. Go是一门非常年轻的语言, 它的主要目标是兼具Python等动态语言的开发速度和C/C++等编译型语言的性能与安全性. 

## 特点

### 出色的并发性

很多公司, 特别是中国公司, 即将或已经完成了使用Go语言改造旧系统的过程. 经过Go语言重构的系统能使用更少的硬件资源获得更高的并发和I/O吞吐表现. Go语言的并发是基于`goroutine`的, 它类似于线程, 但是并非线程, 可以将它理解为一种虚拟线程. Go语言运行的时候会调用它, 并将其合理的分配到每个CPU中, 最大限度地使用CPU性能.

### 学习曲线低

Go语言简单易学, 学习曲线平缓, 不需要像C++那样需要两到三年的学习期. Go的语言风格类似于C语言. 其语法在C语言的基础上进行了大幅的简化. 

### 代码风格统一

Go语言提供了一套格式化工具 -- go fmt. 一些Go语言的开发环境或者编辑器在保存的时候, 都会使用格式化工具进行格式化, 保证了不同开发者提交的代码都是统一的格式.

### 开发效率高

![](https://www.topgoer.com/static/home/2.jpg)

Go语言实现了开发效率和执行效率的完美结合, 让你像写Python代码一样执行C代码.