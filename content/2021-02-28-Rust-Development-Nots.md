+++
title = "Rust 开发手记之 Makefile"
draft = false
[taxonomies]
tags = ["rust"]
categories = [""]
+++

# Makefile 的使用

在开发 rust 程序的过程中，经常需要使用`cargo run`或者`cargo test`来跑测试。
目前使用的`Makefile`文件内容：

```makefile
run:
	RUST_BACKTRACE=full cargo run -j 64

test:
	RUSTFLAGS="-Awarnings" RUST_BACKTRACE=full cargo test -j 32 -- --nocapture
```

`RUSTFLAGS="-Awarnings"`的作用是禁用`warnings`显示来减少一些可忽略的信息的干扰。
`RUST_BACKTRACE=full`的作用是当出现`panic`的错误时候，会打印完整的调用栈。
`-j N` 的作用

