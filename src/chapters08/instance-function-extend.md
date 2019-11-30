# 实例对象方法扩展

## 内部数据寻找

### [find()][find]

#### 基础语法

```javascript
arr.find(callback(element[, index[, array]])[, thisArg])
```

---

#### 参数说明

> callback

在数组每一项上执行的函数，接收 3 个参数：

- element

当前遍历到的元素。

---

- index

当前遍历到的索引。

---

- array

数组本身。

---

> thisArg 可选

可选，指定 `callback` 的 `this` 参数。

---

#### 返回值说明

当某个元素通过 `callback` 的测试时，即可返回数组中的对应值，否则返回 `undefined`。

---

#### 详细说明

`find()` 方法返回数组中满足提供的测试函数的第一个元素的值。否则返回 `undefined`。
另请参见  [`findIndex()`][findIndex] 方法，它返回数组中找到的元素的`索引`，而不是`其值`。

如果你需要找到一个元素的位置或者一个元素是否存在于数组中，使用`Array.prototype.indexOf()` 或 `Array.prototype.includes()`。

`find` 方法对数组中的每一项元素执行一次 `callback` 函数，直至有一个 `callback` 返回 `true`。

当找到了这样一个元素后，该方法会`立即`返回这个元素的值，否则返回 `undefined`。

注意 `callback` 函数会为数组中的每个索引调用即从 `0` 到 `length - 1`，而不仅仅是那些被赋值的索引，这意味着对于`稀疏数组`来说，该方法的效率要低于那些只遍历有值的索引的方法。

`callback` 函数带有3个参数：`当前元素的值`、`当前元素的索引`，以及`数组本身`。

如果提供了 `thisArg` 参数，那么它将作为每次 `callback` 函数执行时的上下文对象，否则上下文对象为 `undefined`。

`find` 方法不会改变数组。

在第一次调用 `callback` 函数时会确定元素的索引范围，因此在 `find` 方法开始执行之后添加到数组的新元素将不会被 `callback` 函数访问到。

如果数组中一个尚未被`callback`函数访问到的元素的值被`callback`函数所改变，那么当`callback`函数访问到它时，它的值是将是根据它在数组中的索引所访问到的当前值。

`被删除`的`元素`仍旧会被访问到。

---

#### 案例

> 用对象的属性查找数组里的对象

```javascript
const inventory = [
    {name: 'apples', quantity: 2},
    {name: 'bananas', quantity: 0},
    {name: 'cherries', quantity: 5}
];

function findCherries(fruit) {
    return fruit.name === 'cherries';
}

console.log(inventory.find(findCherries)); // { name: 'cherries', quantity: 5 }
```

---

> 寻找数组中的质数

下面的例子展示了如何从一个数组中寻找质数（如果找不到质数则返回`undefined`）

```javascript
function isPrime(element, index, array) {
  var start = 2;
  while (start <= Math.sqrt(element)) {
    if (element % start++ < 1) {
      return false;
    }
  }
  return element > 1;
}

console.log([4, 6, 8, 12].find(isPrime)); // undefined, not found
console.log([4, 5, 8, 12].find(isPrime)); // 5
```

---

> 当在回调中删除数组中的一个值时，当访问到这个位置时，其传入的值时 `undefined`：

```javascript
(()=>{
  const {log} =console;
// Declare array with no element at index 2, 3 and 4
var a = [0,1,,,,5,6];

// Shows all indexes, not just those that have been assigned values
a.find(function(value, index) {
  log('Visited index ' + index + ' with value ' + value);
});

// Visited index 0 with value 0
// Visited index 1 with value 1
// Visited index 2 with value undefined
// Visited index 3 with value undefined
// Visited index 4 with value undefined
// Visited index 5 with value 5
// Visited index 6 with value 6

// Shows all indexes, including deleted
a.find(function(value, index) {

  // Delete element 5 on first iteration
  if (index == 0) {
    log('Deleting a[5] with value ' + a[5]);
    delete a[5];  // 注：这里只是将a[5]设置为undefined
    log('Deleting a[6] with value ' + a[6]);
    a.pop(1)// 用a.pop()删除最后一项，依然会遍历到被删的那一项
  }
  // Element 5 is still visited even though deleted
  log('Visited index ' + index + ' with value ' + value);
});
// Deleting a[5] with value 5
// Visited index 0 with value 0
// Visited index 1 with value 1
// Visited index 2 with value undefined
// Visited index 3 with value undefined
// Visited index 4 with value undefined
// Visited index 5 with value undefined
// Visited index 6 with value undefined
})()
```

---

#### Polyfill

如果您需要兼容不支持`Object.defineProperty`的`JavaScript`引擎，那么最好不要对`Array.prototype`方法进行 `polyfill` ，因为您无法使其成为不可枚举的。

```javascript
// https://tc39.github.io/ecma262/#sec-array.prototype.find
if (!Array.prototype.find) {
  Object.defineProperty(Array.prototype, 'find', {
    value: function(predicate) {
     // 1. Let O be ? ToObject(this value).
      if (this == null) {
        throw new TypeError('"this" is null or not defined');
      }

      var o = Object(this);

      // 2. Let len be ? ToLength(? Get(O, "length")).
      var len = o.length >>> 0;

      // 3. If IsCallable(predicate) is false, throw a TypeError exception.
      if (typeof predicate !== 'function') {
        throw new TypeError('predicate must be a function');
      }

      // 4. If thisArg was supplied, let T be thisArg; else let T be undefined.
      var thisArg = arguments[1];

      // 5. Let k be 0.
      var k = 0;

      // 6. Repeat, while k < len
      while (k < len) {
        // a. Let Pk be ! ToString(k).
        // b. Let kValue be ? Get(O, Pk).
        // c. Let testResult be ToBoolean(? Call(predicate, T, « kValue, k, O »)).
        // d. If testResult is true, return kValue.
        var kValue = o[k];
        if (predicate.call(thisArg, kValue, k, o)) {
          return kValue;
        }
        // e. Increase k by 1.
        k++;
      }

      // 7. Return undefined.
      return undefined;
    }
  });
}
```

---

### [findIndex()][findIndex]

#### 基础语法

```javascript
arr.findIndex(callback(element[, index[, array]])[, thisArg])
```

---

#### 参数说明

> callback

在数组每一项上执行的函数，接收 3 个参数：

- element

当前遍历到的元素。

---

- index

当前遍历到的索引。

---

- array

数组本身。

---

> thisArg 可选

可选，指定 `callback` 的 `this` 参数。

---

#### 返回值说明

当某个元素通过 `callback` 的测试时，返回数组中满足提供的测试函数的第一个元素的`索引`。否则返回`-1`。

---

#### 详细说明

`findIndex()`方法返回数组中满足提供的测试函数的第一个元素的`索引`。否则返回`-1`。

另请参见  `find()` 方法，它返回数组中找到的元素的`值`，而不是其`索引`。

`findIndex`方法对数组中的每个数组索引`0..length-1`（包括）执行一次`callback`函数，直到找到一个`callback`函数返回真实值（强制为true）的值。如果找到这样的元素，`findIndex`会立即返回该元素的索引。如果回调从不返回真值，或者数组的`length`为`0`，则`findIndex`返回`-1`。 与某些其他数组方法（如`Array#some`）不同，在`稀疏数组`中，即使对于数组中不存在的条目的索引也会调用回调函数。

回调函数调用时有三个参数：`元素的值`，`元素的索引`，以及`被遍历的数组`。

如果一个 `thisArg` 参数被提供给 `findIndex`, 它将会被当作`this`使用在每次回调函数被调用的时候。如果没有被提供，将会使用`undefined`。

`findIndex`不会修改所调用的数组。

在第一次调用`callback`函数时会确定元素的索引范围，因此在`findIndex`方法开始执行之后添加到数组的新元素将不会被`callback`函数访问到。

