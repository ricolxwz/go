---
title: Go条件
layout: doc
navbar: true
sidebar: true
aside: true
outline: deep
lastUpdated: true
editLink: true
footer: true
---

# 条件

## `if`语句

Go语言中`if`语句的语法如下:

- 可省略条件表达式括号
- 可以在块内部定义局部变量
- 不支持三元操作符

::: details
```go
x := 0

if n := "abc"; x > 0 {     // 初始化语句未必就是定义变量, 如 println("init") 也是可以的
    println(n[2])
} else if x < 0 {    // 注意 else if 和 else 左大括号位置
    println(n[1])
} else {
    println(n[0])
}
```
:::

## `switch`语句

`switch`语句用于基于不同条件执行不同动作, 每一个`case`分支都是唯一的, 从上到下逐一测试, 直到匹配为止. 分支表达式可以是任何类型, 不限于常量. 可省略`break`, 默认自动终止. 其语法如下:

```go
switch <var> {
    case <v1>:
        ...
    case <v2>:
        ...
    default:
        ...
}
```

::: details
```go
func main() {
   /* 定义局部变量 */
   var grade string = "B"
   var marks int = 90

   switch marks {
      case 90: grade = "A"
      case 80: grade = "B"
      case 50,60,70 : grade = "C"
      default: grade = "D"  
   }

   switch {
      case grade == "A" :
         fmt.Printf("优秀!\n" )     
      case grade == "B", grade == "C" :
         fmt.Printf("良好\n" )      
      case grade == "D" :
         fmt.Printf("及格\n" )      
      case grade == "F":
         fmt.Printf("不及格\n" )
      default:
         fmt.Printf("差\n" )
   }
   fmt.Printf("你的等级是 %s\n", grade )
}
```
:::

::: tip
Go的`switch`语句在匹配成功后不会自动向下执行其他`case`, 而是跳出整个`switch`, 但是可以使用`fallthrough`强制执行后面的`case`代码.
:::