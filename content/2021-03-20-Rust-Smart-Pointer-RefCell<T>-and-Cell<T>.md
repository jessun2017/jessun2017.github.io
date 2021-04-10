+++
title = "Rust Trait 介绍：Cell<T>, RefCell<T>与内部可变性模式"
draft = false

[taxonomies]
tags = ["rust"]
categories = [""]
+++

## 介绍

内部可变性模式（Interior mutability）允许你即使在有不可变引用时也可以改变数据。
`RefCell<T>`代表其数据的唯一的所有权。
`RefCell<T>`允许在运行时执行可变借用检查，所以可以在`RefCell<T>`自身是不可变的情况下修改其内部的值。
在不可变值内部改变值就是内部可变性模式。
`RefCell<T>`在任何时候只允许有多个不可变借用或者一个可变借用，没有突破原有的借用和所有权规则。

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

`Rc<T>`与`RefCell<T>`结合使用
```rust
use std::cell::RefCell;
use std::rc::Rc;

enum List3 {
    Cons(Rc<RefCell<i32>>, Rc<List3>),
    Nil,
}

fn list3() {
    let value = Rc::new(RefCell::new(5));
    let a = Rc::new(List3::Cons(Rc::clone(&value), Rc::new(List3::Nil)));

    let b = List3::Cons(Rc::new(RefCell::new(6)), Rc::clone(&a));
    let c = List3::Cons(Rc::new(RefCell::new(10)), Rc::clone(&a));

    *value.borrow_mut() += 10;

    println!("a after = {:?}", a);
    println!("b after = {:?}", b);
    println!("c after = {:?}", c);
}
```
