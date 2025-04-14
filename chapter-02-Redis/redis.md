# Redis
> Redis 是一种基于内存的数据库，对数据的读写操作都是在内存中完成，因此读写速度非常快，常用于缓存，消息队列、分布式锁等场景。

Redis 提供了多种数据类型来支持不同的业务场景，比如 String（字符串）、Hash（哈希）、 List（列表）、Set（集合）、Zset（有序集合）、Bitmaps（位图）、HyperLogLog（基数统计）、GEO（地理信息）、Stream（流），并且对数据类型的操作都是原子性的，因为执行命令由单线程负责的，不存在并发竞争的问题。

除此之外，Redis 还支持事务 、持久化、Lua 脚本、多种集群方案（主从复制模式、哨兵模式、切片集群模式）、发布/订阅模式，内存淘汰机制、过期删除机制等等。

**为什么用 Redis 作为 MySQL 的缓存？**

因为 Redis 具有`高性能`和`高并发`两种特性
- 高性能

用户不需要从硬盘中读取 MySQL 的数据，而是直接从内存中读取 Redis 缓存的数据即可。
- 高并发

单台设备的 Redis 的 QPS（Query Per Second，每秒钟处理完请求的次数）是 MySQL 的 10 倍。

[一文讲透Redis分布式锁安全问题](https://dev-brands.cnblogs.com/tencentcloud/p/16709)
