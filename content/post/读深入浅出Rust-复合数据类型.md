---
title: "读深入浅出Rust 复合数据类型"
date: 2024-02-29T14:48:34Z
draft: true
---

Rust从语法上支持了几种复合数据类型：
<!--more-->
# 0x00 | tuple 元组
就是Python和其他语言里传统意义上的元组。
元组是若干个元素（无论类型）的集合，当然，元组也可以是元素。
```rust
let a = (1i32,false,(1i32,2i32));
```
可以使用传统的数字索引来访问其中的数据：
```rust
println!("{} {}",a.0,a.1);
```

或者，考虑使用模式匹配：
```rust
fn main() {
    let a = (("nihao",114),(514,1919),false);
    let (x,y,z) = a;
    println!("{:?} {:?} {:?}",x,y,z);
}
```
输出如下：
```rust
("nihao", 114) (514, 1919) false
```
~~真是到处都有C++的影子啊，虽然不好说是谁像谁~~

另外，你也可以创建一个空的元组：
```rust
let empty: () = ();
```
虽然但是我并不知道它有什么用，值得一提的是空元组和空结构体在Rust中的大小都是0。
```rust
println!("{}",std::mem::size_of::<()>());
```
输出确实是0。

# 0x01 | struct 结构体
大体上就是个很正常的C-Style struct：
```rust
struct Point {
    x: f32,
    y: f32
}

fn main() {
    let p = Point{x:114.514,y:1919.810};
    println!("x={} y={}",p.x,p.y);
}
```
另外Rust提供了一个为结构体成员变量初始化的语法糖，允许你在参数与成员变量同名时省去重复的冒号：
```rust
fn main() {
    let x = 114.514;
    let y = 1919.810;
    let p = Point{x,y};
    println!("x={} y={}",p.x,p.y);
}
```
同样的，只要变量名与struct的成员变量相同，你也可以使用模式匹配访问struct中的数据：
```rust
fn main() {
    let x = 114.514;
    let y = 1919.810;
    let p = Point{x,y};
    let Point{x,y} = p;
    println!("x={} y={}",x,y);
}
```
虽然这段代码中发生了变量名的覆盖，但是我只是想告诉你可以这样做。

另外，Rust提供了一个语法糖来让你能够在为struct初始化数据时在某个默认的数据组上进行覆盖：
```rust
struct Point {
    x: f32,
    y: f32
}

fn default() -> Point {
    Point{x:0.0,y:0.0}
}

fn main() {
    let x = 114.514;
    let y = 1919.810;
    let p1 = Point{x,..default()};
    let p2 = Point{y,..default()};
    let p3 = Point{..default()};
  
    let Point{x,y} = p1;
    println!("x={} y={}",x,y);
  
    let Point{x,y} = p2;
    println!("x={} y={}",x,y);
  
    let Point{x,y} = p3;
    println!("x={} y={}",x,y);
}
```
虽然，感觉不如直接OO。

# 0x02 | tuple struct 元组结构体
和元组的区别是，元组结构体和结构体一样，有原型，有自己的类型名。但是和结构体不同的是，其中的元素没有名字。
```rust
struct Color(u8,u8,u8,u8);

fn main() {
    let rgba = Color(255,255,255,255);
    println!("r={} g={} b={} a={}",rgba.0,rgba.1,rgba.2,rgba.3);
}
```

# 0x03 | 总述
`tuple`,`struct`和`tuple struct`三者在内存结构与除了申明外的语法上都是高度相似的。但是我已经懒得写，甚至懒得看了。