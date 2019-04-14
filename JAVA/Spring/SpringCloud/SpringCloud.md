[TOC]
# SpringCloud组件

## 什么是 spring cloud？
spring cloud 是一系列框架的有序集合。它利用 spring boot 的开发便利性巧妙地简化了分布式系统基础设施的开发，如服务发现注册、配置中心、消息总线、负载均衡、断路器、数据监控等，都可以用 spring boot 的开发风格做到一键启动和部署。

## spring cloud 的核心组件有哪些？
Eureka：服务注册于发现。
Feign：基于动态代理机制，根据注解和选择的机器，拼接请求 url 地址，发起请求。
Ribbon：实现负载均衡，从一个服务的多台机器中选择一台。
Hystrix：提供线程池，不同的服务走不同的线程池，实现了不同服务调用的隔离，避免了服务雪崩的问题。
Zuul：网关管理，由 Zuul 网关转发请求给对应的服务。

## Eureka
Eureka是基于AP原则,ZooKeeper是基于CP原则构建的(CAP)
服务注册中心，用于管理服务

Eureka Client：负责将这个服务的信息注册到Eureka Server中
Eureka Server：注册中心，里面有一个注册表，保存了各个服务所在的机器和端口号

Eureka：各个服务启动时，Eureka Client都会将服务注册到Eureka Server，并且Eureka Client还可以反过来从Eureka Server拉取注册表，从而知道其他服务在哪里

## Ribbon
基于客户端的负载均衡组件

Ribbon：服务间发起请求的时候，基于Ribbon做负载均衡，从一个服务的多台机器中选择一台

## Hystris
在分布式架构中，断路器模式的作用也是类似的，当某个服务单元发生故障（类似用电器发生短路）之后，通过断路器的故障监控（类似熔断保险丝），向调用方返回一个错误响应，而不是长时间的等待。这样就不会使得线程因调用故障服务被长时间占用不释放，避免了故障在分布式系统中的蔓延。

容错框架，能够防止服务的雪崩效应

Hystrix：发起请求是通过Hystrix的线程池来走的，不同的服务走不同的线程池，实现了不同服务调用的隔离，避免了服务雪崩的问题

## Feign
Web服务客户端，能够简化Http接口的调用

人家Feign Client会在底层根据你的注解，跟你指定的服务建立连接、构造请求、发起靕求、获取响应、解析响应，等等。这一系列脏活累活，人家Feign全给你干了。


那么问题来了，Feign是如何做到这么神奇的呢？很简单，Feign的一个关键机制就是使用了动态代理。咱们一起来看看下面的图，结合图来分析：

首先，如果你对某个接口定义了@FeignClient注解，Feign就会针对这个接口创建一个动态代理
接着你要是调用那个接口，本质就是会调用 Feign创建的动态代理，这是核心中的核心
Feign的动态代理会根据你在接口上的@RequestMapping等注解，来动态构造出你要请求的服务的地址
最后针对这个地址，发起请求、解析响应

Feign：基于Feign的动态代理机制，根据注解和选择的机器，拼接请求URL地址，发起请求

## Zuul
API网关，提供路由转发、请求过滤等功能

Zuul：如果前端、移动端要调用后端系统，统一从Zuul网关进入，由Zuul网关转发请求给对应的服务

## Config
分布式配置管理

## Sleuth
服务跟踪

## Stream
构建消息驱动的微服务应用程序的框架

## Bus
消息代理的集群消息总线


# 各组件

## Eureka

### 为什么服务下线了，Eureka Server接口返回的信息还会存在
Eureka Server并不是强一致的，因此 registry 中会存留过期的实例信息
- 应用实例异常挂掉，没能在挂掉之前告知Eureka Server要下线掉该服务实例信息。这个就需要依赖 Eureka Server 的 EvictionTask 去剔除。
- 应用实例下线时有告知 Eureka Server 下线，但是由于 Eureka Server 的REST API有response cache，因此需要等待缓存过期才能更新。
- Eureka Server由于开启并引入了 SELF PRESERVATION 模式，导致 registry 的信息不会因为过期而被剔除掉，直到退出 SELF PRESERVATION 模式

针对 Client 下线没有通知 Eureka Server 的问题，可以调整 EvictionTask 的调度频率，比如下面配置将调度间隔从默认的60秒，调整为5秒:
`eureka.server.eviction-interval-timer-in-ms=5000`
针对 response cache 的问题，可以根据情况考虑关闭 readOnlyCacheMap:
`eureka.server.use-read-only-response-cache=false`
或者调整 readWriteCacheMap 的过期时间:
`eureka.server.response-cache-auto-expiration-in-seconds=60`
针对 SELF PRESERVATION 的问题，在测试环境可以将 enable-self-preservation 设置为false:
`eureka.server.enable-self-preservation=false`

测试环境 提高Client端拉取 Server 注册信息的频率
`eureka.client.registry-fetch-interval-seconds=5`

### 如何利用eureka进行项目的新代码发布(老项目在跑)
调用Eureka REST API
启动新服务
1. 查询所有实例(GET /eureka/apps)
2. 暂停应用实例(PUT /eureka/apps/{appId}/{instanceId}/status?value=OUT_OF_SERVICE)
3. 注销应用实例(DELETE /eureka/apps/{appId}/{instanceId})
等数据跑完，关闭旧服务
