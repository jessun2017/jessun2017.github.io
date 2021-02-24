+++
title = "Rust From Scratch chap.05 | Rust 中的所有权规则与生命周期"
draft = true
[taxonomies]
tags = ["Rust"]
categories = ["Rust From Scratch"]
+++

管理计算机内存的方式:

1. 采用垃圾回收机制，在程序运行时不断地寻找不再使用的内存。
2. 手动分配和释放内存。
3. Rust 使用所有权机制来管理内存。

Rust 所有权规则

1. Rust 中的每一个值都有一个被称为其 所有者（owner）的变量。
2. 值在任一时刻有且只有一个所有者。
3. 当所有者（变量）离开作用域，这个值将被丢弃。

```rust
{                      // s 在这里无效, 它尚未声明
    let s = "hello";   // 从此处起，s 是有效的

    // 使用 s
}   // 此作用域已结束，s 不再有效
```

String 类型
String 类型是在堆上进行内存分配

```rust
let s1 = String::from("hello");
let s2 = s1;

println!("{}, world!", s1); // s1 所有权已经移动
```

关于浅拷贝（shallow copy）和深拷贝（deep copy），Rust 永远也不会自动创建数据的 “深拷贝”。

如果需要深拷贝（deep copy），使用`clone`方法。
```rust
let s1 = String::from("hello");
let s2 = s1.clone();

println!("s1 = {}, s2 = {}", s1, s2);
```

像整型这种在编译时已知大小的类型，会被整个存储在栈上，没有深拷贝和浅拷贝的区别。
像整型这种在编译时已知大小的类型，都会拥有`Copy`的`trait`。如果是一个拥有`Copy` `trait`的类型，不会发生所有权移动。

拥有`Copy` `trait`的类型：
1. 所有整数类型。
2. 所有浮点类型。
3. 布尔类型。
4. 字符类型，`char`类型。
5. 元组，当且仅当其包含的类型也都是`Copy`的时候。

引用，允许使用值但不用获取所有权
```rust
fn main() {
    let s1 = String::from("hello");

    let len = calculate_length(&s1);

    println!("The length of '{}' is {}.", s1, len);
}

fn calculate_length(s: &String) -> usize {
    s.len()
}
```
> 注意：与使用 & 引用相反的操作是 解引用（dereferencing），它使用解引用运算符，*。

可变引用
```rust
fn main() {
    let mut s = String::from("hello");

    change(&mut s);
}

fn change(some_string: &mut String) {
    some_string.push_str(", world");
}
```

引用的规则
1. 在任意给定时间，要么 只能有一个可变引用，要么 只能有多个不可变引用。
2. 引用必须总是有效的。

`Slice` 类型
另一个没有所有权的数据类型是 slice。slice 允许你引用集合中一段连续的元素序列，而不用引用整个集合。
```rust
let s = String::from("hello world");

let hello = &s[0..5];
let world = &s[6..11];
```

```rust
fn first_word(s: &String) -> &str {
 // ...
}

// 可以支持 String 和 &str
fn first_word(s: &str) -> &str {
 // ...
}
```

其他类型的 slice
```rust
let a = [1, 2, 3, 4, 5];
let slice = &a[1..3];
```

