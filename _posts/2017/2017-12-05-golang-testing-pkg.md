---
layout: post
title: Golang自带的测试包说明
categories:
- Technology
tags:
- Golang
---


# 1、Golang测试包

golang自带了测试包（testing），直接可以进行单元测试、性能分析、输出结果验证等。简单看着官方文档试了试，总结一下:
目录结构和命令

## 目录结构

使用golang的测试包，需要遵循简单的目录结构

测试代码放在待测试代码的目录下（一个包内），以_test.go结尾，例如如下目录结构，MyTest目录下有待测试的代码文件MyTest.go和测试代码MyTest_test.go

```sh
.
|-- bin
|   `-- main
|-- pkg
|   `-- darwin_amd64
|       `-- MyTest.a
`-- src
    |-- MyTest
    |   |-- MyTest.go
    |   `-- MyTest_test.go
    `-- main
        `-- main.go
```

## 注意点概述

* 测试文件命名规则:xxx_test.go
* 详细的testing库使用方法，具体参考:[Golang标准库测试部分](https://studygolang.com/static/pkgdoc/index.html) 或者 [Golang标准库示例的测试部分](http://books.studygolang.com/The-Golang-Standard-Library-by-Example/chapter09/09.0.html)
* 测试方法命名规则:
  * 基本测试:`Test[A-Z][A-z_]*`，`首`写字母`不能是小写字母[a-z]`
  * 性能测试:`Benchmark[A-Z][A-z_]*`，`首`写字母`不能是小写字母[a-z]`
* 方法参数:
  * 基本测试: `t *testing.T`
  * 性能测试: `b *testing.B`

## 启动测试

直接在MyTest目录下执行go test命令即可，go test包含很多选项，可以参考Golang手册相关部分
基本的测试函数

# 2、基本的函数测试

待测试的MyTest.go源码如下:

```golang
package MyTest

import (
    "fmt"
)

type MyStruct struct {
    name string
}

func GetFieldValue(x *MyStruct) string {
    value := x.name
    return value
}

func SetFieldValue(x *MyStruct, value string) {
    fmt.Println("SetFieldValue()")
    x.name = value
}
```

对于MyTest_test.go，首先自然是要导入golang的测试包:

    package MyTest
    import testing

基本的测试函数以Test开头，后面接的字符串，第一个字符必须是数字或者大写，例如:

```golang
func TestMytest(t *testing.T) {
    var st MyStruct
    SetFieldValue(&st, "hello")
    val := GetFieldValue(&st)

    if val != "hello" {
        t.Error("Set Field")
    }
}
```

T是testing包里定义的一个结构体，其包含了名为common的接口，提供了很多格式化输出的功能，golang提供了自动检查与调用测试函数的机制，测试函数的执行逻辑则需要编写者自行完成。

执行go test 结果为:


    Call SetFieldValue()
    PASS
    ok  	/go/src/MyTest 0.004s

如果写成`Testmytest`，运行时会直接忽略掉该函数。得到的结果仍然为`Pass`，但是不会打印`“Call SetFieldValue()”`，也就是说测试函数实际没有执行。
修改一下判断条件:

```golang
func TestMytest(t *testing.T) {
    var st MyStruct
    SetFieldValue(&st, "hello")
    val := GetFieldValue(&st)

    if val != "world" {
        t.Error("Set Field")
    }
}
```

这个测试用例不会通过, 而Error函数的入参就是测试不通过时打印的信息:

```sh
SetFieldValue()
--- FAIL: TestMytest (0.00s)
	MyTest_test.go:15: Set Field
