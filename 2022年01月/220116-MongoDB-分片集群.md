# 每日一句
Medalist don't grow on trees， you have to nurture them with love, with hard work, with dedication. 
金牌选手不会从天而降，你必须用热爱、刻苦和投入来浇灌他们。

# 概述

分片（sharding）是一种垮多台机器分布数据的方法，MongoDB使用分片来支持具有非常大的数据集和高吞吐量操作的部署。

分片(sharding)是指将数据拆分，将其分散存在不同的机器上的过程。有时也用分区(partitioning)来表示这个概念。将数据分散到不同的机器上，不需要功能强大的大型计算机就可以储存更多的数据，处理更多的负载。

具有大型数据集或高吞吐量应用程序的数据库系统可以会挑战单个服务器的容量。例如，高查询率会耗尽服务器的CPU容量。工作集大小大于系统的RAM会强调磁盘驱动器的I / O容量。

有两种解决系统增长的方法：垂直扩展和水平扩展。

- 垂直扩展意味着增加单个服务器的容量，例如使用更强大的CPU，添加更多RAM或增加存储空间量。可用技术的局限性可能会限制单个机器对于给定工作负载而言足够强大。此外，基于云的提供商基于可用的硬件配置具有硬性上限。结果，垂直缩放有实际的最大值。
- 水平扩展意味着划分系统数据集并加载多个服务器，添加其他服务器以根据需要增加容量。虽然单个机器的总体速度或容量可能不高，但每台机器处理整个工作负载的子集，可能提供比单个高速大容量服务器更高的效率。扩展部署容量只需要根据需要添加额外的服务器，这可能比单个机器的高端硬件的总体成本更低。MongoDB支持通过分片进行水平扩展。



# 组件

MongoDB分片群集包含以下组件：

- 分片（存储）：每个分片包含分片数据的子集。每个分片都可以部署为副本集。
- Mongos（路由）：mongos充当查询路由器，在客户端应用程序和分片集群之间提供接口。
- config servers：配置服务器存储集群的元数据和配置设置。从MongoDB3.4开始，必须将配置服务器部署为副本集（CSRS）

下图描述了分片集群中组件的交互：


MongoDB在集合级别对数据进行分片，将集合数据分布在集群中的分片上。



# 实例

两个分片节点副本集（3+3）+一个配置节点副本集（3）+两个路由节点（2），共11个服务节点。



## 分片节点副本集的创建

所有的的配置文件都直接放到 sharded_cluster 的相应的子目录下面，默认配置文件名字：mongod.conf



### 第一套副本集

1 准备存放数据和日志的目录

```
#-----------myshardrs01 
mkdir -p /mongodb/sharded_cluster/myshardrs01_27018/log \ & 
mkdir -p /mongodb/sharded_cluster/myshardrs01_27018/data/db \ & 
mkdir -p /mongodb/sharded_cluster/myshardrs01_27118/log \ & 
mkdir -p /mongodb/sharded_cluster/myshardrs01_27118/data/db \ & 
mkdir -p /mongodb/sharded_cluster/myshardrs01_27218/log \ & 
mkdir -p /mongodb/sharded_cluster/myshardrs01_27218/data/db
```

2 新建或修改配置文件：`vim /mongodb/sharded_cluster/myshardrs01_27018/mongod.conf`

