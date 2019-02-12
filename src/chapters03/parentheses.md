# 圆括号问题

`解构赋值`虽然很方便，但是`解析`起来并不容易。

对于`编译器`来说，一个`式子`到底是`模式`，还是`表达式`，没有办法从一开始就知道，必须`解析`到（或解析不到）`等号`才能知道。

由此带来的问题是，如果模式中出现`圆括号`怎么处理。`ES6` 的规则是，只要有可能导致`解构`的`歧义`，就不得使用`圆括号`。

但是，这条规则实际上不那么容易`辨别`，处理起来相当麻烦。因此，建议只要有可能，就不要在`模式`中放置`圆括号`。

## 避免使用情况

不能使用圆括号的情况

以下三种解构赋值不得使用圆括号。

### 变量声明语句

```javascript
(function (log) {
    // 全部报错
    const [(a)] = [1];// SyntaxError: Unexpected token (

    const {x: (c)} = {};// SyntaxError: Unexpected token (
    const ({x: c}) = {};// SyntaxError: Unexpected token (
    const {(x: c)} = {};// SyntaxError: Unexpected token (
    const {(x): c} = {};// SyntaxError: Unexpected token (

    const { o: ({ p: p }) } = { o: { p: 2 } };// SyntaxError: Unexpected token (
})(console.log)
```

上面 6 个语句都会报错，因为它们都是`变量`声明语句，`模式`不能使用`圆括号`。

---

### 函数参数

`函数参数`也属于`变量声明`，因此不能带有`圆括号`。

```javascript
// 报错
function f([(z)]) { return z; }// SyntaxError: Unexpected token (
// 报错
function f([z,(x)]) { return x; }// SyntaxError: Unexpected token (
```

---

### 赋值语句的模式

```javascript
(function (log) {
    // 全部报错
    ({ p: a }) = { p: 42 };// SyntaxError: Unexpected token (
    ([a]) = [5];// SyntaxError: Unexpected token (
})(console.log)
```

上面代码将整个`模式`放在`圆括号`之中，导致报错。

```javascript
(function (log) {
    // 报错
    [({ p: a }), { x: c }] = [{}, {}];// SyntaxError: Unexpected token (
})(console.log)
```

上面代码将一部分`模式`放在`圆括号`之中，导致报错。

---

## 可以使用情况

可以使用`圆括号`的情况只有一种：`赋值语句`的`非模式`部分，可以使用`圆括号`。

```javascript
(function (log) {
    [(b)] = [3]; // 正确
    log(b);// 3
    ({ p: (d) } = {}); // 正确
    log(d);// undefined
    [(parseInt.prop)] = [3]; // 正确
    log(parseInt.prop);// 3
})(console.log)
```

上面三行语句都可以正确执行，因为首先它们都是`赋值`语句，而不是`声明`语句；

其次它们的`圆括号`都不属于`模式`的一部分。

第一行`语句`中，`模式`是取`数组`的第一个成员，跟`圆括号`无关；

第二行`语句`中，模式是`p`，而不是`d`；

第三行`语句`与第一行`语句`的`性质`一致。

`解构赋值`允许等号左边的`模式`之中，不放置任何`变量名`。因此，可以写出`非常古怪`的`赋值表达式`。

```javascript
({} = [true, false]);
({} = 'abc');
({} = []);
```

上面的`表达式`虽然`毫无意义`，但是语法是`合法`的，可以执行。

---
