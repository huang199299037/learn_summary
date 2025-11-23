# mongodb

## 安装

```bash
docker pull mongo
docker run -d \
  --name mongodb \
  -v mongo_data:/data/db \
  -p 27017:27017 \
  mongo
进入容器：
docker exec -it mongodb bash
mongosh
```

## MongoDB 中库、集合、文档的总结

MongoDB 是一种 **文档型 NoSQL 数据库**，其数据模型基于以下核心概念：

------

#### 1. **数据库（Database）**

- **定义**：数据库是 MongoDB 中存储数据的顶层容器，类似于关系型数据库中的“数据库”。

- **特点**：

  - 一个 MongoDB 实例可以包含多个独立的数据库。
  - 数据库之间完全隔离（权限、配置、存储等）。
  - 系统默认数据库：
    - `admin`：存储管理员权限信息。
    - `local`：存储当前实例的配置和临时数据。
    - `config`：分片集群的元数据。

- **操作示例**：

  bash

  复制

  ```
  use mydb  # 切换到数据库（若不存在则隐式创建）
  db.dropDatabase()  # 删除当前数据库
  ```

------

#### 2. **集合（Collection）**

- **定义**：集合是 MongoDB 中存储文档的容器，类似于关系型数据库中的“表”，但无需固定结构（无模式）。

- **特点**：

  - 集合是动态的：文档可以有不同的字段和结构。
  - 集合名称需满足：
    - 不能以 `system.` 开头（保留给系统集合）。
    - 不能包含特殊字符（如空格、`$`）。
  - 子集合：可通过 `db.users.posts` 的形式组织数据（仅逻辑分类）。

- **操作示例**：

  bash

  复制

  ```
  db.createCollection("users")  # 显式创建集合（可选配置如大小、索引）
  db.users.drop()  # 删除集合
  ```

------

#### 3. **文档（Document）**

- **定义**：文档是 MongoDB 中的基本数据单元，以 **BSON（二进制 JSON）** 格式存储。

- **特点**：

  - 键值对结构：字段名（字符串） + 值（支持多种类型：字符串、数值、日期、嵌套文档、数组等）。

  - 每个文档必须有一个唯一标识符 `_id`（主键），未指定时自动生成 `ObjectId`。

  - 示例文档：

    json

    复制

    ```json
    {
      "_id": ObjectId("5f3c7a8d2a9e1d2a7c8b1e5c"),
      "name": "Alice",
      "age": 30,
      "address": {
        "city": "Beijing",
        "street": "Main St"
      },
      "tags": ["developer", "mongodb"]
    }
    ```

- **操作示例**：

  bash

  复制

  ```bash
  db.users.insertOne({name: "Bob", age: 25})  # 插入文档
  db.users.find({age: {$gt: 20}})  # 查询文档
  db.users.updateOne({name: "Bob"}, {$set: {age: 26}})  # 更新文档
  db.users.deleteOne({name: "Bob"})  # 删除文档
  ```

------

### 三者的关系总结

- **层级结构**：`数据库` → `集合` → `文档`

- **类比关系型数据库**：

  | MongoDB | 关系型数据库 |
  | :------ | :----------- |
  | 数据库  | 数据库       |
  | 集合    | 表           |
  | 文档    | 行（记录）   |

### 内置数据库

#### 1. `admin` 数据库

- **用途**：`admin` 数据库是 MongoDB 的管理数据库，主要用于执行管理任务和配置操作。

- 功能

  ：

  - **用户管理**：在 `admin` 数据库中创建和管理用户以及角色权限。
  - **全局操作**：执行全局操作，如备份、恢复和监控 MongoDB 实例。
  - **运维命令**：许多与数据备份、恢复和实例状态检查相关的命令在 `admin` 数据库中执行。

#### 2. `config` 数据库

- **用途**：`config` 数据库主要用于存储 MongoDB 集群的配置数据，尤其与分片（sharding）相关的信息。

- 功能

  ：

  - **分片元数据**：存储与分片架构相关的元数据，包括分片的状态、分片键、分片的路由信息和集合的分片配置。
  - **路由管理**：`config` 数据库帮助协调并管理在分布式环境中的数据路由。
  - **集群状态**：存储当前集群的状态和配置，以及各个分片的统计信息。

#### 3. `local` 数据库

- **用途**：`local` 数据库包含与该实例相关的信息，仅在本地实例上有效。

- 功能

  ：

  - **操作日志**：`local` 数据库保存了操作日志（oplog），用于副本集的复制和同步。
  - **状态信息**：该数据库也可以用于存储一些内部状态和信息。
  - **不跨集群**：重要的是，`local` 数据库的内容不会被复制到其他副本集节点，因此它是一个特有于每个单独实例的数据库。

#### 总结

- **`admin`**：用于数据库管理和权限控制，执行全局数据库管理任务。
- **`config`**：存储分片的元数据和配置信息，是分布式集群中使用的重要数据库。
- **`local`**：保存本地操作日志和状态信息，每个实例独有。

## 操作



以下是 MongoDB 中 **数据库（Database）** 和 **集合（Collection）** 的常用操作总结，涵盖创建、删除、管理及查询等核心操作：

------

### 一、数据库（Database）操作

#### 1. **创建/切换数据库**

```sql
use mydb  # 切换到数据库（若不存在则隐式创建）
```

