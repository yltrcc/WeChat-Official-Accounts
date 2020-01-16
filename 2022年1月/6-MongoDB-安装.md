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

暂无