---
title: "读深入浅出Rust 浮点"
date: 2024-02-29T14:48:24Z
draft: true
---

Rust的浮点可以表示正常的，基于`IEEE 754-2008`标准的浮点类型，但是也可以表示一些正常之外的值：标准库中的`std::num::FpCategory`是一个`enum`，其表示了浮点数的所有状态：
<!--more-->
```rust
enum FpCategory {
	Nan,
	Infinite,
	Zero,
	Subnormal,
	Normal
}
```
其中，`Subnormal`和`Zero`是为了兼容`IEEE 754`标准而存在的。前者的引入是为了保证当数值过于趋近0时，不会发生太明显的跳变（跳变到0）。`Subnormal`状态下，浮点数的精度会比`Normal`时稍差。
运行如下代码，你可以直观地感受这个过程：
```rust
fn main() {
    let mut x = std::f32::EPSILON;
    while x > 0.0 {
        x = x / 2.0;
        println!("{} {:?}",x,x.classify());
    }
}
```
当上述循环进行数十次后，浮点数所表示的值已经远小于32bit范围内合理表达的程度了。此时进入了`Subnormal`状态，以牺牲一定的精度换取更小的数值表示。

`Infinite`和`Nan`（`Not A Number`)则是为了应对更棘手的情况存在的。比如将一个浮点数除以`0.0`。
```rust
fn main() {
    let x = 1.0f32 / 0.0;
    let y = 0.0f32 / 0.0;
    println!("{} {}",x,y);

}
```

`Nan`是一个非常特殊的存在，因为它破坏了浮点类型原本的`全序关系`。因为`Nan`，一个浮点可以不等于它自己。
```rust
fn main() {
    let x = std::f32::NAN;
    println!("{} {} {}",x < x, x > x, x == x);
}
```
输出如下：
```
false false false
```
恐怖吗？

