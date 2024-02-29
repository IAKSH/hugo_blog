---
title: "读深入浅出Rust Panic"
date: 2024-02-29T14:50:11Z
draft: true
---

实际上panic有两种实现机制：unwind和abort。
前者是触发panic后逐层退栈，期间正常析构
后者则是非常传统的abort，让程序当场爆炸。
<!--more-->
由于unwind会先尝试退栈，所以比较容易追踪堆栈，还原现场。
但是，unwind在一些比如嵌入式裸机平台上无法实现，或是实现代价很大，所以abort的保留是有必要的。

编译器允许你指定panic实现方式：
```shell
rustc -C panic=unwind test.rs
rustc -C panic=abort test.rs
```

# unwind
你可能已经发现了，unwind的机制很像是C++和其他大多数语言中的，传统的throw机制。
在rust中你的确可以这样用，Rust提供了一些工具函数让你能够手动终止栈展开：
```rust
use std::panic;

fn main() {
    panic::catch_unwind(|| {
        let x: Option<i32> = None;
        x.unwrap();
        println!("interrupted!");
    }).ok();
    
    println!("continue to execute!");
    for i in 0..3 {
        println!("i = {}",i);
    }
}
```

虽然但是，unwind机制并不是用来模拟传统`try-catch`的，Rust推荐的错误处理方式是依靠返回值的。panic的unwind机制是为处理”继续执行所导致的危害比崩溃更严重“这种情况而存在的，它是最后一种错误处理机制，是处理错误的最后手段。

`panic::catch_unwind`的一种使用情景见下2.

# panic的注意事项
1. 在FFI场景下，对于从C中调用的Rust函数，如果Rust内部出现了panic，并且没有在Rust内被处理，这个panic将会直接被扔到C代码的堆栈中去，通常会造成UB。
2. 在某些高级抽象模型中，需要阻止栈展开：比如线程池，当线程出现panic，应当只关闭该线程，而不是仍其将整个线程池拖下水。