---
title: 七周七语言2-Lua
date: 2018-04-20 21:37:57
tags: [Lua, Book]
category: [CS]
---

# Lua

## Lua一览

Lua 是一门基于 table 的编程语言，它的核心抽象层简单而强大，你可以用它实现过程式、面向对象、事件驱动等。
Lua 的 table 很适合基于原型的面向对象编程。这种编程风格中，类和实例不是割裂的概念。你先创建一个实例，这个实例看起来像你需要的对象。然后你再把这个实例复制多份，对每一份做一些定制化。

<!-- more -->
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

Lua 的函数定义看起来和其他常见脚本语言类似，如JS.

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

可以把最后一个参数标为 `...` 来表示不定参数。

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

Lua 不像 python 和 ruby 在语法层面上支持具名函数。不过你可以通过把 table 当作参数来获得类似的效果。

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

还可以用 for 循环来遍历集合，之后会讲。最后一种是 while 循环。

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

## table 作为字典

和 JS 的 Object， Ruby 的哈希类似，Lua 的 table 是键值对的集合。通过大括号创建， 这种表达式叫做 table 构造器。（最后一个逗号可以省略）

```lua
-- create
> book = {
>>     title = "My story",
>>     auther = "yjh",
>>     pages = 100
>> }
>
> book
table: 0276e1e0
-- get
> book["title"]
My story
-- add
> book.stars = 5
> book[0] = 1
> book[0]
1
-- delete
> book.stars = nil
> book.stars
nil
```

你可以使用任何类型的数据作为键：布尔，函数，table。

Lua 并没有自带打印 table 的函数，需要自定义。

```lua
> function print_table(t)
>>     for k, v in pairs(t) do
>>         print(k .. ": " .. v)
>>     end
>> end
>
> print_table(book)
auther: yjh
title: My story
pages: 100
0: 1
```

pairs() 是内建函数，它是一个迭代器，可以与循环工作。

## 穿着数组外衣的字典

对于 Lua 来说，数组只是键值对存储结构的一个特例，键是连续的数字。


```lua
medals = {
    "gold",
    "silver",
    "bronze"
}

> medals[1]
gold
> medals[4] = "lead"
```

Lua 运行时给数组提供了特殊的快车道，只要你连续用数字作为键向字典添加数据，Lua 就能高效地存储和访问数据。

## metatables

目前为止的table，如果你给定键，Lua给你找到一个值。这个逻辑是 Lua 内建的。
有时候这种默认行为不是程序需要的。你可能需要返回一个默认值而不是nil；或者想把读写的历史记录到某个 table。这些可以通过 metatables 实现。

如果你熟悉 JS 的原型或者 Python 的双下划线方法名，你会发现 Lua 的方式也很熟悉。
Lua 的每个 table 都有一个 metatable，其中可以包含读写键值对的函数，可以包含用来遍历 table 内容的代码，也可以重载某些操作符。

```lua
> greek_numbers = {
>>     ena = "one",
>>     dyo = "two",
>>     tria = "three"
>> }
>
> getmetatable(greek_numbers)
nil
```

```lua
function table_to_string(t)
    local result = {}
    for k, v in pairs(t) do
        result[#result + 1] = k .. ": " .. v
    end
    return table.concat(result, "\n")
end
```

```lua
> mt = {
>>     __tostring = table_to_string
>> }
>
> setmetatable(greek_numbers, mt)
dyo: two
tria: three
ena: one
> greek_numbers
dyo: two
tria: three
ena: one
```
这样我们实现了自定义table输出为字符串的行为。

### 读和写

Lua 的 table 非常宽容，读取不存在的键只会得到一个 nil。如果需要更加严格的 table，读取不存在的键或者覆盖已经存在的键都会导致运行时错误。
要做到这件事，只需要：
- 把你想要自定义的读写逻辑写到两个函数里。
- 把这两个函数存储到一个 table 中， 分别为 `__index` 和 `__newindex`。
- 把上一步创建的 table 设置为你的数据 metatable。

