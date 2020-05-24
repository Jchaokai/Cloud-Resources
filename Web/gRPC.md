## gRPC相关

### RPC是什么

在分布式计算，远程过程调用（英语：Remote Procedure Call，缩写为 RPC）是一个计算机通信协议。该协议允许运行于一台计算机的程序调用另一个地址空间（通常为一个开放网络的一台计算机）的子程序，而程序员就像调用本地程序一样，无需额外地为这个交互作用编程（无需关注细节）。RPC是一种服务器-客户端（Client/Server）模式，经典实现是一个通过发送请求-接受回应进行信息交互的系统。
### gRPC是什么
开源的高性能RPC框架，使用HTTP/2作为传输协议
在gRPC中，客户端向调用本地方法一样 调用其他机器上应用程序的方法，让你更容易创建分布式程序和服务。

### 为什么使用gRPC
使用gRPC， 我们可以一次性的在一个.proto文件中定义服务并使用任何支持它的语言去实现客户端和服务端，反过来，它们可以应用在各种场景中，从Google的服务器到你自己的平板电脑—— gRPC帮你解决了不同语言及环境间通信的复杂性。使用protobuf还能获得其他好处，包括高效的序列号，简单的IDL以及容易进行接口更新。
总之一句话，使用gRPC能让我们更容易编写跨语言的分布式代码。

- - -
### 安装gRPC

**1. 安装gRPC**

```shell
go get -u google.golang.org/grpc
```
**2. 安装protobuf v3**
安装用于生成gRPC服务代码的协议编译器，最简单的方法是从下面的链接：[https://github.com/google/protobuf/releases](https://github.com/google/protobuf/releases)下载适合你平台的预编译好的二进制文件`（protoc-<version>-<platform>.zip）`。

下载完之后，执行下面的步骤：
解压下载好的文件
 1. 把`protoc.exe`二进制文件的路径加到环境变量中
 2. 执行下面的命令安装protoc的Go插件：
```shell
go get -u github.com/golang/protobuf/protoc-gen-go'
```
编译插件`protoc-gen-go`将会安装到`$GOBIN` (默认是`$GOPATH/bin`)，它必须在你的环境变量`$PATH`中&ensp;以便协议编译器`protoc`能够找到它。
- - -
### gRPC开发分三步
把大象放进冰箱分几步？
&emsp;把冰箱门打开。
&emsp;把大象放进去。
&emsp;把冰箱门带上。

gRPC开发同样分三步：
&emsp;编写.proto文件，生成指定语言源代码。
&emsp;编写服务端代码
&emsp;编写客户端代码

### gRPC入门示例
**1. 编写protobuf代码**
gRPC是基于`Protocol Buffers`。
`Protocol Buffers`是一种与语言无关，平台无关的可扩展机制，用于序列化结构化数据。使用`Protocol Buffers`可以一次定义结构化的数据，然后可以使用特殊生成的源代码轻松地在各种数据流中使用各种语言编写和读取结构化数据。
```
syntax = "proto3"; // 版本声明，使用Protocol Buffers v3版本

package pb; // 包名


// 定义一个打招呼服务
service Greeter {
    // SayHello 方法
    rpc SayHello (HelloRequest) returns (HelloReply) {}
}

// 包含人名的一个请求消息
message HelloRequest {
    string name = 1;
}

// 包含问候语的响应消息
message HelloReply {
    string message = 1;
}
```
执行下面的命令，生成go语言源代码：
```shell
protoc -I helloworld/ helloworld/pb/helloworld.proto --go_out=plugins=grpc:helloworld
```
在`gRPC_demo/helloworld/pb`目录下会生成`helloworld.pb.go`文件。

**2. 编写server端 golang代码**

```go
package main

import (
	"fmt"
	"net"

	pb "gRPC_demo/helloworld/pb"
	"golang.org/x/net/context"
	"google.golang.org/grpc"
	"google.golang.org/grpc/reflection"
)

type server struct{}

func (s *server) SayHello(ctx context.Context, in *pb.HelloRequest) (*pb.HelloReply, error) {
	return &pb.HelloReply{Message: "Hello " + in.Name}, nil
}

func main() {
	// 监听本地的8972端口
	lis, err := net.Listen("tcp", ":8972")
	if err != nil {
		fmt.Printf("failed to listen: %v", err)
		return
	}
	s := grpc.NewServer() // 创建gRPC服务器
	pb.RegisterGreeterServer(s, &server{}) // 在gRPC服务端注册服务

	reflection.Register(s) //在给定的gRPC服务器上注册服务器反射服务
	// Serve方法在lis上接受传入连接，为每个连接创建一个ServerTransport和server的goroutine。
	// 该goroutine读取gRPC请求，然后调用已注册的处理程序来响应它们。
	err = s.Serve(lis)
	if err != nil {
		fmt.Printf("failed to serve: %v", err)
		return
	}
}
```
将上面的代码保存到`gRPC_demo/helloworld/server/server.go`文件中，编译并执行：
```
cd helloworld/server
go build
./server
```
**3. 编写client端 golang代码**
```go
package main

import (
	"context"
	"fmt"

	pb "gRPC_demo/helloworld/pb"
	"google.golang.org/grpc"
)

func main() {
	// 连接服务器
	conn, err := grpc.Dial(":8972", grpc.WithInsecure())
	if err != nil {
		fmt.Printf("faild to connect: %v", err)
	}
	defer conn.Close()

	c := pb.NewGreeterClient(conn)
	// 调用服务端的SayHello
	r, err := c.SayHello(context.Background(), &pb.HelloRequest{Name: "q1mi"})
	if err != nil {
		fmt.Printf("could not greet: %v", err)
	}
	fmt.Printf("Greeting: %s !\n", r.Message)
}
```
将上面的代码保存到`gRPC_demo/helloworld/client/client.go`文件中，编译并执行：
```
cd helloworld/client/
go build
./client
```
得到输出如下（注意要先启动server端再启动client端）：
```
$ ./client 
Greeting: Hello dasdsa!
```


### grpc提供REST API

使用grpc-gateway插件

下载安装



```shell
protoc --grpc-gateway_out=logtostderr=true:../dest/dir  your.proto
```







<br/>

### 参考资料
- [gRPC快速入门](https://www.liwenzhou.com/posts/Go/gRPC/)