- 如果数据库 `mydb` 不存在，MongoDB 会在第一次插入数据时自动创建。

------

#### 2. **查看当前数据库**

```sql
db  # 显示当前操作的数据库名称
```

------

#### 3. **列出所有数据库**

```sql
show dbs  # 列出所有已存在数据的数据库（空库不显示）
```

------

#### 4. **删除数据库**

```sql
use mydb    # 先切换到目标数据库
db.dropDatabase()  # 删除当前数据库（谨慎操作！）
```

------

#### 5. **查看数据库统计信息**

```sql
db.stats()  # 返回当前数据库的存储大小、集合数量等统计信息
```

------

### 二、集合（Collection）操作

#### 1. **创建集合**

- **显式创建**（可指定配置）：

  ```sql
  db.createCollection("users", {
    capped: true,      # 固定集合（固定大小，先进先出）
    size: 100000,      # 固定集合大小（字节）
    max: 1000          # 最大文档数
  })
  ```

- **隐式创建**：直接插入文档时自动创建：

  ```sql
  db.orders.insertOne({ product: "Laptop", price: 999 })
  ```

------

#### 2. **查看所有集合**

```sql
show collections  or show tables     # 列出当前数据库的所有集合
# 或
db.getCollectionNames()  # 返回集合名称列表
```

------

#### 3. **删除集合**

```sql
db.users.drop()  # 删除集合（包括所有文档和索引）
```

------

#### 4. **重命名集合**

```sql
db.users.renameCollection("clients")  # 将集合 `users` 重命名为 `clients`
```

------

#### 5. **查看集合信息**

```sql
db.users.stats()  # 返回集合的存储大小、文档数、索引信息等
db.users.countDocuments()  # 返回集合中的文档总数
```

------

#### 6. **修改集合配置**

```sql
db.runCommand({
  collMod: "users",   # 集合名称
  validator: {        # 设置文档验证规则
    $jsonSchema: {
      bsonType: "object",
      required: ["name", "email"],
      properties: { ... }
    }
  }
})
```

### 三、文档操作

以下是 MongoDB 中 **增删改查（CRUD）** 操作的详细说明，涵盖基础语法、常用操作符及实际应用场景。

------

#### 一、**插入操作（Create）**

##### 1. **插入单个文档**

- **方法**：`insertOne()`

- **示例**：

  ```js
  db.users.insertOne({
    name: "Alice",
    age: 30,
    email: "alice@example.com",
    tags: ["developer", "mongodb"]
  });
  ```

- **返回值**：

  ```js
  {
    "acknowledged": true,
    "insertedId": ObjectId("5f3c7a8d2a9e1d2a7c8b1e5c")
  }
  ```

------

##### 2. **批量插入文档**

- **方法**：`insertMany()`

- **示例**：

  ```js
  db.products.insertMany([
    { name: "Laptop", price: 999, stock: 10 },
    { name: "Phone", price: 699, stock: 20 }
  ]);
  ```

- **返回值**：

  ```js
  {
    "acknowledged": true,
    "insertedIds": [
      ObjectId("5f3c7a8d2a9e1d2a7c8b1e5d"),
      ObjectId("5f3c7a8d2a9e1d2a7c8b1e5e")
    ]
  }
  ```

------

#### 二、**查询操作（Read）**

##### 1. **查询所有文档**

- **方法**：`find()`

- **示例**：

  ```js
  db.users.find();  // 返回集合中所有文档
  ```

------

##### 2. **条件查询**

- **精确匹配**：

  ```js
  db.users.find({ name: "Alice" });
  ```

- **范围查询**：

  ```js
  db.users.find({ age: { $gt: 25 } });  // 年龄大于25
  ```

- **逻辑运算符**：

  ```js
  db.users.find({ 
    $or: [
      { age: { $lt: 20 } },
      { age: { $gt: 30 } }
    ]
  });
  ```

------

##### 3. **投影（返回部分字段）**

- **语法**：`find({ query }, { projection })`

- **示例**：

  ```js
  db.users.find(
    { age: 30 },
    { name: 1, email: 1, _id: 0 }  // 1=包含，0=排除
  );
  ```

------

##### 4. **排序与分页**

- **排序**：

  ```js
  db.users.find().sort({ age: 1 });  // 1=升序，-1=降序
  ```

- **分页**：

  ```js
  db.users.find().skip(10).limit(5);  // 跳过前10条，取5条
  ```

------

##### 5. **聚合查询（Aggregation）**

- **示例**：统计每个城市的用户数量

  ```js
  db.users.aggregate([
    { $group: { _id: "$city", total: { $sum: 1 } } }
  ]);
  ```

------

##### 6. $type

###### 一、作用语法

- **匹配字段的数据类型**：检查字段是否属于指定的 BSON 类型。

- **处理数据不一致问题**：例如，某个字段可能存储了混合类型（如字符串和数值），通过 `$type` 可筛选特定类型。

  ```js
  db.collection.find({
    <field>: { 
      $type: <BSON类型编号 或 类型别名> 
    }
  })
  ```

  - `<BSON类型编号>`：如 `1` 表示 `Double`，`2` 表示 `String`。
  - `<类型别名>`：如 `"string"`、`"int"`（MongoDB 3.4+ 支持别名）。

###### 二、BSON 类型对照表

以下是常见的 BSON 类型编号和别名：

