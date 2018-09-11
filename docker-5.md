# docker 笔记五

## docker c/s 模式
![image](FireShot Capture 1 - 【公开课】Docker入坑教程【33集】_哔哩哔哩 (゜-゜)つロ_ - https___www.bilibili.com_video_av17854410_.png)


### remote api
    restful api
![avatat][dokcer_learning/docker_cs_remote_api.png]

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