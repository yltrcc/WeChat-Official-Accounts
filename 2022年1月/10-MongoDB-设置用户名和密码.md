# 给每个数据库设置单独的管理员

我们除了可以设置数据库的超级管理员以外，还可以给每个数据库设置单独的管理员。其只有操作单独数据的一定权限。

```Bash
db.createUser({
  user: 'bobo',  // 用户名
  pwd: '123456',  // 密码
  roles:[{
    role: 'readWrite',  // 角色
    db: 'articledb'  // 数据库名
  }]
})
```





# 参考资料

- [https://www.jianshu.com/p/237a0c5ad9fa](https://www.jianshu.com/p/237a0c5ad9fa)
- [https://docs.mongodb.com/manual/reference/built-in-roles/](https://docs.mongodb.com/manual/reference/built-in-roles/)

