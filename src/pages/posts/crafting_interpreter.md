---
layout: '../../layouts/MarkdownPost.astro'
title: 'Rust 学习'
pubDate: 2023-04-23
description: '使用 Rust 实现 lox 解释器'
author: 'Aaron'
cover:
    url: '/preview/rust.png'
    alt: 'cover'
tags: ["Rust"]
theme: 'light'
featured: false
---

# LOX
首先把字符转换成 token，
然后将 token 转换成 ast (解析表达式).
递归下降分析（recursive descent）：自顶向下解析器

expression     → equality ;

equality       → comparison ( ( "!=" | "==" ) comparison )* ;

comparison     → term ( ( ">" | ">=" | "<" | "<=" ) term )* ;

term           → factor ( ( "-" | "+" ) factor )* ;

factor         → unary ( ( "/" | "*" ) unary )* ;

unary          → ( "!" | "-" ) unary
               | primary ;

primary        → NUMBER | STRING | "true" | "false" | "nil"
               | "(" expression ")" ;

## Statement
program        → statement* EOF ;

statement      → exprStmt
               | printStmt ;

exprStmt       → expression ";" ;
printStmt      → "print" expression ";" ;


## Visitor Pattern

```java
abstract class Pastry {}
class Beignet extends Pastry {}
class Cruller extends Pastry {}

// Each operation that can be performed on pastries is a new class that implements the interface
// xxxVisitor means operations
interface PastryVisitor {
    void visitBeignet(Beignet beignet); 
    void visitCruller(Cruller cruller);
}

// Each subclass implements accept
abstract class Pastry {
    abstract void accept(PastryVisitor visitor);
}

class Beignet extends Pastry {
    @Override
    void accept(PastryVisitor visitor) {
      visitor.visitBeignet(this);
    }
}

// To perform an operation on a pastry, we call its accept() method and pass in the visitor for the operation we want to execute. 

// Visitor2 visitor;
// ElementB element;
// element.accept(visitor);

// now we call Visitor2 的 visitorElementB 方法。或者说 visitBeignet 方法 
```

