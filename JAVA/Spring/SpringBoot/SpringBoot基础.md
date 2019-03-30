[TOC]

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