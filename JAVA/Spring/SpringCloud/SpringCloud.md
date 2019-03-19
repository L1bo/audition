[TOC]
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
