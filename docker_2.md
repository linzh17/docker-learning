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
```

## 守护式容器
可以长期运行 不需要交互式会话

1.以守护形式运行容器

```docker
$ docker run -i -t IMAGE /bin/bash
```
ctrl p 和 Ctrl q 用来退出bash 容器在后台运行

2.访问运行中的容器
```docker
$ docker attach 容器名
```
3.启动守护式容器
```docker
$ docker run -d IMAGES [command][ARG..]
```
-d 后台运行 运行结束 容器自动停止

4.查看后台运行的容器的日志
```docker
$ docker logs [-f][-t][--tail] 容器名
```
    -f --follows = true |false 默认为false 为true 一直跟踪
    -t --timestamps = true |false 默认为false true  添加时间戳
    --tail 默认为all

5.查看运行中容器内的进程
```docker
$ docker top 容器名
```    
6.在运行中的容器内启动新进程
```docekr
$ docker exec [-d][-i][-t] 容器名 [command][ARG..]
```
7.停止守护式容器
```docker
$ docker stop 容器名 等待容器停止 返回信息
$ docker kill 容器名 直接结束容器所有操作
```
