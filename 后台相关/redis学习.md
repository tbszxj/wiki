# redis学习

教程链接：https://www.bilibili.com/video/BV1CJ411m7Gc?p=2

## redis简介

Redis是一个开源的使用ANSI C语言编写、遵守BSD协议、支持网络、可基于内存亦可持久化的日志型、Key-Value数据库，并提供多种语言的API、

主要应用：

* 加速热点数据查询
* 任务队列
* 即时信息查询
* 消息队列
* 分布式锁

## 基本操作

### 功能性命令

```shell
# 设置键值对
set key value
#获取键对应值
get key
# 删除
del key
# 清屏
claer
# 帮助
help 命令名
help @组名
```

## key命名规范

redis的key一般要有一些命名规范，(一般：表名:主键名:主键值:字段名)例如

* user:id:123456:fans
* user:id:123456:blogs
* user:id:123456:focuss

## 数据类型

* string
* hash
* list
* set
* sorted_set

### string类型

常用命令：https://www.runoob.com/redis/redis-strings.html

**string应用场景**

1. 数据库分表主键自增

   ```shell
   # string 扩展对数值增加和减少，redis单线程保证主键不会冲突
   incr key #默认加1
   decr key #默认减1
   ```

2. 设置生命周期，保持一种时效性数据（例如某段时间某个人仅允许操作一次）

   ```shell
   # 为key设置某个值，指定存活时间
   setex key seconds value
   psetex key milliseconds value
   ```

### hash类型

常用命令：https://www.runoob.com/redis/redis-hashes.html

* 新的存储需求：对一系列存储的数据进行编组，方便管理，典型的应用存储对象信息
* 需要的存储结构：一个存储空间保存多个键值对数据
* hash类型：底层使用哈希表结构实现数据存储

**hash类型数据基本操作**

```shell
# 添加/修改数据
hset key field value
# 获取数据
hget key field
hgetall key
# 删除数据
hdel key field1 [field2]
# 获取hash表中是否存在指定的字段
hexists key field
```

**hash扩展操作**

1. 获取hash表中所有的字段名或字段值

   ```shell
   hkeys key
   hvals key
   ```

2. 设置指定字段的数值数据增加指定范围的值

   ```shell
   hincrby key field increment
   hincrbyfloat key field increment
   ```

**hash使用主要事项**

* hash类型下的value只能存储字符串，不允许存储其他数据类型，不存在嵌套现象。如果数据未取到对应值未nil。
* 每个hash可以存储2<sup>32</sup> -1个键值对
* hash类型十分贴近对象的数据存储形式，并且可以灵活添加删除对象属性。但是hash设计初衷不是为了存储大量对象而设计的，不可滥用，更不要将hash作为对象列表使用
* hgetall 操作可以获取全部属性，如果内部field过多，遍历整体数据效率就会很低，有可能成为数据访问瓶颈

**hash应用场景**

1. 实现购物车

   客户id作为key，商品编号作为field

   ```shell
   # 001客户购买g01商品100件 g02商品20件
   hmset 001 g01:nums 100 g02:nums 20
   ```

2. 抢购

   以商家id作为key，将参与抢购的商品id作为field，将参与抢购的商品的数量作为对应的value，抢购时使用降值控制商品数量

   ```shell
   hmset p01 c30 1000 c50 1000 c100 1000
   
   hincrby p01 c30 -1
   
   hincrby p01 c50 -30
   
   hgetall p01
   ```


### list类型

* 数据存储需求：存储多个数据，并对数据进行存储空间顺序进行区分
* 需要的存储结构：一个存储空间保存多个数据，且通过数据可以体现进入顺序
* list类型：保存多个数据，底层使用双向链表存储结构实现

**list的基本操作**

```shell
# 添加/修改数据
lpush key vaule [value2] ...
rpush key vaule [value2] ...
# 获取数据
lrange key start stop
lindex key index
len key
# 获取并移除数据
lpop key
rpop key
```

**扩展操作**

* 规定时间内获取并移除数据(阻塞队列，没有数据就等待，超时停止)

  ```shell
  blpop key1 [key2] timeout
  brpop key1 [key2] timeout
  ```

* 移除指定数据

  ```shell
  lrem key count value
  ```


**list应用场景**

1. 微博中个人用户关注列表按用户关注顺序进行展示

   依赖list的数据具有数据的特征对数据进行管理

   使用队列模型解决多路信息汇总合并的问题

   使用栈模型解决最新消息的问题

### set类型

* 存储大量数据在查询方面提供更高的效率
* 能够保存大量的数据，高效的内部存储机制，便于查询

**基本操作**

