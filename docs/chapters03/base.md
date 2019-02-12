
# 基础数据结构

## 字符串的解构赋值

### 转为数组解构

`字符串`也可以`解构赋值`。这是因为此时，`字符串`被转换成了一个`类似数组`的对象。

```javascript
(function (log) {
    const [a, b, c, d, e] = 'hello';
    log(a); // "h"
    log(b); // "e"
    log(c); // "l"
    log(d); // "l"
    log(e); // "o"

})(console.log)
```

---

### 解构字符串长度

`类似数组`的`对象`都有一个`length`属性，因此还可以对这个`属性`解构赋值。

```javascript
(function (log) {
    const {
        length: len
    } = 'hello';
    log(len); // 5

})(console.log)
```

---

## 数值和布尔值的解构赋值

`解构赋值`时，如果等号右边是`数值`和`布尔值`，则会先转为`对象`。

```javascript
(function (log) {
    const {
        toString: s1
    } = 123;
    log(s1 === Number.prototype.toString); // true

    const {
        toString: s2
    } = true;
    log(s2 === Boolean.prototype.toString); // true
})(console.log)
```

上面代码中，`数值`和`布尔值`的`包装对象`都有`toString`属性，因此变量`s1`和变量`s2`都能取到`值`。

---
