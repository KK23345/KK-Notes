# REST架构风格

## 简介

[官网](https://restfulapi.net/)定义：`REST`是 `REpresentational State Transfer` 的首字母缩写，是**分布式超媒体系统**的一种架构风格 。罗伊菲尔丁于 2000 年在他著名的 [论文](https://www.ics.uci.edu/~fielding/pubs/dissertation/rest_arch_style.htm)中首次提出它。

`REST`本身并没有创造新的技术、组件或服务，而隐藏在`RESTful`背后的理念就是—更好地使用现有Web标准中的一些准则和约束。理论上`REST`架构风格并不是绑定在`HTTP`上，只不过目前`HTTP`是唯一与`REST`相关的实例。 

## 什么是REST

`REST`： `REpresentational State Transfer`(表述性状态转移)。实际上，它的全称是`Resource Representational State Transfer`，即**资源**在网络传输中以某种**表现形式**进行**状态转移**。

- **资源**：在 REST 架构风格中，数据和功能被视为资源，并使用 **统一资源标识符** (URI) 进行访问；
- **表现形式**：**资源**必须与其**表示**分离，以便客户端可以访问各种格式的内容，例如 HTML、XML、纯文本、PDF、JPEG、JSON 等；
- **状态转移**：客户端和服务器通过使用**标准化的接口和协议**来交换**资源的表示**。并且，与服务器的每次交互都必须是**无状态**的(`HTTP`协议就是一个无状态协议，所有的资源状态都保存在服务器端，并且服务端不应该保存客户端状态)。
- **资源表示**：资源在任何特定时间的状态，包括：
  - **数据**_ 
  - 描述数据的 **元数据**
  - 可以帮助客户过渡到下一个所需状态的**超媒体链接**

## RESTful API

符合`REST`架构风格的 `Web API`（或 `Web 服务`）是 `RESTful API`。

参考：

- [RestFul API 简明教程](https://snailclimb.gitee.io/javaguide/#/docs/system-design/basis/RESTfulAPI?id=restful-api-简明教程)

- [RESTful接口详解](https://www.jianshu.com/p/7893169a7c93)

优点：

- 隐藏资源的访问行为，无法通过url得知对资源是何种操作
- 书写简化
