# RegExp 原型属性

## [dotAll 属性]

### 基础说明

`RegExp.prototype.dotAll`

返回值为一个`布尔值`，表示该`正则表达式`是否处在`dotAll`模式。

如果使用了`s`修饰符，将返回`Boolean`类型的`true`，否则将返回`false`。

`s`修饰符表示，特殊字符`.`应另外匹配字符串中的下述行终结符（`line terminator characters`），否则将会失配：

- U+000A 换行符（`\n`）
- U+000D 回车符（`\r`）
- U+2028 行分隔符（line separator）
- U+2029 段分隔符（paragraph separator）

这实际上意味着`.`将会匹配任意的单个`Unicode Basic Multilingual Plane` (`BMP`)字符。

若要使其与`astral`字符（大于`\uFFFF`的`Unicode`字符）匹配，你应当使用`u`（`Unicode`）修饰符。

一起使用这两个`修饰符`，`.`将无一例外地匹配任意`Unicode`字符。

---

### 举例

```javascript
((log) => {
    log(/foo.bar/.test('foo\nbar'));   // false
})(console.log)
```

上面代码中，因为`.`不匹配`\n`，所以`正则表达式`返回`false`。

但是，很多时候我们希望匹配的是`任意单个字符`，这时有一种变通的写法。

```javascript
((log) => {
    log(/foo[^]bar/.test('foo\nbar'));   // true
})(console.log)
```

这种解决方案毕竟不太符合直觉，`ES2018` 引入`s`修饰符，使得`.`可以匹配`任意单个字符`。

```javascript
/foo.bar/s.test('foo\nbar') // true
```

即开启`dotAll`模式，点（`dot`）代表`一切字符`。

```javascript
((log) => {
    const re = /foo.bar/s;
    // 另一种写法
    // const re = new RegExp('foo.bar', 's');

    log(re.test('foo\nbar')); // true
    log(re.dotAll); // true
    log(re.flags); // 's'
})(console.log)
```

---

### tip

`/s`修饰符和多行修饰符`/m`不冲突，两者一起使用的情况下，`.`匹配所有字符，而`^`和`$`匹配每一行的`行首`和`行尾`。

目前该提案属于`Stage 4`即 `Finished（定案阶段）`，预计实现时间是`2018年`，但是在[MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/RegExp/dotAll)中，关于浏览器兼容性描述是

```text
No compatibility data found. Please contribute data for `javascript.builtins.RegExp.dotAll`
```

请在使用中注意浏览器兼容性。

## [flags 属性]

### 基础说明

`RegExp.prototype.flags`

`flags`属性返回一个字符串，由当前`正则表达式`对象的`修饰符标志`组成。

| 特性名       | 特性值 |
| :----------- | :----- |
| writable     | false  |
| enumerable   | false  |
| configurable | true   |

`flags`属性中的标志以字典序排序（从左到右，即`gimsuy`）。

---

### 举例

```javascript
((log) => {
    log(/foo/ig.flags);   // `gi`
    log(/bar/myu.flags);  // `muy`
})(console.log)
```

---

### Polyfill

```javascript

if (RegExp.prototype.flags === undefined) {
  Object.defineProperty(RegExp.prototype, 'flags', {
    configurable: true,
    get: function() {
      return this.toString().match(/[gimsuy]*$/)[0];
    }
  });
}
```

---

### tip

目前除了`Chrome`和`Firefox (Gecko)`两大浏览器支持外，其他浏览器没有支持

---

## [lastIndex 属性]

### 使用语法

```javascript
lastIndex = regExpObj.lastIndex;
regExpObj.lastIndex = lastIndex;
```

---

### 基础说明

`RegExp.lastIndex`

`lastIndex` 是正则表达式的一个`可读可写`的`整型`属性，用来指定`下一次匹配`的`起始索引`。

只有`正则表达式`使用了表示全局检索的 `g` 标志时，该`属性`才会起作用，并且`值`被`动态维护`。此时应用下面的规则：

- 如果`lastIndex`大于字符串的长度，则 `regexp.test` 和 `regexp.exec` 将会匹配失败，然后`lastIndex`被设置为 `0`。
- 如果`lastIndex`等于`字符串`的`长度`，且该`正则表达式`匹配`空字符串`，则该`正则表达式`匹配从`lastIndex`开始的字符串。（then the regular expression matches input starting at lastIndex.）
- 如果`lastIndex`小于`字符串`的`长度`，且该`正则表达式`不匹配`空字符串` ，则该`正则表达式`不匹配`字符串`，`lastIndex` 被设置为 `0`。
- 否则，`lastIndex` 被设置为紧随`最近一次成功匹配`的`下一个位置`。

---

### 举例

#### 基础举例

