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

server
```yml
spring:
  application:
    name: eurka-server
  # 安全认证
  security:
    user:
      name: admin
      password: 123456

eureka:
  instance:
    # 服务注册中心实例的主机名
    hostname: localhost
  client:
    # false 表示不向注册中心中注册自己
    registerWithEureka: false
    # false 表示自己端就是注册中心,我的职责就是维护服务实例，并不需要去检索服务
    fetchRegistry: false
    serviceUrl:
      defaultZone: http://${spring.security.user.name}:${spring.security.user.password}@localhost:8888/eureka/
  server:
    eviction-interval-timer-in-ms: 5000
    # 关闭保护模式
    enable-self-preservation: false
```

client
```yml
server:
  port: 8901

spring:
  application:
    name: feign-server

eureka:
  instance:
    # 服务注册中心实例的主机名
    hostname: localhost
    # 设置实例的ID为ip:port
    instance-id: ${spring.application.name}:${spring.cloud.client.ip-address}:${server.port}
    # 使用ip代替实例名
    prefer-ip-address: true
    # 自定义跳转链接
    status-page-url: http://${spring.cloud.client.ip-address}:${server.port}
  user: admin
  password: 123456
  client:
    serviceUrl:
      defaultZone: http://${eureka.user}:${eureka.password}@localhost:8888/eureka/
```
### 高可用
集群需要相互指向(server 1-> 2 3, 2-> 1 3, 3-> 1, 2)
client-> 1 2 3


## Ribbon
基于客户端的负载均衡组件

Ribbon：服务间发起请求的时候，基于Ribbon做负载均衡，从一个服务的多台机器中选择一台

### 为什么在RestTemplate上加一个@LoadBalanced之后，RestTemplate就能跟Eureka结合了，可以使用服务名称去调用接口，还可以负载均衡?
给 RestTemplate 增加拦截器，在请求之前对请求的地址进行替换，或者根据具体的负载策略选择服务地址，然后再去调用。

### 获取一个服务的服务地址
```java
@Autowired
private LoadBalancerClient loadBalancer;

@GetMapping("/choose")
public Object chooseUrl() {
    ServiceInstance serviceInstance = loanBalancer.choose("fsh-house");
    return serviceInstance;
}
```

### Ribbon 饥饿加载
Ribbon的客户端是在第一次请求的时候初始化的，如果超时时间较短的话，初始化Client的时间再加上请求接口的时间，就会导致第一次请求超时。
```properties
ribbon.eager-load.enabled=true
ribbon.eager-load.clients=fsh-house
```
开启Ribbon的饥饿加载模式
指定需要饥饿加载的服务名(需要调用的服务)，若有多个则用逗号隔开

### 负载均衡策略
- BestAvailabl
- AvailabilityFilteringRule
- ZoneAvoidanceRule
- RandomRule: 随机选择一个Server
- RoundRobinRule: 轮询选择，轮询index，选择index对应位置的Server
- RetryRule: 对选定的负载均衡策略机上重试机制
- ResponseTimeWeightedRule
- WeightedResponseTimeRule

### 自定义负载均衡策略
```java
public class MyRule implements IRule {
    private ILoadBalancer lb;

    @Override
    public Server choose(Object key) {
        List<Server> servers = lb.getAllServers();
        for (Server server : servers) {
            System.out.println(server.getHostPort());
        }
        return servers.get(0);
    }

    @Override
    public void setLoadBalancer(ILoadBalancer lb) {
        this.lb = lb;
    }

    @Override
    public ILoadBalancer getLoadBalancer() {
      return lb;
    }
}
```
通过配置的方式自定义负载均衡策略
```properties
fsh-house.ribbon.NFLoadBalancerRuleClassName=com.fangjia.fsh.substitution.rule.MyRule
```

### 常用配置

#### 禁用Eureka
不想和Eureka集成
ribbon.eureka.enabled=false
不能使用服务名称去调用接口，必须指定服务名

#### 配置接口列表
fsh-house.ribbon.listOfServers=localhost:8081,localhost:8083
针对具体服务，前缀就是服务名称

