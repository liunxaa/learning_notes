# The GO Programming Language

中文版 [Go语言圣经](https://golang-china.github.io/gopl-zh/index.html)

#### chapter01 Tutorial

介绍Go语言的基础组件

### 1.1 Hello,World

Go 是一门编译型语言，Go 语言的工具链将源代码及其依赖转换成计算机的机器指令（译注：静态编译）。因为静态编译，所以不用担心在系统库更新的时候冲突。

Go 语言的代码通过**包**（package）组织，包类似于其它语言里的库（libraries）或者模块（modules）。一个包由位于单个目录下的一个或多个 `.go` 源代码文件组成。每个源文件都以一条 `package` 声明语句开始，表示该文件属于哪个包，紧跟着一系列导入（import）的包，之后是存储在这个文件里的程序语句。

`main` 包比较特殊。它定义了一个独立可执行的程序，而不是一个库。在 `main` 里的 `main` *函数*也很特殊，它是整个程序执行时的入口（译注：C 系语言差不多都这样）。`main` 函数所做的事情就是程序做的。

Go 语言不需要在语句或者声明的末尾添加分号，除非一行上有多条语句。实际上，编译器会主动把特定符号后的换行符转换为分号，因此换行符添加的位置会影响 Go 代码的正确解析（译注：比如行末是标识符、整数、浮点数、虚数、字符或字符串文字、关键字 `break`、`continue`、`fallthrough`或 `return` 中的一个、运算符和分隔符 `++`、`--`、`)`、`]` 或 `}` 中的一个）。举个例子，函数的左括号 `{` 必须和 `func` 函数声明在同一行上，且位于末尾，不能独占一行，而在表达式 `x+y` 中，可在 `+` 后换行，不能在 `+` 前换行（译注：以+结尾的话不会被插入分号分隔符，但是以 x 结尾的话则会被分号分隔符，从而导致编译错误）。

Go 语言在代码格式上采取了很强硬的态度。`gofmt`工具把代码格式化为标准格式（译注：这个格式化工具没有任何可以调整代码格式的参数，Go 语言就是这么任性），并且 `go` 工具中的 `fmt` 子命令会对指定包，否则默认为当前目录中所有go 源文件应用 `gofmt` 命令。

### 1.2 命令行参数

输入来自于程序外部：文件、网络连接、其它程序的输出、敲键盘的用户、命令行参数或其它类似输入源。

命令行参数：`os` 包以跨平台的方式，提供了一些与操作系统交互的函数和变量。程序的命令行参数可从 `os` 包的 `Args` 变量获取；`os` 包外部使用 `os.Args` 访问该变量。`os.Args` 变量是一个字符串（string）的 *切片*（slice），`os.Args` 的第一个元素：`os.Args[0]`，是命令本身的名字；其它的元素则是程序启动时传给它的参数。

Go 语言只有 `for` 循环这一种循环语句。`for` 循环有多种形式，其中一种如下所示：

```go
for initialization; condition; post {
    // zero or more statements
}
```

*`initialization`* 语句是可选的，在循环开始前执行。*`initalization`* 如果存在，必须是一条 *简单语句*（simple statement），即，短变量声明、自增语句、赋值语句或函数调用。`condition` 是一个布尔表达式（boolean expression），其值在每次循环迭代开始时计算。如果为 `true` 则执行循环体语句。`post` 语句在循环体执行结束后执行，之后再次对 `condition` 求值。`condition` 值为 `false` 时，循环结束。

for 循环的这三个部分每个都可以省略，如果省略 `initialization` 和 `post`，分号也可以省略：

```go
// a traditional "while" loop
for condition {
    // ...
}
```

如果连 `condition` 也省略了，像下面这样：

```go
// a traditional infinite loop
for {
    // ...
}
```

这就变成一个无限循环，尽管如此，还可以用其他方式终止循环，如一条 `break` 或 `return` 语句。

`for` 循环的另一种形式，在某种数据类型的区间（range）上遍历，如字符串或切片。

```go
for _, arg := range os.Args[1:] {
    s += sep + arg
    sep = " "
}
```

每次循环迭代，`range` 产生一对值；索引以及在该索引处的元素值。这个例子不需要索引，但 `range` 的语法要求，要处理元素，必须处理索引。一种思路是把索引赋值给一个临时变量（如 `temp`）然后忽略它的值，但 Go 语言不允许使用无用的局部变量（local variables），因为这会导致编译错误。Go 语言中这种情况的解决方法是用 *空标识符*（blank identifier），即 `_`（也就是下划线）。空标识符可用于在任何语法需要变量名但程序逻辑不需要的时候（如：在循环里）丢弃不需要的循环索引，并保留元素值。

### 1.3查找重复的行

**map** 存储了键/值（key/value）的集合，对集合元素，提供常数时间的存、取或测试操作。键可以是任意类型，只要其值能用 `==` 运算符比较，最常见的例子是字符串；值则可以是任意类型。



`bufio` 包，使处理输入和输出方便又高效。`Scanner` 类型是该包最有用的特性之一，它读取输入并将其拆成行或单词；通常是处理行形式的输入最简单的方法。

程序使用短变量声明创建 `bufio.Scanner` 类型的变量 `input`。

`input := bufio.NewScanner(os.Stdin)`

该变量从程序的标准输入中读取内容。每次调用 `input.Scan()`，即读入下一行，并移除行末的换行符；读取的内容可以调用 `input.Text()` 得到。

### 1.4. GIF动画

常量是指在程序编译后运行时始终都不会变化的值。常量声明和变量声明一般都会出现在包级别，所以这些常量在整个包中都是可以共享的，或者也可以把常量声明定义在函数体内部，那么这种常量就只能在函数体内用。目前常量声明的值必须是一个数字值、字符串或者一个固定的boolean值。

### 1.5. 获取URL

### 1.6. 并发获取多个URL

goroutine是一种函数的并发执行方式，而channel是用来在goroutine之间进行参数传递。main函数本身也运行在一个goroutine中，而go function则表示创建一个新的goroutine，并在这个新的goroutine中执行这个函数。

当一个goroutine尝试在一个channel上做send或者receive操作时，这个goroutine会阻塞在调用处，直到另一个goroutine从这个channel里接收或者写入值，这样两个goroutine才会继续执行channel操作之后的逻辑。

### 1.7. Web服务

### 1.8. 本章要点

Go语言并不需要显式地在每一个case后写break，语言默认执行完case后的逻辑语句会自动退出。当然了，如果你想要相邻的几个case都执行同一逻辑的话，需要自己显式地写上一个fallthrough语句来覆盖这种默认行为。不过fallthrough语句在一般的程序中很少用到。

Go语言里的switch还可以不带操作对象（译注：switch不带操作对象时默认用true值代替，然后将每个case的表达式和true值进行比较）；可以直接罗列多种条件，像其它语言里面的多个if else一样.

continue会跳过内层的循环，如果我们想跳过的是更外层的循环的话，我们可以在相应的位置加上label，这样break和continue就可以根据我们的想法来continue和break任意循环。这看起来甚至有点像goto语句的作用了。

**指针：** Go语言提供了指针。指针是一种直接存储了变量的内存地址的数据类型。在其它语言中，比如C语言，指针操作是完全不受约束的。在另外一些语言中，指针一般被处理为“引用”，除了到处传递这些指针之外，并不能对这些指针做太多事情。Go语言在这两种范围中取了一种平衡。指针是可见的内存地址，&操作符可以返回一个变量的内存地址，并且*操作符可以获取指针指向的变量内容，但是在Go语言里没有指针运算，也就是不能像c语言里可以对指针进行加或减操作。

在你开始写一个新程序之前，最好先去检查一下是不是已经有了现成的库可以帮助你更高效地完成这件事情。你可以在 https://golang.org/pkg 和 [https://godoc.org](https://godoc.org/) 中找到标准库和社区写的package。godoc这个工具可以让你直接在本地命令行阅读标准库的文档。比如下面这个例子。

```
$ go doc http.ListenAndServe
package http // import "net/http"
func ListenAndServe(addr string, handler Handler) error
    ListenAndServe listens on the TCP network address addr and then
    calls Serve with handler to handle requests on incoming connections.
...
```




