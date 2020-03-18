## Go-Mirco

Go-Mirco(库)、Mirco(基于Go-Mirco开发的运行时工具集)

### Mirco工具集

1. **API**

    ![Mirco-API示意图](https://github.com/Jchaokai/Cloud-Resources/blob/master/images/Mirco-API.JPG)

    将一些http请求 基于命名空间ns 路由到内部的对应的API处理，也可以将消息`event`，广播到消息订阅者，此图只示意两种

    **Mirco-API有五种类型：**

    - API&emsp;&emsp;将http请求路由到对应API接口

    - RPC&emsp;&emsp;将http请求路由到RPC服务

    - event&emsp;&emsp;将http请求广播到订阅者

    - proxy&emsp;&emsp;反向代理

    - web&emsp;&emsp;支持websocket的反响代理

        

2. **Web**

    两个服务，反向代理 与 管理控制台（可以管理查看服务的健康状况）

    ![Mirco-web示意图](https://github.com/Jchaokai/Cloud-Resources/blob/master/images/Mirco-web.JPG)

    

3. **Proxy**

    ![Mirco-proxy示意图](https://github.com/Jchaokai/Cloud-Resources/blob/master/images/Mirco-proxy.JPG)

    

4. **Cli**

    功能：以命令行操作Mirco服务

    mirco help 了解更多

    

5. **Bot**

    ![Mirco-bot示意图](https://github.com/Jchaokai/Cloud-Resources/blob/master/images/Mirco-proxy.JPG)

    Go-Mirco V2 可能改名位  agent





### References

1. [Github: https://github.com/micro-in-cn](Github: https://github.com/micro-in-cn)