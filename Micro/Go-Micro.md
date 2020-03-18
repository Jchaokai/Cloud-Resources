## Go-Mirco

Go-Mirco(库)、Mirco(基于Go-Mirco开发的运行时工具集)



### Mirco工具集

1. **API Gateway**

    参考了[ API网关模式 ](http://microservices.io/patterns/apigateway.html)为服务提供了一个单一的公共入口。通过服务发现，Micro API以http方式，将请求动态路由到具体的后台服务接口。我们以下简称**API**

    ![Mirco-API示意图](https://github.com/Jchaokai/Cloud-Resources/blob/master/images/Mirco/Mirco-API.JPG)

    将一些http请求 基于命名空间ns 路由到内部的对应的API处理，也可以将消息`event`，广播到消息订阅者，此图只示意两种

    **API Gateway  Handlers  (Handler负责持有并管理HTTP请求路由)：**

    - API Handler&emsp;&emsp;将http请求路由到对应API接口
    - RPC Handler&emsp;&emsp;将http请求路由到RPC服务
    - event Handler&emsp;&emsp;将http请求广播到订阅者
    - proxy Handler&emsp;&emsp;反向代理
    - web Handler&emsp;&emsp;支持websocket的反响代理

    通过[`/rpc`](https://micro.mu/docs/cn/api.html#rpc-endpoint)入口可以绕开handler处理器。

    目前版本（V1）无法支持多个handler并存运行，也即同时只能使用一个handler。

    **API Gateway Resolver (解析器，Micro使用命名空间与HTTP请求路径来动态路由到具体的服务)**

    - RPC Resolver

    - Proxy Resolver

        

    [API Gateway  ## 具体见官网 ##](https://micro.mu/docs/cn/api.html)

    

2. **Web**

    两个服务，反向代理 与 管理控制台（可以管理查看服务的健康状况）

    ![Mirco-web示意图](https://github.com/Jchaokai/Cloud-Resources/blob/master/images/Mirco/Mirco-web.JPG)

    

3. **Proxy**

    ![Mirco-proxy示意图](https://github.com/Jchaokai/Cloud-Resources/blob/master/images/Mirco/Mirco-proxy.JPG)

    

4. **Cli**

    功能：以命令行操作Mirco服务

    mirco help 了解更多

    

5. **Bot**

    ![Mirco-bot示意图](https://github.com/Jchaokai/Cloud-Resources/blob/master/images/Mirco/Mirco-bot.JPG)

    Go-Mirco V2 可能改名位  agent



### Go - Mirco framework

![Go-Mircoframework示意图](https://github.com/Jchaokai/Cloud-Resources/blob/master/images/Mirco/Go-Mirco-framework.JPG)

基础组件调用关系图：

![基础组件调用关系图](https://github.com/Jchaokai/Cloud-Resources/blob/master/images/Mirco/Go-Mirco%E5%85%B6%E4%BB%96%E7%BB%84%E4%BB%B6.JPG)

其他组件 ：

![其他组件](https://github.com/Jchaokai/Cloud-Resources/blob/master/images/Mirco/Go-Mirco%E5%85%B6%E4%BB%96%E7%BB%84%E4%BB%B6.JPG)

- **Broker 异步消息组件**

    ![Broker](https://github.com/Jchaokai/Cloud-Resources/blob/master/images/Mirco/Go-Mirco-broker.JPG)

- **Register 注册组件**

    ![Registry](https://github.com/Jchaokai/Cloud-Resources/blob/master/images/Mirco/Go-Mirco-registry.JPG)

    支持的注册类型：

    1. 基于通用注册中心，如Etcd、Consul、ZooKeeper、Eureka
    2. 基于网络广播，如mDNS、Gossip
    3. 基于消息中间件，如NATs

- **Selector 选择器组件**

    ![Selector](https://github.com/Jchaokai/Cloud-Resources/blob/master/images/Mirco/Go-Mirco-selector.JPG)

- **transport 同步通信组件**

    ![transport](https://github.com/Jchaokai/Cloud-Resources/blob/master/images/Mirco/Go-Mirco-transport.JPG)

    **transport 分类：**

    1. HTTP ：&emsp;httpTransport、grpcTransport
    2. TCP ：&emsp;tcpTransport
    3. UDP ：&emsp;udpTransport、quicTransport
    4. MQ ：&emsp;rabbitMQTransport、natsTransport



### 关于 Go-Mirco 的插件化

插件化的原理：

![插件化的原理](https://github.com/Jchaokai/Cloud-Resources/blob/master/images/Mirco/Go-Mirco%E6%8F%92%E4%BB%B6%E5%8C%96%E5%8E%9F%E7%90%86.JPG)

插件化代码演示：

![插件化代码演示](https://github.com/Jchaokai/Cloud-Resources/blob/master/images/Mirco/Go-Mirco%E6%8F%92%E4%BB%B6%E5%8C%96%E4%BB%A3%E7%A0%81%E6%BC%94%E7%A4%BA.JPG)



------



### References

1. [Github: micro-in-cn](https://github.com/micro-in-cn)
2. [Micro 文档](https://micro.mu/docs/cn/index.html)
3. [microservices.io](https://microservices.io/)

