# Chapter 4 - Code Organization and Interfaces
# 第三章 - 代码组织和接口

It's now time to look at how to organize our code.

现在是时候来看看我们是怎么组织代码了。

## Packages
## 包

To keep more complicated libraries and systems organized, we need to learn about packages. In Go, package names follow the directory structure of your Go workspace. If we were building a shopping system, we'd probably start with a package name "shopping" and put our source files in `$GOPATH/src/shopping/`.

为了组织更复杂的类库和系统，我们需要了解包。在Go中，包名紧跟在工作目录结构之下。如果我们构建一个电商系统，我们可能以"shopping"命名包和把源文件存在`$GOPATH/src/shopping/`下。

We don't want to put everything inside this folder though. For example, maybe we want to isolate some database logic inside its own folder. To achieve this, we create a subfolder at `$GOPATH/src/shopping/db`. The package name of the files within this subfolder is simply `db`, but to access it from another package, including the `shopping` package, we need to import `shopping/db`.

显然我们不想反所有的东西都放在这个目录。例如，我们希望在数据库目录下关联一些数据库的逻辑。要达到这个目的，我们创建了一个子目录`$GOPATH/src/shopping/db`。这个目录下的包名可以是简单的`db`，但是其他包需要访问这个包时，就需要包含`shopping`，我们需要这样导入`shopping/db`。

In other words, when you name a package, via the `package` keyword, you provide a single value, not a complete hierarchy (e.g., "shopping" or "db"). When you import a package, you specify the complete path.

换名话来说，当你命名一个包时，可以通过`package`关键字，你提供一个单一的值，不是完整的层级（比如，"shopping"或者"db"）。当你导入包时，你需要指定完整的路径。

Let's try it. Inside your Go workspace's `src` folder (which we set up in Getting Started of the Introduction), create a new folder called `shopping` and a subfolder within it called `db`.

让我们来试一试，在我们的Go的工作目录下的`src`文件夹中（我们在入门介绍中设置的），创建一个`shopping`的文件夹并创建一个`db`的子文件夹。

Inside of `shopping/db`, create a file called `db.go` and add the following code:

在`shopping/db`目录下，创建一个名为`db.go`的文件并添加如下代码： 

```go
package db

type Item struct {
  Price float64
}

func LoadItem(id int) *Item {
  return &Item{
    Price: 9.001,
  }
}
```

Notice that the name of the package is the same as the name of the folder. Also, obviously, we aren't actually accessing the database. We're just using this as an example to show how to organize code.

注意下包名和文件夹的名字是一样的。显然，我们也没有真下的访问数据库。我们只是用这个例子来演示如何组织代码。

Now, create a file called `pricecheck.go` inside of the main `shopping` folder. Its content is:

现在，在主目录`shopping`下创建一个名为`pricecheck.go`的文件。它的代码如下：

```go
package shopping

import (
  "shopping/db"
)

func PriceCheck(itemId int) (float64, bool) {
  item := db.LoadItem(itemId)
  if item == nil {
    return 0, false
  }
  return item.Price, true
}
```

It's tempting to think that importing `shopping/db` is somehow special because we're inside the `shopping` package/folder already. In reality, you're importing `$GOPATH/src/shopping/db`, which means you could just as easily import `test/db` so long as you had a package named `db` inside of your workspace's `src/test` folder.

这很容易理解，导入`shopping/ db`有些特殊，因为我们是`shopping`包/文件夹中了。实际上，要导入`$ GOPATH/src/shopping/db`，这意味着你可以很容易地导入`test/db`，只要工作空间下的`src/test/db`的文件夹有一个`db`的包。

If you're building a package, you don't need anything more than what we've seen. To build an executable, you still need a `main`. The way I prefer to do this is to create a subfolder called `main` inside of `shopping` with a file called `main.go` and the following content:

如果你想建一个包，只需要我们看到的这些内容就可以了。要创建一个可执行程序，我们还需要一个`main`函数。我喜欢在`shopping`文件夹下建一个`main`文件夹并新增一个`main.go`的文件。它的内容如下：

```go
package main

import (
  "shopping"
  "fmt"
)

func main() {
  fmt.Println(shopping.PriceCheck(4343))
}
```

You can now run your code by going into your `shopping` project and typing:

进入`shopping`目录输入如下内容可以运行代码：

```
go run main/main.go
```

### Cyclical Imports
### 循环导入

As you start writing more complex systems, you're bound to run into cyclical imports. This happens when package A imports package B but package B imports package A (either directly or indirectly through another package). This is something the compiler won't allow.

当你开始写一些更复杂的系统时，你不免会碰到循环导入的问题。当包A的导入了包B但是包B又导入了包A（无论是直接还是间接通过其他包导入）循环导入就发生了。这是编译器不允许的。

Let's change our shopping structure to cause the error.

让我们来修改一下`shopping`的结构来引发这个错误。

Move the `Item` definition from `shopping/db/db.go` into `shopping/pricecheck.go`. Your `pricecheck.go` file should now look like:

