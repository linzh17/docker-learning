# docker 笔记四

## 查看和删除镜像

1.docker 的存储地址
```
/var/lib/docker
```
2.列出镜像
```
$ docker images [optsions] [repository]
```
    -a , --all=false 查看所有镜像
    -f,--filter=[]   查看镜像时的过滤条件
    --no-trunc=false 不截断的显示
    -q,--quiet=false 只显示镜像的唯一id

3.镜像的仓库
     repository 包含一系列的关联的镜像的仓库 包含一个个独立的镜像 一个个独立的镜像由tag区分
     一个镜像的完整名字由repository名+tag名 对应唯一id
      不同于 registry （docker镜像的托管服务）

4.查看镜像
```
$ docker inspect [options] container|image[container|image] -f,--format=""
```
5.删除镜像
```
$ docker rmi [options] image [image]
    -f,-force=false Force removal of the image 强制删除镜像
    --no-prune=false Do not delete untagged parents 保留被打标签的父镜像
```
6.查找镜像

1.通过docker hub 

2.命令行
```
$ docker search [options] term
    --automated=false only show automated builds 展示自动构建的镜像
    --no-trunc=false don't truncate output 为true不以截断的形式的方式输出
    -s,--stars=0 only displays with at least x stars 指定星数
```

7.拉取镜像
```
$ docker pull [options] name [:tag]
    -a,--all-tags=false download all tagged images in the repository
```
镜像源 可以换成国内的 ，具体的参考daocloud


8.push镜像

镜像不会完全push上去，只会push修改的部分
```
docker push 镜像名
```

9.构建镜像

    保存对容器的修改，并再次使用
    自定义镜像的能力
    以软件的形式打包并分发服务及其运行环境

1.第一种方式
```
$ docker commit [options] container [repository[:tag]] 通过容器构建
    -a,--author=""
    -m,--message=""
    -p,--pause=true 构建时容器暂停

