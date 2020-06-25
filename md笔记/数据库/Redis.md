# Redis

## 1、NoSQL

(Not Only SQL)

- 泛指非关系型数据库
- 这些类型的数据存储不需要固定的模式，无需多余的操作就可以横向扩展

**优点**：

- 易扩展
- 大数据量高性能
- 多样灵活的数据模型

**3V和3高：**

- 大数据时代的3V:
  - 海量Volume
  - 多样Variety
  - 实时Velocity
- 互联网需求的三高：
  - 高并发
  - 高可扩
  - 高性能

**常见Nosql数据库**

- Redis
- memcache
- HBase
- MongoDB

![image-20200625102100073](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200625102100073.png)

## 2、Redis概述

**概念**：Redis(REmote Dictionary Server)是用C语言开发的一个开源的高性能键值对（key-value)数据库。

**特征**：

1. 数据间没有必然的关联关系
2. 内部采用单线程机制进行工作
3. 高性能
4. 多数据类型支持
   - 字符串类型
   - 列表类型
   - 有序集合类型
   - 散列类型（哈希）
   - 集合类型
5. 持久化支持。可以进行数据灾难恢复 

**应用：**

- 为热点数据加速查询（主要场景），如热点商品、热点新闻、热点资讯、推广类等高访问量信息等
- 任务队列，如秒杀、抢购、购票队列等
- 即时信息查询，如各位排行榜、各类网站访问统计、公交到站信息、在线人数信息（聊天记录、网站）、设备信号等
- 时效性信息控制，如验证码控制、投票控制等
- 分布式数据共享，如分布式集群架构中的session分离
- 消息队列
- 分布式锁

## 3、基本操作

**信息添加**

- 功能：设置key、value数据

- 命令

  ```
  set key value
  ```

**信息查询**

- 功能：根据key查询对应的value,如果不存在，返回空（nill）

- 命令

  ```
  get key
  ```

**清除屏幕信息**

- 命令

  ```
  clear
  ```

**帮助**

- 功能：获取命令帮助

- 命令

  ```
  help 命令名
  help @组名（或者help+空格+tab+tab...)
  ```

退出

- 命令

  ```
  quit 
  exit
  <esc>
  ```

## 4、数据类型

- string
- hash
- list 
- set
- sorted_set

### 4.1 string类型

**redis数据存储类型**

- redis自身是一个map，其中所有的数据都是采用key:value的形式存储
- 数据类型指的是存储的数据的类型，也就是value部分的类型，key部分永远都是字符串。

**string 类型**

- 存储的数据：单个数据，最简单的数据存储类型，也是最常用的数据存储类型
- 存储数据的格式：一个存储空间保存一个数据
- 存储内容：通常使用字符串，如果字符串以整数的形式展示，可以作为数字操作使用（本质还是字符串）

**基本操作**

- 添加、修改数据

  ```
  set key value
  ```

- 获取数据

  ```
  get key
  ```

- 删除数据

  ```
  del key
  ```

- 添加/修改多个数据

  ```
  mset key1 value1 key2 value2 ...
  ```

- 获取多个数据

  ```
  mget key1 key2 ...
  ```

- 获取数据字符个数（字符串长度）

  ```
  strlen key
  ```

- 追加信息到原始信息后部（如果原信息存在就追加，否则新建）

  ```
  append key value
  ```


- 设置数值数据增加指定范围的值

  ```
  incr key (每次加1)
  incrby key increment  (每次增加increment)
  incrbyfloat key increament
  ```

- 设置数值类型减少指定范围的值

  ```
  decr key 
  decrby key increment  
  ```

- string在redis内部存储默认是一个字符串，当遇到增减类操作incr、decr会转成数值型进行计算。

- 按数值进行操作的数据，如果原始数据不能转成数值，或超越了redis数值上限范围，将报错。

- 设置数据具有指定的生命周期

  ```
  setex key seconds value
  setex key millseconds value
  ```

**注意事项**

- 数据操作不成功的反馈与数据正常操作之间的差异
  - 表示运行结果是否成功
    - （integer) 0 -> false
    - （integer) 1 -> true
  - 表示运行结果值
    - （integer) 3 -> 3个
    - （integer) 1 -> 1个

**应用场景**

- 在redis中为大V用户设定用户信息，以用户主键和属性值作为key，后台设定定时刷新策略即可。

  ```
  set user:id:01:fans 123
  get user:id:01:fans
  ```

### 4.2 hash类型

- 一个存储空间保存多个键值对数据
- hash类型：底层使用哈希表结果实现数据类型

- 添加/修改数据

  ```
  hset key field value
  ```

- 获取数据

  ```
  hget key field
  hgetall key
  ```

