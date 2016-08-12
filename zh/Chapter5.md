# Chapter 5 - Tidbits
# 第五章 - 花絮

In this chapter, we'll talk about a miscellany of Go's feature which didn't quite fit anywhere else.

在这一章是，我们会来介绍一些Go的特性的杂烩，这些内容不太适合放在其他章节中。

## Error Handling
## 错误处理

Go's preferred way to deal with errors is through return values, not exceptions. Consider the `strconv.Atoi` function which takes a string and tries to convert it to an integer:

Go更喜欢用返回值而不是异常的方式来处理错误。例如`strconv.Atoi`函数将一个字符串转换成一个整数：

```go
package main

import (
  "fmt"
  "os"
  "strconv"
)

func main() {
  if len(os.Args) != 2 {
    os.Exit(1)
  }

  n, err := strconv.Atoi(os.Args[1])
  if err != nil {
    fmt.Println("not a valid number")
  } else {
    fmt.Println(n)
  }
}
```

You can create your own error type; the only requirement is that it fulfills the contract of the built-in `error` interface, which is:

你可以创建你自己的错误类型；唯一的要求就是需要实现内置接口`error`:

```go
type error interface {
  Error() string
}
```

More commonly, we can create our own errors by importing the `errors` package and using it in the `New` function:

更为常见的是，我们可以通过导入`errors`包，并通过它的`New`函数来创建自己的错误：

```go
import (
  "errors"
)


func process(count int) error {
  if count < 1 {
    return errors.New("Invalid count")
  }
  ...
  return nil
}
```

There's a common pattern in Go's standard library of using error variables. For example, the `io` package has an `EOF` variable which is defined as:

在GO的标准库中，使用错误变量是一个常用的模式。例如， 在`io`包中有一个`EOF`变量是这样定义的：

```go
var EOF = errors.New("EOF")
```

This is a package variable (it's defined outside of a function) which is publicly accessible (upper-case first letter). Various functions can return this error, say when we're reading from a file or STDIN. If it makes contextual sense, you should use this error, too. As consumers, we can use this singleton:

这是一个公共（大写字母开关）的包变量（它定义有函数之外）。如果我们从文件或者标准输入时失败时，我们可以返回这个错误。为了更容易理解，你也应该用这个错误。作为使用者，我们可以用这个单件：

```go
package main

import (
  "fmt"
  "io"
)

func main() {
  var input int
  _, err := fmt.Scan(&input)
  if err == io.EOF {
    fmt.Println("no more input!")
  }
}
```

As a final note, Go does have `panic` and `recover` functions. `panic` is like throwing an exception while `recover` is like `catch`; they are rarely used.

最后要注意的是，Go有`panic`和`recover`函数。`panic`像是抛出异常，而`recover`是捕获异常；它们不常使用。

## Defer
## Defer

Even though Go has a garbage collector, some resources require that we explicitly release them. For example, we need to `Close()` files after we're done with them. This sort of code is always dangerous. For one thing, as we're writing a function, it's easy to forget to `Close` something that we declared 10 lines up. For another, a function might have multiple return points. Go's solution is the `defer` keyword:

虽然Go有垃圾回收机制，但是有些资源需要显示的释放它们。比如，当我们使用文件完了之后，需要调用`Close()`来关闭它们。这类代码总是很危险。其一，我们写一下函数的时候，如果申请一个资源超过10行，就很容易忘记`Close`。其二，一个函数可能会有多个返回点。Go的解决方案是使用`defer`关键字：

```go
package main

import (
  "fmt"
  "os"
)

func main() {
  file, err := os.Open("a_file_to_read")
  if err != nil {
    fmt.Println(err)
    return
  }
  defer file.Close()
  // read the file
}
```

If you try to run the above code, you'll probably get an error (the file doesn't exist). The point is to show how `defer` works. Whatever you `defer` will be executed after the method returns, even if it does so violently. This lets you release resources near where it’s initialized and takes care of multiple return points.

如果你尝试运行上面的代码，你可能收到一个错误（文件不存在）。这里展示的是`defer`是如何工作的。无论如何在函数返回时`defer`都会被执行，虽然这样有点极端。但这可以让你在初始化附近释放资源和不要操心多个返回点的问题。

## go fmt
## go fmt

Most programs written in Go follow the same formatting rules, namely, a tab is used to indent and braces go on the same line as their statement.

绝大多数Go写的代码遵守有一个相同的格式化规则，也就是说，使用Tab来缩进和花括号与语句同一行。

I know, you have your own style and you want to stick to it. That's what I did for a long time, but I'm glad I eventually gave in. A big reason for this is the `go fmt` command. It's easy to use and authoritative (so no one argues over meaningless preferences).

我知道你有自己的代码风格并且严格遵守它。我一直以来也是这么做的，但是我最终还是放弃了。一个最在的原因就是`go fmt`命令。它很容用且权威（所以没有人会为了毫无意义的偏好而争论） 

When you're inside a project, you can apply the formatting rule to it and all sub-projects via:

当你在一个工程目录下，你可以通过下面的命令将工程下所有文件使用相同的格式化规则：

```
go fmt ./...
```

Give it a try. It does more than indent your code; it also aligns field declarations and alphabetically orders imports.

尝试一下吧。除了缩进代码，它还会自动对齐你的声明语句并将包导入按字母顺序排序。

## Initialized If
## If初始化

Go supports a slightly modified if-statement, one where a value can be initiated prior to the condition being evaluated:
Go提供了一种稍有不同的if声明，一个可以在条件执行之前声明和初始化：

```go
if x := 10; count > x {
  ...
}
```

That's a pretty silly example. More realistically, you might do something like:

