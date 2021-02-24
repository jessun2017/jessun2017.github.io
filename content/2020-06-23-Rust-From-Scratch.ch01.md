+++
title = "Rust From Scratch chap.01 | Rust 中的数据类型与结构体"
draft = false

[taxonomies]
tags = ["Rust"]
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

