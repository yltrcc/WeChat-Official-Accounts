# 每日一句
Sometimes your whole life boils down to one insane move. 
人一生中出人头地的机会不多，一旦有了一定要抓住！

# 概述

默认情况下，MongoDB实例启动运行时是没有启用用户访问权限控制的，也就是说，在实例本机服务器上都可以随意连接到实例进行各种操作，MongoDB不会对连接客户端进行用户验证，这是非常危险的。



可以通过以下的几种方式来保障 MongoDB的安全：

- 使用新的端口，默认的 27017 端口如果一旦知道了IP就能连接上，不太安全
- 设置MongoDB的网络环境。最好将MongoDB部署到公司服务器内网，这样外网是访问不到的。公司内部访问使用VPN等。
- 开启安全认证。认证要同时设置服务器之间的内部认证方式，同时设置客户端连接到集群的账号密码认证方式。



为了强制开启用户访问控制（用户验证），则需要在MongoDB实例启动时使用选项 `--auth` 或在指定启动配置文件中添加选项 `auth=true`



# 相关概念

## 启动访问控制

MongoDB使用的是基于角色的访问控制（Role-Based-Access-Control，RBAC）来管理用户对实例的访问。通过对用户授予一个或多个角色来控制用户访问数据库资源的权限和数据库操作的权限，在对用户分配角色之前，用户无法访问实例。

## 角色

在MongoDB中通过角色对用户授予相应数据库资源的操作权限。每个角色当中的权限可以显式指定，也可以通过继承其他角色的权限，或者两者都存在的权限。

## 权限

权限由指定的数据库资源（resource）以及允许在指定资源上进行的操作（action）组成。

- 资源（resource）：数据库、集合、部分集合和集群
- 操作（action）：对资源进行的增、删、改、查（CRUD）操作



# 角色

在角色定义时可以包含一个或多个已存在的角色，新创建的角色会继承包含的角色所有的权限。在同一个数据库中，新创建角色可以继承其他角色的权限，在 admin 数据库中创建的角色可以继承在其它任意数据库中角色的权限。



## 角色相关命令

```PowerShell
# 查询所有角色权限(仅用户自定义角色)
db.runCommand({ rolesInfo: 1 })

# 查询所有角色权限（包含内置角色）
db.runCommand({ rolesInfo: 1, showBuiltinRoles: true })

# 查询当前数据库中的某角色的权限
db.runCommand({ rolesInfo: "<rolename>" })

# 查询其它数据库中指定的角色权限
db.runCommand({ rolesInfo: { role: "<rolename>", db: "<database>" } }

# 查询多个角色权限
db.runCommand( { rolesInfo: [ "<rolename>", { role: "<rolename>", db: "<database>" }, ... ] } )

```



## 常用的内置角色：

- 数据库用户角色：`read` 、`readWrite`
- 所有数据库用户角色：`readAnyDatabase`、`readWriteAnyDatabase`、`userAdminAnyDatabase`、`dbAdminAnyDatabase`
- 数据库管理角色：`dbAdmin`、`dbOwner`、`userAdmin`；
- 集群管理角色：`clusterAdmin`、`clusterManager`、`clusterMonitor`、`hostManager`；
- 备份恢复角色：`backup`、`restore`；
- 超级用户角色：`root`
- 内部角色：`system`

**角色说明**：

|||
|-|-|
|**角色**|**权限描述**|
|read|可以读取指定数据库中任何数据。|
|readWrite|可以读写指定数据库中任何数据，包括创建、重命名、删除集合。|
|readAnyDatabase|可以读取所有数据库中任何数据(除了数据库confifig和local之外)。|
|readWriteAnyDatabase|可以读写所有数据库中任何数据(除了数据库confifig和local之外)。|
|userAdminAnyDatabase|可以在指定数据库创建和修改用户(除了数据库confifig和local之外)。|
|dbAdminAnyDatabase|可以读取任何数据库以及对数据库进行清理、修改、压缩、获取统计信息、执行检查等操作(除了数据库confifig和local之外)。|
|dbAdmin|可以读取指定数据库以及对数据库进行清理、修改、压缩、获取统计信息、执行检查等操作。|
|userAdmin|可以在指定数据库创建和修改用户。|
|clusterAdmin|可以对整个集群或数据库系统进行管理操作。|
|backup|备份MongoDB数据最小的权限。|
|restore|从备份文件中还原恢复MongoDB数据(除了system.profifile集合)的权限。|
|root|超级账号，超级权限|







# 美文佳句

人生其实就像一场旅行，不必在乎目的地，在乎的是沿途的风景以及看风景的心情。忙碌的生活中懂得适时停下脚步欣赏一路走来的风景，是一种享受，是一种需要，是一种智慧，更是一种对待人生的态度。

花随风落，雨伴云晴，过客匆匆，相逢有期。路过的风景，经过的往事，放在心间就好。感恩生活的所有，珍惜所有的有缘人，努力让自己活得更幸福快乐一点。