# 数组的空位处理

## 数组空位说明

数组的`空位`指，数组的某一个位置没有任何值，比如 `Array` 构造函数返回的数组都是`空位`。

```javascript
Array(3)// [,,,]
```

上面代码中，`Array(3)`返回一个具有三个`空位`的数组

注意，`空位`不是`undefined`，一个位置的值等于`undefined`，依然是有值的。

`空位`是没有任何值，`in`运算符可以说明这一点。

```javascript
0 in [undefined, undefined, undefined] // true
0 in [, , ,] // false
```

上面代码说明，第一个数组的 `0` 号位置是有值的，第二个数组的 `0` 号位置没有值。

由于`空位`的处理规则非常不统一，所以建议避免出现`空位`。

---

## ES5空位处理

`ES5` 对`空位`的处理，已经很不一致了，大多数情况下会忽略`空位`。

- `forEach()`, `filter()`, `reduce()`, `every()` 和`some()`都会跳过`空位`。
- `map()`会跳过`空位`，但会保留这个值。
- `join()`和`toString()`会将`空位`视为`undefined`
- `undefined`和`null`会被处理成`空字符串`。

```javascript
// forEach方法
[,'a'].forEach((x,i) => console.log(i)); // 1

// filter方法
['a',,'b'].filter(x => true) // ['a','b']

// every方法
[,'a'].every(x => x==='a') // true

// reduce方法
[1,,2].reduce((x,y) => x+y) // 3

// some方法
[,'a'].some(x => x !== 'a') // false

// map方法
[,'a'].map(x => 1) // [,1]

// join方法
[,'a',undefined,null].join('#') // "#a##"

// toString方法
[,'a',undefined,null].toString() // ",a,,"
```

---

## ES6空位处理

`ES6` 则是明确将`空位`转为`undefined`。

### Array.from

`Array.from`方法会将数组的`空位`，转为`undefined`，也就是说，这个方法不会忽略`空位`。

```javascript
Array.from(['a',,'b'])
// [ "a", undefined, "b" ]
```

---

### `...`

扩展运算符（`...`）也会将`空位`转为`undefined`。

```javascript
[...['a',,'b']]
// [ "a", undefined, "b" ]
```

---

### `copyWithin`

`copyWithin()`会连`空位`一起拷贝。

```javascript
[,'a','b',,].copyWithin(2,0) // [,"a",,"a"]
```

---

### `fill`

`fill()`会将`空位`视为正常的数组位置。

```javascript
new Array(3).fill('a') // ["a","a","a"]
```

---

### `for...of`

`for...of`循环也会遍历`空位`。

```javascript
let arr = [, ,];
for (let i of arr) {
  console.log(1);
}
// 1
// 1
```
上面代码中，数组`arr`有两个`空位`，`for...of`并没有忽略它们。如果改成`map`方法遍历，`空位`是会跳过的。


---

### 遍历实例方法

`entries()`、`keys()`、`values()`、`find()`和`findIndex()`会将`空位`处理成`undefined`。

```javascript
// entries()
[...[,'a'].entries()] // [[0,undefined], [1,"a"]]

// keys()
[...[,'a'].keys()] // [0,1]

// values()
[...[,'a'].values()] // [undefined,"a"]

// find()
[,'a'].find(x => true) // undefined

// findIndex()
[,'a'].findIndex(x => true) // 0
```
