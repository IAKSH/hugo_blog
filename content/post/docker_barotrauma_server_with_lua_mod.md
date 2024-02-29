---
title: "Docker_barotrauma_server_with_lua_mod"
date: 2024-02-29T14:57:11Z
draft: true
---

基于steamcmd docker和sakura frp docker
古典运维风格
<!--more-->
需要从dockerfile构建一个基于steamcmd的服务器镜像，这个过程手动打指令类似于这样
sudo docker run --rm -it cm2network/steamcmd /home/steam/steamcmd/steamcmd.
sh "+login anonymous +app_update 1026340 validate +quit"
然后，~~这个新容器每次启动后需要先从steamcmd检查更新~~，然后启动barotrauma服务器。

还是不每次更新了，要更新就重新构建容器

服务器的端口是27015（默认，下同）
给steam的队列端口是27016

# docker-compose
```yaml
version: '3'
services:
  barotrauma-server:
    build: .
    networks:
      - network0
    ports:
      - "27015:27015/udp"
    volumes:
      - ./barotrauma:/home/steam/steamcmd/barotrauma

networks:
  network0:
volumes:
  server_store:
```

# dockerfile
```dockerfile
FROM cm2network/steamcmd
WORKDIR /home/steam/steamcmd/
ENV http_proxy=http://192.168.250.193:10809
ENV https_proxy=$http_proxy
ENV socks5_proxy="socks5://192.168.250.193:10809"
RUN /home/steam/steamcmd/steamcmd.sh +force_install_dir ./barotrauma +login anonymous +app_update 1026340 validate +quit
CMD ["/home/steam/steamcmd/barotrauma/DedicatedServer"]
```

# 目前问题
无法从原frpc镜像自动配置穿透，且docker网络似乎对穿透无效，目前是server容器穿透端口，非容器frpc做内网穿透

-f v99cuasaymhkwckpheqzx3l3ofrrvd39:11087705

似乎解决了，可以通过给sakura frp添加上述参数来阻止进入TUI，直接连接隧道。
文档见：[frpc 基本使用指南 | SakuraFrp 帮助文档 (natfrp.com)](https://doc.natfrp.com/frpc/usage.html#running-frpc)

知道为什么docker网络似乎对穿透无效了，因为docker-compose里设置的networks确实能让容器互通，但是问题是需要直接访问docker-compose service名才能互通。frpc显然是无法做到的。

直接用host网络应该能解决，但是好像很不安全？

等等，似乎只需要在frpc控制面板改本地IP就好了，改成service名。

成功了

# 存档自动备份
感觉是好像直接再弄个dockerfile来起个容器，里面塞个自动备份程序，然后再把barotrauma的文件路径穿进去就好了。
应该能够让同一路径被多个容器穿透的吧？

暂时搁置

# 一键Lua
直接修改barotrauma docker的dockerfile，在从steamcmd下载好服务器后，直接从github下载luaCs然后解压覆盖就是了

成功

# 一键导入mod
barotrauma/LocalMods
由于一些未知原因，mod应当全部放在LocalMods，而不是Dae...的那个workshop mods文件夹
config_player实际上只需要从本地覆盖，然后简单改个路径就好了，似乎不用自动化完成。

# 一键导入配置文件
主要就是barotrauma/下的俩.xml和barotrauma/Data里的几个.xml
下载好服务器文件后直接让dockerfile把预先修改好的复制进去就好了

好了，但是config_player.xml里的mod不知道为什么全部挂不上了

似乎是因为之前mods文件是从docker volume里复制出来的，只有root有读写权。现在又重新传了一遍，应该能行了。

确实，成功了

# 一键导入存档
```dockerfile
ARG SAVE_PATH="/home/steam/steamcmd/barotrauma/Daedalic Entertainment GmbH/Barotrauma/Multiplayer"

ARG TEMP_SERVER_PATH="/home/steam/steamcmd/barotrauma/Daedalic Entertainment GmbH/Barotrauma/temp_server"

ARG WORKSHOP_MOD_PATH="/home/steam/steamcmd/barotrauma/Daedalic Entertainment GmbH/Barotrauma/WorkshopMods/Installed"
```

```dockerfile
COPY ./saves/ ${SAVE_PATH}

COPY ./temp_server/ ${TEMP_SERVER_PATH}

COPY ./empty/ ${WORKSHOP_MOD_PATH}
```

真难伺候。
而且一直报错
```
Error decompressing Daedalic Entertainment GmbH/Barotrauma/Multiplayer/HaoQiHao.save {Access to the path '/home/steam/steamcmd/barotrauma/Daedalic Entertainment GmbH/Barotrauma/temp_server/gamesession.xml' is denied.}
```
不知道为什么，一直没有这个文件的读写权限。

但是直接复制进volume还是可以的，所以暂时不修了（
# Web控制面板（咕