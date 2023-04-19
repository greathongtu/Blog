---
layout: '../../layouts/MarkdownPost.astro'
title: 'Rust 学习'
pubDate: 2023-04-15
description: '学习 Rust 等知识总结'
author: 'Aaron'
cover:
    url: '/preview/rust.png'
    alt: 'cover'
tags: ["Javascript"]
theme: 'light'
featured: false
---

## ownership 管理 heap momery

Rust 不会自动深拷贝数据。自动 move 语意：
```rust
let s1 = String::from("Hello");
let s2 = s1;

// 此时 s1 被 move 到 s2 了
// 如果想要深拷贝可以使用 clone()
// copy trait 和 drop trait 冲突 可copy的不会move
```

## 引用
同一时刻只能存在一个 mutable reference
不可以同时存在一个可变引用和一个不可变引用

## slice 不持有ownership
&str
将字符串切片作为参数传递：使用&str 作为参数类型，可以同时接收 String 和 &str 类型的参数

## struct
一旦struct实例可变，那么实例中所有字段都可变
struct 可以放引用，需要使用 lifetime。
println 需要 std::fmt::Display, std::fmt::Debug, #[derive(Debug)], {:?}, {:#?}

method 第一个参数可以是 &self
关联函数：不把self 当作第一个参数的函数。如 String::from() 通常用于 Constructor

## Option 枚举
Rust 没有 null 的概念。用 Option 取代
```rust
enum Option<T> {
    Some(T),
    None,
}
```
Option<T> 比 Null 好在哪儿？
* Option<T> 和 T 的类型不一样
    如在 C# 中： string a = null; string b = a = "123";

## match 枚举
可以匹配Option<T>
需要穷举，可以使用_ 通配符

## if let
比 match 更简洁，可以看作是 match 的语法糖

## 代码组织模块系统 package crate module path 
* package
    * 一个 package 包含一个 Cargo.toml
    * 只能包含 0-1 个 library crate
    * 包含任意数量的 binary crate
    * 至少包含一个 crate
    * 一个 package 可以有多个 binary crate，文件放在 src/bin 目录，每个文件是单独的 binary crate
* module
    * 一个 crate 内，将代码进行分组
    * 控制项目（item）的私有性 public private
* Path （像其他语言的namespace）
    * 绝对路径：从 crate root 开始，使用 crate 名字或者 crate 字面值
    * 相对路径：从当前模块开始，使用 self，super 或当前模块的标识符
    * 标识符使用 :: 分割
    ```Rust
    // src/lib.rs
    mod front_of_house{
        pub mod hosting {
            pub fn add_to_waitlist() {}
        }
    }

    pub fn eat_at_restaurant() {
        crate::front_of_house::hosting::add_to_waitlist();
        front_of_house::hosting::add_to_waitlist();
    }
    ```
    * super 关键字，就像文件系统的 ..
    * use 关键字，就像别的语言的 import
    * pub use 重导出
* 将模块移动到其他文件：模块定义时模块名后面是“;”
    * Rust 会从与模块同名的文件中加载内容
## dependency
* cargo build 的时候blocking， file lock：  找到.cargo文件夹，删除.package-cache文件。
* 换源：在.cargo 下新建 config 文件

## collections

* Vector
* HashMap

## Lifetime

## trait

## closure
闭包捕获值的方式：
1. 取得所有权： FnOnce。 所有闭包都实现了这个
2. 可变借用：FnMut
3. 不可变借用：Fn 尽量先用这个。

move 会夺走 ownership

## iterator
1. iter 方法：不可变引用上创建迭代器
2. into_iter 方法：获得 ownershipp
3. iter_mut : 迭代可变的引用

## workspace

## smart pointer
实现了 Deref 和 Drop trait

1. Box<T>
2. Rc<T> 只能单线程
3. Ref<T> & RefMut<T> 在运行时强制借用规则

内部可变模式（interior mutability pattern）
RefCell<T> : owner. 只能单线程

# Async

## async
* 把一段代码转化为实现了 Future trait 的状态机
* async fn 返回的是 Future， Future 需要一个执行者来执行

```rust
// thread way
use std::thread;
use std::thread::sleep;
use std::time::Duration;

fn main() {
    println!("Hello before reading file!");
    let handle1 = thread::spawn(|| {
        println!("Hello from thread1!");
        sleep(Duration::new(4, 0));
        println!("Hello from file1!");
    });
    let handle2 = thread::spawn(|| {
        println!("Hello from thread2!");
        sleep(Duration::new(2, 0));
        println!("Hello from file2!");
    });
    handle1.join().unwrap();
    handle2.join().unwrap();
}
```

```rust
// async way
use std::thread::sleep;
use std::time::Duration;

#[tokio::main]
async fn main() {
    println!("Hello before reading file!");
    let handle1 = tokio::spawn(async {
        let _file_content = read_from_file1().await;
        println!("{}", _file_content);
    });
    let handle2 = tokio::spawn(async {
        println!("Hello from thread2!");
        sleep(Duration::new(2, 0));
        println!("Hello from file2!");
    });
    async fn read_from_file1() -> String {
        println!("Hello from thread1!");
        sleep(Duration::new(4, 0));
        String::from("Hello from file1!")
    }
    let _ = tokio::join!(handle1, handle2);
}
```

```rust
// aync timer
use std::{
    future::Future,
    pin::Pin,
    task::{Context, Poll},
    thread::sleep,
    time::{Duration, Instant},
}; 

struct AsyncTimer {
    expiration_time: Instant,
}

impl Future for AsyncTimer {
    type Output = String;

    fn poll(self: Pin<&mut Self>, cx: &mut Context<'_>) -> Poll<Self::Output> {
        if Instant::now() >= self.expiration_time {
            println!("Hello, it's time for Future 1");
            Poll::Ready(String::from("Future 1 has completed!"))
        } else {
            println!("Hello, it's not time for Future 1. Going to sleep.");
            let waker = cx.waker().clone();
            let expiration_time = self.expiration_time;
            std::thread::spawn(move || {
                let current_time = Instant::now();
                if current_time < expiration_time {
                    sleep(expiration_time - current_time);
                }
                waker.wake();
            });
            Poll::Pending
        }
    }
}

#[tokio::main]
async fn main() {
    println!("Hello before reading file!");

    let h1 = tokio::spawn(async {
        let future1 = AsyncTimer {
            expiration_time: Instant::now() + Duration::from_millis(4000),
        };
        println!("{:?}", future1.await);
    });
 
    let h2 = tokio::spawn(async {
        let file2_contents = read_from_file2().await;
        println!("{:?}", file2_contents);
    });   

    let _ = tokio::join!(h1, h2);

    async fn read_from_file2() -> String {
        sleep(Duration::new(2, 0));
        String::from("Future 2 has completed!")
    }
}

```

## borrow checker 
局限性 reference：https://www.youtube.com/watch?v=HwupNf9iCJk&ab_channel=CodetotheMoon

