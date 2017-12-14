# ES6代码规范

<p align="center">
  <img src="http://coenraets.org/present/es6/img/js.png"/>
</p>

## 目标

本规范提供了一种统一的编码规范来编写ES6代码；

## 目录
* [语句](#语句)
* [引用](#引用)
* [对象](#对象)
* [数组](#数组)
* [函数](#函数)
* [箭头函数](#箭头函数)
* [构造器](#构造器)
* [模块](#模块)
* [变量](#变量)
* [提升](#提升)
* [比较运算符](#比较运算符)
* [注释](#注释)
* [空白](#空白)

## 语句
### 所有语句完成后要用分号进行结束
提升代码可读性
```html
//bad
import ElementUI from 'element-ui'

let a = 2
let b = 3

//good
import ElementUI from 'element-ui';

let a = 2;
let b = 3;

```

[↑ 回到目录](#目录)

## 引用

### 对所有的引用使用 const ，不要使用 var？

```html
// bad
var a = 1;
var b = 2;

// good
const a = 1;
const b = 2;
```

### 如果一定需要可变动的引用，使用 let 代替 var。
因为 let 是块级作用域，而 var 是函数作用域。
```html
// bad
var count = 1;
if (true) {
  count += 1;
}

// good, use the let.
let count = 1;
if (true) {
  count += 1;
}
```

[↑ 回到目录](#目录)

## 对象

### 使用字面值创建对象。

```html
// bad
const item = new Object();

// good
const item = {};
```

### 使用对象方法的简写。
```html
// bad
const atom = {
  value: 1,

  addValue: function (value) {
    return atom.value + value;
  },
};

// good
const atom = {
  value: 1,

  addValue(value) {
    return atom.value + value;
  },
};
```
### 使用对象属性值的简写。
这样更短更有描述性。

```html
const lukeSkywalker = 'Luke Skywalker';

// bad
const obj = {
  lukeSkywalker: lukeSkywalker,
};

// good
const obj = {
  lukeSkywalker,
};
```
### 不要直接调用 Object.prototype 的方法，如：hasOwnProperty, propertyIsEnumerable, 和 isPrototypeOf

```html
// bad
console.log(object.hasOwnProperty(key));

// good
console.log(Object.prototype.hasOwnProperty.call(object, key));

// best
const has = Object.prototype.hasOwnProperty; // cache the lookup once, in module scope.
/* or */
const has = require('has');
…
console.log(has.call(object, key));
```

### 浅拷贝对象的时候最好是使用 … 操作符而不是 Object.assign

```html
// very bad
const original = { a: 1, b: 2 };
const copy = Object.assign(original, { c: 3 }); // this mutates `original`
delete copy.a; // so does this

// bad
const original = { a: 1, b: 2 };
const copy = Object.assign({}, original, { c: 3 }); // copy => { a: 1, b: 2, c: 3 }

// good
const original = { a: 1, b: 2 };
const copy = { ...original, c: 3 }; // copy => { a: 1, b: 2, c: 3 }

const { a, ...noA } = copy; // noA => { b: 2, c: 3 }
```

[↑ 回到目录](#目录)

## 数组

虽然 Vue.js 支持传递复杂的 JavaScript 对象通过 props 属性，但是你应该尽可能的使用原始类型的数据。尽量只使用 [JavaScript 原始类型](https://developer.mozilla.org/en-US/docs/Glossary/Primitive)（字符串、数字、布尔值）和函数。尽量避免复杂的对象。

### 使用字面值创建数组

```html
// bad
const items = new Array();

// good
const items = [];
```

### 使用拓展运算符 … 复制数组。

```html
// bad
const items = new Array();

// good
const items = [];

// bad
const len = items.length;
const itemsCopy = [];
let i;

for (i = 0; i < len; i++) {
  itemsCopy[i] = items[i];
}

// good
const itemsCopy = [...items]
```
### 使用 Array#from 把一个类数组对象转换成数组

```html
const foo = document.querySelectorAll('.foo');
const nodes = Array.from(foo);
```

[↑ 回到目录](#目录)

## 函数

### 使用函数声明代替函数表达式

为什么？因为函数声明是可命名的，所以他们在调用栈中更容易被识别。此外，函数声明会把整个函数提升（hoisted），而函数表达式只会把函数的引用变量名提升。这条规则使得箭头函数可以取代函数表达式。

```html
// bad
const foo = function () {
};

// good
function foo() {
}
```
### 函数表达式

```html
// 立即调用的函数表达式 (IIFE)
(() => {
   console.log('Welcome to the Internet. Please follow me.');
})();
```

### 永远不要在一个非函数代码块（if、while 等）中声明一个函数，把那个函数赋给一个变量。浏览器允许你这么做，但它们的解析表现不一致
```html
// bad
if (currentUser) {
  function test() {
    console.log('Nope.');
  }
}

// good
let test;
if (currentUser) {
  test = () => {
    console.log('Yup.');
  };
}
```

### 不要使用 arguments。可以选择 rest 语法 … 替代

为什么？使用 … 能明确你要传入的参数。另外 rest 参数是一个真正的数组，而 arguments 是一个类数组。

```html
// bad
function concatenateAll() {
  const args = Array.prototype.slice.call(arguments);
  return args.join('');
}

// good
function concatenateAll(...args) {
  return args.join('');
}
```
[↑ 回到目录](#目录)

## 箭头函数

按照一定的结构组织，使得组件便于理解。

### 当你必须使用函数表达式（或传递一个匿名函数）时，使用箭头函数符号。

为什么?因为箭头函数创造了新的一个 this 执行环境（译注：参考 Arrow functions - JavaScript | MDN 和 ES6 arrow functions, syntax and lexical scoping），通常情况下都能满足你的需求，而且这样的写法更为简洁。

为什么不？如果你有一个相当复杂的函数，你或许可以把逻辑部分转移到一个函数声明上。

```html
// bad
[1, 2, 3].map(function (x) {
  return x * x;
});

// good
[1, 2, 3].map((x) => {
  return x * x;
});
```
### 如果一个函数适合用一行写出并且只有一个参数，那就把花括号、圆括号和 return 都省略掉。如果不是，那就不要省略

为什么？语法糖。在链式调用中可读性很高。 
为什么不？当你打算回传一个对象的时候。

```html
 // good
  [1, 2, 3].map(x => x * x);

  // good
  [1, 2, 3].reduce((total, n) => {
    return total + n;
  }, 0);
```

[↑ 回到目录](#目录)

## 构造器


### 总是使用 class。避免直接操作 prototype

为什么? 因为 class 语法更为简洁更易读。

```html
// bad
function Queue(contents = []) {
  this._queue = [...contents];
}
Queue.prototype.pop = function() {
  const value = this._queue[0];
  this._queue.splice(0, 1);
  return value;
}


// good
class Queue {
  constructor(contents = []) {
    this._queue = [...contents];
  }
  pop() {
    const value = this._queue[0];
    this._queue.splice(0, 1);
    return value;
  }
}
```
### 使用 extends 继承。

为什么？因为 extends 是一个内建的原型继承方法并且不会破坏 instanceof。

### 方法可以返回 this 来帮助链式调用。


[↑ 回到目录](#目录)

## 模块

### 总是使用模组 (import/export)

而不是其他非标准模块系统。你可以编译为你喜欢的模块系统。 
为什么？模块就是未来，让我们开始迈向未来吧。

### 不要使用通配符 import

为什么？这样能确保你只有一个默认 export。

```html
// bad
import * as AirbnbStyleGuide from './AirbnbStyleGuide';

// good
import AirbnbStyleGuide from './AirbnbStyleGuide';

```
### 不要从 import 中直接 export

为什么？虽然一行代码简洁明了，但让 import 和 export 各司其职让事情能保持一致。

```html
// bad
// filename es6.js
export { es6 as default } from './airbnbStyleGuide';

// good
// filename es6.js
import { es6 } from './AirbnbStyleGuide';
export default es6;
```

[↑ 回到目录](#目录)

## 变量

### 一直使用 const 来声明变量
如果不这样做就会产生全局变量。我们需要避免全局命名空间的污染。
```html
// bad
superPower = new SuperPower();

// good
const superPower = new SuperPower();
```

### 使用 const 声明每一个变量

为什么？增加新变量将变的更加容易，而且你永远不用再担心调换错。

### 将所有的 const 和 let 分组
为什么？当你需要把已赋值变量赋值给未赋值变量时非常有用。
```html
// bad
let i, len, dragonball,
    items = getItems(),
    goSportsTeam = true;

// bad
let i;
const items = getItems();
let dragonball;
const goSportsTeam = true;
let len;

// good
const goSportsTeam = true;
const items = getItems();
let dragonball;
let i;
let length;
```
### 在你需要的地方给变量赋值，但请把它们放在一个合理的位置
为什么？let 和 const 是块级作用域而不是函数作用域。

[↑ 回到目录](#目录)

## 提升


### var 声明会被提升至该作用域的顶部，但它们赋值不会提升。

let 和 const 被赋予了一种称为「暂时性死区（Temporal Dead Zones, TDZ）」的概念。这对于了解为什么 type of 不再安全相当重要。

### 匿名函数表达式的变量名会被提升，但函数内容并不会。

### 命名的函数表达式的变量名会被提升，但函数名和函数函数内容并不会。

### 函数声明的名称和函数体都会被提升。


[↑ 回到目录](#目录)

## 比较运算符

## 优先使用 === 和 !== 而不是 == 和 !=

### 条件表达式例如 if 语句通过抽象方法 ToBoolean 强制计算它们的表达式并且总是遵守下面的规则：

.对象 被计算为 true 
.Undefined 被计算为 false 
.Null 被计算为 false 
.布尔值 被计算为 布尔的值 
.数字 如果是 +0、-0、或 NaN 被计算为 false, 否则为 true 
.字符串 如果是空字符串 ” 被计算为 false，否则为 true

# Range slider

## 注释

### 使用 /* … / 作为多行注释。包含描述、指定所有参数和返回值的类型和值

```html
// bad
// make() returns a new element
// based on the passed in tag name
//
// @param {String} tag
// @return {Element} element
function make(tag) {

  // ...stuff...

  return element;
}

// good
/**
 * make() returns a new element
 * based on the passed in tag name
 *
 * @param {String} tag
 * @return {Element} element
 */
function make(tag) {

  // ...stuff...

  return element;
}
```

### 使用 // 作为单行注释。在注释对象上面另起一行使用单行注释。在注释前插入空行。

### 给注释增加 FIXME 或 TODO 的前缀
帮助其他开发者快速了解这是一个需要复查的问题，或是给需要实现的功能提供一个解决方式。这将有别于常见的注释，因为它们是可操作的。使用 FIXME – need to figure this out 或者 TODO – need to implement。

### 使用 // FIXME: 标注问题。
```html
class Calculator {
  constructor() {
    // FIXME: shouldn't use a global here
    total = 0;
  }
}
```

### 使用 // TODO: 标注问题的解决方式。

```html
class Calculator {
  constructor() {
    // TODO: total should be configurable by an options param
    this.total = 0;
  }
}
```

## 空白

### 使用 2 个空格作为缩进。

### 在花括号前要放一个空格。

### 在控制语句（if、while 等）的小括号前放一个空格。

在函数调用及声明中，不在函数的参数列表前加空格。

### 在文件末尾插入一个空行。

### 在使用长方法链时进行缩进。使用放置在前面的点 . 强调这是方法调用而不是新语句
