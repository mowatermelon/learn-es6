# Number 对象方法扩展

## [Number.isFinite()]

### 基础说明

`Number.isFinite(value)`

`Number.isFinite()` 方法用来检测传入的参数是否是一个`有穷数`（`finite number`）。

传入的 `value` 参数是要被检测`有穷性`的`值`，方法会返回一个 `布尔值` 表示给定的值是否是一个`有穷数`。

和全局的 `isFinite()` 函数相比，这个方法不会强制将一个`非数值`的参数转换成`数值`。

这就意味着，只有`数值类型`的值，且是`有穷`的（`finite`），才返回 `true`， 其他情况一律返回`false`。

---

### 举例

```javascript
(() => {
    const { log } = console;
    log(Number.isFinite(Infinity));  // false
    log(Number.isFinite(NaN));       // false
    log(Number.isFinite(-Infinity)); // false

    log(Number.isFinite(0));         // true
    log(Number.isFinite(2e64));      // true

    log(isFinite('0'));              // true
    log(Number.isFinite('0'));       // false
})()
```

---

### Polyfill

```javascript
Number.isFinite = Number.isFinite || (value) => {
    return typeof value === 'number' && isFinite(value);
}
```

## [Number.isNaN()]

### 基础说明

`Number.isNaN(value)`

`Number.isNaN()`方法确定传递的值是否为 `NaN`和其类型是 `Number`。

它是原始的全局`isNaN()`的更强大的版本。

传入的 `value` 参数是要被检测是否是 `NaN` 的`值`，方法会返回一个 `布尔值` 表示给定的值是否是 `NaN` 的值。

在 `JavaScript` 中，`NaN` 最特殊的地方就是，我们不能使用相等运算符（`==` 和 `===`）来判断一个值是否是 `NaN`，因为 `NaN == NaN` 和 `NaN === NaN` 都会返回 `false`。因此，必须要有一个判断值是否是 `NaN` 的方法。

和全局函数 `isNaN()` 相比，这个方法不会强制将一个`非数值`的参数转换成`数值`。

这就意味着，只有`数值类型`的值，且值为 `NaN` 的时候才会返回 `true`，其他情况一律返回`false`。

---

### 举例

```javascript
(() => {
    const { log } = console;
    log(Number.isNaN(NaN));        // true
    log(Number.isNaN(Number.NaN)); // true
    log(Number.isNaN(0 / 0))       // true

    // 与全局的 isNaN() 返回值不同的案例。
    log(isNaN('NaN'));      // true，字符串 'NaN' 不会被隐式转换成数字 NaN。
    log(Number.isNaN('NaN'));      // false，字符串 'NaN' 不会被隐式转换成数字 NaN。

    log(isNaN(undefined));  // true
    log(Number.isNaN(undefined));  // false

    log(isNaN({}));         // true
    log(Number.isNaN({}));         // false

    log(isNaN('blabla'));   // true
    log(Number.isNaN('blabla'));   // false

    // 下面的都返回 false
    log(Number.isNaN(true));
    log(Number.isNaN(null));
    log(Number.isNaN(37));
    log(Number.isNaN('37'));
    log(Number.isNaN('37.37'));
    log(Number.isNaN(''));
    log(Number.isNaN(' '));
})()
```

---

### Polyfill

```javascript
Number.isNaN = Number.isNaN || (value) => {
    return typeof value === 'number' && isNaN(value);
}
```

## [Number.parseInt()]

### 基础说明

`Number.parseInt(string[, radix])`

`Number.parseInt()` 方法可以根据给定的`进制`数把一个`字符串`解析成`整数`，

`Number.parseInt()`  函数将其第一个参数转换为`字符串`，解析它，并返回一个`整数`或`NaN`。

如果不是`NaN`，返回的值将是作为`指定基数`（基数）中的数字的第一个参数的`整数`。

实现逻辑和全局方法[parseInt()]没有区别。

```javascript
(() => {
    const { log } = console;
    log(Number.parseInt === parseInt); // true
})()
```

方法返回解析后的`整数值`。 如果被解析参数的第一个`字符`无法被转化成`数值`类型，则返回 `NaN`。

注意：`radix`参数为`n` 将会把第一个参数看作是一个数的`n`进制表示，而返回的值则是`十进制`的。

```javascript
(() => {
    const { log } = console;
    const num1 = parseInt('123', 5);
    const num2 = Number.parseInt('123', 5);
    // 将'123'看作5进制数，返回十进制数38 => 1*5^2 + 2*5^1 + 3*5^0 = 38;
    log(num1); // 38
    log(num2); // 38
})()
```

如果`radix`参数为`10` 将会把第一个参数看作是一个数的`十进制`表示，`8` 对应`八进制`，`16` 对应`十六进制`，等等。

