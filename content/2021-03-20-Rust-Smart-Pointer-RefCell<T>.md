+++
title = "Rust Trait 介绍：RefCell<T>与内部可变性模式"
draft = true


[taxonomies]
tags = [""]
categories = [""]
+++

## 介绍

内部可变性模式（Interior mutability）允许你即使在有不可变引用时也可以改变数据。
`RefCell<T>`代表其数据的唯一的所有权。
`RefCell<T>`允许在运行时执行可变借用检查，所以可以在`RefCell<T>`自身是不可变的情况下修改其内部的值。
在不可变值内部改变值就是内部可变性模式。

## 使用场景

- 单线程环境
- 需要获得执行某些变量的可变

## 实现

```rust
use std::cell::RefCell;

pub trait Messenger {
    fn send(&self, msg: &str);
}

struct MockMessenger {
    messages: RefCell<Vec<String>>,
}

impl MockMessenger {
    fn new() -> MockMessenger {
        MockMessenger {
            messages: RefCell::new(vec![]),
        }
    }
}

impl Messenger for MockMessenger {
    fn send(&self, msg: &str) {
        self.messages.borrow_mut().push(String::from(msg));
    }
}

```

