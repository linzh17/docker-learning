# docker 笔记五

## docker c/s 模式
![image](https://github.com/linzh17/docker-learning/blob/master/FireShot%20Capture%201%20-%20%E3%80%90%E5%85%AC%E5%BC%80%E8%AF%BE%E3%80%91Docker%E5%85%A5%E5%9D%91%E6%95%99%E7%A8%8B%E3%80%9033%E9%9B%86%E3%80%91_%E5%93%94%E5%93%A9%E5%93%94%E5%93%A9%20(%E3%82%9C-%E3%82%9C)%E3%81%A4%E3%83%AD_%20-%20https___www.bilibili.com_video_av17854410_.png?raw=true)


### remote api
    restful api
![avatat](https://github.com/linzh17/docker-learning/blob/master/docker_cs_remote_api.png?raw=true)

###连接方式

docker客户端与守护进程的连接

    三种socket连接的方式
    unix://var/run/dokcer.sock
    tcp://host:port
    fd://socketfd

    e.g
    #socket连接
    nc -U /var/run/docker.sock
    GET /info HTTP/1.1