| **类型编号** | **类型别名** | **说明**                 |
| :----------- | :----------- | :----------------------- |
| 1            | `"double"`   | 双精度浮点数             |
| 2            | `"string"`   | UTF-8 字符串             |
| 3            | `"object"`   | 嵌套文档（子文档）       |
| 4            | `"array"`    | 数组                     |
| 5            | `"binData"`  | 二进制数据               |
| 7            | `"objectId"` | ObjectId（唯一标识符）   |
| 8            | `"bool"`     | 布尔值（true/false）     |
| 9            | `"date"`     | 日期时间（ISODate）      |
| 10           | `"null"`     | 空值（null）             |
| 16           | `"int"`      | 32 位整数                |
| 18           | `"long"`     | 64 位整数                |
| 19           | `"decimal"`  | 高精度小数（Decimal128） |

------

###### 三、使用示例

1. **基本用法**

**场景**：查询 `age` 字段为字符串类型的文档。

```js
db.users.find({
  age: { $type: "string" }  // 或 $type: 2
});
```

------

2. **匹配数值类型**

**场景**：区分 `price` 字段是整数（`int`）还是浮点数（`double`）。


```js
// 查找 price 为整数（32位）的文档
db.products.find({
  price: { $type: "int" }  // 或 $type: 16
});

// 查找 price 为双精度浮点数的文档
db.products.find({
  price: { $type: "double" }  // 或 $type: 1
});
```

------

3. **匹配嵌套字段类型**

**场景**：查询 `address.city` 是字符串的文档。


```js
db.users.find({
  "address.city": { $type: "string" }
});
```

------

4. **匹配数组类型**

**场景**：查找 `tags` 字段是数组的文档。

```js
db.posts.find({
  tags: { $type: "array" }  // 或 $type: 4
});
```

------

5. **匹配日期类型**

**场景**：查询 `createdAt` 是日期类型的文档。

```js
db.orders.find({
  createdAt: { $type: "date" }  // 或 $type: 9
});
```

------

6. **匹配 Null 值**

**场景**：查询 `description` 字段为 `null` 的文档。

```js
db.products.find({
  description: { $type: "null" }  // 或 $type: 10
});
```

------

###### 四、高级用法

1. **组合查询**

**场景**：查询 `age` 是数值类型（`int` 或 `double`）且大于 20 的文档。

```js
db.users.find({
  age: {
    $gt: 20,
    $type: ["int", "double"]  // MongoDB 4.2+ 支持多类型匹配
  }
});
```

------

2. **排除特定类型**

**场景**：查询 `status` 字段不是布尔类型的文档。

```js
db.tasks.find({
  status: { $not: { $type: "bool" } }
});
```

------

3. **数组元素的类型匹配**

**场景**：查询 `scores` 数组中存在字符串元素的文档。

```js
db.students.find({
  scores: { $elemMatch: { $type: "string" } }
});
```

#### 三、**更新操作（Update）**

##### 1. **更新单个文档**

- **方法**：`updateOne()`

- **示例**：

  ```js
  db.users.updateOne(
    { name: "Alice" },
    { $set: { age: 31 } }  // 更新字段
  );
  ```

------

##### 2. **批量更新文档**

- **方法**：`updateMany()`

- **示例**：

  ```js
  db.products.updateMany(
    { stock: { $lt: 5 } },
    { $set: { status: "low-stock" } }
  );
  ```

------

##### 3. **替换文档**

- **方法**：`replaceOne()`

- **示例**：

  ```js
  db.users.replaceOne(
    { name: "Alice" },
    { name: "Alice", age: 31, role: "admin" }  // 完全替换文档
  );
  ```

------

##### 4. **更新操作符**

| 操作符   | 用途           | 示例                             |
| :------- | :------------- | :------------------------------- |
| `$set`   | 设置字段值     | `{ $set: { status: "active" } }` |
| `$unset` | 删除字段       | `{ $unset: { tempField: 1 } }`   |
| `$inc`   | 数值增减       | `{ $inc: { stock: -1 } }`        |
| `$push`  | 向数组添加元素 | `{ $push: { tags: "new" } }`     |
| `$pull`  | 从数组移除元素 | `{ $pull: { tags: "old" } }`     |

------

#### 四、**删除操作（Delete）**

##### 1. **删除单个文档**

- **方法**：`deleteOne()`

- **示例**：

  ```js
  db.users.deleteOne({ name: "Bob" });
  ```

------

##### 2. **批量删除文档**

- **方法**：`deleteMany()`

- **示例**：

  ```js
  db.users.deleteMany({ age: { $lt: 18 } });  // 删除所有年龄小于18的文档
  ```

------

#### 五、**高级操作**

##### 1. **原子操作（findAndModify）**

- **用途**：查询并修改文档（原子性操作）

- **示例**：

  ```js
  db.users.findOneAndUpdate(
    { name: "Alice" },
    { $inc: { loginCount: 1 } },
    { returnNewDocument: true }  // 返回更新后的文档
  );
  ```

------

##### 2. **索引优化**

- **创建索引**：

  ```js
  db.users.createIndex({ email: 1 });  // 1=升序索引
  ```

- **查询性能分析**：

  ```js
  db.users.find({ email: "alice@example.com" }).explain("executionStats");
  ```

------

##### 3. **事务支持（多文档操作）**

