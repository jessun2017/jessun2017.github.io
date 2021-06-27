+++
title = "Rust From Scratch chap.02 | Rust 中的表达式（expressions）与语句（statements）"
draft = false

[taxonomies]
tags = ["rust"]
categories = ["Rust From Scratch"]
+++


# 1.Rust 中语句（statements）的四种类型

- 声明语句

- 流程控制语句

- 表达式语句

- 宏语句

## 三个关键元素

- 属性，类似于 #![...]

- 分号，行分隔符

- 花括号，块分隔符

## 求值规则

- 分号表达式返回值是永远为自身的单元（unit）类型。

- 分号表达式只有在块表达式最后一行才会进行求值，其他时候只作为『连接符』存在。

- 块表达式只对其最后一行表达式进行求值。


## Rust 编译期计算

### 什么是编译期计算

即编译期函数计算求值。

Rust 编译器计算支持：常量函数和常量泛型

Rust 支持两种方式：

- 过程宏 + Build脚本(build.rs)

- CTFE (Compile-Time function Execution)

    - 常量函数（const fn），可支持递归。
    - 常量泛型（const generic）

## Rust 常量泛型（const generics）

### 常量上下文（const context）包含：

1. 常量值初始化位置

2. 静态数组的长度表达式，[T; N]

3. 重复的长度表达式，类似于：[0; 10]

4. 静态变量、枚举判别式的初始化位置。

### 常量传播（const propagation）

常量传播和编译期计算是不同的：

- 常量传播是编译器的一种优化。

- 常量传播并不能改变程序的任何行为，并且对开发者是隐藏的。

- 编译期计算则是指编译时执行的代码，必须要知道其结果，才能继续编译。

```rust
const X: u32 = 3 + 4; // CTFE 
let x: uew = 4 + 3; // 不是 CTFE，但可能会被常量传播优化，因为它不是常量上下文。
```

### 常量安全（const safe）

- Rust 大部分表达式都可用作常量表达式。

- 并不是所有常量表达式都可以用在常量上下文。

- 编译期求值必须得到一个确定性的结果。

### 常量上下文可接受的常量表达式

- const fn 函数

- 元组结构体

- 元组的值

### 编译期计算如何实现

编译过程中 MIR （中级中间语言）阶段使用 MIRI（编译器内置MIR解释器）执行计算。

> 常量泛型会依赖泛型的具体实现。

# 2. Rust 中表达式（expressions）的分类

## 位置表达式

```rust
let a = 42; // a 就是位置表达式，42 就是值表达式，let 作用就是绑定。
```

包括：

- 静态变量初始化。比如：static mut LEVELS: u32 = 0;

- 解引用表达式。形如 *expr

- 数组索引表达式。形如 expr[expr]

- 字段表达式。形如 expr.field

- 加上括号的位置表达式。形如(expr)


### 位置上下文

1. 复合赋值操作的左侧

```rust
let mut a = 1;
a += 1;
```

2. 一元『借用』和『解引用』操作中的操作数所在区域

```rust
let a = &mut 7;
*a = 42;
let b = &a;
```

3. 字段表达式的操作数所在区域

```rust
struct A {
    name: &'static str,
}

let a = A{name: "Alex"};
a.name; 
```

4. 数组索引表达式的操作数所在区域

```rust
let mut a = [1, 2, 3];
let b = &mut a;
a[1] = 42;
```

5. 任意隐式借用操作数所在的区域

```rust
let mut v = vec![1, 2, 3];
v.push(4); // push 方法调用会隐式借用 &mut v，因为 push 方法的第一个参数就是 &mut self
```

6. let 语句初始化

```rust
let a: i32;
a = 42;
```

7. if let/ while let/ match 的匹配表达式所在的区域

```rust
let dish = ("Ham", "Eggs");
if let ("Bacon", b) = dish{
    println!("Bacon is served with {}", b);
} else {
    println!("No bacon will be served");
}

// 同理
// while let (位置上下文) = ... { ... }
// match (位置上下文) { ... }
```

8. 结构体更新语法中的 base 表达式（..操作符后面的操作数区域）

```rust
let mut base = Point3d{x: 1, y: 2, z: 3};
let y_ref = &mut base.y;
Point3d {y: 0, z: 10, ..base} // 得到 base.x
```

#### Rust 所有权语义在表达式上的体现

```rust
let stack_a = 42;
let stack_b = stack_a; // stack_a 出现在值上下文中，发生了 Copy
stack_a;

let heap_a = "hello".to_string();
let heap_b = heap_a; // heap_a 出现在值
heap_a;
```

#### Rust 借鉴了函数式语言的不可变特性

包括：

- 不可变绑定与可变绑定

- 继承式可变（inherited mutability）

```rust
// 变量遮蔽
let answer = 42;
let answer = 43;

// 使用 mut 修饰符
let mut answer = 42;
```

- 可变引用

```rust
let mut answer = 42;
let r = &mut answer;
*r = 43;
println!("{:?}", answer); // 43
```

