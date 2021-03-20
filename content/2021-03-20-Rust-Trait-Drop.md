+++
title = "Rust Trait 介绍: Drop"
draft = false


[taxonomies]
tags = ["rust"]
categories = [""]
+++

## 介绍

`Drop` trait 可以让一个变量在离开作用域时执行一些代码。

官方文档地址：https://doc.rust-lang.org/std/ops/trait.Drop.html

## 使用场景

在 Rust 中，可以指定每当值离开作用域时被执行的代码，编译器会自动插入这些代码。

## 实现


```rust
struct CustomStruct {
    data: String,
}

impl Drop for CustomStruct {
    fn drop(&mut self) {
        println!("Droping CustomStruct with data {}", &self.data);
    }
}

#[test]
fn drop_trait() {
    let cs = CustomStruct {
        data: String::from("============this is data==========="),
    };

    // 如果需要手动提前释放
    drop(cs)
    // -- snip --
}
```
