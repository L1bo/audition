[TOC]

# Spring Boot
## 为什么要用 Spring Boot？
- 配置简单
- 独立运行
- 自动装配
- 无代码生成和 xml 配置
- 提供应用监控
- 易上手
- 提升开发效率

## spring boot 核心配置文件是什么？
spring boot 核心的两个配置文件：

bootstrap (. yml 或者 . properties)：boostrap 由父 ApplicationContext 加载的，比 applicaton 优先加载，且 boostrap 里面的属性不能被覆盖；
application (. yml 或者 . properties)：用于 spring boot 项目的自动化配置。

## spring boot 配置文件有哪几种类型？它们有什么区别？
配置文件有 . properties 格式和 . yml 格式，它们主要的区别是书法风格不同。

. properties 配置如下：
```properties
spring. RabbitMQ. port=5672
```
. yml 配置如下：
```yml
spring:
    RabbitMQ:
        port: 5672
```
. yml 格式不支持 @PropertySource 注解导入。

## spring boot 有哪些方式可以实现热部署？
使用 devtools 启动热部署，添加 devtools 库，在配置文件中把 spring. devtools. restart. enabled 设置为 true；
使用 Intellij Idea 编辑器，勾上自动编译或手动重新编译。

## jpa 和 hibernate 有什么区别？
jpa 全称 Java Persistence API，是 Java 持久化接口规范，hibernate 属于 jpa 的具体实现。

# 配置文件

## properties yml 优先级
properties 高

# 注解

## @SpringBootApplication 注解
是一个组合注解
- @EnableAutoConfiguration
- @ComponentScan
- @SpringBootConfiguration

入口类需要放置在包的最外层，以便能够扫描到所有子包中的类

## @SpringBootTest注解
可以引用入口类的配置

# spring-boot-starter

## spring-boot-starter-data-jpa

### Repository Bean是如何创建的
JpaRepositoriesRegistrar
- 激活了@EnableJpaRepositories
- 返回了JpaRepositoriesConfigExtension
RepositoryBeanDefinitionRegistrarSupport.registerBeanDefinitions
- 注册 Repository Bean (类型是 JpaRepositoryFactoryBean)
RepositoryConfigurationExtensionSupport.getRepositoryConfigurations
- 取得 Repository 配置
JpaRepositoryFactory.getTargetRepository
- 创建了 Repository

### 接口中的方法是如何被解释的
RepositoryFactorySupport.getRepository 添加了Advice
- DefaultMethodInvokingMethodInterceptor
- QueryExecutorMethodInterceptor
AbstractJpaQuery.execute 执行具体的查询
语法解析在 Part 中

# 连接池

## HikariCP

### HikariCP为什么快
1. 字节码级别的优化(很多方法 JavaAssist 生成)
2. 大量小改进
   1. 用FastStatementList代替ArrayList
   2. 无锁集合 ConcurrentBag
   3. 代理类的优化(比如，用invokestatic代替了invokevirtual)