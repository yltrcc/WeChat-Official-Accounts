# 概述

如果在启动Docker容器的过程中没有单独配置localtime，很可能造成Docker容器时间与主机时间不一致的情况，比如UTC和CST相差8小时，换句话来说就是容器时间与北京时间相差8个小时

可以通过 `date` 命令分别查看容器和宿主机系统时间

# 解决方法

## 1. docker run 添加参数

```Bash
-v /etc/localtime:/etc/localtime

# 实例
docker run -p 3306:3306 --name mysql -v /etc/localtime:/etc/localtime

```

## 2. DockerFile

```Bash
# 方法1
# 添加时区环境变量，亚洲，上海
ENV TimeZone=Asia/Shanghai
# 使用软连接，并且将时区配置覆盖/etc/timezone
RUN ln -snf /usr/share/zoneinfo/$TimeZone /etc/localtime && echo $TimeZone > /etc/timezone

# 方法2
# CentOS
RUN echo "Asia/shanghai" > /etc/timezone
# Ubuntu
RUN cp /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
```

## 3. docker-compose

```Bash
#第一种方式(推荐)：
environment:
  TZ: Asia/Shanghai
  
#第二种方式：
environment:
  SET_CONTAINER_TIMEZONE=true
  CONTAINER_TIMEZONE=Asia/Shanghai

#第三种方式：
volumes:
  - /etc/timezone:/etc/timezone
  - /etc/localtime:/etc/localtime
```

4. 宿主机直接执行命令给某个容器同步时间

```Bash
# 方法1：直接在宿主机操作
docker cp /etc/localtime 【容器ID或者NAME】:/etc/localtime
docker cp -L /usr/share/zoneinfo/Asia/Shanghai 【容器ID或者NAME】:/etc/localtime

# 方法2：登录容器同步时区timezone
ln -sf /usr/share/zoneinfo/Asia/Singapore /etc/localtime
```

注：**这种方式在容器中运行的程序的时间不一定能更新过来，比如在容器运行的mysql服务，在更新时间后，通过sql查看mysql的时间**

```Bash
select now() from dual;
```

可以看到，时间并没有更改过来  ，这时候必须要重启mysql服务或者重启docker容器，mysql才能读取到更改过后的时间

