+++
title = "Rust From Scratch chap.07 | Rust 中的指针与智能指针"
draft = true
[taxonomies]
tags = ["Rust"]
categories = ["Rust From Scratch"]
+++

# 基本概念

指针（pointer, ptr）是一个包含内存地址的通用概念。
Rust 最常见的指针就是引用（reference）。
引用（reference）以`&`符号为标志并借用它们所指向的值。
除了引用数据没有任何其他特殊功能，它们没有任何额外开销，所以应用得最多。

智能指针（smart pointers）是一类数据结构。**表现类似指针，但是也拥有额外的元数据和功能** 。
智能指针（smart pointers）通常使用结构体实现，智能指针区别于常规结构体的显著特性在于其实现了`Deref`和`Drop`trait。

> `String`和`Vec<T>`也是智能指针

在 Rust 中，普通引用和智能指针的一个额外的区别是引用是一类只借用数据的指针。相反，在大部分情况下，智能指针**拥有**他们指向的数据。

来自标准库的常用的智能指针：
- `Box<T>`用于在堆上分配值
- `Rc<T>`一个引用计数类型，其数据可以有多个所有者。
- `Ref<T>`和`RefMut<T>`，通过`RefCell<T>`访问。`RefCell<T>`是一个在运行时而不是在编译时执行借用规则的类型。

# `Box<T>` 指针

`Box<T>` 是最简单的智能指针，它可以使一个值存储在堆上而不是栈上。留在栈上的则是指向堆数据的指针。
一般用于一下场景：
- 当有一个在编译时未知大小的类型，而又想要在需要确切大小的上下文中使用这个类型值的时候
- 当有大量数据并希望在确保数据不被拷贝的情况下转移所有权的时候
- 当希望拥有一个值并只关心它的类型是否实现了特定 trait 而不是其具体类型的时候

```rust
fn main() {
    let b = Box::new(5);
    println!("b = {}", b);
}
```

使用`Box<T>`来实现`cons list`
```rust
#[derive(Debug)]
enum List {
    Cons(i32, Box<List>),
    Nil,
}
#[test]
fn cons_list_example() {
    let list = List::Cons(
        1,
        Box::new(List::Cons(2, Box::new(List::Cons(3, Box::new(List::Nil))))),
    );
    println!("cons_list_example: {:?}", list);
}
```

# `Rc<T>`引用计数智能指针

某些场景下需要支持多重所有权，例如在图数据结构中，多个边可能指向相同的节点。
`Rc<T>`，引用计数（reference counting）。引用计数意味着记录一个值引用的数量来知晓这个值是否仍在被使用。
如果某个值有零个引用，就代表没有任何有效引用并可以被清理。

`Rc<T>`只能用于单线程场景。

# `RefCell<T>`与内部可变性

```rust
use std::cell::RefCell;

trait Messenger {
    fn send(&self, msg: &str);
}

#[derive(Debug)]
struct LimitTracker<'a, T: Messenger> {
    messenger: &'a T,
    value: usize,
    max: usize,
}

impl<'a, T> LimitTracker<'a, T>
where
    T: Messenger,
{
    fn new(messenger: &T, max: usize) -> LimitTracker<T> {
        LimitTracker {
            messenger,
            value: 0,
            max,
        }
    }

    fn set_value(&mut self, value: usize) {
        self.value = value;
        let percentage_of_max = self.value as f64 / self.max as f64;

        match percentage_of_max {
            x if x >= 1.0 => self.messenger.send("Error: You are over your quota!"),
            x if x >= 0.9 => self
                .messenger
                .send("Urgent warning: You're used up over 90% of your quota!"),
            x if x >= 0.75 => self
                .messenger
                .send("Waring: You're used up over 75% of your quota!"),
            _ => {}
        }
    }
}

#[derive(Debug)]
struct MockMessenger {
    sent_messages: RefCell<Vec<String>>,
}

impl MockMessenger {
    fn new() -> MockMessenger {
        MockMessenger {
            sent_messages: RefCell::new(vec![]),
        }
    }
}

impl Messenger for MockMessenger {
    fn send(&self, msg: &str) {
        self.sent_messages.borrow_mut().push(String::from(msg))
    }
}

#[test]
fn it_sends_an_over_75_percent_warning_message() {
    let mock_messenger = MockMessenger::new();
    let mut limit_tracker = LimitTracker::new(&mock_messenger, 100);

    limit_tracker.set_value(74);

    println!("=== {:?}", limit_tracker)
}
```

`Rc<T>`与`RefCell<T>`结合使用
```rust
enum List {
    Cons(Rc<RefCell<i32>>, Rc<List>),
    Nil,
}


fn main() {
    let value = Rc::new(RefCell::new(5));

    let a = Rc::new(List::Cons(Rc::clone(&value), Rc::new(List::Nil)));
    let b = List::Cons(Rc::new(RefCell::new(6)), Rc::clone(&a));
    let c = List::Cons(Rc::new(RefCell::new(10)), Rc::clone(&a));

    *value.borrow_mut() += 10;

    println!("a after = {:?}", a);
    println!("b after = {:?}", b);
    println!("c after = {:?}", c);
}
```

# 使用`Weak<T>`代替`Rc<T>`来避免循环引用

`Rc::clone`会增加`Rc<T>`实例的`strong_count`，只有当`string_count`为0时，才会清理`Rc<T>`实例。
`Rc::downgrade`会得到`Weak<T>`类型的智能指针。`Rc<T>`类型使用`weak_count`来记录其存在多少个`Weak<T>`引用，类似于`string_count`。
强引用代表如何共享`Rc<T>`实例的所有权，但弱引用并不属于所有权关系。他们不会造成引用循环，因为任何弱引用的循环会在其相关的强引用计数为0时被打断。
由于`Weak<T>`引用的值可能已经被丢弃了，为了使用`Weak<T>`所指向的值，我们必须确保其值仍然有效。为此可以调用`Weak<T>`实例的`upgrade`方法，这会返回`Option<Rc<T>>`。
如果`Rc<T>`还未被丢弃，则结果是`Some`。如果`Rc<T>`已被丢弃，则结果是`None`。因为`upgrade`返回一个`Option<T>`，所以它不会返回非法指针。

```rust
use std::cell::RefCell;
use std::rc::{Rc, Weak};

#[derive(Debug)]
struct Node {
    value: i32,
    parent: RefCell<Weak<Node>>,
    children: RefCell<Vec<Rc<Node>>>,
}

#[test]
fn main() {
    let leaf = Rc::new(Node {
        value: 3,
        parent: RefCell::new(Weak::new()),
        children: RefCell::new(vec![]),
    });

    println!("=== 1 === leaf parent {:?}", leaf.parent.borrow().upgrade());

    let branch = Rc::new(Node {
        value: 5,
        parent: RefCell::new(Weak::new()),
        children: RefCell::new(vec![Rc::clone(&leaf)]),
    });

    println!(
        "=== 2 === leaf parent = {:?}",
        leaf.parent.borrow().upgrade()
    );

    *leaf.parent.borrow_mut() = Rc::downgrade(&branch);

    println!(
        "=== 3 === leaf parent = {:?}",
        leaf.parent.borrow().upgrade()
    );
}
```