#### 配置负载均衡策略
fsh-house.ribbon.NFLoadBalancerRuleClassName=com.netflix.loadbalancer.RandomRule

#### 超时时间
```properties
# 请求连接的超时时间
ribbon.connectTimeout=2000
# 请求处理的超时时间
ribbon.readTimeout=5000
```

#### 请求重试
```properties
# 对当前实例的重试次数
ribbon.maxAutoRetries=1
# 切换实例的重试次数
ribbon.maxAutoRetriesNextServer=3
# 对所有操作请求都进行重试
ribbon.okToRetryOnAllOperations=true
```

pom 添加Spring Retry依赖
```xml
<dependency>
    <groupId>org.springframework.retry</groupId>
    <artifactId>spring-retry</artifactId>
</dependency>
```

#### 代码配置Ribbon
```java
@Configuration
public class BeanConfiguration {

    @Bean
    public MyRule rule() {
        return new MyRule();
    }
}
```
创建一个Ribbon客户端的配置类，关联BeanConfiguration，用name来指定调用的服务名称
```java
@RibbonClient(name = "fsh-house", configuration = BeanConfiguration.class)
public class RibbonClientConfig {}
```


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

声明式的REST客户端

### 日志配置
```java
package com.exler.feignclient.config;

import feign.Logger;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class FeignConfiguration {

    @Bean
    Logger.Level feignLoggerLevel() {
        return Logger.Level.FULL;
    }
}
```
- NONE: 不输出日志
- BASIC: 只输出请求方法的URL和响应状态码以及接口执行的时间
- HEADERS: 将BASIC信息和请求头信息输出
- FULL: 输出完整的请求信息

```
logging.level.com.exler.feignclient.client.HelloClient=debug
```

### 定义请求拦截器
RequestInterceptor
```java
import feign.RequestInterceptor;
import feign.RequestTemplate;
import lombok.extern.slf4j.Slf4j;

/**
 * @author Exler(yz)
 * name: FeignBasicRequestInterceptro
 * time: 2019/5/6 11:00
 * describe: feign 请求拦截器
 */
@Slf4j
public class FeignBasicRequestInterceptor implements RequestInterceptor {

    @Override
    public void apply(RequestTemplate requestTemplate) {
        log.info("请求拦截器");
        requestTemplate.header("test", "test");
    }
}
```
在feign配置类中添加
```java
@Bean
public FeignBasicRequestInterceptor basicRequestInterceptor() {
    return new FeignBasicRequestInterceptor();
}
```


## Zuul
API网关，提供路由转发、请求过滤等功能

Zuul：如果前端、移动端要调用后端系统，统一从Zuul网关进入，由Zuul网关转发请求给对应的服务

Zuul是一个基于JVM路由和服务端端负载均衡器。提供路由、监控、弹性、安全等方面的服务框架。
过滤器

- 动态路由: 动态的将客户端的请求路由到后端不同的服务，做一些逻辑处理，比如聚合多个服务的据返回
- 请求监控: 可以对整个系统的请求进行监控，记录详细的请求日志，可以实时统计当前系统的访问量以及监控状态
- 认证鉴权: 对每一个访问的请求做认证，拒绝非法请求，保护好后端的服务
- 压力测试: 压力测试是一项很重要的工作，像一些电商公司需要模拟更多的用户并发量来保证重大活动时系统的稳定。通过Zuul可以动态地将请求转发到后端服务集群中，和可以识别测试流量和真实流量，从而做一些特殊处理
- 灰度发布: 灰度发布可以保证整体系统的稳定，在初始灰度的时候就可以发现、调整问题，以保证其影响度

