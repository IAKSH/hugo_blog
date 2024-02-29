---
title: "Something_about_docker"
date: 2024-02-29T14:59:15Z
draft: true
summary: "docker随记"
---

1. 
```
sudo docker -i -t ...
```
`-i`是允许与容器的`stdin`进行交互。`-t`是在新容器内指定一个伪终端或终端。

2. 
```
docker logs 2b1b7a428627
```
可以直接查看容器内的`stdout`，包括已经退出的实例。

3. 
```
sudo docker stats
```
可以以类似`top`的工具监视各实例的状态。还可以加`-a`之类的其他参数，具体见`-h`。

4. 
对于带有`-d`的后台运行的实例，可以使用一下两种方法将其转入前台：
```
sudo docker attach 2b1b7a428627
sudo docker exec -it 2b1b7a428627 /bin/bash
```
区别是`exec`是在容器中启动新的程序，使用`-it`将其转入前台。这样当退出时，容器不会被停止。

5. 
你可以这样导出一个容器到本地：
```
sudo docker export 1e560fca3906 > ubuntu.tar
```
然后这样导入它：
```
cat docker/ubuntu.tar | docker import - test/ubuntu:v1
# 也可以通过URL导入，如下
sudo docker import http://example.com/exampleimage.tgz example/imagerepo
```

6. 
`Docker`的`-P`参数是将容器使用的端口随机映射到本地的可用端口。而`-p xxx:xx`是将本地端口`xxx`绑定到容器的`xx`端口。

7. 
可以使用`sudo docker port bf08b7f2cd89`来查看一个实例的端口转发，当然也可以写实例的名字。

8. 
可以使用`sudo docker top bf08b7f2cd89`来查看实例中的进程。

9. 
如果要绑定`UDP`协议的端口，需要在`-p`后的容器端口后面加上`/udp`。

10. 
多个`Docker容器实例`除了可以通过端口相互连接，还能通过`docker network`连接。类似于创建了一个虚拟网络。
```
# 查看所有docker network
sudo docker network ls
# 创建一个 bridge 类型的 docker network，-d仅用于指定网络类型
docker network create -d bridge test-net

# 创建容器实例并将其连接到刚刚创建的网络
docker run -itd --name test1 --network test-net ubuntu /bin/bash
docker run -itd --name test2 --network test-net ubuntu /bin/bash
# 你现在可以直接在test1中ping test2，docker会自动配置好相关映射。
```

11. 
宿主机的`/etc/docker/daemon.json`中可以一次性为所有`docker容器实例`配置`DNS`:
```
{
  "dns" : [
    "114.114.114.114",
    "8.8.8.8"
  ]
}
```
可能需要重启容器实例。


12.
可以手动为单个容器实例指定`DNS`：
```
docker run -it --rm -h host_ubuntu  --dns=114.114.114.114 --dns-search=test.com ubuntu
```
>**-h HOSTNAME 或者 --hostname=HOSTNAME**： 设定容器的主机名，它会被写到容器内的 /etc/hostname 和 /etc/hosts。
**--dns=IP_ADDRESS**： 添加 DNS 服务器到容器的 /etc/resolv.conf 中，让容器用这个服务器来解析所有不在 /etc/hosts 中的主机名。
**--dns-search=DOMAIN**： 设定容器的搜索域，当设定搜索域为 .example.com 时，在搜索一个名为 host 的主机时，DNS 不仅搜索 host，还会搜索 host.example.com。

