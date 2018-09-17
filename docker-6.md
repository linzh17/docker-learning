# docker 笔记六

## docker容器的网络基础

    $ ifconfig
    如下图
![avatar](https://github.com/linzh17/docker-learning/blob/master/2018-09-15%2014-55-44%20%E7%9A%84%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE.png?raw=true)

### docker0 
    linux 虚拟网桥
    特点：
    可以设置IP地址
    相当于拥有一个隐藏的虚拟网卡
    docker0的地址划分：
    IP:172.17.42.1 子网掩码：255.255.0.0
    MAC:02:42:ac:11::00:00 到02:42：ac:11:ff:ff
    总共提供了65534个地址

    安装网桥管理工具
    $ sudo apt-get install bridge-utils
    查看网桥
    $ sudo brctl show
    运行容器时，docker 会自动生成一个网桥
    原理下图
![avatar](https://github.com/linzh17/docker-learning/blob/master/%E8%99%9A%E6%8B%9F%E7%BD%91%E6%A1%A5.png?raw=true)


### 自定义docker0
    $ sudo ifconfig docker0 ip-address netmask ...

### 自定义虚拟网桥
    e.g
    1. 添加虚拟网桥
    $ sudo bctrl addbr br0
    $ sudo ifconfig br0 192.168.100.1 netmask 255.255.255.0
    2. 更改docker 守护进程的启动配置：
    /lib/systemd/system/docker.service Execstart 后添加参数
    -b=br0

## 容器的互联

    1.环境准备
    Dockerfile:
    FROM ubuntu
    RUN apt-get update
    RUN apt-get install -y iputils-ping
    RUN apt-get update
    RUN apt-get install -y nginx
    Run apt-get install -y curl
    EXPOSE 80
    CMD /bin/bash

    2.默认方式
    在同一素主机的容器是可以互相连接的
    --icc=true
    注意：容器停止后，再次启动，分配的IP地址可能会发生变化
    所以另外的容器，一些依靠与此容器IP地址的服务可能会失效
    解决方式

    --link 
    $docker run --link=[container_name]:[alias] [image] [command] //alias 别名
    e.g
    $ docker run -it --name cct3 --link=cct1:webtest linzh17/cct
    $ ping webtest
    查看环境变量
    $ env 
    或者
    $ cat /etc/hosts

###  拒绝所有的容器的互相连接

    修改启动选项配置参数 
    $ sudo vim /lib/systemd/system/docker.service
    在Execstart 参数后添加参数 --icc=false
    $ systemctl daemon-reload
    $ sudo service docker restart
    再次连接容器 会发现该容器无法连接到其他容器上

#### 允许特定容器的连接

    修改启动选项
    --icc=false
    --iptables=true
    允许docker容器将配置添加到Linux的iptables的配置中
    --link 


## 容器与外部网络的连接

### ip_forward
    该参数决定系统是否转发流量
    --ip-forward=true（默认为true）docker 会在守护进程启动时 将系统的ip_forward 设置为一
    系统自带的工具查看是否开启系统转发
    $ sysctl net.ipv4.conf.all.forwarding


### iptables
    iptables是与linux内核集成的包过滤防火墙系统
#### table
    nat
    mangle
    raw
    filter
#### chain
    filter表中包含的链
    INPUT
    FORWARD
    OUTPUT
#### rule 
    ACCEPT、REJECT、DROP

### 允许或阻止特定IP访问特定容器
    
    阻止
    sudo iptables -I DOCKER -s 远程客户端ip地址 -d 特定容器的ip地址 -p TCP --dport 80 -j DROP
