+++
title = "Rust From Scratch chap.02 | Rust 中常用集合类型"
[taxonomies]
tags = ["Rust"]
categories = ["Rust From Scratch"]
+++

Rust 标准库中包含一系列被称为集合（collections）的非常有用的数据结构。

# 1. 动态数组（Vector）

动态数组Vec<T>允许你在单个数据结构中存储多个相同类型的值。
动态数组Vec<T>非常适合在存储一系列相同类型值的场景中使用。

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

# 2. 字符串（String）

字符串是基于字节的集合。Rust 的标准库中同时包含了其他一系列的字符串类型，比如 OsString、OsStr、CString 和 CStr。
以 String 和 Str 结尾来表示类型提供的是所有者版本还是借用者版本。

创建新的空字符串

```rust
fn main() {
    let mut s1 = String::new();

    let data = "initial contents";
    let s2 = data.to_string();

    let s3 = "initial contents".to_string();

    let s4 = String::from("initial contents");
}
```

更新字符串

```rust
fn main() {
    let mut s1 = String::new();

    s1.push_str("aaa");
}
```

使用+运算符和`format!`宏来拼接字符串。
String 实际上是一个基于Vec<u8>的封装类型。
使用`chars()`方法来遍历字符串。
使用`bytes()`方法来遍历字符串的字节。

# 3. 哈希映射（HashMap）

```rust
use std::collections::HashMap;

fn main() {
    let mut scores = HashMap::new();
    scores.insert(String::from("Blue"), 10);
    scores.insert(String::from("Yellow"), 50);
    println!("{:?}", scores);
}
```

使用`Vector`的`collect()`方法来构建 vector

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

