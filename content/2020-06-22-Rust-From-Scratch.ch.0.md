+++
title = "Rust From Scratch Ch.0 | Rust 介绍与安装"


[taxonomies]
tags = ["Rust"]
categories = ["Rust From Scratch"]
+++

# 1. Rust 简介

## 1.1 起源

> Rust是由 Mozilla 主导开发的通用、编译型编程语言。设计准则为“安全、并发、实用”，支持函数式、并发式、过程式以及面向对象的编程风格。
> Rust语言原本是 Mozilla 员工 Graydon Hoare 的私人计划，而 Mozilla 于2009年开始赞助这个计划 ，并且在2010年首次揭露了它的存在。
> 也在同一年，其编译器源代码开始由原本的OCaml语言转移到用Rust语言，进行bootstrapping工作，称做rustc，并于2011年实际完成。
> 这个可自我编译的编译器在架构上采用了LLVM做为它的后端。

---

# 2. Rust 环境初始化

## 2.1 使用 rustup 管理不同版本的 Rust

rustup 是 Rust 官方的版本管理工具。应当作为安装 Rust 的首选。安装 Rust 使用来自官方的 `rustup` 工具，[官方推荐](https://www.rust-lang.org/tools/install)。
`rustup` 命令文档请访问 [https://rust-lang.github.io/rustup/](https://rust-lang.github.io/rustup/)。中文版请访问 [https://github.com/chinanf-boy/rustup.rs-zh](https://github.com/chinanf-boy/rustup.rs-zh)
`rustc` 是 Rust 的编译器，`cargo` 是 Rust 的包管理工具。

```bash
# MacOS
$ brew install rustup-init

# Arch Linux
$ sudo pacman -S yay
$ yay -S rustup

# (optional)
$ rustup default nightly
$ rustup toolchain install nightly
$ rustup component add rust-src 
```
```bash
# 更新
$ rustup update

# 卸载
$ rustup self uninstall
```

为 Bash/Fish/Zsh 添加 tab 补全

```bash
# Bash
$ rustup completions bash > ~/.local/share/bash-completion/completions/rustup

# Bash (macOS/Homebrew)
$ rustup completions bash > $(brew --prefix)/etc/bash_completion.d/rustup.bash-completion

# Fish
$ mkdir -p ~/.config/fish/completions
$ rustup completions fish > ~/.config/fish/completions/rustup.fish

# Zsh
$ rustup completions zsh > ~/.zfunc/_rustup

# PowerShell v5.0+
$ rustup completions powershell >> $PROFILE.CurrentUserCurrentHost
# or
$ rustup completions powershell | Out-String | Invoke-Expression
```

> 如果需要更换为国内的安装源，请访问清华大学的[镜像源](https://mirrors.tuna.tsinghua.edu.cn/help/rustup/)和中科大的[镜像源](https://mirrors.ustc.edu.cn/help/rust-static.html)。

Rust 由工具 rustup 安装和管理。Rust 有着以 6 星期为周期的 快速版本迭代机制，支持 大量平台，因而不同时期存在大量不同的 Rust 构建版本。
rustup 用于管理不同平台下的 Rust 构建版本并使其互相兼容， 支持安装由 Beta 和 Nightly 频道发布的版本，并支持其他用于交叉编译的编译版本。

---

## 2.2 使用 Cargo 来管理你的项目

rustup 是 rust 的版本管理器。Cargo 是 Rust 的构建系统和包管理器，比如构建代码、下载依赖库并编译这些库。
一般安装 Rust 完成后，Cargo 已经安装完成。关于 Cargo 的详细介绍：[Cargo-Book](http://llever.com/cargo-book-zh/)

```bash
# 创建新项目目录
$ cargo new new_project // --bin or --lib
# or
$ cargo init new_project
# cargo 命令会自动初始化一个 git 仓库
```

`new_project` 的结构如下:

```bash
.
├── Cargo.toml
└── src
    └── main.rs
```

其中 `Cargo.toml` 的内容基本如下：

```toml
[package]
name = "new_project"
version = "0.1.0"
authors = ["username <username@xxx.com>"]
edition = "2018"

# See more keys and their definitions at https://doc.rust-lang.org/cargo/reference/manifest.html

[dependencies]
```

最后一行，`[dependencies]` 是罗列项目依赖的片段的开始。在 Rust 中，代码包被称为`crates`。Cargo 期望源文件存放在 `src` 目录中。

- 构建项目使用命令：`cargo build`

- 编译并运行使用命令：`cargo run`

- 检查代码但不编译使用命令：`cargo check`

- 发布构建：`cargo build --release`

- 运行测试：`cargo test`

# 3. 开发工具

- [neovim](https://github.com/neovim/neovim)

- 插件 [coc-rust-analyzer](https://github.com/fannheyward/coc-rust-analyzer) 和 [rust-analyzer](https://github.com/rust-analyzer/rust-analyzer)

- 需要安装 `rustup component add rust-src`，并配置变量 `$RUST_SRC_PATH` 为 `/home/jessun/.rustup/toolchains/nightly-x86_64-unknown-linux-gnu/lib/rustlib/src/rust/library/`

- 扩展 cargo 命令。

  - 安装 `cargo-edit` 来实现 `cargo add` 来编辑项目目录下的 `cargo.toml` 文件中的依赖项，仓库链接：https://github.com/killercup/cargo-edit 。

