## Go-Mirco

Go-Mirco(库)、Mirco(基于Go-Mirco开发的运行时工具集)

### Mirco工具集

1. **API**

    ![Mirco-API示意图](https://github.com/Jchaokai/Cloud-Resources/blob/master/images/Mirco/Mirco-API.JPG)

    将一些http请求 基于命名空间ns 路由到内部的对应的API处理，也可以将消息`event`，广播到消息订阅者，此图只示意两种

    **Mirco-API有五种类型：**

    - API&emsp;&emsp;将http请求路由到对应API接口

    - RPC&emsp;&emsp;将http请求路由到RPC服务

    - event&emsp;&emsp;将http请求广播到订阅者

    - proxy&emsp;&emsp;反向代理

    - web&emsp;&emsp;支持websocket的反响代理

        

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

![基础组件调用关系图](https://github.com/Jchaokai/Cloud-Resources/blob/master/images/Mirco/Go-Mirco基础组件.JPG)

其他组件 ：

![其他组件](https://github.com/Jchaokai/Cloud-Resources/blob/master/images/Mirco/Go-Mirco其他组件.JPG)

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

![插件化的原理](https://github.com/Jchaokai/Cloud-Resources/blob/master/images/Mirco/Go-Mirco插件化原理.JPG)

插件化代码演示：

![插件化代码演示](https://github.com/Jchaokai/Cloud-Resources/blob/master/images/Mirco/Go-Mirco插件化代码演示.JPG)





### References

1. [Github: micro-in-cn](https://github.com/micro-in-cn)