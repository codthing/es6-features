# ECMAScript 6 - 新功能：概述和比较

## 索引
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
1. 生成器 [generators](#generators)
1. Map / Set ＆ WeakMap / WeakSet [map-set-weak-map-weak-set](#map-set-weak-map-weak-set)
1. 键入数组 [typed-arrays](#typed-arrays)
1. 新的内置方法 [new-built-in-methods](#new-built-in-methods)
1. Promises [promises](#promises)
1. 元编程 [meta-programming](#meta-programming)
1. 国际化与本土化 [internationalization-localization](#internationalization-localization)

## 一. 常量
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


## 二. 作用域
###### scoping

### 1.块范围变量
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

### 2.块范围功能
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


## 三. 箭头函数
###### arrow-functions

### 1.表达式
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

### 2.声明主体
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

### 3.词汇 this
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


## 四. 扩展参数处理
###### extended-parameter-handling

### 1.默认参数值
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

### 2.剩余参数
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

### 3.传播运营商
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


## 五. 模板文字
###### template-literals

### 1.字符串插值
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

### 2.自定义插值
- 任意方法的灵活表达式插值。

 ```
get`http://example.com/foo?bar=${bar + baz}&quux=${quux}`
```
```
get([ "http://example.com/foo?bar=", "&quux=", "" ],bar + baz, quux);
```

### 3.原始字符串访问
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


## 六. 扩展文字
###### extended-literals

### 1.二进制和八进制文字
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

### 2.Unicode字符串和RegExp Literal
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


## 七. 增强的正则表达式
###### enhanced-regular-expression

### 正则表达式粘滞匹配
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


## 八. 增强的对象属性
###### enhanced-object-properties

### 1.属性速记
- 通用对象属性定义惯用语法的长度较短。

 ```
var x = 0, y = 0
obj = { x, y }
```
```
var x = 0, y = 0;
obj = { x: x, y: y };
```

### 2.计算属性名称
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

### 3.方法属性
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

### 1.数组匹配
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

### 2.对象匹配，速记符号
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

### 3.对象匹配，深度匹配
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

### 4.对象和数组匹配，默认值
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

### 5.参数上下文匹配
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

### 6.失败软解构
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


## 十. 模块
###### modules

### 1.值导出/导入
- 支持从/向模块输出/输入值，而不会造成全局命名空间污染。

 ```
//  lib/math.js
export function sum (x, y) { return x + y }
export var pi = 3.141593
//  someApp.js
import * as math from "lib/math"
console.log("2π = " + math.sum(math.pi, math.pi))
//  otherApp.js
import { sum, pi } from "lib/math"
console.log("2π = " + sum(pi, pi))
```
```
//  lib/math.js
LibMath = {};
LibMath.sum = function (x, y) { return x + y };
LibMath.pi = 3.141593;
//  someApp.js
var math = LibMath;
console.log("2π = " + math.sum(math.pi, math.pi));
//  otherApp.js
var sum = LibMath.sum, pi = LibMath.pi;
console.log("2π = " + sum(pi, pi));
```

### 2.默认和通配符
- 将值标记为默认导出值和值的质量混合。

 ```
//  lib/mathplusplus.js
export * from "lib/math"
export var e = 2.71828182846
export default (x) => Math.exp(x)
//  someApp.js
import exp, { pi, e } from "lib/mathplusplus"
console.log("e^{π} = " + exp(pi))
```
```
//  lib/mathplusplus.js
LibMathPP = {};
for (symbol in LibMath)
    if (LibMath.hasOwnProperty(symbol))
        LibMathPP[symbol] = LibMath[symbol];
LibMathPP.e = 2.71828182846;
LibMathPP.exp = function (x) { return Math.exp(x) };
//  someApp.js
var exp = LibMathPP.exp, pi = LibMathPP.pi, e = LibMathPP.e;
console.log("e^{π} = " + exp(pi));
```


## 十一. 类
###### classes

### 1.类定义
- 更直观，面向对象的风格和空模板的类。

 ```
class Shape {
    constructor (id, x, y) {
        this.id = id
        this.move(x, y)
    }
    move (x, y) {
        this.x = x
        this.y = y
    }
}
```
```
var Shape = function (id, x, y) {
    this.id = id;
    this.move(x, y);
};
Shape.prototype.move = function (x, y) {
    this.x = x;
    this.y = y;
};
```

### 2.类继承
- 更直观，OOP风格和空模板继承。

 ```
class Rectangle extends Shape {
    constructor (id, x, y, width, height) {
        super(id, x, y)
        this.width  = width
        this.height = height
    }
}
class Circle extends Shape {
    constructor (id, x, y, radius) {
        super(id, x, y)
        this.radius = radius
    }
}
```
```
var Rectangle = function (id, x, y, width, height) {
    Shape.call(this, id, x, y);
    this.width  = width;
    this.height = height;
};
Rectangle.prototype = Object.create(Shape.prototype);
Rectangle.prototype.constructor = Rectangle;
var Circle = function (id, x, y, radius) {
    Shape.call(this, id, x, y);
    this.radius = radius;
};
Circle.prototype = Object.create(Shape.prototype);
Circle.prototype.constructor = Circle;
```

### 3.表达式的类继承
- 通过扩展表达式来生成函数对象，从而支持mixin风格的继承。 [注意：当然，泛型聚合函数通常由像这样的库提供]

 ```
var aggregation = (baseClass, ...mixins) => {
    let base = class _Combined extends baseClass {
        constructor (...args) {
            super(...args)
            mixins.forEach((mixin) => {
                mixin.prototype.initializer.call(this)
            })
        }
    }
    let copyProps = (target, source) => {
        Object.getOwnPropertyNames(source)
            .concat(Object.getOwnPropertySymbols(source))
            .forEach((prop) => {
            if (prop.match(/^(?:constructor|prototype|arguments|caller|name|bind|call|apply|toString|length)$/))
                return
            Object.defineProperty(target, prop, Object.getOwnPropertyDescriptor(source, prop))
        })
    }
    mixins.forEach((mixin) => {
        copyProps(base.prototype, mixin.prototype)
        copyProps(base, mixin)
    })
    return base
}
class Colored {
    initializer ()     { this._color = "white" }
    get color ()       { return this._color }
    set color (v)      { this._color = v }
}
class ZCoord {
    initializer ()     { this._z = 0 }
    get z ()           { return this._z }
    set z (v)          { this._z = v }
}
class Shape {
    constructor (x, y) { this._x = x; this._y = y }
    get x ()           { return this._x }
    set x (v)          { this._x = v }
    get y ()           { return this._y }
    set y (v)          { this._y = v }
}
class Rectangle extends aggregation(Shape, Colored, ZCoord) {}
var rect = new Rectangle(7, 42)
rect.z     = 1000
rect.color = "red"
console.log(rect.x, rect.y, rect.z, rect.color)
```
```
var aggregation = function (baseClass, mixins) {
    var base = function () {
        baseClass.apply(this, arguments);
        mixins.forEach(function (mixin) {
            mixin.prototype.initializer.call(this);
        }.bind(this));
    };
    base.prototype = Object.create(baseClass.prototype);
    base.prototype.constructor = base;
    var copyProps = function (target, source) {
        Object.getOwnPropertyNames(source).forEach(function (prop) {
            if (prop.match(/^(?:constructor|prototype|arguments|caller|name|bind|call|apply|toString|length)$/))
                return
            Object.defineProperty(target, prop, Object.getOwnPropertyDescriptor(source, prop))
        })
    }
    mixins.forEach(function (mixin) {
        copyProps(base.prototype, mixin.prototype);
        copyProps(base, mixin);
    });
    return base;
};
var Colored = function () {};
Colored.prototype = {
    initializer: function ()  { this._color = "white"; },
    getColor:    function ()  { return this._color; },
    setColor:    function (v) { this._color = v; }
};
var ZCoord = function () {};
ZCoord.prototype = {
    initializer: function ()  { this._z = 0; },
    getZ:        function ()  { return this._z; },
    setZ:        function (v) { this._z = v; }
};
var Shape = function (x, y) {
    this._x = x; this._y = y;
};
Shape.prototype = {
    getX: function ()  { return this._x; },
    setX: function (v) { this._x = v; },
    getY: function ()  { return this._y; },
    setY: function (v) { this._y = v; }
}
var _Combined = aggregation(Shape, [ Colored, ZCoord ]);
var Rectangle = function (x, y) {
    _Combined.call(this, x, y);
};
Rectangle.prototype = Object.create(_Combined.prototype);
Rectangle.prototype.constructor = Rectangle;
var rect = new Rectangle(7, 42);
rect.setZ(1000);
rect.setColor("red");
console.log(rect.getX(), rect.getY(), rect.getZ(), rect.getColor());
```

### 4.基类访问
- 直观地访问基类的构造函数和方法。

 ```
class Shape {
    …
    toString () {
        return `Shape(${this.id})`
    }
}
class Rectangle extends Shape {
    constructor (id, x, y, width, height) {
        super(id, x, y)
        …
    }
    toString () {
        return "Rectangle > " + super.toString()
    }
}
class Circle extends Shape {
    constructor (id, x, y, radius) {
        super(id, x, y)
        …
    }
    toString () {
        return "Circle > " + super.toString()
    }
}
```
```
var Shape = function (id, x, y) {
    …
};
Shape.prototype.toString = function (x, y) {
    return "Shape(" + this.id + ")"
};
var Rectangle = function (id, x, y, width, height) {
    Shape.call(this, id, x, y);
    …
};
Rectangle.prototype.toString = function () {
    return "Rectangle > " + Shape.prototype.toString.call(this);
};
var Circle = function (id, x, y, radius) {
    Shape.call(this, id, x, y);
    …
};
Circle.prototype.toString = function () {
    return "Circle > " + Shape.prototype.toString.call(this);
};
```

### 5.静态成员
- 简单支持静态类成员。

 ```
class Rectangle extends Shape {
    …
    static defaultRectangle () {
        return new Rectangle("default", 0, 0, 100, 100)
    }
}
class Circle extends Shape {
    …
    static defaultCircle () {
        return new Circle("default", 0, 0, 100)
    }
}
var defRectangle = Rectangle.defaultRectangle()
var defCircle    = Circle.defaultCircle()
```
```
var Rectangle = function (id, x, y, width, height) {
    …
};
Rectangle.defaultRectangle = function () {
    return new Rectangle("default", 0, 0, 100, 100);
};
var Circle = function (id, x, y, width, height) {
    …
};
Circle.defaultCircle = function () {
    return new Circle("default", 0, 0, 100);
};
var defRectangle = Rectangle.defaultRectangle();
var defCircle    = Circle.defaultCircle();
```

### 6.getter / setter
- Getter / Setter也直接在类中（而不仅仅是在对象初始化器中，因为从ECMAScript 5.1开始是可能的）。

 ```
class Rectangle {
    constructor (width, height) {
        this._width  = width
        this._height = height
    }
    set width  (width)  { this._width = width               }
    get width  ()       { return this._width                }
    set height (height) { this._height = height             }
    get height ()       { return this._height               }
    get area   ()       { return this._width * this._height }
}
var r = new Rectangle(50, 20)
r.area === 1000
```
```
var Rectangle = function (width, height) {
    this._width  = width;
    this._height = height;
};
Rectangle.prototype = {
    set width  (width)  { this._width = width;               },
    get width  ()       { return this._width;                },
    set height (height) { this._height = height;             },
    get height ()       { return this._height;               },
    get area   ()       { return this._width * this._height; }
};
var r = new Rectangle(50, 20);
r.area === 1000;
```


## 十二. 符号类型
###### symbol-type

### 1.符号类型
- 唯一且不可变的数据类型将用作对象属性的标识符。符号可以有可选的描述，但仅用于调试目的。

 ```
Symbol("foo") !== Symbol("foo")
const foo = Symbol()
const bar = Symbol()
typeof foo === "symbol"
typeof bar === "symbol"
let obj = {}
obj[foo] = "foo"
obj[bar] = "bar"
JSON.stringify(obj) // {}
Object.keys(obj) // []
Object.getOwnPropertyNames(obj) // []
Object.getOwnPropertySymbols(obj) // [ foo, bar ]
```
```
// 在ES5中没有等价物
```

### 2.全局符号
- 全局符号，通过唯一键索引。

 ```
Symbol.for("app.foo") === Symbol.for("app.foo")
const foo = Symbol.for("app.foo")
const bar = Symbol.for("app.bar")
Symbol.keyFor(foo) === "app.foo"
Symbol.keyFor(bar) === "app.bar"
typeof foo === "symbol"
typeof bar === "symbol"
let obj = {}
obj[foo] = "foo"
obj[bar] = "bar"
JSON.stringify(obj) // {}
Object.keys(obj) // []
Object.getOwnPropertyNames(obj) // []
Object.getOwnPropertySymbols(obj) // [ foo, bar ]
```
```
// 在ES5中没有等价物
```


## 十三. 迭代器
###### iterators
### 迭代器和For-Of运算符
- 支持“迭代”协议，允许对象定制其迭代行为。 此外，支持“迭代器”协议来产生序列值（有限或无限）。 最后，为运算符提供一个方便的方法来遍历一个可迭代对象的所有值。
```
let fibonacci = {
    [Symbol.iterator]() {
        let pre = 0, cur = 1
        return {
           next () {
               [ pre, cur ] = [ cur, pre + cur ]
               return { done: false, value: cur }
           }
        }
    }
}
for (let n of fibonacci) {
    if (n > 1000)
        break
    console.log(n)
}
```
```
var fibonacci = {
    next: (function () {
        var pre = 0, cur = 1;
        return function () {
            tmp = pre;
            pre = cur;
            cur += tmp;
            return cur;
        };
    })()};
var n;
for (;;) {
    n = fibonacci.next();
    if (n > 1000)
        break;
    console.log(n);
}
```


## 十四. 生成器
###### generators

### 1.生成器函数，迭代器协议
- 支持生成器，这是一个包含生成器函数的迭代器的特例，可以暂停和恢复控制流，以生成序列值（有限或无限）。
```
let fibonacci = {
    *[Symbol.iterator]() {
        let pre = 0, cur = 1
        for (;;) {
            [ pre, cur ] = [ cur, pre + cur ]
            yield cur
        }
    }
}
for (let n of fibonacci) {
    if (n > 1000)
        break
    console.log(n)
}
```
```
var fibonacci = {
    next: (function () {
        var pre = 0, cur = 1;
        return function () {
            tmp = pre;
            pre = cur;
            cur += tmp;
            return cur;
        };
    })()
};
var n;
for (;;) {
    n = fibonacci.next();
    if (n > 1000)
        break;
    console.log(n);
}
```

### 2.生成器函数，直接使用
- 支持生成器函数，这是函数的一个特殊变体，可以暂停和恢复控制流，以生成序列值（有限或无限）。
```
function* range (start, end, step) {
    while (start < end) {
        yield start
        start += step
    }
}
for (let i of range(0, 10, 2)) {
    console.log(i) // 0, 2, 4, 6, 8
}
```
```
function range (start, end, step) {
    var list = [];
    while (start < end) {
        list.push(start);
        start += step;
    }
    return list;
}
var r = range(0, 10, 2);
for (var i = 0; i < r.length; i++) {
    console.log(r[i]); // 0, 2, 4, 6, 8
}
```

### 3.生成器匹配
- 支持生成器函数，即可以暂停和恢复控制流的函数，以生成和传播值序列（有限或无限）。
```
let fibonacci = function* (numbers) {
    let pre = 0, cur = 1
    while (numbers-- > 0) {
        [ pre, cur ] = [ cur, pre + cur ]
        yield cur
    }
}
for (let n of fibonacci(1000))
    console.log(n)
let numbers = [ ...fibonacci(1000) ]
let [ n1, n2, n3, ...others ] = fibonacci(1000)
```
```
//  在ES5中没有等价物
```

### 4.生成器控制流程
- 支持生成器，这是迭代器的一个特例，它可以暂停和恢复控制流，以支持与“Promises”（见下文）结合的“协同例程”风格的异步编程。 [注意：通用异步函数通常由可重用的库提供，并在此给出以便更好地理解。 在实践中看到co或Bluebird的协同程序。]
```
//  通用异步控制流驱动程序
function async (proc, ...params) {
    let iterator = proc(...params)
    return new Promise((resolve, reject) => {
        let loop = (value) => {
            let result
            try {
                result = iterator.next(value)
            }
            catch (err) {
                reject(err)
            }
            if (result.done)
                resolve(result.value)
            else if (   typeof result.value      === "object"
                     && typeof result.value.then === "function")
                result.value.then((value) => {
                    loop(value)
                }, (err) => {
                    reject(err)
                })
            else
                loop(result.value)
        }
        loop()
    })
}
//  特定于应用程序的异步构建器
function makeAsync (text, after) {
    return new Promise((resolve, reject) => {
        setTimeout(() => resolve(text), after)
    })
}
//  应用程序特定的异步过程
async(function* (greeting) {
    let foo = yield makeAsync("foo", 300)
    let bar = yield makeAsync("bar", 200)
    let baz = yield makeAsync("baz", 100)
    return `${greeting} ${foo} ${bar} ${baz}`
}, "Hello").then((msg) => {
    console.log("RESULT:", msg) // "Hello foo bar baz"
})
```
```
// 在ES5中没有等价物
```


### 5.生成器方法
- 基于生成器函数，支持生成器方法，即类和对象中的方法。
```
class Clz {
    * bar () {
        …
    }
}
let Obj = {
    * foo () {
        …
    }
}
```
```
// 在ES5中没有等价物
```


## 十五. Map/Set & WeakMap/WeakSet
###### map-set-weak-map-weak-set

### 1.设置数据结构
- 基于集合的常用算法的更清晰的数据结构。
```
let s = new Set()
s.add("hello").add("goodbye").add("hello")
s.size === 2
s.has("hello") === true
for (let key of s.values()) // 插入指令
    console.log(key)
```
```
var s = {};
s["hello"] = true; s["goodbye"] = true; s["hello"] = true;
Object.keys(s).length === 2;
s["hello"] === true;
for (var key in s) // 任意的顺序
    if (s.hasOwnProperty(key))
        console.log(s[key]);
```

### 2.map 数据结构
- 基于 map 的常用算法的更清晰的数据结构。
```
let m = new Map()
let s = Symbol()
m.set("hello", 42)
m.set(s, 34)
m.get(s) === 34
m.size === 2
for (let [ key, val ] of m.entries())
    console.log(key + " = " + val)
```
```
var m = {};
// 在ES5中没有任何等价物
m["hello"] = 42;
// 在ES5中没有任何等价物
// 在ES5中没有任何等价物
Object.keys(m).length === 2;
for (key in m) {
    if (m.hasOwnProperty(key)) {
        var val = m[key];
        console.log(key + " = " + val);
    }
}
```

### 3. 弱链数据结构
- 无内存泄漏的Object-key'd并排数据结构。
```
let isMarked = new WeakSet()
let attachedData = new WeakMap()
export class Node {
    constructor (id){ this.id = id }
    mark (){ isMarked.add(this) }
    unmark (){ isMarked.delete(this) }
    marked (){ return isMarked.has(this) }
    set data (data){ attachedData.set(this, data)}
    get data (){ return attachedData.get(this) }
}
let foo = new Node("foo")
JSON.stringify(foo) === '{"id":"foo"}'
foo.mark()
foo.data = "bar"
foo.data === "bar"
JSON.stringify(foo) === '{"id":"foo"}'
isMarked.has(foo) === true
attachedData.has(foo) === true
foo = null  /* remove only reference to foo */
attachedData.has(foo) === false
isMarked.has(foo) === false
```
```
// 在ES5中没有等价物
```



## 十六. 键入数组
###### typed-arrays
- 支持任意字节数据结构来实现网络协议，密码算法，文件格式操作等。
```
//  相当于以下C结构的ES6类：
//  struct Example { unsigned long id; char username[16]; float amountDue }
class Example {
    constructor (buffer = new ArrayBuffer(24)) {
        this.buffer = buffer
    }
    set buffer (buffer) {
        this._buffer    = buffer
        this._id        = new Uint32Array (this._buffer,  0,  1)
        this._username  = new Uint8Array  (this._buffer,  4, 16)
        this._amountDue = new Float32Array(this._buffer, 20,  1)
    }
    get buffer ()     { return this._buffer       }
    set id (v)        { this._id[0] = v           }
    get id ()         { return this._id[0]        }
    set username (v)  { this._username[0] = v     }
    get username ()   { return this._username[0]  }
    set amountDue (v) { this._amountDue[0] = v    }
    get amountDue ()  { return this._amountDue[0] }
}
let example = new Example()
example.id = 7
example.username = "John Doe"
example.amountDue = 42.0
```
```
// 在ES5中没有等价物
// （只有HTML5中的一个等效项）
```


## 十七. 新的内置方法
###### new-built-in-methods

### 1.对象属性分配
- 将一个或多个源对象的枚举属性分配到目标对象的新函数。
```
var dest = { quux: 0 }
var src1 = { foo: 1, bar: 2 }
var src2 = { foo: 3, baz: 4 }
Object.assign(dest, src1, src2)
dest.quux === 0
dest.foo  === 3
dest.bar  === 2
dest.baz  === 4
```
```
var dest = { quux: 0 };
var src1 = { foo: 1, bar: 2 };
var src2 = { foo: 3, baz: 4 };
Object.keys(src1).forEach(function(k) {
    dest[k] = src1[k];
});
Object.keys(src2).forEach(function(k) {
    dest[k] = src2[k];
});
dest.quux === 0;
dest.foo  === 3;
dest.bar  === 2;
dest.baz  === 4;
```

### 2.数组元素查找
- 在数组中查找元素的新功能。
```
[ 1, 3, 4, 2 ].find(x => x > 3) // 4
[ 1, 3, 4, 2 ].findIndex(x => x > 3) // 2
```
```
[ 1, 3, 4, 2 ].filter(function (x) { return x > 3; })[0]; // 4
// 在ES5中没有任何等价物
```

### 3.字符串重复
- 新的字符串重复功能。
```
" ".repeat(4 * depth)
"foo".repeat(3)
```
```
Array((4 * depth) + 1).join(" ");
Array(3 + 1).join("foo");
```

### 4.字符串搜索
- 新的特定字符串函数来搜索子字符串。
```
"hello".startsWith("ello", 1) // true
"hello".endsWith("hell", 4)   // true
"hello".includes("ell")       // true
"hello".includes("ell", 1)    // true
"hello".includes("ell", 2)    // false
```
```
"hello".indexOf("ello") === 1;    // true
"hello".indexOf("hell") === (4 - "hell".length); // true
"hello".indexOf("ell") !== -1;    // true
"hello".indexOf("ell", 1) !== -1; // true
"hello".indexOf("ell", 2) !== -1; // false
```

### 5.数字类型检查
- 用于检查非数字和有限数字的新函数。
```
Number.isNaN(42) === false
Number.isNaN(NaN) === true
Number.isFinite(Infinity) === false
Number.isFinite(-Infinity) === false
Number.isFinite(NaN) === false
Number.isFinite(123) === true
```
```
var isNaN = function (n) {
    return n !== n;
};
var isFinite = function (v) {
    return (typeof v === "number" && !isNaN(v) && v !== Infinity && v !== -Infinity);
};
isNaN(42) === false;
isNaN(NaN) === true;
isFinite(Infinity) === false;
isFinite(-Infinity) === false;
isFinite(NaN) === false;
isFinite(123) === true;
```

### 6.数字安全检查
- 检查整数是否在安全范围内，即它是否由JavaScript正确表示（其中所有数字（包括整数）在技术上都是浮点数）。
```
Number.isSafeInteger(42) === true
Number.isSafeInteger(9007199254740992) === false
```
```
function isSafeInteger (n) {
    return (
           typeof n === 'number'
        && Math.round(n) === n
        && -(Math.pow(2, 53) - 1) <= n
        && n <= (Math.pow(2, 53) - 1)
    );
}
isSafeInteger(42) === true;
isSafeInteger(9007199254740992) === false;
```

### 7.数量比较
- 标准 Epsilon 值的可用性，用于更精确地比较浮点数。
```
console.log(0.1 + 0.2 === 0.3) // false
console.log(Math.abs((0.1 + 0.2) - 0.3) < Number.EPSILON) // true
```
```
console.log(0.1 + 0.2 === 0.3); // false
console.log(Math.abs((0.1 + 0.2) - 0.3) < 2.220446049250313e-16); // true
```

### 8.数字截断
- 将浮点数截断为其整数部分，完全删除小数部分。
```
console.log(Math.trunc(42.7)) // 42
console.log(Math.trunc( 0.1)) // 0
console.log(Math.trunc(-0.1)) // -0
```
```
function mathTrunc (x) {
    return (x < 0 ? Math.ceil(x) : Math.floor(x));
}
console.log(mathTrunc(42.7)) // 42
console.log(mathTrunc( 0.1)) // 0
console.log(mathTrunc(-0.1)) // -0
```

### 9.数字符号确定
- 确定一个数字的符号，包括有符号零和非数字的特殊情况。
```
console.log(Math.sign(7))   // 1
console.log(Math.sign(0))   // 0
console.log(Math.sign(-0))  // -0
console.log(Math.sign(-7))  // -1
console.log(Math.sign(NaN)) // NaN
```
```
function mathSign (x) {
    return ((x === 0 || isNaN(x)) ? x : (x > 0 ? 1 : -1));
}
console.log(mathSign(7))   // 1
console.log(mathSign(0))   // 0
console.log(mathSign(-0))  // -0
console.log(mathSign(-7))  // -1
console.log(mathSign(NaN)) // NaN
```


## 十八. Promises
###### promises

### 1.Promise 用法
- 可以异步生成并在将来可用的值的第一类表示。
```
function msgAfterTimeout (msg, who, timeout) {
    return new Promise((resolve, reject) => {
        setTimeout(() => resolve(`${msg} Hello ${who}!`), timeout)
    })
}
msgAfterTimeout("", "Foo", 100).then((msg) =>
    msgAfterTimeout(msg, "Bar", 200)
).then((msg) => {
    console.log(`done after 300ms:${msg}`)
})
```
```
function msgAfterTimeout (msg, who, timeout, onDone) {
    setTimeout(function () {
        onDone(msg + " Hello " + who + "!");
    }, timeout);
}
msgAfterTimeout("", "Foo", 100, function (msg) {
    msgAfterTimeout(msg, "Bar", 200, function (msg) {
        console.log("done after 300ms:" + msg);
    });
});
```

### 2.Promise 组合
- 将一个或多个 Promise 组合成新的 Promise，而无需亲自处理底层异步操作的指令。
```
function fetchAsync (url, timeout, onData, onError) {
    …
}
let fetchPromised = (url, timeout) => {
    return new Promise((resolve, reject) => {
        fetchAsync(url, timeout, resolve, reject)
    })
}
Promise.all([
    fetchPromised("http://backend/foo.txt", 500),
    fetchPromised("http://backend/bar.txt", 500),
    fetchPromised("http://backend/baz.txt", 500)
]).then((data) => {
    let [ foo, bar, baz ] = data
    console.log(`success: foo=${foo} bar=${bar} baz=${baz}`)
}, (err) => {
    console.log(`error: ${err}`)
})
```
```
function fetchAsync (url, timeout, onData, onError) {
    …
}
function fetchAll (request, onData, onError) {
    var result = [], results = 0;
    for (var i = 0; i < request.length; i++) {
        result[i] = null;
        (function (i) {
            fetchAsync(request[i].url, request[i].timeout, function (data) {
                result[i] = data;
                if (++results === request.length)
                    onData(result);
            }, onError);
        })(i);
    }
}
fetchAll([
    { url: "http://backend/foo.txt", timeout: 500 },
    { url: "http://backend/bar.txt", timeout: 500 },
    { url: "http://backend/baz.txt", timeout: 500 }
], function (data) {
    var foo = data[0], bar = data[1], baz = data[2];
    console.log("success: foo=" + foo + " bar=" + bar + " baz=" + baz);
}, function (err) {
    console.log("error: " + err);
});
```


## 十九. 元编程
###### meta-programming

### 1.代理
- 挂钩到运行时级别的对象元操作。
```
let target = {
    foo: "Welcome, foo"
}
let proxy = new Proxy(target, {
    get (receiver, name) {
        return name in receiver ? receiver[name] : `Hello, ${name}`
    }
})
proxy.foo   === "Welcome, foo"
proxy.world === "Hello, world"
```
```
// 在ES5中没有等价物
```

### 2.反射
- 进行对应于对象元操作的调用。
```
let obj = { a: 1 }
Object.defineProperty(obj, "b", { value: 2 })
obj[Symbol("c")] = 3
Reflect.ownKeys(obj) // [ "a", "b", Symbol(c) ]
```
```
var obj = { a: 1 };
Object.defineProperty(obj, "b", { value: 2 });
// 在ES5中没有等价物
Object.getOwnPropertyNames(obj); // [ "a", "b" ]
```

## 二十. 国际化与本土化
###### internationalization-localization

### 1.整理
- 排序一组字符串并在一组字符串中搜索。 整理由区域设置参数化并且了解Unicode。
```
// 在德语中，“ä”与“a”
// 在瑞典语中，“ä”在“z”之后排序
var list = [ "ä", "a", "z" ]
var l10nDE = new Intl.Collator("de")
var l10nSV = new Intl.Collator("sv")
l10nDE.compare("ä", "z") === -1
l10nSV.compare("ä", "z") === +1
console.log(list.sort(l10nDE.compare)) // [ "a", "ä", "z" ]
console.log(list.sort(l10nSV.compare)) // [ "a", "z", "ä" ]
```
```
// 在ES5中没有等价物
```

### 2.数字格式
- 使用数字分组和本地化分隔符格式化数字。
```
var l10nEN = new Intl.NumberFormat("en-US")
var l10nDE = new Intl.NumberFormat("de-DE")
l10nEN.format(1234567.89) === "1,234,567.89"
l10nDE.format(1234567.89) === "1.234.567,89"
```
```
// 在ES5中没有等价物
```

### 3.货币格式
- 使用数字分组格式化数字，本地化分隔符和附加的货币符号。
```
var l10nUSD = new Intl.NumberFormat("en-US", { style: "currency", currency: "USD" })
var l10nGBP = new Intl.NumberFormat("en-GB", { style: "currency", currency: "GBP" })
var l10nEUR = new Intl.NumberFormat("de-DE", { style: "currency", currency: "EUR" })
l10nUSD.format(100200300.40) === "$100,200,300.40"
l10nGBP.format(100200300.40) === "£100,200,300.40"
l10nEUR.format(100200300.40) === "100.200.300,40 €"
```
```
// 在ES5中没有等价物
```

### 4.日期/时间格式
- 使用本地化排序和分隔符格式化日期/时间。
```
var l10nEN = new Intl.DateTimeFormat("en-US")
var l10nDE = new Intl.DateTimeFormat("de-DE")
l10nEN.format(new Date("2015-01-02")) === "1/2/2015"
l10nDE.format(new Date("2015-01-02")) === "2.1.2015"
```
```
// 在ES5中没有等价物
```