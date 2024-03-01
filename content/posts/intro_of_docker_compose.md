---
title: "Intro_of_docker_compose"
date: 2024-02-29T14:58:31Z
draft: true
summary: "关于docker compose的一点笔记"
tags: ["Docker"]
author: "IAKSH"
---

# 0x00 | 总述
在大多数实际项目中，我们往往需要多个（许多个）容器同时运行并且相互交互。于是我们需要一种工具能够便捷的部署和管理所有容器。

`Docker Compose`是`Docker`官方的用于定义和运行多容器`Docker应用`的工具。`Docker Compose`主要通过`docker-compose.yml`进行配置，同时可以使用`Dockerfile`定义应用程序的环境。

在配置好`Dockerfile`（可选）和`docker-compose.yml`后，执行`docker-compose up`即可启动整个应用程序。

另外，本文默认你没有配置`dockerd-rootless`，如果你有，请无视`sudo`。

# 0x01 | Docker Compose安装
`Docker`和`Docker Compose`通常是分离的，你可能需要单独安装它们。
`Debian Linux`以及其他发行版的软件源应该都有`docker-compose`或者类似命名的包。如果你实在找不到，可以尝试从`github`下载：
[Release v2.21.0 · docker/compose (github.com)](https://github.com/docker/compose/releases/tag/v2.21.0)

# 0x02 | docker-compose.yml配置
参考配置如下：
```yaml
version: '3'
services:
  web:
    build: .
    ports:
   - "5000:5000"
    volumes:
   - .:/code
    - logvolume01:/var/log
    links:
   - redis
  redis:
    image: redis
volumes:
  logvolume01: {}
```
接下来以结构进行分析：

1. 版本信息：指定`Docker Compose`文件的版本。
```yaml
version: '3'
```

2. 服务定义：定义各个服务（容器）及其配置。
```yaml
services:
  webapp:
    image: nginx:latest
    ports:
      - "80:80"
  database:
    image: mysql:latest
    environment:
      - MYSQL_ROOT_PASSWORD=secret
```

3. 网络配置：定义容器之间的网络配置。
```yaml
networks:
  my_network:
```
你可以像这样把这个网络应用到容器：
```yaml
version: '3'
services:
  app1:
    image: myapp1
    networks:
      - my-network1

  app2:
    image: myapp2
    networks:
      - my-network1
      - my-network2:

networks:
  my-network1:
  my-network2:
```
你可能已经注意到了，可以给一个容器应用多个网络。
`networks`可以在不进行任何配置的情况下使用，但实际上也是可以进行一些配置的：

|配置选项|描述|
|---|---|
|`driver`|指定网络的驱动程序（如 `bridge`、`overlay` 等）|
|`driver_opts`|指定驱动程序的选项|
|`ipam`|指定自定义 IP 地址管理|
|`internal`|设置为 `true` 时，限制访问网络的容器仅限于网络内部|
|`external`|指定网络来自于外部，不由 Compose 管理|
|`attachable`|设置为 `true` 时，允许将其他容器连接到网络中的任意容器|
|`aliases`|为网络中的服务指定别名|

4. 卷配置：定义用于持续化数据的卷。
```yaml
volumes:
  my_db_data:
```
你可以像这样把这个卷挂载给容器的目录：
```yaml
version: '3'
services:
  myservice:
    image: nginx:latest
    ports:
      - 8080:80
    volumes:
      - my_db_data:/app
volumes:
  my_db_data:
```
然后，`volumes`也可以在没有任何配置的情况下使用，你也可以尝试下面的配置项：

|配置选项|描述|
|---|---|
|`driver`|指定卷的驱动程序（如 `local`、`nfs` 等）|
|`driver_opts`|指定驱动程序的选项|
|`external`|指定卷来自于外部，不由 Compose 管理|
|`name`|为卷指定一个自定义名称|
|`labels`|为卷添加自定义标签|
|`nocopy`|设置为 `true` 时，禁止在容器之间复制数据|

最后，volume的储存位置是可以更改的，可以尝试修改`/etc/docker/daemon.json`并添加如下内容：
```json'
{
  "data-root": "/your/new/volume/location"
}
```
需要注意的是，在这之后你可能需要手动迁移数据。

# 0x03 | 自动构建和依赖
```yaml
version: '3'
services:
  db:
    image: mysql
    # 其他配置项...

  app:
    build: ./path/to/dockerfile
    # 其他配置项...
    depends_on:
      - db
```
默认情况下，`Docker Compose`会并行地启动每个服务。对于某些强依赖启动顺序的情景，你可以通过为服务设置`depens_on`来指定启动顺序。

# 0x04 | 启动和管理
你可以通过`sudo docker-compose up`命令启动你的所有容器。该命令也可以添加其他参数，其中一些如下：

|参数|描述|
|---|---|
|`-f <文件路径>` 或 `--file <文件路径>`|指定要使用的 Compose 文件路径（默认为 `docker-compose.yml`）。|
|`-p <项目名称>` 或 `--project-name <项目名称>`|指定 Compose 项目的名称。默认情况下，将使用所在目录的名称作为项目名称。|
|`-v` 或 `--verbose`|显示详细的命令输出信息。|
|`--log-level <级别>`|设置日志级别，可以是 `debug`、`info`、`warning`、`error` 或 `critical`。|
|`-h` 或 `--help`|显示帮助信息，列出所有可用的命令和选项。|
|`config`|验证和显示 Compose 文件的配置。|
|`up`|创建并启动容器。|
|`down`|停止并移除容器，还原其状态。|
|`start`|启动已创建的容器。|
|`stop`|停止正在运行的容器。|
|`restart`|重启正在运行的容器。|
|`pause`|暂停容器的执行。|
|`unpause`|取消暂停容器的执行。|
|`build`|构建或重新构建服务的镜像。|
|`ps`|显示当前项目的容器状态。|


---
参考：[Docker Compose | 菜鸟教程 (runoob.com)](https://www.runoob.com/docker/docker-compose.html)