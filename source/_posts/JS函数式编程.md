---
title: JS函数式编程笔记
date: 2017-09-19 20:47:31
tags: JS
category: study
---
# JS函数式编程

## 我们在做什么

> “we both know what happens when you assume”，源自一句名言“When you assume you make an ASS of U and ME”，意思是“让两人都难堪”）。但我猜想你在使用可变状态（mutable state）、无限制副作用（unrestricted side effects）和无原则设计（unprincipled design）的过程中已经遇到过一些麻烦。

现在已经有一些通用的编程原则了，各种缩写词带领我们在编程的黑暗隧道里前行：DRY（不要重复自己，don't repeat yourself），高内聚低耦合（loosecoupling high cohesion），YAGNI （你不会用到它的，ya ain't gonna need it），最小意外原则（Principle of least surprise），单一责任（single responsibility）等等。这些原则同样适用于函数式编程(FP)。

先看基本的例子

```js
var add = function (x, y) {
    return x + y
};
var multiply = function (x, y) {
    return x * y
};
var a = 4;
var b = 2;
var c = 0;
var result = add(multiply(b, add(a, c)), multiply(a, b));
//=>16
```

你会发现我们不过是在运用古人早已获得的知识

```js
// 结合律（assosiative）
add(add(x, y), z) == add(x, add(y, z));
// 交换律（commutative）
add(x, y) == add(y, x);
// 同一律（identity）
add(x, 0) == x;
// 分配律（distributive）
multiply(x, add(y, z)) == add(multiply(x, y), multiply(x, z));

// 原有代码
add(multiply(b, add(a, c)), multiply(a, b));
// 应用同一律，去掉多余的加法操作（add(a, c) == a）
add(multiply(b, a), multiply(a, b));
// 再应用分配律
multiply(b, add(a, a));
```

## 一等公民的函数

先看看常见的JS风格的一个例子

```js
function ajaxCall(func) {
    //...
}

// 用一个函数把另一个函数包起来，目的仅仅是延迟执行，真的是非常糟糕的编程习惯
var getServerStuff = function (callback) {
    return ajaxCall(function (json) {
        return callback(json);
    });
};

// 这行
// return ajaxCall(function (json) {
// return callback(json);
// });
// 等价于这行
// return ajaxCall(callback);

// 应该改成这样
var getServerStuff = ajaxCall;
```

再看另一个例子

```js
class Views {
    //...
}

class Db {
    //...
}

// 这样做除了徒增代码量，提高维护和检索代码的成本外，没有任何用处
var BlogController = (function () {
    var index = function (posts) {
        return Views.index(posts);
    };
    var show = function (post) {
        return Views.show(post);
    };
    var create = function (attrs) {
        return Db.create(attrs);
    };
    var update = function (post, attrs) {
        return Db.update(post, attrs);
    };
    var destroy = function (post) {
        return Db.destroy(post);
    };
    return {
        index,
        show,
        create,
        update,
        destroy
    };
})();

// 我们可以把它重写成这样：
var BlogController = {
    index: Views.index,
    show: Views.show,
    create: Db.create,
    update: Db.update,
    destroy: Db.destroy
};
// 或者直接全部删掉，因为它的作用仅仅就是把视图（Views）和数据库（Db）打包在一起而已
```

另外，如果一个函数被不必要地包裹起来了，而且发生了改动，那么包裹它的那个函数也要做相应的变更。

```js
httpGet('/post/2', function(json){
    return renderPost(json);
});
// 如果 httpGet 要改成可以抛出一个可能出现的 err 异常，那我们还要回过头去把“胶水”函数也改了

// 把整个应用里的所有 httpGet 调用都改成这样，可以传递 err 参数。
httpGet('/post/2', function(json, err){
    return renderPost(json, err);
});

// 写成一等公民函数的形式，要做的改动将会少得多
httpGet('/post/2', renderPost); 
// renderPost 将会在 httpGet 中调用，想要多少参数都行
```

除了删除不必要的函数，正确地为参数命名也必不可少。当然命名不是什么大问题，但还是有可能存在一些不当的命名，尤其随着代码量的增长以及需求的变更，这种可能性也会增加。