把`Item`的定义从`shopping/db/db.go`移到`shopping/pricecheck.go`中。你的`pricecheck.go`应该看起来像这样：

```go
package shopping

import (
  "shopping/db"
)

type Item struct {
  Price float64
}

func PriceCheck(itemId int) (float64, bool) {
  item := db.LoadItem(itemId)
  if item == nil {
    return 0, false
  }
  return item.Price, true
}
```

If you try to run the code, you'll get a couple of errors from `db/db.go` about `Item` being undefined. This makes sense. `Item` no longer exists in the `db` package; it's been moved to the shopping package. We need to change `shopping/db/db.go` to:

如果你尝试运行代码，你会从`db/db.go`中收到一些错误信息说`Item`未定义。这好理解。`Item`已经不在`db`包下了；这被移到了`shopping`包。我们需要修改`shopping/db/db.go`的代码为：

```go
package db

import (
  "shopping"
)

func LoadItem(id int) *shopping.Item {
  return &shopping.Item{
    Price: 9.001,
  }
}
```

Now when you try to run the code, you'll get a dreaded *import cycle not allowed* error. We solve this by introducing another package which contains shared structures. Your directory structure should look like:

现在当你尝试运行代码时，你会收到一个令人害怕的错误*import cycle not allowed*。我们通过引入一个包含共享结构的另一个包来解决这个问题。你的目录结构看起来像这样：

```
$GOPATH/src
  - shopping
    pricecheck.go
    - db
      db.go
    - models
      item.go
    - main
      main.go
```

`pricecheck.go` will still import `shopping/db`, but `db.go` will now import `shopping/models` instead of `shopping`, thus breaking the cycle. Since we moved the shared `Item` structure to `shopping/models/item.go`, we need to change `shopping/db/db.go` to reference the `Item` structure from `models` package:

`pricecheck.go`一样是导入`shopping/db`，但是`db.go`现在要导入`shopping/models`来替换`shopping`，这样打破了循环。因为将共享结构体`Item`移到了`shopping/models/item.go`，我们需要修改`shopping/db/db.go`从`model`包中引用结构体`Item`：

```go
package db

import (
  "shopping/models"
)

func LoadItem(id int) *models.Item {
  return &models.Item{
    Price: 9.001,
  }
}
```

You'll often need to share more than just `models`, so you might have other similar folder named `utilities` and such. The important rule about these shared packages is that they shouldn't import anything from the `shopping` package or any sub-packages. In a few sections, we'll look at interfaces which can help us untangle these types of dependencies.

你常常需要的共享结构不仅仅是`models`，所以你可能还有一些类似`utilities`这样的文件夹。最重要的原则是这些共享对象，不能导入`shopping`包或者它的子包。要不了几个章节，我们会看到接口是如何解开这些依赖的。

### Visibility
### 可见性

Go uses a simple rule to define what types and functions are visible outside of a package. If the name of the type or function starts with an uppercase letter, it's visible. If it starts with a lowercase letter, it isn't.

Go用一个非常简单的原则来决定一个包的类型和函数是否在包外可见。如果类型或者函数是以大写字母开头，就是可见的，如果是小写字母开头，就是不可见的。

This also applies to structure fields. If a structure field name starts with a lowercase letter, only code within the same package will be able to access them.

这对结构体的字段也是一样适用的。如果一个结构体的字段名是以小写字母开头，只有在同一个包里的代码可以访问它们。

For example, if our `items.go` file had a function that looked like:

例如：在我们的`items.go`文件中有这样的一个函数：

```go
func NewItem() *Item {
  // ...
}
```

it could be called via `models.NewItem()`. But if the function was named `newItem`, we wouldn't be able to access it from a different package.

可以通过`models.NewItem()`来调用。但是如果这个函数被命名为`newItem`，我们就不能在其他包里面来调用它了。

Go ahead and change the name of the various functions, types and fields from the `shopping` code. For example, if you rename the `Item's` `Price` field to `price`, you should get an error.

继续修改在`shopping`中修改函数，类型和字段的名字。例如，如果你修改`Item`的`Price`为`price`,你会收到一个编译错误。

### Package Management
### 包管理

The `go` command we've been using to `run` and `build` has a `get` subcommand which is used to fetch third-party libraries. `go get` supports various protocols but for this example, we'll be getting a library from Github, meaning, you'll need `git` installed on your computer.

我们用来`run`和`build`的`go`命令，它还一个`get`的子命令用来获取第三方的类库。`go get`支持多种协议，但是这里，我们将从Github上获取一个类库。这是说，你要在你的电脑上安装好`git`。

Assuming you already have git installed, from a shell/command prompt, enter:

假设你已经安装好了git，打开一个shell/命令提示符，输入：

```
go get github.com/mattn/go-sqlite3
```

`go get` fetches the remote files and stores them in your workspace. Go ahead and check your `$GOPATH/src`. In addition to the `shopping` project that we created, you'll now see a `github.com` folder. Within, you'll see a `mattn` folder which contains a `go-sqlite3` folder.

