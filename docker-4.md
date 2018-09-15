# docker 笔记四

## 查看和删除镜像

1.docker 的存储地址
```
/var/lib/docker
```
2.列出镜像
```
$ docker images [optsions] [repository]
```
    -a , --all=false 查看所有镜像
    -f,--filter=[]   查看镜像时的过滤条件
    --no-trunc=false 不截断的显示
    -q,--quiet=false 只显示镜像的唯一id

3.镜像的仓库
     repository 包含一系列的关联的镜像的仓库 包含一个个独立的镜像 一个个独立的镜像由tag区分
     一个镜像的完整名字由repository名+tag名 对应唯一id
      不同于 registry （docker镜像的托管服务）

4.查看镜像
```
$ docker inspect [options] container|image[container|image] -f,--format=""
```
5.删除镜像
```
$ docker rmi [options] image [image]
    -f,-force=false Force removal of the image 强制删除镜像
    --no-prune=false Do not delete untagged parents 保留被打标签的父镜像
```
6.查找镜像

1.通过docker hub 

2.命令行
```
$ docker search [options] term
    --automated=false only show automated builds 展示自动构建的镜像
    --no-trunc=false don't truncate output 为true不以截断的形式的方式输出
    -s,--stars=0 only displays with at least x stars 指定星数
```

7.拉取镜像
```
$ docker pull [options] name [:tag]
    -a,--all-tags=false download all tagged images in the repository
```
镜像源 可以换成国内的 ，具体的参考daocloud


8.push镜像

镜像不会完全push上去，只会push修改的部分
```
docker push 镜像名
```

9.构建镜像

    保存对容器的修改，并再次使用
    自定义镜像的能力
    以软件的形式打包并分发服务及其运行环境

1.第一种方式
```
$ docker commit [options] container [repository[:tag]] 通过容器构建
    -a,--author=""
    -m,--message=""
    -p,--pause=true 构建时容器暂停

```
2. 使用dockerfile 构建镜像
   ```
   1. 创建Dockerfile
   2.$ docker build
   e.g
   #first dockerfile
   From ubuntu:14.04  #镜像的基础
   MAINTAINER linzh17 "15018279123@163.com" #维护者
   RUN apt-get update                       #镜像中执行的命令
   RUN apt-get install -y nginx
   EXPOSE 80                                #暴露的端口

   $docker build [options] PATH|URL |-
   --force-rm=false
   --no-cache=fasle
   --pull=false
   -q,--quiet=false
   -rm=true
   -t,-tag="" #指定构建的镜像的名字

### dockerfile指令
   指令格式 

    #注释
    大写的指令名 指令参数

    e.g.
     #first dockerfile
    From ubuntu:14.04  #镜像的基础
    MAINTAINER linzh17 "15018279123@163.com" #维护者
    RUN apt-get update                       #镜像中执行的命令
    RUN apt-get install -y nginx
    EXPOSE 80                                #暴露的端口

#### FROM
    FROM<image>
    FROM<image>:<tag> #必须为第一条未注释的指令

#### MAINTAINER
    MAINTAINER<name>
    指定镜像的作者信息，包含镜像的所有者和联系信息

##### RUN
    RUN<command>(shell模式)
    e.g
    /bin/sh -c command
    RUN echo hello

    RUN["executable","param1","param2"] (exec模式)
    e.g
    RUN["/bin/bash","-c","echo hello"]

##### EXPOSE
    EXPOSE<port>[<port>..]
    指定运行该镜像的容器使用的端口 可以指定一个或多个端口
    注意：运行该容器时，run命令中还是得指定映射的端口

#### CMD
    CMD 命令是在容器运行时运行的，会被docker run中指定的命令所覆盖，
    CMD 指定的是容器运行时的默认指令，而RUN命令是在构建镜像时运行的命令

    CMD命令的几种模式
    CMD["executable","param1","param2"](exec 模式)
    CMD command param1 param2(shell 模式)
    CMD["param1","param2"](作为ENTRYPOINT指令的默认参数)

#### ENTYPOINT
    与CMD命令类似，不会被docker run 命令中指定的命令所覆盖
    ENTYPOINT["executable","param1","param2"](exec 模式)
    ENTYPOINT command param1 param2(shell 模式)

    如果要覆盖
    docker run --entrypoint

####  ADD & COPY

    这两个指令将文件或目录复制到docker构建的镜像中
    来源地址可以使本地或者远程的URL,目标路径要指定镜像中的绝对路径
    1.ADD
    ADD<src>..<dest>
    ADD["<src>".."<dest>"](适用于文件路劲中有空格)

    2.COPY
    COPY<src>..<dest>
    COPY["<src>".."<dest>"](适用于文件路劲中有空格)

    ADD包含类似tar的解压功能
    单纯复制文件，Docker推荐使用COPY

#### VOLUME
    VOLUME["/data"] 
    向基于镜像构建的容器提供卷 一个卷可以存一个或多个容器的特定目录
    可以绕过联合文件系统，可以实现数据共享，数据持久性等功能

#### WORKDIR 
    WORKDIR /path/to/workdir //使用绝对路径 使用相对路径，路径会一直传递下去
    指定镜像在创建一个新容器时，在容器内部中指定工作目录，CMD和ENTRYPOINT 指定的指令都会在这个工作目录中执行


#### ENV
    ENV<key><value>
    ENV<key>=<value>...
    用于设置环境变量

#### USER daemon
    指定镜像被什么用户运行,默认使用root用户
    e.g
    USER nginx    
#### ONBUILD
    为镜像添加触发器
    当这个镜像被其他镜像作为基础镜像构建时 触发器会被执行，当子镜像构建时 会在构建过程中插入触发器指令

    ONBUILD[INSTRUCTION]

### dockerfile的构建过程

    从基础镜像运行一个容器
    执行一条容器，对容器做出修改
    执行类似docker commit的操作，提交一个新的镜像层
    再基于刚提交的镜像运行一个新的容器
    执行dockerfile中的下一条指令，直至所有指令执行完毕


#### 使用中间层镜像
    docker build 命令会删除中间镜像的容器，不会删除中间容器
    可以在中间层容器进行调试，查找错误

#### 构建缓存
    每一步构建过程都是记录在镜像中，这样原来的镜像类似于缓存
    再一次构建时，将会很快
    
    不使用缓存时
    1.
    $ docker build --no-cache

    2.在dockerfile中
    设置EVN 参数  设置缓存刷新时间，后续的命令在构建时就会不使用缓存

#### 查看镜像的构建过程
    $ docker history image    