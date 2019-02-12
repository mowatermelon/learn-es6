
# 字符串的遍历器接口

`ES6` 为`字符串`添加了`遍历器`接口（详见`《Iterator》`一章），使得`字符串`可以被`for...of`循环遍历。

```javascript
(function (log, S) {
    for (let codePoint of 'foo') {
        log(codePoint)
    }
    // "f"
    // "o"
    // "o"
})(console.log, String)
```

除了遍历字符串，这个`遍历器`最大的优点是可以识别大于`0xFFFF`的`码点`，传统的`for循环`无法识别这样的`码点`。

```javascript
(function (log, S) {
    const text = String.fromCodePoint(0x20BB7);

    for (let i = 0; i < text.length; i++) {
      log(text[i]);
    }
    // �
    // �

    for (let i of text) {
      log(i);
    }
    // "𠮷"
})(console.log, String)
```

上面代码中，字符串`text`只有一个`字符`，但是`for`循环会认为它包含两个`字符`（都不可打印），而`for...of`循环会正确`识别`出这一个`字符`。

---
