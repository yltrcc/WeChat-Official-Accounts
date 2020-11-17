# 数据库操作

## 查询数据库

### 查看所有数据库

查看所有数据库，可以使用 **`show dbs`** 或者 `show databases`命令

```Bash
> show dbs
admin   0.000GB
config  0.000GB
local   0.000GB
> show databases
```

### 查看当前的数据库

查看当前的数据库名：`db` 命令

```Bash
> db
test
>
```

## 创建或切换数据库

**语法**

MongoDB 创建数据库的语法格式：`use DATABASE_NAME` 如果数据库不存在，则创建数据库，否则切换到指定数据库。

**实例**

以下实例我们创建了数据库 test

```Bash
> use test1
switched to db test1
> db
test
> show dbs
admin   0.000GB
config  0.000GB
local   0.000GB

```

可以看到，我们刚创建的数据库 runoob 并不在数据库的列表中， 要显示它，我们需要向 runoob 数据库插入一些数据。

```Bash
> db.runoob.insert({"name":"菜鸟就是我"})
WriteResult({ "nInserted" : 1 })
> show dbs
admin   0.000GB
config  0.000GB
local   0.000GB
test1   0.000GB
```

MongoDB 中默认的数据库为 test，如果你没有创建新的数据库，集合将存放在 test 数据库中。

## 删除数据库

**语法**

MongoDB 删除数据库的语法格式：`db.dropDatabase()`

**实例**

以下实例我们删除了数据库 test1。

```Bash
# 首先，查看所有数据库
> show dbs
admin   0.000GB
config  0.000GB
local   0.000GB
test    0.000GB
test1   0.000GB
# 切换到数据库 test1
> use test1
switched to db test1
# 执行删除命令
> db.dropDatabase()
{ "ok" : 1 }
# 查看数据库 test1 是否还存在
> show dbs
admin   0.000GB
config  0.000GB
local   0.000GB
test    0.000GB
>
```



# 集合操作

集合，类似关系型数据库中的表。

可以显式的创建，也可以隐式的创建。

## 查看集合

查看当前库中的表/集合：`show tables` 或者 `show collections` 命令

```Bash
> show collections
mycollection
> show tables
mycollection
>

```

## 创建集合

### 显式创建集合

**语法格式**：`db.createCollection(name)`

**参数说明**：

- name - 要创建的集合名称

**举例说明**：创建一个名为 mycollection 的普通集合。

```Bash
> db.createCollection("mycollection")
{ "ok" : 1 }
> show tables
mycollection

```



集合的命名规范参考：集合的命名规范



### 隐式创建集合

当向一个集合中插入一个文档的是够，如果集合不存在，则会自动创建集合。

具体请看：提示

通常我们使用隐式创建文档即可。



## 删除集合

**语法格式**： `db.某个集体集合名.drop()`

**返回值**：如果成功删除选定集合，则 drop() 方法返回 true，否则返回 false。

**示例**：要删除mycollection集合

```Bash
> show tables
mycollection
> db.mycollection.drop()
true
> show tables
>
```



# 文档操作

文档（document）的数据结构和JSON基本一样。

所有存储在集合中的数据都是BSON格式。关于BSON的介绍可以查看：数据模型



## 查询文档

**语法格式**：`db.collection.find(<query>, [projection])`

**参数说明**：

||||
|-|-|-|
|Parameter|Type|Description|
|query|document|可选。使用查询运算符指定选择筛选器。若要返回集合中的所有文档，请省略此参数或传递空文档( {} )。|
|projection|document|可选。指定要在与查询筛选器匹配的文档中返回的字段（投影）。若要返回匹配文档中的所有字段，请省略此参数|


**实例**1：查询所有 `db.comment.find()`或 `db.comment.find({})`