如果数组中一个尚未被`callback`函数访问到的元素的值被`callback`函数所改变，那么当`callback`函数访问到它时，它的值是将是根据它在数组中的索引所访问到的`当前值`。

被`删除`的元素仍然会被访问到。

---

#### 案例

> 查找数组中首个质数元素的索引

以下示例查找数组中`素数`的元素的索引（如果不存在`素数`，则返回`-1`）。

```javascript
function isPrime(element, index, array) {
  var start = 2;
  while (start <= Math.sqrt(element)) {
    if (element % start++ < 1) {
      return false;
    }
  }
  return element > 1;
}

console.log([4, 6, 8, 12].findIndex(isPrime)); // -1, not found
console.log([4, 6, 7, 12].findIndex(isPrime)); // 2
```

---

#### Polyfill

如果您需要兼容不支持`Object.defineProperty`的`JavaScript`引擎，那么最好不要对`Array.prototype`方法进行 `polyfill` ，因为您无法使其成为不可枚举的。

```javascript
// https://tc39.github.io/ecma262/#sec-array.prototype.findIndex
if (!Array.prototype.findIndex) {
  Object.defineProperty(Array.prototype, 'findIndex', {
    value: function(predicate) {
     // 1. Let O be ? ToObject(this value).
      if (this == null) {
        throw new TypeError('"this" is null or not defined');
      }

      var o = Object(this);

      // 2. Let len be ? ToLength(? Get(O, "length")).
      var len = o.length >>> 0;

      // 3. If IsCallable(predicate) is false, throw a TypeError exception.
      if (typeof predicate !== 'function') {
        throw new TypeError('predicate must be a function');
      }

      // 4. If thisArg was supplied, let T be thisArg; else let T be undefined.
      var thisArg = arguments[1];

      // 5. Let k be 0.
      var k = 0;

      // 6. Repeat, while k < len
      while (k < len) {
        // a. Let Pk be ! ToString(k).
        // b. Let kValue be ? Get(O, Pk).
        // c. Let testResult be ToBoolean(? Call(predicate, T, « kValue, k, O »)).
        // d. If testResult is true, return k.
        var kValue = o[k];
        if (predicate.call(thisArg, kValue, k, o)) {
          return k;
        }
        // e. Increase k by 1.
        k++;
      }

      // 7. Return -1.
      return -1;
    }
  });
}
```

---

### [includes()][includes]

#### 基础语法

```javascript
arr.includes(valueToFind[, fromIndex])
```

---

#### 参数说明

> valueToFind

需要查找的元素值，使用 `includes()`比较字符串和字符时是区分大小写。

> fromIndex

可选参数，从`fromIndex` 索引处开始查找 `valueToFind`。如果为负值，则按升序从 `array.length` + `fromIndex` 的索引开始搜 （即使从末尾开始往前跳 `fromIndex` 的绝对值个索引，然后往后搜寻）。默认为 `0`。

---

#### 返回值说明

返回一个布尔值 `Boolean` ，如果在数组中找到了`valueToFind`值（如果传入了 `fromIndex` ，表示在 `fromIndex` 指定的索引范围中找到了`valueToFind`值）则返回 `true` 。

`0`的值都被认为是相等的，不管符号是什么(也就是说，`-0`被认为同时等于`0`和`+0`)，但是`false`不被认为等于`0`。

从技术上讲，`include()`使用[`sameValueZero`][sameValueZero]算法来确定是否找到了给定的元素。

---

#### 详细说明

`includes()` 方法用来判断一个数组是否包含一个指定的值，根据情况，如果包含则返回 `true`，否则返回`false`。

注意：`对象数组`不能使用`includes`方法来检测。

---

#### 案例

> 基础使用

```javascript
[1, 2, 3].includes(2);     // true
[1, 2, 3].includes(4);     // false
[1, 2, 3].includes(3, 3);  // false
[1, 2, 3].includes(3, -1); // true
[1, 2, NaN].includes(NaN); // true
```

---

> fromIndex 大于等于数组长度

如果 `fromIndex` 大于等于数组的长度，则会返回 `false`，且该数组不会被搜索。

```javascript
var arr = ['a', 'b', 'c'];

arr.includes('c', 3);   // false
arr.includes('c', 100); // false
```

---

> 计算出的索引小于 0

如果 `fromIndex` 为负值，计算出的索引将作为开始搜索`searchElement`的位置。如果计算出的索引小于 `0`，则整个数组都会被搜索。

```javascript
// array length is 3
// fromIndex is -100
// computed index is 3 + (-100) = -97

var arr = ['a', 'b', 'c'];

arr.includes('a', -100); // true
arr.includes('b', -100); // true
arr.includes('c', -100); // true
arr.includes('a', -2); // false
```

---

> 作为通用方法的 `includes()`

`includes()` 方法有意设计为通用方法。它不要求`this`值是数组对象，所以它可以被用于其他类型的对象 (比如`类数组`对象)。下面的例子展示了 在函数的 `arguments` 对象上调用的 `includes()` 方法。

```javascript
(function() {
  console.log([].includes.call(arguments, 'a')); // true
  console.log([].includes.call(arguments, 'd')); // false
})('a','b','c');
```

---

#### Polyfill

如果你需要支持那些不支持`Object.defineProperty`的废弃`JavaScript 引擎`，你最好不要 polyfill `Array.prototype` 方法，因为你不能使它们不可枚举。

`polyfill` 没有实现 对于 `NaN` 的检测。

```javascript
// https://tc39.github.io/ecma262/#sec-array.prototype.includes
if (!Array.prototype.includes) {
  Object.defineProperty(Array.prototype, 'includes', {
    value: function(valueToFind, fromIndex) {

      if (this == null) {
        throw new TypeError('"this" is null or not defined');
      }

      // 1. Let O be ? ToObject(this value).
      var o = Object(this);

      // 2. Let len be ? ToLength(? Get(O, "length")).
      var len = o.length >>> 0;

      // 3. If len is 0, return false.
      if (len === 0) {
        return false;
      }

      // 4. Let n be ? ToInteger(fromIndex).
      //    (If fromIndex is undefined, this step produces the value 0.)
      var n = fromIndex | 0;

      // 5. If n ≥ 0, then
      //  a. Let k be n.
      // 6. Else n < 0,
      //  a. Let k be len + n.
      //  b. If k < 0, let k be 0.
      var k = Math.max(n >= 0 ? n : len - Math.abs(n), 0);

      function sameValueZero(x, y) {
        return x === y || (typeof x === 'number' && typeof y === 'number' && isNaN(x) && isNaN(y));
      }

      // 7. Repeat, while k < len
      while (k < len) {
        // a. Let elementK be the result of ? Get(O, ! ToString(k)).
        // b. If SameValueZero(valueToFind, elementK) is true, return true.
        if (sameValueZero(o[k], valueToFind)) {
          return true;
        }
        // c. Increase k by 1. 
        k++;
      }

      // 8. Return false
      return false;
    }
  });
}
```

---

## 内部数据填充

### [copyWithin()][copyWithin]

#### 基础语法

```javascript
arr.copyWithin(target[, start[, end]])
```

---

#### 参数说明

> target

`0` 为基底的索引，复制序列到该位置。如果是负数，`target` 将从末尾开始计算。
如果 `target` 大于等于 `arr.length`，将会不发生拷贝。如果 `target` 在 `start` 之后，复制的序列将被修改以符合 `arr.length`。

> start

`0` 为基底的索引，开始复制元素的起始位置。如果是负数，`start` 将从末尾开始计算。
如果 `start` 被忽略，`copyWithin` 将会从`0`开始复制。

> end

`0` 为基底的索引，开始复制元素的结束位置。`copyWithin` 将会拷贝到该位置，但不包括 `end` 这个位置的元素。如果是负数， `end` 将从末尾开始计算。
如果 `end` 被忽略，`copyWithin` 将会复制到 `arr.length`。