### 请求的生命周期
外部http请求到达api网关服务的时候，
首先它会进入第一个阶段pre，在这里它会被pre类型的过滤器进行处理。该类型过滤器的主要目的是在进行请求路由之前做一些前置加工，比如请求的校验等。
在完成了pre类型的过滤器处理之后，请求进入第二个阶段routing，也就是之前说的路由请求转发阶段，请求将会被routing类型的处理器处理。这里的具体处理内容就是将外部请求转发到具体服务实例上去的过程，
当服务实例请求结果都返回之后，routing阶段完成，请求进入第三个阶段post。此时请求将会被post类型的过滤器处理，这些过滤器在处理的时候不仅可以获取到请求信息，还能获取到服务实例的返回信息，所以在post类型的过滤器中，我们可以对处理结果进行一些加工或转换等内容。
另外，还有一个特殊的阶段error，该阶段只有在上述三个阶段中发生异常的时候才会触发，但是它的最后流向还是post类型的过滤器，因为它需要通过post过滤器将最终结果返回给请求客户端（对于error过滤器的处理，在spring cloud zuul的过滤链中实际上有一些不同）

![Zuul请求的生命周期](../../../picture/Java/SpringCloud/Zuul/5225109-371a03f23204ae6f.webp)

### 自定义Zuul抛出异常
ZuulFilter 中的 FilterType 中有一个 Error 的处理器
解决方案如下:

error 类型的 zuulFilter
执行顺序在默认的 SendErrorFilter (index=0)之前,因此设置为-1
should 方法中检查有没有抛出异常
run 方法中对异常进行处理

```java
@Component
public class ErrorFilter extends ZuulFilter {
    private final Logger log = LoggerFactory.getLogger(Http401UnauthorizedEntryPoint.class);
 
    @Override
    public String filterType() {
        return "error";
    }
 
    @Override
    public int filterOrder() {
        //需要在默认的 SendErrorFilter 之前
        return -1; // Needs to run before SendErrorFilter which has filterOrder == 0
    }
 
    @Override
    public boolean shouldFilter() {
        // only forward to errorPath if it hasn't been forwarded to already
        return RequestContext.getCurrentContext().containsKey("throwable");
    }
 
    @Override
    public Object run() {
        try {
            RequestContext ctx = RequestContext.getCurrentContext();
            Object e = ctx.get("throwable");
 
            if (e != null && e instanceof ZuulException) {
                ZuulException zuulException = (ZuulException) e;
 
                // Remove error code to prevent further error handling in follow up filters
                // 删除该异常信息,不然在下一个过滤器中还会被执行处理
                ctx.remove("throwable");
                // 根据具体的业务逻辑来处理
                ctx.setResponseStatusCode(HttpStatus.UNAUTHORIZED.value());
                 
            }
        } catch (Exception ex) {
            log.error("Exception filtering in custom error filter", ex);
            ReflectionUtils.rethrowRuntimeException(ex);
        }
        return null;
    }
}
```

### Zuul添加请求信息 
```java
import com.netflix.zuul.ZuulFilter;
import com.netflix.zuul.context.RequestContext;
import com.netflix.zuul.exception.ZuulException;

/**
 * @author Exler(yz)
 * name: ZuulFilter
 * time: 2019/5/6 12:06
 * describe:
 */
public class AuthHeaderFilter extends ZuulFilter {

    public AuthHeaderFilter() {
        super();
    }

    /**
     * 过滤类型
     * pre: 可以在请求被路由之前调用
     * routing: 路由请求时被调用
     * post: 后路由过滤在routing和error过滤器之后被调用
     * error: 处理请求时发生错误时被调用
     *
     * @return
     */
    @Override
    public String filterType() {
        return "pre";
    }

    /**
     * 执行顺序
     * 通过int值来定义过滤器的执行顺序，数值越小优先级越高
     *
     * @return
     */
    @Override
    public int filterOrder() {
        return 5;
    }

    /**
     * 执行条件
     * 返回一个boolean值来判断该过滤器是否要执行。
     * 可以通过此方法来指定过滤器的有效范围
     *
     * @return
     */
    @Override
    public boolean shouldFilter() {
        RequestContext ctx = RequestContext.getCurrentContext();
        Object success = ctx.get("isSuccess");
        return success == null ? true : Boolean.parseBoolean(success.toString());
    }

    /**
     * 具体操作
     *
     * @return
     * @throws ZuulException
     */
    @Override
    public Object run() throws ZuulException {
        RequestContext ctx = RequestContext.getCurrentContext();
        ctx.addZuulRequestHeader("test", "test");
        return null;
    }
}
```


## Config
分布式配置管理

