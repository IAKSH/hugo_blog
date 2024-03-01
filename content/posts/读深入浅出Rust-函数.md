---
title: "读深入浅出Rust 函数"
date: 2024-02-29T14:48:40Z
draft: true
tags: ["Rust"]
author: "IAKSH"
---

大部分是些基础，看菜鸟教程得了。
<!--more-->
不过还是有一些值得写下来的。

# 0x00 | 发散函数
（`Diverging functions`）
发散函数永远不会返回，编译器会检查这一点。也许可以用在一些死循环里，防止一些低级错误。
```rust
fn print(num: (i32,i32)) -> ! {
    println!("{}",num.0 + num.1);
    loop {
        println!("死锁！！！")
    }
}

fn main() {
    print((114,514))
}
```

# 0x01 | 主函数的参数
与C不同，Rust通过标准库函数获取当前应用程序的所有参数。
```rust
fn main() {
    for arg in std::env::args() {
        println!("arg found: {}",arg)
    }
}
```

# 0x02 | 读取环境变量
Rust可以通过标准库函数直接读取环境变量的值
```rust
fn main() {
    for arg in std::env::args() {
        match std::env::var(&arg) {
            Ok(val) => println!("{}: {:?}",&arg,val),
            Err(e) => println!("can't find env {}, {}",&arg,e),
        }
    }
}
```
上述代码是尝试输出每个应用参数所对应的环境变量的值，其中第一个环境变量是程序文件路径，除非你真的有这个名字的环境变量，固定匹配到Err。

# 0x03 | consf fn
和C++的constexpr类似，用于编译期计算，静态求值。
```rust
const fn cube(num: usize) -> usize {
    num.pow(3)
}


fn main() {
    let n = cube(114);
    println!("{}",n)
}
```
书中的Rust版本下，const_fn还是需要手动启用的测试特性。但是目前而言已经并非如此了。而且通过debug，我发现consf fn实际上并未进行静态求值。

我感到很迷惑，但是暂时懒得去Rust文档里查。