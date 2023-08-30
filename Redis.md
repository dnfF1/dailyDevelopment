# Redis
## Redis基础
- Redis是key-value非关系缓存数据库
### 优点：
- 读写快
- 支持数据持久化，RDB和AOF
- 支持事务
- 数据结构丰富
- 支持主从复制
### 缺点：
- 容量受到内存大小限制
- 不具备自动容错和恢复功能
- 主机宕机后会导致部分数据不一致
- 无法在线扩容
### 为什么要使用redis（redis能干嘛）
- 高性能：数据缓存在内存，不用读硬盘
- 高并发：采用多路复用epoll，处理并发的连接
## Redis为什么快
- 基于内存
- 单线程，不存在多线程间切换，节省上下文切换线程时间
## Redis应用场景
- 计数器：对value值+1，进行ip限制访问次数
- 热点数据缓存：
- 会话缓存：session半小时过期
- 分布式锁：setnx、redlock
- set的交集和并集，实现共同好友，zset的有序，实现排行榜
## Redis持久化（推荐：同时使用RDB和AOF）
### RDB（Redis DataBase）
- 默认的持久化方式
- 3600/1，600/100，60/10000的时间将内存的数据以dump.rdb文件格式保存
- save/bgsave命令持久化
- fork子进程进行写操作，主线程没有IO操作，IO最大化
- 数据安全性低
### AOF（Append-only file）
- 将所有写命令记录到单独的日志文件
- 优先选择AOF恢复数据
- 文件大，恢复速度慢，恢复数据全
## Redis事务
- 事务支持一次执行多个命令，事务中的命令都会序列化，按照顺序执行序列化的命令，其他客户端提交的命令不会插入到事务执行命令序列中
- Redis事务：一次性、顺序性、排他性的执行一个队列中的一系列命令
- Redis事务只支持ACID的一致性和隔离性
- Redis不保证事务的原子性，且没有回滚，事务中任意命令执行失败，其余命令仍会被执行
### Redis事务原语
- MULTI：开启一个事务，客户端可以发送任意多条命令，命令不会立即执行，而是放入一个队列
- EXEC：按顺序执行队列中的命令，当操作被打断时，返回nil
- DISCARD：清空队列中的命令，放弃执行事务，客户端从事务状态退出
- WATCH：乐观锁，CAS监控key，有key被修改或删除，之后的事务不会执行
## Redis的数据结构及实现
### String
- 字符串
- 简单的键值对缓存
### Hash
- key-value映射表
- 结构化的对象
### List
- 双向链表实现的列表
- 粉丝列表、评论列表
### Set
- 哈希表实现的不可重复的无序集合
- 并集、交集操作，两个用户的粉丝列表作交集
### Zset
- 哈希表实现的不可重复的有序集合
- 去重并排序，用户排行榜
## Redis集群方式
### 主从架构（高性能）
- master负责写，将数据复制到slave
- slave负责读，水平扩容，支撑读高并发
![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/4/14/171744945c7745d8~tplv-t2oaga2asx-jj-mark:3024:0:0:0:q75.avis)
- master宕机后，需要手动选择slave成为master，并手动修改client新的master节点地址
- 主从复制流程
> slave连接master，发送SYNC/PSYNC命令，进行全量复制

> master接收到SYNC命令后，在后台保存RDB快照，并缓存生成快照期间接收到的写命令

> 快照生成完毕，master将RDB快照发送给slave，并缓存发送快照期间接收到的写命令

> 将缓存的写命令同步给slave

