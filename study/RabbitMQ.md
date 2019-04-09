[TOC]
# 安装

# 代码使用(SpringBoot版本)
## 

## 为什么要使用消息队列
先进先出
- 异步 批量数据的异步处理
- 解耦 串行任务，并行化
- 削峰 高负载任务的负载均衡

Publish Subscirbe

1. 实现异步
2. 实现解耦
3. 流量削峰
## RabbitMQ工作模型详解
AMQP 高级消息队列协议
Broker 中介 存储消息 转发消息
TCP长连接
虚拟连接(消息信道)
Channel API
Queue 存储消息 先进先出 独立运行的进程，数据库
Exchange 交换机 地址列表 消息路由 绑定 Queue

消息的灵活路由
Virtual Host 用户分配虚拟机权限
guset

### Direct Exchange直连
与队列绑定在一起的时候指定**精准**关键字
binding key 精确的绑定关键字
routing key 路由关键字
channel.basicPublish("DIRECT_EXCHANGE", "spring","msg 1");

### Topic Exchange 主题
binding key 
> # 0个或者多个单词
> * 不多不少一个单词

### Fanout Exchange 广播

## Spring Boot集成RabbitMQ
amqp
@Bean
Queue

## 消息可靠性投递分析与解决方案

服务端确认-事务模式
确认模式


自动 autoAck
ACK是什么时候发送的？
消费者接受消息的时候
手动 Ack
finally块
channel.basicAck()

补偿机制
    重发消息 控制次数 计数器
    对账
消息的幂等性
    流水号 唯一 重账控制
    msgId BizId