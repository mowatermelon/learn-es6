
# 前置知识了解

## 块级作用域

### 为什么需要？

ES5 只有`全局作用域`和`函数作用域`，没有`块级作用域`，这带来很多不合理的场景。

> 第一种场景

内层变量可能会覆盖外层变量。

```javascript
var tmp = new Date();

function f() {
  console.log(tmp);
  if (false) {
    var tmp = 'hello world';
  }
}

f(); // undefined
```

上面代码的原意是，`if`代码块的外部使用外层的`tmp`变量，内部使用内层的`tmp`变量。

但是，函数`f`执行后，输出结果为`undefined`，原因在于变量提升，导致内层的`tmp`变量覆盖了外层的`tmp`变量。

  ---

> 第二种场景

用来计数的循环变量泄露为全局变量。

```javascript
var s = 'hello';

for (var i = 0; i < s.length; i++) {
  console.log(s[i]);
}

console.log(i); // 5
```

上面代码中，变量`i`只用来控制循环，但是循环结束后，它并没有消失，泄露成了全局变量。

  ---

### ES6 的块级作用域

`let`实际上为 `JavaScript` 新增了`块级作用域`。

```javascript

function f1() {
  let n = 5;
  if (true) {
    let n = 10;
  }
  console.log(n); // 5
}
```

上面的函数有两个代码块，都声明了变量`n`，运行后输出 `5`。这表示`外层代码块`不受`内层代码块`的影响。

如果两次都使用`var`定义变量`n`，最后输出的值才是 `10`。

> ES6 允许块级作用域的任意嵌套

上面代码使用了一个五层的块级作用域。

```javascript

{{{{{let insane = 'Hello World'}}}}};
```

> `外层作用域`无法读取`内层作用域`的变量。

```javascript

{{{{
  {let insane = 'Hello World'}
  console.log(insane); // 报错
}}}};
```

> 内层作用域可以定义外层作用域的同名变量

```javascript

{{{{
  let insane = 'Hello World';
  {let insane = 'Hello World'}
}}}};
```

> `块级作用域`的出现，实际上使得获得广泛应用的立即执行`函数表达式（IIFE）`不再必要了。

```javascript

// IIFE 写法
(function () {
  var tmp = ...;
  ...
}());

// 块级作用域写法
{
  let tmp = ...;
  ...
}
```

  ---

### 与函数声明相关

> ES5中函数声明

`函数`能不能在`块级作用域`之中声明？这是一个相当令人混淆的问题。

`ES5` 规定，函数只能在`顶层作用域`和`函数作用域`之中声明，不能在`块级作用域`声明。

```javascript

// 情况一
if (true) {
  function f() {}
}

// 情况二
try {
  function f() {}
} catch(e) {
  // ...
}
```

上面两种函数声明，根据 `ES5` 的规定都是非法的。

但是，浏览器没有遵守这个规定，为了兼容以前的旧代码，还是支持在`块级作用域`之中声明`函数`，因此上面两种情况实际都能运行，不会报错。

```javascript

function f() { console.log('I am outside!'); }

(function () {
  if (false) {
    // 重复声明一次函数f
    function f() { console.log('I am inside!'); }
  }

  f();
}());
```

上面代码在 `ES5` 中运行，会得到`I am inside!`，因为在`if`内声明的函数`f`会被提升到`函数头部`，实际运行的代码如下。

```javascript

// ES5 环境
function f() { console.log('I am outside!'); }

(function () {
  function f() { console.log('I am inside!'); }
  if (false) {
  }
  f();
}());
```

  ---

> ES6中函数声明

`ES6` 引入了`块级作用域`，明确允许在块级作用域之中声明函数。`ES6` 规定，`块级作用域`之中，`函数声明`语句的行为类似于`let`，在`块级作用域`之外不可引用。

```javascript

// 浏览器的 ES6 环境
function f() { console.log('I am outside!'); }

(function () {
  if (false) {
    // 重复声明一次函数f
    function f() { console.log('I am inside!'); }
  }

  f();
}());
// Uncaught TypeError: f is not a function
```

符合 `ES6` 的浏览器中运行，理论上会得到`I am outside!`。因为`块级作用域`内声明的函数类似于`let`，对作用域之外没有影响。

但是，如果你真的在 `ES6` 浏览器中运行一下上面的代码，是会报错的，这是为什么呢？

因为实际运行的是下面的代码。

```javascript

// 浏览器的 ES6 环境
function f() { console.log('I am outside!'); }
(function () {
  var f = undefined;
  if (false) {
    function f() { console.log('I am inside!'); }
  }

  f();
}());
// Uncaught TypeError: f is not a function
```

