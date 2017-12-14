---
layout: post
title: Golang 标准库学习(io)
categories:
- 学习
tags:
- Golang
---

# io.Reader

## 说明

Read 将 len(p) 个字节读取到 p 中。它返回读取的字节数 n（0 <= n <= len(p)） 以及任何遇到的错误。即使 Read 返回的 n < len(p)，它也会在调用过程中使用 p 的全部作为暂存空间。若一些数据可用但不到 len(p) 个字节，Read 会照例返回可用的数据，而不是等待更多数据。

当 Read 在成功读取 n > 0 个字节后遇到一个错误或 EOF (end-of-file)，它就会返回读取的字节数。它会从相同的调用中返回（非nil的）错误或从随后的调用中返回错误（同时 n == 0）。 一般情况的一个例子就是 Reader 在输入流结束时会返回一个非零的字节数，同时返回的 err 不是 EOF 就是 nil。无论如何，下一个 Read 都应当返回 0, EOF。

调用者应当总在考虑到错误 err 前处理 n > 0 的字节。这样做可以在读取一些字节，以及允许的 EOF 行为后正确地处理 I/O 错误。

`io.EOF 变量的定义：var EOF = errors.New("EOF")，是 error 类型。根据 reader 接口的说明，在 n > 0 且数据被读完了的情况下，返回的 error 有可能是 EOF 也有可能是 nil。`


## 举例

```golang
func ReaderExample() {
FOREND:
	for {
		readerMenu()

		var ch string
		fmt.Scanln(&ch)
		var (
			data []byte
			err  error
		)
		switch strings.ToLower(ch) {
		case "1":
			fmt.Println("请输入不多于9个字符，以回车结束：")
			data, err = ReadFrom(os.Stdin, 11)
		case "2":
			file, err := os.Open(util.GetProjectRoot() + "/src/chapter01/io/01.txt")
			if err != nil {
				fmt.Println("打开文件 01.txt 错误:", err)
				continue
			}
			data, err = ReadFrom(file, 9)
			file.Close()
		case "3":
			data, err = ReadFrom(strings.NewReader("from string"), 12)
		case "4":
			fmt.Println("暂未实现！")
		case "b":
			fmt.Println("返回上级菜单！")
			break FOREND
		case "q":
			fmt.Println("程序退出！")
			os.Exit(0)
		default:
			fmt.Println("输入错误！")
			continue
		}

		if err != nil {
			fmt.Println("数据读取失败，可以试试从其他输入源读取！")
		} else {
			fmt.Printf("读取到的数据是：%s\n", data)
		}
	}
}

func ReadFrom(reader io.Reader, num int) ([]byte, error) {
	p := make([]byte, num)
	n, err := reader.Read(p)
	if n > 0 {
		return p[:n], nil
	}
	return p, err
}

func readerMenu() {
	fmt.Println("")
	fmt.Println("*******从不同来源读取数据*********")
	fmt.Println("*******请选择数据源，请输入：*********")
	fmt.Println("1 表示 标准输入")
	fmt.Println("2 表示 普通文件")
	fmt.Println("3 表示 从字符串")
	fmt.Println("4 表示 从网络")
	fmt.Println("b 返回上级菜单")
	fmt.Println("q 退出")
	fmt.Println("***********************************")
}
```

# io.Writer

## 说明

Write 将 len(p) 个字节从 p 中写入到基本数据流中。它返回从 p 中被写入的字节数 n（0 <= n <= len(p)）以及任何遇到的引起写入提前停止的错误。若 Write 返回的 n < len(p)，它就必须返回一个 非nil 的错误。

## 举例

    func Fprintln(w io.Writer, a ...interface{}) (n int, err error)

方法的第一个参数是io.Writer，也就是说，任何实现了io.Writer接口的类型实例都可以传递进来；我们再看看Println方法内部实现：


    func Println(a ...interface{}) (n int, err error) {
        return Fprintln(os.Stdout, a...)
    }

我们不妨追溯一下os.Stdout：

    Stdout = NewFile(uintptr(syscall.Stdout), "/dev/stdout")

也就是标准输出。从这里可以看出，os.File也实现了io.Writer，那么，如果第一个参数传递的是一个普通文件，内容便会被输出到该文件。

如果第一个参数传递的是bytes.Buffer，那么，内容便输出到了buffer中。

在写Web程序时，比如：

    func Index(rw http.ResponseWriter, req *http.Request) {
        fmt.Fprintln(rw, "Hello, World")
    }

这样便把”Hello World”输出给了客户端。