如果一个底层函数使用了 this，而且是以一等公民的方式被调用的，那你就等着 JS 这个蹩脚的抽象概念发怒吧

```js
const fs = require('fs');
// 太可怕了
fs.readFile('freaky_friday.txt', Db.save);
// 好一点点
fs.readFile('freaky_friday.txt', Db.save.bind(Db));
```

## 纯函数的好处

> 纯函数是这样一种函数，即相同的输入，永远会得到相同的输出，而且没有任何可观察的副作用。

slice 跟 splice

```js
var xs = [1,2,3,4,5];
// 纯的
xs.slice(0,3);
//=> [1,2,3]
xs.slice(0,3);
//=> [1,2,3]
xs.slice(0,3);
//=> [1,2,3]
// 不纯的
xs.splice(0,3);
//=> [1,2,3]
xs.splice(0,3);
//=> [4,5]
xs.splice(0,3);
//=> []
```

下一个例子

```js
// 不纯的
var minimum = 21;
var checkAge = function (age) {
    return age >= minimum;
};

// 纯的
var checkAge = function (age) {
    var minimum = 21;
    return age >= minimum;
};

// 纯的
const minimum = 21;
var checkAge = function (age) {
    return age >= minimum;
};
```

在不纯的版本中， checkAge 的结果将取决于 minimum 这个可变变量的值。换句话说，它取决于系统状态（system state）；这一点令人沮丧，因为它引入了外部的环境，从而增加了认知负荷（cognitive load）。

另一方面，使用纯函数的形式，函数就能做到自给自足。我们也可以让 minimum成为一个不可变（immutable）对象，这样就能保留纯粹性，因为状态不会有变化。要实现这个效果，必须得创建一个对象，然后调用 Object.freeze 方法

```js
var immutableState = Object.freeze({
    minimun: 21
});
```

> 副作用是在计算结果的过程中，系统状态的一种变化，或者与外部世界进行的可观察的交互。

副作用可能包含，但不限于：

* 更改文件系统
* 往数据库插入记录
* 发送一个 http 请求
* 可变数据
* 打印/log
* 获取用户输入
* DOM 查询
* 访问系统状态

这个列表还可以继续写下去。概括来讲，只要是跟函数外部环境发生的交互就都是副作用——这一点可能会让你怀疑无副作用编程的可行性。函数式编程的哲学就是假定副作用是造成不正当行为的主要原因。

这并不是说，要禁止使用一切副作用，而是说，要让它们在可控的范围内发生。后面讲到 functor 和 monad 的时候我们会学习如何控制它们。

### 追求“纯”的好处

* 可缓存性（Cacheable）
* 可移植性／自文档化（Portable / SelfDocumenting）
* 可测试性（Testable）
* 合理性（Reasonable）
* 并行性(Parallelism)

首先，纯函数总能够根据输入来做缓存。实现缓存的一种典型方式是 memoize 技术

```js
var squareNumber = memoize(function(){return x * x});
squareNumber(4);
//=> 16
squareNumber(4); // 从缓存中读取输入值为 4 的结果
//=> 16
squareNumber(5);
//=> 25
squareNumber(5); // 从缓存中读取输入值为 5 的结果
//=> 25
```

下面的代码是一个简单的实现，尽管它不太健壮

```js
function momoize(f) {
    var cache = {}
    return function () {
        var arg_str = JSON.stringify(arguments)
        // 有cache则返回cache里的，总是保存到cache里
        cache[arg_str] = cache[arg_str] || f(...arguments)
        return cache[arg_str]
    }
}
```

值得注意的一点是，可以通过延迟执行的方式把不纯的函数转换为纯函数

```js
var pureHttpCall = memoize(function(url, params){
    return function() { return $.getJSON(url, params); }
});
```

这里有趣的地方在于我们并没有真正发送 http 请求——只是返回了一个函数，当调用它的时候才会发请求。这个函数之所以有资格成为纯函数，是因为它总是会根据相同的输入返回相同的输出：给定了 url 和 params 之后，它就只会返回同一个发送 http 请求的函数

我们的 memoize 函数工作起来没有任何问题，虽然它缓存的并不是 http 请求所返回的结果，而是生成的函数。

