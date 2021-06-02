+++
title = "Rust 智能指针 Box"
draft = true

[taxonomies]
tags = ["rust"]
categories = [""]
+++

## 介绍

`Box<T>`是最简单的智能指针。`Box<T>`允许将一个值放在堆上而不是栈上。
留在栈上的则是指向堆数据的指针。

## 使用场景

- 当有一个在编译时未知大小的类型，而又想要在需要确切大小的上下文中使用这个类型值的时候
- 当有大量数据并希望在确保数据不被拷贝的情况下转移所有权的时候
- 当希望拥有一个值并只关心它的类型是否实现了特定trait而不是其具体类型的时候

## 实现

```rust
#[derive(Debug)]
enum List {
    Cons(i32, Box<List>),
    Nil,
}

#[test]
fn main() {
    let list = List::Cons(
        1,
        Box::new(List::Cons(2, Box::new(List::Cons(3, Box::new(List::Nil))))),
    );

    println!("List {:?}", list);
}
```