```shell
# 添加数据
sadd key member1 [member2]
# 获取全部数据
smember key
# 删除数据
srem key member1 [member2]

# 获取集合数据的总量
scard key
# 判断集合中是否包含指定数据
sismember key member
```

**业务场景**

* 从热门类型中随机选一个进行推荐

  解决办法：

  随机获取集合在指定数量的数据（推荐后留存）

  ```shell
  srandmember key [count]
  ```

  随机获取集合中的某个数据并将该数据迁移出集合（推荐后移除）

  ```shell
  spop key
  ```

* 求集合的交、并、差集

  ```shell
  sinter key1 [key2]
  sunit key1 [key2]
  sdiff key1 [key2]
  ```

* 求集合的交、并、差集并存储到指定的集合中

  ```shell
  sinterstore destination key1 [key2]
  sunionstore destination key1 [key2]
  sdiffstore destination key1 [key2]
  ```

* 将指定数据从原始集合中移动到目标集合中

  ```shell
  smove source destiontion member
  ```


统计网站的访问量，统计网站的PV(访问量) UV(独立访客) IP(独立IP)

* 利用set集合的数据去重特征记录各种访问的数据
* 建立string类型数据，利用incr统计日访问量（PV）
* 建立set模型，记录不同cookie数量（UV）
* 建立set模型，记录不同IP数量（IP）

### sorted_set类型

 需求：数据排序有利于数据的展示效果，需要提供一种可以根据自身特征进行排序的方式

存储结构：新的存储模型，可以保存可排序的数据

sorted_set类型：在set的存储结构基础上添加可排序的字段

**基本操作**

```shell
# 添加数据
zadd key score1 member1 [score2 member2]

# 获取全部数据
zrange key start stop [WITHSCORES]
zrevrange key start stop [WITHSCORES]

# 删除数据
zrem key member [member ...]
```

**扩展操作**

* 按条件获取数据

  ```shell
  # 获取值在从min到max的数据
  zrangebyscore key min max [WITHSCORE] [LIMIT]
  # 反向获取从max到min的数据
  zrevrangebyscore key max min [WITHSCORE]
  ```

* 条件删除数据

  ```shell
  # 按索引删除，从start 到 stop
  zremrangebyrank key start stop
  # 删除从min到max的值
  zremrangebyscore key min max
  ```

* 获取集合数据总量

  ```shell
  zcard key
  
  zcount key min max
  ```

* 集合交、并操作、

  ```shell
  zinterstore destination numkeys key [key...]
  zunionstore destination numkeys key [key...]
  ```


**应用场景**

* 为所有参与排名的资源建立排序依据

  **解决方案**

  * 获取数据对应的索引（排名）

    ```shell
    zrank key member
    zrevrank key member
    ```

  * score值获取与修改

    ```shell
    zscore key member
    zincrby key increment member
    ```

* 任务/消息权重设定应用

  当任务或消息等待处理，形成了任务队列或消息队列时，对应高优先级的任务要保证对其优先处理，如何实现任务权重管理

  **解决方案**

  * 对于带有权重的任务，优先处理权重高的任务，采用score记录权重

## 通用指令

**key相关通用命令**

* 基本操作

  ```shell
  # 删除指定key
  del key
  # 获取key是否存在
  exists key
  # 获取key的类型
  type key
  ```

* 时效性操作

  ```shell
  # 为指定key设置有效期
  expire key seconds
  
  pexpire key milliseconds
  
  expireat key timestamp
  
  pexpireat key milliseconds-timestamp
  
  # 获取key的有效时间
  ttl key
  pttl key
  
  # 切换key从时效性转换为永久性
  persist key
  ```

* 查询操作

  ```shell
  keys pattern
  
  # keys * 查询所有
  # keys it* 查询所有以it开头
  # keys *name 查询所有以name结尾的
  # keys ??name 查询所有前面两个字符任意，后面以name结尾
  # keys user:? 查询所有user:开头，最后一个字符任意
  # keys u[st]er:1 查询所有以u开头，以er:1结尾，中间包含一个字母，s或t
  ```

* 其他操作

  ```shell
  # 为key改名
  rename key newkey
  renamenx key newkey
  # 对所有key排序
  sort
  # 其他key通用操作
  help @generic
  ```

**数据库相关通用命令**

* 基本操作

  ```shell
  select index
  ```

* 其他操作

  ```shell
  quit
  
  ping
  
  echo message
  ```

* db相关操作

  ```shell
  move key db
  ```

* 数据清除

  ```shell
  # 查看数据总量
  dbsize
  
  # 清除某个库下的数据
  flushdb
  
  # 清除全部数据库的全部数据
  flushall
  ```

  