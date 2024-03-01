---
title: "Cast_a_glance_at_tinyGLTF"
date: 2024-02-29T14:43:03Z
draft: true
summary: "瞥一眼tinyGLTF"
tags: ["OpenGL"]
author: "IAKSH"
---

# example glview
需要关注的函数大体就只有`SetupMeshState(model, progId);`，`DrawModel(model);` ，以及`DrawNode(model, model.nodes[scene.nodes[i]]);`。

1. 使用`tinygltf::TinyGLTF类`直接载入一个`tinygltf::Model`
```cpp
if (ext.compare("glb") == 0) {
  // assume binary glTF.
  ret =
      loader.LoadBinaryFromFile(&model, &err, &warn, input_filename.c_str());
} else {
  // assume ascii glTF.
  ret = loader.LoadASCIIFromFile(&model, &err, &warn, input_filename.c_str());
}
```
2. 使用`SetupMeshState`函数将`tinygltf::Model`中的各种资源加载到VRAM中（似乎主要是创建缓冲区...顶点缓冲区？）。
3. 在主循环中使用`DrawModel`函数递归的绘制`Model`的所有`Node`（递归发生在`DrawNode`）

看起来tinyGLTF并不太需要我自己建立一个Mesh和Model类，只需要为他的Mesh和Model实现资源加载和缓冲区的创建。也就是完成其与OpenGL这种具体的图形API的交互部分。

但是无论如何都还是要自己建立的，这是处于其他方面的考量。能自行扩展/裁剪的系统总是更好的。