基数大于 `10` 时，用字母表中的字母来表示大于 `9` 的数字。例如`十六进制`中，使用 `A` 到 `F`。

---

### 参数说明

| 参数名 | 参数说明                                                                                                                                                                                                                                                               |
| :----- | :--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| string | 要被解析的值。如果参数不是一个字符串，则将其转换为字符串(使用  `ToString` 抽象操作)。字符串开头的空白符将会被忽略。                                                                                                                                                    |
| radix  | 一个介于`2`和`36`之间的`整数`(数学系统的基础)，表示上述字符串的基数。比如参数`10`，表示使用我们通常使用的`十进制`数值系统。<br> 始终指定此参数可以消除阅读该代码时的`困惑`并且保证转换结果`可预测`。当未指定`基数`时，不同的实现会产生不同的结果，通常将值默认为`10`。 |

---

### 注意事项

#### radix 错误

> 字符不是指定基数中的数字

如果`Number.parseInt`的`字符`不是`指定基数`中的`数字`，则忽略该`字符`和所有`后续字符`，并返回解析到该点的`整数值`。`Number.parseInt`将数字截断为`整数值`。

允许使用`前导空格`和`尾随空格`，即`开头`和`结尾`的`空白符`允许存在，会被忽略。

---

> 基数为undefined

在基数为 `undefined`，或者基数为 `0` 或者没有指定的情况下，`JavaScript` 作如下处理：

- 如果字符串 `string` 以`0x`或者`0X`开头, 则基数是`16` (`16进制`).
- 如果字符串 `string` 以`0b`或者`0B`开头, 则基数是`8`（`八进制`）.
- 如果字符串 `string` 以`0`开头, 基数是`8`（`八进制`）或者`10`（十进制），那么具体是哪个`基数`由`实现环境`决定。- `ECMAScript 5` 规定使用`10`，但是并不是所有的`浏览器`都遵循这个规定。因此，永远都要明确给出`radix`参数的值。
- 如果字符串 `string` 以其它任何值开头，则基数是`10` (十进制)。
- 如果第一个字符不能被转换成数字，`Number.parseInt()`返回`NaN`。

算术上， `NaN` 不是任何一个进制下的数。 你可以调用`Number.isNaN()` 来判断 `Number.parseInt()` 是否返回 `NaN`。

如果让`NaN`作为了任意`数学运算`的`操作数`,则`运算结果`必定也是`NaN`.。

---

> 整型数值转换

将`整型数值`以`特定基数`转换成它的`字符串值`可以使用 `intValue.toString(radix)`。

---

#### e字符 错误

一些数中可能包含e字符（例如`6.022e23`），使用`Number.parseInt`去截取包含`e字符`数值部分会造成难以预料的结果。

例如：

```javascript
(() => {
    const { log } = console;
    log(Number.parseInt('6.022e23', 10));        // 6
    log(Number.parseInt(6.022e2, 10)); // 602
})()
```

`Number.parseInt()`不应该用作 `Math.floor()`的替代品。

---

### 举例

#### 15

以下例子均返回`15`:

```javascript
(() => {
    const { log } = console;
    log(Number.parseInt === parseInt); // true
    log(Number.parseInt('0xF', 16));
    log(Number.parseInt('F', 16));
    log(Number.parseInt('17', 8));
    log(Number.parseInt(021, 8));
    log(Number.parseInt('015', 10));   // 注意这句话再严格模式下会报错
    log(Number.parseInt(15.99, 10));
    log(Number.parseInt('15,123', 10));
    log(Number.parseInt('FXX123', 16));
    log(Number.parseInt('1111', 2));
    log(Number.parseInt('15 * 3', 10));
    log(Number.parseInt('15e2', 10));
    log(Number.parseInt('15px', 10));
    log(Number.parseInt('12', 13));
})()
```

---

#### NaN

以下例子均返回 NaN:

```javascript
(() => {
    const { log } = console;
    log(Number.parseInt('Hello', 8)); // 根本就不是数值
    log(Number.parseInt('546', 2));   // 除了“0、1”外，其它数字都不是有效二进制数字
})()
```

---

#### -15

以下例子均返回 -15：

```javascript
(() => {
    const { log } = console;
    log(Number.parseInt('-F', 16));
    log(Number.parseInt('-0F', 16));
    log(Number.parseInt('-0XF', 16));
    log(Number.parseInt(-15.1, 10));
    log(Number.parseInt(' -17', 8));
    log(Number.parseInt(' -15', 10));
    log(Number.parseInt('-1111', 2));
    log(Number.parseInt('-15e1', 10));
    log(Number.parseInt('-12', 13));
})()
```

---

#### 更严格的转换函数

有时采用一个`更严格`的方法来`解析整型值`很有用。此时可以使用`正则表达式`

