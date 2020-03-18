## Go-Micro 使用相关

### [新建服务模板](https://micro.mu/docs/cn/new.html)

#### micro new  [ . . . ]

```shell
$ micro new -h

NAME:
   micro new - Create a service template

USAGE:
   micro new [command options] [arguments...]

OPTIONS:
   --namespace "go.micro"                       Namespace for the service e.g com.example
   --type "srv"                                 Type of service e.g api, fnc, srv, web
   --fqdn                                       FQDN of service e.g com.example.srv.service (defaults to namespace.type.alias)
   --alias                                      Alias is the short name used as part of combined name if specified
   --plugin [--plugin option --plugin option]   Specify plugins e.g --plugin=registry=etcd:broker=nats or use flag multiple times
   --gopath                                     Create the service in the gopath. Defaults to true.
```



当我们创建新服务时，有两点我们要确认

| 配置指令   | 作用         | 默认值   | 说明                                                         |
| :--------- | :----------- | :------- | :----------------------------------------------------------- |
| -namespace | 服务命令空间 | go.micro |                                                              |
| –type      | 服务类型     | srv      | 目前支持4种服务类型，分别是api、fnc(function)、srv(service)、web。 |

其它选项都是可配可不配，一般使用默认即可

| 配置指令 |                作用                 |                   默认值                   |                           说明                            |
| :------- | :---------------------------------: | :----------------------------------------: | :-------------------------------------------------------: |
| –fqdn    | 服务定义域，API需要通过该域找到服务 | 默认是使用服务的命令空间加上类型再加上别名 |                                                           |
| –alias   |              指定别名               |                 声明则必填                 | 使用单词，不要带任何标点符号，名称对Micro路由机制影响很大 |
| -plugin  |            使用哪些插件             |                 声明则必填                 |                    需要自选插件时使用                     |
| -gopath  |     是否使用GOPATH作为代码路径      |                    true                    |                                                           |



#### sample

默认方式创建新服务

```
micro new github.com/micro-in-cn/micro-all-in-one/middle-practices/micro-new/default
```

可以看到指令参数只有生成服务代码的路径，路径**最后一个单词就是服务项目名**，所以 **最后一个单词一定不要加任何符号！：**

```
Creating service go.micro.srv.default in /Users/me/workspace/go/src/github.com/micro-in-cn/micro-all-in-one/middle-practices/micro-new/default

.
├── main.go
├── plugin.go
├── handler
│   └── example.go
├── subscriber
│   └── example.go
├── proto/example
│   └── example.proto
├── Dockerfile
├── Makefile
└── README.md

## 下面是提示要安装protobuf和使用protobuf指令生成类文件，已经安装有了protobuf可以忽略，直接切到项目目录，再执行protoc指令
download protobuf for micro:

brew install protobuf
go get -u github.com/golang/protobuf/{proto,protoc-gen-go}
go get -u github.com/micro/protoc-gen-micro

compile the proto file example.proto:

## 切目录，生成文件
cd /Users/me/workspace/go/src/github.com/micro-in-cn/micro-all-in-one/middle-practices/micro-new/default
protoc --proto_path=. --go_out=. --micro_out=. proto/example/example.proto
```

生成的代码中，命令空间前缀为默认的**go.micro**，默认的服务类型为**srv**，服务别名（alias）为**default**

```go
package main
// ...
func main() {
	// New Service
	service := micro.NewService(
		micro.Name("go.micro.srv.default"),
		micro.Version("latest"),
	)
    // ...
}
```



