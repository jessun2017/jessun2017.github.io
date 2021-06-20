+++
title = "Rust From Scratch chap.01 | Rust 的版本和词法结构"
draft = false

[taxonomies]
tags = ["rust"]
categories = ["Rust From Scratch"]
+++

---


# Rust 中的版本（Version）

Rust 语言的版本包括以下三个相互正交的概念：

- 语义化版本（Sem Ver, Semantic Versioning）：主版本号.次版本号.修订号
    - 主版本号：当做了不兼容的API修改
    - 次版本号：当做了向下兼容的功能性新增
    - 修订号：当做了向下兼容的问题修正。

- 发行版本：nightly(master), beta, stable

- Edition 版次：2015 Edition, 2018 Edition, 2021 Edition

# Rust 的词法结构（Lexical）

## Rust 的编译过程

1. Rust 源码作为UTF8编码序列输入到编译器。

2. 进入到编译器之后经过分词，把词法结构处理为词条流（Tokens）。

3. 词条流（Tokens）经过解析成为抽象语法树（AST）。

4. Rust 在会将抽象语法树降级为高级中间语言（HIR），高级中间语言（HIR）被编译器用于做代码类型检查、方法查找。
版次差异会在这个阶段被消除。

5. 高级中间语言（HIR）会被进一步简化为中级中间语言（MIR），被用于借用检查、宏代码生成、单态化。

6. 中级中间语言（MIR）会被转译为LLVM中间语言（LLVM IR）。

7. LLVM 后端最后生成机器码。

## Rust 词法结构

1. 关键字（Keywords）

    - 严格关键字（Strict）：特定用途和含义

        ```
        as/async/await/break/const/continue/break/const/continue/crate/dyn/else/enum/extern/false/fn/for/
        if/impl/in/let/loop/match/mod/move/mut/pub/ref/return/Self/self/static/struct/super/trait/true/type/ 
        union/unsafe/use/where/while/
        ```

    - 保留字（Reserved）：将来可能会使用的关键字（也可能不会使用）

        ```
        abstract/becom/box/do/final/macro/override/priv/try/typeof/unsized/virtual/yield
        ```

    - 弱关键字（Weak）：特定场景用途

       - 2018 Edition: union, 'static

       - 2015 Edition: dyn


2. 标识符（Identifier）

和大多数数编程语言一样，变量名只能以字母、下划线等开头，不可以使用数字开头。

3. 注释（Commnet）

    - 模块级文档注释

        ```
        // ! - 模块级文档注释，置于模块头部
        //!! - 模块级文档注释，但是和上面的注释置于同一行
        //!  - 模块级文档注释，但会换行

        /*!  - 模块级文档注释 */
        /*!! - 模块级文档注释，但是和上面的注释置于同一行 */
        /*!  - 模块级文档注释，但会换行 */
        ```

    - 行级注释

        ```
        //   - 普通行注释
        ///  - 行级文档注释（必须是3个斜杠）
        //// - 普通行注释
        ```

    - 块级注释

        ```
        /*   - 普通块级注释 */
        /**  - 块级文档注释（精确）2个星号 */ 
        /*** - 普通注释 */
        ```

关于注释的其他：

- 注释可以相互嵌套和包含.

- `/***/`这不是空的块级文档注释，而只是一个普通的块级注释

- 文档注释下面必须要有语言项，比如方法、函数等。

4. 空白（Whitespace）

- Rust 中空白字符包括：\n、\t、tab 等。

- 任何形式的空白字符在 Rust 中只用于分隔标记，没有语义意义。

5. 词条（Tokens）

TODO

6. 路径（Path）

`std::collections::*` 表示使用模块的函数或模块等。

# 参考

- [1] [张汉东的Rust实战课](https://time.geekbang.org/course/detail/100060601-286522)

