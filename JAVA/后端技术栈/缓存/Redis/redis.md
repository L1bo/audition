[TOC]

# 基础

## redis 五大数据结构
**==redis里面 存放的数据类型只有byte,redis有5种数据结构==**

1. string(字符串)
2. hash(哈希): 键值对 key field value
3. list(列表)
4. set(集合) 
5. zset(sorted set：有序集合)

## redis 的淘汰策略 (六种淘汰策略)
- noeviction: 不删除策略, 达到最大内存限制时, 如果需要更多内存, 直接返回错误信息。 大多数写命令都会导致占用更多的内存(有极少数会例外, 如 DEL )。
- allkeys-lru: 所有key通用; 优先删除最近最少使用(less recently used ,LRU) 的 key。
- volatile-lru: 只限于设置了 expire 的部分; 优先删除最近最少使用(less recently used ,LRU) 的 key。
- allkeys-random: 所有key通用; 随机删除一部分 key。
- volatile-random: 只限于设置了 expire 的部分; 随机删除一部分 key。
- volatile-ttl: 只限于设置了 expire 的部分; 优先删除剩余时间(time to live,TTL) 短的key。

## 两种持久化形式
1. RDB快照(snapshotting)
2. AOF(append-only-file)

### RDB
把当前内存中的数据集快照写入磁盘，也就是 Snapshot 快照（数据库中所有键值对数据）。恢复时是将快照文件直接读到内存里
1. 自动触发
    redis.conf 配置文件中的 SNAPSHOTTING
    save：这里是用来配置触发 Redis的 RDB 持久化条件，也就是什么时候将内存中的数据保存到硬盘。比如“save m n”。表示m秒内数据集存在n次修改时，自动触发bgsave（这个命令下面会介绍，手动触发RDB持久化的命令）

　　默认如下配置：
    ```
    save 900 1：表示900 秒内如果至少有 1 个 key 的值变化，则保存
    save 300 10：表示300 秒内如果至少有 10 个 key 的值变化，则保存
    save 60 10000：表示60 秒内如果至少有 10000 个 key 的值变化，则保存
    ```
　　当然如果你只是用Redis的缓存功能，不需要持久化，那么你可以注释掉所有的 save 行来停用保存功能。可以直接一个空字符串来实现停用：save ""
　　stop-writes-on-bgsave-error ：默认值为yes。当启用了RDB且最后一次后台保存数据失败，Redis是否停止接收数据。这会让用户意识到数据没有正确持久化到磁盘上，否则没有人会注意到灾难（disaster）发生了。如果Redis重启了，那么又可以重新开始接收数据了

　　rdbcompression ；默认值是yes。对于存储到磁盘中的快照，可以设置是否进行压缩存储。如果是的话，redis会采用LZF算法进行压缩。如果你不想消耗CPU来进行压缩的话，可以设置为关闭此功能，但是存储在磁盘上的快照会比较大。

　　rdbchecksum ：默认值是yes。在存储快照后，我们还可以让redis使用CRC64算法来进行数据校验，但是这样做会增加大约10%的性能消耗，如果希望获取到最大的性能提升，可以关闭此功能。

　　dbfilename ：设置快照的文件名，默认是 dump.rdb

　　dir：设置快照文件的存放路径，这个配置项一定是个目录，而不能是文件名。默认是和当前配置文件保存在同一目录。

　　也就是说通过在配置文件中配置的 save 方式，当实际操作满足该配置形式时就会进行 RDB 持久化，将当前的内存快照保存在 dir 配置的目录中，文件名由配置的 dbfilename 决定。

2. 手动触发
    手动触发Redis进行RDB持久化的命令有两种：
　　1. save
　　该命令会阻塞当前Redis服务器，执行save命令期间，Redis不能处理其他命令，直到RDB过程完成为止。
　　显然该命令对于内存比较大的实例会造成长时间阻塞，这是致命的缺陷，为了解决此问题，Redis提供了第二种方式。

　　2. bgsave
　　执行该命令时，Redis会在后台异步进行快照操作，快照同时还可以响应客户端请求。具体操作是Redis进程执行fork操作创建子进程，RDB持久化过程由子进程负责，完成后自动结束。阻塞只发生在fork阶段，一般时间很短。
　　基本上 Redis 内部所有的RDB操作都是采用 bgsave 命令。
　　ps:执行执行 flushall 命令，也会产生dump.rdb文件，但里面是空的，无意义


# Java

## RedisTemplate
1. redisTemplate 使用JDK默认的序列化，new JdkSerializationRedisSerializer，会导致k/v乱码
2. 使用StringRedisSerializer
```java
@Bean
public RedisTemplate<String, String> redisTemplate(RedisConnectionFactory redisConnectionFactory) {
    RedisTemplate<String, String> redis = new RedisTemplate<>();
    redis.setConnectionFactory(redisConnectionFactory);

    // 设置redis的String/Value的默认序列化
    StringRedisSerializer stringRedisSerializer = new StringRedisSerializer();
    redis.setKeySerializer(stringRedisSerializer);
    redis.setValueSerializer(stringRedisSerializer);
    redis.setHashKeySerializer(stringRedisSerializer);
    redis.setHashValueSerializer(stringRedisSerializer);

    redis.afterPropertiesSet();
    return redis;
}
```