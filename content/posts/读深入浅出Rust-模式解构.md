---
title: "读深入浅出Rust 模式解构"
date: 2024-02-29T14:49:18Z
draft: true
tags: ["Rust"]
author: "IAKSH"
---

match会涉及到“模式解构”（模式匹配？），大概像这样：
<!--more-->
```rust
[let tuple = (1,false,3.0);
let (x,y,z) = tuple;]
```
然后还有对struct的模式解构，也许也对其他结构适用（？
```rust
struct T1(i32,char);

struct T2 {
    item1: T1,
    item2: bool,
}

  

fn main() {
    let x = T2 {
        item1: T1(0,'A'),
        item2: false,
    };
  
    let T2 {
        item1: T1(value1,value2),
        item2: value3,
    } = x;

    println!("{} {} {}",value1,value2,value3);
}
```
值得一提的是，如果你不想匹配所有内容，可以使用下划线`_`作为占位符以忽略它们。
```rust
struct T1(i32, char);

struct T2 {
    item1: T1,
    item2: bool,
}

fn main() {
    let x = T2 {
        item1: T1(0, 'A'),
        item2: false,
    };

  

    let T2 {
        item1: T1(value1, _),
        item2: value3,
    } = x;
  
    println!("{} {}", value1, value3);
}
```
很好的简化代码的方式，就是有点太灵活了。

---

然后下面是关于match1的了：
我称match为增强版switch，当然也不只是switch。

最简单的一个使用列
```rust
enum Direction {
    East, West, South, North
}

fn print(x: Direction) {
    match x {
        Direction::East => {
            println!("East");
        }
        Direction::West => {
            println!("West");
        }
        _ => {
            println!("Other");
        }
    }
}

fn main() {
    print(Direction::East);
    print(Direction::North);
    print(Direction::South);
    print(Direction::West);
}
```
然而在默认情况下，只需要让match匹配所有分支，就能够通过编译。
可是如果所匹配的内容突然怎加了一个，在这里不会被考虑到的新分支呢？
这种情况下，上游依赖的更新会直接破坏此处的match，导致编译失败，只有修改代码才能解决。

~~这显然不是我们所希望的，于是Rust标准提出了`non_exhaustive`属性，可以强制让匹配有该属性类型的match使用 _ 以匹配其他的分支。~~
属性`#[non_exhaustive]`依然保留，但用法似乎改变，详见Rust官方文档。

然后，match也可以作为表达式，重点是下面的代码中没有return。
```rust
fn abs(x: i32) -> i32 {
    match x {
        x if x < 0 => -x,
        x if x >= 0 => x,
        _ => x,
    }
}

fn main() {
    println!("{}",abs(-114514))
}
```
然后这里用到了`x if ...`，这是”匹配看守“(match guards)。

然后，match中的匹配项也可以做一些基本的逻辑运算：
```rust
fn trans_num(x: i32) -> i32 {
    match x {
        0 | 1 | 2 => 114,
        _ => 514,
    }
}

fn main() {
    println!("{}",trans_num(1));
}
```

还可以结合`..`来表示在一个范围内的匹配：
```rust
fn char_type(x: char) -> &'static str {
    match x {
        'a' ..= 'z' => "lowercase",
        'A' ..= 'Z' => "uppercase",
        _ => "other",
    }
}

fn main() {
    println!("{}",char_type('C'));
    println!("{}",char_type('c'));
    println!("{}",char_type('1'));
}
```

还有一个特性叫变量绑定，简单说就是把match的东西绑定到另一个标识符上。
注意下面的`@`，左边是新的标识符，将x绑定到了其上。
```rust
fn char_type(x: char) -> &'static str {
    match x {
        e @ 'a' ..= 'z' => {
            println!("{} is lowercase",e);
            "lowercase"
        },

        e @ 'A' ..= 'Z' => {
            println!("{} is uppercase",e);
            "uppercase"
        },
        _ => "other",
    }
}

fn main() {
    println!("{}",char_type('C'));
    println!("{}",char_type('c'));
    println!("{}",char_type('1'));
}
```
这个语法基本上只有表达式嵌套的时候有用，比如下面这样：
```rust
#![feature(exclusive_range_pattern)]
fn deep_match(v: Option<Option<i32>>) -> Option<i32> {
    match v {
        Some(r @ Some(1..10)) => r,
        _ => None,
    }
}

  

fn main() {
    let x = Some(Some(5));
    println!("{:?}",deep_match(x));
  
    let y = Some(Some(100));
    println!("{:?}",deep_match(y));
}
```
~~很好，套起来了，头开始痛了~~
很不幸的是(?)，`exclusive_range_pattern`直到`Rust 1.72`依然没有进入`stable`。仅在`nightly`中可用。

然后，上述的使用`@`的绑定实际上是拷贝了被绑定变量。可以使用`ref`以创建其引用而非拷贝。以及，还能够使用mut。
这部分书中讲的比较混乱，不写了，也不看了。
简单说，`ref`是用于模式解构的`&`，两者很大程度上不能通用。

---

模式结构也能用在除了match外的其它一些地方，比如if-let和while-let：
传统的判断和取出Some()中的数据需要有一次判断，然后才能从中读取，像是这样：
```rust
if opt_val.is_some() {
	let x = opt_val.unwarp();
	do_something_with(x);
}
```
虽然也不是不能用，但是感觉很不Rust。
看了之前的内容，你可能会强行使用match然后进行模式解构：
```rust
match opt_val {
	Some(x) => do_something_with(x),
	_ => {},
}
```
虽然但是也没好看多少。

现在引入`if-let`语法：
```rust
if let Some(x) = opt_val {
	do_something_with(x);
}
```
看起来好多了。但是需要注意的是这实际上仅仅是上述match表达式的语法糖，在实际效率上两者并无差异。

至于`while-let`，和上面的`if-let`一样，把`if`换`while`你就知道了。
```rust
fn get_num(x: i32) -> Option<i32> {
    match x {
        x if x > 0 => Some(x),
        _ => None,
    }
}

fn main() {
    for i in -10..10 {
        if let Some(ref x) = get_num(i) {
            println!("{}", x);
        }
    }
}
```

---

最后，模式解构还能用在函数的参数列表中：
```rust
struct T {
    item1: char,
    item2: bool,
}

fn test(T{item1: arg1, item2: arg2}: T) {
    println!("arg1={} arg2={}",arg1,arg2);
}

fn main() {
    let x = T {
        item1: 'A',
        item2: false,
    };
    
    test(x);
}
``` 