FAIL
exit status 1
FAIL	_/Users/ronghuihe/Documents/code/golang/my/go/src/MyTest 0.004s
```

这并不会中断测试程序，这个函数后面的部分仍然会执行，其他的测试函数也会执行。
性能分析函数

# 3、性能分析函数

testing包还自带了性能分析功能，可评估代码执行性能。性能分析函数也可以放到前面的xxx_test.go文件内，命名以`Benchmark`开头，如:

```golang
func BenchmarkGetFieldValue(b *testing.B) {
    var st MyStruct
    SetFieldValue(&st, "hello")

    for i := 0; i < b.N; i++ {
        GetFieldValue(&st)
    }
}
```

其中b.N的值在执行过程中会自动调整，使得循环可以执行足够多次，以便得到较为准确的单次结果:
性能分析加`-bench`参数执行，即`go test -bench` . (不能漏了最后这个点，它表示执行所有的性能测试函数）

```sh
BenchmarkGetFieldValue	SetFieldValue()
SetFieldValue()
SetFieldValue()
SetFieldValue()
SetFieldValue()
SetFieldValue()
2000000000	         0.55 ns/op
```

每次循环大约需要0.55ns。这里SetFieldValue()打印了多次，是因为`BenchmarkGetFieldValue`被调用了多次。

分析testing包的源码benchmark.go里的launch函数和runN函数，可以看到golang会自行调节性能分析函数的调用次数。每次执行runN都会执行一次性能测试函数，而后根据运行时间，会确定后续的内部循环执行次数b.N，直到总的运行时间达到`go test -benchtime`指定的时间（如果没指定默认为1s）

注意统计时间时，执行的是使用者编写的性能分析函数，如上例中的BenchmarkGetFieldValue，而最后输出的结果，表达的是for循环里函数的性能，因此for循环之前的代码执行时间很长的话，可能导致统计误差比较大，如果需要剔除for之前代码的影响，可以在for循环之前调用ResetTimer()接口重置本次统计的时间值:

```golang
func BenchmarkGetFieldValue(b *testing.B) {
    var st MyStruct
    SetFieldValue(&st, "hello")

    b.ResetTimer()
    for i := 0; i < b.N; i++ {
        GetFieldValue(&st)
    }
}
```

# 4、go test参数指南

go test是go语言自带的测试工具，其中包含的是两类，单元测试和性能测试

## 4.1、基本指令说明

通过go help test可以看到go test的使用说明:
格式形如:

    go test [-c] [-i] [build flags] [packages] [flags for test binary]
参数解读:

    -c : 编译go test成为可执行的二进制文件，但是不运行测试。

    -i : 安装测试包依赖的package，但是不运行测试。

    关于build flags，调用go help build，这些是编译运行过程中需要使用到的参数，一般设置为空

    关于packages，调用go help packages，这些是关于包的管理，一般设置为空

    关于flags for test binary，调用go help testflag，这些是go test过程中经常使用到的参数

    -test.v : 是否输出全部的单元测试用例（不管成功或者失败），默认没有加上，所以只输出失败的单元测试用例。

    -test.run pattern: 只跑哪些单元测试用例

    -test.bench patten: 只跑那些性能测试用例

    -test.benchmem : 是否在性能测试的时候输出内存情况

    -test.benchtime t : 性能测试运行的时间，默认是1s

    -test.cpuprofile cpu.out : 是否输出cpu性能分析文件

    -test.memprofile mem.out : 是否输出内存性能分析文件

    -test.blockprofile block.out : 是否输出内部goroutine阻塞的性能分析文件

    -test.memprofilerate n : 内存性能分析的时候有一个分配了多少的时候才打点记录的问题。这个参数就是设置打点的内存分配间隔，也就是profile中一个sample代表的内存大小。默认是设置为512 * 1024的。如果你将它设置为1，则每分配一个内存块就会在profile中有个打点，那么生成的profile的sample就会非常多。如果你设置为0，那就是不做打点了。

    你可以通过设置memprofilerate=1和GOGC=off来关闭内存回收，并且对每个内存块的分配进行观察。

    -test.blockprofilerate n: 基本同上，控制的是goroutine阻塞时候打点的纳秒数。默认不设置就相当于-test.blockprofilerate=1，每一纳秒都打点记录一下

    -test.parallel n : 性能测试的程序并行cpu数，默认等于GOMAXPROCS。

    -test.timeout t : 如果测试用例运行时间超过t，则抛出panic

    -test.cpu 1,2,4 : 程序运行在哪些CPU上面，使用二进制的1所在位代表，和nginx的nginx_worker_cpu_affinity是一个道理

    -test.short : 将那些运行时间较长的测试用例运行时间缩短


## 4.2 常用指令说明

* 1) 测试单个文件，一定要带上被测试的原文件

    go test -v  wechat_test.go wechat.go

* 2) 测试单个函数方法

    go test -v -test.run TestRefreshAccessToken

* 3) 选择性执行

    go test -v func/test
    go test -v -run TestFunc func/test
    go test -v -run ^TestFunc$ func/test

# 5、扩展阅读

* [Golang测试包小结](https://studygolang.com/articles/2464)
* [如何测试Go代码——单元测试](http://codethoughts.info/go/2015/04/05/how-to-test-go-code/)
* [Go单元测试基本篇](https://www.qcloud.com/community/article/56073001484044261?fromSource=gwzcw.59715.59715.59715)
* [Go单元测试进阶篇](https://www.cnblogs.com/qcloud1001/p/6645461.html)