- 删除数据

  ```
  hdel key field1 [field2]
  ```

- 添加/修改多个数据

  ```
  hmset key field1 field2 ...
  ```

- 获取多个数据

  ```
  hmget key field1 field2 ...
  ```

- 获取哈希表中字段的数据

  ```
  hlen key
  ```

- 获取哈希表中是否存在制定的字段

  ```
  hexists key field
  ```

- 获取哈希表中所有的字段名或字段值

  ```
  hkeys key
  hvals key
  ```

- 设置指定字段的数值数据增加指定范围的值

  ```
  hincrby key field increment
  ```

**注意事项**

- hash类型下的value只能存储字符串，不允许存储其他数据类型，不存在嵌套现象。如果数据未获取到，对应的值为（nill)
- 每个hash可以存储2^32-1个键值对
- hash类型十分贴近对象的数据存储形式，并且可以灵活添加删除对象属性。但hash设计初衷不是为了寻出大量对象，不可滥用，更不可将hash作为对象列表使用
- hgetall 操作可以获取全部属性，如果内部field过多，遍历整体数据效率就会很低，又可能成为数据访问瓶颈。

**业务场景**

![image-20200625145838987](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200625145838987.png)

![image-20200625145956888](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200625145956888.png)

### 4.3 list类型

- 添加/修改数据

  ```
  lpush key value1 [value2] ...
  rpush key value1 [value2] ...
  ```

- 获取数据

  ```
  lrange key start stop
  lindex key index
  llen key
  ```

- 获取并移除数据

  ```
  lpop key
  rpop key
  ```

- 规定时间内获取并移除数据

  ```
  blpop key1 [key2] timeout
  brpop key1 [key2] timeout
  ```

- 移除指定数据

  ```
  lrem key count value
  ```

**注意事项**

- list具有索引的概念，但是操作数据时通常以队列的形式进行入队出队操作，或以栈的形式进行入栈出栈操作
- 获取全部数据操作结束索引设置为-1

### 4.4  set类型

- 能够保存大量的数据，高效的内部存储机制，便于查询
- 与hash存储结构完全相同，仅存储键值，不存储值，并且值是不允许重复的

![image-20200625182135751](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200625182135751.png)

**基本操作**

- 添加数据

  ```
  sadd key member1 [member2]
  ```

- 获取全部数据

  ```
  smembers key
  ```

- 删除数据

  ```
  srem key member1 [member2]
  ```

- 获取集合数据总量

  ```
  scard key
  ```

- 判断集合中是否包含指定数据

  ```
  sismember key member
  ```

- 随机获取集合中指定数量的数据

  ```
  srandmember key [count]
  ```

- 随机获取集合中的某个数据并将该数据移出集合

  ```
  spop key
  ```

- 求两个集合的交、并、差集

  ```
  sinter key1 [key2]
  sunion key1 [key2]
  sdiff key1 [key2] 
  ```

- 求两个集合的交、并、差集并储存到指定集合中

  ```
  sinterstore destination key1 [key2]
  sunionstore destination key1 [key2]
  sdiffstore destination key1 [key2] 
  ```

- 将指定数据从原视集合中移动到目标集合中

  ```
  smove source destination member
  ```

**注意**

- set类型不允许数据重复
- set虽然与hash的存储结构相同，但是无法启用hash中存储值的空间

### 4.5 sorted_set类型

- 在set的存储结构基础上添加可排序字段

![image-20200625185150679](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200625185150679.png)

**基本操作**

- 添加数据

  ```
  zadd key score1 member1 [score2 member2]
  ```

- 获取全部数据

  ```
  zrange key start stop [WITHSCORES]
  zrevrange key start stop [WITHSCORES]
  ```

- 删除数据

  ```
  zrem key member [member ...]
  ```

- 按条件获取数据

  ```
  zrangebyscore key min max [WITHSCORES] [LIMIT]
  zREVrangebyscore key MAX MIN [WITHSCORES] [LIMIT]
  ```

- 条件删除数据

  ```
  zremrangebyrank key start stop
  zremrangebyscore key min max
  ```

  注意：

  - min与max用于限定搜索查询的条件
  - start与stop用于限定查询范围，作用于索引，表示开始和结束索引
  - offset于count用于限定查询范围，作用于查询结果，表示开始位置和数据总量

- 获取集合数据总量

  ```
  zcard key
  zcount key min max
  ```

- 集合交、并操作

  ```
  zinterstore destination numkeys key [key ...]
  zunionstore destination numkeys key [key ...]
  ```

- 获取数据对应的索引

  ```
  zrank key member
  zrevrank key member
  ```

- score值获取与修改、

  ```
  zscore key member
  zincrby key increment member
  ```

  