```Bash
> db.comment.find()
{ "_id" : ObjectId("61bfc69ab5617497c0621b00"), ... }
{ "_id" : "1", "articleid" : "100001", ... }
{ "_id" : "2", "articleid" : "100001", ... }
{ "_id" : "3", "articleid" : "100001", ... }
{ "_id" : "4", "articleid" : "100001", ... }
{ "_id" : "5", "articleid" : "100001", ... }
> db.comment.find({})
{ "_id" : ObjectId("61bfc69ab5617497c0621b00"), ... }
{ "_id" : "1", "articleid" : "100001", ... }
{ "_id" : "2", "articleid" : "100001", ... }
{ "_id" : "3", "articleid" : "100001", ... }
{ "_id" : "4", "articleid" : "100001", ... }
{ "_id" : "5", "articleid" : "100001", ... }
# 按一定条件来查询 只需要在find()中添加参数即可
> db.comment.find({userid:'1003'})
{ "_id" : "4", "articleid" : "100001",  "userid" : "1003",  ...  }
{ "_id" : "5", "articleid" : "100001",  "userid" : "1003",  ...  }
>
```

**提示：**每条文档会有一个叫_id的字段，这个相当于我们原来关系数据库中表的主键，当你在插入文档记录时没有指定该字段，MongoDB会自动创建，其类型是ObjectID类型。

**实例2**：投影查询（Projection Query）：要查询结果返回部分指定字段，则需要使用投影查询

```Bash
# 查询结果只显示 _id、userid、nickname 
> db.comment.find({userid:"1003"},{userid:1,nickname:1})
{ "_id" : "4", "userid" : "1003", "nickname" : "凯 撒" }
{ "_id" : "5", "userid" : "1003", "nickname" : "凯撒" }
# 查询结果只显示 、userid、nickname ，不显示 _id
> db.comment.find({userid:"1003"},{userid:1,nickname:1,_id:0})
{ "userid" : "1003", "nickname" : "凯 撒" }
{ "userid" : "1003", "nickname" : "凯撒" }
# 查询所有数据，但只显示 _id、userid、nickname 
> db.comment.find({},{userid:1,nickname:1})
{ "_id" : ObjectId("61bfc69ab5617497c0621b00"), "userid" : "1001", "nickname" : "Rose" }
{ "_id" : "1", "userid" : "1002", "nickname" : "相忘于江湖" }
{ "_id" : "2", "userid" : "1005", "nickname" : "伊人憔 悴" }
{ "_id" : "3", "userid" : "1004", "nickname" : "杰克船 长" }
{ "_id" : "4", "userid" : "1003", "nickname" : "凯 撒" }
{ "_id" : "5", "userid" : "1003", "nickname" : "凯撒" }
>
```







## 插入文档

### 插入单个文档

使用 `insert()` 或 `save()` 方法向集合中插入文档。

**语法格式**：`db.collection.insert( <document or array of documents>, { writeConcern: <document>, ordered: <boolean> } )`

**参数说明**：

