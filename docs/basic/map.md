---
title: Go映射
layout: doc
navbar: true
sidebar: true
aside: true
outline: deep
lastUpdated: true
editLink: true
footer: true
---

# 映射

映射, map是一种无须的基于键值对的数据结构. 

Go中映射的声名/初始化如下:

```
var <mapName> map[<keyType>]<valueType> = make(map[<keyType>]<valueType>, <cap>)
```

::: tip
map类型的变量默认初始值为`nil`.
:::

::: details
```go
func main() {
    scoreMap := make(map[string]int, 8)
    scoreMap["张三"] = 90
    scoreMap["小明"] = 100
    fmt.Println(scoreMap)
    fmt.Println(scoreMap["小明"])
    fmt.Printf("type of a:%T\n", scoreMap)
}
```
:::

## 判断键的存在

Go中判断某个键是否存在有特殊写法:

```
<value>, <ok> := map[<key>]
```

::: details
```go
func main() {
    scoreMap := make(map[string]int)
    scoreMap["张三"] = 90
    scoreMap["小明"] = 100
    // 如果key存在ok为true,v为对应的值；不存在ok为false,v为值类型的零值
    v, ok := scoreMap["张三"]
    if ok {
        fmt.Println(v)
    } else {
        fmt.Println("查无此人")
    }
}
```
:::

::: tip
`map[<key>]`可以根据上下文决定返回一个值还是两个值. 当你声明两个变量来接受`map[<key>]`结果的时候, 编译器知道你需要接受一个值和一个布尔值.
:::

## 遍历

类似[数组的遍历](/basic/array#遍历).

## 删除

可以使用`delete`函数删除一组键值对.

```
delete(<map>, <key>)
```

::: details
```go
func main(){
    scoreMap := make(map[string]int)
    scoreMap["张三"] = 90
    scoreMap["小明"] = 100
    scoreMap["王五"] = 60
    delete(scoreMap, "小明")//将小明:100从map中删除
    for k,v := range scoreMap{
        fmt.Println(k, v)
    }
}
```
:::

## 元素为map类型的切片

::: details
```go
func main() {
    var mapSlice = make([]map[string]string, 3)
    for index, value := range mapSlice {
        fmt.Printf("index:%d value:%v\n", index, value)
    }
    fmt.Println("after init")
    // 对切片中的map元素进行初始化
    mapSlice[0] = make(map[string]string, 10)
    mapSlice[0]["name"] = "王五"
    mapSlice[0]["password"] = "123456"
    mapSlice[0]["address"] = "红旗大街"
    for index, value := range mapSlice {
        fmt.Printf("index:%d value:%v\n", index, value)
    }
}
```
:::

## 值为切片类型的map

::: details
```go
func main() {
    var sliceMap = make(map[string][]string, 3)
    fmt.Println(sliceMap)
    fmt.Println("after init")
    key := "中国"
    value, ok := sliceMap[key]
    if !ok {
        value = make([]string, 0, 2)
    }
    value = append(value, "北京", "上海")
    sliceMap[key] = value
    fmt.Println(sliceMap)
}
```
:::