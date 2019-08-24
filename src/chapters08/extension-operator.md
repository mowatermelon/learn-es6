# 扩展运算符

## 基本说明

扩展运算符（`spread`）是三个点（`...`）。它好比 `rest 参数`的`逆运算`，将一个`数组`转为用`逗号`分隔的参数序列。

```javascript
console.log(...[1, 2, 3])
// 1 2 3

console.log(1, ...[2, 3, 4], 5)
// 1 2 3 4 5

[...document.querySelectorAll('div')]
// [<div>, <div>, <div>]
```

---

## 使用案例

### 函数调用

该运算符主要用于`函数调用`。

```javascript
function push(array, ...items) {
  array.push(...items);
}

function add(x, y) {
  return x + y;
}

const numbers = [4, 38];
add(...numbers) // 42
```

上面代码中，`array.push(...items)`和`add(...numbers)`这两行，都是函数的调用，它们的都使用了`扩展运算符`。该运算符将一个`数组`，变为`参数序列`。

---

### 与普通参数结合

`扩展运算符`与正常的函数参数可以结合使用，非常灵活。

```javascript
function f(v, w, x, y, z) { }
const args = [0, 1];
f(-1, ...args, 2, ...[3]);
```

---

### 结合表达式

`扩展运算符`后面还可以放置`表达式`。

```javascript
const arr = [
  ...(x > 0 ? ['a'] : []),
  'b',
];
```

## 注意事项

### 空数组

如果`扩展运算符`后面是一个`空数组`，则不产生任何效果。

```javascript
[...[], 1]
// [1]
```

---

### 圆括号

注意，只有`函数调用`时，`扩展运算符`才可以放在`圆括号`中，否则会报错。

```javascript
(...[1, 2])
// Uncaught SyntaxError: Unexpected token ...

console.log((...[1, 2]))
// Uncaught SyntaxError: Unexpected token ...

console.log(...[1, 2])
// 1 2
```

上面三种情况，`扩展运算符`都放在圆括号里面，但是前两种情况会报错，因为`扩展运算符`所在的括号不是`函数调用`。