---

#### 返回值说明

改变了的数组。

---

#### 详细说明

`copyWithin()` 方法`浅复制`数组的一部分到同一数组中的另一个位置，并返回它，而不修改其大小。

参数`target`,`start`和`end` 必须为整数。

如果`start`为负，则其指定的索引位置等同于`length+start`，`length`为数组的长度。`end`也是如此。

`copyWithin`方法不要求其`this`值必须是一个数组对象；除此之外，`copyWithin`是一个可变方法，它可以改变`this`对象本身，并且返回它，而不仅仅是它的拷贝。

`copyWithin` 就像 `C` 和 `C++` 的 `memcpy` 函数一样，且它是用来移动 `Array` 或者 `TypedArray` 数据的一个高性能的方法。复制以及粘贴序列这两者是为一体的操作;即使复制和粘贴区域重叠，粘贴的序列也会有拷贝来的值。

`copyWithin` 函数是设计为通用的，其不要求其 `this` 值必须是一个数组对象。

`The copyWithin` 是一个可变方法，它不会改变 `this` 的 `length`，但是会改变 `this` 本身的内容，且需要时会创建新的属性。

---

#### 案例

```javascript
var array1 = ['a', 'b', 'c', 'd', 'e'];

// copy to index 0 the element at index 2 (c)
// ['a', 'b', 'c', 'd', 'e'] ==> ["c", "b", "c", "d", "e"]
console.log(array1.copyWithin(0, 2, 3));

// copy to index 0 the element between index 2 and index 3 (c,d) 
// ["c", "b", "c", "d", "e"] ==> ["c", "d", "c", "d", "e"]
console.log(array1.copyWithin(0, 2, 4));

// copy to index 0 the element between index 2 and index 4 (c,d,e)
// ["c", "d", "c", "d", "e"] ==> ["c", "d", "e", "d", "e"]
console.log(array1.copyWithin(0, 2));

// copy to index 1 the element at index 2 (e)
// ["c", "d", "e", "d", "e"] ==> ["c", "e", "e", "d", "e"]
console.log(array1.copyWithin(1, 2, 3));

// copy to index 1 the element between index 2 and index 3 (e,d) 
// ["c", "e", "e", "d", "e"] ==> ["c", "e", "d", "d", "e"]
console.log(array1.copyWithin(1, 2, 4));

// copy to index 1 the element between index 2 and index 4 (d,d,e) 
// ["c", "e", "d", "d", "e"] ==> ["c", "d", "d", "e", "e"]
console.log(array1.copyWithin(1, 2));

// copy to index 0 the element at index 3 (e)
// ["c", "d", "d", "e", "e"] ==> ["e", "d", "d", "e", "e"]
console.log(array1.copyWithin(0, 3, 4));

// copy to index 2 the element at index 3 (e)
// ["e", "d", "d", "e", "e"] ==> ["e", "d", "e", "e", "e"]
console.log(array1.copyWithin(2, 3, 4));

[1, 2, 3, 4, 5].copyWithin(-2);
// [1, 2, 3, 1, 2]

[1, 2, 3, 4, 5].copyWithin(0, 3);
// [4, 5, 3, 4, 5]

[1, 2, 3, 4, 5].copyWithin(0, 3, 4);
// [4, 2, 3, 4, 5]

[1, 2, 3, 4, 5].copyWithin(-2, -3, -1);
// [1, 2, 3, 3, 4]

[].copyWithin.call({length: 5, 3: 1}, 0, 3);
// {0: 1, 3: 1, length: 5}

// ES2015 Typed Arrays are subclasses of Array
var i32a = new Int32Array([1, 2, 3, 4, 5]);

i32a.copyWithin(0, 2);
// Int32Array [3, 4, 5, 4, 5]

// On platforms that are not yet ES2015 compliant: 
[].copyWithin.call(new Int32Array([1, 2, 3, 4, 5]), 0, 3, 4);
// Int32Array [4, 2, 3, 4, 5]
```

---

#### Polyfill

```javascript
if (!Array.prototype.copyWithin) {
  Array.prototype.copyWithin = function(target, start/*, end*/) {
    // Steps 1-2.
    if (this == null) {
      throw new TypeError('this is null or not defined');
    }

    var O = Object(this);

    // Steps 3-5.
    var len = O.length >>> 0;

    // Steps 6-8.
    var relativeTarget = target >> 0;

    var to = relativeTarget < 0 ?
      Math.max(len + relativeTarget, 0) :
      Math.min(relativeTarget, len);

    // Steps 9-11.
    var relativeStart = start >> 0;

    var from = relativeStart < 0 ?
      Math.max(len + relativeStart, 0) :
      Math.min(relativeStart, len);

    // Steps 12-14.
    var end = arguments[2];
    var relativeEnd = end === undefined ? len : end >> 0;

    var final = relativeEnd < 0 ?
      Math.max(len + relativeEnd, 0) :
      Math.min(relativeEnd, len);

    // Step 15.
    var count = Math.min(final - from, len - to);

    // Steps 16-17.
    var direction = 1;

    if (from < to && to < (from + count)) {
      direction = -1;
      from += count - 1;
      to += count - 1;
    }

    // Step 18.
    while (count > 0) {
      if (from in O) {
        O[to] = O[from];
      } else {
        delete O[to];
      }

      from += direction;
      to += direction;
      count--;
    }

    // Step 19.
    return O;
  };
}
```

---

### [fill()][fill]

#### 基础语法

```javascript
arr.fill(value[, start[, end]])
```

---

#### 参数说明

> value

用来填充数组元素的值。

---

> start

可选参数，起始索引，默认值为`0`。

---

> end

可选参数，终止索引，默认值为 `this.length`。

---

#### 返回值说明

修改后的数组。

---

#### 详细说明

`fill()` 方法用一个固定值填充一个数组中从`起始索引`到`终止索引`内的全部元素。不包括`终止索引`。

`fill` 方法接受三个参数 `value`, `start` 以及 `end`. `start` 和 `end` 参数是可选的, 其默认值分别为 `0` 和 `this` 对象的 `length` 属性值。

如果 `start` 是个负数, 则开始索引会被自动计算成为 `length+start`, 其中 `length` 是 `this` 对象的 `length` 属性值。

如果 `end` 是个负数, 则结束索引会被自动计算成为 `length+end`。

`fill` 方法故意被设计成通用方法, 该方法不要求 `this` 是数组对象。

`fill` 方法是个可变方法, 它会改变调用它的 `this` 对象本身, 然后返回它, 而并不是返回一个副本。

当一个对象被传递给 `fill`方法的时候, 填充数组的是这个对象的引用。

---

#### 案例

```javascript
[1, 2, 3].fill(4);               // [4, 4, 4]
[1, 2, 3].fill(4, 1);            // [1, 4, 4]
[1, 2, 3].fill(4, 1, 2);         // [1, 4, 3]
[1, 2, 3].fill(4, 1, 1);         // [1, 2, 3]
[1, 2, 3].fill(4, 3, 3);         // [1, 2, 3]
[1, 2, 3].fill(4, -3, -2);       // [4, 2, 3]
[1, 2, 3].fill(4, NaN, NaN);     // [1, 2, 3]
[1, 2, 3].fill(4, 3, 5);         // [1, 2, 3]
Array(3).fill(4);                // [4, 4, 4]
[].fill.call({ length: 3 }, 4);  // {0: 4, 1: 4, 2: 4, length: 3}

// Objects by reference.
var arr = Array(3).fill({}) // [{}, {}, {}];
arr[0].hi = "hi"; // [{ hi: "hi" }, { hi: "hi" }, { hi: "hi" }]
```

---

#### Polyfill

