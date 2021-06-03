+++
title = "Rust 指针综述（DOING）"
draft = false

[taxonomies]
tags = ["rust"]
categories = [""]
+++

## 概念

指针（pointer）是一个包含内存地址的变量的通用概念。Rust 中最常见的指针就是**引用**（reference），引用以`&`符号为标志。
除了引用数据没有任何其他特殊功能，没有额外开销。

**智能指针**（smart pointers）是一类数据结构，表现类似指针，同时拥有额外的元数据和功能。智能指针区别于常规结构体的显著特
性在于其实现了`Deref`和`Drop`。`Deref` trait 允许智能指针结构体实例表现的像引用一样。`Drop`trait 允许我们自定义当智能指
针离开作用域时运行的代码。

- 普通引用：只**借用**数据的指针，不含元数据。

- 智能指针：智能指针**拥有**它们指向的数据，包含元数据和额外的功能或保证。

    - `String`的数据总是有效的 UTF-8 编码
    - `Vec<T>`
    - `Box<T>`，用于在堆上分配值。
    - `Rc<T>`，一个引用计数类型，其数据可以有多个所有者。
    - `Ref<T>`和`RefMut<T>`，通过`RefCell<T>`访问。（`RefCell<T>`是一个在运行时而不是在编译时执行借用规则的类型）。

## 智能指针 - Box

### 介绍

`Box<T>`是最简单的智能指针。`Box<T>`允许将一个值放在堆上而不是栈上。
留在栈上的则是指向堆数据的指针。

### 使用场景

- 当有一个在编译时未知大小的类型，而又想要在需要确切大小的上下文中使用这个类型值的时候
- 当有大量数据并希望在确保数据不被拷贝的情况下转移所有权的时候
- 当希望拥有一个值并只关心它的类型是否实现了特定trait而不是其具体类型的时候

### 示例代码

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

## 智能指针 - Rc<T>（TODO）

### 介绍

`Rc<T>`的名字意义为引用计数（reference counting）。
引用计数意味着记录一个值引用的数量来知晓这个值是否仍在被使用。
如果某个值有零个引用，就代表没有任何有效引用并可以被清理。

### 使用场景

- 单线程环境
- 数据被多个变量同时引用，比如二叉树结构等。

### 示例代码

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

## 智能指针 - Cell<T>，Ref<Cell>



## 智能指针 - Arc<T>（TODO）

## 智能指针 - Cow<T>（TODO）

## 智能指针 - Weak<T>（TODO）

