---
title: 七周七语言2-Lua
date: 2018-04-20 21:37:57
tags: [Lua]
category: study
---

# Lua

## Lua一览

Lua 是一门基于 table 的编程语言，它的核心抽象层简单而强大，你可以用它实现过程式、面向对象、事件驱动等。
Lua 的 table 很适合基于原型的面向对象编程。这种编程风格中，类和实例不是割裂的概念。你先创建一个实例，这个实例看起来像
你需要的对象。然后你再把这个实例复制多份，对每一份做一些定制化。

## 初窥

Lua 的语法友好，并且易于接近，无须担心分号或者空格写哪里。空格对 Lua 来说不太重要，两个语句之间甚至不需要换行。Lua 使用 -- 来注释。

以下示例主要在REPL中运行。

```lua
> print "hello world"
hello world
> print
"hello world"
> print "hello world" print "hello world"
```

## Lua 基础

Lua 是动态类型的，也就是说代码中的变量不需要声明类型，在运行时才会决定其具体类型。Lua 的基础类型有：
- 数字
- 布尔值
- 字符串

```lua
> 3.14
3.14
> true
true
> "hello world"
hello world
>
```

Lua 没有整数，就跟 JS 一样。字符串也可以使用单引号，也使用反斜杠转义特殊字符或不可见字符。

字符串可以用 `..` 来拼接，用 `#` 来获取长度。

```lua
> "hello\t" .. "world"
hello   world

> # "hello"
5
```

在 Lua 中，nil 有自己的类型，它表示找不到或者不存在。

```lua
> some_func
nil
```

## 表达式

Lua 里的数学表达式和其他语言差不多。

```lua
> 6 + 5 * 4 - 3 / 2
24.5
> 6 + (5 * 4) - (3 / 2)
24.5
> (6 + 5) * (4 - 3) / 2
5.5
```

Lua 的布尔操作是用 `and` `or` `not` 三个关键字。Lua 的逻辑表达式也是会短路的。Lua 里布尔操作跟JS里的类似。
`==` `~=` 用来比较任意值相等或者不相等。`<` `>` `<=` `>=` 只对字符串和数字适用。 
```lua
> true or 1
true
> true and 1
1
> not 1
false
> not 0
false
> not false
true

> "hello" == "world"
false
> "hello" ~= "world"
true
> "hello" <= "world"
true
> "hello" > "world"
false
```

## 函数

Lua 的函数定义看起来和其他常见脚本语言类似，如JS：

```lua
function triple(num)
    return 3 * num
end

-- 函数名字不是必须的
(function(num) return 3 * num end)(2)
```

在 Lua 的世界里，函数是一等公民。可以跟其他值一样用于赋值和传递参数。

### 灵活的参数

调用函数的时候，传入的参数个数不一致时，未传入的参数赋值为nil，多余的参数则被忽略掉。

```lua
> function print_three(a, b, c)
>>     print(a)
>>     print(b)
>>     print(c)
>> end

> print_three(1, 2)
1
2
nil

> print_three(1, 2, 3, 4)
1
2
3
```

可以把最后一个参数标为 `...` 来表示不定参数

```lua
> function print_char(a, ...)
>>     print(a)
>>     b = {...}
>>     print(b[1])
>>     print(b[2])
>> end

> print_char("hello")
hello
nil
nil

> print_char("hello", "world")
hello
w
nil

> print_char("hello", "world", "你好")
hello
world
你好
```

Lua 的函数会做尾递归优化。和参数类似，函数的返回值可以有多个，你可以选择使用所有返回值，或者忽略一些。

```lua
> function return_two()
>>     return "hello", "world"
>> end
>
> a = return_two()
>
> x, y = return_two()
> a
hello
> x
hello
> y
world

> a,b,c = return_two()
> a
hello
> b
world
> c
nil
```

## 具名参数

Lua 不像 python 和 ruby 在语法层面上支持具名函数。不过你可以通过把 table 当作参数来获得类似的效果：

```lua
> function print_prices(table)
>>     print("The clothes costs " .. table.medium)
>> end
>
> print_prices{small=5.0, medium=7.0}
The clothes costs 7.0
```

## 控制流程

Lua 内建的流程控制有 if 语句，两种 for 循环和 while 循环。

```lua
> score = 7
>
> if score == 10 then
>>     print "good"
>> elseif score >= 6 then
>>     print "not bad"
>> else
>>     print "terrible"
>> end
not bad

> for i = 1, 3 do
>>     print(i)
>> end
1
2
3
>
> for i = 1, 5, 2 do
>>     print(i)
>> end
1
3
5
```

还可以用 for 循环来遍历集合，之后会讲。
最后一种是 while 循环

```lua
> while math.random(100) < 50 do
>>     print("again")
>> end
again
```

## 变量

Lua 的变量默认是全局的，需要使用 local 关键字来给局部变量声明。

```lua
> function doubleMe(a)
>>     a2 = a * 2
>>     return a2
>> end
>
> doubleMe(2)
4
> a2
4

> function doubleMe(a)
>>     local a2 = a * 2
>>     return a2
>> end
```
