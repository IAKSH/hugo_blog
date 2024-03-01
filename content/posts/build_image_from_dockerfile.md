---
title: "Build_image_from_dockerfile"
date: 2024-02-29T14:57:50Z
draft: true
summary: "从Dockerfile创建镜像"
tags: ["Docker"]
author: "IAKSH"
---

# 0x00 | 总述
创建/自定义一个`Docker镜像`有两种方式，一种是很简单的手动修改`Docker容器`内容，然后`docker commit`。另一种是通过编写`Dockerfile`，再使用命令`sudo docker build -t name:version /path/to/folder/of/dockefile`生成。

> 关于`docker commit`：一个实例是`docker commit -m="has update" -a="runoob" e218edb10161 runoob/ubuntu:v2`，其中，`-m`是描述信息，`-a`是镜像作者

# 0x01 | Dockerfile基本结构

>Dockerfile 是一个用来构建镜像的文本文件，文本内容包含了一条条构建镜像所需的指令和说明。

下面是一个完整的`dockerfile`示例：
```dockerfile
# 使用 Debian 作为基础镜像
FROM debian
# 设置工作目录
WORKDIR /app
# 复制应用程序文件到工作目录
COPY . /app
# 安装应用程序所需的依赖
RUN apt-get update && \
    apt-get install -y <your-dependencies>
# 暴露容器的端口
EXPOSE 8080
# 定义容器启动时运行的命令
CMD [ "python", "app.py" ]
```
可以看出实际上就是指令的堆砌。
# 0x02 | Dockerfile指令

|Dockerfile 指令|说明|
|---|---|
|FROM|指定基础镜像，用于后续的指令构建。|
|MAINTAINER|指定Dockerfile的作者/维护者。（已弃用，推荐使用LABEL指令）|
|LABEL|添加镜像的元数据，使用键值对的形式。|
|RUN|在构建过程中在镜像中执行命令。|
|CMD|指定容器创建时的默认命令。（可以被覆盖）|
|ENTRYPOINT|设置容器创建时的主要命令。（不可被覆盖）|
|EXPOSE|声明容器运行时监听的特定网络端口。|
|ENV|在容器内部设置环境变量。|
|ADD|将文件、目录或远程URL复制到镜像中。|
|COPY|将文件或目录复制到镜像中。|
|VOLUME|为容器创建挂载点或声明卷。|
|WORKDIR|设置后续指令的工作目录。|
|USER|指定后续指令的用户上下文。|
|ARG|定义在构建过程中传递给构建器的变量，可使用 "docker build" 命令设置。|
|ONBUILD|当该镜像被用作另一个构建过程的基础时，添加触发器。|
|STOPSIGNAL|设置发送给容器以退出的系统调用信号。|
|HEALTHCHECK|定义周期性检查容器健康状态的命令。|
|SHELL|覆盖Docker中默认的shell，用于RUN、CMD和ENTRYPOINT指令。|

# 0x03 | scratch
从上文可以看出，要构建一个`Docker`镜像通常就得通过编写`dockerfile`再执行`docker build`。但是`dockerfile`只能从已有的镜像上构建。

鸡生蛋，蛋生鸡... 鸡从哪儿来？

>第一个 Docker 镜像来自于 Docker 官方提供的基础镜像，称为 "scratch"。在 Dockerfile 中使用 "scratch" 作为基础镜像，意味着你将从一个空白的镜像开始构建，然后逐步添加你所需的文件和配置。

---
参考：[Docker Dockerfile | 菜鸟教程 (runoob.com)](https://www.runoob.com/docker/docker-dockerfile.html)