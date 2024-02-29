---
title: "Make_texture_expands_in_opengl"
date: 2024-02-29T14:36:28Z
draft: true
---

纹理坐标要大于1.0f才会是重复贴图，否则是单纯放大
很反直觉

可以让model的scale比例和texcoord的scale比例一直，这样放大后的贴图大小会和放大之前保持一致