+++
title = "Rust 智能指针介绍: Rc<T> 引用计数"
draft = true

[taxonomies]
tags = ["rust"]
categories = [""]
+++

## 介绍

`Rc<T>`的名字意义为引用计数（reference counting）。
引用计数意味着记录一个值引用的数量来知晓这个值是否仍在被使用。
如果某个值有零个引用，就代表没有任何有效引用并可以被清理。

## 使用场景

- 单线程环境
- 数据被多个变量同时引用，比如二叉树结构等。

## 实现

```rust
use std::rc::Rc;

enum List2 {
    Cons(i32, Rc<List2>),
    Nil,
}

#[test]
fn smart_pointer_rc() {
    let a = Rc::new(List2::Cons(
        1,
        Rc::new(List2::Cons(2, Rc::new(List2::Cons(3, Rc::new(List2::Nil))))),
    ));
    println!("count after creating a = {}", Rc::strong_count(&a));

    let b = List2::Cons(3, Rc::clone(&a));
    println!("count after creating b = {}", Rc::strong_count(&a));

    {
        let c = List2::Cons(4, Rc::clone(&a));
        println!("count after creating c = {}", Rc::strong_count(&a));
    }

    println!("count after c goes out of scope = {}", Rc::strong_count(&a));
}
```
