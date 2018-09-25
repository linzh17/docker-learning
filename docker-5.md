# docker 笔记五

## docker c/s 模式
![image](https://github.com/linzh17/docker-learning/blob/master/picture/FireShot%20Capture%201%20-%20%E3%80%90%E5%85%AC%E5%BC%80%E8%AF%BE%E3%80%91Docker%E5%85%A5%E5%9D%91%E6%95%99%E7%A8%8B%E3%80%9033%E9%9B%86%E3%80%91_%E5%93%94%E5%93%A9%E5%93%94%E5%93%A9%20(%E3%82%9C-%E3%82%9C)%E3%81%A4%E3%83%AD_%20-%20https___www.bilibili.com_video_av17854410_.png?raw=true)


### remote api
    restful api
![avatat](https://github.com/linzh17/docker-learning/blob/master/picture/docker_cs_remote_api.png?raw=true)

### 连接方式

docker客户端与守护进程的连接

    三种socket连接的方式
    unix://var/run/dokcer.sock
    tcp://host:port
    fd://socketfd

    e.g
    #socket连接
    nc -U /var/run/docker.sock
    GET /info HTTP/1.1  //连接失败，尚未解决

## docker 守护进程的配置和操作
1.启动配置文件

    /etc/default/docker
    文件基础内容如下
    # Docker Upstart and SysVinit configuration file

    #
    # THIS FILE DOES NOT APPLY TO SYSTEMD
    #
    #   Please see the documentation for "systemd drop-ins":
    #   https://docs.docker.com/engine/admin/systemd/
    #

    # Customize location of Docker binary (especially for development testing).
    #DOCKERD="/usr/local/bin/dockerd"
    //设置docker程序存放地址
    # Use DOCKER_OPTS to modify the daemon startup options.
    #DOCKER_OPTS="--dns 8.8.8.8 --dns 8.8.4.4" //设置启动选项
    # If you need Docker to use an HTTP proxy, it can also be specified here.
    #export http_proxy="http://127.0.0.1:3128/"
    //环境变量的值

    # This is also a handy place to tweak where Docker's temporary files go.
    #export DOCKER_TMPDIR="/mnt/bigdrive/docker-tmp"
    ~                                                   
2.相关命令

    查看守护进程
    $ ps -ef |grep docker
    $ sudo status docker //不能使用 ，可能与版本有关，原因不明
    $ sudo service docker status // 可以代替上诉语句
    //更改守护进程配置后 例如 增加docker_opts="--label name=docker_server_1"
    需要用到的命令
    $ service docker stop/start/restart
    //更改守护进程启动配置后 运行 restart 语句 没有发生更改
    //原因可能与系统版本有关，尚未解决

    解决方法
    在以systemd方式配置的系统 ，应该修改systemd配置文件
    $ cd/lib/systemd/system
    $ vim docker.service
    在 ExecStart 后追加参数 例如--lable name=dockr_server_1
    然后重启docker daemon
    $ sudo systemctl daemon-reload
    $ sudo service docker restart
    $ docker info  //就可以看见配置改变生效

    注意：如果有daemon.json文件，应该注意文件中的配置不和启动配置选项冲突，如果冲突无法正常启动docker服务


## docker 的远程访问

### 服务器端的配置
    修改docker守护进程的启动选项
    -H tcp://host:port
        unix:///path/to/socket,
        fd：//* or fd://socketfd
    docker 18 守护进程的默认配置为
    -H fd:// 
    1.非systemctl 配置的系统 修改/etc/default/docker 中opts的配置
    后增加-H tcp:0.0.0.0:2375
    2.systemctl 配置的系统  
    修改/lib/systemd/system/docker.service
    在ExecStart 后追加参数 -H tcp://0.0.0.0:2375 
    服务器设置为远程服务器时，如果设置中-H的默认设置被删除，服务器的docker客户端无法连接服务器本地服务
    需要将docker的默认配置添加回去，-H 可以配置多个
### 客户端的配置
    客户端远程访问
    修改客户端配置
    默认配置与服务器配置一样
    $ docker -H http://服务器地址：端口(2375) 
    为了便于直接操作，便于连接
    可以添加服务器地址到环境变量中
    docker客户端会自动连接远程服务器
    $ export DOCKER_HOST="tcp://服务器：端口"
    使用完远程连接，要使用本地服务
    可以
    $ export DOCKER_HOST=""



