# redis

## 安装

```bash
docker run --name my-redis -d \
  -p 6379:6379 \
  -v redis-data:/data \
  redis
```

#### 进入客户端

```bash
docker exec -it my-redis redis-cli
```

## 原理与应用

### 操作

#### 获取所有的 key

1. **连接到 Redis**：
   首先，通过命令行或 Redis 客户端连接到 Redis 服务器。

   示例使用 `redis-cli` 连接：

   ```bash
   redis-cli  
   ```

2. **执行命令**：
   在 Redis CLI 中，执行以下命令：

   ```bash
   KEYS *  
   ```

   该命令将返回所有的键。

- **性能考虑**：

  - `KEYS` 命令会阻塞 Redis 服务器，特别是当数据库中有大量键时。对于大型数据库，建议考虑使用 `SCAN` 命令，它可以分批次遍历键，减少性能影响。

- **使用 SCAN 命令**：
  若要逐步获取所有键，可以使用 `SCAN` 命令。例如：

  ```
  SCAN 0  
  ```

  每次扫描会返回一些键和下一个游标，然后您可以继续使用返回的游标继续扫描。

  ```
  SCAN <cursor>  
  ```

  使用 SCAN 时，您可以设置一个可选的模式参数来过滤某些键，例如：

  ```
  SCAN 0 MATCH prefix:* COUNT 100  
  ```

  这将返回以 `prefix:` 开头的最多 100 个键。

在 Redis 中，删除键可以使用 `DEL` 命令。以下是详细的步骤和一些注意事项。

#### 删除

##### 要删除单个键

```bash
DEL key_name  
```

例如，如果您要删除名为 `my_key` 的键：

```bash
DEL my_key  
127.0.0.1:6379> del testkey
(integer) 1
127.0.0.1:6379> keys *
1) "codehole"
2) "age"
```

##### 删除多个键

您还可以一次性删除多个键。只需在 `DEL` 命令后列出多个键名，用空格分隔：

```bash
DEL key1 key2 key3  
```

例如，删除多个键：

```bash
DEL key1 key2 key3  
```

##### 确认键是否已删除

要确认键是否已经被删除，可以使用 `EXISTS` 命令，它会返回 1（存在）或 0（不存在）：

```bash
EXISTS my_key  
```

##### 删除所有键

如果要删除 Redis 数据库中的所有键，可以使用 `FLUSHDB` 命令。请注意，这将清除当前数据库中的所有数据。

```bash
FLUSHDB  
```

如果您想清除 Redis 实例中的所有数据库，可以使用：

```bash
FLUSHALL  
```

- **谨慎使用**：在生产环境中，尤其是在使用 `FLUSHDB` 和 `FLUSHALL` 时要小心，确保不会误删重要数据。
- **删除不存在的键**：如果您使用 `DEL` 命令删除的键不存在，Redis 会简单地忽略它，不会返回错误消息。

### 基础数据结构

#### string

```
redis 所有的数据结构都是以唯一的 key 为字符串作为名称，不同类型的数据结构差异在于 value 的结构不一样，动态字符串，是可以修改的字符串内部实现是 java 的ArrayList，扩容成倍的，最大长度是 512MB
```

##### 键值对

```bash
127.0.0.1:6379> set name codehole
OK
127.0.0.1:6379> get name
"codehole"
127.0.0.1:6379> exists name
(integer) 1
127.0.0.1:6379> del name
(integer) 1
127.0.0.1:6379> get name
(nil)
```

##### 批量键值对

```bash
127.0.0.1:6379> mset name1 boy name2 girl name3 unknown
OK
127.0.0.1:6379> mget name1 name2 name3
1) "boy"
2) "girl"
3) "unknown"
```

##### 过期和 set 命令扩展

```bash
127.0.0.1:6379> set name codehole
OK
127.0.0.1:6379> get name
"codehole"
127.0.0.1:6379> expire name 5
(integer)

setex name 5 codehole # 5秒后过期相当于 set+expire

127.0.0.1:6379> setnx name codehole
(integer) 1
127.0.0.1:6379> setnx name holycode
(integer) 0
127.0.0.1:6379> get name
"codehole"
```

