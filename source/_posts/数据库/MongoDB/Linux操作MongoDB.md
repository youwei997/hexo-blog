---
title: Linux操作MongoDB
tags: MongoDB
categories: 数据库
date: 2023-11-11 16:33:33
---
<meta name="referrer" content="no-referrer"/>

1.查看mongodb进程：

```
ps aux |grep mongodb
```

2.进入mongodb启动目录：

```
cd /usr/local/mongodb/mongodb-5.0.5/bin
```

3.杀死mongodb的进程:

```
kill -2 11884
```

4.重启mongodb:

```
./mongod --config mongodb.conf
```

![img](https://img-blog.csdnimg.cn/20190626161001276.png)

关闭数据库

```
 mongod --journal --shutdown -f ./mongodb.conf
```

开启

```
mongod --journal -f ./mongodb.conf
```

