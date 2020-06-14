---

title: "Rust 学习笔记（一）| 安装"
date: 2020-06-15 05:15:32
tags: ["rust"]
categories: ["rust"]

---

## 安装 Rust

安装 Rust 使用来自官方的 `rustup` 工具

```shell
# MacOS 下
$ brew install rustup-init
# Arch Linux
$ sudo pacman -S yay
$ yay -S rustup
```

详细步骤请参考[官方指引](https://kaisery.github.io/trpl-zh-cn/ch01-01-installation.html)。

## 更新和卸载 Rust

```shell
# 更新
$ rustup update
# 卸载
$ rustup self uninstall
```

> 如果需要更换为国内的安装源，请访问[清华大学](https://mirrors.tuna.tsinghua.edu.cn/help/rustup/)和[中科大](https://mirrors.ustc.edu.cn/help/rust-static.html)的镜像源。

## 安装 Cargo

> Cargo 是 Rust 的构建系统和包管理器，比如构建代码、下载依赖库并编译这些库。

一般安装 Rust 完成后，Cargo 已经安装完成。

## 使用 Cargo 创建项目

```shell
# 创建新项目目录
$ cargo new new_project
# 或者
$ cargo init new_project
# cargo 命令会自动初始化一个 git 仓库
```

`new_project` 的结构如下:

```shell
.
├── Cargo.toml
├── src
│   └── main.rs
└── tree.txt
```

其中 `Cargo.toml` 的内容基本如下：

```shell
[package]
name = "rust-test-code"
version = "0.1.0"
authors = ["username <username@xxx.com>"]
edition = "2018"

# See more keys and their definitions at https://doc.rust-lang.org/cargo/reference/manifest.html

[dependencies]
```

> 最后一行，`[dependencies]`是罗列项目依赖的片段的开始。在 Rust 中，代码包被成为`crates`。Cargo 期望源文件存放在 `src`
目录中。

构建项目使用命令：`cargo build`

编译并运行使用命令：`cargo run`

检查代码单不编译使用命令：`cargo check`

发布构建：`cargo build --release`

## 关于版本

Rust 包含 stable、beta、nightly 三个版本，默认情况下使用的是 stable 稳定版。

如果需要修改:

```shell
# 切换至 nightly 版本
$ rustup override set nightly
# 默认设置
$ rustup default nightly
```

如果需要单独为某个工作目录设置特定的版本，在工作目录下创建一个名为`rust-toolchain`的文件，并在其中写入所需的工具链版本：

```shell
nightly-2020-06-09
```

今后所有在这个目录下使用 Rust 时会自动切换到这个版本的工具链。