## Sleuth
服务跟踪 链路跟踪

### 集成Sleuth
```xml
<!--链路跟踪-->
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-sleuth</artifactId>
</dependency>
```
打印日志
[appname,traceId,spanId,exportable]组成
- appname: 服务名称(需要将spring.application.name的配置写在bootstrap.properties)
- traceId: 整个请求的唯一Id，标识整个请求的链路
- spanId: 基本工作单元，发起一次远程调用就是一个span
- exportable: 决定是否导入数据到Zipkin中

### ELK
Elasticsearch: 开源分布式搜索引擎
Logstash: 日志收集
kibana: web界面

### Json格式日志输出
json格式依赖
```xml
<dependency>
    <groupId>net.logstash.logback</groupId>
    <artifactId>logstash-logback-encoder</artifactId>
    <version>5.0</version>
    <exclusions>
        <exclusion>
            <groupId>ch.qos.logback</groupId>
            <artifactId>logback-core</artifactId>
        </exclusion>
    </exclusions>
</dependency>
```
logback-spring.xml
```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
    <include resource="org/springframework/boot/logging/logback/defaults.xml"/>
    ​
    <springProperty scope="context" name="springAppName" source="spring.application.name"/>
    <!-- Example for logging into the build folder of your project -->
    <property name="LOG_FILE" value="${BUILD_FOLDER:-build}/${springAppName}"/>​

    <!-- You can override this to have a custom pattern -->
    <property name="CONSOLE_LOG_PATTERN"
              value="%clr(%d{yyyy-MM-dd HH:mm:ss.SSS}){faint} %clr(${LOG_LEVEL_PATTERN:-%5p}) %clr(${PID:- }){magenta} %clr(---){faint} %clr([%15.15t]){faint} %clr(%-40.40logger{39}){cyan} %clr(:){faint} %m%n${LOG_EXCEPTION_CONVERSION_WORD:-%wEx}"/>

    <!-- Appender to log to console -->
    <appender name="console" class="ch.qos.logback.core.ConsoleAppender">
        <filter class="ch.qos.logback.classic.filter.ThresholdFilter">
            <!-- Minimum logging level to be presented in the console logs-->
            <level>DEBUG</level>
        </filter>
        <encoder>
            <pattern>${CONSOLE_LOG_PATTERN}</pattern>
            <charset>utf8</charset>
        </encoder>
    </appender>

    <!-- Appender to log to file -->​
    <appender name="flatfile" class="ch.qos.logback.core.rolling.RollingFileAppender">
        <file>${LOG_FILE}</file>
        <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
            <fileNamePattern>${LOG_FILE}.%d{yyyy-MM-dd}.gz</fileNamePattern>
            <maxHistory>7</maxHistory>
        </rollingPolicy>
        <encoder>
            <pattern>${CONSOLE_LOG_PATTERN}</pattern>
            <charset>utf8</charset>
        </encoder>
    </appender>
    ​
    <!-- Appender to log to file in a JSON format -->
    <appender name="logstash" class="ch.qos.logback.core.rolling.RollingFileAppender">
        <file>${LOG_FILE}.json</file>
        <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
            <fileNamePattern>${LOG_FILE}.json.%d{yyyy-MM-dd}.gz</fileNamePattern>
            <maxHistory>7</maxHistory>
        </rollingPolicy>
        <encoder class="net.logstash.logback.encoder.LoggingEventCompositeJsonEncoder">
            <providers>
                <timestamp>
                    <timeZone>UTC</timeZone>
                </timestamp>
                <pattern>
                    <pattern>
                        {
                        "severity": "%level",
                        "service": "${springAppName:-}",
                        "trace": "%X{X-B3-TraceId:-}",
                        "span": "%X{X-B3-SpanId:-}",
                        "parent": "%X{X-B3-ParentSpanId:-}",
                        "exportable": "%X{X-Span-Export:-}",
                        "pid": "${PID:-}",
                        "thread": "%thread",
                        "class": "%logger{40}",
                        "rest": "%message"
                        }
                    </pattern>
                </pattern>
            </providers>
        </encoder>
    </appender>
    ​
    <root level="INFO">
        <appender-ref ref="console"/>
        <!-- uncomment this to have also JSON logs -->
        <appender-ref ref="logstash"/>
        <appender-ref ref="flatfile"/>
    </root>
</configuration>
```