- **示例**：

  ```js
  const session = db.getMongo().startSession();
  session.startTransaction();
  try {
    db.users.updateOne(
      { name: "Alice" },
      { $inc: { balance: -100 } },
      { session }
    );
    db.orders.insertOne(
      { product: "Laptop", amount: 100 },
      { session }
    );
    session.commitTransaction();
  } catch (error) {
    session.abortTransaction();
  }
  ```

------

#### 六、**总结对比**

| 操作类型 | 方法           | 用途         | 示例场景           |
| :------- | :------------- | :----------- | :----------------- |
| **插入** | `insertOne()`  | 插入单个文档 | 用户注册           |
|          | `insertMany()` | 批量插入文档 | 初始化产品数据     |
| **查询** | `find()`       | 基础查询     | 列出所有用户       |
|          | `findOne()`    | 查询单个文档 | 根据ID获取用户详情 |
|          | `aggregate()`  | 复杂聚合     | 统计订单金额总和   |
| **更新** | `updateOne()`  | 更新单个文档 | 修改用户邮箱       |
|          | `updateMany()` | 批量更新文档 | 标记所有过期订单   |
|          | `replaceOne()` | 替换整个文档 | 更新用户完整信息   |
| **删除** | `deleteOne()`  | 删除单个文档 | 删除无效用户       |
|          | `deleteMany()` | 批量删除文档 | 清理历史日志       |

## **MongoDB 索引详解**

索引是 MongoDB 中提升查询性能的核心机制，通过合理设计索引可以显著加速数据检索、排序和聚合操作。以下从 **索引简介**、**实现原理**、**复合索引**、**联合查询优化** 四个维度展开说明。

------

### 一、**索引简介**

#### 1. **索引是什么？**

- 索引是数据库中的一种 **数据结构**，类似于书籍的目录，能够快速定位数据位置。
- MongoDB 支持多种索引类型：
  - **单字段索引**：基于单个字段。
  - **复合索引**：基于多个字段。
  - **多键索引**：针对数组字段。
  - **地理空间索引**：用于坐标查询。
  - **文本索引**：支持全文搜索。
  - **哈希索引**：等值查询优化。
  - **TTL 索引**：自动过期数据。

#### 2. **索引的作用**

- **加速查询**：避免全集合扫描（Collection Scan）。
- **优化排序**：利用索引直接返回有序结果。
- **唯一性约束**：通过唯一索引（`unique: true`）防止重复值。

#### 3. **索引的代价**

- **写入开销**：每次插入、更新、删除操作需维护索引。
- **存储占用**：索引需要额外的磁盘和内存空间。

------

### 二、**索引实现原理**

#### 1. **底层数据结构：B-Tree**

- MongoDB 默认使用 **B-Tree（平衡树）** 结构存储索引。
- **B-Tree 特性**：
  - 每个节点包含多个键值对，减少树的高度。
  - 数据有序存储，支持高效的范围查询和排序。
  - 查询时间复杂度为 `O(log n)`，远快于全表扫描的 `O(n)`。

#### 2. **索引的工作流程**

1. 查询优化器根据查询条件选择最佳索引。
2. 从索引树中快速定位到符合条件的文档位置。
3. 根据索引指针回表（集合）获取完整文档（除非使用覆盖索引）。

#### 3. **索引与内存**

- **常驻内存**：索引会被加载到内存中（LRU 策略），加速访问。
- **内存不足时**：部分索引可能被换出到磁盘，导致性能下降。

------

### 三、**复合索引（Compound Index）**

#### 1. **复合索引定义**

- 基于多个字段的索引，例如：

  ```js
  db.users.createIndex({ age: 1, city: 1 }); // 先按 age 排序，再按 city 排序
  ```

#### 2. **最左前缀原则（Leftmost Prefix）**

- 查询条件必须包含复合索引的最左侧字段，才能命中索引。

- **有效场景**：

  ```js
  // 命中索引 { age:1, city:1 }
  db.users.find({ age: 25 });
  db.users.find({ age: 25, city: "Beijing" });
  
  // 未命中索引（缺少 age）
  db.users.find({ city: "Beijing" });
  ```

#### 3. **排序与索引方向**

- 索引方向（升序 `1` 或降序 `-1`）需与排序方向匹配：

  ```js
  // 有效：索引 { age:1, city:1 }，排序方向与索引一致
  db.users.find().sort({ age: 1, city: 1 });
  
  // 无效：排序方向与索引部分相反
  db.users.find().sort({ age: 1, city: -1 });
  ```

#### 4. **索引选择性（Selectivity）**

- **高选择性字段优先**：唯一值多的字段（如 `email`）放在复合索引左侧。

- **示例**：

  ```js
  // 更好的复合索引：{ category:1, price:1 }
  // 因为 category 的选择性通常高于 price
  db.products.createIndex({ category: 1, price: 1 });
  ```

------

### 四、**联合查询与索引优化**

#### 1. **覆盖查询（Covered Query）**

- 查询字段全部在索引中，无需回表获取文档：

  ```json
  // 索引：{ age:1, city:1 }
  db.users.find(
    { age: 25, city: "Beijing" },
    { _id: 0, age: 1, city: 1 } // 只返回索引字段
  );
  ```

- **优点**：减少 I/O 开销，性能最佳。

#### 2. **索引交集（Index Intersection）**

