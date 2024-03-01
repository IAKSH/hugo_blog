---
title: "Dont_let_skybox_cover_your_eyes_in_opengl"
date: 2024-02-29T14:33:29Z
draft: true
summary: "不要让天空盒遮蔽了你的双眼"
tags: ["OpenGL"]
author: "IAKSH"
---

天空盒被当作常规实体，在启用深度测试的时候渲染了（最早渲染），而且由于天空盒的model矩阵没有位移信息，所以相当于一个1.0f缩放的天空盒套着摄像机且相对静止。

这个问题起初被错误地当做是camera类的far裁剪距离的问题，由于发现同一摄像机类在不修改参数的情况下，在早前的另一个demo中没有出现该问题，所以找到了问题原因。

困扰了我大概一晚上。