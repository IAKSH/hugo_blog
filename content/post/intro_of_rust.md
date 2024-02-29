---
title: "Intro_of_rust"
date: 2024-02-29T15:06:57Z
draft: true
summary: "比较早期的一篇文章，可能存在问题"
---


# 前言
我使用Rust进行编码的经验并不多，以下内容可能有失偏颇，甚至是错误的，欢迎指正。

# 概述
Rust是一门新兴的编译型编程语言，它吸收了来自JavaScript和C++的一些特性与思想，能够快速构建一个内存安全，风格统一，使用方便，且性能不俗的程序。
Rust的对手是C++，但至少在短期内，Rust无法完全替代C/C++，且目前的Rust本身也依附于C/C++的构建体系。

不得不提的是，Rust最大的卖点就是其安全性，然而Rust的安全性也不是绝对的，且所谓安全性完全是对程序员的行为进行严格限制换来的。按照C/C++的程序设计思路，许多功能在Rust中只能使用unsafe块实现，然而这也就失去了Rust强调的安全性。当然，也许这不是Rust的问题，而是我们这些依然在使用C/C++思路的程序员的问题？

# 工具
Rust提供了rustc用于编译，Cargo作为构建工具和包管理器。
Rust本身并不提供调试器，也不需要提供，请使用lldb进行调试。

# 开发环境
目前还没有专为Rust设计的集成式开发环境，Rust官网提供了几种解决方案，其中最受欢迎的一种*可能*是`Visual Studio Code` + `rust-analyzer`。后者是一个基于LSP（**Language Server** Protocol）的VSCode插件，用于提供自动补全，静态检查等功能。该插件的后端是Rust提供的同名语言服务器程序。
除此之外，`rust-analyzer`插件还集成了对`CodeLLDB`插件的支持，后者使VSCode能够使用lldb进行对C/C++以及Rust程序的调试。

# 项目结构
Rust以下述三个概念组织项目。
1. 箱（Crate）
2. 包（Package）
3. 模块（Module）

他们的包含关系是这样的：
```
Package A -> 编译为 Crate A
 ├── Module A
 │ ├── main.rs
 │ └── source1.rs
 ├── Module B
 │ ├── source1.rs
 │ └── source2.rs
 └── Module C
   ├── source1.rs
   └── source2.rs
```
`Package`的概念类似于C/C++项目中的工程，一个Rust项目是由至少一个`Package`组成的。
`Module`类似于Python中的模块概念，一个文件就是一个模块，但Rust的Module可以嵌套。
`Crate`实际上就是二进制文件，可以是二进制可执行文件，也可以是库文件。举个例子，你编译出来的程序本来是个Crate，你用到的第三方库下载到本地的也是个Crate，然后把他们链接，就有了可执行程序。

Rust的编译过程实际上就是生成Crate的过程，在这之后还需要链接各个Crate才能生成最终的可执行文件。

Crate的编译过程是树状的，其根部为main函数所在文件，通过读取每个树节点中所申明的模块标识符以寻找源文件并创建新的树节点。这意味着在Rust中，一个最基本的多文件项目可能是这样的：
```rust
// main.rs
mod mylib

fn main() {
	println!("{}",mylib::my_func());
}

// mylib.rs
pub fn my_func() -> i32 {
	return 1;
}
```

# 面向对象
Rust处处面向对象，不过和C++以及Java中的面向对象不同，Rust对整个OO系统进行了一定程度的裁剪，使之更加类似于Python中的OO概念。
一部分表现如下：
1. Rust的面向对象主要基于`struct`，但也可以是`module`
2. 在`struct`和`module`中，一切成员默认为`private`，`public`需要使用`pub`显式地指定
3. Rust并没有类（class）这个关键字，其“类”概念是使用struct和对应impl块实现的，前者储存数据，后者实现逻辑。
4. 由于上述设计，Rust的类并不存在构造函数，但是依然有这样的概念。对于一个类，程序员通常会在其`impl`块中定义一个静态的new函数，该函数能够访问`struct`的`private`数据，由其充当该类的构造函数。
5. Rust保留了析构函数，其定义形式如下：
```rust
pub struct MyStruct {
	id: i32
}

impl MyStruct {
	// 我们自定义的实现构造功能的函数
	pub fn new() -> MyStruct {
		MyStruct {
			id: 1
		}
	}
}

// 对MyStruct类实现的析构函数块
// 其中的函数名必须是drop，且self可变（毕竟要销毁了）
impl Drop for MyStruct {
	fn drop(&mut self) {
        println!("drop");
    }
}
```

