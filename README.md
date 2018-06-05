# ECMAScript 6 - 新功能：概述和比较

### 索引
1. 常量 [constants](#constants)
1. 作用域 [scoping](#scoping)
1. 箭头函数 [arrow-functions](#arrow-functions)
1. 扩展参数处理 [extended-parameter-handling](#extended-parameter-handling)
1. 模板文字 [template-literals](#template-literals)
1. 扩展文字 [extended-literals](#extended-literals)
1. 增强的正则表达 [enhanced-regular-expression](#enhanced-regular-expression)
1. 增强的对象属性 [enhanced-object-properties](#enhanced-object-properties)
1. 解构赋值 [destructuring-assignment](#destructuring-assignment)
1. 模块 [modules](#modules)
1. 类 [classes](#classes)
1. 符号类型 [symbol-type](#symbol-type)
1. 迭代器 [iterators](#iterators)
1. Generators [generators](#generators)
1. Map / Set ＆ WeakMap / WeakSet [map-set-weak-map-weak-set](#map-set-weak-map-weak-set)
1. 键入数组 [typed-arrays](#typed-arrays)
1. 新的内置方法 [new-built-in-methods](#new-built-in-methods)
1. Promises [promises](#promises)
1. 元编程 [meta-programming](#meta-programming)
1. 国际化与本土化 [internationalization-localization](#internationalization-localization)

### 一. 常量
###### constants
- 常量（也称为“不可变变量”），即不能重新分配新内容的变量。注意：这只会使变量本身不可变，而不是它赋值的内容（例如，如果内容是一个对象，这意味着对象本身仍然可以被改变）。

 ```
const PI = 3.141593
PI > 3.0
```
```
//仅在ES5中通过对象属性的帮助，并且仅在全局上下文中，而不在块范围中
Object.defineProperty(typeof global === "object" ? global : window, "PI", {
    value:        3.141593,
    enumerable:   true,
    writable:     false,
    configurable: false
})
PI > 3.0;
```


### 二. 作用域
###### scoping

#### 1.块范围变量
- 块范围的变量（和常量）没有提升。

 ```
for (let i = 0; i < a.length; i++) {
    let x = a[i]
    …
}
for (let i = 0; i < b.length; i++) {
    let y = b[i]
    …
}
let callbacks = []
for (let i = 0; i <= 2; i++) {
    callbacks[i] = function () { return i * 2 }
}
callbacks[0]() === 0
callbacks[1]() === 2
callbacks[2]() === 4
```
```
var i, x, y;
for (i = 0; i < a.length; i++) {
    x = a[i];
    …
}
for (i = 0; i < b.length; i++) {
    y = b[i];
    …
}
var callbacks = [];
for (var i = 0; i <= 2; i++) {
    (function (i) {
        callbacks[i] = function() { return i * 2; };
    })(i);
}
callbacks[0]() === 0;
callbacks[1]() === 2;
callbacks[2]() === 4;
```

#### 2.块范围功能
- 块范围的函数定义。

 ```
{
    function foo () { return 1 }
    foo() === 1
    {
        function foo () { return 2 }
        foo() === 2
    }
    foo() === 1
}
```
```
//只能在ES5中借助块范围模拟函数范围和函数表达式
(function () {
    var foo = function () { return 1; }
    foo() === 1;
    (function () {
        var foo = function () { return 2; }
        foo() === 2;
    })();
    foo() === 1;
})();
```


### 三. 箭头函数
###### arrow-functions

#### 1.表达式
- 更具表达性的闭包语法。

 ```
odds  = evens.map(v => v + 1)
pairs = evens.map(v => ({ even: v, odd: v + 1 }))
nums  = evens.map((v, i) => v + i)
```
```
odds  = evens.map(function (v) { return v + 1; });
pairs = evens.map(function (v) { return { even: v, odd: v + 1 }; });
nums  = evens.map(function (v, i) { return v + i; });
```

#### 2.声明主体
- 更具表达性的闭包语法。

 ```
nums.forEach(v => {
   if (v % 5 === 0)
       fives.push(v)
})
```
```
nums.forEach(function (v) {
   if (v % 5 === 0)
       fives.push(v);
});
```

#### 3.词汇 this
- 更直观地处理当前的对象上下文。

 ```
this.nums.forEach((v) => {
    if (v % 5 === 0)
        this.fives.push(v)
})
```
```
//  变种 1
var self = this;
this.nums.forEach(function (v) {
    if (v % 5 === 0)
        self.fives.push(v);
});
//  变种 2
this.nums.forEach(function (v) {
    if (v % 5 === 0)
        this.fives.push(v);
}, this);
//  变种 3 (仅限ECMAScript 5.1以上版本)
this.nums.forEach(function (v) {
    if (v % 5 === 0)
        this.fives.push(v);
}.bind(this));
```


### 四. 扩展参数处理
###### extended-parameter-handling

#### 1.默认参数值
- 简单直观的函数参数默认值。

 ```
function f (x, y = 7, z = 42) {
    return x + y + z
}
f(1) === 50
```
```
function f (x, y, z) {
    if (y === undefined)
        y = 7;
    if (z === undefined)
        z = 42;
    return x + y + z;
};
f(1) === 50;
```

#### 2.剩余参数
- 将剩余参数聚合为可变参数函数的单个参数。

 ```
function f (x, y, ...a) {
    return (x + y) * a.length
}
f(1, 2, "hello", true, 7) === 9
```
```
function f (x, y) {
    var a = Array.prototype.slice.call(arguments, 2);
    return (x + y) * a.length;
};
f(1, 2, "hello", true, 7) === 9;
```

#### 3.传播运营商
- 将可迭代集合（如数组或字符串）的元素扩展到文字元素和单个函数参数中。

 ```
var params = [ "hello", true, 7 ]
var other = [ 1, 2, ...params ] // [ 1, 2, "hello", true, 7 ]
function f (x, y, ...a) {
    return (x + y) * a.length
}
f(1, 2, ...params) === 9
var str = "foo"
var chars = [ ...str ] // [ "f", "o", "o" ]
```
```
var params = [ "hello", true, 7 ];
var other = [ 1, 2 ].concat(params); // [ 1, 2, "hello", true, 7 ]
function f (x, y) {
    var a = Array.prototype.slice.call(arguments, 2);
    return (x + y) * a.length;
};
f.apply(undefined, [ 1, 2 ].concat(params)) === 9;
var str = "foo";
var chars = str.split(""); // [ "f", "o", "o" ]
```


### 五. 模板文字
###### template-literals

#### 1.字符串插值
- 单行和多行字符串的直观表达式插值。（注意：不要混淆，Template Literals最初在ECMAScript 6语言规范的草稿中被命名为“Template Strings”）

 ```
var customer = { name: "Foo" }
var card = { amount: 7, product: "Bar", unitprice: 42 }
var message = `Hello ${customer.name},
want to buy ${card.amount} ${card.product} for
a total of ${card.amount * card.unitprice} bucks?`
```
```
var customer = { name: "Foo" };
var card = { amount: 7, product: "Bar", unitprice: 42 };
var message = "Hello " + customer.name + ",\n" +
"want to buy " + card.amount + " " + card.product + " for\n" +
"a total of " + (card.amount * card.unitprice) + " bucks?";
```

#### 2.自定义插值
- 任意方法的灵活表达式插值。

 ```
get`http://example.com/foo?bar=${bar + baz}&quux=${quux}`
```
```
get([ "http://example.com/foo?bar=", "&quux=", "" ],bar + baz, quux);
```

#### 3.原始字符串访问
- 访问原始模板字符串内容（反斜杠不解释）。

 ```
function quux (strings, ...values) {
    strings[0] === "foo\n"
    strings[1] === "bar"
    strings.raw[0] === "foo\\n"
    strings.raw[1] === "bar"
    values[0] === 42
}
quux `foo\n${ 42 }bar`
String.raw `foo\n${ 42 }bar` === "foo\\n42bar"
```
```
//  在ES5中没有任何等价物
```


### 六. 扩展文字
###### extended-literals

#### 1.二进制和八进制文字
- 直接支持安全的二进制和八进制文字。

 ```
0b111110111 === 503
0o767 === 503
```
```
parseInt("111110111", 2) === 503;
parseInt("767", 8) === 503;
0767 === 503; // 只有在非严格的向后兼容模式下
```

#### 2.Unicode字符串和RegExp Literal
- 在字符串和正则表达式中使用Unicode的扩展支持。

 ```
"𠮷".length === 2
"𠮷".match(/./u)[0].length === 2
"𠮷" === "\uD842\uDFB7"
"𠮷" === "\u{20BB7}"
"𠮷".codePointAt(0) == 0x20BB7
for (let codepoint of "𠮷") console.log(codepoint)
```
```
"𠮷".length === 2;
"𠮷".match(/(?:[\0-\t\x0B\f\x0E-\u2027\u202A-\uD7FF\uE000-\uFFFF][\uD800-\uDBFF][\uDC00-\uDFFF][\uD800-\uDBFF](?![\uDC00-\uDFFF])(?:[^\uD800-\uDBFF]^)[\uDC00-\uDFFF])/)[0].length === 2;
"𠮷" === "\uD842\uDFB7";
//  no equivalent in ES5
//  no equivalent in ES5
//  no equivalent in ES5
```


### 七. 增强的正则表达式
###### enhanced-regular-expression

#### 正则表达式粘滞匹配
- 保持匹配位置在匹配之间保持粘性，这种方式支持对任意长输入字符串进行高效解析，即使使用任意数量的不同正则表达式。

 ```
let parser = (input, match) => {
    for (let pos = 0, lastPos = input.length; pos < lastPos; ) {
        for (let i = 0; i < match.length; i++) {
            match[i].pattern.lastIndex = pos
            let found
            if ((found = match[i].pattern.exec(input)) !== null) {
                match[i].action(found)
                pos = match[i].pattern.lastIndex
                break
            }
        }
    }
}
let report = (match) => {
    console.log(JSON.stringify(match))
}
parser("Foo 1 Bar 7 Baz 42", [
    { pattern: /Foo\s+(\d+)/y, action: (match) => report(match) },
    { pattern: /Bar\s+(\d+)/y, action: (match) => report(match) },
    { pattern: /Baz\s+(\d+)/y, action: (match) => report(match) },
    { pattern: /\s*/y,         action: (match) => {}            }
])
```
```
var parser = function (input, match) {
    for (var i, found, inputTmp = input; inputTmp !== ""; ) {
        for (i = 0; i < match.length; i++) {
            if ((found = match[i].pattern.exec(inputTmp)) !== null) {
                match[i].action(found);
                inputTmp = inputTmp.substr(found[0].length);
                break;
            }
        }
    }
}
var report = function (match) {
    console.log(JSON.stringify(match));
};
parser("Foo 1 Bar 7 Baz 42", [
    { pattern: /^Foo\s+(\d+)/, action: function (match) { report(match); } },
    { pattern: /^Bar\s+(\d+)/, action: function (match) { report(match); } },
    { pattern: /^Baz\s+(\d+)/, action: function (match) { report(match); } },
    { pattern: /^\s*/,         action: function (match) {}                 }
]);
```


### 八. 增强的对象属性
###### enhanced-object-properties

#### 1.属性速记
- 通用对象属性定义惯用语法的长度较短。

 ```
var x = 0, y = 0
obj = { x, y }
```
```
var x = 0, y = 0;
obj = { x: x, y: y };
```

#### 2.计算属性名称
- 支持对象属性定义中的计算名称。

 ```
let obj = {
   foo: "bar",[ "baz" + quux() ]: 42
}
```
```
var obj = {
    foo: "bar"
};
obj[ "baz" + quux() ] = 42;
```

#### 3.方法属性
- 对于常规函数和生成器函数，支持对象属性定义中的方法符号。

 ```
obj = {
    foo (a, b) {
        …
    },
    bar (x, y) {
        …
    },
    *quux (x, y) {
        …
    }
}
```
```
obj = {
    foo: function (a, b) {
        …
    },
    bar: function (x, y) {
        …
    },
    //  quux：在ES5中没有相应的功能
    …
};
```


### 九. 解构赋值
###### destructuring-assignment

#### 1.数组匹配
- 在分配过程中直观且灵活地将阵列解构成单个变量。

 ```
var list = [ 1, 2, 3 ]
var [ a, , b ] = list
[ b, a ] = [ a, b ]
```
```
var list = [ 1, 2, 3 ];
var a = list[0], b = list[2];
var tmp = a; a = b; b = tmp;
```

#### 2.对象匹配，速记符号
- 在分配过程中直观且灵活地将对象解构成单个变量。

 ```
var { op, lhs, rhs } = getASTNode()
```
```
var tmp = getASTNode();
var op  = tmp.op;
var lhs = tmp.lhs;
var rhs = tmp.rhs;
```

#### 3.对象匹配，深度匹配
- 在分配过程中直观且灵活地将对象解构成单个变量。

 ```
var { op: a, lhs: { op: b }, rhs: c } = getASTNode()
```
```
var tmp = getASTNode();
var a = tmp.op;
var b = tmp.lhs.op;
var c = tmp.rhs;
```

#### 4.对象和数组匹配，默认值
- 简单直观的默认值，用于解构对象和数组。

 ```
var obj = { a: 1 }
var list = [ 1 ]
var { a, b = 2 } = obj
var [ x, y = 2 ] = list
```
```
var obj = { a: 1 };
var list = [ 1 ];
var a = obj.a;
var b = obj.b === undefined ? 2 : obj.b;
var x = list[0];
var y = list[1] === undefined ? 2 : list[1];
```

#### 5.参数上下文匹配
- 在函数调用期间，将数组和对象直观且灵活地解构成单个参数。

 ```
function f ([ name, val ]) {
    console.log(name, val)
}
function g ({ name: n, val: v }) {
    console.log(n, v)
}
function h ({ name, val }) {
    console.log(name, val)
}
f([ "bar", 42 ])
g({ name: "foo", val:  7 })
h({ name: "bar", val: 42 })
```
```
function f (arg) {
    var name = arg[0];
    var val  = arg[1];
    console.log(name, val);
};
function g (arg) {
    var n = arg.name;
    var v = arg.val;
    console.log(n, v);
};
function h (arg) {
    var name = arg.name;
    var val  = arg.val;
    console.log(name, val);
};
f([ "bar", 42 ]);
g({ name: "foo", val:  7 });
h({ name: "bar", val: 42 });
```

#### 6.失败软解构
- 失效软解构，可选择默认值。

 ```
var list = [ 7, 42 ]
var [ a = 1, b = 2, c = 3, d ] = list
a === 7
b === 42
c === 3
d === undefined
```
```
var list = [ 7, 42 ];
var a = typeof list[0] !== "undefined" ? list[0] : 1;
var b = typeof list[1] !== "undefined" ? list[1] : 2;
var c = typeof list[2] !== "undefined" ? list[2] : 3;
var d = typeof list[3] !== "undefined" ? list[3] : undefined;
a === 7;
b === 42;
c === 3;
d === undefined;
```

