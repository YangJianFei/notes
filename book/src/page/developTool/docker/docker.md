## docker 安装
> centos7 安装docker
> Docker 要求 CentOS 系统的内核版本高于 3.10 ，查看本页面的前提条件来验证你的CentOS 版本是否支持 Docker 。

```
通过 uname -r 命令查看你当前的内核版本
[admin@localhost ~]$ uname -r
3.10.0-1160.el7.x86_64
```
> yum安装

```
[admin@localhost ~]$ yum -y install docker
已加载插件：fastestmirror, langpacks
您需要 root 权限执行此命令。
```
> 没有权限，获取权限

```
[admin@localhost ~]$ su root
密码：
[root@localhost admin]# yum -y install docker
已加载插件：fastestmirror, langpacks
Loading mirror speeds from cached hostfile

```
> 安装完成，启动docker

```
[root@localhost admin]# service docker start
Redirecting to /bin/systemctl start docker.service
```
> 运行安装示例容器

```
[root@localhost admin]# docker run hello-world
Unable to find image 'hello-world:latest' locally
Trying to pull repository docker.io/library/hello-world ... 
```
> 查看docker有哪些镜像

```
[root@localhost admin]# docker images
REPOSITORY              TAG                 IMAGE ID            CREATED             SIZE
docker.io/hello-world   latest              d1165f221234        5 months ago 
```
> 查看docker容器

```
[root@localhost admin]# docker ps -a
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS                     PORTS               NAMES
6a9081577dc0        nginx               "/docker-entrypoin..."   2 minutes ago       Exited (0) 2 minutes ago                       mynginx
1d3b7ad18dfc        hello-world         "/hello"                 7 minutes ago       Exited (0) 7 minutes ago                       elated_wilson

```
> 删除容器

```
[root@localhost admin]# docker rm mynginx
mynginx

```
> 运行/停止已有容器

```
docker start container_id
docker stop container_id
```

> 进入容器

```
docker exec -it --user root 容器id /bin/bash
```

> 生成自己的镜像

https://www.jianshu.com/p/8c40e2d78e1b

> 自定义登陆授权

https://www.jianshu.com/p/063ccb8e2a75