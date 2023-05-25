---
layout: '../../layouts/MarkdownPost.astro'
title: 'Lox interpreter'
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

program        → declaration* EOF ;

declaration    → varDecl  (就是 "var" IDENTIFIER ( "=" expression )? ";")
               | statement ;

statement      → exprStmt
               | forStmt
               | ifStmt
               | printStmt
               | whileStmt
               | block ;

whileStmt      → "while" "(" expression ")" statement ;

forStmt        → "for" "(" ( varDecl | exprStmt | ";" )
                 expression? ";"
                 expression? ")" statement ;

ifStmt         → "if" "(" expression ")" statement
               ( "else" statement )? ;

block          → "{" declaration* "}" ;

exprStmt       → expression ";" ;
printStmt      → "print" expression ";" ;

expression     → assignment ;
assignment     → IDENTIFIER "=" assignment
               | logic_or ;
logic_or       → logic_and ( "or" logic_and )* ;
logic_and      → equality ( "and" equality )* ;

equality       → comparison ( ( "!=" | "==" ) comparison )* ;

comparison     → term ( ( ">" | ">=" | "<" | "<=" ) term )* ;

term           → factor ( ( "-" | "+" ) factor )* ;

factor         → unary ( ( "/" | "*" ) unary )* ;

unary          → ( "!" | "-" ) unary | call ;
call           → primary ( "(" arguments? ")" )* ;

arguments      → expression ( "," expression )* ;`

primary        → NUMBER | STRING | "true" | "false" | "nil"
               | "(" expression ")" ;

## Visitor Pattern

```java
abstract class Pastry {}
class Beignet extends Pastry {}
class Cruller extends Pastry {}

// Each operation that can be performed on pastries is a new class that implements the interface
// xxxVisitor means operations to xxx(base). visitXXX means real operation. param is XXX(derived)
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


声明一个变量的规则如下：
varDecl        → "var" IDENTIFIER ( "=" expression )? ";" ;

注意： primary 里要加上一个 identifier

## assignment
expression     → assignment ;
assignment     → IDENTIFIER "=" assignment
               | equality ;

需要修改 error API:
在 parse 出现错误的时候，我们需要报告错误，但
1. 不 abort 整个程序，
2. 同时不再继续进行 interpreter
解决办法： 在 parser 中新增 had_error bool 变量

## 8.5.2 Block

statement      → exprStmt
               | printStmt
               | block ;

block          → "{" declaration* "}" ;

# question
为什么多加一层间接性就不会循环引用了
