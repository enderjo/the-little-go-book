# 入门
如果你想试试Go，你可以使用Go运行环境，它可以让你无需安装任何东西就可以在网上运行代码。这也是在Go论坛如StackOverflow中寻求帮助时，分享Go代码最常用的方式。

安装Go很简单。你可以从源码安装，但我建议你用一个已编译好的可执行文件。当你打开下载页面时，你可以看到不同平台的安装包。让我们避免使用这些并学习自己如何配置Go。然后你就会发现这不难。

除了简单的例子，Go被设计成只有你的代码在工作区时才能正常运行。这个工作区是有`bin`、`pkg`和`src`三个子目录的文件夹。你也许会想强制Go去适应你自己的风格 - 别想。

通常，我把我的项目放在`~/code`目录下。例如，`~/code/blog`是我的博客。对于Go来说，我的工作区是`~/code/go`，而我Go版本的博客会在~/code/go/src/blog下。因为这经常用到，我做了个符号链接，通过`~/code/blog`来访问它：

	ln -s ~/code/go/src/blog ~/code/blog

总之，无论你把你的项目放在哪，创建一个Go目录包含src子目录来放置你的项目。

## OSX / Linux

下载你平台对应`tar.gz`。OSX，你需要关心`go#.#.#.darwin-amd64-osx10.8.tar.gz`，其中`#.#.#`表示Go的最新版本。

通过`tar -C /usr/local -xzf go#.#.#.darwin-amd64-osx10.8.tar.gz`将文件解压到`/usr/local`目录。

设置两个环境变量：

1. `GOPATH`指向你的工作区，对我来说是`$HOME/code/go`。
2. 我们需要将Go可执行文件的路径添加到`PATH`。

你可以通过shell来完成设置：
	
    echo 'export GOPATH=$HOME/code/go' >> $HOME/.profile
    echo 'export PATH=$PATH:/usr/local/go/bin' >> $HOME/.profile

为了使这些变量生效，你需要关闭并重新打开你的Shell，或者运行`source $HOME/.profile`命令。

输入`go version`,你会看到类似这样的输出`go version go1.3.3`。

## Windows
下载最新的zip文件。如果你是x64系统，你需要下载`go#.#.#.windows-amd64.zip`，其中`#.#.#`表示Go的最新版本。

将其解压到你想要的位置。`c:\Go`是个不错和选择。

Set up two environment variables:
设置两个环境变理：
  1. `GOPATH` 指向你的工作区. 可以是这样的目录`c:\users\goku\work\go`。
  2. 添加 `c:\Go\bin` 至你的`PATH`环境变量中。

环境变量可以通过`系统`控制面板中的`环境变量`按钮中的`高级`标签页来设置。某些版本的Windows通过`System`控制面板里面的`高级系统Settings`选项提供该控制面板。

打开一个命令行窗口，输入`go version`，你会看到类似这样的输出`go version go1.3.3 windows/amd64`。
