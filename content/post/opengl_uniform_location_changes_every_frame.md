---
title: "Opengl_uniform_location_changes_every_frame"
date: 2024-02-29T14:44:37Z
draft: true
---

Shader Program的Uniform Location每一帧都会变化
不知道为什么。
我修了快一周的后台渲染不出来的bug，居然是因为这个。
是用RenderDoc发现draw call的时候有个缩放uniform一直是0矩阵。
nmd，为什么