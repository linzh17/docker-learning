# docker笔记八

## 容器的跨主机连接

### 使用网桥实现跨主机容器连接
#### 原理
![avatar](https://github.com/linzh17/docker-learning/blob/master/%E7%BD%91%E6%A1%A5%E8%BF%9E%E6%8E%A5%E5%8E%9F%E7%90%86.png?raw=true)

#### 网络设置
    修改/etc/network/interfaces 文件
    e.g
    auto br0              //网桥名字
    iface br0 inet static   //IP分配的方式
    address 10.211.55.3     
    netmask 255.255.255.0
    gateway 10.211.55.1   //默认网关
    bridge_ports eth0      //将本地网卡连接到网桥上

#### docker设置
    修改 /lib/systemd/system/docker.service文件
    在Execstart 后添加参数
    -b 指定使用的自定义网桥
    -b = br0
    --fixed-cidr 限制ip地址分配范围 防止两台docker主机分配的ip 地址发生冲突
    e.g
    Host1:10.211.55.64/26
        10.211.55.65~10.211.55.126
    Host2:192.168.59.128/26
        10.211.55.129~10.211.55.190

注意：修改生效后，系统中docker0网桥会消失，物理网卡连接到网桥后，也会消失不在ifconfig
此时容器的可以联通外网，也可以联通另外的主机

#### 缺点
    与主机在同一网段，需要小心划分IP地址
    需要有网络控制权，在生产环境中不易实现
    不容易管理
    兼容性不佳

### 使用open vSwitch 实现跨主机容器连接

#### 原理
!avatar[https://github.com/linzh17/docker-learning/blob/master/ovs.png?raw=true]
!avatar[https://github.com/linzh17/docker-learning/blob/master/openswitch.png?raw=true]
!avatar[https://github.com/linzh17/docker-learning/blob/master/gre.png?raw=true]

#### 操作
    建立ovs网桥
    添加gre连接
    配置docker容器虚拟网桥
    为虚拟网桥添加ovs接口
    添加不同docker容器网段路由
    e.g
    sudo ovs-vsctl show 
    查看ovs 状态

    sudo ovs-vsctl add-br obr0
    建立ovs 网桥

    sudo ovs-vsctl add-port obr0 gre0
    sudo ovs-vsctl set interface gre0 type=gre options:remote_ip=192.168.59.104
    添加gre连接

    sudo brctl addbr br0 
    sudo ifconfig br0 192.168.1.1 netmask 255.255.255.0
    为容器创建虚拟网桥
    
    sudoo brctl addif br0 obr0
    为虚拟网桥添加ovs接口

    修改docker 启动配置的网桥选项
    此时容器可以ping通192.168.59.104地址的主机
    但是无法ping通该主机上的容器

    需要在这个本地机器添加路由表另一台主机的容器的网段路由

    route 查看路由表
    sudo ip route add 容器网段路由 via 另一台主机的网段 dev 选择连接的网卡
    e.g
    sudo ip route add 192.168.2.0/24 via 192.168.59.104 dev eth0
    

### 使用weave实现跨主机容器连接

#### 简介
    建立一个虚拟的网络，实现连接
![avatar](https://github.com/linzh17/docker-learning/blob/master/weave.png?raw=true)    
    

#### 下载与安装
    curl -L git.io/weave -o /usr/bin/weave
    sudo chmod a+x /usr/bin/weave

#### 运行weave
    weave launch
    会在docker中运行一个weaved的容器

    e.g
    在另一个主机上运行
    weave launch 本地主机的ip地址 确保跨主机连接成功
    使用weave创建容器，由weave维护
    weave run 分配个容器的IP地址 -it ubuntu /bin/bash
    e.g
    weave run 192.168.1.2/24 -it ubuntu /bin/bash
    会在容器中生成一个ethwe网卡，地址为分配的容器地址

    回到本地主机
    weave run 192.168.1.10/24 -it --name wc1 ubuntu /bn/bash
    docker attach wc1
    ping 192.168.1.2
    发现本地主机已经可以联通另一台主机

