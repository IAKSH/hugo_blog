---
title: "Init_arrays_in_opengles"
date: 2024-02-29T14:39:12Z
draft: true
summary: "OpenGL ES does not allow non-Ctor initialization of arrays"
tags: ["OpenGL"]
author: "IAKSH"
---

这是因为OpenGL ES初始化一个数组的方式比较离谱，像这样：
```glsl
float weight[5] = float[] (0.2270270270, 0.1945945946, 0.1216216216, 0.0540540541, 0.0162162162);
```
传统的，类似C数组的语法只能在完全体的OpenGL中使用，比如：
```
float weight[5] = {0.2270270270, 0.1945945946, 0.1216216216, 0.0540540541, 0.0162162162};
```