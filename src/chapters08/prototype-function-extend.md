# 原型对象方法扩展

## [Array.from()][from]

### 基础语法

```javascript
Array.from(arrayLike[, mapFn[, thisArg]])
```

---

### 参数说明

> arrayLike

想要转换成数组的伪数组对象或可迭代对象。

>mapFn (可选参数)

如果指定了该参数，新数组中的每个元素会执行该回调函数。

>thisArg (可选参数)

可选参数，执行回调函数 `mapFn` 时 `this` 对象。

---

### 返回值说明

一个新的数组实例

---

### 详细说明

`Array.from()` 方法用于将两类对象转为真正的数组：类似数组的对象（`array-like object`）和可遍历（`iterable`）的对象（包括 `ES6` 新增的数据结构 `Set` 和 `Map`）。实例。

`Array.from()` 可以通过以下方式来创建数组对象：

- 伪数组对象（拥有一个 length 属性和若干索引属性的任意对象）
- 可迭代对象（可以获取对象中的元素,如 `Map`和 `Set` 等）

`Array.from()` 方法有一个可选参数 `mapFn`，让你可以在最后生成的数组上再执行一次 `map` 方法后再返回。也就是说 `Array.from(obj, mapFn, thisArg)` 就相当于 `Array.from(obj).map(mapFn, thisArg)`, 除非创建的不是可用的中间数组。 这对一些数组的子类,如  `typed arrays` 来说很重要, 因为中间数组的值在调用 `map()` 时需要是适当的类型。

`from()` 的 `length` 属性为 1 ，即`Array.from.length = 1`。

在 `ES2015` 中， `Class` 语法允许我们为内置类型（比如 `Array`）和自定义类新建子类（比如叫 `SubArray`）。

这些子类也会继承父类的静态方法，比如 `SubArray.from()`，调用该方法后会返回子类 `SubArray` 的一个实例，而不是 `Array` 的实例。

---

### 与扩展运算符区别

扩展运算符（`...`）也可以将某些`数据结构`转为`数组`。

```javascript
// arguments对象
function foo() {
  const args = [...arguments];
}

// NodeList对象
[...document.querySelectorAll('div')]
```

扩展运算符背后调用的是`遍历器接口`（`Symbol.iterator`），如果一个对象没有部署这个接口，就无法转换。

`Array.from`方法还支持`类似数组`的对象。所谓`类似数组`的对象，本质特征只有一点，即必须有`length`属性。

因此，任何有`length`属性的对象，都可以通过`Array.from`方法转为`数组`，而此时`扩展运算符`就无法转换。

```javascript
Array.from({ length: 3 });
// [ undefined, undefined, undefined ]
```

上面代码中，`Array.from`返回了一个具有三个成员的数组，每个位置的值都是`undefined`。`扩展运算符`转换不了这个对象。

---

### 案例

> Array from a String

```javascript
Array.from('foo'); 
// ["f", "o", "o"]
```

---

> Array from a Set

```javascript
let s = new Set(['foo', window]); 
Array.from(s); 
// ["foo", window]
```

---

> Array from a Map

```javascript
let m = new Map([[1, 2], [2, 4], [4, 8]]);
Array.from(m); 
// [[1, 2], [2, 4], [4, 8]]
```

---

> Array from an Array-like object (arguments)

```javascript
function f(arrayLike) {
  return Array.from(arrayLike);
}

const arrayLike = {
    '0': 'a',
    '1': 'b',
    '2': 'c',
    length: 3
};

f(arrayLike);

// ["a", "b", "c"]
```

---

> Array.from an DOM NodeList

实际应用中，常见的类似数组的对象是 `DOM` 操作返回的 `NodeList` 集合。`Array.from`可以将它们转为真正的`数组`。

```javascript
// NodeList对象
let ps = document.querySelectorAll('p');
Array.from(ps).filter(p => {
  return p.textContent.length > 100;
});
```

上面代码中，`querySelectorAll`方法返回的是一个类似数组的对象，可以将这个对象转为真正的数组，再使用`filter`方法。

---

> Array.from an Array

如果参数是一个真正的数组，`Array.from`会返回一个一模一样的新数组。

```javascript
Array.from([1, 2, 3])
// [1, 2, 3]
```

---

> 在Array.from中使用箭头函数

```javascript
// Using an arrow function as the map function to
// manipulate the elements
Array.from([1, 2, 3], x => x + x);  
// x => x + x代表这是一个函数，只是省略了其他的定义，这是一种Lambda表达式的写法
// 箭头的意思表示从当前数组中取出一个值，然后自加，并将返回的结果添加到新数组中    
// [2, 4, 6]


// Generate a sequence of numbers
// Since the array is initialized with `undefined` on each position,
// the value of `v` below will be `undefined`
Array.from({length: 5}, (v, i) => i);
// [0, 1, 2, 3, 4]
```

---

> 数组去重合并

```javascript
function combine(){ 
    let arr = [].concat.apply([], arguments);  //没有去重复的新数组 
    return Array.from(new Set(arr));
} 

var m = [1, 2, 2], n = [2,3,3]; 
console.log(combine(m,n));                     // [1, 2, 3]
```

---

> 返回各种数据的类型

```javascript
function typesOf () {
  return Array.from(arguments, value => typeof value)
}
typesOf(null, [], NaN)
// ['object', 'object', 'number']
```

---

### Polyfill

`ECMA-262 第六版`标准添加了 `Array.from` 。有些实现中可能尚未包括在其中。你可以通过在脚本前添加如下内容作为替代方法，以使用未原生支持的 `Array.from` 方法。

