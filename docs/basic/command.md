---
title: Go命令
layout: doc
navbar: true
sidebar: true
aside: true
outline: deep
lastUpdated: true
editLink: true
footer: true
---

# 命令

## `go build`

`go build`用于编译Go源文件, 该命令会根据源代码生成可执行文件或库. 可执行文件在当前文件夹.

- 编译单个Go文件或整个项目
- 创建库文件
- 交叉编译 

## `go run`

`go run`用于编译并运行Go程序, 适用于快速测试代码片段. 不会生成任何新文件.

- 快速测试小程序
- 不需要生成持久的可执行文件

## `go get`

`go get`用于下载并安装依赖或者项目到`$GOPATH/pkg/mod/`下的某个文件夹下.

- 下载第三方库
- 更新项目依赖

## `go mod`

`go mod`用于Go模块支持, 包括初始化, 添加依赖等.

- 初始化新项目
- 管理项目依赖

## `go list`

`go list`用于列出包或模块.

- 查看当前项目依赖
- 查看全局安装的包

## `go fmt`

`go fmt`用于自动格式化Go源代码.

- 代码审查
- 统一代码风格

## `go vet`

`go vet`用于对Go代码进行静态分析, 检查可能出现的错误.

- 代码质量检查
- 发现潜在问题

## `go test`

`go test`用于运行Go程序的测试.

- 单元测试
- 性能测试

## `go doc`

`go doc`用于查看Go语言标准库或者你的代码库中的文档.

- 查找库函数说明
- 查找接口文档

## `go env`

`go env`用于打印Go的环境信息.

- 环境配置
- 问题诊断

## `go clean`

`go clean`用于删除编译生成的文件.

- 清理项目目录
- 回复到初始状态

## `go tool`

`go tool`用于运行指定的Go工具.

- 编译优化
- 调试

## `go version`

`go version`用于打印当前Go的版本信息.

- 版本检查
- 依赖分析

## `go install`

`go install`用于编译, 生成可执行文件并安装到Go的安装目录中, 通常是`$GOBIN`或`$GOPATH/bin`目录中. 

- 创建可分发的二进制文件
- 安装到Go的目录中

## `go generate`

`go generate`用于通过源代码生成Go文件

- 代码生成
- 模板处理

## `go fix`

`go fix`用于安装包以使用新的API.

- API迁移
- 自动修复代码

## `go workspace`

`go workspace`用于管理Go工作区, 实验性功能. 

- 多项目管理
- 环境隔离

## `go help`

`go help`用于查看命令或主题的帮助信息

- 查找命令语法
- 学习Go工具链

## 参考资料

1. https://www.cnblogs.com/xfuture/p/17786462.html