> 增量同步写命令
![](https://img-blog.csdnimg.cn/20200620204750496.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1Nla3lfZmVp,size_16,color_FFFFFF,t_70)
- SYNC和PSYNC区别
> Redis2.8版本之前，都是SYNC命令

> SYNC：不管第一次还是断开重连，都是全量复制

> PSYNC：只有第一次是全量复制，断开重连是增量复制

> PSYNC增加了runid、offset和复制挤压缓冲区

> PSYNC每次连接，master判断runid和自身id是否一致，一致则说明是断开重连
### 哨兵模式（高可用）
- 流程和主从架构一样
- 哨兵用来监控master和slave的运行状态
- master节点宕机后，通过哨兵投票选举，选择slave节点成为新的master
- 通知client新的master地址
### Redis Cluster
- 多个Node，每个Node有一个master和多个slave
- 每个Node存储一部分数据（哈希槽 hash slot）
- 默认有16384个hash slot
- 每个节点都有所有节点的区间信息，请求访问任意节点，都可以转发到正确的节点
- 每个key通过CRC16校验后对16384取模
- 集群不保证强一致性，只保证最终一致性
## setNx
### 定义
- set if not exists
- 执行：set key value nx px 3000
- 设置key，若key存在，则设置失败，即获取锁失败
- 设置key时，为key设置合理的过期时间
- 通过del方式释放锁
![](https://pic4.zhimg.com/80/v2-40b91743ad688971b79585fe02957cab_1440w.webp)
### 缺点
- A获取锁，过期时间到了，B获取锁，A执行完毕删除锁，B执行完毕蒙蔽【对key的value为uuid，释放锁时先判断uuid是否相同】
```java
String uuid = 123456;
// 获取锁
try {
    // 执行操作
} finally {
    if (uuid.equal(get(key))) {
        del(key);
    }
}
```
- 上述get和del都不是原子操作，正确方式是写lua脚本
```lua
-- lua释放锁
-- KEYS、ARGV分别是以集合方式传入的参数，对应上文的key和uuid
-- 如果对应的value等于传入的uuid
if redis.call("get", KEYS[1]) == ARGV[1] then
    return redis.call("del", KEYS[1])
else
    return 0
end
```
- lua脚本是通过redis的eval或evalsha命令执行
## Redisson
### 定义
- Redisson是java的Redis客户端
- 支持锁、主从架构、哨兵模式、集群模式
- 通过RedissonLock类实现锁
- 支持锁重入
```java
RedissonLock client = getClient();
RLock lock = client.getLock(key);
lock.lock();
try {
    // 执行操作
} finally {
    lock.unlock();
}
```
- 锁超时，watchdog
- 扩容：新主节点，每个现有主节点转移部分槽到新主节点，最终达到所有主节点的槽数量相等【存储节点的设备信息一样，不一样的话，大的放多点，小的放少点】
- 简单哈希：key对存储节点数取模，节点数量变化，大量缓存失效，10 % 3 = 1, 10 % 4 = 2,则key=10的缓存失效
- 一致性哈希【哈希环】：先对存储节点进行哈希计算，存储节点绕成一个环，再对key进行哈希计算，计算结果按照顺时针找到第一个存储节点，增加存储节点时，一样对存储节点进行哈希计算，放在环所对应的计算结果的位置上，按逆时针到最近的存储节点之间的key放到新的存储节点，哈希环的缺点是容易造成数据倾斜，存储节点位置分布不均匀时，会导致部分存储节点的key过多
- 哈希槽：固定16384个槽位
- redis不会自动进行槽位的转移，需要人工配置
- cluster高可用依赖于节点的主从复制
## RedLock
![](https://pic2.zhimg.com/80/v2-f15ffb214eec73c934959c7b69492eb9_1440w.webp)
![](https://pic2.zhimg.com/80/v2-55f22fa1e98cf2f100cdfa3da93e8ec1_1440w.webp)
### 定义
- redLock是一种分布式锁的算法
- 共N个节点，N/2+1个节点获取锁成功，则获取锁成功，解锁时对所有节点解锁
### 流程
- 顺序向N个节点获取锁
- 根据一定的超时时间判断是否跳过该节点
- N/2+1个节点成功获取锁，并且花费时间小于锁的过期时间
- 获取锁成功，锁的有效期 = 锁的过期时间 - N/2+1个锁的花费时间 - 等待的超时时间
## 缓存雪崩
### 定义
- 同一时间，大量key过期，请求全部访问数据库
### 解决方案
- 每个key设置不同的过期时间：相同时间+随机时间，过期时间分散
- 缓存预热，提前走流程，提前将数据设置在缓存，并设置不同的过期时间
- redis集群，读命令分配给各个节点
## 缓存穿透
### 定义
- 缓存和数据库都不存在的key访问，永远会访问数据库
### 解决方案
- 设置key-null，防止同一个key持续访问
- 接口层校验，尽可能避免非法key的访问
- 布隆过滤器，将所有可能出现的key哈希到一个足够大的bitmap，通过过滤器拦截，避免直接访问数据库
## 缓存击穿
### 定义
- 同一时间，同一个key的大量请求，key过期，请求全部访问数据库
### 解决方案
- 热点数据，设置key永不过期，persist命令
- 对热点数据定时更新过期时间
- 对热点数据加互斥锁
## 缓存预热
## 缓存降级
- 缓存降级是为了在Redis服务故障时，保证核心服务可用
- Redis发生故障，不访问数据库，直接返回默认值