原来，如果改变了`块级作用域`内声明的函数的`处理规则`，显然会对`老代码`产生很大影响。

为了减轻因此产生的不兼容问题，`ES6` 在`附录 B`里面规定，浏览器的实现可以不遵守上面的规定，有自己的`行为方式`。

  ---

> ES6中在块级作用域内声明函数规则

- 允许在块级作用域内声明函数。
- `函数声明`类似于`var`，即会提升到`全局作用域`或`函数作用域`的头部。
- 同时，`函数声明`还会提升到所在的`块级作用域`的`头部`。

注意，上面三条规则只对 `ES6` 的浏览器实现有效，其他环境的实现不用遵守，还是将`块级作用域`的`函数声明`当作`let`处理。

根据这三条规则，在浏览器的 `ES6` 环境中，`块级作用域`内声明的函数，行为类似于`var`声明的变量。

考虑到环境导致的行为差异太大，应该避免在`块级作用域`内`声明函数`。如果确实需要，也应该写成`函数表达式`，而不是`函数声明语句`。

  ---

> 块级作用域中函数声明需要使用大括号

`ES6` 的`块级作用域`允许`声明函数`的规则，只在使用`大括号`的情况下成立，如果没有使用`大括号`，就会报错。

```javascript
// 不报错
'use strict';
if (true) {
  function f() {}
}

// 报错
'use strict';
if (true)
  function f() {}
```

---

## 作用域提升

我们知道`ES6`之前没有`块级作用域`，只有`全局作用域`和`函数作用域`。

`JS`在执行脚本之前会先`解析代码`，在`解析`的时候会创建一个全局执行上下文，并将其中的`变量`、`函数`都先拿出来，并给它们提前在`内存`中开辟好空间，变量暂时赋值为`undefined`，`函数`则会提前声明，整个存储在内存中，这一步做完了再正式`执行`程序。

函数在`执行`的时候同理，也会先`解析`代码，创建一个函数执行`上下文`，将其中的`变量`、`函数`提前准备好。

```javascript
console.log(a); // undefined
var a = 1;
test(); // test is running
function test(){
  console.log('test is running')
}
b=2;
```

所以，当执行`console.log(a)`的时候，`JS解析器`已经提前把`a`定义好并赋值为`undefined`。可以在函数定义前就调用。

### 变量提升

我们在使用`变量`或`函数`的时候，理解什么时候`被初始化值`的是至关重要。

`变量提升`是指在`声明`一个`变量`之前就使用了`变量`，在`全局作用域`中，只有使用`var`关键字`声明`的`变量`才会`变量提升`，`变量提升`的时候`浏览器`只知道有这么一个`变量`。

但你下面定义的值还没有`赋值`给这个`变量`，这时候·的值是`undefined`的，等到浏览器执行到下面的代码的时候才是一个`赋值`的过程。

所以`变量提升`的时候没有初始化值。用`var`声明`变量`的时候会给`window`增加一个相同`变量名`的`属性`，所以你也可以通过`属性名`的方式获取这个`变量`的值，当没有使用任何关键字`声明`时，只是给一个`变量`赋值时，`变量`也相当于给`window`增加一个相同`变量名`的`属性`。

### 函数提升

定义一个函数可以使用`函数声明`和`函数表达式`，这两种方式在提升的时候也是有区别的，`函数声明`会提升到`作用域`的`顶部`，在`提升`的时候会分配一个`内存空间`，`变量`指向这个函数的`内存空间`。

所以在定义一个`函数`之前是可以执行这个`函数`的，`函数声明`的方式定义函数会提升。而`函数表达式`就跟`变量提升`，仅仅只是`声明`，并没有给其`赋值`。

```javascript

// 函数声明语句
{
  let a = 'secret';
  function f() {
    return a;
  }
}

// 函数表达式
{
  let a = 'secret';
  let f = function () {
    return a;
  };
}
```

---

## 暂时性死区

`暂时性死区`（temporal dead zone，简称 TDZ），`ES6` 明确规定，如果区块中存在`let`和`const`命令，这个`区块`对这些命令声明的`变量`，从一开始就形成了`封闭作用域`。凡是在`声明`之前就`使用`这些变量，就会报错。

`暂时性死区`的本质就是，只要一进入`当前作用域`，所要使用的`变量`就已经存在了，但是`不可获取`，只有等到`声明变`量的那一行代码出现，才可以`获取`和`使用`该`变量`。

`ES6` 规定`暂时性死区`和`let`、`const`语句不出现`变量提升`，主要是为了减少运行时错误，防止在变量声明前就使用这个变量，从而导致意料之外的行为。

这样的错误在 `ES5` 是很常见的，现在有了这种规定，避免此类错误就很容易了。