下面依次进行这三步。

```lua
local _private = {}

function strict_read(table, key)
    if _private[key] then
        return _private[key]
    else
        error("Invalid key: " .. key)
    end
end

function strict_write(table, key, value)
    if _private[key] then
        error("Duplicate key: " .. key)
    else
        _private[key] = value
    end
end

local mt = {
    __index = strict_read,
    __newindex = strict_write
}

treasure = {}

setmetatable(treasure, mt)
```

现在你可以使用这个新自定义的字典了。

```lua
> treasure.gold = 50
> treasure.gold
50
> treasure.silver
nil
> treasure.gold = 100
> treasure.gold
100
```

除此之外，lua 还有其他特殊的键名，这种语法跟python非常类似。

## 自制面向对象系统

Lua 有自己的面向对象语法。不过得益于 lua 强大的抽象，可以容易地定义另一种面向对象风格，然后可以观察它们的区别。
面向对象的核心观念就是对象之间互相发送消息。下面是一个玩家打 Boss 的代码的例子。

```lua
dietrich = {
    name = "Dietrich",
    health = 100,

    take_hit = function (self)
        self.health = self.health - 10
    end
}

> dietrich.take_hit(dietrich)
> dietrich.health
90
```
Boss 可能不止一个，如果 Boss 共用一份 take_init()，我需要知道让哪个 Boss 掉血，这就是传入 self 参数的目的。

### 原型

```lua
Villain = {
    health = 100,
    new = function(self, name)
        local obj = {
            name = name,
            health = self.health
        }
        
        setmetatable(obj, self)
        self.__index = self
        return obj    
    end,

    take_hit = function(self)
        self.health = self.health - 10
    end
}

> yjh = Villain.new(Villain, "yjh")
> yjh.health
100
> Villain.take_hit(yjh)
> yjh.health
90
```
setmetable 把 obj 委托给了 self，这个例子中是 Villain，也是说把 Villain 作为查找的后备（这里原型继承应该跟 JS 类似，是原型链）。

### 继承

如上所说，基于原型的面向对象系统的一个好处是不需要特殊的机制来实现继承，可以像之前那样定制，复制对象就可以了。
如果你需要创建一个 Final Boss，你只需要创建一个新的原型，然后复制它就好了。

```lua
-- 定制新的 Boss 类
SuperVillain = Villain.new(Villain)
function SuperVillain.take_hit(self)
    self.health = self.health - 5
end

-- 实例化
> boss = SuperVillain.new(SuperVillain, "Final Boss")
> boss.take_hit(boss)
> boss.health
95
```

### 语法糖

如果你写的是 table:method() 而不是 table.method(self), Lua 会隐式传入 self 参数。

```lua
-- same as before
Villain = {
    health = 100
}

function Villain:new(name)
    local obj = {
        name = name,
        health = self.health
    }
    
    setmetatable(obj, self)
    self.__index = self
    return obj    
end

function Villain:take_hit()
    self.health = self.health - 10  
end

> yjh = Villain:new("yjh")
> yjh:take_hit()
> yjh.health
90
```

## 协程

之前的 Lua 代码都是串行执行的。那么 Lua 如何处理多线程？ Lua 不处理多线程。
但是 Lua 内建了简单、容易理解的用于多任务处理的基本类型：`协程`。
与线程不同，协程不是抢占式的，你需要显式标注哪里暂停任务，让位给其他任务。它在概念上更简单，可以免去许多并行的问题。

下面定义一个死循环。

```lua
function fibonacii()
    local m = 1
    local n = 1
    while true do
        coroutine.yield(m)
        m, n = n , m + n
    end
end

> gen = coroutine.create(fibonacii)
> succeeded, value = coroutine.resume(gen)
> value
1
> succeeded, value = coroutine.resume(gen)
> value
1
> succeeded, value = coroutine.resume(gen)
> value
2
```
要运行协程需要先用 coroutine.create 函数创建一个协程，然后调用 couroutine.resume。调用 resume 后就执行该函数直到遇见下一个 yield 调用就跳回调用者处，并且返回一个`状态码`和 `yield 的参数`。
这种方式适合耗时长的计算或者网络操作，可以把它分解为较小的子任务来执行，维持程序的响应性。