```javascript
if (!Array.prototype.fill) {
  Object.defineProperty(Array.prototype, 'fill', {
    value: function(value) {

      // Steps 1-2.
      if (this == null) {
        throw new TypeError('this is null or not defined');
      }

      var O = Object(this);

      // Steps 3-5.
      var len = O.length >>> 0;

      // Steps 6-7.
      var start = arguments[1];
      var relativeStart = start >> 0;

      // Step 8.
      var k = relativeStart < 0 ?
        Math.max(len + relativeStart, 0) :
        Math.min(relativeStart, len);

      // Steps 9-10.
      var end = arguments[2];
      var relativeEnd = end === undefined ?
        len : end >> 0;

      // Step 11.
      var final = relativeEnd < 0 ?
        Math.max(len + relativeEnd, 0) :
        Math.min(relativeEnd, len);

      // Step 12.
      while (k < final) {
        O[k] = value;
        k++;
      }

      // Step 13.
      return O;
    }
  });
}
```

如果你确实需要维护已过时的不支持 `Object.defineProperty` 的 `JavaScript` 引擎，那么最好完全不向 `Array.prototype` 添加方法，因为你不能使它不可枚举。

---

## 内部数据展平

### [flat()][flat]

#### 基础语法

```javascript
var newArray = arr.flat(depth)
```

---

#### 参数说明

> depth

指定要提取`嵌套数组`的结构深度，默认值为 `1`。

---

#### 返回值说明

一个包含将`数组`与`子数组`中所有元素的`新数组`。

---

#### 详细说明

`flat()` 方法会按照一个可指定的`深度递归`遍历数组，并将所有元素与遍历到的`子数组`中的元素合并为一个`新数组`返回。

---

#### 案例

> 扁平化嵌套数组

```javascript
var arr1 = [1, 2, [3, 4]];
arr1.flat();
// [1, 2, 3, 4]

var arr2 = [1, 2, [3, 4, [5, 6]]];
arr2.flat();
// [1, 2, 3, 4, [5, 6]]

var arr3 = [1, 2, [3, 4, [5, 6]]];
arr3.flat(2);
// [1, 2, 3, 4, 5, 6]

//使用 Infinity 作为深度，展开任意深度的嵌套数组
arr3.flat(Infinity);
// [1, 2, 3, 4, 5, 6]
```

> 扁平化与空项

`flat()` 方法会移除数组中的`空项`，但是不会剔除`''`, `undefined`, 和`null`。

```javascript
(()=>{
  const {log} = console;
  var arr4 = [1, 2, , '', undefined, null, 4, 5];
  log(arr4.flat());// [1, 2, "", undefined, null, 4, 5]
})()
```

---

#### 替代方案

使用 `reduce` 与 `concat`模拟`flat`

```javascript
var arr1 = [1, 2, [3, 4]];
arr1.flat();

// 反嵌套一层数组
arr1.reduce((acc, val) => acc.concat(val), []);// [1, 2, 3, 4]

// 或使用 ...
const flatSingle = arr => [].concat(...arr);

// 使用 reduce、concat 和递归无限反嵌套多层嵌套的数组
var arr1 = [1,2,3,[1,2,3,4, [2,3,4]]];

function flattenDeep(arr1) {
   return arr1.reduce((acc, val) => Array.isArray(val) ? acc.concat(flattenDeep(val)) : acc.concat(val), []);
}
flattenDeep(arr1);
// [1, 2, 3, 1, 2, 3, 4, 2, 3, 4]

// 不使用递归，使用 stack 无限反嵌套多层嵌套数组
var arr1 = [1,2,3,[1,2,3,4, [2,3,4]]];
function flatten(input) {
  const stack = [...input];
  const res = [];
  while (stack.length) {
    // 使用 pop 从 stack 中取出并移除值
    const next = stack.pop();
    if (Array.isArray(next)) {
      // 使用 push 送回内层数组中的元素，不会改动原始输入 original input
      stack.push(...next);
    } else {
      res.push(next);
    }
  }
  // 使用 reverse 恢复原数组的顺序
  return res.reverse();
}
flatten(arr1);// [1, 2, 3, 1, 2, 3, 4, 2, 3, 4]
```

---

### [flatMap()][flatMap]

#### 基础语法

```javascript
var new_array = arr.flatMap(function callback(currentValue[, index[, array]]) {
    // 返回新数组的元素
}[, thisArg])
```

---

#### 参数说明

> callback

在数组每一项上执行的函数，接收 3 个参数：

- currentValue

当前正在数组中处理的元素

---

- index

当前遍历到的索引。

---

- array

数组本身。

---

> thisArg 可选

可选，指定 `callback` 的 `this` 参数。

---

#### 返回值说明

一个新的数组，其中每个元素都是回调函数的结果，并且结构深度 `depth` 值为`1`。

---

#### 详细说明

`flatMap()` 方法首先使用映射函数映射每个元素，然后将结果压缩成一个新数组。

它与 `map` 和 深度值`1`的 `flat` 几乎相同，但 `flatMap` 通常在合并成一种方法的效率稍微高一些。

有关回调函数的详细描述，请参见 [Array.prototype.map()][map] 。 `flatMap` 方法与 [`map`][map] 方法和深度`depth`为`1`的 [`flat`][flat] 几乎相同.

---

#### 案例

> `Map` 与 `flatMap`

```javascript
var arr1 = [1, 2, 3, 4];

arr1.map(x => [x * 2]);
// [[2], [4], [6], [8]]

arr1.flatMap(x => [x * 2]);
// [2, 4, 6, 8]

// 只会将 flatMap 中的函数返回的数组 “压平” 一层
arr1.flatMap(x => [[x * 2]]);
// [[2], [4], [6], [8]]

```

---

> `flat` 与 `flatMap`

`flatMap`方法对原数组的每个成员执行一个函数（相当于执行`Array.prototype.map()`），然后对返回值组成的数组执行`flat()`方法。该方法返回一个新数组，不改变原数组。

```javascript
// 相当于 [[2, 4, 4], [3, 6, 9], [4, 8, 16]].flat()
[2, 3, 4].flatMap((x) => [x, x * 2, x * x])
//  [2, 4, 4, 3, 6, 9, 4, 8, 16]
```

---

> 包含几句话的数组拆分成单个汉字组成的新数组

```javascript
let arr = ["今天天气不错", "", "早上好"]

arr.map(s => s.split(""))
// [["今", "天", "天", "气", "不", "错"],[""],["早", "上", "好"]]

arr.flatMap(s => s.split(''));
// 相当于 [["今", "天", "天", "气", "不", "错", [], ["早", "上", "好"]].flat()
// ["今", "天", "天", "气", "不", "错", "早", "上", "好"]
```

---

#### 替代方案

归纳（`reduce`） 与 合并（`concat`）节

```javascript
var arr1 = [1, 2, 3, 4];

arr1.flatMap(x => [x * 2]);
// 等价于
arr1.reduce((acc, x) => acc.concat([x * 2]), []);
// [2, 4, 6, 8]
```

---

## 遍历判断

### [some()][some]

#### 基础语法

```javascript
arr.some(callback(element[, index[, array]])[, thisArg])
```

---

#### 参数说明

> callback

在数组每一项上执行的函数，接收 3 个参数：

- element

当前遍历到的元素。

---

- index

当前遍历到的索引。

---

- array

数组本身。

---

> thisArg 可选

可选，指定 `callback` 的 `this` 参数。

---

#### 返回值说明

如果回调函数返回至少一个数组元素的`truthy`值，则返回`true`；否则为`false`。

注意：对于空数组上的任何条件，此方法返回`false`。

---

#### 详细说明

`some()` 方法测试是否至少有一个元素可以通过被提供的函数方法。该方法返回一个`Boolean`类型的值。

`some()` 为数组中的每一个元素执行一次 `callback` 函数，直到找到一个使得 `callback` 返回一个`真值`（即可转换为布尔值 `true` 的值）。

如果找到了这样一个值，`some()` 将会立即返回 `true`。