重点是我们可以缓存任意一个函数，不管它们看起来多么具有破坏性。
___

纯函数是完全自给自足的，它需要的所有东西都能轻易获得。仔细思考思考这一点...这种自给自足的好处是什么呢？首先，纯函数的依赖很明确，因此更易于观察和理解

```js
// 不纯的
var signUp = function (attrs) {
    var user = saveUser(attrs);
    welcomeUser(user);
};
var saveUser = function (attrs) {
    var user = Db.save(attrs);
    //...
};
var welcomeUser = function (user) {
    // Email(user, ...);
    //...
};

// 纯的
var signUp = function (Db, Email, attrs) {
    return function () {
        // 参数绑定
        var user = saveUser(Db, attrs);
        welcomeUser(Email, user);
    };
};
var saveUser = function (Db, attrs) {
    //...
};
var welcomeUser = function (Email, user) {
    //...
};
```

纯函数对于其依赖必须要诚实，这样我们就能知道它的目的。仅从纯函数版本的 signUp 的签名就可以看出，它将要用到 Db、Email 和 attrs ，这在最小程度上给了我们足够多的信息。

其次，通过强迫“注入”依赖，或者把它们当作参数传递，我们的应用也更加灵活；因为数据库或者邮件客户端等等都参数化了

命令式编程中“典型”的方法和过程都深深地根植于它们所在的环境中，通过状态、依赖和有效作用（available effects）达成；纯函数与此相反，它与环境无关，只要我们愿意，可以在任何地方运行它。

第三点，纯函数让测试更加容易。

我们不需要伪造一个“真实的”支付网关，或者每一次测试之前都要配置、之后都要断言状态（assert the state）。只需简单地给函数一个输入，然后断言输出就好了。

很多人相信使用纯函数最大的好处是引用透明性（referential transparency）。如果一段代码可以替换成它执行所得的结果，而且是在不改变整个程序行为的前提下替换的，那么我们就说这段代码是引用透明的。

来观察一个例子

```js
var Immutable = require('immutable');

var decrementHP = function(player) {
    return player.set("hp", player.hp-1);
};

var isSameTeam = function(player1, player2) {
    return player1.team === player2.team;
};

var punch = function(player, target) {
    if(isSameTeam(player, target)) {
        return target;
    } else {
        return decrementHP(target);
    }
};
var jobe = Immutable.Map({name:"Jobe", hp:20, team: "red"});
var michael = Immutable.Map({name:"Michael", hp:20, team: "green"});

punch(jobe, michael);
//=> Immutable.Map({name:"Michael", hp:19, team: "green"})
```

decrementHP 、 isSameTeam 和 punch 都是纯函数，所以是引用透明的。我们可以使用一种叫做“等式推导”（equational reasoning）的技术来分析代码。所谓“等式推导”就是“一对一”替换，有点像在不考虑程序性执行的怪异行为（quirks of programmatic evaluation）的情况下，手动执行相关代码。我们借助引用透明性来剖析一下这段代码。

```js
var punch = function(player, target) {
    if(player.team === target.team) {
        return target;
    } else {
        return decrementHP(target);
    }
};
```

因为是不可变数据，我们可以直接把 team 替换为实际值：

```js
var punch = function(player, target) {
    if("red" === "green") {
        return target;
    } else {
        return decrementHP(target);
    }
};
```

if 语句执行结果为 false ，所以可以把整个 if 语句都删掉：

```js
var punch = function(player, target) {
    return decrementHP(target);
};
```

如果再内联 decrementHP ，我们会发现这种情况下， punch 变成了一个让 hp 的值减 1 的调用：

```js
var punch = function(player, target) {
    return target.set("hp", target.hp-1);
};
```

总之，等式推导带来的分析代码的能力对重构和理解代码非常重要。事实上，我们重构海鸥程序使用的正是这项技术：利用加和乘的特性。

最后一点，也是决定性的一点：我们可以并行运行任意纯函数。因为纯函数根本不需要访问共享的内存，而且根据其定义，纯函数也不会因副作用而进入竞争态（race condition）。

并行代码在服务端 js 环境以及使用了 web worker 的浏览器那里是非常容易实现的，因为它们使用了线程（thread）。


## 未完待续