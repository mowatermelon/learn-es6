# let 命令

## 基本用法

### 基础说明

`ES6` 新增了`let`命令，用来声明变量。它的用法类似于`var`，但是所声明的变量，只在`let`命令所在的代码块内有效。

```javascript
{
  let a = 10;
  var b = 1;
}

a // ReferenceError: a is not defined.
b // 1
```

上面`代码`在`代码块`之中，分别用`let`和`var`声明了两个`变量`。然后在`代码块`之外调用这两个变量，结果`let`声明的`变量`报错，`var`声明的变量返回了正确的值。这表明，`let`声明的`变量`只在它所在的`代码块`有效。

  ---

### for循环使用

`for`循环的计数器，就很合适使用`let`命令，可以减少`全局变量`的`污染`和`变量作用域`的`错误访问`。

```javascript
for (let i = 0; i < 10; i++) {
  // ...
}

console.log(i);
// ReferenceError: i is not defined
```

上面代码中，计数器`i`只在`for`循环体内有效，在`循环体`外引用就会报错。

  ---

> var和let在for循环中使用对比

下面的代码如果使用`var`，最后输出的是`10`。

```javascript
var a = [];
for (var i = 0; i < 10; i++) {
  a[i] = function () {
    console.log(i);
  };
}
a[6](); // 10
```

上面代码中，变量`i`是`var`命令声明的，在全局范围内都有效，所以全局只有一个变量`i`。

每一次循环，变量`i`的值都会发生改变，而循环内被赋给数组`a`的函数内部的`console.log(i)`，里面的`i`指向的就是全局的`i`。

也就是说，所有数组`a`的成员里面的`i`，指向的都是同一个`i`，导致运行时输出的是最后一轮的`i`的值，也就是 `10`。

如果使用`let`，声明的变量仅在`块级作用域`内有效，最后输出的是 `6`。

```javascript
var a = [];
for (let i = 0; i < 10; i++) {
  a[i] = function () {
    console.log(i);
  };
}
a[6](); // 6
```

上面代码中，变量`i`是`let`声明的，当前的`i`只在本轮循环有效，所以每一次循环的`i`其实都是一个新的变量，所以最后输出的是`6`。你可能会问，如果每一轮循环的变量`i`都是重新声明的，那它怎么知道上一轮循环的值，从而计算出本轮循环的值？这是因为 `JavaScript` 引擎内部会记住上一轮循环的值，初始化本轮的变量`i`时，就在上一轮循环的基础上进行计算。

  ---

> for循环单独作用域

`for循环`还有一个特别之处，就是设置`循环变量`的那部分是一个`父作用域`，而循环体`内部`是一个单独的`子作用域`。

```javascript
for (let i = 0; i < 3; i++) {
  let i = 'abc';
  console.log(i);
}
// abc
// abc
// abc
```

上面代码正确运行，输出了 `3` 次`abc`。这表明`函数内部`的变量`i`与循环变量`i`不在同一个`作用域`，有各自单独的`作用域`。

---

## 注意事项

### 不存在变量提升

`var`命令会发生`变量提升`现象，即变量可以在声明之前使用，值为`undefined`。

这种现象多多少少是有些奇怪的，按照一般的逻辑，`变量`应该在`声明`语句之后才可以`使用`。

为了纠正这种现象，`let`命令改变了语法行为，它所`声明`的变量一定要在`声明`后`使用`，否则报错。

```javascript

// var 的情况
console.log(foo); // 输出undefined
var foo = 2;

// let 的情况
console.log(bar); // 报错ReferenceError
let bar = 2;
```

上面代码中，变量`foo`用`var`命令声明，会发生变量提升，即脚本开始运行时，变量`foo`已经存在了，但是没有值，所以会输出`undefined`。

变量`bar`用`let`命令声明，不会发生变量提升。这表示在声明它之前，变量`bar`是不存在的，这时如果用到它，就会抛出一个错误。

---

### 暂时性死区问题

> 基础案例

只要块级作用域内存在`let`命令，它所声明的变量就`绑定`（`binding`）这个区域，不再受`外部`的影响。

```javascript
var tmp = 123;

if (true) {
  tmp = 'abc'; // ReferenceError
  let tmp;
}
```

上面代码中，存在全局变量`tmp`，但是块级作用域内`let`又声明了一个局部变量`tmp`，导致后者绑定这个`块级作用域`，所以在`let`声明变量前，对`tmp`赋值会报错。

总之，在代码块内，使用`let`命令声明变量之前，该变量都是不可用的。

```javascript
if (true) {
  // TDZ开始
  tmp = 'abc'; // ReferenceError
  console.log(tmp); // ReferenceError

  let tmp; // TDZ结束
  console.log(tmp); // undefined

  tmp = 123;
  console.log(tmp); // 123
}
```

上面代码中，在`let`命令声明变量`tmp`之前，都属于变量`tmp`的`死区`。

  ---

> ES6中`typeof`的不安全性

`暂时性死区`也意味着`typeof`不再是一个百分之百安全的操作。

```javascript
typeof x; // ReferenceError
let x;
```

上面代码中，变量`x`使用`let`命令声明，所以在声明之前，都属于`x`的`死区`，只要用到该变量就会报错。因此，`typeof`运行时就会抛出一个`ReferenceError`。

作为比较，如果一个`变量`根本没有被`声明`，使用`typeof`反而不会报错。

```javascript
typeof undeclared_variable // "undefined"
```

上面代码中，`undeclared_variable`是一个不存在的变量名，结果返回`undefined`。

所以，在没有`let`之前，`typeof`运算符是百分之百安全的，永远不会报错。

现在这一点不成立了。这样的设计是为了让大家养成良好的编程习惯，变量一定要在`声明`之后`使用`，否则就报错。

  ---

> ES6中隐蔽死区

有些`死区`比较隐蔽，不太容易发现。

```javascript
function bar(x = y, y = 2) {
  return [x, y];
}

bar(); // 报错
// Uncaught ReferenceError: y is not defined
```

上面代码中，调用`bar`函数之所以报错（某些实现可能不报错），是因为参数`x`默认值等于另一个参数`y`，而此时`y`还没有声明，属于`死区`。

如果`y`的默认值是`x`，就不会报错，因为此时`x`已经声明了。

```javascript
function bar(x = 2, y = x) {
  return [x, y];
}
bar(); // [2, 2]
```

另外，下面的代码也会报错，与`var`的行为不同。

```javascript
// 不报错
var x = x;

// 报错
let x = x;
// ReferenceError: x is not defined
```

上面代码报错，也是因为`暂时性死区`。使用`let`声明变量时，只要变量在还没有声明完成前使用，就会报错。

上面这行就属于这个情况，在变量`x`的声明语句还没有执行完成前，就去取`x`的值，导致报错`x 未定义`。

---

### 不允许重复声明

`let`不允许在`相同作用域`内，重复声明同一个变量。

```javascript
// 报错
function func() {
  let a = 10;
  var a = 1;
}

// 报错
function func() {
  let a = 10;
  let a = 1;
}
```

因此，不能在函数内部重新声明参数。

```javascript
function func(arg) {
  let arg; // 报错
}

function func(arg) {
  {
    let arg; // 不报错
  }
}
```

---
