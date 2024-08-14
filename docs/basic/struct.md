---
title: Go结构体
layout: doc
navbar: true
sidebar: true
aside: true
outline: deep
lastUpdated: true
editLink: true
footer: true
---

# 结构体

Go语言中没有类的概念, 也不支持类的继承等面向对象的概念. Go语言中通过结构体的内嵌再配合接口比面向对象具有更高的扩展性和灵活性.

## 自定义类型

在Go语言中有一些基本的数据类型, 如字符串, 整型, 浮点型, 布尔类型等, 也可以使用`type`关键字类自定义类型. 

::: tip
自定义类型是一个全新的类型. 我们可以基于内置的基本类型定义, 也可以通过`struct`定义(产生的类型成为结构体), 以基于内置的基本类型定义为例:
::: details
```go
type MyInt int
```
这就基于`int`定义了一种新的类型, 它具有`int`的所有特性.
:::

## 类型别名

类型别名是Go1.9版本后加入的功能. 类型别名规定别名和原始类型是同一个类型. 

::: details
我们之前见过的`rune`, `byte`就是类型别名:
```go
type byte = uint8
type rune = int32
```
:::

::: warning
注意类型别名和类型定义之间的区别.
::: details
```go
//类型定义
type NewInt int
//类型别名
type MyInt = int
```
:::

## 结构体

Go语言中的基础类型可以表示为一些事物的基本字段, 但是当我们想要表达一个事物的全部或者部分字段的时候, 单一的基本数据类型就无法满足需求了, Go语言提供了一种自定义类型, 可以封装多个基本数据类型, 这种数据类型叫作结构体.

## 定义

使用`type`和`struct`关键字来定义结构体:

```go
type <typeName> struct {
    <field1> <fieldType1>
    <field2> <fieldType2>
    ...
}
```

## 实例化

### 使用`var`声明

结构体是一种类型, 所以可以像声明内置类型那样声明实例类型:

```go
var <instance> <structType>
```

### 使用`new`申请

还可以通过`new`关键字对结构体进行实例化, 得到的是实例的地址:

```go
var <var> = new(<structType>)
```

### 取地址

还可以通过取地址的方式进行实例化:

```go
<instance> := &<structType>{}
```

## 初始化

### 使用键值对初始化

使用键值对进行初始化的时候, 键对应实例的字段, 值对应字段的初始值.

::: details
```go
p5 := person{
    name: "pprof.cn",
    city: "北京",
    age:  18,
}
fmt.Printf("p5=%#v\n", p5) //p5=main.person{name:"pprof.cn", city:"北京", age:18}
```
也可以对实例指针进行键值对初始化, 例如:
```go
p6 := &person{
    name: "pprof.cn",
    city: "北京",
    age:  18,
}
fmt.Printf("p6=%#v\n", p6) //p6=&main.person{name:"pprof.cn", city:"北京", age:18}
```
当某些字段没有初始值的时候, 可以不写, 这些字段的值就是该字段类型的零值:
```go
p7 := &person{
    city: "北京",
}
fmt.Printf("p7=%#v\n", p7) //p7=&main.person{name:"", city:"北京", age:0}
```
:::

### 使用值的列表初始化

初始化的时候可以简写, 也就是初始化的时候不写键, 只写值.

::: details
```go
p8 := &person{
    "pprof.cn",
    "北京",
    18,
}
fmt.Printf("p8=%#v\n", p8) //p8=&main.person{name:"pprof.cn", city:"北京", age:18}
```
:::

::: warning
使用这种格式初始化的时候, 需要注意:
- 必须初始化所有的字段
- 初始值的填充顺序必须和结构体定义的顺序一直
- 不能和键值初始化方式混用
:::

## 字段访问

我们可以通过`.`来访问实例字段, 例如`p1.name`, `p1.age`等.

::: warning
`.`前面的可以是实例本身, 也可以是实例的地址. 若是实例的地址, 会进行自动解引用, 这是Go的一个语法糖.
::: details
```go
var p2 = new(person)
p2.name = "测试"
p2.age = 18
p2.city = "北京"
fmt.Printf("p2=%#v\n", p2) // p2=&main.person{name:"测试", city:"北京", age:18}
```
可以看到`p2`中存储的是实例的地址, 也可以直接用`p2.name`访问字段, 在底层, 其实是`(*p2).name`.
:::

