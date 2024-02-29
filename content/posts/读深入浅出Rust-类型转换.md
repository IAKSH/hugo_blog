---
title: "读深入浅出Rust 类型转换"
date: 2024-02-29T14:49:04Z
draft: true
---

为了减少代码中的不确定性，Rust希望完全杜绝隐式类型转换。Rust提供了`as`关键字以进行类型转换。
<!--more-->
`as`关键字所进行的转换是被限制的，只有编译器认为合理的转换才能通过编译。
```rust
fn main() {
    let x:f32 = 0.5;
    println!("{:?}",x as i32);
}
```
输出为`0`

因为严格的编译期检查，一些转换需要分为多步完成。
```rust
fn main() {
    let x:f32 = 0.5;
    // can't turn this into *mut f32 directly
    //let p1 = &x as *mut f32;
    let p2 = &x as *const f32 as *mut f32;
    println!("{:p}",p2);
}
```

然而`as`这种基于语法关键字的转换肯定只能用在一些基础类型上的。至于更复杂的转换，考虑使用标准库中的`From Into`等`trait`。