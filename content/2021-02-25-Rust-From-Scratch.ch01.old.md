+++
title = "Rust From Scratch chap.01 | Rust 中的数据类型、结构体和常用集合类型"
draft = true

[taxonomies]
tags = ["rust"]
categories = ["Rust From Scratch"]
+++

---

# 1 基本数据类型 - 标量类型(Scalar Type)

## 1.1 整型（Integer Type）

Rust 中的整数类型:

|  长度  | 有符号 | 无符号 |
| :----: | :----: | :----: |
| 8-bit  | i8     | u8     |
| 16-bit | i16    | u16    |
| 32-bit | i32    | u32    |
| 63-bit | i64    | u64    |
| arch   | isize  | usize  |

isize 和 usize 的长度取决于程序运行的目标平台。在64位架构上，是64位，在32位架构上，就是32位。

Rust 中的整数字面量:

|  整数字面量   |    示例     | 
| :-----------: | :---------: | 
| Decimal       | 98_222      | 
| Hex           | 0xff        | 
| Octal         | 0o77        | 
| Binary        | 0b1111_0000 | 
| Byte(u8 only) | b'A'        | 

> 关于整型的细节：[https://docnrust-lang.org/book/ch03-02-data-types.html#integer-types](https://doc.rust-lang.org/book/ch03-02-data-types.html#integer-types)

## 1.2 浮点型（Floating-Point Type）

f32 与 f64 的运行效率相差无几，但 f64 拥有更高的精度。Rust 默认使用 f64。

## 1.3 布尔类型（Boolean Type）

```rust
fn bool_example() {
    let t = true;
    println!("t is {}", t);

    let f: bool = false;
    println!("f is {}", f);
}
```

## 1.4 字符（Character Type）

Rust 中 `char` 类型占4字节，是一个 Unicode 标量值。

```rust
fn char_example() {
    let c = 'z';
    let z = 'ℤ';
    let heart_eyed_cat = '😻';

    println!("c is {}", c);
    println!("z is {}", z);
    println!("heart_eyed_cat is {}", heart_eyed_cat);
}
```

---

# 2 复合数据类型 - 复合类型(Compound Type)

## 2.1 元组（Tuple Type）

Rust 中`tuple`的最大的长度是12，原因见：[https://doc.rust-lang.org/book/ch03-02-data-types.html#the-tuple-type](https://doc.rust-lang.org/book/ch03-02-data-types.html#the-tuple-type)

```rust
fn tuple_example() {
    // A tuple with a bunch of different types
    let long_tuple = (
        1u8, 2u16, 3u32, 4u64, -1i8, -2i16, -3i32, -4i64, 0.1f32, 0.2f64, 'a', true,
    );
    // Values can be extracted from the tuple using tuple indexing
    println!("long tuple first value: {}", long_tuple.0);
    println!("long tuple second value: {}", long_tuple.1);

    // Tuples can be tuple members
    let tuple_of_tuples = ((1u8, 2u16, 2u32), (4u64, -1i8), -2i16);
    // Tuples are printable
    println!("tuple of tuples: {:?}", tuple_of_tuples);

    // But long Tuples cannot be printed
    // let too_long_tuple = (1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13);
    // println!("too long tuple: {:?}", too_long_tuple);
    // Reason: https://stackoverflow.com/questions/51846320/why-is-tuple-formatting-limited-to-12-items-in-rust
}
```

## 2.2 数组（Array Type）

```rust
fn array_example() {
    // Fixed-size array (type signature is superfluous)
    let xs: [i32; 5] = [1, 2, 3, 4, 5];
    // Indexing starts at 0
    println!("first element of the array: {}", xs[0]); // 1
    println!("second element of the array: {}", xs[1]); // 2
    // `len` returns the count of elements in the array
    println!("number of elements in array: {}", xs.len()); // 5

    analyze_slice(&xs[1..4]); // [2, 3, 4]

    let ys[i32:500] = [0;500];
}

fn analyze_slice(slice: &[i32]) {
    println!("first element of the slice: {}", slice[0]);
    println!("the slice has {} elements", slice.len());
}
```

---

# 3 结构体类型（Struct）

```rust
#[derive(Debug)]
struct User {
    username: String,
    email: String,
    gender: Gender,
    birthday: time::Instant,
}

#[derive(Debug)]
enum Gender {
    Male,
    Female,
    Unknown,
}

#[test]
fn struct_example() {
    let user = User {
        username: String::from("Jessun"),
        email: String::from("jessun1990@gmail.com"),
        gender: Gender::Male,
        birthday: time::Instant::now(),
    };

    println!("user is {:?}", user);
}
```

---

# 4 枚举类型（Enum）

```rust
fn enum_example() {
    let home = IpAddr {
        kind: IpAddrKind::V4,
        address: String::from("127.0.0.1"),
    };
    println!("home is {:?}", home);

    let loopback = IpAddr {
        kind: IpAddrKind::V6,
        address: String::from("::1"),
    };

    println!("loopback is {:?}", loopback);
}

#[derive(Debug)]
enum IpAddrKind {
    V4,
    V6,
}

#[derive(Debug)]
struct IpAddr {
    kind: IpAddrKind,
    address: String,
}
```

最常使用的枚举值定义，是在标准库中定义的，其中`T`表示泛型。可以结合控制流运算符`match`。

```rust
enum Option<T> {
    Some(T),
    None,
}
```

---

# 5 常用结合类型

Rust 标准库中包含一系列被称为集合（collections）的非常有用的数据结构。

## 5.1 动态数组（Vector）

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

动态数组（Vector）和枚举（Enum）结合来存储多种类型

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

## 5.2 字符串（String）

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

## 5.3 哈希映射（HashMap）

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

