# 每日一句
Sometimes it takes going through something so awful to realize the beauty that is out there in this world. 
有时候就是要经历一些糟糕的事情才能意识到世间存在的美丽。

# 概述

本文介绍MongoDB分别在windows和linux平台下的安装与配置和MongoDB服务启动与连接。

# windows安装与配置

MongoDB提供了可用于32位和64位系统的预编译二进制包，你可以从MongoDB官网下载安装，MongoDB预编译二进制包下载地址：[https://www.mongodb.com/try/download/community](https://www.mongodb.com/try/download/community)

> 注意：在 MongoDB 2.2 版本后已经不再支持 Windows XP 系统。最新版本也已经没有了 32 位系统的安装文件。

你也直接可以直接点击链接下载windows 5.0.5版本→[https://fastdl.mongodb.org/windows/mongodb-windows-x86_64-5.0.5-signed.msi](https://fastdl.mongodb.org/windows/mongodb-windows-x86_64-5.0.5-signed.msi)



安装过程中，你可以通过点击 "Custom(自定义)" 按钮来设置你的安装目录。

## 启动服务

进入 bin 目录：`D:\devTools\MongoDB\Server\5.0\bin` 双击`mongo.exe` 浏览器访问：[http://127.0.0.1:27017/](http://127.0.0.1:27017/)

看到页面出现：`It looks like you are trying to access MongoDB over HTTP on the native driver port.` 就说明启动成功了。



## MongoDB Compass

new Connection：输入 `mongodb://127.0.0.1:27017` 就可以连接到本地的MongoDB服务了。

![](https://secure1.wostatic.cn/static/rksTPFQfgsXevTc4Cq8QK2/image.png)

![](https://secure1.wostatic.cn/static/2Yy5KCXzjni6tfmafAy5cB/image.png)

# linux 安装

MongoDB 提供了 linux 各个发行版本 64 位的安装包，你可以在官网下载安装包。

## 前置依赖包

安装前我们需要安装各个 Linux 平台依赖包。

**Red Hat/CentOS：**`sudo yum install libcurl openssl`

**Ubuntu 18.04 LTS ("Bionic")/Debian 10 "Buster"：**`sudo apt-get install libcurl4 openssl`

**Ubuntu 16.04 LTS ("Xenial")/Debian 9 "Stretch"：**`sudo apt-get install libcurl3 openssl`



# 安装并启动

MongoDB 源码下载地址：[https://www.mongodb.com/try/download/community](https://www.mongodb.com/try/download/community)

```Bash
# 创建 安装包存放目录并进入该目录
mkdir -p /usr/yltrcc/mongodb
cd usr/yltrcc/mongodb/
# 下载并解压缩
wget https://fastdl.mongodb.org/linux/mongodb-linux-x86_64-rhel70-5.0.5.tgz
tar -xvf mongodb-linux-x86_64-rhel70-5.0.5.tgz 
# 新建几个目录，分别用来存储数据和日志
mkdir -p /usr/yltrcc/mongodb/mongodb-linux-x86_64-rhel70-5.0.5/single/data/db
mkdir -p /usr/yltrcc/mongodb/mongodb-linux-x86_64-rhel70-5.0.5/single/log
# 新建并修改配置文件
vi /usr/yltrcc/mongodb/mongodb-linux-x86_64-rhel70-5.0.5/single/mongod.conf

# 向 mongod.conf 添加如下内容：
systemLog:
  #MongoDB发送所有日志输出的目标指定为文件
  # #The path of the log file to which mongod or mongos should send all diagnostic logging information
  destination: file
  #mongod或mongos应向其发送所有诊断日志记录信息的日志文件的路径
  path: "/usr/yltrcc/mongodb/mongodb-linux-x86_64-rhel70-5.0.5/single/log/mongod.log"
  #当mongos或mongod实例重新启动时，mongos或mongod会将新条目附加到现有日志文件的末尾。
  logAppend: true
storage:
  #mongod实例存储其数据的目录。storage.dbPath设置仅适用于mongod。
  ##The directory where the mongod instance stores its data.Default Value is "/data/db".
  dbPath: "/usr/yltrcc/mongodb/mongodb-linux-x86_64-rhel70-5.0.5/single/data/db"
  journal:
    #启用或禁用持久性日志以确保数据文件保持有效和可恢复。
    enabled: true
processManagement:
  #启用在后台运行mongos或mongod进程的守护进程模式。
  fork: true
net:
  #服务实例绑定的IP，默认是localhost 192.168.20.131表示远程访问
  bindIp: localhost,192.168.20.131
  #绑定的端口，默认是27017
  port: 27017
  
# 启动 MongoDB 服务
/usr/yltrcc/mongodb/mongodb-linux-x86_64-rhel70-5.0.5/bin/mongod -f /usr/yltrcc/mongodb/mongodb-linux-x86_64-rhel70-5.0.5/single/mongod.conf

# 通过进程来查看服务是否启动了
ps -ef |grep mongod

```

提示：看到 `child process started successfully, parent exiting `表示启动成功了





# 连接测试

提示：如果远程连接不上，需要配置防火墙放行，或直接关闭linux防火墙

```Bash
#查看防火墙状态 
systemctl status firewalld 
#临时关闭防火墙 
systemctl stop firewalld 
#开机禁止启动防火墙 
systemctl disable firewalld
```



**compass 连接**

地址：mongodb://192.168.20.131:27017





## 停止关闭服务

停止服务的方式有两种：快速关闭和标准关闭

### 快速关闭

快速，简单，数据可能会出错

```Bash
# 通过 ps 命令查看 进程号
ps -ef | grep mongod
# kill命令结束该进程
kill -9 54410

```

【补充】

如果一旦是因为数据损坏，则需要进行如下操作（了解）：

1）删除lock文件：`rm -f /usr/yltrcc/mongodb/mongodb-linux-x86_64-rhel70-5.0.5/single/data/db/*.lock`

2) 修复数据:`/usr/yltrcc/mongodb/mongodb-linux-x86_64-rhel70-5.0.5/bin/mongod --repair --dbpath=/usr/yltrcc/mongodb/mongodb-linux-x86_64-rhel70-5.0.5/single/data/db`

# 标准关闭

通过mongo客户端中的shutdownServer命令来关闭服务

```Bash
# 客户端登录服务，注意，这里通过localhost登录，如果需要远程登录，必须先登录认证才行。 
mongo --port 27017 
# 切换到admin库
use admin 
# 关闭服务 
db.shutdownServer()

```



# 美文佳句

同一个人，用不同的眼光去看，瞬间就不一样。很多时候我们会对身边的人吹毛求疵，可如果一味盯着别人的缺点，生活自然一地鸡毛。只有看到对方的优点，才能舒服相处。

朋友间如此，爱人间亦如此。越是优秀的人，越容易看到别人的好。