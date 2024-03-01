---
title: "读深入浅出Rust 枚举"
date: 2024-02-29T14:49:12Z
draft: true
tags: ["Rust"]
author: "IAKSH"
---

此枚举非彼枚举，Rust的enum更像是C++的std::variant，或者说，是"tagged union"
<!--more-->
```rust
enum Number {
    Integer(i32),
    Float(f32)
}

fn print_number(num: &Number) {
    match num {
        &Number::Integer(value) => println!("Integer({})",value),
        &Number::Float(value) => println!("Float({})",value)
    }
}

fn main() {
    let n1 = Number::Integer(114);
    let n2 = Number::Float(514.1919);
    print_number(&n1);
    print_number(&n2);
    
    println!("sizeof Number: {} byte",std::mem::size_of::<Number>());
    println!("sizeof i32: {} byte",std::mem::size_of::<i32>());
    println!("sizeof f32: {} byte",std::mem::size_of::<f32>());
}
```
输出如下：
```
Integer(114)
Float(514.1919)
sizeof Number: 8 byte
sizeof i32: 4 byte
sizeof f32: 4 byte
```
需要注意的是，这里的`Number`大小之所以会是8byte而非4byte，是因为其中有固定的4byte被用于标记类型。除此之外，其内存分配方式和C的union一致。

值得一提的是，enum还有一些基于ADT（代数类型系统，Algebraic Data Type）的有趣用法，但是这里还是仅仅提一下好了。

至于C中常见的struct递归定义，当然也是可以的，只不过和C/C++一样，struct中不能直接包含它自己，而是它的指针。