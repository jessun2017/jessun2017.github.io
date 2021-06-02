+++
title = "Rust 指针综述（TODO）"
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

## 智能指针 - Box（TODO）

## 智能指针 - Rc<T>（TODO）

## 智能指针 - Cell<T>，Ref<Cell>（TODO）

## 智能指针 - Arc<T>（TODO）

## 智能指针 - Cow<T>（TODO）

## 智能指针 - Weak<T>（TODO）