否则，`some()` 返回 `false`。`callback` 只会在那些`有值`的索引上被调用，不会在那些被删除或从来未被赋值的索引上调用。

`callback` 被调用时传入三个参数：元素的`值`，元素的`索引`，被遍历的`数组`。

将会把它传给被调用的 `callback`，作为 `this` 值。否则，在非严格模式下将会是全局对象，严格模式下是 `undefined`。

`some()` 被调用时不会改变数组。

`some()` 遍历的元素的范围在第一次调用 `callback`. 时就已经确定了。

在调用 `some()` 后被添加到数组中的值不会被 `callback` 访问到。

如果数组中存在且还未被访问到的元素被 `callback` 改变了，则其传递给 `callback` 的值是 `some()` 访问到它那一刻的值。

---

#### 案例

> 测试数组元素的值

下面的例子检测在数组中是否有元素大于 `10`。

```javascript
function isBiggerThan10(element, index, array) {
  return element > 10;
}

[2, 5, 8, 1, 4].some(isBiggerThan10);  // false
[12, 5, 8, 1, 4].some(isBiggerThan10); // true

// 箭头函数 可以通过更简洁的语法实现相同的用例.
[2, 5, 8, 1, 4].some(x => x > 10);  // false
[12, 5, 8, 1, 4].some(x => x > 10); // true
```

---

> 判断数组元素中是否存在某个值

此例中为模仿 `includes()`  方法, 若元素在数组中存在, 则回调函数返回值为 `true` :

```javascript
var fruits = ['apple', 'banana', 'mango', 'guava'];

function checkAvailability(arr, val) {
  return arr.some(function(arrVal) {
    return val === arrVal;
  });
}

checkAvailability(fruits, 'kela');   // false
checkAvailability(fruits, 'banana'); // true
```

---

> 使用箭头函数判断数组元素中是否存在某个值

```javascript
var fruits = ['apple', 'banana', 'mango', 'guava'];

function checkAvailability(arr, val) {
  return arr.some(arrVal => val === arrVal);
}

checkAvailability(fruits, 'kela');   // false
checkAvailability(fruits, 'banana'); // true
```

---

> 将任意值转换为布尔类型

```javascript
var TRUTHY_VALUES = [true, 'true', 1];

function getBoolean(value) {
  'use strict';

  if (typeof value === 'string') {
    value = value.toLowerCase().trim();
  }

  return TRUTHY_VALUES.some(function(t) {
    return t === value;
  });
}

getBoolean(false);   // false
getBoolean('false'); // false
getBoolean(1);       // true
getBoolean('true');  // true
```

---

#### Polyfill

在第 5 版时，`some()` 被添加进 `ECMA-262` 标准；这样导致某些实现环境可能不支持它。

你可以把下面的代码插入到脚本的开头来解决此问题，从而允许在那些没有原生支持它的实现环境中使用它。该算法是 `ECMA-262` 第 5 版中指定的算法，假定 `Object` 和 `TypeError` 拥有他们的初始值，且 `fun.call` 等价于 `Function.prototype.call`。

```javascript
// Production steps of ECMA-262, Edition 5, 15.4.4.17
// Reference: http://es5.github.io/#x15.4.4.17
if (!Array.prototype.some) {
  Array.prototype.some = function(fun/*, thisArg*/) {
    'use strict';

    if (this == null) {
      throw new TypeError('Array.prototype.some called on null or undefined');
    }

    if (typeof fun !== 'function') {
      throw new TypeError();
    }

    var t = Object(this);
    var len = t.length >>> 0;

    var thisArg = arguments.length >= 2 ? arguments[1] : void 0;
    for (var i = 0; i < len; i++) {
      if (i in t && fun.call(thisArg, t[i], i, t)) {
        return true;
      }
    }

    return false;
  };
}
```

---

### [every()][every]

#### 基础语法

```javascript
arr.some(callback(element[, index[, array]])[, thisArg])
```

---

#### 参数说明

> callback

在数组每一项上执行的函数，接收 3 个参数：

- element

当前遍历到的元素。

---

- index

当前遍历到的索引。

---

- array

数组本身。

---

> thisArg 可选

可选，指定 `callback` 的 `this` 参数。

---

#### 返回值说明

如果回调函数的每一次返回都为 `truthy` 值，返回 `true` ，否则返回 `false`。

若收到一个空数组，此方法在一切情况下都会返回 `true`。

`every` 和数学中的`所有`类似，当所有的元素都符合条件才会返回`true`。正因如此，若传入一个`空数组`，无论如何都会返回 `true`。（这种情况属于`无条件正确`：正因为一个`空集合`没有`元素`，所以它其中的所有`元素`都符合给定的条件。)

---

#### 详细说明

`every()` 方法测试一个数组内的所有元素是否都能通过某个指定函数的测试。它返回一个布尔值。

`every` 方法为数组中的每个元素执行一次 `callback` 函数，直到它找到一个会使 `callback` 返回 `falsy` 的元素。

如果发现了一个这样的元素，`every` 方法将会立即返回 `false`。否则，`callback` 为每一个元素返回 `true`，`every` 就会返回 `true`。`callback` 只会为那些已经被赋值的索引调用。

不会为那些被删除或从未被赋值的索引调用。

`callback` 在被调用时可传入三个参数：`元素值`，元素的`索引`，`原数组`。

如果为 `every` 提供一个 `thisArg` 参数，则该参数为调用 `callback` 时的 `this` 值。如果省略该参数，则 `callback` 被调用时的 `this` 值，在非严格模式下为全局对象，在严格模式下传入 `undefined`。详见 [this][this] 条目。

`every` 不会改变原数组。

`every` 遍历的元素范围在第一次调用 `callback` 之前就已确定了。在调用 `every` 之后添加到数组中的元素不会被 `callback` 访问到。

如果数组中存在的元素被更改，则他们传入 `callback` 的值是 `every` 访问到他们那一刻的值。

那些被删除的元素或从来未被赋值的元素将不会被访问到。

---

#### 案例

> 检测所有数组元素的大小

下例检测数组中的所有元素是否都大于 10。

```javascript
function isBigEnough(element, index, array) {
  return element >= 10;
}
[12, 5, 8, 130, 44].every(isBigEnough);   // false
[12, 54, 18, 130, 44].every(isBigEnough); // true
```

> 使用箭头函数

箭头函数为上面的检测过程提供了更简短的语法。

```javascript
[12, 5, 8, 130, 44].every(x => x >= 10); // false
[12, 54, 18, 130, 44].every(x => x >= 10); // true
```

---

#### Polyfill

在 `ECMA-262` 第 5 版时，`every` 被添加进 `ECMA-262` 标准；因此，在某些实现环境中，它尚未被支持。你可以把下面的代码放到脚本的开头来解决此问题，该代码允许在那些没有原生支持 `every` 的实现环境中使用它。该算法是 `ECMA-262` 第 5 版中指定的算法，它假定 `Object` 和 `TypeError` 拥有它们的初始值，且 fun.call 等价于 `Function.prototype.call`。

```javascript
if (!Array.prototype.every) {
  Array.prototype.every = function(callbackfn, thisArg) {
    'use strict';
    var T, k;

    if (this == null) {
      throw new TypeError('this is null or not defined');
    }

    // 1. Let O be the result of calling ToObject passing the this 
    //    value as the argument.
    var O = Object(this);

    // 2. Let lenValue be the result of calling the Get internal method
    //    of O with the argument "length".
    // 3. Let len be ToUint32(lenValue).
    var len = O.length >>> 0;

    // 4. If IsCallable(callbackfn) is false, throw a TypeError exception.
    if (typeof callbackfn !== 'function') {
      throw new TypeError();
    }

    // 5. If thisArg was supplied, let T be thisArg; else let T be undefined.
    if (arguments.length > 1) {
      T = thisArg;
    }

    // 6. Let k be 0.
    k = 0;

    // 7. Repeat, while k < len
    while (k < len) {

      var kValue;

      // a. Let Pk be ToString(k).
      //   This is implicit for LHS operands of the in operator
      // b. Let kPresent be the result of calling the HasProperty internal 
      //    method of O with argument Pk.
      //   This step can be combined with c
      // c. If kPresent is true, then
      if (k in O) {

        // i. Let kValue be the result of calling the Get internal method
        //    of O with argument Pk.
        kValue = O[k];

        // ii. Let testResult be the result of calling the Call internal method
        //     of callbackfn with T as the this value and argument list
        //     containing kValue, k, and O.
        var testResult = callbackfn.call(T, kValue, k, O);

        // iii. If ToBoolean(testResult) is false, return false.
        if (!testResult) {
          return false;
        }
      }
      k++;
    }
    return true;
  };
}
```