- MongoDB 可自动合并多个索引的结果（默认关闭，需配置）：

  ```js
  // 索引：{ age:1 }, { city:1 }
  db.users.find({ age: 25, city: "Beijing" });
  ```

- **缺点**：通常不如复合索引高效，建议优先使用复合索引。

#### 3. **联合查询优化示例**

- **场景**：查询年龄大于 20 且来自北京的用户，并按年龄排序。

- **优化步骤**：

  1. 创建复合索引 `{ city:1, age:1 }`（最左前缀 + 排序）。

  2. 查询条件与排序字段均命中索引：

     ```js
     db.users.find({ city: "Beijing", age: { $gt: 20 } }).sort({ age: 1 });
     ```

------

### 五、**索引管理命令**

#### 1. **查看索引**

```js
db.collection.getIndexes(); // 列出集合所有索引
```

#### 2. **创建索引**

```js
db.collection.createIndex(
  { field1: 1, field2: -1 },
  { 
    unique: true,       // 唯一索引
    background: true,   // 后台创建（避免阻塞）
    sparse: true        // 稀疏索引（仅索引存在字段的文档）
  }
);
```

#### 3. **删除索引**

```js
db.collection.dropIndex("index_name"); // 删除指定索引
db.collection.dropIndexes();           // 删除所有索引
```

#### 4. **索引性能分析**

```js
db.collection.find(...).explain("executionStats");
// 关注：
// - "stage": "IXSCAN"（索引扫描）
// - "totalKeysExamined": 扫描的索引键数量
// - "totalDocsExamined": 扫描的文档数量
```

------

### 六、**最佳实践**

1. **避免过度索引**：每个索引增加写入开销，按查询需求设计。

2. **监控慢查询**：通过 `db.currentOp()` 或日志分析优化目标。

3. **定期重建索引**：删除冗余数据后，使用 `reIndex()` 回收空间。

4. **使用部分索引**（Partial Index）：仅索引符合条件的文档，减少存储：

   ```js
   db.users.createIndex(
     { age: 1 },
     { partialFilterExpression: { age: { $gt: 18 } }
   );
   ```

------

### 七、**总结**

| **场景**        | **索引策略**                           |
| :-------------- | :------------------------------------- |
| 等值查询 + 排序 | 复合索引（查询字段在前，排序字段在后） |
| 范围查询 + 排序 | 复合索引（范围字段在后，排序字段在前） |
| 高频查询字段    | 单独索引或作为复合索引首字段           |
| 覆盖查询        | 确保查询字段全部在索引中               |

合理设计索引是 MongoDB 性能优化的核心，需结合数据分布、查询模式及写入负载综合决策。

## explain

### 一、**基本语法**

#### 1. **运行 `explain`**

- 对任意查询添加 `.explain()` 方法：

  ```js
  db.collection.find({ ... }).explain(<verbosity>);
  ```

- **参数 `<verbosity>`**：

  - `"queryPlanner"`（默认）：返回查询优化器的逻辑执行计划。
  - `"executionStats"`：返回实际执行的统计信息（如扫描文档数、耗时）。
  - `"allPlansExecution"`：返回所有候选执行计划的详细信息。

------

### 二、**执行模式详解**

#### 1. **`queryPlanner` 模式**

- **核心字段**：

  ```js
  {
    "queryPlanner": {
      "plannerVersion": 1,
      "namespace": "db.collection",
      "indexFilterSet": false,   // 是否使用索引过滤器
      "parsedQuery": { ... },    // 解析后的查询条件
      "winningPlan": { ... },    // 优化器选择的执行计划
      "rejectedPlans": [ ... ]   // 其他候选执行计划
    }
  }
  ```

- **作用**：展示查询优化器选择的执行策略（如是否使用索引）。

------

#### 2. **`executionStats` 模式**

- **核心字段**：

  ```js
  {
    "executionStats": {
      "executionSuccess": true,
      "nReturned": 10,            // 返回的文档数量
      "executionTimeMillis": 5,   // 总耗时（毫秒）
      "totalKeysExamined": 20,     // 扫描的索引键数量
      "totalDocsExamined": 15,     // 扫描的集合文档数量
      "executionStages": { ... }   // 执行阶段详情
    }
  }
  ```

- **作用**：提供查询的实际执行数据（如耗时、扫描量）。

------

#### 3. **`allPlansExecution` 模式**

- **核心字段**：

  ```js
  {
    "executionStats": {
      ...,
      "allPlansExecution": [ ... ]  // 所有候选执行计划的统计
    }
  }
  ```

- **作用**：对比所有候选计划的性能，用于分析索引选择策略。

------

### 三、**执行阶段（`executionStages`）解析**

#### 1. **常见执行阶段**

| **阶段（stage）** | **说明**                     |
| :---------------- | :--------------------------- |
| `COLLSCAN`        | 全集合扫描（未使用索引）     |
| `IXSCAN`          | 索引扫描                     |
| `FETCH`           | 根据索引指针回表获取完整文档 |
| `SORT`            | 内存排序（若未命中索引排序） |
| `PROJECTION`      | 字段投影（仅返回指定字段）   |
| `LIMIT`           | 限制返回文档数量             |
| `SKIP`            | 跳过指定数量的文档           |

------

#### 2. **执行阶段树示例**

