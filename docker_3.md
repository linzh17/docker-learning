# docker 笔记三

## 在容器中部署静态网站


### 设置容器的端口映射
```docker
docker run [-P][-p] ...
```
    -P --publish-all=true|false 默认为false 大写的 映射所有暴露的端口
    -p --publish=[]
    指定映射的端口
    1.containerPort
        docker run -p 80 -i -t ubuntu /bin/bash
    2.hostPort:containerPort
        docker run -p 8080:80 -i -t ubuntu /bin/bash
    3.ip::containerPort
        docker run -p 0.0.0.0:80 -i -t ubuntu /bin/bash
    4.ip:hostPort:containerPort
        docker run -p 0.0.0.0:8080:80 -i -t ubuntu /bin/bash       

### Nginx部署流程

1.创建你映射80端口的交互式容器
 ```docker
    sudo docker run -p 80 --name web -i -t ubuntu /bin/bash

```
2.安装Nginx

3.安装vim

4.创建静态页面

创建index.html文件

5.修改Nginx配置文件
寻找nginx的安装位置
```docekr
$ whereis nginx
$ cd /etc/nginx
$ ls
$ cd sites-enabled
$ vim default
```
```docker
    将root 的值 改为 刚刚index.html 的位置
```
6.运行Nginx
```docker
nginx
```
7.验证网站访问

    查看端口的映射
    有多种方式
    1. $ docker ps
    可以查看各个容器端口的映射
    2. $ docker port 容器名
    查看相应容器的端口映射


然后可以通过
```
curl http://相应端口 
```
验证
也可以通过浏览器 输入相应端口进行验证
还有第三种方式
```
docker inspect 容器名  #查看容器的实际地址
```
再用curl 或者 浏览器 验证

注意： 容器停止后，重新启动，需要重启nginx，容器的端口映射也会发生变化



   