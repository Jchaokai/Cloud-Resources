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

    // Err指示为什么 Done channel关闭后, context被cancel。
    Err() error

    // 返回context取消的时间,(如果有的话)
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

context包提供了从现有上下文 派生`新的 Context`值的功能。这些值形成一棵树：取消`该context`时，从`该context`派生的所有`新的context`也会被取消。

`Background`是 `任何context`树的根；它永远不会被取消：

```
// Background returns an empty Context. It is never canceled, has no deadline,
// and has no values. Background is typically used in main, init, and tests,
// and as the top-level Context for incoming requests.
func Background() Context
```

`WithCancel`和`WithTimeout`返回派生的Context值，该值可以比`父Context`更早取消。当请求处理完成后，与请求关联的`context`通常被取消。 使用多个副本时，`WithCancel`对于取消冗余请求也很有用。`WithTimeout`可用于设置对后端服务器的请求的截止日期：

```
// 当parent.Done关闭 或 cancel函数被调用, ctx.Done 也会被关闭
func WithCancel(parent Context) (ctx Context, cancel CancelFunc)

// A CancelFunc cancels a Context.
type CancelFunc func()

// 当parent.Done关闭、cancel函数被调用、timeout时间过去, ctx.Done 也会被关闭
// context的 Deadline是 now + timeout 和 parent's deadline的较早者，(如果有的话)。
// 如果timer仍在运行，cancelFunc 将释放其资源。
func WithTimeout(parent Context, timeout time.Duration) (Context, CancelFunc)
```

`WithValue`提供了一种将请求范围的值与`Context`相关联的方法：

```
// WithValue返回一个衍生的context，其Value方法为key返回val。
func WithValue(parent Context, key interface{}, val interface{}) Context
```



查看如何使用上下文包的最佳方法是通过一个有效的示例。

#### Example

我们的示例是一个HTTP服务器，该服务器通过将查询“ golang” 转发到 [Google Web Search API](https://developers.google.com/web-search/docs/) 并呈现结果来处理类似 `/search?q=golang&timeout=1s`的URL。超时参数告诉服务器在该持续时间过去之后取消请求。

该代码分为三个包：

- [server](https://blog.golang.org/context/server/server.go) 为`/search`提供 `main` function 和 handler
- [userip](https://blog.golang.org/context/userip/userip.go) 提供用于从请求中提取用户IP地址并将其与`context`关联的功能。
- [google](https://blog.golang.org/context/google/google.go) 提供了`search`功能，用于向Google发送查询。



到这里，自己可以去[官方博客](https://blog.golang.org/context)看代码了。





<br/>

### References

- [官方博客:  Go Concurrency Patterns: Context](https://blog.golang.org/context)

- [context API](https://golang.org/pkg/context/)

