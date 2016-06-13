# Chapter 2 - Structures
# 第二章 - 结构体

Go isn't an object-oriented (OO) language like C++, Java, Ruby and C#. It doesn't have objects nor inheritance and thus, doesn't have the many concepts associated with OO such as polymorphism and overloading.

和C++, Java, Ruby以及C#不一样，Go并不是面向对象的语言。它没有对象、继承和其他一些和面向对象相关的概念，比各多态和重载。

What Go does have are structures, which can be associated with methods. Go also supports a simple but effective form of composition. Overall, it results in simpler code, but there'll be occasions where you'll miss some of what OO has to offer. (It's worth pointing out that *composition over inheritance* is an old battle cry and Go is the first language I've used that takes a firm stand on the issue.)

Go有的就是结构体，可以直接绑定方法。Go支持简单便高效的组合。总的来说，它带来更简洁的代码，但在一些场合中失去OO的一些特性。（有必要指出 *组合优于继承* 是一个老早的争议，但Go是我用过的这么多语言中第一个立场这么坚定的。）

Although Go doesn't do OO like you may be used to, you'll notice a lot of similarities between the definition of a structure and that of a class. A simple example is the following `Saiyan` structure:

虽然Go确实不是你用过的OO样，但是你会发现结构体和类之间的很多相似之处。来看一个简单的例子，结构体`Saiyan`：

```go
type Saiyan struct {
  Name string
  Power int
}
```

We'll soon see how to add a method to this structure, much like you'd have methods as part of a class. Before we do that, we have to dive back into declarations.
很快我们就会看到怎么往这个结构体中添加方法，就像你要类中添加方法一样。在那之前，我们先细看下结构体的声明.

## Declarations and Initializations
## 声明和初始化

When we first looked at variables and declarations, we looked only at built-in types, like integers and strings. Now that we're talking about structures, we need to expand that conversation to include pointers.

我们最初学习变量和声明的时候，我们只用到内建类型，比如整形和字符串。现在我们讲的是结构体，我们要深入这个话题，包括指针。

The simplest way to create a value of our structure is:
创建一个结构体的值最简单的方式是：

```go
goku := Saiyan{
  Name: "Goku",
  Power: 9000,
}
```

*Note:* The trailing `,` in the above structure is required. Without it, the compiler will give an error. You'll appreciate the required consistency, especially if you've used a language or format that enforces the opposite.

*注意：*结构体中最后一个`,`是必需的。没有的话，编译器会报错。你会喜欢这种一致性要求，特别是如果你使用了强制性相反的语言或格式

We don't have to set all or even any of the fields. Both of these are valid:
我们可以不给所有或者任何一个字段赋值。下面两种方式都是正确的：

```go
goku := Saiyan{}

// or

goku := Saiyan{Name: "Goku"}
goku.Power = 9000
```

Just like unassigned variables have a zero value, so do fields.

和没有赋值的变量一样，没有赋值的字段默认为0值。

Furthermore, you can skip the field name and rely on the order of the field declarations (though for the sake of clarity, you should only do this for structures with few fields):

再者，你也可以省略字段的名字，按字段的顺序进行声明（尽管为了简洁起见，你尽量在结构体只有少量字段时才使用这种方式）：

```go
goku := Saiyan{"Goku", 9000}
```

What all of the above examples do is declare a variable `goku` and assign a value to it.

上面所有的例子所做的事件就是声明一个变量`goku`并给它赋一个值。

Many times though, we don't want a variable that is directly associated with our value but rather a variable that has a pointer to our value. A pointer is a memory address; it's the location of where to find the actual value. It's a level of indirection. Loosely, it's the difference between being at a house and having directions to the house.

很多时候，我们不想变量直接相关的值，而是一个指向指针的变量。指针是内存地址，它可以定位实际的值有哪里。这是一种间接层。简单点说，这好比是在房子里还是有房子地址的区别。


Why do we want a pointer to the value, rather than the actual value? It comes down to the way Go passes arguments to a function: as copies. Knowing this, what does the following print?

为什么我们确实需要指针，而不是实际的值？这是因为Go在函数中参数的传递方式是：值。了解了这个，下面的程序会打出来什么？

```go
func main() {
  goku := Saiyan{"Goku", 9000}
  Super(goku)
  fmt.Println(goku.Power)
}

func Super(s Saiyan) {
  s.Power += 10000
}
```

