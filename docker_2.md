# docker 笔记二

## docker 基础命令

1.启动容器 单命令 run 在新容器中执行命令
```docker
$ docker run IMAGE [COMMAND][ARG...]
```

2.启动交互式容器 类似进入虚拟机

```docker
$ docker run -i -t IMAGE /bin/bash
```
    -i --interactive=true|false 默认是false 为容器打开标准输入
    -t --tty=true|false 默认是false 为容器打开伪tty 终端
3.查看容器
```docker
$ docker ps [-a][-l]
```
    -a 查看所有容器
    -l 查看最新创建的一个容器

4.查看容器参数
```docker
$ docker inspect 

```
参数为容器名字或者容器id

5.自定义容器名
```docker
$ docker run --name= 自定义名 -i-t IMAGE /bin/bash
```
6.重新启动停止的容器
```docker
$ docker start [-i] 容器名
```
7.删除容器
只能删除停止的容器
```docker
$ docker rm 容器名
```
8.删除镜像
```docker
$ docker rmi 镜像名或id