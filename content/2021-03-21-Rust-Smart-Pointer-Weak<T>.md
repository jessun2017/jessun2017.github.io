+++
title = "Rust 智能指针介绍：Weak<T>"
draft = true

[taxonomies]
tags = ["rust"]
categories = [""]
+++

## 介绍

循环引用存在内存泄漏，请看如下代码存在的引用循环问题。
```rust
use std::cell::RefCell;
use std::rc::Rc;

#[derive(Debug)]
enum List {
    Cons(i32, RefCell<Rc<List>>),
    Nil,
}

impl List {
    fn tail(&self) -> Option<&RefCell<Rc<List>>> {
        match self {
            List::Cons(_, item) => Some(item),
            List::Nil => None,
        }
    }
}

fn main() {
    let a = Rc::new(List::Cons(5, RefCell::new(Rc::new(List::Nil))));

    println!("a initial rc count = {}", Rc::strong_count(&a));
    println!("a next item = {:?}", a.tail());

    let b = Rc::new(List::Cons(10, RefCell::new(Rc::clone(&a))));

    println!("a rc count after b creation = {}", Rc::strong_count(&a));
    println!("b initial rc count = {}", Rc::strong_count(&b));
    println!("b next item = {:?}", b.tail());

    if let Some(link) = a.tail() {
        *link.borrow_mut() = Rc::clone(&b);
    }

    println!("b rc count after changing a = {}", Rc::strong_count(&b));
    println!("a rc count after changing a = {}", Rc::strong_count(&a));

    // Uncomment the next line to see that we have a cycle;
    // it will overflow the stack
    // println!("a next item = {:?}", a.tail());
}
```

## 使用时候注意具体需求

1. `Rc::clone`会增加`Rc<T>`实例的`strong_count`引用计数，并指出`Rc<T>`实例只有在`strong_count`为0时才会被清理。
不过除此之外，我们还可以通过调用`Rc::downgrade`函数来创建`Rc<T>` 实例中值的弱引用。

2. 使用`Rc<T>`的引用来调用`Rc::downgrade`函数会返回一个类型为`Weak<T>`的智能指针，
这一操作会让`Rc<T>`中`weak_count`的的计数增加1。`Rc<T>`并不会在执行清理操作前要求`weak_count`必须减为0。

3. 如果只用`Rc<T>`可能会形成循环引用，从而导致内存泄漏。

4. 强引用可以用来共享一个`Rc<T>`实例的所有权，弱引用不会表达所有权关系。
由于无法确定`Weak<T>`引用的值是否已经被释放，所有需要调用`Weak<T>`实例的`upgrade`方法来验证。


## 实现

```rust
use std::{cell::RefCell, rc::Rc, rc::Weak};

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

    println!("leaf parent = {:?}", leaf.parent.borrow().upgrade());
    // leaf parent = None
    println!(
        "leaf strong_count = {}, weak = {}",
        Rc::strong_count(&leaf),
        Rc::weak_count(&leaf)
    );
    // leaf strong_count = 1, weak = 0

    {
        let branch = Rc::new(Node {
            value: 5,
            parent: RefCell::new(Weak::new()),
            children: RefCell::new(vec![Rc::clone(&leaf)]),
        });
        *leaf.parent.borrow_mut() = Rc::downgrade(&branch);

        println!("leaf parent = {:?}", leaf.parent.borrow().upgrade());
        // leaf parent = Some(Node { value: 5, parent: RefCell { value: (Weak) }, children: RefCell { value: [Node { value: 3, parent: RefCell { value: (Weak) }, children: RefCell { value: [] } }] } })
        //
        println!(
            "branch strong_count = {}, weak = {}",
            Rc::strong_count(&branch),
            Rc::weak_count(&branch)
        );

        // branch strong_count = 1, weak = 1
    }

    println!(
        "leaf strong_count = {}, weak = {}",
        Rc::strong_count(&leaf),
        Rc::weak_count(&leaf)
    );
    // leaf strong_count = 1, weak = 0
}
```