## ES6 声明变量

`ES5` 只有两种声明变量的方法：`var`命令和`function`命令。

`ES6` 除了添加`let`和`const`命令，后面章节还会提到，另外两种声明变量的方法：`import`命令和`class`命令。

所以，`ES6` 一共有 6 种声明变量的方法。

---

## 顶层对象

`ES5` 的`顶层对象`，本身也是一个问题，因为它在各种实现里面是不统一的。

浏览器里面，顶层对象是`window`，但 `Node` 和 `Web Worker` 没有`window`。

浏览器和 `Web Worker` 里面，`self`也指向顶层对象，但是 `Node` 没有`self`。

`Node` 里面，顶层对象是`global`，但其他环境都不支持。

同一段代码为了能够在`各种环境`，都能取到`顶层对象`，现在一般是使用`this`变量，但是有局限性。

`全局环境`中，`this`会返回`顶层对象`。但是，`Node` 模块和 `ES6` 模块中，`this`返回的是`当前模块`。

函数里面的`this`，如果函数不是作为`对象`的方法`运行`，而是单纯作为`函数`运行，`this`会指向`顶层对象`。但是，`严格模式`下，这时`this`会返回`undefined`。

不管是`严格模式`，还是`普通模式`，`new Function('return this')()`，总是会返回`全局对象`。

但是，如果浏览器用了 `CSP`（`Content Security Policy`，`内容安全策略`），那么`eval`、`new Function`这些方法都可能无法使用。

综上所述，很难找到一种方法，可以在所有情况下，都取到`顶层对象`。下面是两种勉强可以使用的方法。

```javascript
// 方法一
(typeof window !== 'undefined'
   ? window
   : (typeof process === 'object' &&
      typeof require === 'function' &&
      typeof global === 'object')
     ? global
     : this);

// 方法二
var getGlobal = function () {
  if (typeof self !== 'undefined') { return self; }
  if (typeof window !== 'undefined') { return window; }
  if (typeof global !== 'undefined') { return global; }
  throw new Error('unable to locate global object');
};
```

现在有一个`提案`，在语言标准的层面，引入`global`作为`顶层对象`。也就是说，在所有环境下，`global`都是存在的，都可以从它拿到`顶层对象`。

垫片库`system.global`模拟了这个`提案`，可以在所有环境拿到`global`。

```javascript
// CommonJS 的写法
require('system.global/shim')();

// ES6 模块的写法
import shim from 'system.global/shim'; shim();
```

上面代码可以保证各种环境里面，`global`对象都是存在的。

```javascript

// CommonJS 的写法
var global = require('system.global')();

// ES6 模块的写法
import getGlobal from 'system.global';
const global = getGlobal();
```

上面代码将顶层对象放入变量`global`。

  ---

## 顶层对象的属性

顶层对象，在浏览器环境指的是`window`对象，在 `Node` 指的是`global`对象。`ES5` 之中，`顶层对象`的属性与`全局变量`是等价的。

```javascript
window.a = 1;
a // 1

a = 2;
window.a // 2
```

上面代码中，`顶层对象`的`属性`赋值与`全局变量`的`赋值`，是同一件事。

`顶层对象`的属性与`全局变量挂`钩，被认为是 `JavaScript` 语言最大的设计败笔之一。

这样的设计带来了几个很大的问题

- 首先是没法在编译时就报出变量未声明的错误，只有运行时才能知道（因为全局变量可能是顶层对象的属性创造的，而属性的创造是动态的）；

- 其次，程序员很容易不知不觉地就创建了全局变量（比如打字出错）；最后，顶层对象的属性是到处可以读写的，这非常不利于模块化编程。

- 另一方面，window对象有实体含义，指的是浏览器的窗口对象，顶层对象是一个有实体含义的对象，也是不合适的。

`ES6` 为了改变这一点，一方面规定，为了保持兼容性，`var命令`和`function命令`声明的`全局变量`，依旧是`顶层对象`的`属性`；

另一方面规定，`let命令`、`const命令`、`class命令`声明的`全局变量`，不属于`顶层对象`的`属性`。

也就是说，从 `ES6` 开始，`全局变量`将逐步与`顶层对象`的`属性`脱钩。

```javascript
var a = 1;
// 如果在 Node 的 REPL 环境，可以写成 global.a
// 或者采用通用方法，写成 this.a
window.a // 1

let b = 1;
window.b // undefined
```

上面代码中，全局变量`a`由`var`命令声明，所以它是`顶层对象`的`属性`；

全局变量`b`由`let命令`声明，所以它不是`顶层对象`的`属性`，返回`undefined`。

---
