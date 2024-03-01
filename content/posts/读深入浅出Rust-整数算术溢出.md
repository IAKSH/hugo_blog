---
title: "读深入浅出Rust 整数算术溢出"
date: 2024-02-29T14:49:50Z
draft: true
tags: ["Rust"]
author: "IAKSH"
---

在`Debug`下，Rust的算术溢出会爆panic，而在release（启用优化）下则只会自动舍弃高位。
<!--more-->
很合理的做法，Debug下进行严格的检查，而release下则以性能为主。

另外，也可以通过给编译器加参数来手动修改这一行为：
```shell
rustc -C overflow-checks=no test.rs
```
通过上述的`no`或`yes`可以手动开关溢出检查。

上面的处理方案要么是当场爆炸，要么是放手不管。有的时候，我们想让程序在一定限度内容容忍算术溢出，或者干脆算术溢出就是计划的一部分。这个时候可以使用标准库中的`.check_*`，`.saturating_*`,`warpping_*`系列函数。这三套函数实现了不同策略的安全的数字运算。当算术溢出时，他们分别会返回`None`（类型是`Option<_>`），`此类型的最大值`，`除去溢出部分后的剩余部分`。
具体见下：
```rust
fn main() {
    let n:i32 = 114;
    println!("{:?}",n.checked_pow(n.try_into().unwrap()));
    println!("{:?}",n.saturating_pow(n.try_into().unwrap()));
    println!("{:?}",n.wrapping_pow(n.try_into().unwrap()));
}
```
输出如下：
```
None
214
0
```

在上述的几种比较合理的处理方案中，`.wrapping_*`的这种对算术溢出的截断处理是最常用的。为了让你的代码看起来不至于到处复读，标准库提供了`std::num::Wrapping<T>`类型，这实际上是对整数类型的装箱，它重载了基本的运算符，任何算术溢出都会被截断处理。
使用该类型，任何算术溢出都不会爆panic，且结果一致。