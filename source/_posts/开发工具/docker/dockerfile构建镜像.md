---
title: dockerfile
tags: docker
categories: 开发工具
date: 2023-11-11 15:19:00
---
<meta name="referrer" content="no-referrer"/>

## Dockerfile主要命令

```shell
FROM # 指定使用哪个镜像源
MAINTAINER # 指定维护者信息
RUN # 运行指定命令
ENV # ENV指令用来指定在执行docker run命令运行镜像时，自动设置的环境变量
COPY # 　这条指令用来将本地的文件或文件夹复制到镜像的指定路径下，类似单向的docker cp
EXPOSE # 这条指令用于标明这个镜像中应用将会侦听某个端口，并希望能将这个端口映射到主机的网络界面上。EXPOSE并不会让容器的端口访问到主机。要使其可访问，需要在docker run运行容器时通过-p来发布这些端口，或通过-P参数来发布EXPOSE导出的所有端口
CMD # CMD提供了容器默认的执行命令。Dockerfile只允许使用一次CMD指令，所以如果有多个CMD指令时只有最后一个指令生效(后面的指令会覆盖之前的所有指令)。
VOLUME # 该指令用来向基于镜像创建的容器添加数据卷
USER # USER指定运行容器时的用户名或UID(默认为root)。
ENTRYPOINT # 这个指定与CMD很相似，其相当于把镜像变成一个固定的命令工具，它一般是不可能通过docker run 来改变的
```

## 简单构建镜像

1. 创建一个docker文件夹 用于存放Dockerfile `mkdir docker`

2. 创建Dockerfile文件 `touch Dockerfile`

3. 编写Dockerfile文件 

   ```shell
   FROM alpine:latest # 指定使用哪个镜像源
   CMD "Dockerfile" # 输出内容
   ```

4. 构建镜像

   ```shell
   docker build -t Dockerfile_image .
   ```

- -t ：指定要创建的镜像名

- . ：Dockerfile文件所在的目录，可以指定Dockerfile的绝对路径

## 手动构建一个nginx镜像

1. 创建一个Dockerfile
   - 这里需要一个同级的index.html文件

```
FROM ubuntu
MAINTAINER youwei
RUN apt-get update
RUN apt-get install -y nginx
COPY index.html /var/www/html
ENTRYPOINT ["/usr/sbin/nginx","-g","daemon off;"]
EXPOSE 80
```

2. 运行

```shell
docker run -d -p 80:80 yw/dockerfile_image
```