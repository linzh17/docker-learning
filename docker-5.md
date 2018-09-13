# docker 笔记五

## docker c/s 模式
![image](https://github.com/linzh17/docker-learning/blob/master/FireShot%20Capture%201%20-%20%E3%80%90%E5%85%AC%E5%BC%80%E8%AF%BE%E3%80%91Docker%E5%85%A5%E5%9D%91%E6%95%99%E7%A8%8B%E3%80%9033%E9%9B%86%E3%80%91_%E5%93%94%E5%93%A9%E5%93%94%E5%93%A9%20(%E3%82%9C-%E3%82%9C)%E3%81%A4%E3%83%AD_%20-%20https___www.bilibili.com_video_av17854410_.png?raw=true)


### remote api
    restful api
![avatat](https://github.com/linzh17/docker-learning/blob/master/docker_cs_remote_api.png?raw=true)

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
    //更改守护进程配置后
    需要用到的命令
    $ service docker stop/start/restart
    //更改守护进程启动配置后 运行 restart 语句 没有发生更改
    //原因可能与系统版本有关，尚未解决