The answer is 9000, not 19000. Why? Because `Super` made changes to a copy of our original `goku` value and thus, changes made in `Super` weren't reflected in the caller. To make this work as you probably expect, we need to pass a pointer to our value:

答案是9000，而示是19000。为什么呢？因为`Super`改变的是`goku`的一个拷贝的值，`Super`中的改变不会在调用者中显示出来。要如你期望的方式运行，我们需要传入一个指针：

```go
func main() {
  goku := &Saiyan{"Goku", 9000}
  Super(goku)
  fmt.Println(goku.Power)
}

func Super(s *Saiyan) {
  s.Power += 10000
}
```

We made two changes. The first is the use of the `&` operator to get the address of our value (it's called the *address of* operator). Next, we changed the type of parameter `Super` expects. It used to expect a value of type `Saiyan` but now expects an address of type `*Saiyan`, where `*X` means *pointer to value of type X*. There's obviously some relation between the types `Saiyan` and `*Saiyan`, but they are two distinct types.

我们做了两处修改。第一处是使用了`&`操作符来获取值的地址（它被称为 *取址*操作符）。接下来，我们修改`Super`期望的参数类型。原来它期望的是`Saiyan`值类型，而现在期望的是`*Saiyan`的地址类型，此处`*X`是指 *指向X类型的指针*。`Saiyan`和 `*Saiyan`的类型有一些明显的关联，但是它们两是不同的类型。

Note that we're still passing a copy of `goku's` value to `Super` it just so happens that `goku's` value has become an address. That copy is the same address as the original, which is what that indirection buys us. Think of it as copying the directions to a restaurant. What you have is a copy, but it still points to the same restaurant as the original.

需要指出的是，我们现在传递给`Super`参数的仍然是`goku`的值拷贝。只是现在`goku`的值变成了一个地址。这个地址拷贝和源地址相同。可以认为它类似一个指向餐厅方向的拷贝，这就间接服务于我们。虽然是一个拷贝，但是和源地址一样，也指向同一个餐厅。

We can prove that it's a copy by trying to change where it points to (not something you'd likely want to actually do):

我们可以通过改变它的指向来证明这是个拷贝（虽然不是你想要的）：

```go
func main() {
  goku := &Saiyan{"Goku", 9000}
  Super(goku)
  fmt.Println(goku.Power)
}

func Super(s *Saiyan) {
  s = &Saiyan{"Gohan", 1000}
}
```

The above, once again, prints 9000. This is how many languages behave, including Ruby, Python, Java and C#. Go, and to some degree C#, simply make the fact visible.

上例，依然，打印9000。这和很多语言的行为是一样的，包括Ruby，Python，Java和C#。Go和C#一定程度是要样的，让这个更显而易见。

It should also be obvious that copying a pointer is going to be cheaper than copying a complex structure. On a 64-bit machine, a pointer is 64 bits large. If we have a structure with many fields, creating copies can be expensive. The real value of pointers though is that they let you share values. Do we want `Super` to alter a copy of `goku` or alter the shared `goku` value itself?

很明显拷贝一个指针比拷贝一个复杂的结构体开销小多了。在64位的机器上，一个指针是64位的大小。如果我们有一个有很多字段的结构体，创建一份拷贝开销是比较大的。指针的真正价值是通过它可以共享值。我们想通过`Super`去改变`goku`的拷贝或者改变共享的`goku`值本身？

All this isn't to say that you'll always want a pointer. At the end of this chapter, after we've seen a bit more of what we can do with structures, we'll re-examine the pointer-versus-value question.

所有这些不是说你一直要用指针。本章末尾，当我们学到更多结构体的内容后，我们会重新审视指针和值类型的问题。

## Functions on Structures
## 结构体上的函数（结构体的方法）

We can associate a method with a structure:
我们可以为结构体关联一个方法：

```go
type Saiyan struct {
  Name string
  Power int
}

func (s *Saiyan) Super() {
  s.Power += 10000
}
```

In the above code, we say that the type `*Saiyan` is the **receiver** of the `Super` method. We call `Super` like so:

上面的代码，我们说`*Saiyan`是`Super`方法的 **接收器** 。我们能过这样的方式调用`Super`方法：

```go
goku := &Saiyan{"Goku", 9001}
goku.Super()
fmt.Println(goku.Power) // will print 19001
```

