* [1.API网关的作用](#1)


API网关作用
API的管理部分

分析（Analytics） – API网关可以和你的分析基础设施保持透明的交互和通信，因为API网关是每个请求（request）的入口，必经地。API网关可以看到所有的数据，可以知道经过你的service的流量。这就相当于你有一个集中的地方，你可以把所有的这些信息push到你的监控或分析工具，比如Kibana或者Splunk。

自动化（Automation） –网关还有助于自动化部署，还可以帮助你实现自动化登入验证（on boarding）。 什么是登入（on boarding）呢？ 如果你有API，并且你希望有身份验证，你可能需要一些功能可以允许用户为该API创建登入凭据（credentials）然后开始使用（消费）API。 开发人员门户网站或你的文档中心等都可以与网关集成来配置这些凭据（credentials），这样你就不用从头开始构建一些功能了。

什么是Kong
官方站点：https://getkong.org

官方描述：

Kong is a scalable, open source API Layer (also known as an API Gateway, or API Middleware). Kong runs in front of any RESTful API and is extended through Plugins, which provide extra functionality and services beyond the core platform. Kong是一个可扩展的，开源的API层，运行在RESTful前端，可通过插件扩展（认证，安全，流量控制，分析&监控，转换，日志）

Kong是Mashape开源的高性能高可用API网关和API服务管理层。它基于OpenResty，进行API管理，并提供了插件实现API的AOP。Kong在Mashape 管理了超过15,000 个API，为200,000开发者提供了每月数十亿的请求支持。 Kong 是基于 Openresty 的，而 Openresty 又是 Nginx 的二次封装，所有很多配置项和 Nginx 类似。

Kong具有以下特点：

可扩展性: Kong通过简单的增加机器节点，可以很容易的水平扩展，它通过简单的增加机器节点。

模块化: Kong可以通过插件方式扩展，这个插件可以很容易的通过一个RESTful管理API来配置。

可以运行在任何基础架构: Kong 可以处处运行。您可以在云或内部部署环境，包括单个或多个数据中心的设置，私有或者邀请受限APIs.

请求工作流
为了更好的理解Kong这个系统，下面是一个典型API的请求工作流:

![image](https://github.com/starzjl/kong/blob/master/images/kong-simple.png)

一旦Kong在运行，每个请求所作出的API将先请求Kong服务器，然后它会被代理到最终的API。这些插件在 API 请求响应循环的生命周期中被执行。Kong将有效地将成为每个API请求的入口点。

Kong 是在客户端和（微）服务间转发API通信的API网关，通过插件扩展功能。Kong 有两个主要组件：

Kong Server ：基于nginx 的服务器，用来接收 API 请求。
Apache Cassandra或PG ：用来存储操作数据。
你可以通过增加更多 Kong Server 机器对 Kong 服务进行水平扩展，通过前置的负载均衡器向这些机器分发请求。根据文档描述，两个Cassandra节点就足以支撑绝大多数情况，但如果网络非常拥挤，可以考虑适当增加更多节点。

对于开源社区来说，Kong 中最诱人的一个特性是可以通过插件扩展已有功能，这些插件在 API 请求响应循环的生命周期中被执行。插件使用 Lua 编写，而且 Kong 还有如下几个基础功能 ：HTTP 基本认证、密钥认证、CORS（ Cross-origin Resource Sharing，跨域资源共享）、TCP、UDP、文件日志、API 请求限流、请求转发以及 nginx 监控。

Kong包 可运行在某些 Linux 发行版、Mac OS X 和 Docker 中，无论是本地机还是云端服务器皆可运行。

除了免费的开源版本，Mashape 还提供了付费的 企业版 ，其中包括技术支持、使用培训服务以及 API 分析插件。

![image](https://github.com/starzjl/kong/blob/master/images/原理.png)


Kong核心基于OpenResty构建，实现了请求/响应的Lua处理化；
Kong插件拦截请求/响应，如果接触过Java Servlet，等价于拦截器，实现请求/响应的AOP处理；
Kong Restful 管理API提供了API/API消费者/插件的管理；
数据中心用于存储Kong集群节点信息、API、消费者、插件等信息，目前提供了PostgreSQL和Cassandra支持，如果需要高可用建议使用Cassandra；
dnsmasq用于提供给Nginx DNS解析功能；
Kong集群中的节点通过gossip协议自动发现其他节点，当通过一个Kong节点的管理API进行一些变更时也会通知其他节点。每个Kong节点的配置信息是会缓存的，如插件，那么当在某一个Kong节点修改了插件配置时，需要通知其他节点配置的变更。

Kong的整体流程架构如下图所示：

![image](https://github.com/starzjl/kong/blob/master/images/流程图.png)

![image](https://github.com/starzjl/kong/blob/master/images/openresty.png)

Kong = OpenResty +NGINX

从技术的角度讲，Kong可以认为是一个OpenResty应用程序。 OpenResty运行在NGINX之上，使用Lua扩展了NGINX。 Lua是一种非常容易使用的脚本语言，可以让你在NGINX中编写一些可以执行的操作。

OpenResty针对一次API 请求-响应（request‑and‑response） 生命周期中的不同的事件提供了钩子，所以你可以编写Lua脚本代码，然后把这些代码挂载到那些事件上-比如，一个新的请求（request）正在进入的时候，一个新的请求（request）将要被代理的时候，一个响应返回了的时候。你可以为每个事件编写定制的代码而且你还可以动态的改变请求的内容（requests）和响应的内容（responses）。

【在架构上】我们底层是使用NGINX为Kong提供底层的支持。所有的代理都是由NGINX来完成-这是一个非常强大的能力。而OpenResty则是Kong的核心的核心。它通过这些新功能扩展了NGINX，而在这两种技术之上，Kong实现了集群(clustering)，插件(plugins)和可用于管理API网关的RESTful API。

像Elasticsearch，你就可以通过API来做一些和数据库有关的你想要做的事情，Kong也暴露了API，你完全可以通过这些API去操作到Kong的内部，只需要发送一次HTTP调用，然后把响应回来的JSON解析就可以使用了。

这意味着你可以集成Kong到这个链接中（href=https://www.nginx.com/resources/glossary/devops/）Devops以及自动化工具中去。你也可以集成Kong到第三方service中去，以及一些开发者门户（developer portals）以及其他一些入口工具中。Kong支持把所有的信息保存到PostgreSQL或者Cassandra，当然这取决于你要哪一种存储方案。

Cassandra是最终一致的数据库。 这意味着如果你有一个由多个节点组成的Cassandra集群，你存储数据的话，则最终该数据将传播到所有其他节点 – 但不是同时[或“立即”]到达每个节点。

Ok，假设现在一个三节点的Cassandra集群位于DC[data center]1，另一个三节点的Cassandra集群位于DC2，然后你把这两个集群又连接到了一起，保持同步。这种情况下，你在一个数据中心做的任何操作最终都会被复制到另外那个数据中心去。

PostgreSQL使用起来更加简单。它支持master和slave，但不支持跨DC的多数据中心复制（就是不支持那种masterless架构）。不过，也有一些工具可以帮你实现跨数据中心复制。

Kong技术架构
Kong把所有的通用功能集中到了一个地方，这就让service的开发变得更加的轻松简单，你只需要安心的关注和业务紧相关的事情。

在使用Kong之前API架构存在的问题：

通用模块在各API中没有复用
难以维护
不影响其他服务状态下难以扩展
由于系统限制生产效率低下
在使用Kong后的API架构：

Kong统一管理通用插件
高效，可扩展的分布式架构体系
通过简单的命令即可完成扩展
团队专注于业务，Kong来处理 REST
Kong默认插件
身份认证：Kong提供了Basic Authentication、Key authentication、OAuth2.0 authentication、HMAC authentication、JWT、LDAP authentication认证实现。

安全：ACL（访问控制）、CORS（跨域资源共享）、动态SSL、IP限制、爬虫检测实现。

流量控制：请求限流（基于请求计数限流）、上游响应限流（根据upstream响应计数限流）、请求大小限制。限流支持本地、Redis和集群限流模式。

分析监控：Galileo（记录请求和响应数据，实现API分析）、Datadog（记录API Metric如请求次数、请求大小、响应状态和延迟，可视化API Metric）、Runscope（记录请求和响应数据，实现API性能测试和监控）。

转换：请求转换（在转发到upstream之前修改请求）、响应转换（在upstream响应返回给客户端之前修改响应）。

日志：TCP、UDP、HTTP、File、Syslog、StatsD、Loggly等。

也可以开发自己的插件，如缓存等。

Kong作为API网关提供了API管理功能，及围绕API管理实现了一些默认的插件，另外还具备集群水平扩展能力，从而提升整体吞吐量。Kong本身是基于OpenResty，可以在现有Kong的基础上进行一些扩展，从而实现更复杂的特性。

有一些特性Kong默认是缺失的，如API级别的超时、重试、fallback策略、缓存、API聚合、ABTest等，这些需要开发者自己定制和扩展。

参考资料
https://github.com/Kong/kong
https://getkong.org/


