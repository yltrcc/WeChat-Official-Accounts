# 概述

MongoDB主要是由文档(document)、集合(collection)、数据库(database)这三部分组成的。类比于mysql的行、表、数据库。



# 体系结构

MYSQL 与 MongoDB对比

![](https://secure1.wostatic.cn/static/m7Hjv5vnPkUZTFqusGfLoY/image.png)

MongoDB与SQL的结构对比详解

|SQL Terms/Concepts|MongoDB Terms/Concepts|解释与说明|
|-|-|-|
|database|database|数据库|
|table|collection|数据库表/集合|
|row|document or BSON document|数据记录行/文档|
|column|field|数据库字段/域|
|index|index|索引|
|table joins|embedded documents and linking|表连接,MongoDB不支持，MongoDB通过嵌入式文档来替代多表连接|
|primary key Specify any unique column or column combination as primary key.|primary key In MongoDB, the primary key is automatically set to the _id field.|主键,MongoDB自动将_id字段设置为主键|
|aggregation (e.g. group by)|aggregation pipeline See the SQL to Aggregation Mapping Chart.||




# 数据模型

MongoDB的最小存储单位就是文档(document)对象。文档(document)对象对应于关系型数据库的行。数据在MongoDB中以BSON ( Binary-JSON)文档的格式存储在磁盘上。


BSON ( Binary Serialized Document Format )是一种类json的一种二进制形式的存储格式，简称Binary SON。

BSON和SON一样，支持内嵌的文档对象和数组对象，但是BSON有JSON没有的一些数据类型，如Date和BinData类型。


BSON采用了类似于C语言结构体的名称、对表示方法，支持内嵌的文档对象和数组对象，具有轻量性、可遍历性、高效性的三个特点，可以有效描述非结构化数据和结构化数据。

这种格式的优点是灵活性高，但它的缺点是空间利用率不是很理想。


Bson中，除了基本的SON类型:string integer,boolean,double,nullarray和object , mongo还使用了特殊的数据类型。

这些类型包括date,object idbinary data,regular expression和code。

每一个驱动都以特定语言的方式实现了这些类型，查看你的驱动的文档来获取详细信息。
BSON数据类型参考列表:


|数据类型|描述|举例|
|-|-|-|
|字符串|UTF-8字符串都可表示为字符串类型的数据|{"x" : "foobar"}|
|对象id|对象id是文档的12字节的唯一 ID|{"X" :ObjectId() }|
|布尔值|真或者假：true或者false|{"x":true}+|
|数组|值的集合或者列表可以表示成数组|{"x" ： ["a", "b", "c"]}|
|32位整数|类型不可用。JavaScript仅支持64位浮点数，所以32位整数会被自动转换。|shell是不支持该类型的，shell中默认会转换成64位浮点数|
|64位整数|不支持这个类型。shell会使用一个特殊的内嵌文档来显示64位整数|shell是不支持该类型的，shell中默认会转换成64位浮点数|
|64位浮点数|shell中的数字就是这一种类型|{"x"：3.14159，"y"：3}|
|null|表示空值或者未定义的对象|{"x":null}|
|undefifined|文档中也可以使用未定义类型|{"x":undefifined}|
|符号|shell不支持，shell会将数据库中的符号类型的数据自动转换成字符串||
|正则表达式|文档中可以包含正则表达式，采用JavaScript的正则表达式语法|{"x" ： /foobar/i}|
|代码|文档中还可以包含JavaScript代码|{"x" ： function() { /* …… */ }}|
|二进制数据|二进制数据可以由任意字节的串组成，不过shell中无法使用||
|最大值/最小值|BSON包括一个特殊类型，表示可能的最大值。shell中没有这个类型。||


提示：

shell默认使用64位浮点型数值。{“x”：3.14}或{“x”：3}。对于整型值，可以使用NumberInt（4字节符号整数）或NumberLong（8字节符

号整数），{“x”:NumberInt(“3”)}{“x”:NumberLong(“3”)}