# docker 笔记七

## docker容器的数据卷
###    data volume
    数据卷是经过特殊设计的目录，可以绕过联合文件系统（UFS）,为一个或多个容器提供访问

    数据卷设计的目的，在于数据的永久化，它完全独立与容器的生存周期，因此，docker不会在容器删除时删除其挂载的数据卷，也不会存在类似的垃圾收集机制，对容器引用的数据卷进行处理
    
### 架构
![avatar](https://github.com/linzh17/docker-learning/blob/master/%E6%95%B0%E6%8D%AE%E5%8D%B7%E6%9E%B6%E6%9E%84.png?raw=true)

#### 数据卷的生命周期和docker容器的生命周期是分离的

###    特点
    数据卷在容器启动时初始化，如果容器使用的镜像在挂载点包含了数据，这些数据会拷贝到新初始化的数据卷中
    数据卷可以在容器之间共享和重用
    可以对数据卷里的内容直接进行修改
    数据卷的变化不会影响镜像的更新
    卷会一直存在，即使挂载数据卷的容器已经被删除

###    为容器添加数据卷
    $ docker run -it -v ~/本机目录:/容器数据目录 image command
    -v 指定数据卷的目录和映射目录
    e.g
    docker run -it -v ~/datavolume:/data ubunru /bin/bash
    可以通过inspect 查看是否挂载数据卷

###    为数据卷添加访问权限
    e.g
    $ sudo docker run -it -v ~/datavolume:/data:ro ubunru /bin/bash
    在目录后添加权限设置
    ：ro 只读

###    使用dockerfile构建包含数据卷的镜像

    dockerfile指令：
    volume["/data"]
    使用volume 命令 不能指定数据卷的本机目录，由docker 自动生成
    且每次用这个dockerfile 新建的镜像，数据卷地址不同，不能共享

## 数据卷容器
    命名的容器挂载数据卷，其他容器通过挂载这个容器实现数据共享，挂载数据卷的容器，就叫做数据卷容器
### 架构
![avatar](https://github.com/linzh17/docker-learning/blob/master/%E6%95%B0%E6%8D%AE%E5%8D%B7%E5%AE%B9%E5%99%A8%E6%9E%B6%E6%9E%84.png?raw=true)

###    挂载数据卷容器的方法
    $ docker run --volumes-from [CONTAINER NAME]
    在不同容器实现数据共享
    可以避免暴露主机的文件目录
    数据卷容器被删除后，其他挂载数据卷容器的容器，数据目录依然存在
    原因：数据卷容器的作用起到一个传递者的作用，将数据卷的配置信息传递到需要挂载数据卷的容器上
    ，然后当这个数据卷还在被其他容器使用时，这个数据卷就不会被删除

### 数据卷的备份和还原
####    数据备份方法
    架构
![avatar](https://github.com/linzh17/docker-learning/blob/master/%E6%95%B0%E6%8D%AE%E5%8D%B7%E5%A4%87%E4%BB%BD.png?raw=true)       
```
    $ docker run --volumes-from [container name] -v $(pwd):/backup ubuntu tar cvf /backup/backup.tar
    -v 指定备份的路径或目录
    tar cvf /backup/backup.tar 容器的运行命令
    e.g 
    docker run --volumes-from dvt5 -v ~/backup:/backup --name dvt10 ubuntu tar cvf /backup/dvt5.tar /datavolume
```
####    数据还原方法
    $ docker run --volumes-from [container name] -v $(pwd):/backup ubuntu tar xvf /backup/backup.tar [container data volume]

