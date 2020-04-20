### [Go Concurrency Patterns: Context](https://blog.golang.org/context)

------

#### Introduction

在Go服务器中，每个传入请求都在其自己的goroutine中进行处理。 请求处理程序通常会启动其他goroutine来访问后端，例如数据库和RPC服务。 处理请求的goroutine集合通常需要访问特定要求的值，例如最终用户的身份，授权令牌和请求的期限。 当一个请求被取消或超时时，处理该请求的所有goroutine应该迅速退出，以便系统可以回收他们正在使用的任何资源。<br/>

在Google，我们开发了一个`context`包，可轻松将跨API边界的  请求范围的值 (`request-scoped data`)，取消信号 (`cancelation signals`) 和截止日期 (`deadlines` )传递给处理请求所涉及的所有goroutine。 该软件包可作为 [context](https://golang.org/pkg/context)公开获得。 本文介绍了如何使用该程序包，并提供了一个完整的工作示例。

#### Context

`context` package的核心是 Context type，简要如下：

```go
// context在API边界上  携带截止日期，取消信号和请求范围的值。 它的方法可安全地用于多个goroutine。
type Context interface {
    // 返回一个channel(作为一个取消信号),关闭这个channel时，运行context的函数应该放弃工作并返回
    // 当context 取消or超时，此channel也会关闭
    Done() <-chan struct{}

    // Err()表明/指出 为什么这个context被取消 ( 在Done()后channel取消 )
    Err() error

    // 返回context取消的时间
    Deadline() (deadline time.Time, ok bool)

    // 返回与 key有关的值，没有返回nil
    Value(key interface{}) interface{}
}
```

Done方法将一个`channel`作为`取消信号`返回给   运行`context`的函数，关闭该通道时，这些函数应放弃工作并返回。 Err方法返回一个错误，指示为什么取消上下文。<br/>

出于Done()返回`<-chan`的原因，`context`没有取消方法。接收`取消信号`的函数通常不是发送该信号的函数。特别是，当父操作为子操作启动goroutines时，这些子操作应该不能取消父操作。相反，WithCancel函数（下面会有描述）提供了一种取消新的`context`值的方法。<br/>

`context`对于由多个`goroutine`同时使用是安全的。可以将`单个context`传递给任意数量的`goroutine`，并通过取消该`context`对所有`goroutine`发出信号。<br/>

Deadline方法允许函数确定是否他们应该全力开始工作。如果剩余时间太少，那可能不值得。代码还可以使用截止日期来设置I / O操作的超时。<br/>

`value`允许`context`携带请求范围的数据 (`request-scoped data`)。该数据必须安全，以供多个goroutine同时使用。

#### Derived contexts （派生的context）



正在更新 . . .

<br/>

### References

- [官方博客:  Go Concurrency Patterns: Context](https://blog.golang.org/context)

