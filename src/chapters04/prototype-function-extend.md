
# 原型对象方法扩展

## fromCodePoint()

`ES5` 提供`String.fromCharCode`方法，用于从`码点`返回对应`字符`，但是这个方法不能识别 `32` 位的 `UTF-16` 字符（`Unicode` 编号大于`0xFFFF`）。

```javascript
(function (log, S) {
    log(S.fromCharCode(0x20BB7)); // ஷ
})(console.log, String)
```

上面代码中，`String.fromCharCode`不能识别大于`0xFFFF`的码点，所以`0x20BB7`就发生了`溢出`，最高位`2`被舍弃了，最后返回码点`U+0BB7`对应的`字符`，而不是码点`U+20BB7`对应的`字符`。

`ES6` 提供了`String.fromCodePoint`方法，可以识别大于`0xFFFF`的字符，弥补了`String.fromCharCode`方法的不足。在作用上，正好与`codePointAt`方法相反。

```javascript
(function (log, S) {
    log(S.fromCodePoint(0x20BB7)); // 𠮷
    log(S.fromCodePoint(0x78, 0x1f680, 0x79) === 'x\uD83D\uDE80y'); // true
})(console.log, String)
```

上面代码中，如果`String.fromCodePoint`方法有多个`参数`，则它们会被`合并`成一个`字符串`返回。

注意，`fromCodePoint`方法定义在`String`对象上，而`codePointAt`方法定义在字符串的`实例对象`上。

---

## raw()

`ES6` 还为原生的 `String` 对象，提供了一个`raw`方法。

`String.raw`方法，往往用来充当`模板字符串`的`处理函数`，返回一个`斜杠`都被`转义`（即`斜杠`前面再加一个`斜杠`）的`字符串`，对应于`替换变量`后的`模板字符串`。

```javascript
(function (log, S) {
    log(S.raw`Hi\n${2+3}!`);//  'Hi\\n5!'

    log(S.raw`Hi\u000A!`);//  'Hi\\u000A!'
})(console.log, String)
```

如果`原字符串`的`斜杠`已经转义，那么`String.raw`会进行再次转义。

```javascript
(function (log, S) {
    log(S.raw`Hi\\n`);//  'Hi\\n'
})(console.log, String)
```

`String.raw`方法可以作为处理`模板字符串`的基本方法，它会将所有变量`替换`，而且对`斜杠`进行`转义`，方便下一步作为`字符串`来使用。

`String.raw`方法也可以作为`正常`的`函数`使用。

这时，它的第一个参数，应该是一个具有`raw属性`的`对象`，且`raw属性`的值应该是一个`数组`。

```javascript
(function (log, S) {
    log(S.raw({
        raw: 'test'
    }, 0, 1, 2));
    // 't0e1s2t'

    // 等同于
    log(S.raw({
        raw: ['t', 'e', 's', 't']
    }, 0, 1, 2));// 't0e1s2t'
})(console.log, String)
```

作为函数，`String.raw`的代码实现基本如下。

```javascript
String.raw = function (strings, ...values) {
  let output = '';
  let index;
  for (index = 0; index < values.length; index++) {
    output += strings.raw[index] + values[index];
  }

  output += strings.raw[index]
  return output;
}
```