`go get`获取远程文件并把它们存在你的工作目录中。到`$GOPATH/src`目录中检查一下。除了我们自己创建的`shopping`项目外，还会看到一个`github.com`文件夹。里面你会看到一个包含`go-sqlite3`文件夹的`mattn`文件夹。

We just talked about how to import packages that live in our workspace. To use our newly gotten `go-sqlite3` package, we'd import it like so:

我们刚介绍了在工作区中如何导入包。要用我们刚刚获取到的`go-sqlite3`包，我们需要像这样来导入它：

```go
import (
  "github.com/mattn/go-sqlite3"
)
```

I know this looks like a URL but in reality, it'll simply import the `go-sqlite3` package which it expects to find in `$GOPATH/src/github.com/mattn/go-sqlite3`.

我知道这看起来像是一个URL，但实际上，如果知道是在`$GOPATH/src/github.com/mattn/go-sqlite3`目录，导入`go-sqlite3`包是很简单的。

### Dependency Management
### 依赖管理

`go get` has a couple of other tricks up its sleeve. If we `go get` within a project, it'll scan all the files, looking for `imports` to third-party libraries and will download them. In a way, our own source code becomes a `Gemfile` or `package.json`.

`go get`有一些其他的戏法。如果我们在一个项目中执行`go get`，它会扫描所有文件并查找所有导入的第三方库，然后下载这些第三方库。某种程度上说，我们自己的源代码变成一个`Gemfile`或者`package.json`。

If you call `go get -u` it'll update the packages (or you can update a specific package via `go get -u FULL_PACKAGE_NAME`).

执行`go get -u`将更新你的包（或者你可以通过`go get -u FULL_PACKAGE_NAME`更新指定的包）

Eventually, you might find `go get` inadequate. For one thing, there's no way to specify a revision, it always points to the master/head/trunk/default. This is an even larger problem if you have two projects needing different versions of the same library.

最后，你可能发现了`go get`的一些不足。首先，它不能指定一个修订，它会一直指向`master/head/trunk/default`。这是一个严重的问题，尤其当你有2个项目需要同一个库的不同版本时。

To solve this, you can use a third-party dependency management tool. They are still young, but two promising ones are [goop](https://github.com/nitrous-io/goop) and [godep](https://github.com/tools/godep). A more complete list is available at the [go-wiki](https://code.google.com/p/go-wiki/wiki/PackageManagementTools).

为了解决这个问题，你可以使用一个第三方的依赖管理工具。虽然还不太成熟，但是有2个依赖管理工具比较有前景，即[goop](https://github.com/nitrous-io/goop)和[godep](https://github.com/tools/godep)。更完整的列表可以参考[go-wiki](https://github.com/golang/go/wiki/PackageManagementTools)。

## Interfaces
## 接口

Interfaces are types that define a contract but not an implementation. Here's an example:

```go
type Logger interface {
  Log(message string)
}
```

You might be wondering what purpose this could possibly serve. Interfaces help decouple your code from specific implementations. For example, we might have various types of loggers:

```go
type SqlLogger struct { ... }
type ConsoleLogger struct { ... }
type FileLogger struct { ... }
```

Yet by programming against the interface, rather than these concrete implementations, we can easily change (and test) which we use without any impact to our code.

How would you use one? Just like any other type, it could be a structure's field:

```go
type Server struct {
  logger Logger
}
```

or a function parameter (or return value):

```go
func process(logger Logger) {
  logger.Log("hello!")
}
```

In a language like C# or Java, we have to be explicit when a class implements an interface:

```go
public class ConsoleLogger : Logger {
  public void Logger(message string) {
    Console.WriteLine(message)
  }
}
```

In Go, this happens implicitly. If your structure has a function name `Log` with a `string` parameter and no return value, then it can be used as a `Logger`. This cuts down on the verboseness of using interfaces:

```go
type ConsoleLogger struct {}
func (l ConsoleLogger) Log(message string) {
  fmt.Println(message)
}
```

It also tends to promote small and focused interfaces. The standard library is full of interfaces. The `io` package has a handful of popular ones such as `io.Reader`, `io.Writer`, and `io.Closer`. If you write a function that expects a parameter that you'll only be calling `Close()` on, you absolutely should accept an `io.Closer` rather than whatever concrete type you're using.

Interfaces can also participate in composition. And, interfaces themselves can be composed of other interfaces. For example, `io.ReadCloser` is an interface composed of the `io.Reader` interface as well as the `io.Closer` interface.

Finally, interfaces are commonly used to avoid cyclical imports. Since they don't have implementations, they'll have limited dependencies.

## Before You Continue

Ultimately, how you structure your code around Go's workspace is something that you'll only feel comfortable with after you've written a couple of non-trivial projects. What's most important for you to remember is the tight relationship between package names and your directory structure (not just within a project, but within the entire workspace).

The way Go handles visibility of types is straightforward and effective. It's also consistent. There are a few things we haven't looked at, such as constants and global variables but rest assured, their visibility is determined by the same naming rule.

Finally, if you're new to interfaces, it might take some time before you get a feel for them. However, the first time you see a function that expects something like `io.Reader`, you'll find yourself thanking the author for not demanding more than he or she needed.

