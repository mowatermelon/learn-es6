# Number 原型对象方法扩展

## [toExponential()]

### 基础说明

`numObj.toExponential(fractionDigits)`

`Number.prototype.toExponential()` 方法以`指数`表示法返回该`数值字符串`表示形式。

对`数值字面量`使用 `toExponential()` 方法，且该数值没有`小数点`和`指数`时，应该在该`数值`与该方法之间隔开一个`空格`，以避免`点号`被解释为一个`小数点`。

也可以使用两个`点号`调用该方法。

---

### 参数说明

`fractionDigits` 是一个可选参数。一个整数，用来指定小数点后有几位数字。

默认情况下用尽可能多的位数来显示`数字`。

如果该`参数`是一个`非整数值`，将会向下舍入到最接近的`整数`。

如果 `fractionDigits` 参数被忽略了，`小数点`后的将尽可能用最多`的`位数`来表示该数值。

---

### 返回值说明

一个用`幂`的形式 (`科学记数法`) 来表示`Number` 对象的字符串。

`小数点`后以`fractionDigits` 提供的值来`四舍五入`。

如果一个数值的`小数位数`多于 `fractionDigits` 参数所提供的，则该数值将会在 `fractionDigits` 指定的小数位数处`四舍五入`。

查看 [Number.prototype.toFixed()][toFixed()] 方法关于`四舍五入`的讨论，同样应用于 `toExponential()` 方法。

---

### 注意事项

#### RangeError

如果 `fractionDigits` 太小或太大将会抛出该错误。介于 `1` 和 `100` （包括边界值）之间的值不会引起 `RangeError` 。

执行环境也可以支持`更大`或`更小`范围。

---

#### TypeError

如果该方法在一个`非数值类型`对象上调用。

---

### 举例

```javascript
(() => {
    const { log } = console;
    const numObj = 77.1234;

    log('numObj.toExponential() is ' + numObj.toExponential()); //numObj.toExponential() is 7.71234e+1

    log('numObj.toExponential(4) is ' + numObj.toExponential(4)); //numObj.toExponential(4) is 7.7123e+1

    log('numObj.toExponential(2) is ' + numObj.toExponential(2)); //numObj.toExponential(2) is 7.71e+1

    log('77.1234.toExponential() is ' + 77.1234.toExponential()); //77.1234.toExponential() is 7.71234e+1

    log('77 .toExponential() is ' + 77 .toExponential()); //77 .toExponential() is 7.7e+1

    log('77 .toExponential(101) is ' + 77 .toExponential(101)); //Uncaught RangeError: toExponential() argument must be between 0 and 100
})()
```

---

## [Number.toPrecision()]

### 基础说明

`numObj.toPrecision(precision)`

`Number.prototype.toPrecision()`方法以指定的`精度`返回该`数值`对象的`字符串`表示。

对`数值字面量`使用 `toPrecision()` 方法，且该数值没有`小数点`和`指数`时，应该在该`数值`与该方法之间隔开一个`空格`，以避免`点号`被解释为一个`小数点`。

也可以使用两个`点号`调用该方法。

---

### 参数说明

`precision` 是一个可选参数。用来指定有效数`个数`的`整数`。

如果忽略 `precision` 参数，则该方法表现类似于 [Number.prototype.toString()][toString()]。

如果该`参数`是一个`非整数值`，将会向下舍入到最接近的`整数`。

---

### 返回值说明

以`定点表示法`或`指数表示法`表示的一个数值对象的`字符串`表示，`四舍五入`到 `precision` 参数指定的显示数字位数。

查看 [Number.prototype.toFixed()][toFixed()] 方法关于`四舍五入`的讨论，同样应用于 `toPrecision()` 方法。

---

### 注意事项

#### RangeError

如果 `precison` 参数不在 `1` 和 `100` （包括边界值）之间，将会抛出一个 `RangeError` 。

执行环境也可以支持`更大`或`更小`的范围。`ECMA-262` 只需要最多 `21` 位显示数字。

---

#### TypeError

如果该方法在一个`非数值类型`对象上调用。

---

### 举例

```javascript
(() => {
    const { log } = console;
    const numObj = 5.123456;
    log('numObj.toPrecision()  is ' + numObj.toPrecision());  // numObj.toPrecision()  is 5.123456
    log('numObj.toPrecision(5) is ' + numObj.toPrecision(5)); // numObj.toPrecision(5) is 5.1235
    log('numObj.toPrecision(2) is ' + numObj.toPrecision(2)); // numObj.toPrecision(2) is 5.1
    log('numObj.toPrecision(1) is ' + numObj.toPrecision(1)); // numObj.toPrecision(1) is 5

    // 注意：在某些情况下会以指数表示法返回
    log((1234.5).toPrecision(2)); // '1.2e+3'

    log(123 .toPrecision(101));// Uncaught RangeError: toPrecision() argument must be between 1 and 100
})()
```

---

[toExponential()]: https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Number/toExponential
[toPrecision()]: https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Number/toPrecision
[toFixed()]: https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Number/toFixed
[toString()]: https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Number/toString