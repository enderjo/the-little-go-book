# Introduction
# 介绍
I’ve always had a love-hate relationship when it comes to learning new languages. On the one hand, languages are so fundamental to what we do, that even small changes can have measurable impact. That aha moment when something clicks can have a lasting effect on how you program and can redefine your expectations of other languages. On the downside, language design is fairly incremental. Learning new keywords, type system, coding style as well as new libraries, communities and paradigms is a lot of work that seems hard to justify. Compared to everything else we have to learn, new languages often feel like a poor investment of our time.

每当我学习一门新语言的时候总是爱恨交加。一方面，语言是如此的重要，以至于一点小的变化对我们产生的可估量的影响。在你的程序和可以重新定义你对其他语言的期望的时候，你会有一个持久的效果。同时，语言的设计是增量的。学习新的关键字、类型体系、编码方式以及新类库、通讯和范式需要很多工作，但又很难评估。相对学习其他必学的东西，学习新语言让我们常常感觉是对时间的投入很大。

That said, we have to move forward. We have to be willing to take incremental steps because, again, languages are the foundation of what we do. Though the changes are often incremental, they tend to have a wide scope and they impact productivity, readability, performance, testability, dependency management, error handling, documentation, profiling, communities, standard libraries, and so on. Is there a positive way to say death by a thousand cuts?

也就是说，我们要进步。我们必须愿意采用渐进的方式，又一次因为，语言是我们的基础。虽然变化是增量的，但它们往往范围很广，它们影响效率，可读性、性能、可测试性、依赖管理、错误处理、文档、分析（监控？）、通讯、标准库等等。除了说千刀万剐我们还能说什么？

That leaves us with an important question: why Go? For me, there are two compelling reasons. The first is that it’s a relatively simple language with a relatively simple standard library. In a lot of ways, the incremental nature of Go is to simplify some of the complexity we’ve seen being added to languages over the last couple of decades. The other reason is that for many developers, it will complement your existing arsenal.

这留给我们一个重要的问题：为什么选Go？对我来说，有两个令人信服的理由。首先它是一门相对简单的语言，还自带相对简单的标准库。在很多方面，Go的增量本质简化了我们已经看到的在过去几十年引入的语言的复杂性。另一个原因对于很多开发者来说，它会完善你的军火库。

Go was built as a system language (e.g., operating systems, device drivers) and thus aimed at C and C++ developers. According to the Go team, and which is certainly true of me, application developers, not system developers, have become the primary Go users. Why? I can’t speak authoritatively for system developers, but for those of us building websites, services, desktop applications and the like, it partially comes down to the emerging need for a class of systems that sit somewhere in between low-level system applications and higher-level applications.

Go被构建为一个系统语言（比如操作系统、设备驱动）并且面向C和C++开发人员。纵观Go的社群，我非常确信，应用开发者，而非系统开发者已经成为Go的主要使用者。为什么？我不能代表系统开发者，但是我们建设的网站、服务、桌面应用等等，这些面向新兴需求可归结为一类介于低层次的系统应用程序和更高级别的应用程序之间的系统。

Maybe it’s a messaging, caching, computational-heavy data analysis, command line interface, logging or monitoring. I don’t know what label to give it, but over the course of my career, as systems continue to grow in complexity and as concurrency frequently measures in the tens of thousands, there’s clearly been a growing need for custom infrastructure-type systems. You can build such systems with Ruby or Python or something else (and many people do), but these types of systems can benefit from a more rigid type system and greater performance. Similarly, you can use Go to build websites (and many people do), but I still prefer, by a wide margin, the expressiveness of Node or Ruby for such systems.

也许它是一个消息，缓存，大数据分析，命令行接口，日志或监控。我不知如何标记它，但是在我的职业身涯中，由于系统的复杂性不断和频繁并发数以万计的增长，定制的基础设施类系统成为一个不断增长的需求。你可以用Ruby或Python或别的东西（确实很多人这么做）来构建这样的系统，如果使用Go，这些系统可以有一个更严格的类型系统和更高的性能优势。同样，您可以使用Go建立网站（确实很多人这样做），但，为了更大的回旋余地，我还是喜欢使用Node或Ruby的来构建这类系统。

There are other areas where Go excels. For example, there are no dependencies when running a compiled Go program. You don’t have to worry if your users have Ruby or the JVM installed, and if so, what version. For this reason, Go is becoming increasingly popular as a language for command-line interface programs and other types of utility programs you need to distribute (e.g., a log collector).

还有一些Go的长处。比如，运行Go程序时没有依赖。你不需要担心你的用户是否已经安装了Ruby或者JVM和它们的版本。因为这个原因，Go作为命令行程序或者需要分发的其他类型的实用程序（比如日志收集器）开发语言，越来越流行了。

Put plainly, learning Go is an efficient use of your time. You won’t have to spend long hours learning or even mastering Go, and you’ll end up with something practical from your effort.

简单地说，学习Go是一种有效利用你的时间。你将不必花费很长时间来学习甚至掌握go，你可以通过一些实践来达成。

# A Note from the Author
# 关于作者

I’ve hesitated writing this book for a couple reasons. The first is that Go’s own documentation, in particular Effective Go, is solid.

我曾犹豫写这本小册子有几个原因。首先Go有自已的文档，特别是高效Go，它很实在（实用？）。

The other is my discomfort at writing a book about a language. When I wrote The Little MongoDB Book, it was safe to assume most readers understood the basics of relational database and modeling. With The Little Redis Book, you could assume a familiarity with a key value store and take it from there.

另一个原因是我写一本关于语言的书时的不适。当我写MongoDB小册子的时候，你可以假设很多读者理解基本的关系型数据库和模型。写Redis小册子的时候，你可以假设从一个熟悉的键值存储开始。

As I think about the paragraphs and chapters that lay ahead, I know that I won’t be able to make those same assumptions. How much time do you spend talking about interfaces knowing that for some, the concept will be new, while others won’t need much more than Go has interfaces? Ultimately, I take comfort in knowing that you’ll let me know if some parts are too shallow or others too detailed. Consider that the price of this book.

当想到摆在面前的段落和章节的时候，我知道我不能做这样的假设。你要花多少时间来讲解接口，因为对于一些人来说这是一个新概念，而另个一些人已经不需用再了解了。最终，我会感到欣慰如果你让我知道有些部分是太浅或过于详细。考虑一下这本书的价格。
