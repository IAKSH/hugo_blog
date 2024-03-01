---
title: "Dont_call_super_constructor_in_cpp"
date: 2024-02-29T14:32:06Z
draft: true
tags: ["C++"]
author: "IAKSH"
---

因为当你调用一次另自身的其他构造函数时，会重复调用一次基类的构造函数。
大多数时候这最多造成一些性能问题，但是这对于像OpenGL这种全局状态机而言，非常糟糕，甚至是灾难性的。