```javascript
((log) => {
    const re = /(hi)?/g;
    log(re.exec("hi"));   // ["hi", "hi", index: 0, input: "hi", groups: undefined]
    log(re.lastIndex);  // 2

    log(re.exec("hi"));   // ["", undefined, index: 2, input: "hi", groups: undefined]
    log(re.lastIndex);  // 2
})(console.log)
```

第一次匹配完毕之后，`lastIndex`值为`2`，而`hi` 长度为`2`，即这个时候`lastIndex`等于`字符串`的`长度` ，则该`正则表达式`匹配`空字符串`，`正则表达式`匹配从`lastIndex`开始的字符串。

所以，第二次返回 `["", undefined]`，因为没有进行下一次匹配，`lastIndex`的值没有变。

---

#### 直接指定lastIndex属性

```javascript
((log) => {
    const REGEX = /a/g;

    // 指定从2号位置（y）开始匹配
    REGEX.lastIndex = 2;

    // 匹配成功
    const match = REGEX.exec('xaya');

    // 在3号位置匹配成功
    log(match.index); // 3

    // 下一次匹配从4号位开始
    log(REGEX.lastIndex); // 4

    // 4号位开始匹配失败
    log(REGEX.exec('xaya')); // null
})(console.log)
```

上面代码中，`lastIndex`属性指定每次搜索的`开始位置`，`g`修饰符从这个位置开始`向后搜索`，直到发现匹配为止。

#### 结合`y`修饰符

`y修饰符`同样遵守`lastIndex`属性，但是要求必须在`lastIndex`指定的位置发现`匹配`。

```javascript
((log) => {
    const REGEX = /a/y;

    // 指定从2号位置开始匹配
    REGEX.lastIndex = 2;

    // 不是粘连，匹配失败
    log(REGEX.exec('xaya')); // null

    // 指定从3号位置开始匹配
    REGEX.lastIndex = 3;

    // 3号位置是粘连，匹配成功
    const match = REGEX.exec('xaya');
    log(match.index); // 3
    log(REGEX.lastIndex);// 4
})(console.log)
```

---

### tip

目前所有浏览器都支持这个属性设置和获取

---

## [global 属性]

### 基础说明

`RegExp.prototype.global`

返回值为一个`布尔值`，表示该`正则表达式`是否使用了`g` 标志。

如果使用了`g`修饰符，将返回`Boolean`类型的`true`，否则将返回`false`。

`global` 是一个`正则表达式`实例的`只读属性`。

| 特性名       | 特性值 |
| :----------- | :----- |
| writable     | false  |
| enumerable   | false  |
| configurable | false  |

### 举例

```javascript
((log) => {
    const regex1 = /foo/g;
    log(regex1.global);   // true

    const regex2 = new RegExp("foo", "g");
    log(regex2.global);   // true
})(console.log)
```

---

### tip

目前所有浏览器都支持这个属性获取

`global`最初只是`RegExp`实例的属性，而不是`RegExp`对象的属性。

`global`现在是一个`原型访问器属性`，而不是`实例`自己的`数据属性`。

---

## [ignoreCase 属性]

### 基础说明

`RegExp.prototype.ignoreCase`

返回值为一个`布尔值`，表示该`正则表达式`是否使用了`i` 标志。

如果使用了`i`修饰符，将返回`Boolean`类型的`true`，否则将返回`false`。

`ignoreCase` 是一个`正则表达式`实例的`只读属性`。

| 特性名       | 特性值 |
| :----------- | :----- |
| writable     | false  |
| enumerable   | false  |
| configurable | false  |

### 举例

```javascript
((log) => {
    const regex1 = /foo/gi;
    log(regex1.ignoreCase);   // true

    const regex2 = new RegExp("foo", "ig");
    log(regex2.ignoreCase);   // true
})(console.log)
```

---

### tip

目前所有浏览器都支持这个属性获取，`i` 标志意味着在`字符串`进行匹配时，应该忽略`大小写`。

`ignoreCase`最初只是`RegExp`实例的属性，而不是`RegExp`对象的属性。

`ignoreCase`现在是一个`原型访问器属性`，而不是`实例`自己的`数据属性`。

---

## [multiline 属性]

### 基础说明

`RegExp.prototype.multiline`

返回值为一个`布尔值`，表示该`正则表达式`是否使用了`m` 标志。

如果使用了`m`修饰符，将返回`Boolean`类型的`true`，否则将返回`false`。

`multiline` 是一个`正则表达式`实例的`只读属性`。

| 特性名       | 特性值 |
| :----------- | :----- |
| writable     | false  |
| enumerable   | false  |
| configurable | false  |

### 举例

```javascript
((log) => {
    const regex1 = /foo/gm;
    log(regex1.multiline);   // true

    const regex2 = new RegExp("foo", "img");
    log(regex2.multiline);   // true
})(console.log)
```

