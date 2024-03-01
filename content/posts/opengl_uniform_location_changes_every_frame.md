---
title: "opengl_uniform_location_changes_every_frame"
date: 2024-02-29T14:44:37Z
# weight: 1
# aliases: ["/first"]
tags: ["OpenGL"]
author: "IAKSH"
# author: ["Me", "You"] # multiple authors
showToc: false
TocOpen: false
draft: false
hidemeta: false
comments: false
description: ""
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

Shader Program的Uniform Location每一帧都会变化
不知道为什么。
我修了快一周的后台渲染不出来的bug，居然是因为这个。
是用RenderDoc发现draw call的时候有个缩放uniform一直是0矩阵。
nmd，为什么