## 匿名结构体

在定义一些临时数据结构等场景下可以使用匿名结构体.

::: details
```go
func main() {
    var user struct{Name string; Age int}
    user.Name = "pprof.cn"
    user.Age = 18
    fmt.Printf("%#v\n", user)
}
```
:::

## 构造函数

Go语言的结构体没有构造函数, 我们可以自己实现. 因为结构体是值类型, 所以结构体比较复杂的话, 返回的过程中进行值拷贝的开销会比较大, 所以构造函数返回的是实例的地址.

::: details
```go
func newPerson(name, city string, age int8) *person {
    return &person{
        name: name,
        city: city,
        age:  age,
    }
}
```
使用构造函数:
```go
p9 := newPerson("pprof.cn", "测试", 90)
fmt.Printf("%#v\n", p9)
```
:::

## 方法

Go语言的方法是一种作用于特定类型变量的函数. 这种特定类型变量叫作接收者. 接收者的概念类似于其他语言中的`this`或者`self`. 方法定义的格式如下:

```go
func (<receiver> <recevierType>) <methodName>(<param1>, <param2>, ...) (<returnParam1>, <returnParam2>, ...) {
    ...
}
```

::: tip
- 接收者变量: 接收者变量在命名的时候, 官方建议用接收者类型名的第一个小写字母
- 接收者类型: 接收者类型和参数类似, 可以是指针类型和非指针类型
- 方法名, 参数列表, 返回参数: 和普通的函数相同
- 方法和函数的区别: 函数不属于任何类型, 方法属于特定的类型
:::

::: details
```go
//Person 结构体
type Person struct {
    name string
    age  int8
}

//NewPerson 构造函数
func NewPerson(name string, age int8) *Person {
    return &Person{
        name: name,
        age:  age,
    }
}

//Dream Person做梦的方法
func (p Person) Dream() {
    fmt.Printf("%s的梦想是学好Go语言！\n", p.name)
}

func main() {
    p1 := NewPerson("测试", 25)
    p1.Dream()
}
```
:::

::: warning
函数传递有两种方式, 值传递和引用传递, 无论是哪种传递方式, 传递给函数的都是变量的副本. 只不过, 值传递是值的拷贝, 引用传递是地址的拷贝.
:::

### 指针类型的接收者

由于函数传参的特性, 若需要对外界的实例做修改的话, 需要传入的是实例的指针, 而不是实例本身. 指针类型的接收者接受的是实例的指针的拷贝.

::: details
```go
//NewPerson 构造函数
func NewPerson(name string, age int8) *Person {
    return &Person{
        name: name,
        age:  age,
    }
}
func (p *Person) SetAge(newAge int8) {
    p.age = newAge
}
func main() {
    p1 := NewPerson("测试", 25)
    fmt.Println(p1.age) // 25
    p1.SetAge(30)
    fmt.Println(p1.age) // 30
}
```
:::

::: tip
什么时候该使用指针类型的接收者?
- 需要修改外部实例的字段
- 实例比较大, 不适合传入
- 保证一致性, 如果有某个方法使用了指针接收者, 那么其他的方法也应该使用指针接收者
:::

### 非指针类型的接收者

非指针类型的接收者是实例的拷贝.

::: details
```go
//NewPerson 构造函数
func NewPerson(name string, age int8) *Person {
    return &Person{
        name: name,
        age:  age,
    }
}
func (p Person) SetAge2(newAge int8) {
    p.age = newAge
}
func main() {
    p1 := NewPerson("测试", 25)
    p1.Dream()
    fmt.Println(p1.age) // 25
    p1.SetAge2(30) // (*p1).SetAge2(30), 这里又是Go的语法糖
    fmt.Println(p1.age) // 25
}
```
:::

### 任意类型添加方法

Go语言中, 接收者的类型可以是任何类型, 不仅仅是实例, 任何类型都可以拥有方法. 

