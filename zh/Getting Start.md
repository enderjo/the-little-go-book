# Getting Started
# 入门

If you’re looking to play a little with Go, you should check out the Go Playground which lets you run code online without having to install anything. This is also the most common way to share Go code when seeking help in Go’s discussion forum and places like StackOverflow.

如果你想试试Go，你可以使用Go运行环境，它可以让你无需安装任何东西就可以在网上运行代码。这也是在Go论坛如StackOverflow中寻求帮助时，分享Go代码最常用的方式。

Installing Go is straightforward. You can install it from source, but I suggest you use one of the pre-compiled binaries. When you go to the download page, you’ll see installers for various platforms. Let’s avoid these and learn how to set up Go ourselves. As you’ll see, it isn’t hard.

安装Go很简单。你可以从源码安装，但我建议你用一个已经编译好的版本。当你打开下载页面进，你可以看到不同平台的安装包。让我们避免使用这些并学习自己如何配置Go。然后你就会发现这不难。

Except for simple examples, Go is designed to work when your code is inside a workspace. The workspace is a folder composed of bin, pkg and src subfolders. You might be tempted to force Go to follow your own style - don’t.

除了简单的例子，Go被设计成只有你的代码在工作区时才能正常运行。这个工作区是有bin、pkg和src三个子目录的文件夹。你也许会想强制Go去适应你自己的风格 - 别想。

Normally, I put my projects inside of ~/code. For example, ~/code/blog contains my blog. For Go, my workspace is ~/code/go and my Go-powered blog would be in ~/code/go/src/blog. Since that’s a lot to type, I use a symbolic link to make it accessible via ~/code/blog:

通常，我把我的项目放在~/code目录下。例如，~/code/blog是我的博客。对于Go来说，我的工作区是~/code/go，而我Go版本的博客会在~/code/go/src/blog下。因为这经常用到，我做了个符号链接，通过~/code/blog来访问它：

ln -s ~/code/go/src/blog ~/code/blog

`ln -s ~/code/go/src/blog ~/code/blog`

In short, create a go folder with a src subfolder wherever you expect to put your projects.

总之，无论你把你的项目放在哪，创建一个Go目录包含src子目录来放置你的项目。

# OSX / Linux
# OSX / Linux

Download the tar.gz for your platform. For OSX, you’ll most likely be interested in go#.#.#.darwin-amd64-osx10.8.tar.gz, where #.#.# is the latest version of Go.

下载你平台对应tar.gz。OSX，你需要关心go#.#.#.darwin-amd64-osx10.8.tar.gz，其中#.#.#表示Go的最新版本。

Extract the file to /usr/local via tar -C /usr/local -xzf go#.#.#.darwin-amd64-osx10.8.tar.gz.

通过`tar -C /usr/local -xzf go#.#.#.darwin-amd64-osx10.8.tar.gz`将文件
Set up two environment variables:
1.	GOPATH points to your workspace, for me, that’s $HOME/code/go.
2.	We need to append Go’s binary to our PATH.
You can set these up from a shell:
echo 'export GOPATH=$HOME/code/go' >> $HOME/.profile echo 'export PATH=$PATH:/usr/local/go/bin' >> $HOME/.profile
You’ll want to activate these variables. You can close and reopen your shell, or you can run source $HOME/.profile.
Type go version and you’ll hopefully get an output that looks like go version go1.3.3 darwin/amd64.
Windows
Download the latest zip file. If you’re on an x64 system, you’ll want go#.#.#.windows-amd64.zip, where #.#.# is the latest version of Go.
Unzip it at a location of your choosing. c:\Go is a good choice.
Set up two environment variables:
1.	GOPATH points to your workspace. That might be something like c:\users\goku\work\go.
2.	Add c:\Go\bin to your PATH environment variable.
Environment variables can be set through the Environment Variables button on the Advanced tab of the System control panel. Some versions of Windows provide this control panel through the Advanced System Settings option inside the System control panel.
Open a command prompt and type go version. You’ll hopefully get an output that looks like go version go1.3.3 windows/amd64. 