```javascript
(() => {
    const { log } = console;
    const filterInt = (value) => {
    if(/^(\-|\+)?([0-9]+|Infinity)$/.test(value))
        return Number(value);
    return NaN;
    }

    log(filterInt('421'));               // 421
    log(filterInt('-421'));              // -421
    log(filterInt('+421'));              // 421
    log(filterInt('Infinity'));          // Infinity
    log(filterInt('421e+0'));            // NaN
    log(filterInt('421hop'));            // NaN
    log(filterInt('hop1.61803398875'));  // NaN
    log(filterInt('1.61803398875'));     // NaN
})()
```

---

### Polyfill

```javascript
Number.parseInt = parseInt;
```

---

## [Number.parseFloat()]

### 基础说明

`Number.parseFloat(value)`

`Number.parseFloat()` 函数解析一个`字符串`参数并返回一个`浮点数`。

传入的 `value` 参数是要被检测是否是 `NaN` 的`值`，方法会返回给`定值`被解析成的`浮点数`。如果给`定值`不能被转换成`数值`，则会返回 `NaN`。

实现逻辑和全局方法[parseFloat()]没有区别。

```javascript
(() => {
    const { log } = console;
    log(Number.parseFloat === parseFloat); // true
})()
```

---

### 注意事项

#### 字符不是指定基数中的数字

`Number.parseFloat()`将它的`字符串`参数解析成为`浮点数`并返回。

如果在解析过程中遇到了`正负号`(`+`或`-`),数字(`0-9`),小数点,或者`科学记数法`中的指数(`e`或`E`)以外的字符,则它会忽略该`字符`以及之后的所有`字符`,返回当前已经解析到的`浮点数`。

允许使用`前导空格`和`尾随空格`，即`开头`和`结尾`的`空白符`允许存在，会被忽略。

---

#### 字符串不能解析为数值

如果`参数字符串`的第一个`字符`不能被解析成为`数字`,则`Number.parseFloat()`返回`NaN`.

算术上， `NaN` 不是任何一个进制下的数。 你可以调用`Number.isNaN()` 来判断 `Number.parseFloat()` 是否返回 `NaN`。

如果让`NaN`作为了任意`数学运算`的`操作数`,则`运算结果`必定也是`NaN`.。

---

#### Infinity值

`Number.parseFloat()` 也可转换和返回`Infinity`值. 可以使用`Number.isFinite` 函数来判断结果是否是一个有限的数值 (非`Infinity`, `-Infinity`, 或 `NaN`).

---

#### 其他对象转译

`Number.parseFloat()` 也可以转换一个已经定义了 `toString` 或者 `valueOf` 方法的对象，它返回的值和在调用该方法的结果上调用 `parseFloat` 值相同。

---

### 举例

#### 3.14

下面的例子都返回`3.14`

```javascript
(() => {
    const { log } = console;
    log(Number.parseFloat('3.14'));
    log(Number.parseFloat('314e-2'));
    log(Number.parseFloat('0.0314E+2'));
    log(Number.parseFloat('3.14more non-digit characters'));
})()
```

---

#### NaN

下面的例子将返回NaN
```javascript
(() => {
    const { log } = console;
    log(Number.parseFloat('FF2'));
})()
```

---

#### 更严格的转换函数

该函数通过`正则表达式`的方式,在需要更严格地转换`float`值时可能会有用:

```javascript
(() => {
    const { log } = console;
    const filterFloat = (value) => {
        if(/^(\-|\+)?|(\.\d+)(\d+(\.\d+)?|(\d+\.)|Infinity)$/
        .test(value))
        return Number(value);
    return NaN;
    }

    log(filterFloat('421'));               // 421
    log(filterFloat('-421'));              // -421
    log(filterFloat('+421'));              // 421
    log(filterFloat('Infinity'));          // Infinity
    log(filterFloat('1.61803398875'));     // 1.61803398875
    log(filterFloat('421e+0'));            // 421
    log(filterFloat('421hop'));            // NaN
    log(filterFloat('hop1.61803398875'));  // NaN
    log(filterFloat('999 888'));           // NaN
    log(filterFloat('.421'));              // 0.421
    log(filterFloat('421.'));              // 421
})()
```

---

### Polyfill

```javascript
Number.parseFloat = parseFloat;
```

---

## [Number.isInteger()]

### 基础说明

`Number.isInteger(value)`

`Number.isInteger()` 方法用来判断给定的参数是否为`整数`。

传入的 `value` 参数是要被检测是否是 `整数` 的`值`，方法会返回一个 `布尔值` 表示给定的值是否是 `整数` 的值。

如果被检测的值是`整数`，则返回 `true`，否则返回 `false`。注意 `NaN` 和正负 `Infinity` 不是`整数`。

需要注意的是，在`Javascript`内部，`整数`和`浮点数`使用同样的`存储方法`，所以`3`和`3.0`被视为同一个值。

---

### 举例

