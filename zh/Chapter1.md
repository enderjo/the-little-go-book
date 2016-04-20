# Chapter 1 - The Basics
# 第一章 - 基础

Go is a compiled, statically typed language with a C-like syntax and garbage collection. What does that mean?

Go是一门静态类型、编译型语言，有类C风格的语法和垃圾回收机制。这意味着什么呢？

## Compilation
## 编译

Compilation is the process of translating the source code that you write into a lower level language -- either assembly (as is the case with Go), or some other intermediary language (as with Java and C#).

编译将你写的源代码转换成一种更低级的语言————可能是汇编（如Go就是这样），或者其他中间语言（如Java和C#）的过程。

Compiled languages can be unpleasant to work with because compilation can be slow. It's hard to iterate quickly if you have to spend minutes or hours waiting for code to compile. Compilation speed is one of the major design goals of Go. This is good news for people working on large projects as well as those of us used to a quick feedback cycle offered by interpreted languages.

因为编译可能很慢，使用编译型语言可能不是个令人愉快的事情。很难实现快速迭代因为你不得不花几分钟甚至几个小时的时间来等待编绎完成。编译速度是Go设计时的一个主要目标。这对于大项目的开发人员来说是个好消息，就像我们可以使用解释语言提供的快速反馈周期。

Compiled languages tend to run faster and the executable can be run without additional dependencies (at least, that's true for languages like C, C++ and Go which compile directly to assembly).

编译型语言往往运行得更快，不需要额外的依赖也可以正常运行（至少，像C、C++和Go这样直接编译成汇编的语言来说，就是如此。）

## Static Typing
## 静态类型

Being statically typed means that variables must be of a specific type (int, string, bool, []byte, etc.). This is either achieved by specifying the type when the variable is declared or, in many cases, letting the compiler infer the type (we'll look at examples shortly).

静态类型是指变量必须指定一个类型（整型、字符串、布尔、字节数组等等）。可以在申明变量的时候指定数据类型，也可以,大多数情况是让编译器来推断类型（我们将会在接下来的例子中看到）。

There's a lot more that can be said about static typing, but I believe it's something better understood by looking at code. If you're used to dynamically typed languages, you might find this cumbersome. You're not wrong, but there are advantages, especially when you pair static typing with compilation. The two are often conflated. It's true that when you have one, you normally have the other but it isn't a hard rule. With a rigid type system, a compiler is able to detect problems beyond mere syntactical mistakes as well as make further optimizations.

关于静态类型还有很多可以介绍，但我相信理解它更好的方式是阅读代码。如果你习惯于动态语言，你可能觉得这比较麻烦。没错，不过静态类型也有优势，尤其是和编译相结合的时候。静态类型和编译这两者经常被混为一谈。虽然这不是硬性的规定，但通常情况下，有其一就必有其二。在严格类型系统中，编译器除了能够检测出单纯的语法错误问题还能做出进一步的优化。

## C-Like Syntax
## 类C语法

Saying that a language has a C-like syntax means that if you're used to any other C-like languages such as C, C++, Java, JavaScript and C#, then you're going to find Go familiar -- superficially, at least. For example, it means `&&` is used as a boolean AND, `==` is used to compare equality, `{` and `}` start and end a scope, and array indexes start at 0.

说一门语言有一个类C的语法意味着，如果你使用的任何其他类似C语言，如C，C ++，Java，JavaScript以及C＃，那么你会发现Go的相似之处————至少从表面上看。比如，`&&`表示逻辑与,`==`表示相等判断，`{}`和`}`是作用域的开始和结束，以及数组从0开始索引。

C-like syntax also tends to mean semi-colon terminated lines and parentheses around conditions. Go does away with both of these, though parentheses are still used to control precedence. For example, an `if` statement looks like this:

类C语法也往往使用分号结束行和条件表达式用括号括起来。Go没用使用这两种方式，尽管依然使用括号来控制优先权。比如，一个`if`表达式看起来像这样：

```go
if name == "Leto" {
  print("the spice must flow")
}
```

And in more complicated cases, parentheses are still useful:

在更复杂的情况下，括号依然有用：

```go
if (name == "Goku" && power > 9000) || (name == "gohan" && power < 4000)  {
  print("super Saiyan")
}
```

Beyond this, Go is much closer to C than C# or Java - not only in terms of syntax, but in terms of purpose. That's reflected in the terseness and simplicity of the language which will hopefully start to become obvious as you learn it.

除此之外，Go比C#或者Java更接近C，不仅在语法方面，还在用途方面。这体现在语言风格的简洁和简单，随着不断深入学习，你会越来越明显的体会到这种特性。

## Garbage Collected
## 垃圾回收机制

Some variables, when created, have an easy-to-define life. A variable local to a function, for example, disappears when the function exits. In other cases, it isn't so obvious -- at least to a compiler. For example, the lifetime of a variable returned by a function or referenced by other variables and objects can be tricky to determine. Without garbage collection, it's up to developers to free the memory associated with such variables at a point where the developer knows the variable isn't needed. How? In C, you'd literally `free(str);` the variable.

一些变量，在创建时就有明确的生命周期。如函数内的局部变量，当函数结束时就消失了。在另一些情况下，就没有这么明显了，起码对编译器来说是这样。比如函数中返回的变量，变量的引用和对象的引用的生命周期就很难判断了。没有垃圾回收机制的情况下，这依赖于开发人员在不需要这些变量时进行内存的释放。怎么实现？例如在c中，你需要正确的去释放一个变量的内存`free(str);`。

Languages with garbage collectors (e.g., Ruby, Python, Java, JavaScript, C#, Go) are able to keep track of these and free them when they're no longer used. Garbage collection adds overhead, but it also eliminates a number of devastating bugs.

有垃圾回收机制的语言（如Ruby、Python、Java、JavaScript、C#、Go）能记录变量并在不使用时进行释放。垃圾回收机制增加了开销，但也杜绝了一些破坏性的bug。

## Running Go Code
## 运行Go代码

Let's start our journey by creating a simple program and learning how to compile and execute it. Open your favorite text editor and write the following code:

让我们创建一个简单的例子来学习如何编译和运行它，来开始我们的Go学习之旅。打开你最喜欢的文本编辑器，输入如下的代码：

```go
package main

func main() {
  println("it's over 9000!")
}
```

Save the file as `main.go`. For now, you can save it anywhere you want; we don't need to live inside Go's workspace for trivial examples.

将文件保存为`main.go`。开始，你可以将它保存在任何你想要的地方；作为简单的例子，我们还不需要深入理解Go的工作区。

Next, open a shell/command prompt and change the directory to where you saved the file. For me, that means typing `cd ~/code`.

接下来，打开一个shell/命令行，然后将目录切换到你保存文件的位置。对我来，输入`cd ~/code`就可以了。

Finally, run the program by entering:

最后，能过输入如下命令来运行程序：

```
go run main.go
```

If everything worked, you should see *it's over 9000!*.

如果一切正常，你会看到 *it's over 9000!*。

But wait, what about the compilation step? `go run` is a handy command that compiles *and* runs your code. It uses a temporary directory to build the program, executes it and then cleans itself up. You can see the location of the temporary file by running:

等等，那编译过程呢？`go run`是一个方便的编译和执行代码的命令。它使用临时目录来生成程序和运行，然后清理。通过下面的代码你可以查看临时文件所在位置：

```
go run --work main.go
```

To explicitly compile code, use `go build`:

要显示的编译代码，使用`go build`:

```
go build main.go
```

This will generate an executable `main` which you can run. On Linux / OSX, don't forget that you need to prefix the executable with dot-slash, so you need to type `./main`.

这会生成一个可执行的`main`程序。在Linux/OSX中，不要忘记在可执行文件前面加上点和反斜杠，所有你需要输入`./main`。

While developing, you can use either `go run` or `go build`. When you deploy your code however, you'll want to deploy a binary via `go build` and execute that.

在开发的时候，你可以使用`go run`或者`go build`。但当你发布的时候，你需要使用`go build`来生成可执行文件并运行它。

### Main
### Main

Hopefully, the code that we just executed is understandable. We've created a function and printed out a string with the built-in `println` function. Did `go run` know what to execute because there was only a single choice? No. In Go, the entry point to a program has to be a function called `main` within a package `main`.

但愿，我们刚刚的执行的代码是可以理解的。我们创建了一个函数，它调用内置的`println`函数打印一个字符串。难道是因为只有一个选择，所以`go run`才知道要执行什么吗？不是的，在Go语言中，程序的入口是`main`包中的`main`函数。

We'll talk more about packages in a later chapter. For now, while we focus on understanding the basics of Go, we'll always write our code within the `main` package.

后续章节我们会介绍更多包的内容。现在，为了我们着重理解Go的基础知识，我们只在`main`包中写代码。

If you want, you can alter the code and change the package name. Run the code via `go run` and you should get an error. Then, change the name back to `main` but use a different function name. You should see a different error message. Try making those same changes but use `go build` instead. Notice that the code compiles, there's just no entry point to run it. This is perfectly normal when you are, for example, building a library.

如果你愿意，你也可以修改代码并改变包名89。并使用`go run`去执行，你会得到一个错误信息。然后，将包名改成`main`，但是函数名不叫`main`，再次运行代码，你会得到一个不同的错误信息。使用`go build`进行相同的操作，注意编译代码时，这里没有运行代码的入口点。这是很正常的，例如当你编译一个库时。

## Imports
## 包导入

Go has a number of built-in functions, such as `println`, which can be used without reference. We can't get very far though, without making use of Go's standard library and eventually using third-party libraries. In Go, the `import` keyword is used to declare the packages that are used by the code in the file.

Go有一些内建函数是不需要引入就可以直接使用，如`println`。不利用Go标准库和第三方类库的话，我们不能走得很远。在Go中，使用`import`关键字来申明代码中使用的包。

Let's change our program:
让我们来修改下程序：

```go
package main

import (
  "fmt"
  "os"
)

func main() {
  if len(os.Args) != 2 {
    os.Exit(1)
  }
  fmt.Println("It's over ", os.Args[1])
}
```

Which you can run via:

通过下面的命令来运行它：

```
go run main.go 9000
```

We're now using two of Go's standard packages: `fmt` and `os`. We've also introduced another built-in function `len`. `len` returns the size of a string, or the number of values in a dictionary, or, as we see here, the number of elements in an array. If you're wondering why we expect 2 arguments, it's because the first argument -- at index 0 -- is always the path of the currently running executable. (Change the program to print it out and see for yourself.)

我们用了两个Go的标准包：`fmt`和`os`。我们引入了另一个内建函数`len`。`len`返加字符串的长度，或者字典的个数，再或者，如这个例子，数组元素的个数。如果你想知道为什么我们期望是两个参数，这是因为索引为0的第一个参数是当前可执程序的路径（你可以自己修改代码将它打印出来看看）。

You've probably noticed we prefix the function name with the package, e.g., `fmt.Println`. This is different from many other languages. We'll learn more about packages in later chapters. For now, knowing how to import and use a package is a good start.

你可能已经注意到了函数名之前的包名了，比如：`fmt.Println`，这和其他很多语言不同。后续章节我们会学习更多包的内容。现在，知道如何导入和使用包就好了。

Go is strict about importing packages. It will not compile if you import a package but don't use it. Try to run the following:

Go对包导入很严格。如果导入了包，但没有使用是不能通过编译的。试试运行下面的代码：

```go
package main

import (
  "fmt"
  "os"
)

func main() {
}
```

You should get two errors about `fmt` and `os` being imported and not used. Can this get annoying? Absolutely. Over time, you'll get used to it (it'll still be annoying though). Go is strict about this because unused imports can slow compilation; admittedly a problem most of us don't have to this degree.

你会看到两个错误信息，显示`fmt`和`os`包被导入但是没有被使用。这会让人烦吗？绝对的。随着时间的推移，你会习惯（虽然还是烦人）。Go之所以在这点上这么严格是因为导入未使用的包会影响编译速度。不可否认的是，我们大多数人都没有这个深度。

Another thing to note is that Go's standard library is well documented. You can head over to <http://golang.org/pkg/fmt/#Println> to learn more about the `Println` function that we used. You can click on that section header and see the source code. Also, scroll to the top to learn more about Go's formatting capabilities.

令一个值得注意的地方就是Go的标准包的文档很完善。你可以通过<http://golang.org/pkg/fmt/#Println> 来学习更多我们用到过的`Println`的内容。你可以点击章节标题来查看源码。也可以滚动到顶部来查看更多关于Go格式化的功能。

If you're ever stuck without internet access, you can get the documentation running locally via:
如果你不能访问网络，你可以通过下面的方面运行本地的文档：

```
godoc -http=:6060
```

and pointing your browser to `http://localhost:6060`

然后通过`http://localhost:6060`来浏览。


## Variables and Declarations

It'd be nice to begin and end our look at variables by saying *you declare and assign to a variable by doing x = 4.* Unfortunately, things are more complicated in Go. We'll begin our conversation by looking at simple examples. Then, in the next chapter, we'll expand this when we look at creating and using structures. Still, it'll probably take some time before you truly feel comfortable with it.

You might be thinking *Woah! What can be so complicated about this?* Let's start looking at some examples.

The most explicit way to deal with variable declaration and assignment in Go is also the most verbose:

```go
package main

import (
  "fmt"
)

func main() {
  var power int
  power = 9000
  fmt.Printf("It's over %d\n", power)
}
```

Here, we declare a variable `power` of type `int`. By default, Go assigns a zero value to variables. Integers are assigned `0`, booleans `false`, strings `""` and so on. Next, we assign `9000` to our `power` variable. We can merge the first two lines:

```go
var power int = 9000
```

Still, that's a lot of typing. Go has a handy short variable declaration operator, `:=`, which can infer the type:

```go
power := 9000
```

This is handy, and it works just as well with functions:

```go
func main() {
  power := getPower()
}

func getPower() int {
  return 9001
}
```

It's important that you remember that `:=` is used to declare the variable as well as assign a value to it. Why? Because a variable can't be declared twice (not in the same scope anyway). If you try to run the following, you'll get an error.

```go
func main() {
  power := 9000
  fmt.Printf("It's over %d\n", power)

  // COMPILER ERROR:
  // no new variables on left side of :=
  power := 9001
  fmt.Printf("It's also over %d\n", power)
}
```

The compiler will complain with *no new variables on left side of :=*. This means that when we first declare a variable, we use `:=` but on subsequent assignment, we use the assignment operator `=`. This makes a lot of sense, but it can be tricky for your muscle memory to remember when to switch between the two.

If you read the error message closely, you'll notice that *variables* is plural. That's because Go lets you assign multiple variables (using either `=` or `:=`):


```go
func main() {
  name, power := "Goku", 9000
  fmt.Printf("%s's power is over %d\n", name, power)
}
```

As long as one of the variables is new, `:=` can be used. Consider:

```go
func main() {
  power := 1000
  fmt.Printf("default power is %d\n", power)

  name, power := "Goku", 9000
  fmt.Printf("%s's power is over %d\n", name, power)
}
```

Although `power` is being used twice with `:=`, the compiler won't complain the second time we use it, it'll see that the other variable, `name`, is a new variable and allow `:=`. However, you can't change the type of `power`. It was declared (implicitly) as an integer and thus, can only be assigned integers.

For now, the last thing to know is that, like imports, Go won't let you have unused variables. For example,

```go
func main() {
  name, power := "Goku", 1000
  fmt.Printf("default power is %d\n", power)
}
```

won't compile because `name` is declared but not used. Like unused imports it'll cause some frustration, but overall I think it helps with code cleanliness and readability.

There's more to learn about declaration and assignments. For now, remember that you'll use `var NAME TYPE` when declaring a variable to its zero value, `NAME := VALUE` when declaring and assigning a value, and `NAME = VALUE` when assigning to a previously declared variable.

## Function Declarations

This is a good time to point out that functions can return multiple values. Let's look at three functions: one with no return value, one with one return value, and one with two return values.

```go
func log(message string) {
}

func add(a int, b int) int {
}

func power(name string) (int, bool) {
}
```

We'd use the last one like so:

```go
value, exists := power("goku")
if exists == false {
  // handle this error case
}
```

Sometimes, you only care about one of the return values. In these cases, you assign the other values to `_`:

```go
_, exists := power("goku")
if exists == false {
  // handle this error case
}
```

This is more than a convention. `_`, the blank identifier, is special in that the return value isn't actually assigned. This lets you use `_` over and over again regardless of the returned type.

Finally, there's something else that you're likely to run into with function declarations. If parameters share the same type, we can use a shorter syntax:

```go
func add(a, b int) int {

}
```

Being able to return multiple values is something you'll use often. You'll also frequently use `_` to discard a value. Named return values and the slightly less verbose parameter declaration aren't that common. Still, you'll run into all of these sooner than later so it's important to know about them.

## Before You Continue

We looked at a number of small individual pieces and it probably feels disjointed at this point. We'll slowly build larger examples and hopefully, the pieces will start to come together.

If you're coming from a dynamic language, the complexity around types and declarations might seem like a step backwards. I don't disagree with you. For some systems, dynamic languages are categorically more productive.

If you're coming from a statically typed language, you're probably feeling comfortable with Go. Inferred types and multiple return values are nice (though certainly not exclusive to Go). Hopefully as we learn more, you'll appreciate the clean and terse syntax.