##### 计数

```bash
127.0.0.1:6379> set age 30
OK
# 自增1
127.0.0.1:6379> incr age
(integer) 31
# 非自增1
127.0.0.1:6379> incrby age 5
(integer) 36
127.0.0.1:6379> incrby age -5
(integer) 31
# 设置时超过最大值
127.0.0.1:6379> set codehole 9223372036854775808
OK
127.0.0.1:6379> incr codehole
(error) ERR value is not an integer or out of range
# 设置时未超过最大值
127.0.0.1:6379> set codehole 9223372036854775807
OK
# 超过最大或者最小值会溢出
127.0.0.1:6379> incr codehole
(error) ERR increment or decrement would overflow

# 加了引号，依然可以自增
127.0.0.1:6379> set testkey "5"
OK
127.0.0.1:6379> incr testkey
(integer) 6
127.0.0.1:6379> incr testkey
(integer) 7
```

#### list

相当于java中的LinkedList，是链表，插入和删除非常快O(1),查找O(n),双向链表，支持前向后向

**左右均有命令操作**

##### 队列（右进左出）

```bash
127.0.0.1:6379> RPUSH books python java golang
(integer) 3
127.0.0.1:6379> llen books
(integer) 3
127.0.0.1:6379> llen age
(error) WRONGTYPE Operation against a key holding the wrong kind of value
127.0.0.1:6379> lpop books
"python"
127.0.0.1:6379> lpop books
"java"
127.0.0.1:6379> lpop books
"golang"
127.0.0.1:6379> lpop books
(nil)
127.0.0.1:6379> get books
(nil)
```

##### 栈（右进右出）

```bash
127.0.0.1:6379> RPUSH books python java golang
(integer) 3
127.0.0.1:6379> rpop books
"golang"
```

##### 慢操作

lindex 相当于 java 的 get（int index），需要对链表进行遍历

ltrim 保留 start_index 和 end_index区间的值

```bash
127.0.0.1:6379> RPUSH books python java golang
(integer) 3
127.0.0.1:6379> lindex books 1
"java"
127.0.0.1:6379> lrange books 0 -1 (查看范围的数据)
1) "python"
2) "java"
3) "golang"
127.0.0.1:6379> ltrim books 1 -1
OK
127.0.0.1:6379> lrange books 0 -1
1) "java"
2) "golang"
127.0.0.1:6379> ltrim books 1 0
OK
127.0.0.1:6379> exists books
(integer) 0
```

##### 快速列表

redis 内部的 list 不是一个简单的 linkedlist，而是一个快速列表

#### hash

redis 中的 hash 相当于 java 中的 HashMap

缺点是 hash 存储消耗高于单个字符串

```bash
127.0.0.1:6379> hset books java "think in java" // 有空格使用双引号
(integer) 1
127.0.0.1:6379> hset books golang "go in"
(integer) 1
127.0.0.1:6379> hset books python "python cookbook"
(integer) 1
127.0.0.1:6379> hgetall books  // 获取所有元素 key value 形式
1) "java"
2) "think in java"
3) "golang"
4) "go in"
5) "python"
6) "python cookbook"
127.0.0.1:6379> hlen books
(integer) 3
127.0.0.1:6379> hget books java
"think in java"
127.0.0.1:6379> hset books golang "learning go" // 更新操作
(integer) 0
127.0.0.1:6379> hget books golang
"learning go"
127.0.0.1:6379> hmset books java "effective java" python "learning" test "test"
OK
127.0.0.1:6379> hgetall books
1) "java"
2) "effective java"
3) "golang"
4) "learning go"
5) "python"
6) "learning"
7) "test"
8) "test"

// 单个子 key 也可以计数，使用 hincrby
127.0.0.1:6379> hset user-ll age 29
(integer) 1
127.0.0.1:6379> HINCRBY user-ll age 1
(integer) 30
```

#### set

相当于 java 的 hashset，拥有去重功能,无序