```javascript
(() => {
    const { log } = console;
    log(Number.isInteger(0));         // true
    log(Number.isInteger(1));         // true
    log(Number.isInteger(-100000));   // true

    log(Number.isInteger(0.1));       // false
    log(Number.isInteger(Math.PI));   // false

    log(Number.isInteger(Infinity));  // false
    log(Number.isInteger(-Infinity)); // false
    log(Number.isInteger('10'));      // false
    log(Number.isInteger(true));      // false
    log(Number.isInteger(false));     // false
    log(Number.isInteger([1]));       // false
})()
```

### Polyfill

```javascript
Number.isInteger = Number.isInteger || (value) => {
    return typeof value === 'number' &&
           isFinite(value) &&
           Math.floor(value) === value;
};
```

---

## [Number.isSafeInteger()]

### 基础说明

`Number.isSafeInteger(testValue)`

`Number.isSafeInteger()` 方法用来判断传入的参数值是否是一个`安全整数`（safe integer）。一个`安全整数`是一个符合下面条件的`整数`：

- can be exactly represented as an [IEEE-754] double precision number, and
- whose [IEEE-754] representation cannot be the result of rounding any other integer to fit the [IEEE-754] representation.

比如，`2^53 - 1` 是一个`安全整数`，它能被精确表示，在任何 [IEEE-754] 舍入模式（`rounding mode`）下，没有其他整数舍入结果为该整数。作为对比，`2^53` 就不是一个`安全整数`，它能够使用 [IEEE-754] 表示，但是 `2^53 + 1` 不能使用 [IEEE-754] 直接表示，在`就近舍入`（`round-to-nearest`）和`向零舍入`中，会被舍入为 `2^53`。

安全整数范围为 `-(2^53 - 1 )` 到 `2^53 - 1` 之间的整数，（包含`边界值`）。

传入的 `testValue` 参数是要被检测是否是 `安全整数` 的`值`，方法会返回一个 `布尔值` 表示给定的值是否是 `安全整数` 的值。

如果被检测的值是`安全整数`，则返回 `true`，否则返回 `false`。

---

### 注意事项

#### 精度丢弃

注意，由于 `JavaScript` 采用 [IEEE 754] 标准，数值存储为`64`位双精度格式，数值精度最多可以达到 `53` 个二进制位（`1` 个`隐藏位`与 `52` 个`有效位`）。

如果数值的`精度`超过这个`限度`，第`54`位及后面的位就会被`丢弃`，这种情况下，`Number.isInteger`可能会误判。

```javascript
(() => {
    const { log } = console;
    logNumber.isInteger(3.0000000000000002)); // true
})()
```

上面代码中，`Number.isInteger`的参数明明不是`整数`，但是会返回`true`。

原因就是这个小数的`精度`达到了`小数点`后`16`个十进制位，转成`二进制位`超过了`53`个`二进制位`，导致最后的那个`2`被丢弃了。

---

#### 临界值转换

如果一个数值的绝对值小于`Number.MIN_VALUE`（`5E-324`），即小于 `JavaScript` 能够分辨的`最小值`，会被自动转为 `0`。这时，`Number.isInteger`也会误判。

```javascript
(() => {
    const { log } = console;
    log(Number.isInteger(5E-324)); // false
    log(Number.isInteger(5E-325)); // true
})()
```

上面代码中，`5E-325`由于值太小，会被自动转为`0`，因此返回`true`。

总之，如果对`数据精度`的要求较高，不建议使用`Number.isInteger()`判断一个数值是否为`整数`。

---

### 举例

```javascript
(() => {
    const { log } = console;
    log(Number.isSafeInteger(3));;                    // true
    log(Number.isSafeInteger(Math.pow(2, 53)));       // false
    log(Number.isSafeInteger(Math.pow(2, 53) - 1));   // true
    log(Number.isSafeInteger(NaN));;                  // false
    log(Number.isSafeInteger(Infinity));;             // false
    log(Number.isSafeInteger('3'));;                  // false
    log(Number.isSafeInteger(3.1));;                  // false
    log(Number.isSafeInteger(3.0));;                  // true
})()
```

---

### Polyfill

```javascript
Number.isSafeInteger = Number.isSafeInteger || (value) => {
   return Number.isInteger(value) && Math.abs(value) <= Number.MAX_SAFE_INTEGER;
};
```

[Number.isFinite()]: https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Number/isFinite
[Number.isNaN()]: https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Number/isNaN
[parseInt()]: https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/parseInt
[Number.parseInt()]: https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Number/parseInt
[parseFloat()]: https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/parseFloat
[Number.parseFloat()]: https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Number/parseFloat
[Number.isInteger()]: https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Number/isInteger
[Number.isSafeInteger()]: https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Number/isSafeInteger
[IEEE 754]: http://en.wikipedia.org/wiki/IEEE_floating_point