---
title: "Vscode Remote SSH Pubkey"
date: 2024-02-29T14:53:58Z
# weight: 1
# aliases: ["/first"]
tags: ["SSH","VSCode"]
author: "IAKSH"
# author: ["Me", "You"] # multiple authors
showToc: false
TocOpen: false
draft: false
hidemeta: false
comments: false
description: "SSH 密钥登录"
disableHLJS: true # to disable highlightjs
disableShare: false
disableHLJS: false
hideSummary: false
searchHidden: true
ShowReadingTime: true
ShowBreadCrumbs: true
ShowPostNavLinks: true
ShowWordCount: true
ShowRssButtonInSectionTermList: true
UseHugoToc: true
editPost:
    URL: "https://github.com/IAKSH/iaksh.github.io/content"
    Text: "Suggest Changes" # edit text
    appendFilePath: true # to append file path to Edit link
---

注：`~/.ssh/authorized_keys` 是一个文件，不是目录！
<!--more-->
# 0x00 | 概述
默认情况下，每次使用`vscode`的`remote-ssh`插件登录到远程服务器都需要重新输入密钥（甚至是切换路径时）。显然这太麻烦了。

不同于传统密码，`SSH`使用基于`RSA`算法的密钥对作为凭证来实现自动登录。和传统密码相比该方案显著提高了安全性。（`SSH`的安全真的很重要！）

`RSA`密钥对分为`公钥`，`私钥`。本地机保留自己的的私钥，然后将自己的`公钥`复制给远程机即可。

# 0x01 | 生成密钥对
如果你没有密钥对，可以使用`ssh-keygen`指令生成。这将会生成`id_rsa.pub`（公钥）和`id_rsa`（私钥）。

接下来需要将本地机的公钥文件复制到远程机（以Linux为例）的`~/.ssh/authorized_keys`（这是一个文件）。如果远程机没有`.ssh`目录，你需要手动将其创建。需要注意的是`.ssh`和`.ssh/authorized_keys`的权限。
```shell
chmod 600 ~/.ssh/authorized_keys
chmod 700 ~/.ssh
```

如果你希望允许多个机器通过公钥登录，只需要将他们的`RSA公钥`逐行添加到`~/.ssh/authorized_keys`中。

# 0x02 | 复制公钥
`OpenSSH`提供了`ssh-copy-id`工具以将本机的公钥复制到远程机器的`authorized_keys`中，同时自动进行一些基本的配置。
```shell
ssh-copy-id -i /path/to/id_rsa.pub user@192.168.x.xxx
```
如果你的平台没有该工具，可以通过`scp`等方式手动配置。

# 0x03 | 修改sshd_config
少数系统上，公钥登录默认可能是关闭的，需要修改`/etc/ssh/sshd_config`，将`PubkeyAuthentication no`改为`PubkeyAuthentication yes`。

# 0x05 | 重启SSH服务
```
sudo systemctl restart sshd.service
```

# 0x06 | 适应新的手动SSH方式
只有当你通过`-i`参数指定私钥时，`OpenSSH`才会尝试使用公钥登录，而不是密码。
```shell
ssh -i /path/to/id_rsa user_name@192.168.x.xxx
```

# 0x07 | 在VSCode中配置SSH
`VSCode`的`Romote-SSH`插件将会在左侧添加一个“远程资源管理器”的小图标，点击其中的`远程(隧道/SSH)`下的`SSH`右侧隐藏的齿轮按钮即可打开任意位置的配置文件。在配置文件中为所需的`Host`项添加`IdentityFile /path/to/id_rsa`即可。