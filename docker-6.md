# docker 笔记六

## docker容器的网络基础

    $ ifconfig
    如下图
    ![avatat]()

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
