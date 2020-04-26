# 概述

MongoDB中的副本集（Replica Set）是一组维护相同数据集的mongod服务。副本集可提供冗余和高可用性，是所有生产部署的基础。

副本集类似于有自动故障恢复功能的主从集群。通俗的讲就是用多台机器进行同一数据的异步同步，从而使多台机器拥有同一数据的多个副本，并且当主库当掉时在不需要用户干预的情况下自动切换其他备份服务器做主库。而且还可以利用副本服务器做只读服务器，实现读写分离，提高负载。

## 冗余和数据可用性

复制提供冗余并提高数据可用性。 通过在不同数据库服务器上提供多个数据副本，复制可提供一定级别的容错功能，以防止丢失单个数据库服务器。

在某些情况下，复制可以提供增加的读取性能，因为客户端可以将读取操作发送到不同的服务上，在不同数据中心维护数据副本可以增加分布式应用程序的数据位置和可用性。

您还可以为专用目的维护其他副本，例如灾难恢复，报告或备份。

## MongoDB中的复制

副本集是一组维护相同数据集的mongod实例。

副本集包含多个数据承载节点和可选的一个仲裁节点。在承载数据的节点中，一个且仅一个成员被视为主节点，而其他节点被视为次要（从）节点。主节点接收所有写操作。

副本集只能有一个主要能够确认具有`{w：“most”}`写入关注的写入;

虽然在某些情况下，另一个mongod实例可能暂时认为自己也是主要的。主要记录其操作日志中的数据集的所有更改，即oplog。