```js
"executionStages": {
  "stage": "FETCH",
  "nReturned": 10,
  "executionTimeMillisEstimate": 1,
  "inputStage": {
    "stage": "IXSCAN",
    "indexName": "age_1",
    "nReturned": 10,
    "indexBounds": { "age": ["[25, 25]"] }
  }
}
```

- **解读**：
  - 外层 `FETCH`：通过索引指针获取完整文档。
  - 内层 `IXSCAN`：使用 `age_1` 索引扫描匹配的键。

------

### 四、**关键性能指标**

#### 1. **核心指标**

- **`nReturned`**：查询返回的文档数量。
- **`executionTimeMillis`**：查询总耗时（毫秒）。
- **`totalKeysExamined`**：扫描的索引键数量（越少越好）。
- **`totalDocsExamined`**：扫描的集合文档数量（越少越好）。

------

#### 2. **性能优化目标**

- **理想情况**：
  - `nReturned` ≈ `totalKeysExamined` ≈ `totalDocsExamined`（索引高度匹配查询）。
  - `executionTimeMillis` 尽可能小。
- **警告信号**：
  - `totalDocsExamined` ≫ `nReturned`：大量文档扫描，可能缺少有效索引。
  - `executionStages` 包含 `COLLSCAN`：全集合扫描，需优化索引。

------

### 五、**实战示例**

#### 1. **场景：查询年龄为 25 的用户**

```js
db.users.find({ age: 25 }).explain("executionStats");
```

#### **输出分析**：

- **情况 1：未使用索引**

  ```js
  "executionStats": {
    "nReturned": 100,
    "executionTimeMillis": 120,
    "totalKeysExamined": 0,
    "totalDocsExamined": 10000,
    "executionStages": {
      "stage": "COLLSCAN"  // 全集合扫描，性能差
    }
  }
  ```

  **优化**：为 `age` 字段创建索引。

- **情况 2：使用索引**

  ```js
  "executionStats": {
    "nReturned": 100,
    "executionTimeMillis": 5,
    "totalKeysExamined": 100,
    "totalDocsExamined": 100,
    "executionStages": {
      "stage": "FETCH",
      "inputStage": {
        "stage": "IXSCAN",  // 索引扫描
        "indexName": "age_1"
      }
    }
  }
  ```

  **结论**：索引生效，性能显著提升。

------

### 六、**高级技巧**

#### 1. **覆盖查询（Covered Query）**

- 确保查询仅使用索引字段，避免回表：

  ```js
  // 索引：{ age: 1, name: 1 }
  db.users.find({ age: 25 }, { _id: 0, age: 1, name: 1 }).explain();
  ```

  **输出**：

  ```js
  "executionStages": {
    "stage": "PROJECTION",
    "inputStage": {
      "stage": "IXSCAN"  // 无需 FETCH 阶段
    }
  }
  ```

------

#### 2. **复合索引排序优化**

- 索引方向需与排序一致：

  ```
  // 索引：{ age: 1, name: 1 }
  db.users.find().sort({ age: 1, name: 1 }).explain();
  ```

  **输出**：

  ```js
  "executionStages": {
    "stage": "IXSCAN",  // 索引排序，无 SORT 阶段
    "direction": "forward"
  }
  ```

------

#### 3. **强制使用指定索引**

- 使用 `hint()` 指定索引：

  ```js
  db.users.find({ age: 25 }).hint({ age: 1 }).explain();
  ```

------

### 七、**总结**

| **操作**     | **命令示例**                                                 |
| :----------- | :----------------------------------------------------------- |
| 查看查询计划 | `db.collection.find(...).explain("executionStats")`          |
| 分析索引命中 | 检查 `executionStages` 是否包含 `IXSCAN`                     |
| 识别全表扫描 | `executionStages` 出现 `COLLSCAN`                            |
| 优化目标     | 最小化 `totalKeysExamined` 和 `totalDocsExamined`，避免 `COLLSCAN` |

通过 `explain` 分析执行计划，可以精准定位性能瓶颈，针对性优化索引和查询逻辑。

## 副本集

### 一、**MongoDB 副本集简介**

#### 1. **副本集的作用**

- **高可用性**：自动故障转移（主节点宕机后选举新主节点）。
- **数据冗余**：数据自动同步到多个节点。
- **读写分离**：从节点可处理读请求（需配置）。

#### 2. **副本集成员**

- **主节点（Primary）**：处理所有写操作和默认读操作。
- **从节点（Secondary）**：复制主节点数据，可处理读请求。
- **仲裁节点（Arbiter）**：不存储数据，仅参与选举投票（可选）。

------

### 二、**基于 Docker 搭建 MongoDB 副本集**

#### 1. **创建 Docker 网络**

为 MongoDB 容器创建专用网络，确保节点间通信：

```bash
docker network create mongo-net
```

#### 2. **启动三个 MongoDB 容器**

使用以下命令启动 **1 主 + 2 从** 的副本集：

```bash
# 主节点（Primary）
docker run -d --name mongo1 \
  -p 27017:27017 \
  --network mongo-net \
  -v mongo-data1:/data/db \
  mongo \
  mongod --replSet rs0 --bind_ip localhost,mongo1

# 从节点1（Secondary）
docker run -d --name mongo2 \
  -p 27018:27017 \
  --network mongo-net \
  -v mongo-data2:/data/db \
  mongo \
  mongod --replSet rs0 --bind_ip localhost,mongo2

# 从节点2（Secondary）
docker run -d --name mongo3 \
  -p 27019:27017 \
  --network mongo-net \
  -v mongo-data3:/data/db \
  mongo \
  mongod --replSet rs0 --bind_ip localhost,mongo3
```