# 安全性
Rust的策略是在编译期最大限度的解决安全问题，尽量保证通过编译后的程序不会出现明显的问题。主要从下述两个方面实现。

## 编码风格
Rust严格限制程序员所使用的缩进和命名风格。如果你不遵守，那么rustc将使用警告对你进行精神攻击。

## 内存安全
内存安全问题可能是程序设计中所面临的最常见，同时也是最严重的问题。内存安全问题包括但不限于内存泄漏和访问冲突。
Rust强制程序员使用RAII机制管理内存，同时程序中的所有变量默认为常量，模块和结构体中所有的成员默认为私有。如果你需要可变变量或共有成员，你需要分别使用`mut`和`pub`关键字显式地进行指定。同时，rustc和rust-analyzer也会严格地检查你的代码中是否有不必要的，可能导致问题的内容，比如根本不会变化的`mut`变量，或者是从来没有用过的函数。


# 错误处理
Rust没有使用任何一种传统的异常处理机制，在Rust中，错误被分为`可恢复错误`和`不可恢复错误`。
## 不可恢复错误
对于不可恢复错误，最好的做法就是让程序当场炸掉，Rust使用panic!宏实现了这一机制。
```rust
fn main() {  
    panic!("error occured");  
    println!("Hello, Rust");  
}
```
输出如下：
```
thread 'main' panicked at 'error occured', src\main.rs:3:5
note: run with `RUST_BACKTRACE=1` environment variable to display a backtrace.
```
第二行输出中提到在环境变量中加入`RUST_BACKTRACE=1`可以进行回溯（backtrace），这是一个非常实用的功能，对于另一串代码，其效果如下：
```
thread 'main' panicked at 'CPU id can't be 1', src/cpu.rs:14:13
stack backtrace:
   0: std::panicking::begin_panic
             at /usr/src/rustc-1.48.0/library/std/src/panicking.rs:505
   1: emu51::cpu::VirtCPU::get_id
             at ./src/cpu.rs:14
   2: emu51::main
             at ./src/main.rs:7
   3: core::ops::function::FnOnce::call_once
             at /usr/src/rustc-1.48.0/library/core/src/ops/function.rs:227
note: Some details are omitted, run with `RUST_BACKTRACE=full` for a verbose backtrace.
```
## 可恢复错误
C程序中通常使函数返回一个int来表示是否发生错误，Rust对可恢复错误的处理方式其实和C的这种差不多，不过返回的是一个泛型枚举类，其定义如下：
```rust
enum Result<T, E> {
    Ok(T),
    Err(E),
}
// 由于这是一个模板枚举，所以可以
```
Rust标准库中所有可能出现可恢复错误的函数的返回值都是该泛型枚举类，例如，当打开一个文件时，可能因为某种原因导致无法打开：
```rust
use std::fs::File;  
  
fn main() {  
    let f = File::open("hello.txt");  
    match f {  
        Ok(file) => {  
            println!("File opened successfully.");  
        },  
        Err(err) => {  
            println!("Failed to open the file.");  
        }  
    }  
}
```

你也完全可以在自己的函数中使用`Result<T,E>`进行错误处理：
```rust
fn f(i: i32) -> Result<i32, bool> {  
    if i >= 0 { Ok(i) }  
    else { Err(false) }  
}  
  
fn main() {  
    let r = f(10000);  
    if let Ok(v) = r {  
        println!("Ok: f(-1) = {}", v);  
    } else {  
        println!("Err");  
    }  
}
```
值得一提的是，如果有多层调用，需要传递`Result<T,E>`，可以使用如下语法进行简化：
```rust
fn f(i: i32) -> Result<i32, bool> {  
    if i >= 0 { Ok(i) }  
    else { Err(false) }  
}  
  
fn g(i: i32) -> Result<i32, bool> {  
    let t = f(i)?; // 当为Err时，直接返回。
    Ok(t) // 因为确定 t 不是 Err, t 在这里已经是 i32 类型  
}  
  
fn main() {  
    let r = g(10000);  
    if let Ok(v) = r {  
        println!("Ok: g(10000) = {}", v);  
    } else {  
        println!("Err");  
    }  
}
```

