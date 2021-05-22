+++
title = "Rust From Scratch | Rust 中的所有权（Ownership）与生命周期（Lifetimes）"
draft = true

[taxonomies]
tags = ["rust"]
categories = ["Rust From Scratch"]
+++

## 所有权（Ownership）

### 介绍

所有权（Ownership）是 Rust 中一个非常重要并且区别于其他语言的概念，甚至影响了开发者的编码习惯。

首先需要了解内存管理的基本概念：栈与堆。详细的解释需要自行搜索下。栈与堆的主要区别有：

- 分配在栈上的数据需要已知且固定的大小，堆上没有这个要求。
- 访问堆内存的时候比访问栈内存多了指针跳转环节，因此效率要低。

对于堆上无用的数据处理，有三种方式：

- 采用垃圾回收机制，在程序运行时不断地寻找不再使用的内存。
- 手动分配和释放内存。
- 像 Rust 那样使用所有权机制来管理内存。

Rust 所有权（Ownership）机制的重点原则：

- Rust 中的每一个值都有一个对应的变量作为它的所有者。
- 在同一个时间内，值有且只有一个所有者。
- 当所有者离开自己的作用域时，它持有的值就会被释放掉。

示例代码：
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
像整型这种在编译时已知大小的类型，都会拥有`Copy`的`trait`。如果是一个拥有`Copy`的类型，不会发生所有权移动。

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

## 生命周期（Lifetimes）（TODO）