产生的日志文件在项目parent文件夹下的build文件夹中

### Zipkin
收集所有服务的监控数据的分布式跟踪系统，提供收集数据和查询数据两大接口

创建Zipkin Server
1. 自己新建项目
  ```xml
  <!--Zipkin-->
  <dependency>
      <groupId>io.zipkin.java</groupId>
      <artifactId>zipkin-server</artifactId>
  </dependency>
  <dependency>
      <groupId>io.zipkin.java</groupId>
      <artifactId>zipkin-autoconfigure-ui</artifactId>
  </dependency>
  ```
  启动类添加@EnableZipkinServer

  ```properties
  spring.application.name=zipkin-server
  server.prot=9411
  ```
  现成jar包
2. 项目集成Zipkin发送调用链数据
  ```xml
  <dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-zipkin</artifactId>
  </dependency>
  ```
  ```properties
  # 配置 zipkin Server地址
  spring.zipkin.base-url=http://127.0.0.1:9411
  ```
3. 抽样采集数据
  ```properties
  # zipkin 抽样比例
  spring.sleuth.sampler.percentage=1
  ```
4. 用RabbitMQ代替HTTP发送调用链数据
  发送
  ```xml
  <dependency>
      <groupId>org.springframework.cloud</groupId>
      <artifactId>spring-cloud-sleuth-zipkin-stream</artifactId>
  </dependency>
  <dependency>
      <groupId>org.springframework.cloud</groupId>
      <artifactId>spring-cloud-starter-stream-rabbit</artifactId>
  </dependency>
  ```
  启动类添加@EnableZipkinStreamServer注解
  ```properties
  spring.rabbitmq.addresses=amqp://192.168.10.47:5672
  spring.rabbitmq.username=exler
  spring.rabbitmq.password=123456
  ```
  需要跟踪的服务
    ```xml
  <dependency>
      <groupId>org.springframework.cloud</groupId>
      <artifactId>spring-cloud-sleuth-zipkin-stream</artifactId>
  </dependency>
  <dependency>
      <groupId>org.springframework.cloud</groupId>
      <artifactId>spring-cloud-starter-stream-rabbit</artifactId>
  </dependency>
  ```
  ```properties
  spring.rabbitmq.addresses=amqp://192.168.10.47:5672
  spring.rabbitmq.username=exler
  spring.rabbitmq.password=123456
  ```
  去掉spring.zipkin.base-url配置
5. 用ElasticSearch存储调用链数据
   ```xml
  <dependency>
      <groupId>io.zipkin.java</groupId>
      <artifactId>zipkin-autoconfigure-storage-elasticsearch-http</artifactId>
      <version>1.24.0</version>
      <optional>true</optional>
  </dependency>
  ```
  ```properties
  zipkin.storage.StorageComponent=elasticsearch
  zipkin.storage.type=elasticsearch
  zipkin.storage.elasticsearch.cluster=elasticsearch-zipkin-cluster
  zipkin.storage.hosts=127.0.0.1:9300
  zipkin.storage.elasticsearch.max-requests=64
  zipkin.storage.elasticsearch.index=zipkin
  zipkin.storage.elasticsearch.index-shards=5
  zipkin.storage.elasticsearch.index-replicas=1
  ```

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

### 快速移除已经失效的服务信息
服务下线后，服务信息还是一直存在于Eureka中
server
```yml
eureka:
  server:
    # 清理间隔
    eviction-interval-timer-in-ms: 5000
    # 关闭保护模式
    enable-self-preservation: false
```

client
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

```yml
# 默认30s 表示eureka发送心跳给server端的频率
lease-renewal-interval-in-seconds: 5
# 默认90s 表示Eureka Server至上一次收到client的心跳之后，等待下一次心跳的超时时间，在这个时间内若没有收到下一次心跳则移除该Instance
lease-expiration-duration-in-seconds: 5
```