![](https://secure1.wostatic.cn/static/fztErgww8Sxo7PKBUx1aP7/image.png)

辅助(副本)节点复制主节点的oplog并将操作应用于其数据集，以使辅助节点的数据集反映主节点的数据集。 如果主要人员不在，则符合条件的中学将举行选举以选出新的主要人员。



## 主从复制和副本集区别

主从集群和副本集最大的区别就是副本集没有固定的“主节点”；

整个集群会选出一个“主节点”，当其挂掉后，又在剩下的从节点中选中其他节点为“主节点”，副本集总有一个活跃点(主、primary)和一个或多个备份节点(从、secondary)。



# 副本集的三个角色

副本集有两种类型三种角色

**两种类型**：

- 主节点（Primary）类型：数据操作的主要连接点，可读写。
- 次要（辅助、从）节点（Secondaries）类型：数据冗余备份节点，可以读或选举。

**三种角色**：

- 主要成员（Primary）：主要接收所有写操作。就是主节点。
- 副本成员（Replicate）：从主节点通过复制操作以维护相同的数据集，即备份数据，不可写操作，但可以读操作（但需要配置）。是默认的一种从节点类型。
- 仲裁者（Arbiter）：不保留任何数据的副本，只具有投票选举作用。当然也可以将仲裁服务器维护为副本集的一部分，即副本成员同时也可以是仲裁者。也是一种从节点类型。

![](https://secure1.wostatic.cn/static/cVktsd7XZSpaYbTAUxhZ3o/image.png)

**关于仲裁者的额外说明：**

您可以将额外的mongod实例添加到副本集作为仲裁者。

仲裁者不维护数据集。 仲裁者的目的是通过响应其他副本集成员的心跳和选举请求来维护副本集中的仲裁。 因为它们不存储数据集，所以仲裁器可以是提供副本集仲裁功能的好方法，其资源成本比具有数据集的全功能副本集成员更便宜。

如果您的副本集具有偶数个成员，请添加仲裁者以获得主要选举中的“大多数”投票。 仲裁者不需要专用硬件。

仲裁者将永远是仲裁者，而主要人员可能会退出并成为次要人员，而次要人员可能成为选举期间的主要人员。

如果你的副本+主节点的个数是偶数，建议加一个仲裁者，形成奇数，容易满足大多数的投票。如果你的副本+主节点的个数是奇数，可以不加仲裁者。



# 实例：一主三从一仲裁

通过实例来演示这种架构的搭建。

![](https://secure1.wostatic.cn/static/8mZit96Lqi5ttxcGmJT7AG/image.png)

## 创建主节点

提示：在创建主节点之前记得保证没有mongd进程在运行中。

### 创建目录：存放数据和日志

```Bash
mkdir -p /usr/yltrcc/mongodb/replica_sets/myrs_27017/data/db
mkdir -p /usr/yltrcc/mongodb/replica_sets/myrs_27017/log

```

### 新建或修改配置文件

```Bash
vim /usr/yltrcc/mongodb/replica_sets/myrs_27017/mongod.conf

# 添加如下内容
systemLog:
  #MongoDB发送所有日志输出的目标指定为文件
  destination: file
  #mongod或mongos应向其发送所有诊断日志记录信息的日志文件的路径
  path: "/usr/yltrcc/mongodb/replica_sets/myrs_27017/log/mongod.log"
  #当mongos或mongod实例重新启动时，mongos或mongod会将新条目附加到现有日志文件的末尾。
  logAppend: true
storage:
  #mongod实例存储其数据的目录。storage.dbPath设置仅适用于mongod。
  dbPath: "/usr/yltrcc/mongodb/replica_sets/myrs_27017/data/db"
  journal:
    #启用或禁用持久性日志以确保数据文件保持有效和可恢复。
    enabled: true
processManagement:
  #启用在后台运行mongos或mongod进程的守护进程模式。
  fork: true
  #指定用于保存mongos或mongod进程的进程ID的文件位置，其中mongos或mongod将写入其PID
  pidFilePath: "/usr/yltrcc/mongodb/replica_sets/myrs_27017/log/mongod.pid"
net:
  #服务实例绑定所有IP，有副作用，副本集初始化的时候，节点名字会自动设置为本地域名，而不是ip #bindIp
  #All: true
  #服务实例绑定的IP
  bindIp: localhost,192.168.20.131
  #bindIp #绑定的端口
  port: 27017
replication:
  #副本集的名称
  replSetName: myrs

```

### 启动节点服务

```
/usr/yltrcc/mongodb/bin/mongod -f /usr/yltrcc/mongodb/replica_sets/myrs_27017/mongod.conf
```



## 创建从节点1

### 创建目录：存放数据和日志

```Bash
mkdir -p /usr/yltrcc/mongodb/replica_sets/myrs_27018/data/db
mkdir -p /usr/yltrcc/mongodb/replica_sets/myrs_27018/log

```

### 新建或修改配置文件

```Bash
vim /usr/yltrcc/mongodb/replica_sets/myrs_27018/mongod.conf

# 添加如下内容
systemLog:
  #MongoDB发送所有日志输出的目标指定为文件
  destination: file
  #mongod或mongos应向其发送所有诊断日志记录信息的日志文件的路径
  path: "/usr/yltrcc/mongodb/replica_sets/myrs_27018/log/mongod.log"
  #当mongos或mongod实例重新启动时，mongos或mongod会将新条目附加到现有日志文件的末尾。
  logAppend: true
storage:
  #mongod实例存储其数据的目录。storage.dbPath设置仅适用于mongod。
  dbPath: "/usr/yltrcc/mongodb/replica_sets/myrs_27018/data/db"
  journal:
    #启用或禁用持久性日志以确保数据文件保持有效和可恢复。
    enabled: true
processManagement:
  #启用在后台运行mongos或mongod进程的守护进程模式。
  fork: true
  #指定用于保存mongos或mongod进程的进程ID的文件位置，其中mongos或mongod将写入其PID
  pidFilePath: "/usr/yltrcc/mongodb/replica_sets/myrs_27018/log/mongod.pid"
net:
  #服务实例绑定所有IP，有副作用，副本集初始化的时候，节点名字会自动设置为本地域名，而不是ip #bindIp
  #All: true
  #服务实例绑定的IP
  bindIp: localhost,192.168.20.131
  #bindIp #绑定的端口
  port: 27018
replication:
  #副本集的名称
  replSetName: myrs

```

### 启动节点服务

```
/usr/yltrcc/mongodb/bin/mongod -f /usr/yltrcc/mongodb/replica_sets/myrs_27018/mongod.conf
```



## 创建从节点2

### 创建目录：存放数据和日志

```Bash
mkdir -p /usr/yltrcc/mongodb/replica_sets/myrs_27019/data/db
mkdir -p /usr/yltrcc/mongodb/replica_sets/myrs_27019/log

```

### 新建或修改配置文件

```Bash
vim /usr/yltrcc/mongodb/replica_sets/myrs_27019/mongod.conf

# 添加如下内容
systemLog:
  #MongoDB发送所有日志输出的目标指定为文件
  destination: file
  #mongod或mongos应向其发送所有诊断日志记录信息的日志文件的路径
  path: "/usr/yltrcc/mongodb/replica_sets/myrs_27019/log/mongod.log"
  #当mongos或mongod实例重新启动时，mongos或mongod会将新条目附加到现有日志文件的末尾。
  logAppend: true
storage:
  #mongod实例存储其数据的目录。storage.dbPath设置仅适用于mongod。
  dbPath: "/usr/yltrcc/mongodb/replica_sets/myrs_27019/data/db"
  journal:
    #启用或禁用持久性日志以确保数据文件保持有效和可恢复。
    enabled: true
processManagement:
  #启用在后台运行mongos或mongod进程的守护进程模式。
  fork: true
  #指定用于保存mongos或mongod进程的进程ID的文件位置，其中mongos或mongod将写入其PID
  pidFilePath: "/usr/yltrcc/mongodb/replica_sets/myrs_27019/log/mongod.pid"
net:
  #服务实例绑定所有IP，有副作用，副本集初始化的时候，节点名字会自动设置为本地域名，而不是ip #bindIp
  #All: true
  #服务实例绑定的IP
  bindIp: localhost,192.168.20.131
  #bindIp #绑定的端口
  port: 27019
replication:
  #副本集的名称
  replSetName: myrs

```

### 启动节点服务

```
/usr/yltrcc/mongodb/bin/mongod -f /usr/yltrcc/mongodb/replica_sets/myrs_27019/mongod.conf
```





## 创建从节点3

### 创建目录：存放数据和日志

```Bash
mkdir -p /usr/yltrcc/mongodb/replica_sets/myrs_27020/data/db
mkdir -p /usr/yltrcc/mongodb/replica_sets/myrs_27020/log

```

### 新建或修改配置文件

```Bash
vim /usr/yltrcc/mongodb/replica_sets/myrs_27020/mongod.conf

# 添加如下内容
systemLog:
  #MongoDB发送所有日志输出的目标指定为文件
  destination: file
  #mongod或mongos应向其发送所有诊断日志记录信息的日志文件的路径
  path: "/usr/yltrcc/mongodb/replica_sets/myrs_27020/log/mongod.log"
  #当mongos或mongod实例重新启动时，mongos或mongod会将新条目附加到现有日志文件的末尾。
  logAppend: true
storage:
  #mongod实例存储其数据的目录。storage.dbPath设置仅适用于mongod。
  dbPath: "/usr/yltrcc/mongodb/replica_sets/myrs_27020/data/db"
  journal:
    #启用或禁用持久性日志以确保数据文件保持有效和可恢复。
    enabled: true
processManagement:
  #启用在后台运行mongos或mongod进程的守护进程模式。
  fork: true
  #指定用于保存mongos或mongod进程的进程ID的文件位置，其中mongos或mongod将写入其PID
  pidFilePath: "/usr/yltrcc/mongodb/replica_sets/myrs_27020/log/mongod.pid"
net:
  #服务实例绑定所有IP，有副作用，副本集初始化的时候，节点名字会自动设置为本地域名，而不是ip #bindIp
  #All: true
  #服务实例绑定的IP
  bindIp: localhost,192.168.20.131
  #bindIp #绑定的端口
  port: 27020
replication:
  #副本集的名称
  replSetName: myrs

```

### 启动节点服务

```
/usr/yltrcc/mongodb/bin/mongod -f /usr/yltrcc/mongodb/replica_sets/myrs_27020/mongod.conf
```



## 创建仲裁节点

### 创建目录：存放数据

```Bash
mkdir -p /usr/yltrcc/mongodb/replica_sets/myrs_arb

```

### 启动节点服务

```
/usr/yltrcc/mongodb/bin/mongod --port 27030 --dbpath /usr/yltrcc/mongodb/replica_sets/myrs_arb --replSet myrs --bind_ip 192.168.20.131
```

## 初始化配置副本集和主节点

使用客户端命令连接任意一个节点，但这里尽量要连接主节点(27017节点)

```
/usr/yltrcc/mongodb/bin/mongo --host=192.168.20.131 --port=27017
```

结果，连接上之后，很多命令无法使用，，比如 show dbs 等，必须初始化副本集才行。

使用命令：`rs.initiate()`



## 添加从节点

在主节点添加从节点，将其他成员加入到副本集

命令：`rs.add("192.168.20.131:27018")`



## 添加仲裁从节点

添加一个仲裁节点到副本集

命令：`rs.addArb("192.168.20.131:27030")`



## 设置从节点读操作权限

当前从节点只是一个备份，不是奴隶节点，无法读取数据，写当然更不行。因为默认情况下，从节点是没有读写权限的，可以增加读的权限，但需要进行设置。

使用命令：`rs.slaveOk()`或者 `rs.slaveOk(true)`

取消读权限命令:`rs.slaveOk(false)`

**提示**：注意一定要连接到从节点上，不要连接到主节点上了。



## 答疑

### 1主1从1仲裁 不可以吗？

这种是不用添加仲裁节点的，非仲裁者的数量要大于投票节点的大多数，具体请查看：[https://docs.mongodb.com/manual/reference/write-concern/](https://docs.mongodb.com/manual/reference/write-concern/)



# Spring Data MongoDB 连接副本集

**语法格式**：`mongodb://host1,host2,host3/articledb?connect=replicaSet&slaveOk=true&replicaSet=副本集名字`

**参数说明**：

- `slaveOk=true`：开启副本节点读的功能，可实现读写分离。
- `connect=replicaSet`：自动到副本集中选择读写的主机。如果slaveOK是打开的，则实现了读写分离

**实例**：

连接 replica set 三台服务器 (端口 27017, 27018, 和27019)，直接连接第一个服务器，无论是replicaset一部分或者主服务器或者从服务器，写入操作应用在主服务器 并且分布查询到从服务器。

配置文件application.yml

```
spring:
  #数据源配置
  data:
    mongodb:
      # 副本集的连接字符串
      # uri: mongodb://192.168.40.141:27017,192.168.40.141:27018,192.168.40.141:27019/articledb?connect=replicaSet&slaveOk=true&replicaSet=myrs
      #副本集有认证的情况下，字符串连接
      uri: mongodb://bobo:123456@192.168.40.141:27017,192.168.40.141:27018,192.168.40.141:27019/articledb?connect=replicaSet&slaveOk=true&replicaSet=myrs
```