---

### [reduce()][reduce]

#### 基础语法

```javascript
arr.reduce(callback[initialValue])
```

---

#### 参数说明

> callback

执行数组中每个值的函数，包含四个参数：

- accumulator

  累计器累计回调的返回值; 它是上一次调用回调时返回的累积值，或`initialValue`（见于下方）。

- currentValue

  数组中正在处理的元素。

- currentIndex 可选

  数组中正在处理的当前元素的索引。 如果提供了`initialValue`，则起始索引号为`0`，否则为`1`。

- array 可选

  调用`reduce()`的数组

> `initialValue` 可选

作为第一次调用 `callback`函数时的第一个参数的值。 如果没有提供初始值，则将使用数组中的第一个元素。 在没有初始值的空数组上调用 `reduce` 将报错。

---

#### 返回值说明

函数累计处理的结果

---

#### 详细说明

`reduce()` 方法对数组中的每个元素执行一个由您提供的`reducer`函数(`升序执行`)，将其结果汇总为单个返回值。

`reduce`为数组中的每一个元素依次执行`callback`函数，不包括数组中`被删除`或`从未被赋值`的元素。

`reducer` 函数接收`4`个参数:

- accumulator (acc) (累计器)
- Current Value (cur) (当前值)
- Current Index (idx) (当前索引)
- Source Array (src) (源数组)

`reducer` 函数的`返回值`分配给`累计器`，该返回值在数组的每个`迭代`中被记住，并最后成为最终的`单个`结果值。

回调函数第一次执行时，`accumulator` 和`currentValue`的取值有两种情况：如果调用`reduce()`时提供了`initialValue`，`accumulator`取值为`initialValue`，`currentValue`取数组中的第一个值；如果没有提供 `initialValue`，那么`accumulator`取数组中的第一个值，`currentValue`取数组中的第二个值。

注意：如果没有提供`initialValue`，reduce 会从索引1的地方开始执行 callback 方法，跳过第一个索引。如果提供`initialValue`，从索引0开始。

如果数组为空且没有提供`initialValue`，会抛出TypeError 。如果数组仅有一个元素（无论位置如何）并且没有提供`initialValue`， 或者有提供`initialValue`但是数组为空，那么此唯一值将被返回并且callback不会被执行。

提供初始值通常更安全，正如下面的例子，如果没有提供`initialValue`，则可能有三种输出：

```javascript
var maxCallback = ( acc, cur ) => Math.max( acc.x, cur.x );
var maxCallback2 = ( max, cur ) => Math.max( max, cur );

// reduce() 没有初始值
[ { x: 22 }, { x: 42 } ].reduce( maxCallback ); // 42
[ { x: 22 }            ].reduce( maxCallback ); // { x: 22 }
[                      ].reduce( maxCallback ); // TypeError

// map/reduce; 这是更好的方案，即使传入空数组或更大数组也可正常执行
[ { x: 22 }, { x: 42 } ].map( el => el.x )
                        .reduce( maxCallback2, -Infinity );
```

---

#### 运行说明

假如运行下段`reduce()`代码：

```javascript
[0, 1, 2, 3, 4].reduce((prev, curr) => prev + curr );
```

`callback` 被调用四次，每次调用的参数和返回值如下表：

| callback    | accumulator | currentValue | currentIndex | array           | return value |
| :---------- | :---------- | :----------- | :----------- | :-------------- | :----------- |
| first call  | 0           | 1            | 1            | [0, 1, 2, 3, 4] | 1            |
| second call | 1           | 2            | 2            | [0, 1, 2, 3, 4] | 3            |
| third call  | 3           | 3            | 3            | [0, 1, 2, 3, 4] | 6            |
| fourth call | 6           | 4            | 4            | [0, 1, 2, 3, 4] | 10           |

由`reduce`返回的值将是上次回调调用的值`（10）`。

如果你打算提供一个初始值作为`reduce()`方法的第二个参数，以下是运行过程及结果：

```javascript
[0, 1, 2, 3, 4].reduce((accumulator, currentValue, currentIndex, array) => { return accumulator + currentValue; }, 10 );
```

| callback    | accumulator | currentValue | currentIndex | array           | return value |
| :---------- | :---------- | :----------- | :----------- | :-------------- | :----------- |
| first call  | 10          | 0            | 0            | [0, 1, 2, 3, 4] | 10           |
| second call | 10          | 1            | 1            | [0, 1, 2, 3, 4] | 11           |
| third call  | 11          | 2            | 2            | [0, 1, 2, 3, 4] | 13           |
| fourth call | 13          | 3            | 3            | [0, 1, 2, 3, 4] | 16           |
| fifth call  | 16          | 4            | 4            | [0, 1, 2, 3, 4] | 20           |

这种情况下`reduce()`返回的值是`20`。

---

#### 案例

> 数组里所有值的和

```javascript
var total = [ 0, 1, 2, 3 ].reduce(
    ( acc, cur ) => acc + cur,
  0
);
// 和为 6
```

---

> 累加对象数组里的值

要累加对象数组中包含的值，必须提供初始值，以便各个`item`正确通过你的函数。

```javascript
var initialValue = 0;
var sum = [{x: 1}, {x:2}, {x:3}].reduce(
    (accumulator, currentValue) => accumulator + currentValue.x
    ,initialValue
);

console.log(sum) // logs 6
```

---

> 将二维数组转化为一维

```javascript
var flattened = [[0, 1], [2, 3], [4, 5]].reduce(
 ( acc, cur ) => acc.concat(cur),
 []
);
// flattened is [0, 1, 2, 3, 4, 5]
```

---

> 计算数组中每个元素出现的次数

```javascript
var names = ['Alice', 'Bob', 'Tiff', 'Bruce', 'Alice'];

var countedNames = names.reduce(function (allNames, name) { 
  if (name in allNames) {
    allNames[name]++;
  }
  else {
    allNames[name] = 1;
  }
  return allNames;
}, {});
// countedNames is:
// { 'Alice': 2, 'Bob': 1, 'Tiff': 1, 'Bruce': 1 }
```

---

> 按属性对`object`分类

```javascript
var people = [
  { name: 'Alice', age: 21 },
  { name: 'Max', age: 20 },
  { name: 'Jane', age: 20 }
];

function groupBy(objectArray, property) {
  return objectArray.reduce(function (acc, obj) {
    var key = obj[property];
    if (!acc[key]) {
      acc[key] = [];
    }
    acc[key].push(obj);
    return acc;
  }, {});
}

var groupedPeople = groupBy(people, 'age');
// groupedPeople is:
// {
//   20: [
//     { name: 'Max', age: 20 },
//     { name: 'Jane', age: 20 }
//   ],
//   21: [{ name: 'Alice', age: 21 }]
// }
```

---

> 使用扩展运算符和`initialValue`绑定包含在对象数组中的数组

