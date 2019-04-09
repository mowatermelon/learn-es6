# Number 原型属性

## [MAX_SAFE_INTEGER]

### 基础说明

`Number.MAX_SAFE_INTEGER`

`Number.MAX_SAFE_INTEGER` 常量表示在 `JavaScript` 中最大的安全整数（`maxinum safe integer`)（`2^53` - 1）。

| 特性名       | 特性值 |
| :----------- | :----- |
| writable     | false  |
| enumerable   | false  |
| configurable | false   |

`MAX_SAFE_INTEGER` 是一个值为 `9007199254740991`的常量。

因为`Javascript`的数字存储使用了[IEEE 754][IEEE 754]中规定的[双精度浮点数][Double_precision]数据类型，而这一数据类型能够安全存储 `-(2^53 - 1 )` 到 `2^53 - 1`之间的数值（包含`边界值`）。

这里`安全存储`的意思是指能够准确区分两个不相同的值，例如 `Number.MAX_SAFE_INTEGER + 1 === Number.MAX_SAFE_INTEGER + 2` 将得到 `true`的结果，因为达到阈值之后，不会再做数值增加，而这在数学上是错误的，参考[Number.isSafeInteger()][isSafeInteger]获取更多信息.

由于 `MAX_SAFE_INTEGER` 是`Number`的一个`静态属性`，所以你不用自己动手为`Number`对象创建`Number.MAX_SAFE_INTEGER`这一属性，就可以直接使用它。

---

### 举例

```javascript
(() => {
    const { log } = console;
    const MAX_SAFE_INTEGER = Number.MAX_SAFE_INTEGER;
    const num1 = Math.pow(2, 53) - 1;
    log(MAX_SAFE_INTEGER); // 9007199254740991
    log(MAX_SAFE_INTEGER === num1); // true
})()
```

---

## [MIN_SAFE_INTEGER]

### 基础说明

`Number.MIN_SAFE_INTEGER`

`Number.MIN_SAFE_INTEGER` 代表在 `JavaScript`中最小的安全的`integer`型数字 `-(2^53 - 1 )`.

| 特性名       | 特性值 |
| :----------- | :----- |
| writable     | false  |
| enumerable   | false  |
| configurable | false   |

`MIN_SAFE_INTEGER` 的值是`-9007199254740991`。

形成这个数字的原因是 `JavaScript` 在 [IEEE 754][IEEE 754] 中使用[double-precision floating-point format numbers][Double_precision] 作为规定。

在这个规定中能安全的表示数字的范围在`-(2^53 - 1 )` 到 `2^53 - 1` 之间.

由于`MIN_SAFE_INTEGER` 是`Number`的一个`静态属性`,你可以直接使用`Number.MIN_SAFE_INTEGER`, 而不是自己去创建一个`Number`的属性。

---

### 举例

```javascript
(() => {
    const { log } = console;
    const MIN_SAFE_INTEGER = Number.MIN_SAFE_INTEGER;
    const num1 = - (Math.pow(2, 53) - 1);
    log(MIN_SAFE_INTEGER); // -9007199254740991
    log(MIN_SAFE_INTEGER === num1); // true
})()
```

---

## [NaN]

### 基础说明

`Number.NaN`

`Number.NaN` 表示`非数字`（`Not-A-Number`）。和 `NaN` 相同。

| 特性名       | 特性值 |
| :----------- | :----- |
| writable     | false  |
| enumerable   | false  |
| configurable | false   |

不必创建一个 `Number` 实例来访问该属性，使用 `Number.NaN` 来访问该`静态属性`。

---

### 举例

```javascript
(() => {
    const { log } = console;
    const _NaN = Number.NaN;
    log(_NaN); // NaN
})()
```

---

## [Number.EPSILON][EPSILON]

### 基础说明

`ES6` 在`Number`对象上面，新增一个极小的常量`Number.EPSILON`。

| 特性名       | 特性值 |
| :----------- | :----- |
| writable     | false  |
| enumerable   | false  |
| configurable | false   |

你不必创建一个 `Number` 对象来访问这个`静态属性`（直接使用 `Number.EPSILON`）。

根据规范，它表示 `1` 与大于 `1` 的`最小浮点数`之间的`差`。

对于 `64` 位浮点数来说，大于 `1` 的`最小浮点数`相当于`二进制`的`1.00..001`，小数点后面有连续 `51` 个`零`。

这个值减去 `1` 之后，接近于 `2.2204460492503130808472633361816E-16`，或者 `2^-52`。

```javascript
(() => {
    const { log } = console;
    const EPSILON = Number.EPSILON;
    log(EPSILON); // 2.220446049250313e-16
    log(EPSILON === Math.pow(2, -52)); // true
    log(EPSILON.toFixed(20)); // 0.00000000000000022204
})()
```

`Number.EPSILON`实际上是 `JavaScript` 能够表示的`最小精度`。

`误差`如果小于这个值，就可以认为已经没有`意义`了，即不存在`误差`了。

引入一个这么小的量的目的，在于为`浮点数`计算，设置一个`误差范围`。

---

### 举例

#### 0.1 + 0.2

我们知道`浮点数`计算是`不精确`的。

```javascript
(() => {
    const { log } = console;
    const num1 = 0.1;
    const num2 = 0.2;
    const num3 = num1 + num2;
    const num4 = 0.3;
    const num5 = 5.551115123125783e-17;
    log(num3); // 0.30000000000000004
    log(num3 === num4); // false
    log(num3 - num4); // 5.551115123125783e-17
    log(num5.toFixed(20)); // '0.00000000000000005551'
})()
```

上面代码解释了，为什么比较`0.1 + 0.2`与`0.3`得到的结果是`false`。

`Number.EPSILON`可以用来设置`能够接受的误差范围`。比如，误差范围设为 `2` 的`-50` 次方（即`Number.EPSILON * Math.pow(2, 2)`），即如果两个浮点数的差小于这个值，我们就认为这两个浮点数相等。

```javascript
(() => {
    const { log } = console;
    log(5.551115123125783e-17 < Number.EPSILON * Math.pow(2, 2));// true
})()
```

#### 误差检查函数

`Number.EPSILON`的实质是一个可以接受的`最小误差范围`。

```javascript
(() => {
    const { log } = console;
    const withinErrorMargin = (left, right) => Math.abs(left - right) < Number.EPSILON * Math.pow(2, 2);
    const num1 = 0.1;
    const num2 = 0.2;
    const num3 = num1 + num2;
    const num4 = 0.3;
    const num5 = 1.1;
    const num6 = 1.3;
    const num7 = num5 + num6;
    const num8 = 2.4;
    log(num3); // 0.30000000000000004
    log(num3 === num4); // false
    log(num3 - num4); // 5.551115123125783e-17
    log(withinErrorMargin(num3, num4)); // true

    log(num7); // 2.4000000000000004
    log(num7 === num8); // false
    log(num7 - num8); // 4.440892098500626e-16
    log(withinErrorMargin(num7, num8)); // true
})()
```

[MAX_SAFE_INTEGER]: https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Number/MAX_SAFE_INTEGER
[MIN_SAFE_INTEGER]: https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Number/MIN_SAFE_INTEGER
[NaN]: https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Number/NaN
[EPSILON]: https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Number/EPSILON
[isSafeInteger]: https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Number/isSafeInteger
[IEEE 754]: http://en.wikipedia.org/wiki/IEEE_floating_point
[Double_precision]: http://en.wikipedia.org/wiki/Double_precision_floating-point_format