### 架构的演进之路
<br/>

&emsp;**单体架构&emsp;->&emsp;垂直架构&emsp;->&emsp;SOA架构&emsp;->&emsp;微服务架构**

<br/>

- **单体架构**
将所有的功能放到一个应用中，所有的代码放在一起，最快的做出一个demo出来,一台服务器就可以搞定

- **垂直架构**
 模块化的开发，当业务丰富，将不同的业务分开，比如MVC架构，当有新的功能需要添加/修改，要写代码，测试，有时候测试过了，提交上线时，模块之间又会出现问题

- **SOA架构**
面向服务的体系架构，将一些常用的、核心的服务抽取出来，作为单独的服务运行，供其他的服务调用。把项目拆成各个组件，组件之间相互合作。但是服务变得越来越多，变得杂乱，这时候大家可能会将所有的服务与一个“高速公路对接”（ESB，商业通信组件技术）

- **微服务架构**
 SOA架构的升级，不同于SOA拆分成一个个的服务，拆分的力度非常小，可能将同一个服务下的不同业务都拆分开来，强调一个“微”字，每个业务可能会用不同的数据库，技术栈 · · · 

**SOA与微服务的区别示意图：**
![SOA与微服务区别图.JPG](https://github.com/Jchaokai/Cloud-Resources/blob/master/images/Mirco/SOA%E4%B8%8E%E5%BE%AE%E6%9C%8D%E5%8A%A1%E5%8C%BA%E5%88%AB.JPG)

==架构只有合适不合适！==

### 微服务介绍
1. 根据业务模块划分服务种类
2. 每个微服务单独部署且相互隔离
3. 通过轻量的API调用
4. 服务需要保证高可用

微服务优势：
&emsp;松耦合、独立发布、快速迭代、故障隔离
微服务带来的挑战：
&emsp;DevOps要求、系统复杂性、数据一致性、测试复杂性

### 微服务技术选型
Java系：Spring Cloud、Dubbo

<br/>
Go系：Go-Mirco、Go Kit、Kite

### 微服务核心

1. API gateway
2. 进程间通信
3. 服务注册发现
4. 时间驱动的数据管理
5. 微服务部署策略
6. 微服务改造

### Go微服务架构

![GoMirco架构.JPG](https://github.com/Jchaokai/Cloud-Resources/blob/master/images/Mirco/Go-Mirco%E6%9E%B6%E6%9E%84.JPG)




