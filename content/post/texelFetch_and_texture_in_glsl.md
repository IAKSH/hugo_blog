---
title: "TexelFetch_and_texture_in_glsl"
date: 2024-02-29T14:38:19Z
draft: true
---

两者都是对纹理进行采样的函数，有两个参数，第一个是纹理采样器，第二个是片元坐标
区别是texelFetch是使用的真正的像素坐标，而texture使用的是归一化坐标。
比如，对于一个64x64的纹理，获取位于32x32点的rgba值，texelFetch的坐标为(32,32)，而texture的坐标为(0.5,0.5)