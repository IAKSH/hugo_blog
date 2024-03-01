---
title: "use_same_binding_point_for_ssbo_and_ubo_in_opengl"
date: 2024-02-29T14:41:33Z
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
description: "SSBO和UBO共用同一组绑定点"
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

[在OpenGL ES 3.2中，Shader Storage Buffer Object (SSBO) 和 Uniform Buffer Object (UBO) 都可以通过绑定点 (binding point) 进行链接](https://zhuanlan.zhihu.com/p/483482044)[1](https://zhuanlan.zhihu.com/p/483482044)[2](http://www.selfgleam.com/interface-block-ubo-ssbo)[。这意味着你可以为每个SSBO和UBO选择不同的绑定点，以避免冲突。例如，你可以在着色器中这样定义](https://blog.csdn.net/What_can_you_do/article/details/125620229)[3](https://blog.csdn.net/What_can_you_do/article/details/125620229)：

```glsl
layout (std140, binding = 0) uniform UBO_data {
    float randNum;
    float vtime;
};

layout (std430, binding = 1) buffer SSBO_data {
    vec3 SSBO_color;
};
```

在这个例子中，`UBO_data` 的绑定点是 `0`，而 `SSBO_data` 的绑定点是 `1`。因此，只要你为每个SSBO和UBO选择不同的绑定点，就不会有冲突。总的来说，SSBO和UBO的绑定点是否会冲突，取决于你如何设置它们的绑定点。如果你为它们选择了相同的绑定点，那么就会发生冲突。如果你为它们选择了不同的绑定点，那么就不会发生冲突。所以，在设置绑定点时，需要确保每个SSBO和UBO都有一个唯一的绑定点。

From Bing