---

### tip

目前所有浏览器都支持这个属性获取，`m` 标志意味着一个`多行`输入字符串被看作`多行`。

例如，使用 `m`，`^` 和 `$` 将会从只匹配`正则字符串`的`开头`或`结尾`，变为匹配`字符串`中`任一行`的`开头`或`结尾`。

`multiline`最初只是`RegExp`实例的属性，而不是`RegExp`对象的属性。

`multiline`现在是一个`原型访问器属性`，而不是`实例`自己的`数据属性`。

---

## [source 属性]

### 基础说明

`RegExp.prototype.source`

`source` 属性返回一个值为当前`正则表达式`对象`模式文本`的`字符串`，该`字符串`不会包含`正则字面量`两边的`斜杠`以及任何的`修饰符`字符。

### 举例

```javascript
((log) => {
    const regex1 = /foo/gm;
    log(regex1.source);   // foo

    const regex2 = new RegExp("foo", "img");
    log(regex2.source);   // foo
})(console.log)
```

---

### tip

目前所有浏览器都支持这个属性获取

`source`最初只是`RegExp`实例的属性，而不是`RegExp`对象的属性。

`source`现在是一个`原型访问器属性`，而不是`实例`自己的`数据属性`。

---

## [sticky 属性]

### 基础说明

`RegExp.prototype.sticky`

返回值为一个`布尔值`，表示该`正则表达式`是否使用了`y` 标志，即`搜索`是否具有`粘性`（ 仅从正则表达式的 `lastIndex` 属性表示的`索引`处搜索 ）。

如果使用了`y`修饰符，将返回`Boolean`类型的`true`，否则将返回`false`。

`sticky` 是一个`正则表达式`实例的`只读属性`。

| 特性名       | 特性值 |
| :----------- | :----- |
| writable     | false  |
| enumerable   | false  |
| configurable | false  |

### 举例

```javascript
((log) => {
    const regex1 = /foo/y;
    log(regex1.sticky);   // true

    const regex2 = new RegExp("foo", "y");
    log(regex2.sticky);   // true
})(console.log)
```

---

### tip

目前`Chrome`，`Edge`和`Firefox (Gecko)`浏览器支持该属性获取。

当使用带有`y`标识的匹配模式时，`^`断言总是会匹配`输入`的`开始位置`或者（如果是`多行模式`）每一行的`开始位置`。

可以使用 `try { … } catch { … }` 来测试运行时（`run-time`）是否支持 `sticky` 标志。

这种情况下，必须使用 `eval(…)` 表达式或 `RegExp(regex-string, flags-string)` 语法（这是由于 `/regex/flags` 表示法将会在`编译`时刻被处理，因此在 `catch` 语句块处理异常前就会抛出一个`异常`。

```javascript
((log) => {
    let supports_sticky;
    try {
        RegExp('','y');
        supports_sticky = true;
    }catch(e) {
        supports_sticky = false;
    }
    log(supports_sticky); // true

})(console.log)

```

---

## [unicode 属性]

### 基础说明

`RegExp.prototype.unicode`

返回值为一个`布尔值`，表示该`正则表达式`是否使用了`u` 标志。

如果使用了`u`修饰符，将返回`Boolean`类型的`true`，否则将返回`false`。

`unicode` 是一个`正则表达式`实例的`只读属性`。

| 特性名       | 特性值 |
| :----------- | :----- |
| writable     | false  |
| enumerable   | false  |
| configurable | false  |

### 举例

```javascript
((log) => {
    const regex1 = /\u{61}/u;
    log(regex1.unicode);   // true
    log(regex1.source);   // \u{61}

    const regex2 = new RegExp('\u{61}', 'u');
    log(regex2.unicode);   // true
    log(regex2.source);   // a
})(console.log)
```

---

### tip

目前`Chrome`，`Edge`和`Firefox (Gecko)`浏览器支持该属性获取。

`u` 标志开启了多种 `Unicode` 相关的特性。使用 `u` 标志，任何 `Unicode` 代码点的`转译`都会被`解释`。

注意开启`u` 标志，最好结合`构造函数`进行使用，如果是`字面量`的`正则表达式`，则内容是不会`转译`的。

---

## MDN地址

https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/RegExp

[dotAll 属性]: https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/RegExp/dotAll
[flags 属性]: https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/RegExp/flags
[lastIndex 属性]: https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/RegExp/lastIndex
[global 属性]: https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/RegExp/global
[ignoreCase 属性]: https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/RegExp/ignoreCase
[multiline 属性]: https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/RegExp/multiline
[source 属性]: https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/RegExp/source
[sticky 属性]: https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/RegExp/sticky
[unicode 属性]: https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/RegExp/unicode