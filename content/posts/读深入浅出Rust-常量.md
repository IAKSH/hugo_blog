---
title: "读深入浅出Rust 常量"
date: 2024-02-29T14:47:52Z
draft: true
tags: ["Rust"]
author: "IAKSH"
---

Rust中是有真正的常量的，也就是说，没有mut的let实际上并不是C/C++意义上的常量，只是编译器会阻止你修改它而已。
<!--more-->
```rust
const GLOBAL: i32 = 0;
```
实际上和`let`最大的区别是不能模式匹配，最直接的影响是`const`常量必须在申明时指定类型。