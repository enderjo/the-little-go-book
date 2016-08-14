# Chapter 6 - Concurrency
# 第六章 - 并发

Go is often described as a concurrent-friendly language. The reason for this is that it provides a simple syntax over two powerful mechanisms: goroutines and channels.

Go常被描述为是一种适用于并发的语言。是因为它在两个强大的机制提供了简法的语法支持：`go协程`和`通道`。

## Goroutines
## Go协程

A goroutine is similar to a thread, but it is scheduled by Go, not the OS. Code that runs in a goroutine can run concurrently with other code. Let's look at an example:

一个Go协程和一个线程类似，只不这它是由Go,而不是系统来调度的。在协程中的代码可以和其他代码并发执行。让我们看一个例子：

```go
package main

import (
  "fmt"
  "time"
)

func main() {
  fmt.Println("start")
  go process()
  time.Sleep(time.Millisecond * 10) // this is bad, don't do this! 这样不好，不能这么做！
  fmt.Println("done")
}

func process() {
  fmt.Println("processing")
}
```

There are a few interesting things going on here, but the most important is how we start a goroutine. We simply use the `go` keyword followed by the function we want to execute. If we just want to run a bit of code, such as the above, we can use an anonymous function. Do note that anonymous functions aren't only used with goroutines, however.

这里有几个有趣的地方，但最重要的是我们如何开启一个Go协程。我们只是简单的使用了`go`关键字后紧跟我们需的执行的函数。如果我们只是要运行一小段代码，比如上面的例子，我们可以使用匿名函数。但是记住，匿名函数不只适用于Go协程。

```go
go func() {
  fmt.Println("processing")
}()
```

Goroutines are easy to create and have little overhead. Multiple goroutines will end up running on the same underlying OS thread. This is often called an M:N threading model because we have M application threads (goroutines) running on N OS threads. The result is that a goroutine has a fraction of overhead (a few KB) than OS threads. On modern hardware, it's possible to have millions of goroutines.

Go协程创建简单和开销小。多个Go协程最终会运行在一个系统线程中。这通常称为`M:N`线程模型，因为我们有`M`个应用线程（Go协程）运行在`N`个系统线程上。结果就是，一个Go协程的开销比系统线程小（一般都是几KB）。在现代的硬件上，有可能创建成千上万个Go协程。

Furthermore, the complexity of mapping and scheduling is hidden. We just say *this code should run concurrently* and let Go worry about making it happen.

此外，因为隐藏了映射和调度的复杂性。我们只需要说*这段代码需要并发执行*，然后让Go自己来运行它。

