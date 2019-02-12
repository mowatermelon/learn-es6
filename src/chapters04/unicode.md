# 字符的 Unicode 表示法

## 基础说明

`JavaScript` 允许采用`\uxxxx`形式表示一个字符，其中`xxxx`表示字符的 `Unicode 码点`。

但是，这种表示法只限于`\u0000-\uFFFF`之间的字符，超过这个范围的字符，必须用两个`双字节`的形式表达。

如果直接在`\u`后面跟上超过`0xFFFF`的数值（比如`\u20BB7`），`Javascript`会理解为`\u20BB+7`，由于`\u20BB`是一个不可打印字符，所以会打印一个空格，后面跟着一个`7`。

`ES6`对于这一点做了改进，只要将超过`0xFFFF`的编号放入`大括号`，就能得到正确解读。

```javascript
(function (log) {
    log('\u0061');// 'a'

    log('\uD842\uDFB7');// 𠮷

    log('\u20BB7'); // ₻7

    log('\u{20BB7}'); // 𠮷

    log('\u{41}\u{42}\u{43}'); // ABC

    let hello = 123;
    log(hell\u{6F}); // 123

    log('\u{1F680}' === '\uD83D\uDE80');
})(console.log)
```

上面代码中，最后一个`例子`表明，`大括号表示法`与`四字节`的 `UTF-16` 编码是等价的。

---

## 字符描述举例

有了这种表示法之后，`JavaScript` 共有 `6` 种方法可以表示一个`字符`。

```javascript
(function (log) {
    log('\z' === 'z');  // true
    log('\172' === 'z'); // true
    log('\x7A' === 'z'); // true
    log('\u007A' === 'z'); // true
    log('\u{7A}' === 'z'); // true
})(console.log)
```

---