当然，有些时候你根本不想处理错误，干脆就让程序直接炸掉好了。你可以通过如下手段将可恢复错误转换为不可恢复错误：
```rust
use std::fs::File;  
  
fn main() {  
	// 方法1：使用.unwarp()
    let f1 = File::open("hello.txt").unwrap();  
    // 方法2： 使用.expect(message: &str)
    let f2 = File::open("hello.txt").expect("Failed to open.");
    // 实际上就是当Result为Err时调用panic!宏
    // 两者的唯一区别是后者能在炸掉前发送输出一个字符串
}
```
如果你还是思恋传统的`try..catch`，可以试试`.kind()`：
```rust
use std::io;  
use std::io::Read;  
use std::fs::File;  
  
fn read_text_from_file(path: &str) -> Result<String, io::Error> {  
    let mut f = File::open(path)?;  
    let mut s = String::new();  
    f.read_to_string(&mut s)?;  
    Ok(s)  
}  
  
fn main() {  
    let str_file = read_text_from_file("hello.txt");  
    match str_file {  
        Ok(s) => println!("{}", s),  
        Err(e) => {  
            match e.kind() {  
                io::ErrorKind::NotFound => {  
                    println!("No such file");  
                },  
                _ => {  
                    println!("Cannot read the file");  
                }  
            }  
        }  
    }  
}
```

# 泛型和特性
Rust的泛型和C++的模板差不多，一些差异看看报错就得了，不写了。
至于特性（trait），有点类似与C++之Concepts，Java之Interface。
## 使用特性标识类应该有的函数
```rust
trait Descriptive {
    fn describe(&self) -> String;
}

struct Person {  
    name: String,  
    age: u8  
}  

// 特性Descriptive在Person类中的实现
// 格式为impl <特性名> for <所实现的类型名>
// Rust 同一个类可以实现多个特性，每个 impl 块只能实现一个。
impl Descriptive for Person {  
    fn describe(&self) -> String {  
        format!("{} {}", self.name, self.age)  
    }  
}
```
顺便一提，Rust中的析构函数也使用了`trait`
另外，Rust的`trait`还可以提供默认实现
```rust
trait Descriptive {  
    fn describe(&self) -> String {  
        String::from("[Object]")  
    }  
}  
  
struct Person {  
    name: String,  
    age: u8  
}  
  
impl Descriptive for Person {  
    fn describe(&self) -> String {  
        format!("{} {}", self.name, self.age)  
    }  
}  
  
fn main() {  
    let cali = Person {  
        name: String::from("Cali"),  
        age: 24  
    };  
    println!("{}", cali.describe());  
}
```
如果去掉impl Descriptive for Person块中的内容，则最终输出的是`[Object]`。
感觉又有点像C++的虚继承了？
## 使用特性约束参数类
```rust
fn output(object: impl Descriptive) {
    println!("{}", object.describe());
}
```
在这个例子中，`Descriptive`是一个`trait`，其约束了参数object必须符合该特性
你也可以使用泛型实现上述函数，虽然这仅仅是一个语法糖
```rust
fn output<T: Descriptive>(object: T) {
    println!("{}", object.describe());
}
```
如果你有多个参数都要符合这个特性，泛型语法糖可以让代码看起来更简洁：
```rust
fn output_two<T: Descriptive>(arg1: T, arg2: T) {
    println!("{}", arg1.describe());
    println!("{}", arg2.describe());
}
```
如果需要参数需要符合多个特性，可以使用`+`将各个特性直观地连接起来
```rust
fn notify(item: impl Summary + Display)
fn notify<T: Summary + Display>(item: T)
```
如果你的特性匹配情况实在太复杂，这种写法可能不会很好看，可以使用`where`进行简化：
```rust
// fn some_function<T: Display + Clone, U: Clone + Debug>(t: T, u: U)
// 使用where简化如下：
fn some_function<T, U>(t: T, u: U) -> i32
    where T: Display + Clone,
          U: Clone + Debug
```
# 使用特性约束返回类
```rust
fn person() -> impl Descriptive {  
    Person {  
        name: String::from("Cali"),  
        age: 24  
    }  
}
```
这个函数只能返回一个实现了`Descriptive`特性的类
# 有条件的实现
使用`trait`还能再编译期判断一个类是否应该拥有某个函数，示例如下：
```rust
struct A<T> {}

impl<T: B + C> A<T> {
    fn d(&self) {}
    fn e(&self) {}
}
```
其中，泛型类A只有在符合B和C这两个特性时才会有函数d和e。

