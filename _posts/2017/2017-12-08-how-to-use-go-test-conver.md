---
layout: post
title: golang测试覆盖率学习
categories:
- Technology
tags:
- Golang
---

*我是测试术语小白，为了做一个了解，从网上查看一些博文，摘取记录下，以备后续再温习，如果认为有侵权，告知，删之*


# 什么是测试覆盖率

一般通过被测试的软件产品需求、功能点、测试用例数或程序代码行等来进行计算。

* 1.功能覆盖率= 至少被执行一次的测试功能点数/ 测试功能点总数 （功能点）
* 2.需求覆盖率= 被验证到的需求数量 /总的需求数量 （需求）
* 3.覆盖率= 至少被执行一次的测试用例数/ 应执行的测试用例总数 （测试用例）

测试评估可以说贯穿整个软件测试过程，可以在测试每个阶段结束前进行，也可以在测试过程中某一个时间进行，目的只有一个，提高测试覆盖度，保证测试的质量。

通过不断的测试覆盖度评估或测试覆盖率计算，及时掌握测试的实际状况与测试覆盖度目标的差距，及时采取措施，就可以提高测试的覆盖度。

测试覆盖度的评估依赖于不同的测试阶段或不同的测试方法。如在单元测试中，测试覆盖率是建立在被测试的代码行、程序分支和程序路径等的度量之上，从软件质量保证的要求出发，单元测试的覆盖率要达到80％之上。白盒测试方法主要以程序语句、判定－条件、条件组合和基本路径等覆盖率来衡量，和单元测试是吻合的。而在系统功能测试中，则以功能点、测试用例、需求数等覆盖率来衡量


