# Number 进制表示法

## 基础说明

`ES6` 提供了`二进制`和`八进制`数值的新的写法，分别用前缀`0b`（或`0B`）和`0o`（或`0O`）表示。

```javascript
(() => {
    const { log } = console;
    log(0b111110111 === 503); // true
    log(0o767 === 503); // true
})()
```

---

## 注意事项

从 `ES5` 开始，在`严格模式`之中，`八进制`就不再允许使用前缀`0`表示，`ES6` 进一步明确，要使用前缀`0o`表示。

```javascript
(() => {
    // 非严格模式
    const { log } = console;
    log(0o11 === 011); // true
})();

(() => {
    // 严格模式
    'use strict';
    const { log } = console;
    log(0o11 === 011); // Uncaught SyntaxError: Octal literals are not allowed in strict mode.
})();

```

---

## 进制转换

如果要将`0b`和`0o`前缀的字符串数值转为`十进制`，要使用`Number`方法。

```javascript
(() => {
    // 严格模式
    'use strict';
    const { log } = console;
    const octalStr = '0b111';
    const hexStr = '0o10';

    log(Number(octalStr)); // 7
    log(parseInt(octalStr, 10)); // 0

    log(Number(hexStr)); // 8
    log(parseInt(hexStr, 10)); // 0
})();
```

---