**参数说明**：

- `--replSet rs0`：指定副本集名称为 `rs0`。
- `--bind_ip`：允许容器间通过主机名通信。
- `-v`：挂载数据目录（避免容器重启后数据丢失）。

------

#### 3. **初始化副本集**

进入主节点容器，执行副本集初始化：

```bash
# 进入主节点容器
docker exec -it mongo1 mongosh

# 在 MongoDB Shell 中执行初始化命令
rs.initiate({
  _id: "rs0",
  members: [
    { _id: 0, host: "mongo1:27017" },
    { _id: 1, host: "mongo2:27017" },
    { _id: 2, host: "mongo3:27017" }
  ]
})
```

**输出成功提示**：

```
{ ok: 1 }
```

------

#### 4. **验证副本集状态**

在 MongoDB Shell 中执行：

```js
rs.status()
```

**关键输出字段**：

```js
{
  "set": "rs0",
  "members": [
    {
      "_id": 0,
      "name": "mongo1:27017",
      "stateStr": "PRIMARY",  // 主节点
      "health": 1
    },
    {
      "_id": 1,
      "name": "mongo2:27017",
      "stateStr": "SECONDARY",  // 从节点
      "health": 1
    },
    {
      "_id": 2,
      "name": "mongo3:27017",
      "stateStr": "SECONDARY",  // 从节点
      "health": 1
    }
  ]
}
```

------

### 三、**测试副本集功能**

#### 1. **写入数据到主节点**

在主节点（`mongo1`）插入测试数据：

```bash
# 进入主节点容器
docker exec -it mongo1 mongosh

# 插入数据
use testdb
db.users.insertOne({ name: "Alice", age: 30 })
```

------

#### 2. **从从节点读取数据**

默认从节点不允许读操作，需设置读权限：

```bash
# 进入从节点容器（如 mongo2）
docker exec -it mongo2 mongosh

# 允许从节点读操作
rs.secondaryOk()

# 查询数据
use testdb
db.users.find()
```

**输出**：

```bash
[
  { _id: ObjectId("..."), name: 'Alice', age: 30 }
]
```

------

#### 3. **模拟主节点故障**

手动停止主节点容器，观察副本集自动选举新主节点：

```bash
docker stop mongo1
```

进入剩余节点查看状态：

```bash
docker exec -it mongo2 mongosh
rs.status()
```

**输出**：`mongo2` 或 `mongo3` 成为新的 `PRIMARY`。

------

### 四、**关键操作命令总结**

| **操作**           | **命令**                               |
| :----------------- | :------------------------------------- |
| 查看副本集状态     | `rs.status()`                          |
| 添加节点           | `rs.add("host:port")`                  |
| 移除节点           | `rs.remove("host:port")`               |
| 强制重新配置副本集 | `rs.reconfig(config, { force: true })` |
| 允许从节点读操作   | `rs.secondaryOk()`                     |

### **如果副本集只剩最后一个节点，能否正常工作？**

**结论**：不能正常处理写操作，主节点将自动降级为从节点，副本集进入只读状态。

- **原因**：
  根据多数派原则，3节点副本集需要至少2个节点在线才能维持主节点。当仅剩1个节点时：

  - **写操作**：无法满足“大多数确认”（Write Concern Majority），导致写入失败。
  - **读操作**：若配置了 `secondaryOk`，仍可读取数据，但无高可用保障。

- **模拟验证**：

  1. 停止 `mongo2` 和 `mongo3`：

     ```bash
     docker stop mongo2 mongo3
     ```

  2. 查看 `mongo1` 状态：

     ```js
     rs.status().members
     ```

     输出：

     ```js
     [
       { "name": "mongo1:27017", "stateStr": "SECONDARY" }, // 主节点降级为从节点
       { "name": "mongo2:27017", "stateStr": "(不可达)" },
       { "name": "mongo3:27017", "stateStr": "(不可达)" }
     ]
     ```

  3. 尝试写入数据：

     ```js
     db.test.insertOne({ message: "Hello" });
     ```

     报错：

     ```bash
     MongoError: not master
     ```

## 分片集群

------

### **一、架构概述**

1. **分片（Shard）**
   - 存储实际数据，每个分片为副本集（3节点）确保高可用。
2. **配置服务器（Config Server）**
   - 存储集群元数据（分片信息、数据分布），需为副本集。
3. **查询路由（mongos）**
   - 客户端入口，路由请求到对应分片，支持多实例部署。

------

### **二、环境准备**

#### 1. 创建 Docker 网络

```bash
docker network create mongo-shard-net
```

#### 2. 创建数据目录

```bash
mkdir -p ./mongo-data/{cfg1,cfg2,cfg3,shard1a,shard1b,shard1c,shard2a,shard2b,shard2c,shard3a,shard3b,shard3c}
```

------

### **三、部署配置服务器副本集**

#### 1. 启动配置服务器

