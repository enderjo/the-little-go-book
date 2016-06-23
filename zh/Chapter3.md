# Chapter 3 - Maps, Arrays and Slices
# 第三章 - 字典 ，数组和切片

So far we've seen a number of simple types and structures. It's now time to look at arrays, slices and maps.

目前为止我们看了些简单类型和结构体。现在是时候来看看数组，切片和字典了。

## Arrays
## 数组

If you come from Python, Ruby, Perl, JavaScript or PHP (and more), you're probably used to programming with *dynamic arrays*. These are arrays that resize themselves as data is added to them. In Go, like many other languages, arrays are fixed. Declaring an array requires that we specify the size, and once the size is specified, it cannot grow:

如果你从Python、Ruby、Perl、JavaScript或者PHP（还有更多），你可能使用过*动态数组*。这些数组当数据添加进来可以调整大小。在Go中，和其他语言一样，数组是固定大小的。申请一个数组需要我们指定它的大小。申明一个数组时需要指定大小，一旦大小指定了，就不能增长了：

```go
var scores [10]int
scores[0] = 339
```

The above array can hold up to 10 scores using indexes `scores[0]` through `scores[9]`. Attempts to access an out of range index in the array will result in a compiler or runtime error.

上面的这个数组从`scores[0]`到`scores[9]`可以容纳10个分数。尝试访问超出数组索引的会报编译或者运行时错误。

We can initialize the array with values:

我们可以带值初始化数组：

```go
scores := [4]int{9001, 9333, 212, 33}
```

We can use `len` to get the length of the array. `range` can be used to iterate over it:

我们可能通过`len`来获取数组的长度。可以用`range`来迭代数组。

```go
for index, value := range scores {

}
```

Arrays are efficient but rigid. We often don't know the number of elements we'll be dealing with upfront. For this, we turn to slices.

数组高效但不灵活。我们常常不能预先知道有多少元素需要处理。因此，我们使用切片。

## Slices
## 切片

In Go, you rarely, if ever, use arrays directly. Instead, you use slices. A slice is a lightweight structure that wraps and represents a portion of an array. There are a few ways to create a slice, and we'll go over when to use which later on. The first is a slight variation on how we created an array:

在Go中，你很少，或者根本不，直接使用数组。反而，你使用切片。切片是对数组一个轻量型封装。有几种方式来创建一个切片，我们会全部过一遍。第一种方式和创建数组有一点小小的变化。

```go
scores := []int{1,4,293,4,9}
```

Unlike the array declaration, our slice isn't declared with a length within the square brackets. To understand how the two are different, let's see another way to create a slice, using `make`:

和申请数组不同，我们的切片没有在中括号内指定长度。要理解这两者的不同，我们用另一种方式创建切片，使用`make`：

```go
scores := make([]int, 10)
```

We use `make` instead of `new` because there's more to creating a slice than just allocating the memory (which is what `new` does). Specifically, we have to allocate the memory for the underlying array and also initialize the slice.  In the above, we initialize a slice with a length of 10 and a capacity of 10. The length is the size of the slice, the capacity is the size of the underlying array. Using `make` we can specify the two separately:

我们使用`make`来替代`new`因为创建一个切片比只是分配内存（`new`做的事情）要复杂一些。特别的，我们需要分配底层数组和初始化切片。上面的例子，我们初始化了一个长度和容量为10的切片。长度是切片的大小，容量时底层数组的大小。使用`make`时可以分开提定这两个值：

```go
scores := make([]int, 0, 10)
```