## Constructors
## 构造器

Structures don't have constructors. Instead, you create a function that returns an instance of the desired type (like a factory):

结构体没有构造器。你可创建一个函数来返回一个期望类型的实例来替代（像工厂一样）：

```go
func NewSaiyan(name string, power int) *Saiyan {
  return &Saiyan{
    Name: name,
    Power: power,
  }
}
```

This pattern rubs a lot of developers the wrong way. On the one hand, it's a pretty slight syntactical change; on the other, it does feel a little less compartmentalized.

这种方式导致很多开发者犯错。一方面，它有一些轻微的语法变化；另一方面，它有一点让人感觉不好区分。 

Our factory doesn't have to return a pointer; this is absolutely valid:

我们的工厂没有必要返回指针；下面的代码完全正确：

```go
func NewSaiyan(name string, power int) Saiyan {
  return Saiyan{
    Name: name,
    Power: power,
  }
}
```

## New
## 创建

Despite the lack of constructors, Go does have a built-in `new` function which is used to allocate the memory required by a type. The result of `new(X)` is the same as `&X{}`:

尽管没有构造器，但是Go有内置的`new`函数可以用来分配一下指定类弄的内存。`new(X)`和`&X{}`的效果是一样的：

```go
goku := new(Saiyan)
// same as
goku := &Saiyan{}
```

Which you use is up to you, but you'll find that most people prefer the latter whenever they have fields to initialize, since it tends to be easier to read:

使有哪种方式看你自己的喜好，但是你会发现当字段需要初始化时，大多数人喜欢使用后一种方式，因为这样更易读：

```go
goku := new(Saiyan)
goku.name = "goku"
goku.power = 9001

//vs

goku := &Saiyan {
  name: "goku",
  power: 9000,
}
```

Whichever approach you choose, if you follow the factory pattern above, you can shield the rest of your code from knowing and worrying about any of the allocation details.

无论你使用哪种方式，如果你使用上面的工厂模式，接下来的代码中你可以不要了解和担心任何分配的细节。

## Fields of a Structure
## 结构体字段

In the example that we've seen so far, `Saiyan` has two fields `Name` and `Power` of types `string` and `int`, respectively. Fields can be of any type -- including other structures and types that we haven't explored yet such as arrays, maps, interfaces and functions.

目前为止我们看到的例子中，`Saiyan`有两个字段，一个`字符串`类型的`Name`和一个`整型`的`Power`。字段可以是任何类型————包括其他的结构体和暂时我们没有讲到的类型，例如数组、字典、接口和函数。

For example, we could expand our definition of `Saiyan`:

例如，我们可以这样扩展`Saiyan`的定义：

```go
type Saiyan struct {
  Name string
  Power int
  Father *Saiyan
}
```

which we'd initialize via:

我们可以通过下面的方式初始化：

```go
gohan := &Saiyan{
  Name: "Gohan",
  Power: 1000,
  Father: &Saiyan {
    Name: "Goku",
    Power: 9001,
    Father: nil,
  },
}
```

## Composition
## 组合

Go supports composition, which is the act of including one structure into another. In some languages, this is called a trait or a mixin. Languages that don't have an explicit composition mechanism can always do it the long way. In Java:

Go支持组合，就是将一个结构体包含在另一个之中。在一些语言中，这被叫特性或混入。没有明确的组合机制的语言，要实现这个特性就比较繁杂。在Java中：

```java
public class Person {
  private String name;

  public String getName() {
    return this.name;
  }
}

public class Saiyan {
  // Saiyan is said to have a person
  private Person person;

  // we forward the call to person
  public String getName() {
    return this.person.getName();
  }
  ...
}
```

This can get pretty tedious. Every method of `Person` needs to be duplicated in `Saiyan`. Go avoids this tediousness:

这样会相当的冗长。每个`Person`的方法都需要在`Saiyan`中复制一遍。Go可以避免这种冗长：

```go
type Person struct {
  Name string
}

func (p *Person) Introduce() {
  fmt.Printf("Hi, I'm %s\n", p.Name)
}

type Saiyan struct {
  *Person
  Power int
}

// and to use it:
goku := &Saiyan{
  Person: &Person{"Goku"},
  Power: 9001,
}
goku.Introduce()
```

