数据结构/持久化/失效淘汰机制



#### Q：**Redis 持久化机制**

RDB：Redis默认的持久化方式。按照一定的时间周期策略把内存的数据以快照的形式保存到硬盘的二进制文件。
AOF：Redis会将每一个收到的写命令都通过Write函数追加到文件最后，类似于MySQL的binlog。当Redis重启是会通过重新执行文件中保存的写命令来在内存中重建整个数据库的内容。

#### Q：**缓存雪崩/缓存穿透/缓存击穿**

缓存雪崩给过期时间添加随机值

缓存穿透使用布隆过滤器

缓存击穿

#### Q：Redis中的事务

Redis 事务的本质是通过MULTI、EXEC、WATCH等一组命令的集合。事务支持一次执行多个命令，一个事务中所有命令都会被序列化。

使用的是乐观锁 WATCH命令会去监测版本号的改变

#### Q：支持的数据类型有哪些

**string**：**int、raw、embstr**

hash：**ziplist、hashtable**

list：ziplist、linkedlist

set：intset、hashtable

zset：ziplist、skiplist

#### Q：Redis 为什么这么快？

1. 完全基于内存 2. 采用单线程，避免了不必要的上下文切换和竞争条件 3. 使用多路 I/O 复用模型，非阻塞 IO

#### Q：怎么保证缓存和数据库数据的一致性？

1. 先删除缓存，后更新数据库
2. 先更新数据库，后删除缓存

#### Q：Redis 内存淘汰策略有哪些？

已设置过期时间/全部数据集  ｜ 最近最少使用/挑选将要过期/任意

#### Q：Redis的过期键的删除策略

定时过期/惰性过期/定期清除

#### Q：在生成 RDB 期间，Redis 可以同时处理写请求么

Redis 在持久化时会调用 glibc 的函数`fork`产生一个子进程，快照持久化完全交给子进程来处理，父进程继续处理客户端请求

#### Q：**Redis事务保证原子性吗，支持回滚吗**

Redis中，单条命令是原子性执行的，但事务不保证原子性，且没有回滚。事务中任意命令执行失败，其余的命令仍会被执行。

#### Q：**主从架构的核心原理**

slave node的时候，它会发送一个PSYNC命令给master node，判断是增量还是全复制

slave不会过期key，只会等待master过期key。如果master过期了一个key，或者通过LRU淘汰了一个key，那么会模拟一条del命令发送给slave。

#### Q：主从延迟导致读取到过期数据怎么处理

通过scan命令扫库然后利用Redis惰性删除的策略

#### Q：主从复制的过程中如果因为网络原因停止复制了会怎么样

master node会在内存中创建一个backlog，如果master和slave网络连接断掉了，slave会让master从上次的replica offset开始继续复制。

#### Q：同步配置的时候其他哨兵根据什么更新自己的配置呢

新的master配置是跟着新的version号的，其他的哨兵都是根据版本号的大小来更新自己的master配置的。

#### Q：Redis cluster节点间通信是什么机制？

Redis cluster节点间采取gossip协议进行通信，所有节点都持有一份元数据，不同的节点如果出现了元数据的变更，Redis cluster节点间采取gossip协议进行通信，所有节点都持有一份元数据，不同的节点如果出现了元数据的变更