## 多任务

协程虽然简单但功能强大，可以实现类似多线程的行为。操作系统的`进程调度器`通常需要几千行代码，不过我们可以使用几十行来实现一个。

先定义 scheduler.lua 。*注意：* 局部函数必须先定义后使用。

```lua
local function sort_by_time(array)
    table.sort(array, function(e1, e2) return e1.time < e2.time end)
end

local pending = {}

local function schedule(time, action)
    pending [#pending + 1] = {
        time = time,
        action = action
    }

    sort_by_time(pending)
end
```

想让某件事在未来发生的话，把它丢到 pending 数组里，该数组以时间戳排序（程序启动到现在的秒数）。

```lua
local function wait(seconds)
    coroutine.yield(seconds)
    -- seconds will be sent to coroutine.resume's return.
end
```

协程一个是轻量级的。在开始运行（run）以后后就做相应的处理，做完马上返回或者 yield。所以 wait 函数不应该真的等待，而是向调度器 yield。

```lua
local function remove_first(array)
    result = array[1]
    array [1] = array[#array]
    array[#array] = nil
    return result
end

local function run()
    -- when there is not task, the scheduler will quit.
    while #pending > 0 do
        while os.clock() < pending[1].time do 
        end -- busy-wait

        local item = remove_first(pending)
        local _, seconds = coroutine.resume(item.action)
        -- got seconds from coroutine.yield.
        if seconds then
            later = os.clock() + seconds
            -- item's next action time -- `now` plus `schedule time`            
            -- print('later:' .. later)
            schedule(later, item.action)
        end
    end
end
```
run 会在没有任务时等待（任务时间还未到达），在有任务时执行任务。如果执行的任务调度 wait, 任务执行所需要消耗的秒数就会被 yield 给我们。
我们会用这个秒数决定未来什么时候继续执行当前的任务（因为协程是非抢占的，所以调度以后，可能其他任务花费 wait 给定时间的数倍才让回来，这里需要程序员自己来设计好调度时机）。

当任务执行完毕后，协程不会 yield 任何东西。 这时候 resume 函数会返回 nil，我们也不会给该任务分配任何时间了。

最后我们把这个 API 封装成一个模块。下面代码仍然写到 scheduler.lua里。
```lua
return {
    schedule = schedule,
    run = run,
    wait = wait
}
```

现在可以执行一个新文件了。
```lua
-- main.lua
scheduler = require 'scheduler'

function punch()
    for i = 1, 5 do
        print('punch ' .. i)
        scheduler.wait(0.3)
    end
end

function block()
    for i = 1, 3 do
        print('block ' .. i)
        scheduler.wait(1.0)
    end
end

scheduler.schedule(0, coroutine.create(punch))
scheduler.schedule(0, coroutine.create(block))
scheduler.run()
```

我们使用 require 函数来加载模块：
- 检查模块是否已经加载过。
- 搜索多个程序库的加载路径（可以配置）。
- 给局部变量提供安全的命名空间。

```
$ ./lua53.exe main.lua
punch 1
block 1
punch 2
punch 3
punch 4
block 2
punch 5
block 3
```

## 总结

我们实现了一个面向对象系统以及一个类似线程的并行 API，并且这些代码写得可读、紧凑、模块化。
让这一切容易实现得益于 Lua 易于组合的数据结构：table 和 协程。table 可以作为数组和字典使用，lua 还给我们提供了切入点来扩展 table 的行为。
之后我们学习了协程，这是 Lua 并行的方式。尽管协程 API 不多，但是仍然可以构造复杂强大的多任务系统。