```bash
docker run -d --name cfg1 \
  --network mongo-shard-net \
  -v $(pwd)/mongo-data/cfg1:/data/db \
  mongo:6.0 \
  mongod --configsvr --replSet cfg-rs --port 27019 --bind_ip_all

docker run -d --name cfg2 \
  --network mongo-shard-net \
  -v $(pwd)/mongo-data/cfg2:/data/db \
  mongo:6.0 \
  mongod --configsvr --replSet cfg-rs --port 27019 --bind_ip_all

docker run -d --name cfg3 \
  --network mongo-shard-net \
  -v $(pwd)/mongo-data/cfg3:/data/db \
  mongo:6.0 \
  mongod --configsvr --replSet cfg-rs --port 27019 --bind_ip_all
```

#### 2. 初始化副本集

```bash
docker exec -it cfg1 mongosh --port 27019 --eval 'rs.initiate({
  _id: "cfg-rs",
  configsvr: true,
  members: [
    { _id: 0, host: "cfg1:27019" },
    { _id: 1, host: "cfg2:27019" },
    { _id: 2, host: "cfg3:27019" }
  ]
})'
```

**验证**：

```bash
docker exec -it cfg1 mongosh --port 27019 --eval 'rs.status()'
```

------

### **四、部署分片副本集**

#### 1. 启动分片1（Shard1）

```bash
docker run -d --name shard1a \
  --network mongo-shard-net \
  -v $(pwd)/mongo-data/shard1a:/data/db \
  mongo:6.0 \
  mongod --shardsvr --replSet shard1-rs --port 27018 --bind_ip_all

docker run -d --name shard1b \
  --network mongo-shard-net \
  -v $(pwd)/mongo-data/shard1b:/data/db \
  mongo:6.0 \
  mongod --shardsvr --replSet shard1-rs --port 27018 --bind_ip_all

docker run -d --name shard1c \
  --network mongo-shard-net \
  -v $(pwd)/mongo-data/shard1c:/data/db \
  mongo:6.0 \
  mongod --shardsvr --replSet shard1-rs --port 27018 --bind_ip_all
```

#### 2. 初始化分片1副本集

```bash
docker exec -it shard1a mongosh --port 27018 --eval 'rs.initiate({
  _id: "shard1-rs",
  members: [
    { _id: 0, host: "shard1a:27018" },
    { _id: 1, host: "shard1b:27018" },
    { _id: 2, host: "shard1c:27018" }
  ]
})'
```

**重复上述步骤部署分片2和分片3**。

------

### **五、启动 mongos 路由**

```bash
docker run -d --name mongos \
  --network mongo-shard-net \
  -p 27017:27017 \
  mongo:6.0 \
  mongos --configdb cfg-rs/cfg1:27019,cfg2:27019,cfg3:27019 --bind_ip_all
```

------

### **六、配置分片集群**

#### 1. 添加分片到集群

```bash
docker exec -it mongos mongosh --eval '
  sh.addShard("shard1-rs/shard1a:27018,shard1b:27018,shard1c:27018");
  sh.addShard("shard2-rs/shard2a:27018,shard2b:27018,shard2c:27018");
  sh.addShard("shard3-rs/shard3a:27018,shard3b:27018,shard3c:27018");
'
```

#### 2. 启用分片数据库

```bash
docker exec -it mongos mongosh --eval 'sh.enableSharding("testdb")'
```

#### 3. 创建分片键（哈希分片）

```bash
docker exec -it mongos mongosh --eval '
  sh.shardCollection("testdb.users", { user_id: "hashed" })
'
```

------

### **七、验证分片集群效果**

#### 1. 插入测试数据

```bash
# 进入容器之后执行
docker exec -it mongos mongosh 

use testdb;
for (let i=0; i<100000; i++) {
  db.users.insertOne({ user_id: i, name: "user" + i, value: Math.random() * 1000 });
}
```

#### 2. 查看数据分布

```bash
docker exec -it mongos mongosh --eval 'sh.status()'
```

**输出示例**：

```bash
{
  "shards": [
    { "_id": "shard1-rs", "host": "shard1-rs/shard1a:27018,..." },
    { "_id": "shard2-rs", "host": "shard2-rs/shard2a:27018,..." },
    { "_id": "shard3-rs", "host": "shard3-rs/shard3a:27018,..." }
  ],
  "databases": [
    {
      "_id": "testdb",
      "partitioned": true,
      "primary": "shard1-rs",
      "collections": {
        "testdb.users": {
          "shardKey": { "user_id": "hashed" },
          "chunks": {
            "shard1-rs": 20,
            "shard2-rs": 20,
            "shard3-rs": 20
          }
        }
      }
    }
  ]
}
```

#### 3. 查询路由测试

```bash
# 进入容器执行
docker exec -it mongos mongosh 
use testdb;
db.users.find({ user_id: 12345 }).explain()
```

**输出中关注**：

```js
{
  "queryPlanner": {
    "winningPlan": {
      "stage": "SHARD_MERGE",
      "shards": [
        {
          "shardName": "shard2-rs", // 数据实际位于 shard2
          "stage": "COLLSCAN"
        }
      ]
    }
  }
}
```

------

### **八、核心参数说明**

| **参数**           | **作用**                       |
| :----------------- | :----------------------------- |
| `--configsvr`      | 标记为配置服务器（端口 27019） |
| `--shardsvr`       | 标记为分片节点（端口 27018）   |
| `--bind_ip_all`    | 允许所有 IP 连接               |
| `--replSet <name>` | 指定副本集名称                 |
