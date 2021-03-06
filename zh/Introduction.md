# 引言
每当我学习一门新语言的时候总是爱恨交加。一方面，语言是如此的重要，以至于一点小的变化对我们产生的可估量的影响。在你的程序和可以重新定义你对其他语言的期望的时候，你会有一个持久的效果。同时，语言的设计是增量的。学习新的关键字、类型体系、编码方式以及新类库、通讯和范式需要很多工作，但又很难评估。相对学习其他必学的东西，学习新语言让我们常常感觉是对时间的投入很大。

也就是说，我们要进步。我们必须愿意采用渐进的方式，又一次因为，语言是我们的基础。虽然变化是增量的，但它们往往范围很广，它们影响效率，可读性、性能、可测试性、依赖管理、错误处理、文档、分析（监控？）、通讯、标准库等等。除了说千刀万剐我们还能说什么？

这留给我们一个重要的问题：为什么选Go？对我来说，有两个令人信服的理由。首先它是一门相对简单的语言，还自带相对简单的标准库。在很多方面，Go的增量本质简化了我们已经看到的在过去几十年引入的语言的复杂性。另一个原因对于很多开发者来说，它会完善你的军火库。

Go被构建为一个系统语言（比如操作系统、设备驱动）并且面向C和C++开发人员。纵观Go的社群，我非常确信，应用开发者，而非系统开发者已经成为Go的主要使用者。为什么？我不能代表系统开发者，但是我们建设的网站、服务、桌面应用等等，这些面向新兴需求可归结为一类介于低层次的系统应用程序和更高级别的应用程序之间的系统。

也许它是一个消息，缓存，大数据分析，命令行接口，日志或监控。我不知如何标记它，但是在我的职业身涯中，由于系统的复杂性不断和频繁并发数以万计的增长，定制的基础设施类系统成为一个不断增长的需求。你可以用Ruby或Python或别的东西（确实很多人这么做）来构建这样的系统，如果使用Go，这些系统可以有一个更严格的类型系统和更高的性能优势。同样，您可以使用Go建立网站（确实很多人这样做），但，为了更大的回旋余地，我还是喜欢使用Node或Ruby的来构建这类系统。

还有一些Go的长处。比如，运行Go程序时没有依赖。你不需要担心你的用户是否已经安装了Ruby或者JVM和它们的版本。因为这个原因，Go作为命令行程序或者需要分发的其他类型的实用程序（比如日志收集器）开发语言，越来越流行了。

简单地说，学习Go是一种有效利用你的时间。你将不必花费很长时间来学习甚至掌握go，你可以通过一些实践来达成。

## 关于作者
我曾犹豫写这本小册子有几个原因。首先Go有自已的文档，特别是高效Go，它很实在（实用？）。

另一个原因是我写一本关于语言的书时的不适。当我写MongoDB小册子的时候，你可以假设很多读者理解基本的关系型数据库和模型。写Redis小册子的时候，你可以假设从一个熟悉的键值存储开始。

当想到摆在面前的段落和章节的时候，我知道我不能做这样的假设。你要花多少时间来讲解接口，因为对于一些人来说这是一个新概念，而另个一些人已经不需用再了解了。最终，我会感到欣慰如果你让我知道有些部分是太浅或过于详细。考虑一下这本书的价格。
