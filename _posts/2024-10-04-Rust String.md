---
title: Rust String
date: 2024-10-04 17:06:50 +0200
categories: [Rust, Programming Language]
tags: [rust]     # TAG names should always be lowercase
---

新学期计划选 Formal Verification 试试看，课程用 Rust，所以提前学习一下，之后就被其神奇的特性吸引，记录一下 Rust String。

## Rust String

由于有所有权和借用机制，所以对于我这种外来的开发者，似乎存在了4种字符串的定义方式，为了理解这些东西，首先从最基础的`let s1 = String::from("hello");` 开始讨论。

### String，栈与堆

当我们使用`let s1 = String::from("hello");`来定义一个字符串的时候，实际上这里在内部字符串定义的结构为：

- **栈上：**`s1`是一个`String`的结构，其包含了三个部分的元数据：
    - **指针 ptr：**指向堆上世纪分配的字符串数据。
    - **长度 len：**表示当前字符串的支付数量，这里是5。
    - **容量 capacity：**表示分配的堆内存中大小，有可能稍大于字符串的长度。
- **堆上**，字符串 `"hello"` 的实际数据存储在堆上，所需的内存根据字符串长度动态分配。

那么通过`let s1 = String::from("hello");`就是定义了一个不可变的`String`，所以下面的程序是错误的:

```rust
fn main() {
    let s1 = String::from("hello");
    let mut s2 = &s1;
    println!("{}", s2);
    s2.push_str(", world");
    println!("{}", s2);
}
```

会报错：

```rust
s2.push_str(", world");
^^ `s2` is a `&` reference, so the data it refers to cannot be borrowed as mutable
```

这里创建了一个`String`后，所有权归属`s1`，之后尝试`s2`借用`s1`，这里是一个不可变借用，所以实际上`mut`这里是没有意义的，因为`s1`是不变的。那么自然的后面就不能使用`s2.push_str(", world");`这种方法。那么自然而然的我们就想到了另一种定义方法：`let mut s1 = String::from("hello");` 这种情况下，结合可变借用，就可以实现我们想要的效果。

注意这里使用的是借用，而不是移动将一个变量的所有权转移到另一个变量，所以我们可以这也写:

```rust
fn main() {
    let s1 = String::from("hello");
    let mut s2 = s1;
    s2.push_str(" world");
    println!("{}", s2);
}
```

在这种情况下，`s1` 的所有权被移动到 `s2`。在 Rust 中，`String` 类型实现了 `Drop` 特征，这意味着一旦一个变量拥有一个 `String` 的所有权，该变量就会负责在超出其作用域时释放该内存。由于 `s1` 的所有权被转移，`s1` 之后不再有效，因此不能再使用 `s1`。同时这里我们声明 `s2` 时使用了 `mut`，这意味着 `s2` 是可变的，我们可以对其内容进行修改。mut修饰的其实是这个变量（`s1, s2, ...`）这些变量的可变性，包括变量的指针或引用。

### &str，字符串字面值

`let s1 = String::from("hello");`这似乎有些奇怪的写法，如果我们是一个Python开发者，我们常见的写法是`let s1="hello"`来定义一个字符串，这时候如果使用IDE，会提示我们这是一个`&str`类型的变量。接下来，我们可能很自然的想写`s1.pust_str(" world");`，之后运行，我们会发现报错：

```rust
s1.pust_str(" world");
^^^^^^^^ method not found in `&str`
```

其实变量`s1`绑定到了一个字符串字面值，这个字符串值是硬编码进程序代码中的。但是我们还不死心，接下来我们可能很自然的认为那我们让`s1`可变就可以了，于是用`let mut s1 = "hello";`定义，但是依然报相同的错误。那么这个`mut`有任何意义吗？

实际上还是有意义的，如果我们不使用`mut`，编写下面的程序:

```rust
fn main() {
    let s1 = "hello";
    s1 = "world";
    println!("{}", s1);
}
```

我们尝试给`s1`赋另一个值，但是会报错：

```	rust
let s1 = "hello";
    -- first assignment to `s1`
s1 = "world";
^^^^^^^^^^^^ cannot assign twice to immutable variable
help: consider making this binding mutable
let mut s1 = "hello";
    +++
```

所以，`mut`还是有意义的，在了解了这些之后，我们突然发现可以写一写很神奇的程序：

```rust
fn main() {
    let s1 = String::from("hello");
    println!("{}", s1);
    let s2 = s1.as_str();
    let mut s3 = s2.to_string();
    println!("{}", s2);
    s3.push_str(", world!");
    println!("{}", s3);
    let mut s4 = s1.to_string();
    s4.push('!');
    println!("{}", s4);
}
```

这是一个合法的程序，最后`s1`依旧是可以用的，说明`s1`的所有权没有被转移，我预期内部应该是分配了新的存储空间。