```javascript
// friends - 对象数组
// where object field "books" - list of favorite books 
var friends = [{
  name: 'Anna',
  books: ['Bible', 'Harry Potter'],
  age: 21
}, {
  name: 'Bob',
  books: ['War and peace', 'Romeo and Juliet'],
  age: 26
}, {
  name: 'Alice',
  books: ['The Lord of the Rings', 'The Shining'],
  age: 18
}];

// allbooks - list which will contain all friends' books +  
// additional list contained in initialValue
var allbooks = friends.reduce(function(prev, curr) {
  return [...prev, ...curr.books];
}, ['Alphabet']);

// allbooks = [
//   'Alphabet', 'Bible', 'Harry Potter', 'War and peace', 
//   'Romeo and Juliet', 'The Lord of the Rings',
//   'The Shining'
// ]
```

---

> 数组去重节

```javascript
let arr = [1,2,1,2,3,5,4,5,3,4,4,4,4];
let result = arr.sort().reduce((init, current)=>{
    if(init.length===0 || init[init.length-1]!==current){
        init.push(current);
    }
    return init;
}, []);
console.log(result); //[1,2,3,4,5]
```

---

> 按顺序运行Promise节

```javascript
/**
 * Runs promises from array of functions that can return promises
 * in chained manner
 *
 * @param {array} arr - promise arr
 * @return {Object} promise object
 */
function runPromiseInSequence(arr, input) {
  return arr.reduce(
    (promiseChain, currentFunction) => promiseChain.then(currentFunction),
    Promise.resolve(input)
  );
}

// promise function 1
function p1(a) {
  return new Promise((resolve, reject) => {
    resolve(a * 5);
  });
}

// promise function 2
function p2(a) {
  return new Promise((resolve, reject) => {
    resolve(a * 2);
  });
}

// function 3  - will be wrapped in a resolved promise by .then()
function f3(a) {
 return a * 3;
}

// promise function 4
function p4(a) {
  return new Promise((resolve, reject) => {
    resolve(a * 4);
  });
}

const promiseArr = [p1, p2, f3, p4];
runPromiseInSequence(promiseArr, 10)
  .then(console.log);   // 1200
```

---

> 功能型函数管道节

```javascript
// Building-blocks to use for composition
const double = x => x + x;
const triple = x => 3 * x;
const quadruple = x => 4 * x;

// Function composition enabling pipe functionality
const pipe = (...functions) => input => functions.reduce(
    (acc, fn) => fn(acc),
    input
);

// Composed functions for multiplication of specific values
const multiply6 = pipe(double, triple);
const multiply9 = pipe(triple, triple);
const multiply16 = pipe(quadruple, quadruple);
const multiply24 = pipe(double, triple, quadruple);

// Usage
multiply6(6); // 36
multiply9(9); // 81
multiply16(16); // 256
multiply24(10); // 240
```

---

#### Polyfill

如果您需要兼容不支持`Object.defineProperty`的`JavaScript`引擎，那么最好不要 `polyfill` `Array.prototype`方法，因为你无法使其成为`不可枚举`的。

```javascript
// Production steps of ECMA-262, Edition 5, 15.4.4.21
// Reference: http://es5.github.io/#x15.4.4.21
// https://tc39.github.io/ecma262/#sec-array.prototype.reduce
if (!Array.prototype.reduce) {
  Object.defineProperty(Array.prototype, 'reduce', {
    value: function(callback /*, initialValue*/) {
      if (this === null) {
        throw new TypeError( 'Array.prototype.reduce ' + 
          'called on null or undefined' );
      }
      if (typeof callback !== 'function') {
        throw new TypeError( callback +
          ' is not a function');
      }

      // 1. Let O be ? ToObject(this value).
      var o = Object(this);

      // 2. Let len be ? ToLength(? Get(O, "length")).
      var len = o.length >>> 0; 

      // Steps 3, 4, 5, 6, 7      
      var k = 0; 
      var value;

      if (arguments.length >= 2) {
        value = arguments[1];
      } else {
        while (k < len && !(k in o)) {
          k++; 
        }

        // 3. If len is 0 and initialValue is not present,
        //    throw a TypeError exception.
        if (k >= len) {
          throw new TypeError( 'Reduce of empty array ' +
            'with no initial value' );
        }
        value = o[k++];
      }

      // 8. Repeat, while k < len
      while (k < len) {
        // a. Let Pk be ! ToString(k).
        // b. Let kPresent be ? HasProperty(O, Pk).
        // c. If kPresent is true, then
        //    i.  Let kValue be ? Get(O, Pk).
        //    ii. Let accumulator be ? Call(
        //          callbackfn, undefined,
        //          « accumulator, kValue, k, O »).
        if (k in o) {
          value = callback(value, o[k], k, o);
        }

        // d. Increase k by 1.      
        k++;
      }

      // 9. Return accumulator.
      return value;
    }
  });
}
```

---

### [reduceRight()][reduceRight]

#### 基础语法

```javascript
arr.reduceRight(callback[, initialValue])
```

---

#### 参数说明

> callback

执行数组中每个值的函数，包含四个参数：

- previousValue

    上一次调用回调的返回值，或提供的 `initialValue`（见于下方）。

- currentValue

  数组中正在处理的元素。

- currentIndex

  数组中正在处理的当前元素的索引。 如果提供了`initialValue`，则起始索引号为`0`，否则为`1`。

- array 可选

  调用`reduceRight()`的数组

> `initialValue` 可选

可作为第一次调用回调 `callback` 的第一个参数

---

#### 返回值说明

执行之后的返回值

---

#### 详细说明

`reduceRight()` 方法接受一个函数作为累加器（`accumulator`）和数组的每个值（从右到左）将其减少为单个值。

`reduceRight` 为数组中每个元素调用一次 `callback`回调函数，但是数组中`被删除`的索引或`从未被赋值`的索引会跳过。

回调函数接受四个参数：`初始值（或上次调用回调的返回值）`、`当前元素值`、`当前索引`，以及`调用 reduceRight 的数组`。

可以像下面这样调用 `reduceRight` 的回调函数 `callback`

```javascript
array.reduceRight(function(previousValue, currentValue, index, array) {
    // ...
});
```

首次调用回调函数时，`previousValue` 和 `currentValue` 可以是两个值之一。

如果调用 `reduceRight` 时提供了 `initialValue` 参数，则 `previousValue` 等于 `initialValue`，`currentValue` 等于数组中的最后一个值。

如果没有提供 `initialValue` 参数，则 `previousValue` 等于数组最后一个值， `currentValue` 等于数组中倒数第二个值。

如果数组为空，且没有提供 `initialValue` 参数，将会抛出一个 `TypeError` 错误。如果数组只有一个元素且没有提供 `initialValue` 参数，或者提供了 `initialValue` 参数，但是数组为空将会直接返回数组中的那一个元素或 `initialValue` 参数，而不会调用 `callback`。

---

#### 运行说明

假如运行下段`reduceRight()`代码：

```javascript
[0, 1, 2, 3, 4].reduceRight((previousValue, currentValue, index, array) => previousValue + currentValue );
```

`callback` 被调用四次，每次调用的参数和返回值如下表：

| callback    | previousValue | currentValue | index | array       | return value |
| :---------- | :------------ | :----------- | :---- | :---------- | :----------- |
| first call  | 4             | 3            | 3     | [0,1,2,3,4] | 7            |
| second call | 7             | 2            | 2     | [0,1,2,3,4] | 9            |
| third call  | 9             | 1            | 1     | [0,1,2,3,4] | 10           |
| fourth call | 10            | 0            | 0     | [0,1,2,3,4] | 10           |

由`reduceRight`返回的值将是上次回调调用的值`（10）`。

如果你打算提供一个初始值作为`reduceRight ()`方法的第二个参数，以下是运行过程及结果：

```javascript
[0, 1, 2, 3, 4]..reduceRight((previousValue, currentValue, index, array) => previousValue + currentValue , 10 );
```