```bash
27.0.0.1:6379> sadd books python
(integer) 1
127.0.0.1:6379> sadd books python
(integer) 0
127.0.0.1:6379> sadd books java golang
(integer) 2
127.0.0.1:6379> smembers books //列出元素
1) "python"
2) "java"
3) "golang"
127.0.0.1:6379> SISMEMBER books java // 判断是否包含元素
(integer) 1
127.0.0.1:6379> SISMEMBER books rust
(integer) 0
127.0.0.1:6379> scard books // count 功能
(integer) 3
127.0.0.1:6379> spop books // 弹出一个
"python"
```

#### zset

有序列表，类似于 java 的 SortedSet和 HashMap 的结合体

```bash
127.0.0.1:6379> zadd books 9.0 "thinks in java"
(integer) 1
127.0.0.1:6379> zadd books 9.1 "thinks in java"
(integer) 0
127.0.0.1:6379> zadd books 8.9 "java concurrency"
(integer) 1
127.0.0.1:6379> zadd books  8.6 "java cookbook"
(integer) 1
127.0.0.1:6379> zrange books 0 -1
1) "java cookbook"
2) "java concurrency"
3) "thinks in java"
127.0.0.1:6379> ZREVRANGE books 0 -1
1) "thinks in java"
2) "java concurrency"
3) "java cookbook"
127.0.0.1:6379> zcard books
(integer) 3
127.0.0.1:6379> zscore books "java cookbook"
"8.6"
127.0.0.1:6379> zscore books "java concurrency"
"8.9"
127.0.0.1:6379> zrank books "java concurrency"
(integer) 1
127.0.0.1:6379> ZRANGEBYSCORE books 0 8.9
1) "java cookbook"
2) "java concurrency"
127.0.0.1:6379> ZRANGEBYSCORE books -inf 8.9
1) "java cookbook"
2) "java concurrency"
127.0.0.1:6379> ZRANGEBYSCORE books -inf 8.9 withscores
1) "java cookbook"
2) "8.6"
3) "java concurrency"
4) "8.9"
127.0.0.1:6379> zrem books "java concurrency"
(integer) 1
127.0.0.1:6379> zrange books 0 -1
1) "java cookbook"
2) "thinks in java"
```

#### 通用规则

list，set，hash，zset 这四种数据是容器型数据结构符合以下两条规则

1. create if not exists 如果容器不存在那么就创建一个，再进行操作
2. drop if no elements 如果容器内没有元素，那么立即删除容器，释放内存

#### 过期时间

所有数据结构都可以设置过期时间，过期以对象为单位，比如一个 hash 结构过期，整个 hash 对象过期，而不是某个子 key

> [!NOTE]
>
> 如果一个字符串设置了过期时间，使用 set 方法修改后，过期时间会消失, 只针对字符串

```bash
127.0.0.1:6379> rpush books python java
(integer) 2
127.0.0.1:6379> expire books 60
(integer) 1
127.0.0.1:6379> ttl books
(integer) 50
127.0.0.1:6379> rpush books golang
(integer) 3
127.0.0.1:6379> ttl books
(integer) 41
127.0.0.1:6379> ttl books
(integer) 39
127.0.0.1:6379> ttl books
(integer) 38
127.0.0.1:6379> set codehole yoyo
OK
127.0.0.1:6379> expire codehole 60
(integer) 1
127.0.0.1:6379> ttl codehole
(integer) 52
127.0.0.1:6379> set codehole yoyo
OK
127.0.0.1:6379> ttl codehole
(integer) -1
127.0.0.1:6379> keys *
1) "codehole"
```

#### 分布式锁

```
setnx 
只允许一个客户端占用，但是无法原子性设置过期时间
```

set 指令扩展使得 setnx 和 expire 可以原子性执行 （redis2.8 版本后）

```bash
127.0.0.1:6379> set lock:codehole true ex 50 nx
OK
127.0.0.1:6379> get lock:codehole
"true"
127.0.0.1:6379> get lock:codehole
(nil)
```

### 位图

