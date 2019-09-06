---
title: 实现Lua-AST
date: 2019-03-24 13:57:37
tags: [Rust, Lua, Book]
category: [CS]
---

# 实现Lua解释器-AST

## 调研

最近终于开始做毕设了，于是开始考虑是否用一款语法分析生成器来写Lua前端，遂发现[`Pest`](https://pest.rs/), 一款Rust的语法分析器，使用PEG文法。这个文法有点像BNF和正则的融合，但是定义无歧义，表达能力小于等于CFG。写起来比较舒服，考虑到Lua官方文法整理出来有许多左递归文法，改写很麻烦（我不太会），虽然Pest本身编写很友好，但是感觉文档描述有点少，有些关键的地方使用不清楚，加上没有IDE的代码提示，遂放弃（不过写一些小语言的简单文法以后可以考虑）。之前用过Nom这款解析组合子库，这个库性能非常好，但是我觉得语法设计的非常怪异，之前用也没搞清楚，对于之前编写的某个玩具虚拟机的字节码编译器使用起来也是小题大做，反而搞复杂了。

于是乎，还是打算手写。考虑到Lua本身文法少，手写代码量不会多上几百行；自己之前也没有实现一个比较满意的解析器；如果用语法生成生成器，首先还得学，然后得花时间改写文法，有点得不偿失。

<!-- more -->

## 词法

首先随便写了点词法这块，发现我抽象能力太弱了，本来想多用第三方库实现更抽象性能更好的`词法分析类`，但我有点不太熟悉泛型编程，而且前期具体工作不明确，写了很大概率后面要改。于是乎，就是很粗糙地使用了String类型。而对于具体的词法分析，感觉写过很多次，每次套路都写的不一样，参考别人的实现，也都是五花八门，我也没法判断哪种够优雅。而对于Lua语言，毕竟是工业级语言，词法分析实现细节一定要参考书或官方文档，语法细节非常多。

## 先从AST开始

果然了解一门语言除了常见的语法特性见识了以外，看文法和AST感觉也不错。不过我个人对文法不太感兴趣，也不太擅长，而AST可以看清楚这个语言的很多语义。
下面整理一下Lua的AST，可能设计的一般般，以后肯定还需要改动。下面我只列出AST，而不写出文法。

```rust
#[derive(Debug)]
pub struct Block {
    last_line: Line,
    stats: Vec<Stat>,
    ret_exps: Vec<Exp>,
}
```
`Block`，或 `Chunk`，是Lua最小编译单位。Lua解释器通过传递Block给编译器，生成字节码后解释执行。Block必须包含语句和返回值，当然返回值可以有多个，而且可以是表达式，这个很自然。


接下来的 `Stat`有点长，它表示Lua的语句(statment)。语句包括了表达式(expression)的结构，这跟许多静态语言不一样所以会比较长，但这也是它的主体了。
仔细一看，Stat也只有几个流程控制，赋值（包括变量定义），函数定义，无他。写过一点lua就知道，lua没有哪些地方的代码需要提升(lift)，都是在解释时进入到环境的。

```rust
#[derive(Debug)]
pub enum Stat {
    Empty,
    Break {
        line: Line,
    },
    Label {
        name: String,
    },
    Goto {
        name: String,
    },
    Do {
        block: Box<Block>,
    },
    While {
        exp: Exp,
        block: Box<Block>,
    },
    Repeat {
        exp: Exp,
        block: Box<Block>,
    },
    If {
        exps: Vec<Exp>,
        blocks: Vec<Block>,
    },
    ForNum {
        line_of_for: Line,
        line_of_do: Line,
        var_name: String,
        exps: (Exp, Exp, Exp),
        block: Box<Block>,
    },
    ForIn {
        line_of_do: Line,
        name_list: Vec<String>,
        exp_list: Vec<Exp>,
        block: Box<Block>,
    },
    LocalVarDecl {
        last_line: Line,
        name_list: Vec<String>,
        exp_list: Vec<Exp>,
    },
    Assign {
        last_line: Line,
        var_list: Vec<Exp>,
        exp_list: Vec<Exp>,
    },
    LocalFnDef {
        name: String,
        exp: Box<Exp>,
    },
}
```

写到这里，说实话好像讲AST没什么意思，于是下面这个也没什么好总结了。不过要注意好一些值的限定，虽然有些地方使用了`Exp`，但是其实里面用的枚举值是固定的，
其实可以提出来单独定义。我为了整体的简洁性，就没这样做。在后面的实现中，应该对性能和代码量有一定影响，不过到时候改也不迟。
这里有个不好的地方就是Line几乎都用到了，为什么不封装一下呢？主要是还是为了简单而多了样板代码，到后期写的时候不会因为类型过多而处理麻烦。

```rust
#[derive(Debug)]
pub enum Exp {
    Nil {
        line: Line,
    },
    True {
        line: Line,
    },
    False {
        line: Line,
    },
    Vararg {
        line: Line,
    },
    Integer {
        line: Line,
        val: i64,
    },
    Float {
        line: Line,
        val: f64,
    },
    String {
        line: Line,
        val: String,
    },
    Name {
        line: Line,
        val: String,
    },
    Unop {
        line: Line,
        op: usize,
        exp: Box<Self>,
    },
    Binop {
        line: Line,
        op: usize,
        exp1: Box<Self>,
        exp2: Box<Self>,
    },
    Concat {
        line: Line,
        exps: Vec<Self>,
    },

    TableConstructor {
        line: Line,
        last_line: Line,
        keyExps: Vec<Exp>,
        valExps: Vec<Exp>,
    },
    FnDef {
        line: Line,
        last_line: Line,
        par_list: Vec<String>,
        is_vararg: bool,
        block: Box<Block>,
    },
    Parens(Box<Exp>),
    TableAccess {
        last_line: Line, // line of ']'
        prefix_exp: Box<Exp>,
        key_exp: Box<Exp>,
    },
    FnCall {
        line: Line,      // line of '('
        last_line: Line, // line of ')'
        prefix_exp: Box<Exp>,
        name_exp: Box<Exp>,
        args: Vec<Exp>,
    },
}
```

最后发现AST和Lua的chunk二进制格式定义非常相近。大概原因是Lua字节码的抽象程度也非常高，这样代码生成难度也降低了很多。如果是把Lua编译到像WebAssembly这样的字节码中，可能得手写字节码先封装出类型Lua字节码层次的函数供代码生成，不然每行代码段的生成可能包括复杂的字节码。