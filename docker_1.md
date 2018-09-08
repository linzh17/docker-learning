# docker 笔记一

## docker 新一代PAAS平台


### docker组成
![avatar](https://docs.docker.com/engine/images/architecture.svg)

    docker client 
        客户端访问守护进程
    docker daemon 
        守护进程 操作容器
    docker image
        容器的基石
        层叠的只读文件系统
        联合加载 （union mount） 一次加载多个文件系统 最终显示一个文件系统 包含所有文件目录
    docker container
        通过镜像启动
        启动和执行阶段
        写时复制 在只读的image 最顶层 创建一个读写层 来源与底部只读层复制而来 底部只读层不变
    docker registry
        存放镜像
        公有 ：docker hub
        私有


### docker 核心技术

#### namespace
    命名空间 实现轻量级虚拟化

    The pid namespace: Process isolation (PID: Process ID).
    进程隔离
    The net namespace: Managing network interfaces (NET: Networking). 
    管理网络接口
    The ipc namespace: Managing access to IPC resources (IPC: InterProcess Communication).
    管理跨进程通信的访问
    The mnt namespace: Managing filesystem mount points (MNT: Mount).
    管理挂载点
    The uts namespace: Isolating kernel and version identifiers. (UTS: Unix Timesharing System).
    隔离内核版本和版本标识

    所以docker container 的能力
    文件系统隔离
    进程隔离
    网络隔离
    资源隔离和分组


#### cgroups controller group
    控制组 用来控制资源 限制资源 优先级设定  资源计量 资源控制

    先挂载子系统（资源控制器） 然后在子系统中创建一个cgroup节点 可以限制CPU时间 内存等资源的使用


#### LXC:  linux containers

    主机级虚拟化：
    类型一：直接在硬件平台上装虚拟机管理器 不需要主系统(host OS) 在虚拟机管理器上装系统
    类型二：在素机上装主系统 然后在虚拟机管理软件上装系统 比如vmware上装系统
 
    操作系统级别的虚拟化：
    lxc 共享kernel OS 级虚拟化方法 
    借助于namespace的隔离机制 （各自的用户空间 创建的驱动是独立的 例如网卡）
    大致分为三层
    底层：内核层（共享）
    中间层：隔离空间管理层
    上层：隔离环境层（用户空间层） 可以有多个 就是容器 容器的kernel与host 共享

#### AUFS
    一个能透明覆盖一或多个现有文件系统的层状文件系统
    支持将不同目录挂载到同一个虚拟文件系统下，可以把不同的目录联合在一起，组成一个单一目录
    写时复制 copy on write(类似ps的层)
