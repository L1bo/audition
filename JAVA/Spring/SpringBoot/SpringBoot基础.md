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
