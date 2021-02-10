+++
title = "Rust From Scratch chap.04 | Rust 中常用集合类型"
[taxonomies]
tags = ["Rust"]
categories = ["Rust From Scratch"]
+++

Rust 标准库中包含一系列被称为 集合（collections）的非常有用的数据结构。

Vector
```rust
// 新建 vector
let v1: Vec<i32> = Vec::new();
let v2 = vec![1, 2, 3];

// 更新 vector
let mut v3 = Vec::new();
v.push(1);
v.push(2);
v.push(3);

// 读取 vector
let v = vec![1, 2, 3, 4, 5];

// 第一种方式
let third: &i32 = &v[2];
println!("The third element is {}", third);

// 第二种方式
match v.get(2) {
    Some(third) => println!("The third element is {}", third),
    None => println!("There is no third element."),
}

// 遍历 vector 中的元素
let v = vec![100, 32, 57];
for i in &v {
    println!("{}", i);
}

let mut v = vec![100, 32, 57];
for i in &mut v {
    *i += 50;
}
```

Vector 和枚举结合来存储多种类型
```rust
enum SpreadsheetCell {
    Int(i32),
    Float(f64),
    Text(String),
}

let row = vec![
    SpreadsheetCell::Int(3),
    SpreadsheetCell::Text(String::from("blue")),
    SpreadsheetCell::Float(10.12),
];
```

HashMap
```rust
use std::collections::HashMap;

fn main() {
    let mut scores = HashMap::new();
    scores.insert(String::from("Blue"), 10);
    scores.insert(String::from("Yellow"), 50);
    println!("{:?}", scores);
}
```

使用 vector 的 collect 方法来构建 vector
```rust
fn main() {
    let teams = vec![String::from("Blue2"), String::from("Yellow2")];
    let initial_scores = vec![102, 502];
    let scores: HashMap<_, _> = teams.iter().zip(initial_scores.iter()).collect();
    println!("{:?}", scores);
}
```

只在键没有对应值时插入，同时根据旧值更新一个值
```rust
fn main() {
    let text = "hello world wonderful world";

    let mut map = HashMap::new();

    for word in text.split_whitespace() {
        let count = map.entry(word).or_insert(0);
        *count += 1;
    }
}
```