||||
|-|-|-|
|**Parameter**|**Type**|**Description**|
|document|document or array|要插入到集合中的文档或文档数组。（(json格式）|
|writeConcern|document|Optional. A document expressing the [write concern](https://docs.mongodb.com/manual/reference/write-concern/). Omit to use the default write concern.See [Write Concern](https://docs.mongodb.com/manual/reference/method/db.collection.insert/#std-label-insert-wc).Do not explicitly set the write concern for the operation if run in a transaction. To use write concern with transactions, see [Transactions and Write Concern](https://docs.mongodb.com/manual/core/transactions/#std-label-transactions-write-concern).|
|ordered|boolean|可选。如果为真，则按顺序插入数组中的文档，如果其中一个文档出现错误，MongoDB将返回而不处理数组中的其余文档。如果为假，则执行无序插入，如果其中一个文档出现错误，则继续处理数组中的主文档。在版本2.6+中默认为true|


**示例**：向comment的集合(表)中插入一条测试数据

```Bash
> db.comment.insert({"articleid":"100000","content":"今天天气真好， 阳光明 媚","userid":"1001","nickname":"Rose","createdatetime":new Date(),"likenum":NumberInt(10),"state":null})
WriteResult({ "nInserted" : 1 })
>
```

**提示**

- comment集合如果不存在，则会隐式创建
- MongoDB中的数数字， 默认情况下是 double 类型，如果要存整型，必须使用函数 `NumberInt(整型数字)` ，否则取出来就有问题了。
- 插入当前日期使用 `new Date()`
- 插入的数据没有指定 `_id` , 会自动生成主键值
- 如果某字段没值，可以赋值为 `null` , 或不写该字段



执行后，如下，说明插入一条数据成功了。

```Bash
WriteResult({ "nInserted" : 1 })

```



**注意**

- 文档中的键/值对是有序的。
- 文档中的值不仅可以是在双引号里面的字符串，还可以是其他几种数据类型（甚至可以是整个嵌入的文档)。
- MongoDB区分类型和大小写
- MongoDB的文档不能有重复的键。文档的键是字符串。除了少数例外情况，键可以使用任意UTF-8字符。



文档键命名规范请看：文档的命名规范



### 批量插入文档

**语法格式**：`db.collection.insertMany( [ <document 1> , <document 2>, ... ], { writeConcern: <document>, ordered: <boolean> } )`

**参数说明**

||||
|-|-|-|
|**Parameter**|**Type**|**Description**|
|document|document or array|要插入到集合中的文档或文档数组。（(json格式）|
|writeConcern|document|Optional. A document expressing the [write concern](https://docs.mongodb.com/manual/reference/write-concern/). Omit to use the default write concern.See [Write Concern](https://docs.mongodb.com/manual/reference/method/db.collection.insert/#std-label-insert-wc).Do not explicitly set the write concern for the operation if run in a transaction. To use write concern with transactions, see [Transactions and Write Concern](https://docs.mongodb.com/manual/core/transactions/#std-label-transactions-write-concern).|
|ordered|boolean|可选。一个布尔值，指定Mongod实例应执行有序插入还是无序插入。默认为true。|


**实例**：批量插入多条文章评论

```Bash
> db.comment.insertMany([ {"_id":"1","articleid":"100001","content":"我们不应该把清晨浪费在手机上，健康很重要，一杯温水幸福你我 他。","userid":"1002","nickname":"相忘于江湖","createdatetime":new Date("2019-08- 05T22:08:15.522Z"),"likenum":NumberInt(1000),"state":"1"}, {"_id":"2","articleid":"100001","content":"我夏天空腹喝凉开水，冬天喝温开水","userid":"1005","nickname":"伊人憔 悴","createdatetime":new Date("2019-08-05T23:58:51.485Z"),"likenum":NumberInt(888),"state":"1"}, {"_id":"3","articleid":"100001","content":"我一直喝凉开水，冬天夏天都喝。","userid":"1004","nickname":"杰克船 长","createdatetime":new Date("2019-08-06T01:05:06.321Z"),"likenum":NumberInt(666),"state":"1"}, {"_id":"4","articleid":"100001","content":"专家说不能空腹吃饭，影响健康。","userid":"1003","nickname":"凯 撒","createdatetime":new Date("2019-08-06T08:18:35.288Z"),"likenum":NumberInt(2000),"state":"1"}, {"_id":"5","articleid":"100001","content":"研究表明，刚烧开的水千万不能喝，因为烫 嘴。","userid":"1003","nickname":"凯撒","createdatetime":new Date("2019-08- 06T11:01:02.521Z"),"likenum":NumberInt(3000),"state":"1"} ]);
{
        "acknowledged" : true,
        "insertedIds" : [
                "1",
                "2",
                "3",
                "4",
                "5"
        ]
}
```

**提示**

- 插入时指定了 _id ，则主键就是该值。
- 如果某条数据插入失败，将会终止插入，但已经插入成功的数据不会回滚掉。

因为批量插入由于数据较多容易出现失败，因此，可以使用try catch进行异常捕捉处理，测试的时候可以不处理。如（了解）：

```Bash
> try { db.comment.insertMany([ {"_id":"1","articleid":"100001","content":"我们不应该把清晨浪费在手机上，健康很重要，一杯温水幸福你我 他。","userid":"1002","nickname":"相忘于江湖","createdatetime":new Date("2019-08- 05T22:08:15.522Z"),"likenum":NumberInt(1000),"state":"1"}, {"_id":"2","articleid":"100001","content":"我夏天空腹喝凉开水，冬天喝温开水","userid":"1005","nickname":"伊人憔 悴","createdatetime":new Date("2019-08-05T23:58:51.485Z"),"likenum":NumberInt(888),"state":"1"}, {"_id":"3","articleid":"100001","content":"我一直喝凉开水，冬天夏天都喝。","userid":"1004","nickname":"杰克船 长","createdatetime":new Date("2019-08-06T01:05:06.321Z"),"likenum":NumberInt(666),"state":"1"}, {"_id":"4","articleid":"100001","content":"专家说不能空腹吃饭，影响健康。","userid":"1003","nickname":"凯 撒","createdatetime":new Date("2019-08-06T08:18:35.288Z"),"likenum":NumberInt(2000),"state":"1"}, {"_id":"5","articleid":"100001","content":"研究表明，刚烧开的水千万不能喝，因为烫 嘴。","userid":"1003","nickname":"凯撒","createdatetime":new Date("2019-08- 06T11:01:02.521Z"),"likenum":NumberInt(3000),"state":"1"} ]); } catch (e) { print (e); }
BulkWriteError({
"writeErrors" : [
  {
    "index" : 0,
    "code" : 11000,
    "errmsg" : "E11000 duplicate key error collection: test1.comment index: _id_ dup key: { _id: \"1\" }",
    "op" : {
      "_id" : "1",
      "articleid" : "100001",
      "content" : "我们不应该把清晨浪费在手机上，健康很重要，一杯温水幸福你我 他。",
      "userid" : "1002",
      "nickname" : "相忘于江湖",
      "createdatetime" : ISODate("0NaN-NaN-NaNTNaN:NaN:NaNZ"),
      "likenum" : NumberInt(1000),
      "state" : "1"
    }
  }
],
"writeConcernErrors" : [ ],
"nInserted" : 0,
"nUpserted" : 0,
"nMatched" : 0,
"nModified" : 0,
"nRemoved" : 0,
"upserted" : [ ]
})
>
```



## 更新文档

**语法格式**: `db.collection.update(query, update, options)`  或者 `db.collection.update( <query>, <update>, { upsert: <boolean>, multi: <boolean>, writeConcern: <document>, collation: <document>, arrayFilters: [ <filterdocument1>, ... ], hint: <document|string> // Available starting in MongoDB 4.2 } )`

**参数**



**提示**：主要关注前四个参数即可



**实例**

```Bash
# 覆盖修改，将删除执行语句中未指定的其他的字段
> db.comment.update({_id:"1"},{likenum:NumberInt(1001)})
WriteResult({ "nMatched" : 1, "nUpserted" : 0, "nModified" : 1 })
> db.comment.find({_id:"1"})
{ "_id" : "1", "likenum" : 1001 }
>

# 局部修改，为了解决上面的问题，我们需要使用修改器$set来实现
> db.comment.update({_id:"2"},{$set:{likenum:NumberInt(889)}})
WriteResult({ "nMatched" : 1, "nUpserted" : 0, "nModified" : 1 })
> db.comment.find({_id:"2"})
{ "_id" : "2", "articleid" : "100001", ... }
>

# 批量修改 更新用户为 1003 的用户的昵称为 凯撒大帝 
## 默认只更新第一条数据
> db.comment.update({userid:"1003"},{$set:{nickname:"凯撒2"}})
WriteResult({ "nMatched" : 1, "nUpserted" : 0, "nModified" : 1 })
> db.comment.find({userid:"1003"})
{ "_id" : "4", "articleid" : "100001", "nickname" : "凯撒2", ... }
{ "_id" : "5", "articleid" : "100001", "nickname" : "凯撒", ... }
>

## 修改所有符合条件的数据，需要指定参数 {multi:true}
> db.comment.update({userid:"1003"},{$set:{nickname:"凯撒大帝"}},{multi:true})
WriteResult({ "nMatched" : 2, "nUpserted" : 0, "nModified" : 2 })
> db.comment.find({userid:"1003"})
{ "_id" : "4", "articleid" : "100001", "nickname" : "凯撒大帝", ... }
{ "_id" : "5", "articleid" : "100001", "nickname" : "凯撒大帝", ... }
>

# 列值增长的修改：如果我们想实现对某列值在原有值的基础上进行增加或减少，可以使用 $inc 运算符来实现。
## 对3号数据的点赞数，每次递增1
> db.comment.find({_id:"3"})
{ "_id" : "3", "likenum" : 667, ... }
> db.comment.update({_id:"3"},{$inc:{likenum:NumberInt(1)}})
WriteResult({ "nMatched" : 1, "nUpserted" : 0, "nModified" : 1 })
> db.comment.find({_id:"3"})
{ "_id" : "3", "likenum" : 668, ... }
>

```



## 删除文档

**语法格式**：`db.集合名称.remove(条件)`

**实例**

```Bash
# 删除_id=1的记录
> db.comment.remove({_id:"1"})
WriteResult({ "nRemoved" : 1 })
> db.comment.find({_id:"1"})
>

# 可以将数据全部删除，请慎用
> db.comment.find({})
{ "_id" : ObjectId("61bfc69ab5617497c0621b00"), "articleid" : "100000", ... }
{ "_id" : "2", "articleid" : "100001", ... }
{ "_id" : "3", "articleid" : "100001", ... }
{ "_id" : "4", "articleid" : "100001", ... }
{ "_id" : "5", "articleid" : "100001", ... }
> db.comment.remove({})
WriteResult({ "nRemoved" : 5 })
> db.comment.find({})
>

```



# 更多查询方式

上面介绍了关于文档的一些基本查询，我们接着学习一些关于查询的使用方式。

## 统计查询

统计查询使用 `count()` 方法。

**语法格式**：`db.collection.count(query, options)`

**参数说明**：

||||
|-|-|-|
|**Parameter**|**Type**|**Description**|
|query|document|查询选择条件。|
|options|document|可选。用于修改计数的额外选项。|


**实例**

```Bash
# 统计所有记录数
> db.comment.count()
5
>

# 按条件统计：统计userid为1003的记录条数
> db.comment.count({userid:"1003"})
2
>

```



## 分页列表查询

可以使用`limit()`方法来读取指定数量的数据，使用`skip()` 方法来跳过指定数量的数据。

**语法格式**：`db.COLLECTION_NAME.find().limit(NUMBER).skip(NUMBER)`

**实例**

```Bash
# 返回指定条数的记录:调用limit来返回结果(TopN)，默认值20
> db.comment.find().limit(3)
{ "_id" : "1", "articleid" : "100001", ... }
{ "_id" : "2", "articleid" : "100001", ... }
{ "_id" : "3", "articleid" : "100001", ... }
>

# skip方法同样接受一个数字参数作为跳过的记录条数。（前N个不要）,默认值是0
> db.comment.count()
5
> db.comment.find().skip(3)
{ "_id" : "4", "articleid" : "100001", ...}
{ "_id" : "5", "articleid" : "100001", ...}
>


# 分页查询:每页2个，第二页开始：跳过前两条数据，接着值显示3和4条数据
> db.comment.find().skip(0).limit(2)
{ "_id" : "1", "articleid" : "100001", ....}
{ "_id" : "2", "articleid" : "100001", ....}
> db.comment.find().skip(2).limit(2)
{ "_id" : "3", "articleid" : "100001", ....}
{ "_id" : "4", "articleid" : "100001", ....}
> db.comment.find().skip(4).limit(2)
{ "_id" : "5", "articleid" : "100001", ....}
>

```



## 排序查询

`sort()` 方法对数据进行排序，sort() 方法可以通过参数指定排序的字段，并使用 1 和 -1 来指定排序的方式，其中 1 为升序排列，而 -1 是用于降序排列。

**语法格式**：`db.COLLECTION_NAME.find().sort({KEY:1})`

**实例**

```Bash
# 对userid和访问量进行排列
> db.comment.find().sort({userid:-1,likenum:1})
{ "_id" : "2", "userid" : "1005", "likenum" : 888, ...}
{ "_id" : "3", "userid" : "1004", "likenum" : 666, ...}
{ "_id" : "4", "userid" : "1003", "likenum" : 2000, ...}
{ "_id" : "5", "userid" : "1003", "likenum" : 3000, ...}
{ "_id" : "1", "userid" : "1002", "likenum" : 1000, ...}
> db.comment.find().sort({likenum:1})
{ "_id" : "3", "userid" : "1004", "likenum" : 666, ...}
{ "_id" : "2", "userid" : "1005", "likenum" : 888, ...}
{ "_id" : "1", "userid" : "1002", "likenum" : 1000, ...}
{ "_id" : "4", "userid" : "1003", "likenum" : 2000, ...}
{ "_id" : "5", "userid" : "1003", "likenum" : 3000, ...}
> db.comment.find().sort({userid:-1,likenum:-1})
{ "_id" : "2", "userid" : "1005", "likenum" : 888, ...}
{ "_id" : "3", "userid" : "1004", "likenum" : 666, ...}
{ "_id" : "5", "userid" : "1003", "likenum" : 3000, ...}
{ "_id" : "4", "userid" : "1003", "likenum" : 2000, ...}
{ "_id" : "1", "userid" : "1002", "likenum" : 1000, ...}
>

```

**提示**：`skip()`, `limilt()`, `sort()`三个放在一起执行的时候，执行的顺序是先 `sort()`, 然后是 `skip()`，最后是显示的 `limit()`，和命令编写顺序无关。



## 正则条件查询

MongoDB的模糊查询是通过正则表达式的方式实现的。

**语法格式**：`db.集合.find({字段:/正则表达式/})`

**提示**：正则表达式是js的语法，直接量的写法。

**实例**

```Bash
# 查询评论内容包含“开水”的所有文档
> db.comment.find({content:/开水/})
{ "_id" : "2", "articleid" : "100001", "content" : "我夏天空腹喝凉开水，冬天喝温开水", ... }
{ "_id" : "3", "articleid" : "100001", "content" : "我一直喝凉开水，冬天夏天都喝。", ... }

# 查询评论的内容中以“专家”开头的
> db.comment.find({content:/^专家/})
{ "_id" : "4", "articleid" : "100001", "content" : "专家说不能空腹吃饭，影响健康。", ... }
>
```



## 比较查询

<, <=, >, >= 这个操作符也是很常用的。

**语法格式**

```Bash
db.集合名称.find({ "field" : { $gt: value }}) // 大于: field > value 
db.集合名称.find({ "field" : { $lt: value }}) // 小于: field < value 
db.集合名称.find({ "field" : { $gte: value }}) // 大于等于: field >= value 
db.集合名称.find({ "field" : { $lte: value }}) // 小于等于: field <= value 
db.集合名称.find({ "field" : { $ne: value }}) // 不等于: field != value
```

**实例**

```Bash
# 查询评论点赞数量大于700的记录
> db.comment.find({likenum:{$gt:NumberInt(700)}})
{ "_id" : "1", "articleid" : "100001", ... , "likenum" : 1000, "state" : "1" }
{ "_id" : "2", "articleid" : "100001", ... , "likenum" : 888, "state" : "1" }
{ "_id" : "4", "articleid" : "100001", ... , "likenum" : 2000, "state" : "1" }
{ "_id" : "5", "articleid" : "100001", ... , "likenum" : 3000, "state" : "1" }
>

```



## 条件连接查询

我们如果需要查询同时满足两个以上条件，需要使用`$and`操作符将条件进行关联。（相 当于SQL的and）

如果两个以上条件之间是或者的关系，我们使用 `$or` 操作符进行关联

**语法格式**：`$and:[ { },{ },{ } ]` 、`$or:[ { },{ },{ } ]`

**实例**

```Bash
# 查询评论集合中likenum大于等于700 并且小于2000的文档
> db.comment.find({$and:[{likenum:{$gte:NumberInt(700)}},{likenum:{$lt:NumberInt(2000)}}]})
{ "_id" : "1", "articleid" : "100001", ... , "likenum" : 1000, "state" : "1" }
{ "_id" : "2", "articleid" : "100001", ... , "likenum" : 888, "state" : "1" }

# 查询评论集合中userid为1003，或者点赞数小于1000的文档记录
> db.comment.find({$or:[ {userid:"1003"} ,{likenum:{$lt:1000} }]})
{ "_id" : "2", ... , "userid" : "1005", "likenum" : 888, "state" : "1" }
{ "_id" : "3", ... , "userid" : "1004", "likenum" : 666, "state" : "1" }
{ "_id" : "4", ... , "userid" : "1003", "likenum" : 2000, "state" : "1" }
{ "_id" : "5", ... , "userid" : "1003", "likenum" : 3000, "state" : "1" }
>

```