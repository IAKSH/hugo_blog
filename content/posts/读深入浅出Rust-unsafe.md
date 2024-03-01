---
title: "读深入浅出Rust Unsafe"
date: 2024-02-29T14:50:22Z
draft: true
tags: ["Rust"]
author: "IAKSH"
---

unsafe块允许你：
<!--more-->
1. 对裸指针进行解引用
2. 读写可变静态变量
3. 读取union，或者写入union中的非copy成员
4. 调用unsafe函数

unsafe块不一定就真的是不安全的，只是编译器无法对unsafe块进行完整的检查，你需要像写C/C++一样对待unsafe块。