```
systemLog: 
  #MongoDB发送所有日志输出的目标指定为文件 
  destination: file
  #mongod或mongos应向其发送所有诊断日志记录信息的日志文件的路径 
  path: "/mongodb/sharded_cluster/myshardrs01_27018/log/mongod.log" 
  #当mongos或mongod实例重新启动时，mongos或mongod会将新条目附加到现有日志文件的末尾。 
  logAppend: true 
storage: 
  #mongod实例存储其数据的目录。storage.dbPath设置仅适用于mongod。 
  dbPath: "/mongodb/sharded_cluster/myshardrs01_27018/data/db" 
  journal: 
    #启用或禁用持久性日志以确保数据文件保持有效和可恢复。 
    enabled: true 
processManagement: 
  #启用在后台运行mongos或mongod进程的守护进程模式。 
  fork: true 
  #指定用于保存mongos或mongod进程的进程ID的文件位置，其中mongos或mongod将写入其PID 
  pidFilePath: "/mongodb/sharded_cluster/myshardrs01_27018/log/mongod.pid" 
net:
  #服务实例绑定所有IP，有副作用，副本集初始化的时候，节点名字会自动设置为本地域名，而不是ip 
  #bindIpAll: true 
  #服务实例绑定的IP 
  bindIp: localhost,192.168.0.2 
  #bindIp 
  #绑定的端口 
  port: 27018 
replication: 
  #副本集的名称 
  replSetName: myshardrs01 
sharding: 
  #分片角色 
  clusterRole: shardsvr
```

sharding.clusterRole：