`Test coverage is a term that describes how much of a package’s code is exercised by running the package’s tests.`

 简而言之，在go语言中，测试覆盖率是用来描述在执行package测试时，有多少代码被覆盖到，与传统的测试覆盖率没有太大的差别，也即： `代码覆盖度的一种度量`

 如果想要做更细致的学习，可以参考[代码覆盖率浅谈](https://www.cnblogs.com/coderzh/archive/2009/03/29/1424344.html)

# golang中的测试覆盖率

在go语言的测试覆盖率统计时的参数有： cover、covermode、coverpkg多种方式，请看`go test -h`:


```sh
	-cover
	    Enable coverage analysis.
	    Note that because coverage works by annotating the source
	    code before compilation, compilation and test failures with
	    coverage enabled may report line numbers that don't correspond
	    to the original sources.

	-covermode set,count,atomic
	    Set the mode for coverage analysis for the package[s]
	    being tested. The default is "set" unless -race is enabled,
	    in which case it is "atomic".
	    The values:
		set: bool: does this statement run?
		count: int: how many times does this statement run?
		atomic: int: count, but correct in multithreaded tests;
			significantly more expensive.
	    Sets -cover.

	-coverpkg pkg1,pkg2,pkg3
	    Apply coverage analysis in each test to the given list of packages.
	    The default is for each test to analyze only the package being tested.
	    Packages are specified as import paths.
	    Sets -cover.
```


go test通过参数covermode的设定可以对覆盖率统计模式作如下三种设定。

|:-:|:-:|
|模式|解释|
|set|缺省模式, 只记录语句是否被执行过|
|count|记录语句被执行的次数|
|atomic|记录语句被执行的次数，并保证在并发执行时的正确性|


## 示例：

```sh
zero@zero:~/Gopath/src/github.com/hyperledger/fabric/peer/node$ go test -cover
……
status:STARTED
status:STARTED
……
status:UNKNOWN
……
status:UNKNOWN
PASS
coverage: 69.2% of statements
ok  	github.com/hyperledger/fabric/peer/node	3.362s
```


# 使用html来详细展示覆盖率情况

可以看到我们现在得到了一个69.2%的覆盖率，这说明69.2%的代码在测试用例执行中被至少执行过一次。但是想确认那些语句没有被执行的时候，上述方法就无法使用了。接下来我们将继续使用go test命令生成html文件用以显示测试覆盖率情况。

## 使用指令`go test -coverprofile=covprofile`生成profile文件

```sh
zero@zero:~/Gopath/src/github.com/hyperledger/fabric/peer/node$ go test -coverprofile=covprofile
……
status:STARTED
status:STARTED
……
status:UNKNOWN
……
status:UNKNOWN
PASS
coverage: 69.2% of statements
ok  	github.com/hyperledger/fabric/peer/node	4.102s
zero@zero:~/Gopath/src/github.com/hyperledger/fabric/peer/node$ ls
covprofile  node.go  start.go  start_test.go  status.go  status_test.go
zero@zero:~/Gopath/src/github.com/hyperledger/fabric/peer/node$
```

## 使用指令`go tool cover -html=covprofile -o 2017-12-08-coverage-peer.html`生成html文件

```sh
zero@zero:~/Gopath/src/github.com/hyperledger/fabric/peer/node$ go tool cover -html=covprofile -o coverage.html
zero@zero:~/Gopath/src/github.com/hyperledger/fabric/peer/node$ ls
coverage.html  covprofile  node.go  start.go  start_test.go  status.go  status_test.go
```
## coverage.html的内容

[coverage.html](https://github.com/neujie/neujie.github.io/blob/master/packages/2017-12-08-coverage-peer.html)

## covprofile的内容

```sh
zero@zero:~/Gopath/src/github.com/hyperledger/fabric/peer/node$ cat covprofile
mode: set
github.com/hyperledger/fabric/peer/node/node.go:35.27,40.2 3 0
github.com/hyperledger/fabric/peer/node/start.go:58.32,68.2 5 1
github.com/hyperledger/fabric/peer/node/start.go:74.54,76.3 1 1
github.com/hyperledger/fabric/peer/node/start.go:80.19,84.2 2 1
github.com/hyperledger/fabric/peer/node/start.go:86.33,91.22 3 1
github.com/hyperledger/fabric/peer/node/start.go:99.2,99.50 1 1
github.com/hyperledger/fabric/peer/node/start.go:103.2,104.16 2 1
github.com/hyperledger/fabric/peer/node/start.go:109.2,112.16 3 1
github.com/hyperledger/fabric/peer/node/start.go:115.2,116.16 2 1
github.com/hyperledger/fabric/peer/node/start.go:120.2,120.25 1 1
github.com/hyperledger/fabric/peer/node/start.go:128.2,129.16 2 1
github.com/hyperledger/fabric/peer/node/start.go:134.2,153.16 11 1
github.com/hyperledger/fabric/peer/node/start.go:157.2,164.45 3 1
github.com/hyperledger/fabric/peer/node/start.go:180.2,182.16 2 1
github.com/hyperledger/fabric/peer/node/start.go:185.2,191.35 3 1
github.com/hyperledger/fabric/peer/node/start.go:196.2,205.12 5 1
github.com/hyperledger/fabric/peer/node/start.go:211.2,211.12 1 1
github.com/hyperledger/fabric/peer/node/start.go:221.2,221.97 1 1
github.com/hyperledger/fabric/peer/node/start.go:226.2,226.27 1 1
github.com/hyperledger/fabric/peer/node/start.go:231.2,231.43 1 1
github.com/hyperledger/fabric/peer/node/start.go:241.2,247.44 3 1
github.com/hyperledger/fabric/peer/node/start.go:254.2,257.16 2 1
github.com/hyperledger/fabric/peer/node/start.go:91.22,97.3 3 0
github.com/hyperledger/fabric/peer/node/start.go:99.50,101.3 1 0
github.com/hyperledger/fabric/peer/node/start.go:104.16,107.3 2 0
github.com/hyperledger/fabric/peer/node/start.go:112.16,114.3 1 0
github.com/hyperledger/fabric/peer/node/start.go:116.16,118.3 1 0
github.com/hyperledger/fabric/peer/node/start.go:120.25,125.3 3 0
github.com/hyperledger/fabric/peer/node/start.go:129.16,131.3 1 0
github.com/hyperledger/fabric/peer/node/start.go:153.16,155.3 1 0
github.com/hyperledger/fabric/peer/node/start.go:164.45,172.24 4 0
github.com/hyperledger/fabric/peer/node/start.go:178.3,178.18 1 0
github.com/hyperledger/fabric/peer/node/start.go:172.24,175.4 2 0
github.com/hyperledger/fabric/peer/node/start.go:175.4,177.4 1 0
github.com/hyperledger/fabric/peer/node/start.go:182.16,184.3 1 0
github.com/hyperledger/fabric/peer/node/start.go:191.35,194.3 2 0
github.com/hyperledger/fabric/peer/node/start.go:205.12,209.3 3 1
github.com/hyperledger/fabric/peer/node/start.go:211.12,213.51 2 1
github.com/hyperledger/fabric/peer/node/start.go:218.3,218.19 1 0
github.com/hyperledger/fabric/peer/node/start.go:213.51,215.4 1 0
github.com/hyperledger/fabric/peer/node/start.go:215.4,217.4 1 0
github.com/hyperledger/fabric/peer/node/start.go:221.97,223.3 1 0
github.com/hyperledger/fabric/peer/node/start.go:226.27,228.3 1 1
github.com/hyperledger/fabric/peer/node/start.go:231.43,232.13 1 0
github.com/hyperledger/fabric/peer/node/start.go:232.13,235.87 3 0
github.com/hyperledger/fabric/peer/node/start.go:235.87,237.5 1 0
github.com/hyperledger/fabric/peer/node/start.go:247.44,249.17 2 1
github.com/hyperledger/fabric/peer/node/start.go:249.17,251.4 1 0
github.com/hyperledger/fabric/peer/node/start.go:261.116,272.27 4 1
github.com/hyperledger/fabric/peer/node/start.go:299.2,299.22 1 1
github.com/hyperledger/fabric/peer/node/start.go:272.27,279.3 3 0
github.com/hyperledger/fabric/peer/node/start.go:279.3,279.49 1 1
github.com/hyperledger/fabric/peer/node/start.go:279.49,286.3 3 0
github.com/hyperledger/fabric/peer/node/start.go:286.3,288.17 2 1
github.com/hyperledger/fabric/peer/node/start.go:292.3,293.17 2 1
github.com/hyperledger/fabric/peer/node/start.go:296.3,296.41 1 1
github.com/hyperledger/fabric/peer/node/start.go:288.17,289.14 1 0
github.com/hyperledger/fabric/peer/node/start.go:293.17,294.14 1 0
github.com/hyperledger/fabric/peer/node/start.go:305.81,311.53 3 1
github.com/hyperledger/fabric/peer/node/start.go:318.2,323.54 3 1
github.com/hyperledger/fabric/peer/node/start.go:311.53,314.3 2 1
github.com/hyperledger/fabric/peer/node/start.go:314.3,316.3 1 0
github.com/hyperledger/fabric/peer/node/start.go:326.62,329.16 2 1
github.com/hyperledger/fabric/peer/node/start.go:333.2,334.22 2 1
github.com/hyperledger/fabric/peer/node/start.go:338.2,338.59 1 1
github.com/hyperledger/fabric/peer/node/start.go:342.2,345.8 1 1
github.com/hyperledger/fabric/peer/node/start.go:329.16,331.3 1 0
github.com/hyperledger/fabric/peer/node/start.go:334.22,336.3 1 0
github.com/hyperledger/fabric/peer/node/start.go:338.59,340.3 1 0
github.com/hyperledger/fabric/peer/node/start.go:348.90,352.16 4 1
github.com/hyperledger/fabric/peer/node/start.go:356.2,357.16 2 1
github.com/hyperledger/fabric/peer/node/start.go:361.2,366.24 3 1
github.com/hyperledger/fabric/peer/node/start.go:352.16,354.3 1 0
github.com/hyperledger/fabric/peer/node/start.go:357.16,360.3 2 0
github.com/hyperledger/fabric/peer/node/start.go:369.47,371.16 2 1
github.com/hyperledger/fabric/peer/node/start.go:375.2,376.69 2 1
github.com/hyperledger/fabric/peer/node/start.go:381.2,381.12 1 1
github.com/hyperledger/fabric/peer/node/start.go:371.16,373.3 1 0
github.com/hyperledger/fabric/peer/node/start.go:376.69,379.3 2 1
github.com/hyperledger/fabric/peer/node/status.go:29.33,31.2 1 1
github.com/hyperledger/fabric/peer/node/status.go:37.54,39.3 1 1
github.com/hyperledger/fabric/peer/node/status.go:42.27,45.16 2 1
github.com/hyperledger/fabric/peer/node/status.go:51.2,52.16 2 1
github.com/hyperledger/fabric/peer/node/status.go:58.2,59.12 2 1
github.com/hyperledger/fabric/peer/node/status.go:45.16,49.3 3 1
github.com/hyperledger/fabric/peer/node/status.go:52.16,57.3 4 1
```

