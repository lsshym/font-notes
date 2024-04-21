如果输出mysql命令遇到

```
Can't connect to local MySQL server through socket '/var/lib/mysql/mysql.sock' (2)
```

那这可能是没启动服务

```
service mysqld restart
// 就能解决
```

![image-20200208211849181](C:\Users\Mr.Giraffe\AppData\Roaming\Typora\typora-user-images\image-20200208211849181.png)



```
create schema 'react'报错
不用加引号，爷真是惊了
```

https://blog.csdn.net/Ginny_2017/article/details/90485540



这个错误因为没启动数据库

![image-20200210132929902](C:\Users\Mr.Giraffe\AppData\Roaming\Typora\typora-user-images\image-20200210132929902.png)