The `Saiyan` structure has a field of type `*Person`. Because we didn't give it an explicit field name, 
we can implicitly access the fields and functions of the composed type. 
However, the Go compiler *did* give it a field name, consider the perfectly valid:

`Saiyan`结构体中有一个`*Person`类型的字段。因为我们没有给他一个显示的字段名，我们可以隐示的访问组合类型的所有字段和函数。
但出于完全有效的考虑，Go编辑器*确实*有给它分配一个字段名。

```go
goku := &Saiyan{
  Person: &Person{"Goku"},
}
fmt.Println(goku.Name)
fmt.Println(goku.Person.Name)
```

Both of the above will print "Goku".

上面的两个输出都是"Goku"。

Is composition better than inheritance? Many people think that it's a more robust way to share code.
When using inheritance, your class is tightly coupled to your superclass and you end up focusing on hierarchy rather than behavior.
组合是不优于继承？很多人认为这是一种更健壮的共享代码的方式。当使用继承时，你的类和超类捆绑在一起，你最终关注继承而不是行为。

### Overloading
### 重载

While overloading isn't specific to structures, it's worth addressing. Simply, Go doesn't support overloading. 
For this reason, you'll see (and write) a lot of functions that look like `Load`, `LoadById`, `LoadByName` and so on.

值得指出的是，结构体没有重载。简而言之，Go不支持重载。因为这个原因，你会看到（和写）很多像 `Load`, `LoadById`, `LoadByName`这样的函数。

However, because implicit composition is really just a compiler trick, we can "overwrite" the functions of a composed type. 
For example, our `Saiyan` structure can have its own `Introduce` function:
但是，因为非显示的组合是一个编辑器技巧，我们可以“重写”组合类型的函数。比如， 我们的`Saiyan`结构体可以有自己的`Introduce`方法：

```go
func (s *Saiyan) Introduce() {
  fmt.Printf("Hi, I'm %s. Ya!\n", s.Name)
}
```

The composed version is always available via `s.Person.Introduce()`.

组合版本中使用`s.Person.Introduce()`也是一样的。

## Pointers versus Values
## 指针和值

As you write Go code, it's natural to ask yourself *should this be a value, or a pointer to a value?* There are two pieces of good news. 
First, the answer is the same regardless of which of the following we're talking about:

当你写Go代码的时候，你很自然的就会问你自己*这应该是要用值还是要用指针?*下面是两个好消息。首先，下面讨论的这些话题是没有什么差别的：

* A local variable assignment
* Field in a structure
* Return value from a function
* Parameters to a function
* The receiver of a method

* 局部变量赋值
* 结构体中的字段
* 函数的返回值
* 函数的参数
* 方法的接收者

Secondly, if you aren't sure, use a pointer.
其次，如果你不确定，就用指针好了。

As we already saw, passing values is a great way to make data immutable 
(changes that a function makes to it won't be reflected in the calling code). 
Sometimes, this is the behavior that you'll want but more often, it won't be.

就如我们看到的那样，传值是一个让值不可变的好方法（函数内的改变不会影响调用代码中的值）。有些时候，我们却时希望如此，可常常不是这样的。
Even if you don't intend to change the data, consider the cost of creating a copy of large structures. 
Conversely, you might have small structures, say:

就算你不想改变值，想一下创建一个大结构体拷贝的开销。相反地，你可能有一个小结构体，例如：

```go
type Point struct {
  X int
  Y int
}
```

In such cases, the cost of copying the structure is probably offset by being able to access `X` and `Y` directly, without any indirection.

在这种情况下，拷贝结构体的开销可以通过偏移量来直接访问`X`和`Y`，而不是间接访问。
Again, these are all pretty subtle cases. Unless you're iterating over thousands or possibly tens of thousands of such points, you wouldn't notice a difference.

再次指出，这些只是非常微妙的情况。除非你要访问成千上百个这样的点，否则你不会察觉有任何的不同。

## Before You Continue
## 继续之前

From a practical point of view, this chapter introduced structures, how to make an instance of a structure a receiver of a function, and added pointers to our existing knowledge of Go's type system. The following chapters will build on what we know about structures as well as the inner workings that we've explored.

本章从实践的角度来看，介绍了结构体，以及如何创建方法接收器的结构体实例，并在我们现有的Go知识体系中引入了指针。
下面的章节将基于我们所知道的结构体知识来探讨其内部运行机制。