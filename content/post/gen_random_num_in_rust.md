---
title: "Gen_random_num_in_rust"
date: 2024-02-29T15:07:41Z
draft: true
---

Rust标准库中并没有随机数生成器，想要实现此类功能，你需要自行设计（纯Rust或FFI到C），或者使用第三方包。
一个常见的解决方案是使用`rand`包，该项目基于`Apache-2.0 和 MIT协议`开源，且拥有完备的文档：
<!--more-->
[rust-random/rand: A Rust library for random number generation. (github.com)](https://github.com/rust-random/rand)

# 0x00 | 在Cargo中导入

在`Cargo.toml`的`[dependencies]`下加入以下内容即可：
```toml
rand = "*"
```
整个文件看起来会象是这样：
```toml
[package]
name = "hello_world"
version = "0.1.0"
edition = "2021"

# See more keys and their definitions at https://doc.rust-lang.org/cargo/reference/manifest.html

[dependencies]
rand = "*"
```

# 0x01 | 生成随机数
`rand`包提供了`rand::Rng`以生成随机数，这是一个随机数生成器，它的使用是这样的：
```rust
use rand::Rng;

fn main() {
	let mut rng = rand::thread_rng();
	let i: i32 = rng.gen();
	let f: f32 = rng.gen();
	println!("i={},f={}",i,f);
	println!("random u8: {}",rng.gen::<u8>());
	println!("random u32: {}",rng.gen::<u32>());
}
```

# 0x02 | 给定范围生成随机数
`Rng::gen_range`函数能够在一个左闭又开的区间内生成随机值：
```rust
use rand::Rng;

fn main() {
	let mut rng = rand::thread_rng();
	println!("random integer: {}",rng.gen_range(0..10)); // 在[0,10)内生成随机整数
	println!("random float: {}",rng.gen_range(0.0..10.0)); // 在[0,10)内生成随机浮点数
}
```
`Rng::gen_range`函数使用方便，但当在相同范围内连续生成随机数时，其效果可能不尽人意。使用`Uniform`模块可以得到符合`均匀分布`的值，以改善这一问题。如下：
```rust
use rand::distributions::{Distribution, Uniform};

fn main() {
	let mut rng = rand::thread_rng();
	let die = Uniform::from(1..7);
	loop {
		let throw = die.sample(&mut rng);
		println!("Rool the die: {}",throw);
		if throw == 6 {
			break;
		}
	}
}
```
`rand_distr`是另一个包，它提供了基于rand包的数种分布模型，你可以在[rand_distr - Rust (docs.rs)](https://docs.rs/rand_distr/0.4.3/rand_distr/index.html)中找到相关信息。
以下是生成一个符合`正态分布`的随机数的实列：
```rust
use rand_distr::{Distribution, Normal, NormalError};
use rand::thread_rng;
  
fn main() -> Result<(), NormalError> {
    let mut rng = thread_rng();
    let normal = Normal::new(2.0,3.0)?;
    let v = normal.sample(&mut rng);
    println!("spawn random num {} from N(2,6)",v);
    Ok(())
}
```


# 0x03 | 给定类型生成随机值
`rand`包可以直接生成一个随机数元组：
```rust
use rand::Rng;

fn main() {
	let mut rng = rand::thread_rng();
	let rand_tuple = rng.gen::<(i32,bool,f64)>();
	println!("Random tuple: {:?}",rand_tuple);
}
```
不过这种方法并不能指定随机数的范围，如果你真的需要限制其范围，可能需要手动生成每一个值。比如：
```rust
use rand::Rng;

fn main() {
    let mut rng = rand::thread_rng();
    let rand_tuple = (
        rng.gen_range(0..100),
        rng.gen::<bool>(),
        rng.gen_range(0.0..1.0)
    );
    println!("Random tuple: {:?}", rand_tuple);
}
```
`rand`包也允许你通过为`Standard`类实现`Distribution`特性以将随机数生成器集成到你的类中，如下：
```rust
use rand::Rng;
use rand::distributions::{Distribution, Standard};

#[derive(Debug)]
struct Point {
	x: i32,
	y: i32
}

impl Distribution<Point> for Standard {
	fn sample<R: Rng + ?Sized>(&self, rng: &mut R) -> Point {
		let (rand_x,rand_y) = rng.gen();
		Point {
			x: rand_x,
			y: rand_y
		}
	}
}

fn main() {
	let mut rng = rand::thread_rng();
	let rand_point: Point = rng.gen();
	println!("Random Point: {:?}",rand_point);
}
```
通过修改`sample`函数可以进一步指定范围和分布模型。

# 0x04 | 生成随机字符串
`rand`包可以生成一个指定长度的随机ASCII字符串，其样本为`A-Z,a-z,0-9`。
一个生成包含30个ASCII字符的随机字符串的例子如下：
```rust
use rand::{thread_rng, Rng};
use rand::distributions::Alphanumeric;

fn main() {
	let rand_string: String = thread_rng()
		.sample_iter(&Alphanumeric)
		.take(30)
		.map(char::from)
		.collect();

	println!("{}", rand_string);
}
```
当然，也可以从指定的字符集中生成随机字符串：
```rust
use rand::Rng;

fn main() {
    const CHARSET: &[u8] = b"123ABC?!";
    let mut rng = rand::thread_rng();
    let password: String = (0..30)
        .map(|_| {
            let idx = rng.gen_range(0..CHARSET.len());
            CHARSET[idx] as char
        })
        .collect();
        
    println!("{:?}", password);
}
```

---
参考：
[生成随机值 - Rust Cookbook 中文版 (rustwiki.org)](https://rustwiki.org/zh-CN/rust-cookbook/algorithms/randomness.html)