|||
|-|-|
|Value|Description|
|configsvr|Start this instance as a [config server](https://docs.mongodb.com/manual/reference/glossary/#term-config-server). The instance starts on port 27019 by default.|
|shardsvr|Start this instance as a [shard](https://docs.mongodb.com/manual/reference/glossary/#term-shard). The instance starts on port 27018 by default.|


注意：

设置sharding.clusterRole需要mongod实例运行复制。 要将实例部署为副本集成员，请使用

replSetName设置并指定副本集的名称。

3 新建或修改配置文件: `vim /mongodb/sharded_cluster/myshardrs01_27118/mongod.conf`

```
systemLog: 
  #MongoDB发送所有日志输出的目标指定为文件 
  destination: file
  #mongod或mongos应向其发送所有诊断日志记录信息的日志文件的路径 
  path: "/mongodb/sharded_cluster/myshardrs01_27118/log/mongod.log" 
  #当mongos或mongod实例重新启动时，mongos或mongod会将新条目附加到现有日志文件的末尾。 
  logAppend: true 
storage: 
  #mongod实例存储其数据的目录。storage.dbPath设置仅适用于mongod。 
  dbPath: "/mongodb/sharded_cluster/myshardrs01_27118/data/db" 
  journal: 
    #启用或禁用持久性日志以确保数据文件保持有效和可恢复。 
    enabled: true 
processManagement: 
  #启用在后台运行mongos或mongod进程的守护进程模式。 
  fork: true 
  #指定用于保存mongos或mongod进程的进程ID的文件位置，其中mongos或mongod将写入其PID 
  pidFilePath: "/mongodb/sharded_cluster/myshardrs01_27118/log/mongod.pid" 
net:
  #服务实例绑定所有IP，有副作用，副本集初始化的时候，节点名字会自动设置为本地域名，而不是ip 
  #bindIpAll: true 
  #服务实例绑定的IP 
  bindIp: localhost,192.168.0.2 
  #bindIp 
  #绑定的端口 
  port: 27118 
replication: 
  #副本集的名称 
  replSetName: myshardrs01 
sharding: 
  #分片角色 
  clusterRole: shardsvr
```

4 新建或修改配置文件: `vim /mongodb/sharded_cluster/myshardrs01_27218/mongod.conf`

```
systemLog: 
  #MongoDB发送所有日志输出的目标指定为文件 
  destination: file
  #mongod或mongos应向其发送所有诊断日志记录信息的日志文件的路径 
  path: "/mongodb/sharded_cluster/myshardrs01_27218/log/mongod.log" 
  #当mongos或mongod实例重新启动时，mongos或mongod会将新条目附加到现有日志文件的末尾。 
  logAppend: true 
storage: 
  #mongod实例存储其数据的目录。storage.dbPath设置仅适用于mongod。 
  dbPath: "/mongodb/sharded_cluster/myshardrs01_27218/data/db" 
  journal: 
    #启用或禁用持久性日志以确保数据文件保持有效和可恢复。 
    enabled: true 
processManagement: 
  #启用在后台运行mongos或mongod进程的守护进程模式。 
  fork: true 
  #指定用于保存mongos或mongod进程的进程ID的文件位置，其中mongos或mongod将写入其PID 
  pidFilePath: "/mongodb/sharded_cluster/myshardrs01_27218/log/mongod.pid" 
net:
  #服务实例绑定所有IP，有副作用，副本集初始化的时候，节点名字会自动设置为本地域名，而不是ip 
  #bindIpAll: true 
  #服务实例绑定的IP 
  bindIp: localhost,192.168.0.2 
  #bindIp 
  #绑定的端口 
  port: 27218 
replication: 
  #副本集的名称 
  replSetName: myshardrs01 
sharding: 
  #分片角色 
  clusterRole: shardsvr
```

5 启动第一套副本集：一主一副本一仲裁

依次启动三个mongod服务:

```
/usr/yltrcc/mongodb/bin/mongod -f /mongodb/sharded_cluster/myshardrs01_27018/mongod.conf

/usr/yltrcc/mongodb/bin/mongod -f /mongodb/sharded_cluster/myshardrs01_27118/mongod.conf

/usr/yltrcc/mongodb/bin/mongod -f /mongodb/sharded_cluster/myshardrs01_27218/mongod.conf

```



6 初始化副本集和创建主节点

使用客户端命令连接任意一个节点，但这里尽量要连接主节点: `/usr/yltrcc/mongodb/bin/mongo --host 180.76.159.126 --port 27018`

执行命令：

```
# 初始化副本集
> rs.initiate()

# 查看副本集情况
myshardrs01:SECONDARY> rs.status()

# 主节点配置查看
myshardrs01:PRIMARY> rs.conf()

```



7 添加副本节点和仲裁节点

```
# 添加从节点
myshardrs01:PRIMARY> rs.add("180.76.159.126:27118")

# 添加仲裁节点
myshardrs01:PRIMARY> rs.addArb("180.76.159.126:27218")

# 查看配置情况
myshardrs01:PRIMARY> rs.conf()

```



### 第二套副本集

1 准备存放数据和日志的目录

```
#-----------myshardrs01 
mkdir -p /mongodb/sharded_cluster/myshardrs01_27318/log \ & 
mkdir -p /mongodb/sharded_cluster/myshardrs01_27318/data/db \ & 
mkdir -p /mongodb/sharded_cluster/myshardrs01_27418/log \ & 
mkdir -p /mongodb/sharded_cluster/myshardrs01_27418/data/db \ & 
mkdir -p /mongodb/sharded_cluster/myshardrs01_27518/log \ & 
mkdir -p /mongodb/sharded_cluster/myshardrs01_27518/data/db
```

2 新建或修改配置文件：`vim /mongodb/sharded_cluster/myshardrs01_27318/mongod.conf`

```
systemLog: 
  #MongoDB发送所有日志输出的目标指定为文件 
  destination: file
  #mongod或mongos应向其发送所有诊断日志记录信息的日志文件的路径 
  path: "/mongodb/sharded_cluster/myshardrs01_27318/log/mongod.log" 
  #当mongos或mongod实例重新启动时，mongos或mongod会将新条目附加到现有日志文件的末尾。 
  logAppend: true 
storage: 
  #mongod实例存储其数据的目录。storage.dbPath设置仅适用于mongod。 
  dbPath: "/mongodb/sharded_cluster/myshardrs01_27318/data/db" 
  journal: 
    #启用或禁用持久性日志以确保数据文件保持有效和可恢复。 
    enabled: true 
processManagement: 
  #启用在后台运行mongos或mongod进程的守护进程模式。 
  fork: true 
  #指定用于保存mongos或mongod进程的进程ID的文件位置，其中mongos或mongod将写入其PID 
  pidFilePath: "/mongodb/sharded_cluster/myshardrs01_27318/log/mongod.pid" 
net:
  #服务实例绑定所有IP，有副作用，副本集初始化的时候，节点名字会自动设置为本地域名，而不是ip 
  #bindIpAll: true 
  #服务实例绑定的IP 
  bindIp: localhost,192.168.0.2 
  #bindIp 
  #绑定的端口 
  port: 27318 
replication: 
  #副本集的名称 
  replSetName: myshardrs01 
sharding: 
  #分片角色 
  clusterRole: shardsvr
```

3 新建或修改配置文件: `vim /mongodb/sharded_cluster/myshardrs01_27418/mongod.conf`

```
systemLog: 
  #MongoDB发送所有日志输出的目标指定为文件 
  destination: file
  #mongod或mongos应向其发送所有诊断日志记录信息的日志文件的路径 
  path: "/mongodb/sharded_cluster/myshardrs01_27418/log/mongod.log" 
  #当mongos或mongod实例重新启动时，mongos或mongod会将新条目附加到现有日志文件的末尾。 
  logAppend: true 
storage: 
  #mongod实例存储其数据的目录。storage.dbPath设置仅适用于mongod。 
  dbPath: "/mongodb/sharded_cluster/myshardrs01_27418/data/db" 
  journal: 
    #启用或禁用持久性日志以确保数据文件保持有效和可恢复。 
    enabled: true 
processManagement: 
  #启用在后台运行mongos或mongod进程的守护进程模式。 
  fork: true 
  #指定用于保存mongos或mongod进程的进程ID的文件位置，其中mongos或mongod将写入其PID 
  pidFilePath: "/mongodb/sharded_cluster/myshardrs01_27418/log/mongod.pid" 
net:
  #服务实例绑定所有IP，有副作用，副本集初始化的时候，节点名字会自动设置为本地域名，而不是ip 
  #bindIpAll: true 
  #服务实例绑定的IP 
  bindIp: localhost,192.168.0.2 
  #bindIp 
  #绑定的端口 
  port: 27418 
replication: 
  #副本集的名称 
  replSetName: myshardrs01 
sharding: 
  #分片角色 
  clusterRole: shardsvr
```

4 新建或修改配置文件: `vim /mongodb/sharded_cluster/myshardrs01_27518/mongod.conf`

```
systemLog: 
  #MongoDB发送所有日志输出的目标指定为文件 
  destination: file
  #mongod或mongos应向其发送所有诊断日志记录信息的日志文件的路径 
  path: "/mongodb/sharded_cluster/myshardrs01_27518/log/mongod.log" 
  #当mongos或mongod实例重新启动时，mongos或mongod会将新条目附加到现有日志文件的末尾。 
  logAppend: true 
storage: 
  #mongod实例存储其数据的目录。storage.dbPath设置仅适用于mongod。 
  dbPath: "/mongodb/sharded_cluster/myshardrs01_27518/data/db" 
  journal: 
    #启用或禁用持久性日志以确保数据文件保持有效和可恢复。 
    enabled: true 
processManagement: 
  #启用在后台运行mongos或mongod进程的守护进程模式。 
  fork: true 
  #指定用于保存mongos或mongod进程的进程ID的文件位置，其中mongos或mongod将写入其PID 
  pidFilePath: "/mongodb/sharded_cluster/myshardrs01_27518/log/mongod.pid" 
net:
  #服务实例绑定所有IP，有副作用，副本集初始化的时候，节点名字会自动设置为本地域名，而不是ip 
  #bindIpAll: true 
  #服务实例绑定的IP 
  bindIp: localhost,192.168.0.2 
  #bindIp 
  #绑定的端口 
  port: 27518 
replication: 
  #副本集的名称 
  replSetName: myshardrs01 
sharding: 
  #分片角色 
  clusterRole: shardsvr
```

5 启动第一套副本集：一主一副本一仲裁

依次启动三个mongod服务:

```
/usr/yltrcc/mongodb/bin/mongod -f /mongodb/sharded_cluster/myshardrs01_27318/mongod.conf

/usr/yltrcc/mongodb/bin/mongod -f /mongodb/sharded_cluster/myshardrs01_27418/mongod.conf

/usr/yltrcc/mongodb/bin/mongod -f /mongodb/sharded_cluster/myshardrs01_27518/mongod.conf

```



6 初始化副本集和创建主节点

使用客户端命令连接任意一个节点，但这里尽量要连接主节点: `/usr/yltrcc/mongodb/bin/mongo --host 180.76.159.126 --port 27318`

执行命令：

```
# 初始化副本集
> rs.initiate()

# 查看副本集情况
myshardrs01:SECONDARY> rs.status()

# 主节点配置查看
myshardrs01:PRIMARY> rs.conf()

```



7 添加副本节点和仲裁节点

```
# 添加从节点
myshardrs01:PRIMARY> rs.add("180.76.159.126:27418")

# 添加仲裁节点
myshardrs01:PRIMARY> rs.addArb("180.76.159.126:27518")

# 查看配置情况
myshardrs01:PRIMARY> rs.conf()

```



## 配置节点副本集创建

1 准备存放数据和日志的目录

```
#-----------myshardrs01 
mkdir -p /mongodb/sharded_cluster/myconfigrs_27019/log \ & 
mkdir -p /mongodb/sharded_cluster/myconfigrs_27019/data/db \ & 
mkdir -p /mongodb/sharded_cluster/myconfigrs_27119/log \ & 
mkdir -p /mongodb/sharded_cluster/myconfigrs_27119/data/db \ & 
mkdir -p /mongodb/sharded_cluster/myconfigrs_27219/log \ & 
mkdir -p /mongodb/sharded_cluster/myconfigrs_27219/data/db
```

2 新建或修改配置文件：`vim /mongodb/sharded_cluster/myconfigrs_27019/mongod.conf`

```
systemLog: 
  #MongoDB发送所有日志输出的目标指定为文件 
  destination: file
  #mongod或mongos应向其发送所有诊断日志记录信息的日志文件的路径 
  path: "/mongodb/sharded_cluster/myconfigrs_27019/log/mongod.log" 
  #当mongos或mongod实例重新启动时，mongos或mongod会将新条目附加到现有日志文件的末尾。 
  logAppend: true 
storage: 
  #mongod实例存储其数据的目录。storage.dbPath设置仅适用于mongod。 
  dbPath: "/mongodb/sharded_cluster/myconfigrs_27019/data/db" 
  journal: 
    #启用或禁用持久性日志以确保数据文件保持有效和可恢复。 
    enabled: true 
processManagement: 
  #启用在后台运行mongos或mongod进程的守护进程模式。 
  fork: true 
  #指定用于保存mongos或mongod进程的进程ID的文件位置，其中mongos或mongod将写入其PID 
  pidFilePath: "/mongodb/sharded_cluster/myconfigrs_27019/log/mongod.pid" 
net:
  #服务实例绑定所有IP，有副作用，副本集初始化的时候，节点名字会自动设置为本地域名，而不是ip 
  #bindIpAll: true 
  #服务实例绑定的IP 
  bindIp: localhost,192.168.0.2 
  #bindIp 
  #绑定的端口 
  port: 27019 
replication: 
  #副本集的名称 
  replSetName: myconfigrs
sharding: 
  #分片角色 
  clusterRole: configsvr
```

3 新建或修改配置文件: `vim /mongodb/sharded_cluster/myconfigrs_27119/mongod.conf`

```
systemLog: 
  #MongoDB发送所有日志输出的目标指定为文件 
  destination: file
  #mongod或mongos应向其发送所有诊断日志记录信息的日志文件的路径 
  path: "/mongodb/sharded_cluster/myconfigrs_27119/log/mongod.log" 
  #当mongos或mongod实例重新启动时，mongos或mongod会将新条目附加到现有日志文件的末尾。 
  logAppend: true 
storage: 
  #mongod实例存储其数据的目录。storage.dbPath设置仅适用于mongod。 
  dbPath: "/mongodb/sharded_cluster/myconfigrs_27119/data/db" 
  journal: 
    #启用或禁用持久性日志以确保数据文件保持有效和可恢复。 
    enabled: true 
processManagement: 
  #启用在后台运行mongos或mongod进程的守护进程模式。 
  fork: true 
  #指定用于保存mongos或mongod进程的进程ID的文件位置，其中mongos或mongod将写入其PID 
  pidFilePath: "/mongodb/sharded_cluster/myconfigrs_27119/log/mongod.pid" 
net:
  #服务实例绑定所有IP，有副作用，副本集初始化的时候，节点名字会自动设置为本地域名，而不是ip 
  #bindIpAll: true 
  #服务实例绑定的IP 
  bindIp: localhost,192.168.0.2 
  #bindIp 
  #绑定的端口 
  port: 27119
replication: 
  #副本集的名称 
  replSetName: myconfigrs
sharding: 
  #分片角色 
  clusterRole: configsvr
```

4 新建或修改配置文件: `vim /mongodb/sharded_cluster/myconfigrs_27219/mongod.conf`

```
systemLog: 
  #MongoDB发送所有日志输出的目标指定为文件 
  destination: file
  #mongod或mongos应向其发送所有诊断日志记录信息的日志文件的路径 
  path: "/mongodb/sharded_cluster/myconfigrs_27219/log/mongod.log" 
  #当mongos或mongod实例重新启动时，mongos或mongod会将新条目附加到现有日志文件的末尾。 
  logAppend: true 
storage: 
  #mongod实例存储其数据的目录。storage.dbPath设置仅适用于mongod。 
  dbPath: "/mongodb/sharded_cluster/myconfigrs_27219/data/db" 
  journal: 
    #启用或禁用持久性日志以确保数据文件保持有效和可恢复。 
    enabled: true 
processManagement: 
  #启用在后台运行mongos或mongod进程的守护进程模式。 
  fork: true 
  #指定用于保存mongos或mongod进程的进程ID的文件位置，其中mongos或mongod将写入其PID 
  pidFilePath: "/mongodb/sharded_cluster/myconfigrs_27219/log/mongod.pid" 
net:
  #服务实例绑定所有IP，有副作用，副本集初始化的时候，节点名字会自动设置为本地域名，而不是ip 
  #bindIpAll: true 
  #服务实例绑定的IP 
  bindIp: localhost,192.168.0.2 
  #bindIp 
  #绑定的端口 
  port: 27219 
replication: 
  #副本集的名称 
  replSetName: myconfigrs
sharding: 
  #分片角色 
  clusterRole: configsvr
```

5 启动第一套副本集：一主一副本一仲裁

依次启动三个mongod服务:

```
/usr/yltrcc/mongodb/bin/mongod -f /mongodb/sharded_cluster/myconfigrs_27019/mongod.conf

/usr/yltrcc/mongodb/bin/mongod -f /mongodb/sharded_cluster/myconfigrs_27119/mongod.conf

/usr/yltrcc/mongodb/bin/mongod -f /mongodb/sharded_cluster/myconfigrs_27219/mongod.conf

```



6 初始化副本集和创建主节点

使用客户端命令连接任意一个节点，但这里尽量要连接主节点: `/usr/yltrcc/mongodb/bin/mongo --host 180.76.159.126 --port 27219`

执行命令：

```
# 初始化副本集
> rs.initiate()

# 查看副本集情况
myshardrs01:SECONDARY> rs.status()

# 主节点配置查看
myshardrs01:PRIMARY> rs.conf()

```



7 添加两个副本节点

```
# 添加从节点
myshardrs01:PRIMARY> rs.add("180.76.159.126:27119")
myshardrs01:PRIMARY> rs.add("180.76.159.126:27219")

# 查看配置情况
myshardrs01:PRIMARY> rs.conf()

```



## 路由节点的创建

### 第一个路由节点

1 准备存放数据和日志的目录

```
#-----------mongos01
mkdir -p /mongodb/sharded_cluster/mymongos_27017/log
```

2 新建或修改配置文件：`vi /mongodb/sharded_cluster/mymongos_27017/mongos.conf`

```
systemLog: 
  #MongoDB发送所有日志输出的目标指定为文件 
  destination: file
  #mongod或mongos应向其发送所有诊断日志记录信息的日志文件的路径 
  path: "/mongodb/sharded_cluster/mymongos_27017/log/mongod.log" 
  #当mongos或mongod实例重新启动时，mongos或mongod会将新条目附加到现有日志文件的末尾。 
  logAppend: true 
storage: 
  #mongod实例存储其数据的目录。storage.dbPath设置仅适用于mongod。 
  dbPath: "/mongodb/sharded_cluster/mymongos_27017/data/db" 
  journal: 
    #启用或禁用持久性日志以确保数据文件保持有效和可恢复。 
    enabled: true 
processManagement: 
  #启用在后台运行mongos或mongod进程的守护进程模式。 
  fork: true 
  #指定用于保存mongos或mongod进程的进程ID的文件位置，其中mongos或mongod将写入其PID 
  pidFilePath: "/mongodb/sharded_cluster/mymongos_27017/log/mongod.pid" 
net:
  #服务实例绑定所有IP，有副作用，副本集初始化的时候，节点名字会自动设置为本地域名，而不是ip 
  #bindIpAll: true 
  #服务实例绑定的IP 
  bindIp: localhost,192.168.0.2 
  #bindIp 
  #绑定的端口 
  port: 27017 
sharding: 
  #指定配置节点副本集 
  configDB: myconfigrs/180.76.159.126:27019,180.76.159.126:27119,180.76.159.126:27219
```



3 启动mongod服务:

```
/usr/yltrcc/mongodb/bin/mongod -f /mongodb/sharded_cluster/mymongos_27017/mongos.conf

```



4 客户端登录mongos: `/usr/yltrcc/mongodb/bin/mongo --host 180.76.159.126 --port 27017`

通过路由节点操作，现在只是连接了配置节点，还没有连接分片数据节点，因此无法写入业务数据。



5 在路由节点上进行分片配置操作

**添加分片**：`sh.addShard("IP:Port")` 

```
# 添加第一套副本集
sh.addShard("myshardrs01/192.168.0.2:27018,180.76.159.126:27118,180.76.159.126:2 7218")

# 添加第二套副本集
sh.addShard("myshardrs02/192.168.0.2:27318,180.76.159.126:27418,180.76.159.126:2 7518")

```

提示：如果添加分片失败，需要先手动移除分片，检查添加分片的信息的正确性后，再次添加分片。

移除分片参考(了解)：

```
use admin 
db.runCommand( { removeShard: "myshardrs02" } )
```

**开启分片功能**: `sh.enableSharding("库名")`、`sh.shardCollection("库名.集合名",{"key":1})`

在mongos上的articledb数据库配置sharding

```
sh.enableSharding("articledb")

# 查看分片状态
 sh.status()

```

**集合分片**： 对集合分片，你必须使用 `sh.shardCollection()` 方法指定集合和分片键。

语法格式：`sh.shardCollection(namespace, key, unique)`

参数说明：

||||
|-|-|-|
|Parameter|Type|Description|
|namespace|string|要（分片）共享的目标集合的命名空间，格式： <database>.<collection>|
|key|document|用作分片键的索引规范文档。shard键决定MongoDB如何在shard之间分发文档。除非集合为空，否则索引必须在shard collection命令之前存在。如果集合为空，则MongoDB在对集合进行分片之前创建索引，前提是支持分片键的索引不存在。简单的说：由包含字段和该字段的索引遍历方向的文档组成。|
|unique|boolean|当值为true情况下，片键字段上会限制为确保是唯一索引。哈希策略片键不支持唯一索引。默认是false。|




6 分片后插入数据测试

7 增加另一个路由节点





# 美文佳句

电影散场，影院里的观众皆唏嘘而去。我和太太看到最后，直到音乐的休止落下、字幕拉到最底部。我似乎不能完整记住险象环生的情节，但那印度电影的唯美音乐，和虽然处于隐线却从来没有放弃追逐的爱情，让我喜欢和回味。