该算法按照  `ECMA-262 第六版`中的规范实现，并假定 `Object` 和 `TypeError` 有其本身的值，  `callback.call` 对应 `Function.prototype.call` 。

此外，鉴于无法使用 `Polyfill` 实现真正的的迭代器，该实现不支持规范中定义的`泛型可迭代`元素。

```javascript
// Production steps of ECMA-262, Edition 6, 22.1.2.1
// Reference: https://people.mozilla.org/~jorendorff/es6-draft.html#sec-array.from
if (!Array.from) {
  Array.from = (function () {
    var toStr = Object.prototype.toString;
    var isCallable = function (fn) {
      return typeof fn === 'function' || toStr.call(fn) === '[object Function]';
    };
    var toInteger = function (value) {
      var number = Number(value);
      if (isNaN(number)) { return 0; }
      if (number === 0 || !isFinite(number)) { return number; }
      return (number > 0 ? 1 : -1) * Math.floor(Math.abs(number));
    };
    var maxSafeInteger = Math.pow(2, 53) - 1;
    var toLength = function (value) {
      var len = toInteger(value);
      return Math.min(Math.max(len, 0), maxSafeInteger);
    };

    // The length property of the from method is 1.
    return function from(arrayLike/*, mapFn, thisArg */) {
      // 1. Let C be the this value.
      var C = this;

      // 2. Let items be ToObject(arrayLike).
      var items = Object(arrayLike);

      // 3. ReturnIfAbrupt(items).
      if (arrayLike == null) {
        throw new TypeError("Array.from requires an array-like object - not null or undefined");
      }

      // 4. If mapfn is undefined, then let mapping be false.
      var mapFn = arguments.length > 1 ? arguments[1] : void undefined;
      var T;
      if (typeof mapFn !== 'undefined') {
        // 5. else      
        // 5. a If IsCallable(mapfn) is false, throw a TypeError exception.
        if (!isCallable(mapFn)) {
          throw new TypeError('Array.from: when provided, the second argument must be a function');
        }

        // 5. b. If thisArg was supplied, let T be thisArg; else let T be undefined.
        if (arguments.length > 2) {
          T = arguments[2];
        }
      }

      // 10. Let lenValue be Get(items, "length").
      // 11. Let len be ToLength(lenValue).
      var len = toLength(items.length);

      // 13. If IsConstructor(C) is true, then
      // 13. a. Let A be the result of calling the [[Construct]] internal method 
      // of C with an argument list containing the single item len.
      // 14. a. Else, Let A be ArrayCreate(len).
      var A = isCallable(C) ? Object(new C(len)) : new Array(len);

      // 16. Let k be 0.
      var k = 0;
      // 17. Repeat, while k < len… (also steps a - h)
      var kValue;
      while (k < len) {
        kValue = items[k];
        if (mapFn) {
          A[k] = typeof T === 'undefined' ? mapFn(kValue, k) : mapFn.call(T, kValue, k);
        } else {
          A[k] = kValue;
        }
        k += 1;
      }
      // 18. Let putStatus be Put(A, "length", len, true).
      A.length = len;
      // 20. Return A.
      return A;
    };
  }());
}
```

---

## [Array.of()][of]

### 基础语法

```javascript
Array.of(element0[, element1[, ...[, elementN]]])
```

---

### 参数说明

> elementN

任意个参数，将按顺序成为返回数组中的元素。

---

### 返回值说明

新的 Array 实例。

---

### 详细描述

`Array.of()` 方法创建一个具有可变数量参数的新数组实例，而不考虑参数的数量或类型。

这个方法的主要目的，是弥补数组构造函数`Array()`的不足。因为参数个数的不同，会导致`Array()`的行为有差异。

`Array.of()` 和 `Array` 构造函数之间的区别在于处理整数参数：

- `Array.of(7)` 创建一个具有单个元素 7 的数组
- `Array(7)` 创建一个长度为`7`的空数组（注意：这是指一个有7个空位的数组，而不是由7个`undefined`组成的数组）。

```javascript
Array.of(7);       // [7]
Array.of(1, 2, 3); // [1, 2, 3]

Array(7);          // [ , , , , , , ]
Array(1, 2, 3);    // [1, 2, 3]
```

上面代码中，`Array`方法没有参数、一个参数、三个参数时，返回结果都不一样。只有当参数个数不少于 `2` 个时，`Array()`才会返回由参数组成的新数组。参数个数只有一个时，实际上是指定数组的长度。

`Array.of`基本上可以用来替代`Array()`或`new Array()`，并且不存在由于参数不同而导致的`重载`。它的行为非常统一。

```javascript
Array.of() // []
Array.of(undefined) // [undefined]
Array.of(1) // [1]
Array.of(1, 2) // [1, 2]
```

`Array.of`总是返回`参数值`组成的`数组`。如果没有参数，就返回一个`空数组`。

此函数是`ECMAScript 2015`标准的一部分。详见 [`Array.of`][proposal] 和 [`Array.from proposal`][proposal] 和 [`Array.of polyfill`][polyfill]。

---

### 案例

```javascript
Array.of(1);         // [1]
Array.of(1, 2, 3);   // [1, 2, 3]
Array.of(undefined); // [undefined]
```

---

### polyfill

如果原生不支持的话，在其他代码之前执行以下代码会创建 `Array.of()` 。

```javascript
if (!Array.of) {
  Array.of = function() {
    return Array.prototype.slice.call(arguments);
  };
}
```

---

[from]: https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array/from
[of]: https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array/of
[proposal]: https://gist.github.com/rwaldron/1074126
[polyfill]: https://gist.github.com/rwaldron/3186576