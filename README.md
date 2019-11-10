### Redis



[Redis的一些基础知识](./REDISBASE.md)

[Redis中的bitmap(位图)](./BITMAP.md)

[Redis中的Hash](./HASH.md)

[Redis中的List](./LIST.md)

[Redis中的Set](./SET.md)

[Redis中的ZSet](./ZSet.md)



> 数据结构的总结

| 对象         | 对象type属性值 | type命令输出 | 底层可能的存储结构                                           | object encoding               |
| ------------ | -------------- | ------------ | ------------------------------------------------------------ | ----------------------------- |
| 字符串对象   | OBJ_STRING     | "string"     | OBJ_ENCODING_INT<br />OBJ_ENCODING_EMBSTR<br />OBJ_ENCODING_RAW | int<br />embstr<br />raw      |
| 列表对象     | OBJ_LIST       | "list        | OBJ_ENCODING_QUICKLIST                                       | quicklist                     |
| 哈希对象     | OBJ_HASH       | "hash"       | OBJ_ENCODING_ZIPLIST<br />OBJ_ENCODING_HT                    | ziplist<br />hashtable        |
| 集合对象     | OBJ_SET        | "set"        | OBJ_ENCODING_INSET<br />OBJ_ENCODING_HT                      | intset<br />hashtable         |
| 有序集合对象 | OBJ_ZSET       | "zset"       | OBJ_ENCODING_ZIPLIST<br />OBJ_ENCODING_SKIPLIST              | ziplist<br />skiplist(包含ht) |

> 编码转换总结

* 字符串对象
  * 原始编码  int 整数并且小于 long 2^63-1
  * 超过44字节，被修改 升级为 embtr
  * raw为最后的方案，不连续的存储方式。
* 哈希对象
  * 键值长度小于64bytes，键值对个数小于512 ，这两个条件同时满足，为ziplist
  * 否则升级为hashtable
* 列表对象
  * quicklist （双向链表，具体存储为ziplist）
* 集合对象
  * intset，元素都是整型，元素个数小于512个，同时满足
  * 否则升级为hashtable
* 有序集合对象
  * 元素数量不超过 128 个，任何一个 member 的长度小于 64 字节，同时满足。ziplist
  * 否则升级为skiplist

----



> 应用场景总结
>
> 缓存——提升热点数据的访问速度
> 共享数据——数据的存储和共享的问题
> 全局 ID —— 分布式全局 ID 的生成方案（分库分表）
> 分布式锁——进程间共享数据的原子操作保证
> 在线用户统计和计数
> 队列、栈——跨进程的队列/栈
> 消息队列——异步解耦的消息机制
> 服务注册与发现 —— RPC 通信机制的服务协调中心（Dubbo 支持 Redis）
> 购物车
> 新浪/Twitter 用户消息时间线
> 抽奖逻辑（礼物、转发）
> 点赞、签到、打卡
> 商品标签
> 用户（商品）关注（推荐）模型
> 电商产品筛选
> 排行榜