::: details
我们基于内置的`int`类型使用`type`关键字可以自定义新的类型, 然后为新的自定义类型添加方法:
```go
//MyInt 将int定义为自定义MyInt类型
type MyInt int

//SayHello 为MyInt添加一个SayHello的方法
func (m MyInt) SayHello() {
    fmt.Println("Hello, 我是一个int。")
}
func main() {
    var m1 MyInt
    m1.SayHello() //Hello, 我是一个int。
    m1 = 100
    fmt.Printf("%#v  %T\n", m1, m1) //100  main.MyInt
}
```
:::

## 匿名字段

结构体允许其字段在声明的时候没有字段名只有类型, 这种没有名字的字段就称为匿名字段, 匿名字段默认采用类型名作为字段名, 由于结构体要求字段名称必须唯一, 所以结构体中同种类型的匿名字段只能有一个.

::: details
```go
//Person 结构体Person类型
type Person struct {
    string
    int
}

func main() {
    p1 := Person{
        "pprof.cn",
        18,
    }
    fmt.Printf("%#v\n", p1)        //main.Person{string:"pprof.cn", int:18}
    fmt.Println(p1.string, p1.int) //pprof.cn 18
}
```
:::

## 嵌套结构体

一个结构体中可以嵌套包含另一个结构体或者结构体指针.

::: details
```go
//Address 地址结构体
type Address struct {
    Province string
    City     string
}

//User 用户结构体
type User struct {
    Name    string
    Gender  string
    Address Address
}

func main() {
    user1 := User{
        Name:   "pprof",
        Gender: "女",
        Address: Address{
            Province: "黑龙江",
            City:     "哈尔滨",
        },
    }
    fmt.Printf("user1=%#v\n", user1)//user1=main.User{Name:"pprof", Gender:"女", Address:main.Address{Province:"黑龙江", City:"哈尔滨"}}
}
```
:::

### 嵌套匿名结构体

::: details
```go
//Address 地址结构体
type Address struct {
    Province string
    City     string
}

//User 用户结构体
type User struct {
    Name    string
    Gender  string
    Address //匿名结构体
}

func main() {
    var user2 User
    user2.Name = "pprof"
    user2.Gender = "女"
    user2.Address.Province = "黑龙江"    //通过匿名结构体.字段名访问
    user2.City = "哈尔滨"                //直接访问匿名结构体的字段名
    fmt.Printf("user2=%#v\n", user2) //user2=main.User{Name:"pprof", Gender:"女", Address:main.Address{Province:"黑龙江", City:"哈尔滨"}}
}
```
:::

::: tip
- 访问字段查找路径: 当访问实例字段的时候会先在当前层级结构体的字段中查找, 如果找不到再去下一级匿名结构体的字段中查找, 如上述例子中的`user2.City`
- 字段名称冲突: 当嵌套结构体中的字段名称冲突的时候, 如有两个同名的字段. 需要指定具体的路径
    ::: details
    ```go
    //Address 地址结构体
    type Address struct {
        Province   stringichen
        City       string
        CreateTime string
    }

    //Email 邮箱结构体
    type Email struct {
        Account    string
        CreateTime string
    }

    //User 用户结构体
    type User struct {
        Name   string
        Gender string
        Address
        Email
    }

    func main() {
        var user3 User
        user3.Name = "pprof"
        user3.Gender = "女"
        // user3.CreateTime = "2019" //ambiguous selector user3.CreateTime
        user3.Address.CreateTime = "2000" //指定Address结构体中的CreateTime
        user3.Email.CreateTime = "2000"   //指定Email结构体中的CreateTime
    }
    ```
    :::
:::

## "继承"

Go语言中使用结构体也可以实现其他编程语言中面向对象的"继承"

::: details
```go
//Animal 动物
type Animal struct {
    name string
}

func (a *Animal) move() {
    fmt.Printf("%s会动！\n", a.name)
}

//Dog 狗
type Dog struct {
    Feet    int8
    *Animal //通过嵌套匿名结构体实现继承
}

func (d *Dog) wang() {
    fmt.Printf("%s会汪汪汪~\n", d.name)
}

func main() {
    d1 := &Dog{
        Feet: 4,
        Animal: &Animal{ //注意嵌套的是结构体指针
            name: "乐乐",
        },
    }
    d1.wang() //乐乐会汪汪汪~
    d1.move() //乐乐会动！
}
```
:::

## 字段的可见性

结构体中字段大写开头表示可公开访问, 小写表示私有(仅在定义当前结构体的包中可以访问).