| callback    | previousValue | currentValue | index | array       | return value |
| :---------- | :------------ | :----------- | :---- | :---------- | :----------- |
| first call  | 10            | 4            | 4     | [0,1,2,3,4] | 14           |
| second call | 14            | 3            | 3     | [0,1,2,3,4] | 17           |
| third call  | 17            | 2            | 2     | [0,1,2,3,4] | 19           |
| fourth call | 19            | 1            | 1     | [0,1,2,3,4] | 20           |
| fifth call  | 20            | 0            | 0     | [0,1,2,3,4] | 20           |

这种情况下`reduceRight()`返回的值是`20`。

---

#### 案例

> 求一个数组中所有值的和节

```javascript
const total = [0, 1, 2, 3].reduceRight(function(a, b) {
    return a + b;
});
// total == 6
```

---

> 扁平化（flatten）一个元素为数组的数组

```javascript
const flattened = [[0, 1], [2, 3], [4, 5]].reduceRight(function(a, b) {
    return a.concat(b);
}, []);
// flattened is [4, 5, 2, 3, 0, 1]
```

---

> `reduce` 与 `reduceRight` 之间的区别

```javascript
var a = ['1', '2', '3', '4', '5'];
var left  = a.reduce(function(prev, cur)      { return prev + cur; });
var right = a.reduceRight(function(prev, cur) { return prev + cur; });

console.log(left);  // "12345"
console.log(right); // "54321"
```

---

#### Polyfill

如果您需要兼容不支持`Object.defineProperty`的`JavaScript`引擎，那么最好不要 `polyfill` `Array.prototype`方法，因为你无法使其成为`不可枚举`的。

```javascript
// Production steps of ECMA-262, Edition 5, 15.4.4.21
// Reference: http://es5.github.io/#x15.4.4.21
// https://tc39.github.io/ecma262/#sec-array.prototype.reduce
if (!Array.prototype.reduceRight) {
  Object.defineProperty(Array.prototype, 'reduceRight', {
    value: function(callback /*, initialValue*/) {
    'use strict';
    if (null === this || 'undefined' === typeof this) {
      throw new TypeError('Array.prototype.reduceRight called on null or undefined');
    }
    if ('function' !== typeof callback) {
      throw new TypeError(callback + ' is not a function');
    }
    var t = Object(this), len = t.length >>> 0, k = len - 1, value;
    if (arguments.length >= 2) {
      value = arguments[1];
    } else {
      while (k >= 0 && !(k in t)) {
        k--;
      }
      if (k < 0) {
        throw new TypeError('reduceRight of empty array with no initial value');
      }
      value = t[k--];
    }
    for (; k >= 0; k--) {
      if (k in t) {
        value = callback(value, t[k], k, t);
      }
    }
    return value;
  });
}
```

---

### [entries()][entries]

#### 基础语法

```javascript
arr.entries()
```

---

#### 返回值说明

一个新的 `Array` 迭代器对象。`Array Iterator`是对象，它的原型（`__proto__:Array Iterator`）上有一个`next`方法，可用用于遍历迭代器取得原数组的`[key,value]`。

---

#### 详细说明

`entries()` 方法返回一个新的`Array Iterator`对象，该对象包含数组中每个索引的`键/值对`。

---

#### 案例

> Array Iterator

```javascript

var arr = ["a", "b", "c"];
var iterator = arr.entries();
console.log(iterator);

/*Array Iterator {}
         __proto__:Array Iterator
         next:ƒ next()
         Symbol(Symbol.toStringTag):"Array Iterator"
         __proto__:Object
*/
```

---

> iterator.next()

```javascript

var arr = ["a", "b", "c"];
var iterator = arr.entries();
console.log(iterator.next());

/*{value: Array(2), done: false}
          done:false
          value:(2) [0, "a"]
           __proto__: Object
*/
// iterator.next()返回一个对象，对于有元素的数组，
// 是next{ value: Array(2), done: false }；
// next.done 用于指示迭代器是否完成：在每次迭代时进行更新而且都是false，
// 直到迭代器结束done才是true。
// next.value是一个["key":"value"]的数组，是返回的迭代器中的元素值。
```

---

> iterator.next方法运行

```javascript

var arr = ["a", "b", "c"];
var iter = arr.entries();
var a = [];

// for(var i=0; i< arr.length; i++){   // 实际使用的是这个 
for(var i=0; i< arr.length+1; i++){    // 注意，是length+1，比数组的长度大
    var tem = iter.next();             // 每次迭代时更新next
    console.log(tem.done);             // 这里可以看到更新后的done都是false
    if(tem.done !== true){             // 遍历迭代器结束done才是true
        console.log(tem.value);
        a[i]=tem.value;
    }
}

console.log(a);                      // 遍历完毕，输出next.value的数组
```

---

> 二维数组按行排序

```javascript

function sortArr(arr) {
    var goNext = true;
    var entries = arr.entries();
    while (goNext) {
        var result = entries.next();
        if (result.done !== true) {
            result.value[1].sort((a, b) => a - b);
            goNext = true;
        } else {
            goNext = false;
        }
    }
    return arr;
}

var arr = [[1,34],[456,2,3,44,234],[4567,1,4,5,6],[34,78,23,1]];
sortArr(arr);

/*(4) [Array(2), Array(5), Array(5), Array(4)]
    0:(2) [1, 34]
    1:(5) [2, 3, 44, 234, 456]
    2:(5) [1, 4, 5, 6, 4567]
    3:(4) [1, 23, 34, 78]
    length:4
    __proto__:Array(0)
*/
```

---

> 使用`for…of` 循环

```javascript

var arr = ["a", "b", "c"];
var iterator = arr.entries();
// undefined

for (let e of iterator) {
    console.log(e);
}

// [0, "a"]
// [1, "b"]
// [2, "c"]
```

---

### [keys()][keys]

#### 基础语法

```javascript
arr.keys()
```

---

#### 返回值说明

一个新的 `Array` 迭代器对象。`Array Iterator`是对象

---

#### 详细说明

 `keys()` 方法返回一个包含数组中每个`索引键`的`Array Iterator`对象。

---

#### 案例

> 索引迭代器会包含那些没有对应元素的索引节

```javascript

var arr = ["a", , "c"];
var sparseKeys = Object.keys(arr);
var denseKeys = [...arr.keys()];
console.log(sparseKeys); // ['0', '2']
console.log(denseKeys);  // [0, 1, 2]
```

---

### [values()][values]

#### 基础语法

```javascript
arr.values()
```

---

#### 返回值说明

一个新的 `Array` 迭代器对象。`Array Iterator`是对象

---

#### 详细说明

 `values()` 方法返回一个包含数组每个索引的`值`的`Array Iterator`对象。

---

#### 案例

> 使用 `for...of` 循环进行迭代

```javascript
let arr = ['w', 'y', 'k', 'o', 'p'];
let eArr = arr.values();
// 您的浏览器必须支持 for..of 循环
// 以及 let —— 将变量作用域限定在 for 循环中
for (let letter of eArr) {
  console.log(letter);
}
```

> 另一种迭代方式

```javascript
let arr = ['w', 'y', 'k', 'o', 'p'];
let eArr = arr.values();
console.log(eArr.next().value); // w
console.log(eArr.next().value); // y
console.log(eArr.next().value); // k
console.log(eArr.next().value); // o
console.log(eArr.next().value); // p
```

---

[copyWithin]: https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array/copyWithin
[find]: https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array/find
[fill]: https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array/fill
[findIndex]: https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array/findIndex
[includes]: https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array/includes
[flat]: https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array/flat
[flatMap]: https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array/flatMap
[some]: https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array/some
[every]: https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array/every
[reduce]: https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array/reduce
[reduceRight]: https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array/reduceRight
[entries]: https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array/entries
[keys]: https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array/keys
[values]: https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array/values
[this]: https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/this
[map]: https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/map
[sameValueZero]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Equality_comparisons_and_sameness#Same-value-zero_equality
