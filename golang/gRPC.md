## gRPC相关

### RPC是什么
在分布式计算，远程过程调用（英语：Remote Procedure Call，缩写为 RPC）是一个计算机通信协议。该协议允许运行于一台计算机的程序调用另一个地址空间（通常为一个开放网络的一台计算机）的子程序，而程序员就像调用本地程序一样，无需额外地为这个交互作用编程（无需关注细节）。RPC是一种服务器-客户端（Client/Server）模式，经典实现是一个通过发送请求-接受回应进行信息交互的系统。
### gRPC是什么
开源的高性能RPC框架，使用HTTP/2作为传输协议
在gRPC中，客户端向调用本地方法一样 调用其他机器上应用程序的方法，让你更容易创建分布式程序和服务。

### 为什么使用gRPC
使用gRPC， 我们可以一次性的在一个.proto文件中定义服务并使用任何支持它的语言去实现客户端和服务端，反过来，它们可以应用在各种场景中，从Google的服务器到你自己的平板电脑—— gRPC帮你解决了不同语言及环境间通信的复杂性。使用protocol buffers还能获得其他好处，包括高效的序列号，简单的IDL以及容易进行接口更新。
总之一句话，使用gRPC能让我们更容易编写跨语言的分布式代码。

### 安装gRPC
1. 安装gRPC
```go
go get -u google.golang.org/grpc
```
2. 安装protobuf v3
安装用于生成gRPC服务代码的协议编译器，最简单的方法是从下面的链接：[https://github.com/google/protobuf/releases](https://github.com/google/protobuf/releases)下载适合你平台的预编译好的二进制文件`（protoc-<version>-<platform>.zip）`。

下载完之后，执行下面的步骤：

解压下载好的文件
把`protoc.exe`二进制文件的路径加到环境变量中
接下来执行下面的命令安装protoc的Go插件：
```shell
go get -u github.com/golang/protobuf/protoc-gen-go'
```
编译插件protoc-gen-go将会安装到`$GOBIN` (默认是`$GOPATH/bin`)，它必须在你的环境变量`$PATH`中 以便协议编译器protoc能够找到它。

### gRPC开发分三步
把大象放进冰箱分几步？

把冰箱门打开。
把大象放进去。
把冰箱门带上。
gRPC开发同样分三步：

编写.proto文件，生成指定语言源代码。
编写服务端代码
编写客户端代码
gRPC入门示例