这是一个非常简单的例子。更实际的例子是，你可能是这样做的：

```go
if err := process(); err != nil {
  return err
}
```

Interestingly, while the values aren't available outside the if-statement, they are available inside any `else if` or `else`.

有趣的是，`if`语句中定义并初始化的值在`if`语句之外是不可用的，仅可以在`else if`和`else`语句中使用。

## Empty Interface and Conversions
## 空接口和转换

In most object-oriented languages, a built-in base class, often named `object`, is the superclass for all other classes. Go, having no inheritance, doesn't have such a superclass. What it does have is an empty interface with no methods: `interface{}`. Since every type implements all 0 of the empty interface's methods, and since interfaces are implicitly implemented, every type fulfills the contract of the empty interface.

在大多数面向对象语言中，都有一种内置的基类，叫`object`，它是所有其他类的超类。但是go语言不支持继承，所以没有类似的超类。Go确实有一个没有任何方法的空接口：`interface{}`。因为接口都是隐式实现，每种类型都实现了空接口的0个方法，所以每种类型都实现了空接口的协议。

 If we wanted to, we could write an `add` function with the following signature:

如果我们愿意，我们可以通过下面声明方式写一个`add`函数：

```go
func add(a interface{}, b interface{}) interface{} {
  ...
}
```

To convert an interface variable to an explicit type, you use `.(TYPE)`:

将一个空接口变量转换成一个指定的类型，你可以使用`.(TYPE)`:

```go
return a.(int) + b.(int)
```

Note that if the underlying type is not `int`, the above will result in an error.

需要注意如果底层类型示是一个`int`，上面的代码会导致一个错误。

You also have access to a powerful type switch:

你也可以通过switch使用强大的类型转换：

```go
switch a.(type) {
  case int:
    fmt.Printf("a is now an int and equals %d\n", a)
  case bool, string:
    // ...
  default:
    // ...
}
```

You'll see and probably use the empty interface more than you might first expect. Admittedly, it won't result in clean code. Converting values back and forth is ugly and dangerous but sometimes, in a static language, it's the only choice.

你会发现，空接口的使用会超出你的预期。诚然，这不会让代码变得简洁。来回转换值是丑陋和危险的，但有时候在静态类型语言中，这是唯一的选择。

## Strings and Byte Arrays
## 字符串和字节数组

Strings and byte arrays are closely related. We can easily convert one to the other:

字符串和字节数组有密切关系，我们可以轻易的将它们转换成对方：

```go
stra := "the spice must flow"
byts := []byte(stra)
strb := string(byts)
```

In fact, this way of converting is common across various types as well. Some functions explicitly expect an `int32` or an `int64` or their unsigned counterparts. You might find yourself having to do things like:

事实上，这也是大多数类型的转换方式。一些函数明确指定一个`int32`或者`int64`或者相应的无符号类型。你可能会发现你自己不得不像下面这样做：

```go
int64(count)
```

Still, when it comes to bytes and strings, it's probably something you'll end up doing often. Do note that when you use `[]byte(X)` or `string(X)`, you're creating a copy of the data. This is necessary because strings are immutable.

然而，当涉及到字节和字符串时，这可能是你会经常做的事。当你使用`[]byte(X)`或者`string(X)`时务必注意，你创建了数据的拷贝。这是因为字符串的不可变性。

Strings are made of `runes` which are unicode code points. If you take the length of a string, you might not get what you expect. The following prints 3:

当字符串有由`unicode`字符码`runes`组成时。如果你计算字符串的长度时，可能得到的结果和你期待的不同。下面结果是输出3：

    fmt.Println(len("椒"))

If you iterate over a string using `range`, you'll get runes, not bytes. Of course, when you turn a string into a `[]byte` you'll get the correct data.

如果你使用`range`迭代一个字符串，你得到的是`runes`,而不是`bytes`。当然，你将一个字符串转换为`[]byte`时，你得到的数据还是正确的。

## Function Type
## 函数类型

Functions are first-class types:
函数是第一类类型：

```go
type Add func(a int, b int) int
```

which can then be used anywhere -- as a field type, as a parameter, as a return value.

可以在任何地方使用————可以做为一个字段，参数，返回值。

```go
package main

import (
  "fmt"
)

type Add func(a int, b int) int

func main() {
  fmt.Println(process(func(a int, b int) int{
      return a + b
  }))
}

func process(adder Add) int {
  return adder(1, 2)
}
```

Using functions like this can help decouple code from specific implementations much like we achieve with interfaces.

像这样使用函数可以使你在一些特定实现时减少代码的耦合性，就像使用接口实现那样。

## Before You Continue
## 继续之前

We looked at various aspects of programming with Go. Most notably, we saw how error handling behaves and how to release resources such as connections and open files. Many people dislike Go's approach to error handling. It can feel like a step backwards. Sometimes, I agree. Yet, I also find that it results in code that's easier to follow. `defer` is an unusual but practical approach to resource management. In fact, it isn't tied to resource management only. You can use `defer` for any purpose, such as logging when a function exits.

我们已经学习了Go编程的很多内容。显而易见，我们看见了错误处理的行为和资源释放如连接或者打开文件。很多人不喜欢Go的错误处理方式。它让人觉得这是一种退步。有些时候，我同意这种说法。然而，我也发现这会导致代码更易读。`defer`是一种不常见但很实用的资源管理手段。事实上，它不仅仅可以进行资源管理。你可以使用`defer`完成任何目的，例如当一个函数退出时打印日志记录。

Certainly, we haven't looked at all of the tidbits Go has to offer. But you should be feeling comfortable enough to tackle whatever you come across.

当然，我们还没有学习Go提供的所有花絮。但是无论你遇到什么你应该可以轻松应对。