This creates a slice with a length of 0 but with a capacity of 10. (If you're paying attention, you'll note that `make` and `len` *are* overloaded. Go is a language that, to the frustration of some, makes use of features which aren't exposed for developers to use.)

这创建了一个长度为0但容量为10的切片。（如果你留心，你会注意到`make`和`len`*是*重载的。Go有的时候令人沮丧，一些在使用的功能没有暴露给开发者使用。）

To better understand the interplay between length and capacity, let's look at some examples:

为了更好的理解长度和容量之间的相互作用，让我们来看一个例子：

```go
func main() {
  scores := make([]int, 0, 10)
  scores[5] = 9033
  fmt.Println(scores)
}
```

Our first example crashes. Why? Because our slice has a length of 0. Yes, the underlying array has 10 elements, but we need to explicitly expand our slice in order to access those elements. One way to expand a slice is via `append`:

我们的第一个程序崩溃了。为什么呢？因我们的切片的长度为0。是的，底层数组有10个元素，但是我们需要显示的扩展切片来访问这些元素。一种扩展方式是使用`append`：

```go
func main() {
  scores := make([]int, 0, 10)
  scores = append(scores, 5)
  fmt.Println(scores) // prints [5]
}
```

But that changes the intent of our original code. Appending to a slice of length 0 will set the first element. For whatever reason, our crashing code wanted to set the element at index 5. To do this, we can re-slice our slice:

上面的修改改变了我们的原始代码的意图。扩展一个长度为0的切片会设置第一个元素。无论什么原因，我们崩溃的代码想要的是修改索引为5的元素。 我们可以重切片一次我们的切片：


```go
func main() {
  scores := make([]int, 0, 10)
  scores = scores[0:6]
  scores[5] = 9033
  fmt.Println(scores)
}
```

How large can we resize a slice? Up to its capacity which, in this case, is 10. You might be thinking *this doesn't actually solve the fixed-length issue of arrays.* It turns out that `append` is pretty special. If the underlying array is full, it will create a new larger array and copy the values over (this is exactly how dynamic arrays work in PHP, Python, Ruby, JavaScript, ...). This is why, in the example above that used `append`, we had to re-assign the value returned by `append` to our `scores` variable: `append` might have created a new value if the original had no more space.

我们调整切片最大是多少？这是由它的容量决定的，在本例中，是10。你可能会想*这没有从本质上解决数能固定和长度的问题*。事实上，`append`是非常特殊的。当底层数组满了，它会创建一个新的数组，并把数值拷贝过来（PHP, Python, Ruby, JavaScript等也是这么做的）。这也就是为什么，我们上面的代码，使用了`append`之后，我们需要把`append`的返回值重新赋值给`scores`的原因：`append`会产生一个新的值如果原始的空间不足。

If I told you that Go grew arrays with a 2x algorithm, can you guess what the following will output?

如果我告诉你说Go是近两倍的算法来增长数组，那下面的代码会输出什么？

```go
func main() {
  scores := make([]int, 0, 5)
  c := cap(scores)
  fmt.Println(c)

  for i := 0; i < 25; i++ {
    scores = append(scores, i)

    // if our capacity has changed,
    // Go had to grow our array to accommodate the new data
    if cap(scores) != c {
      c = cap(scores)
      fmt.Println(c)
    }
  }
}
```

The initial capacity of `scores` is 5. In order to hold 20 values, it'll have to be expanded 3 times with a capacity of 10, 20 and finally 40.

`scores`最初的容量是5。为了容纳20个值，它将会扩展3次，分别是10，20和40。

As a final example, consider:

来思考下最后一个例子：

```go
func main() {
  scores := make([]int, 5)
  scores = append(scores, 9332)
  fmt.Println(scores)
}
```

Here, the output is going to be `[0, 0, 0, 0, 0, 9332]`. Maybe you thought it would be `[9332, 0, 0, 0, 0]`? To a human, that might seem logical. To a compiler, you're telling it to append a value to a slice that already holds 5 values.

这里，输出是`[0, 0, 0, 0, 0, 9332]`。可能你会想它应该是`[9332, 0, 0, 0, 0]`？对于人来说这可能符合逻辑。对于编译器来说，你告诉它的就是要扩展一个已经有5个值的切片。

Ultimately, there are four common ways to initialize a slice:

最后，有四种常用的方式来初始化一个切片：

```go
names := []string{"leto", "jessica", "paul"}
checks := make([]bool, 10)
var names []string
scores := make([]int, 0, 20)
```

When do you use which? The first one shouldn't need much of an explanation. You use this when you know the values that you want in the array ahead of time.

何时用哪一种呢？第一种不需要过多的解释。当你知道所有的值并且你要的是数组头的时候使用。

The second one is useful when you'll be writing into specific indexes of a slice. For example:

当你需要写入切片的指定索引时，第二种就很有用。比如：

```go
func extractPowers(saiyans []*Saiyans) []int {
  powers := make([]int, len(saiyans))
  for index, saiyan := range saiyans {
    powers[index] = saiyan.Power
  }
  return powers
}
```

The third version is a nil slice and is used in conjunction with `append`, when the number of elements is unknown.

当知道有多少元素的时候，就可使用第三种是一个空切片和`append`配合使用。

The last version lets us specify an initial capacity; useful if we have a general idea of how many elements we'll need.

当我们对需要多少元素有多少了解时使用最后一种方式来指定初始容量。

Even when you know the size, `append` can be used. It's largely a matter of preference:

即使你知道了大小，`append`依然可以使用。这很大程度上是一个偏好问题：

```go
func extractPowers(saiyans []*Saiyans) []int {
  powers := make([]int, 0, len(saiyans))
  for _, saiyan := range saiyans {
    powers = append(powers, saiyan.Power)
  }
  return powers
}
```

Slices as wrappers to arrays is a powerful concept. Many languages have the concept of slicing an array. Both JavaScript and Ruby arrays have a `slice` method. You can also get a slice in Ruby by using `[START..END]` or in Python via `[START:END]`. However, in these languages, a slice is actually a new array with the values of the original copied over. If we take Ruby, what's the output of the following?

切片做为数组的封装是一个很有用的概念。许多语言有数组切片的概念。JavaScript和Ruby的数组都有`slice`方法。你可以对通过`[START..END]`或者在Python中用`[START:END]`的方式取得一个切片。但是，在这些语言中切片是对原数组的拷贝。如果我们使用Ruby，下面的代码会输出什么？

```go
scores = [1,2,3,4,5]
slice = scores[2..4]
slice[0] = 999
puts scores
```

The answer is `[1, 2, 3, 4, 5]`. That's because `slice` is a completely new array with copies of values. Now, consider the Go equivalent:

答案是`[1, 2, 3, 4, 5]`。那是因为`slice`是一个复制了所有值的全新数组。现在，来看下Go相同的代码：

```go
scores := []int{1,2,3,4,5}
slice := scores[2:4]
slice[0] = 999
fmt.Println(scores)
```

The output is `[1, 2, 999, 4, 5]`.

输出是`[1, 2, 999, 4, 5]`。

This changes how you code. For example, a number of functions take a position parameter. In JavaScript, if we want to find the first space in a string (yes, slices work on strings too!) after the first five characters, we'd write:

这改变了你的编码方式。比如，一些函数需要一个位置参数。在JavaScript中，如果我们需要找到字符串中第五个字符之后的第一个空格（是的，切片对字符串也是有效的！）， 我们这样写：

```go
haystack = "the spice must flow";
console.log(haystack.indexOf(" ", 5));
```

In Go, we leverage slices:

在Go中，我们使用切片：

```go
strings.Index(haystack[5:], " ")
```

We can see from the above example, that `[X:]` is shorthand for *from X to the end* while `[:X]` is shorthand for *from the start to X*. Unlike other languages, Go doesn't support negative values. If we want all of the values of a slice except the last, we do:

上面的例子，我们可以看到，`[X:]`是*从X到结束*的简写，就如`[:X]`是*从开始到X*的简写一样。和其他语言不同的是，Go不支持反向取值（这边感觉不对）。如果我们需要切片除了最后一个值以外的所有值，我们这样来写：

```go
scores := []int{1, 2, 3, 4, 5}
scores = scores[:len(scores)-1]
```

The above is the start of an efficient way to remove a value from an unsorted slice:

以上是一种快速删除未排序的切片中的某个值的方法的开头：

```go
func main() {
  scores := []int{1, 2, 3, 4, 5}
  scores = removeAtIndex(scores, 2)
  fmt.Println(scores)
}

func removeAtIndex(source []int, index int) []int {
  lastIndex := len(source) - 1
  //swap the last value and the value we want to remove
  source[index], source[lastIndex] = source[lastIndex], source[index]
  return source[:lastIndex]
}
```

Finally, now that we know about slices, we can look at another commonly used built-in function: `copy`. `copy` is one of those functions that highlights how slices change the way we code. Normally, a method that copies values from one array to another has 5 parameters: `source`, `sourceStart`, `count`, `destination` and `destinationStart`. With slices, we only need two:

最后，既然我们是学习了切片，我们来看另一个常用的内置函数： `copy`。 `copy`是能突出切片是如何改变我们的编码方式的函数之一。通常，数组间拷贝需要5个参数：`source`, `sourceStart`, `count`, `destination` 和 `destinationStart`。使用切片只要两个：

```go
import (
  "fmt"
  "math/rand"
  "sort"
)

func main() {
  scores := make([]int, 100)
  for i := 0; i < 100; i++ {
    scores[i] = int(rand.Int31n(1000))
  }
  sort.Ints(scores)

  worst := make([]int, 5)
  copy(worst, scores[:5])
  fmt.Println(worst)
}
```

Take some time and play with the above code. Try variations. See what happens if you change copy to something like `copy(worst[2:4], scores[:5])`, or what if you try to copy more or less than `5` values into `worst`?

花一些时间来执行上面的代码。多试几次。看看会发生什么，如果将代码改为`copy(worst[2:4], scores[:5])`，或者要拷贝比`5`更多或少的值到`worst`？

## Maps
## 映射

Maps in Go are what other languages call hashtables or dictionaries. They work as you expect: you define a key and value, and can get, set and delete values from it.

Go中的映射在其他语言中叫哈希表或者字典。它们都如你想的一样：你定义一个键和值，然后你可以通过映射来删改查这些值。

Maps, like slices, are created with the `make` function. Let's look at an example:

映射，和切片一样，是通过`make`函数来创建的。让我们来看一个例子：

```go
func main() {
  lookup := make(map[string]int)
  lookup["goku"] = 9001
  power, exists := lookup["vegeta"]

  // prints 0, false
  // 0 is the default value for an integer
  fmt.Println(power, exists)
}
```

To get the number of keys, we use `len`. To remove a value based on its key, we use `delete`:

我们使用`len`来获取有多少键。要通过键来删除一个值，我们用`delete`：

```go
// returns 1
total := len(lookup)

// has no return, can be called on a non-existing key
delete(lookup, "goku")
```

Maps grow dynamically. However, we can supply a second argument to `make` to set an initial size:

映射是动态增长的。但是，我们可以通过`make`的第二个参数来设置它的初始大小：

```go
lookup := make(map[string]int, 100)
```

If you have some idea of how many keys your map will have, defining an initial size can help with performance.

如果知道有多少值，指定初始大小可以有更好的性能表现。

When you need a map as a field of a structure, you define it as:

如果想要把映射做为结构体的一个字段，我们这样定义：

```go
type Saiyan struct {
  Name string
  Friends map[string]*Saiyan
}
```

One way to initialize the above is via:

初始化的一种方式：

```go
goku := &Saiyan{
  Name: "Goku",
  Friends: make(map[string]*Saiyan),
}
goku.Friends["krillin"] = ... //todo load or create Krillin
```

There's yet another way to declare and initialize values in Go. Like `make`, this approach is specific to maps and arrays. We can declare as a composite literal:

在Go中有另一种申明和初始化的方式。和`make`一样，这对映射和数组都是有效的。我们可以像组合文字一样申明：

```go
lookup := map[string]int{
  "goku": 9001,
  "gohan": 2044,
}
```

We can iterate over a map using a `for` loop combined with the `range` keyword:

我们可以通过`for`和`range`关键字来迭代映射：

```go
for key, value := range lookup {
  ...
}
```

Iteration over maps isn't ordered. Each iteration over a lookup will return the key value pair in a random order.

映射的迭代器是无序的。每个迭代器随机查找键值对。

## Pointers versus Values
## 指针和值

We finished Chapter 2 by looking at whether you should assign and pass pointers or values. We'll now have this same conversation with respect to array and map values. Which of these should you use?

通过了解什么时候传值或指针我们结束了第二章。在数组和映射的值方面我们将碰到相同的问题。我们应该用哪一种？

```go
a := make([]Saiyan, 10)
//or
b := make([]*Saiyan, 10)
```

Many developers think that passing `b` to, or returning it from, a function is going to be more efficient. However, what's being passed/returned is a copy of the slice, which itself is a reference. So with respect to passing/returning the slice itself, there's no difference.

许多开发人员会想对于一个函数来说是传`b`还是将它做为返回值更高效。然而，这里传递或者返回的都是一个切片的拷贝，它本身就是一个引用。所以就传递或者返回这个切片而言，没有什么区别。

Where you will see a difference is when you modify the values of a slice or map. At this point, the same logic that we saw in Chapter 2 applies. So the decision on whether to define an array of pointers versus an array of values comes down to how you use the individual values, not how you use the array or map itself.

当你改变一个切片或者映射的值时，你会看见不同。在这点上，同样的逻辑，我们在第二章看到已经适用。所以是否定义一个数组指针还是一个数组值主要归结于如何使用单个值，而不是你如何使用数组或者映射本身。

## Before You Continue
## 继续之前

Arrays and maps in Go work much like they do in other languages. If you're used to dynamic arrays, there might be a small adjustment, but `append` should solve most of your discomfort. If we peek beyond the superficial syntax of arrays, we find slices. Slices are powerful and they have a surprisingly large impact on the clarity of your code.

在Go中数组和映射的工作方式与其他语言非常像。如果你用过动态数组，可能有会有一些需要调整，但是通过`append`可以解决所有的不适。如果我们抛开数组表面的语法，我们就会发现切片。切片是相当强大的，使用切片对你代码的整洁性有着非常巨大的影响。

There are edge cases that we haven't covered, but you're not likely to run into them. And, if you do, hopefully the foundation we've built here will let you understand what's going on.

这里有一些边界例子我们没有涉及到，但是你不太可能遇见这些例子。另外，如果你遇到了，希望我们已经打下的基础能让你理解这是怎么回事。