If we go back to our example, you'll notice that we had to `Sleep` for a few milliseconds. That's because the main process exits before the goroutine gets a chance to execute (the process doesn't wait until all goroutines are finished before exiting). To solve this, we need to coordinate our code.

回到我们的例子中，你将会注意到我们使用了`Sleep`让程序等待了几毫秒。这是让主进程在退出前有机会去执行协程（主进程退出时不会等待所有协程都执行结束）。为了解决这个问题，我们必须让代码协同。

## Synchronization
## 同步

Creating goroutines is trivial, and they are so cheap that we can start many; however, concurrent code needs to be coordinated. To help with this problem, Go provides `channels`. Before we look at `channels`, I think it's important to understand a little bit about the basics of concurrent programming.

创建Go协程是容易的，而且他们的开销很小，所以我们可以开启很多Go协程；但是并发代码需要协同。为了帮助我们解决这个问题，Go提供了`通道`。在我们继续`通道`之前，我觉得有必要先了解一些并发编程的基础知识。

Writing concurrent code requires that you pay specific attention to where and how you read and write values. In some ways, it's like programming without a garbage collector -- it requires that you think about your data from a new angle, always watchful for possible danger. Consider:

在编写并发执行的代码时，你需要特别的注意在哪里和如何读写一个值。出于某些原因，例如没有垃圾回收的语言，需要你从一个新的角度去考虑你的数据，总是警惕着可能存在的危险。例如：

```go
package main

import (
  "fmt"
  "time"
)

var counter = 0

func main() {
  for i := 0; i < 2; i++ {
    go incr()
  }
  time.Sleep(time.Millisecond * 10)
}

func incr() {
  counter++
  fmt.Println(counter)
}
```

What do you think the output will be?

你觉得输出的会是什么呢？

If you think the output is `1, 2` you're both right and wrong. It's true that if you run the above code, you'll very likely get that output. However, the reality is that the behavior is undefined. Why? Because we potentially have multiple (two in this case) goroutines writing to the same variable, `counter`, at the same time. Or, just as bad, one goroutine would be reading `counter` while another writes to it.

如果你觉得输出是`1`和`2`，不能说你对或者错。如果你运行上面的代码，你很有可能得到那样的输出。但是，实际上这个输出是不确定的。为什么？因为我们可能有多个（这里是2个）Go协程同时写同一个变量`counter`。或者更糟的情况是一个协程正在读`counter`，而另一个协程正在写`counter`。

Is that really a danger? Yes, absolutely. `counter++` might seem like a simple line of code, but it actually gets broken down into multiple assembly statements -- the exact nature is dependent on the platform that you're running. It's true that, in this example, the most likely case is things will run just fine. However, another possible outcome would be that they both see `counter` when its equal to `0` and you get an output of `1, 1`. There are worse possibilities, such as system crashes or accessing an arbitrary piece of data and incrementing it!

这很危险吗？是的，绝对的。`counter++`似乎看起来只是一行简单的代码，但是实际上它被拆分为很多汇编指令————具体依赖于你运行的软件和硬件平台。是的，在上面的例子中，确实在大多数情况下运行良好。但是，其他一些平台可能的输出结果是`1, 1`，因为两个协程看到的`counter`都是`0`。还有更糟的情况是，比如系统崩溃或者访问到一个随机值并递增它。

The only concurrent thing you can safely do to a variable is to read from it. You can have as many readers as you want, but writes need to be synchronized. There are various ways to do this, including using some truly atomic operations that rely on special CPU instructions. However, the most common approach is to use a mutex:

在并发编程中维一安全的事情就是读一个变量。无论你想读多少次都可以，但是写变量时必须是同步的。有几种方式来实现，包括一些在特定CPU架构上真正的原子操作。但是，最常见的方式就是用互斥锁：

```go
package main

import (
  "fmt"
  "time"
  "sync"
)

var (
  counter = 0
  lock sync.Mutex
)

func main() {
  for i := 0; i < 2; i++ {
    go incr()
  }
  time.Sleep(time.Millisecond * 10)
}

func incr() {
  lock.Lock()
  defer lock.Unlock()
  counter++
  fmt.Println(counter)
}
```

A mutex serializes access to the code under lock. The reason we simply define our lock as `lock sync.Mutex` is because the default value of a `sync.Mutex` is unlocked.

互斥锁会顺序化有锁的代码的访问。因为`sync.Mutex`默认值是未锁状态，所以我们简单的定义了一个锁`lock sync.Mutex`。

Seems simple enough? The example above is deceptive. There's a whole class of serious bugs that can arise when doing concurrent programming. First of all, it isn't always so obvious what code needs to be protected. While it might be tempting to use coarse locks (locks that cover a large amount of code), that undermines the very reason we're doing concurrent programming in the first place. We generally want fine locks; else, we end up with a ten-lane highway that suddenly turns into a one-lane road.

看起来足够简单？上面的例子有欺骗性。在并发编程时，会碰到一系列很严重的bug。首先，那些需要被保护代码通常都不是这么明显。虽然它可能是想使用一个粗锁（涵盖了大量代码的锁），但这破坏了并发编程首要原则。我们需要适度的锁，或者说，我们最终由一个10快车道的突然转变成一个单车道。

The other problem has to do with deadlocks. With a single lock, this isn't a problem, but if you're using two or more locks around the same code, it's dangerously easy to have situations where goroutineA holds lockA but needs access to lockB, while goroutineB holds lockB but needs access to lockA.

另一个问题是如何处理死锁。只有一个锁的时候，这不是问题，但是如果你在相同的代码中使用2个或者更多的锁，就很容易出现一种危险的情况，即协程A拥有锁`lockA`，想去访问锁`lockB`，同时协程B拥有`lockB`并需要访问锁`lockA`。

It actually *is* possible to deadlock with a single lock, if we forget to release it. This isn't as dangerous as a multi-lock deadlock (because those are *really* tough to spot), but just so you can see what happens, try running:

实际上使用一个锁*也有*可能发生死锁，如果我们忘记释放它时。但是这和多个锁引起的死锁为比起来，危害性不大（因为这*真的*很少出现），但只是想让你看会发生什么，试试下面的代码：

```go
package main

import (
  "time"
  "sync"
)

var (
  lock sync.Mutex
)

func main() {
  go func() { lock.Lock() }()
  time.Sleep(time.Millisecond * 10)
  lock.Lock()
}
```

There's more to concurrent programming than what we've seen so far. For one thing, there's another common mutex called a read-write mutex. This exposes two locking functions: one to lock for reading and one to lock for writing. This distinction allows multiple simultaneous readers while ensuring that writing is exclusive. In Go, `sync.RWMutex` is such a lock. In addition to the `Lock` and `Unlock` methods of a `sync.Mutex`, it also exposes `RLock` and `RUnlock` methods; where `R` stands for *Read*. While read-write mutexes are commonly used, they place an additional burden on developers: we must now pay attention to not only when we're accessing data, but also how.

接下来我们会介绍更多的并发编程。一方面，另一个常见的互斥锁叫读写互斥锁。它主要提供2中锁功能：一个读锁定和一个写锁定。在Go中，`sync.RWMutex`就是这种锁。另外`sync.Mutex`结构不但提供了`Lock`和`Unlock`方法，也提供了`RLock`和`RLock`方法，这里的`R`代表*读*。虽然读写锁很常用，但是他们也给开发者带来一些额外的负担：我们不但要关注我们何时访问数据，而且也要关注如何访问。

Furthermore, part of concurrent programming isn't so much about serializing access across the narrowest possible piece of code; it's also about coordinating multiple goroutines. For example, sleeping for 10 milliseconds isn't a particularly elegant solution. What if a goroutine takes more than 10 milliseconds? What if it takes less and we're just wasting cycles? Also, what if instead of just waiting for goroutines to finish, we want to tell one *hey, I have new data for you to process!*?

此外，部分并发编程不只是通过为数不多代码按顺序的访问变量，也需要协调多个go协程。例如，休眠10毫秒不是一种优雅的方法。如果一个Go协程运行的时间超过10毫秒呢？如果Go协程运行时间少于10毫秒，我们只是浪费了cpu？又或者可以等待Go协程运行完毕，我们告诉另外一个Go协程*嗨，我有一些新数据给你处理？*

These are all things that are doable without `channels`. Certainly for simpler cases, I believe you **should** use primitives such as `sync.Mutex` and `sync.RWMutex`, but as we'll see in the next section, `channels` aim at making concurrent programming cleaner and less error-prone.

所有的这些事在不使用通道的情况下也都是可以实现的。当然，对于更简单的例子，我认为你应该使用基本的功能例如`sync.Mutex`和`sync.RWMutex`，但是在下一节我们将看到，通道的目的是为了使并发编程更清晰和不易出错。

## Channels
## 通道

The challenge with concurrent programming stems from sharing data. If your goroutines share no data, you needn't worry about synchronizing them. That isn't an option for all systems, however. In fact, many systems are built with the exact opposite goal in mind: to share data across multiple requests. An in-memory cache or a database, are good examples of this. This is becoming an increasingly common reality.

并发编程的最在挑战来自共享数据。如果你的Go协程没有共享数据，你不需要担心他们之间的同步。但是这不是所有系统的选择。事实上，许多系统的构建就是为了：在多个请求中共享数据。内存缓存或者数据库，都是很好的例子。这也成为越来越普遍的事实。

Channels help make concurrent programming saner by taking shared data out of the picture. A channel is a communication pipe between goroutines which is used to pass data. In other words, a goroutine that has data can pass it to another goroutine via a channel. The result is that, at any point in time, only one goroutine has access to the data.

通过共享数据规划，通道使并发编程更清晰。一个通道是一个通信管道用于Go协程之间的数据传递。换一句话来说。一个Go协程可以通过通道来把数据传递给另一个Go协程。这样做的结果就是，无论什么时间节点，都只有一个Go协程可以访问共享数据。

A channel, like everything else, has a type. This is the type of data that we'll be passing through our channel. For example, to create a channel which can be used to pass an integer around, we'd do:

通道和其他类型一样有类型。这个类型就是我们将在通道中传递的数据类型。例如，创建一个用来传递整数的通道，我们这样做：

```go
c := make(chan int)
```

The type of this channel is `chan int`. Therefore, to pass this channel to a function, our signature looks like:

这个通道的类型是`chan int`。因此，将这个通道传递给一个函数是，可以这样声明：

```go
func worker(c chan int) { ... }
```

Channels support two operations: receiving and sending. We send to a channel by doing:

通道支持2种操作：接收和发送。我们可以使用下面方式往通道发送数据：

```
CHANNEL <- DATA
```

and receive from one by doing

然后可以使用下面方式从通道接收数据：

```
VAR := <-CHANNEL
```

The arrow points in the direction that data flows. When sending, the data flows into the channel. When receiving, the data flows out of the channel.

箭头的方向就是数据的流动方向。当发送数据时，数据流入通道。当发送数据时，数据是流出通道。

The final thing to know before we look at our first example is that receiving and sending to and from a channel is blocking. That is, when we receive from a channel, execution of the goroutine won't continue until data is available. Similarly, when we send to a channel, execution won't continue until the data is received.

最后，在看我们的第一个例子之前，从一个通道接收或者发送数据时会阻塞。也就是说，当我们从一个通道接收数据时，直到数据可用Go协程才会继续执行。类似的，往一个通道发送数据时，在数据被接收之前Go协程也不会继续执行。

Consider a system with incoming data that we want to handle in separate goroutines. This is a common requirement. If we did our data-intensive processing on the goroutine which accepts the incoming data, we'd risk timing out clients. First, we'll write our worker. This could be a simple function, but I'll make it part of a structure since we haven't seen goroutines used like this before:

假设这样的一个系统，我们想通过不同的协程来处理输入数据。这是一个常见的需求。如果通过Go协程接收输入的数据并进行数据密集型处理，那么在客户端会有超时风险。首先，我们将写出我们的处理器。这是一个简单的函数，但是我会让它变成一个结构体的部分，因为我们之前从来没有这样使用过Go协程：

```go
type Worker struct {
  id int
}

func (w Worker) process(c chan int) {
  for {
    data := <-c
    fmt.Printf("worker %d got %d\n", w.id, data)
  }
}
```

Our worker is simple. It waits until data is available then "processes" it. Dutifully, it does this in a loop, forever waiting for more data to process.

我们的处理器很简单。它会一直等待直到数据可用并“处理”它。它通过一个循环来实现，永久等待更多的数据来处理。

To use this, the first thing we'd do is start some workers:

为了使用上面的代码，我们首先要做的是启动一些处理器：

```go
c := make(chan int)
for i := 0; i < 4; i++ {
  worker := Worker{id: i}
  go worker.process(c)
}
```

And then we can give them some work:

然后我们可以给他们一些工作：

```go
for {
  c <- rand.Int()
  time.Sleep(time.Millisecond * 50)
}
```

Here's the complete code to make it run:

下面是完整的可执行代码：

```go
package main

import (
  "fmt"
  "time"
  "math/rand"
)

func main() {
  c := make(chan int)
  for i := 0; i < 5; i++ {
    worker := &Worker{id: i}
    go worker.process(c)
  }

  for {
    c <- rand.Int()
    time.Sleep(time.Millisecond * 50)
  }
}

type Worker struct {
  id int
}

func (w *Worker) process(c chan int) {
  for {
    data := <-c
    fmt.Printf("worker %d got %d\n", w.id, data)
  }
}
```

We don't know which worker is going to get what data. What we do know, what Go guarantees, is that the data we send to a channel will only be received by a single receiver.

我们不知道哪个处理器将获得数据。我们所知道的是，Go确保了往一个通道发送数据时，仅有一个单独的接收器可以接受。

Notice that the only shared state is the channel, which we can safely receive from and send to concurrently. Channels provide all of the synchronization code we need and also ensure that, at any given time, only one goroutine has access to a specific piece of data.

需要指出的是通道是唯一的共享方式，通过通道我们可以并发安全的发送和接收数据。通道提供了我们需要的所有同步代码，并且也确保在任意的特定时刻只有一个Go协程可以访问一个特定的数据。

### Buffered Channels
### 带缓存的通道

Given the above code, what happens if we have more data coming in than we can handle? You can simulate this by changing the worker to sleep after it has received data:

在上面的代码中，如果输入的数据超过我们可以处理的数据会发生什么？你可以模拟这种场景，在处理器收到数据后执行`time.Sleep`：

```go
for {
  data := <-c
  fmt.Printf("worker %d got %d\n", w.id, data)
  time.Sleep(time.Millisecond * 500)
}
```

What's happening is that our main code, the one that accepts the user's incoming data (which we just simulated with a random number generator) is blocking as it sends to the channel because no receiver is available.

在`main`函数中会发什么呢？接收用户的输入数据（这里通过一个随机的数字生成器模拟）会被阻塞，因为往通道发送数据时没有可用的接收者。

In cases where you need high guarantees that the data is being processed, you probably will want to start blocking the client. In other cases, you might be willing to loosen those guarantees. There are a few popular strategies to do this. The first is to buffer the data. If no worker is available, we want to temporarily store the data in some sort of queue. Channels have this buffering capability built-in. When we created our channel with `make`, we can give our channel a length:

在这种情况下，你需要确保数据被处理，你可能想要让客户端阻塞。在其他情况下，你可能愿意不确保数据被处理。这里有一些流行的策略能完成此事。首先是将数据缓存起来。如果没有处理器可用，我们想将数据暂时存放在一个有序的队列中。通道内置缓存能力。当我们使用`make`创建一个通道时，我们可以指定通道的长度：

```go
c := make(chan int, 100)
```

You can make this change, but you'll notice that the processing is still choppy. Buffered channels don't add more capacity; they merely provide a queue for pending work and a good way to deal with a sudden spike. In our example, we're continuously pushing more data than our workers can handle.

你可以做这样的修改，但是你将注意到处理过程仍然震荡。缓冲通道没有增加处理能力，他们只是为挂起的工作提供了一个队列和应对突发尖峰的好方法。在我们示例中，我们持续不断的发送超出我们处理器可以处理的数据。

Nevertheless, we can get a sense that the buffered channel is, in fact, buffering by looking at the channel's `len`:

尽管如此，事实上，我们可以查看通道的`len`,来了解到带缓存的通道的缓冲情况：

```go
for {
  c <- rand.Int()
  fmt.Println(len(c))
  time.Sleep(time.Millisecond * 50)
}
```

You can see that it grows and grows until it fills up, at which point sending to our channel start to block again.

你可以看到它的长度在不断增大，直到装满为止，此时，往通道发送的数据又开始被阻塞。

### Select
### 选择

Even with buffering, there comes a point where we need to start dropping messages. We can't use up an infinite amount of memory hoping a worker frees up. For this, we use Go's `select`.

即使借助缓存，有一点需要指出的是，我们需要开始丢弃一些消息，我们不能使用一个无限大的内存，并指望人工的释放它。所以我们使用Go的`select`。

Syntactically, `select` looks a bit like a switch. With it, we can provide code for when the channel isn't available to send to. First, let's remove our channel's buffering so that we can clearly see how `select` works:

在语法结构上，`select`看起来有点类似`switch`。通过`select`，我们能写出一些针对通道不可写情况下的代码。首先，让我们去掉我们通道的缓存，这样可以更清晰的看到`select`是如何工作的。

```go
c := make(chan int)
```

Next, we change our `for` loop:

接下来，我们修改`for`循环：

```go
for {
  select {
  case c <- rand.Int():
    //optional code here
  default:
    //this can be left empty to silently drop the data
    fmt.Println("dropped")
  }
  time.Sleep(time.Millisecond * 50)
}
```

We're pushing out 20 messages per second, but our workers can only handle 10 per second; thus, half the messages get dropped.

我们每秒往通道中发送20个信息，但是我们的处理器每秒只能处理10个信息；因此，有一半的信息被丢弃。

This is only the start of what we can accomplish with `select`. A main purpose of select is to manage multiple channels. Given multiple channels, `select` will block until the first one becomes available. If no channel is available, `default` is executed if one is provided. A channel is randomly picked when multiple are available.

这仅仅只是我们使用`select`完成一些事的开始。使用`select`的最主要目的是通过它管理多个通道。给定多个通道，`select`将阻塞直到有一个通道可用。如果没有可用的通道，当提供了`default`语句时，执行该分支。当多个通道都可用时，选择其中的一个通道是随机的。

It's hard to come up with a simple example that demonstrates this behavior as it's a fairly advanced feature. The next section might help illustrate this though.

很难想出一个简单的例子来证明这种行为，因为这是一种高级特性。在下一小节可能有助于说明这个问题。

### Timeout
### 超时

We've looked at buffering messages as well as simply dropping them. Another popular option is to timeout. We're willing to block for some time, but not forever. This is also something easy to achieve in Go. Admittedly, the syntax might be hard to follow but it's such a neat and useful feature that I couldn't leave it out.

我们已经学习了缓存消息和简单丢弃消息。另外一种比较流行的做法是使用超时。我们将阻塞一段时间，但不是一直阻塞。在Go中这很容易实现。老实说，这个语法有点难于接受，但是它是比较灵活和有用的特性，我基本不能没有它。

To block for a maximum amount of time, we can use the `time.After` function. Let's look at it then try to peek beyond the magic. To use this, our sender becomes:

为了达到阻塞的最大时限，我们可以使用`time.After`函数。让我们看看它，并试着看出其中的魔法。为了使用这种方式，我们的发送器需要修改为：

```go
for {
  select {
  case c <- rand.Int():
  case <-time.After(time.Millisecond * 100):
    fmt.Println("timed out")
  }
  time.Sleep(time.Millisecond * 50)
}
```

`time.After` returns a channel, so we can `select` from it. The channel is written to after the specified time expires. That's it. There's nothing more magical than that. If you're curious, here's what an implementation of `after` could look like:

`time.After`返回一个通道，所以我们可以对它使用`select`语法。当指定的时间到期时这个通道被写入。就是如此。没有其他更多的魔法了。如果你还是好奇，这里有一个`after`的实现：

```go
func after(d time.Duration) chan bool {
  c := make(chan bool)
  go func() {
    time.Sleep(d)
    c <- true
  }()
  return c
}
```

Back to our `select`, there are a couple of things to play with. First, what happens if you add the `default` case back? Can you guess? Try it. If you aren't sure what's going on, remember that `default` fires immediately if no channel is available.

回到我们的`select`中来，还一些内容可以研究。首先，如果添加了`default`条件会发生会什么呢？你可以猜猜？试试。如果你不确定会发生什么，记住如果没有可用的通道`default`会立即被触发。

Also, `time.After` is a channel of type `chan time.Time`. In the above example, we simply discard the value that was sent to the channel. If you want though, you can receive it:

同时，`time.After`的通道类型是`chan time.Time`。上面的例子中，我们简单的丢弃了发送给通道的值。如果你相要，你可以这样接收它：

```go
case t := <-time.After(time.Millisecond * 100):
  fmt.Println("timed out at", t)
```

Pay close attention to our `select`. Notice that we're sending to `c` but receiving from `time.After`. `select` works the same regardless of whether we're receiving from, sending to, or any combination of channels:

更近一步的看我们的`select`。注意我们向`c`发送数据，但是从`time.After`接收数据。`select`对无论是接收数据，发送数据，还是其他通道的组合，都是一样对待的：

* The first available channel is chosen.
* If multiple channels are available, one is randomly picked.
* If no channel is available, the default case is executed.
* If there's no default, select blocks.

* 第一个可用的通道被选择。
* 如果有多个通道可用，随机选择一个。
* 如果没有通道可用，默认条件被执行。
* 如果没有默认条件，选择阻塞。

Finally, it's common to see a `select` inside a `for`. Consider:

最后，`select`通常在`for`循环中使用。例如：

```go
for {
  select {
  case data := <-c:
    fmt.Printf("worker %d got %d\n", w.id, data)
  case <-time.After(time.Millisecond * 10):
    fmt.Println("Break time")
    time.Sleep(time.Second)
  }
}
```

## Before You Continue
## 继续之前

If you're new to the world of concurrent programming, it might all seem rather overwhelming. It categorically demands considerably more attention and care. Go aims to make it easier.

如果你是并发编程的新手，它可能显得相当庞大。它绝对是需要相当多的重视和关注。 Go的目标就是使其更容易。

Goroutines effectively abstract what's needed to run concurrent code. Channels help eliminate some serious bugs that can happen when data is shared by eliminating the sharing of data. This doesn't just eliminate bugs, but it changes how one approaches concurrent programming. You start to think about concurrency with respect to message passing, rather than dangerous areas of code.

Go协程有效的抽象了需要并发执行的代码。通道协助消除了可能在数据共享时的严重Bug。这不只是消除了Bug,更是改变了并发编程的开发方式。你开始使用消息传递的方式来考虑并发，而不是危险的共享代码。

Having said that, I still make extensive use of the various synchronization primitives found in the `sync` and `sync/atomic` packages. I think it's important to be comfortable with both. I encourage you to first focus on channels, but when you see a simple example that needs a short-lived lock, consider using a mutex or read-write mutex.

虽然这么说，我仍然广泛使用的各种同步原语中发现的“同步”和“同步/原子”包。我觉得这两种情况都要适应是很重要的。我鼓励你先聚焦在通道上，但是如果你碰到只是需要短暂的多锁，建议你使用互斥锁或者读写互斥锁。

# Conclusion
# 结论

I recently heard Go described as a *boring* language. Boring because it's easy to learn, easy to write and, most importantly, easy to read. Perhaps, I did this reality a disservice. We *did* spend three chapters talking about types and how to declare variables after all.

我最近听说Go被描述为一门*单调的*语言。单调是因为它很容易学习，很容易编写，最为重要的是，很容易读。也许，我这是在帮倒忙，我确实花了三个章节来介绍类型和如何申请变量。

If you have a background in a statically typed language, much of what we saw was probably, at best, a refresher. That Go makes pointers visible and that slices are thin wrappers around arrays probably isn't overwhelming to seasoned Java or C# developers.

如果你在静态类型语言的背景，大多数我们看到的，充其量只是复习。同时Go的指针可见性和切片的轻量封装对经验丰富的Java的C＃开发人员来说不算什么。

If you've mostly been making use of dynamic languages, you might feel a little different. It *is* a fair bit to learn. Not least of which is the various syntax around declaration and initialization. Despite being a fan of Go, I find that for all the progress towards simplicity, there's something less than simple about it. Still, it comes down to some basic rules (like you can only declare variable once and `:=` does declare the variable) and fundamental understanding (like `new(X)` or `&X{}` only allocate memory, but slices, maps and channels require more initialization and thus, `make`).

如果你更多的是使用动态语言，你可能会觉得有点不同。这*是*一点公平的学习。

Beyond this, Go gives us a simple but effective way to organize our code. Interfaces, return-based error handling, `defer` for resource management and a simple way to achieve composition.

除此之外，Go提供了一个简洁但又高效的方式来组织我们的代码。接口，基于返回值的错误处理，用于资源管理的`defer`和简单的实现组合。

Last but not least is the built-in support for concurrency. There's little to say about goroutines other than they’re effective and simple (simple to use anyway). It's a good abstraction. Channels are more complicated. I always think it's important to understand basics before using high-level wrappers. I *do* think learning about concurrent programming without channels is useful. Still, channels are implemented in a way that, to me, doesn't feel quite like a simple abstraction. They are almost their own fundamental building block. I say this because they change how you write and think about concurrent programming. Given how hard concurrent programming can be, that is definitely a good thing.
