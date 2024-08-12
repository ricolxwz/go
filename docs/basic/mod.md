---
title: Go模式
layout: doc
navbar: true
sidebar: true
aside: true
outline: deep
lastUpdated: true
editLink: true
footer: true
---

# 模式

## GoPath模式

当你在电脑上安装好Go之后, 在终端执行`go env`命令, 在输出的内容中, 你会发现一个`GOPATH`的环境变量, 它的值是一个目录路径. 从Go 1.8版本开始, 安装Go会默认为`GOPATH`变量设置一个目录路径, 它表示的是Go的工作目录, 这个目录下会有三个子目录, 分别是:

- `bin`: 存放编译后生成的二进制可执行文件
- `pkg`: 存放编译后生成的`.a`文件
- `src`: 存放项目的源代码, 有自己写的代码, 还有通过`go get`命令下载的包

所谓的GoPath开发模式就是指将项目文件和下载的包放到`$GOPATH/src`目录下进行管理的方式.

### GoPath模式的弊端

在Go 1.11版本之前, 开发者是必须要配置这个`GOPATH`环境变量的, 这种代码管理模式存在比较严重的问题就是没有版本控制. 因为多个项目都会放到`src`目录下, 而每个项目依赖的一些第三方包也是下载在`src`目录下的, 当升级某个依赖包的时候就是全局升级了, 这是一件很危险的事情, 你不知道升级的包在另一个项目中是否能正常运行. 而且在多人协同开发的时候, 你不知道别人下载的包是不是你所用的那个版本, 容易出错而且不好排查原因.

#### vendor方案

针对GoPath模式的弊端, 官方给出了解决方案, 就是引入`vendor`, 解决思路是, 在每个项目下都创建一个`vendor`目录, 每个项目所需的依赖可以指定下载到自己的`vendor`目录下, 项目之间的依赖包就不互相影响了. 在使用包的时候, 会先从当前项目目录下的`vendor`目录查找, 然后依次向上级目录查找. 但是这种方案也不是特别完美, 如果多个项目使用的第三方库的版本是一样的, 那么就会造成相同的库存在在多个目录下, 占用空间而且没法集中管理. 再一个就是当你要分享自己的项目的时候, 除了源码, 还要上传所有依赖的包, 才能保证别人使用时不会因为版本问题报错.

官方已经不推荐使用GoPath开发了, 新版本中推荐GoMoudles模式.

## GoModules模式

GoModules模式是Go语言在1.11版本正式推出的. 在1.14版本的时候, 官方说明GoModules的已经成熟, 可以用于生产环境了.

使用GoModules模式主要依赖于官方自己的包管理工具, 即`mod`, 如果做过前端的话, 一定熟悉`npm`, `mod`就是类似于`npm`的工具. 当你要使用GoModules模式的时候, 需要主动开启它, 在终端输入命令`go env`时, 你会发现一个`GO111MODULE`变量, 它就是开关, 默认是`auto`模式, 它所有可以设置的值有:

- `auto`: 自动模式, 当项目下或上一层目录存在`go.mod`文件并且当前目录不在`$GOPATH/src/`下, 就启用GoModules模式
- `on`: 开启模块支持, 无论当前目录是否在`$GOPATH/src/`下, 都会启用
- `off`: 关闭模块支持, 使用GoPath模式

通过以下命令可以调整该环境变量的值:

```bash
go env -w GO111MODULE=on
```

或者也可以通过改写`.bashrc`实现:

```bashrc
export GO111MODULE=on
```

### 使用`go mod`初始化项目

开发者不需要将项目放到`$GOPATH/src`下了, 随便哪个位置都可以放.

::: details 例子
主要使用`go mod init`来初始化项目, 例如创建新的项目目录`go-demo1`和`go-demo2`, 需要执行以下命令:
```bash
mkdir go-demo1
mkdir go-demo2
cd go-demo1
go mod init goDemo1 # 后面是自定义项目名
cd go-demo2
go mod init goDemo2 # 后面是自定义项目名
```
此时两个项目的文件夹下就会多出一个`go.mod`文件, 内容如下: 
```
module goDemo1

go 1.22.6
```
和:
```
module goDemo2

go 1.22.6
```
:::

### 尝试下载第三方包

可以使用`go get <pkg>`来下载安装第三方包.

::: tip
- 包的使用顺序
    1. `vendor`文件夹中的包
    2. `$GOPATH/pkg/mod/`下的包
    3. 找不到包, 从远程下载, 并保存在`$GOPATH/pkg/mod/`下的某个文件夹中并使用
- 不管是否有`vendor`文件夹, `go get`下载的包都会被放到`$GOPATH/pkg/mod/`的某个文件夹下
- `vendor`目录允许你将项目的所有依赖包本地化. 如果项目中存在`vendor`目录, 会直接使用`vendor`中的包. 这在没有网络连接的时候特别好用
- 通过`go mod vendor`可以将所有的依赖包都放到`vendor`目录下
:::

::: details 例子
尝试在`go-demo1`下载一个第三方包, 使用`go get github.com/jinzhu/now@v1.1.0`, 发现`go-demo1`文件夹下的`go.mod`文件发生改变:
```
module goDemo1

go 1.22.6

require github.com/jinzhu/now v1.1.0 // indirect
```
还产生了一个新的文件`go.sum`, 这个文件类似于`package-lock.json`, 用来锁定项目的依赖版本: 
```
github.com/jinzhu/now v1.1.0 h1:qHxxrYrheFfW4oOq+oR6NmctEcazbkmw0o704v8mOk4=
github.com/jinzhu/now v1.1.0/go.mod h1:d3SSVoowX0Lcu0IBviAWJpolVfI5UJVZZ7cO71lE/z8=
```
这个包被放到`$GOPATH/pkg/mod/github.com/jinzhu/now@v1.1.0`下.

尝试在`go-demo2`下载一个相同的不同版本的第三方包, 使用`go get github.com/jinzhu/now@v1.1.5`, 发现`go-demo2`文件夹下的`go.mod`文件发生改变:
```
module goDemo2

go 1.22.6

require github.com/jinzhu/now v1.1.5 // indirect
```
同时, 也产生了一个新的文件`go.sum`:
```
github.com/jinzhu/now v1.1.5 h1:/o9tlHleP7gOFmsnYNz3RGnqzefHA47wQpKrrdTIwXQ=
github.com/jinzhu/now v1.1.5/go.mod h1:d3SSVoowX0Lcu0IBviAWJpolVfI5UJVZZ7cO71lE/z8=
```
这个包被放到`$GOPATH/pkg/mod/github.com/jinzhu/now@v1.1.5`下, 所以此时, `$GOPATH/pkg/mod/github.com/jinzhu`下有两个不同版本的包. 这就实现了同个依赖不同版本的管理. 并且, 如果有项目要使用版本号相同的依赖, 使用的是同一个文件夹, 不会创建多个相同版本的文件夹.
:::

## 参考资料

1. https://www.cnblogs.com/wjaaron/p/14797003.html