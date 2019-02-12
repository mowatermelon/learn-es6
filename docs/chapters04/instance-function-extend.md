
# 实例对象方法扩展

## codePointAt()

### 产生原因

在 `Javascript`内部，字符以`UTF-16`的格式存储，每个字符固定为`2`个字节。

对于那些需要`4`个字节存储的字符（`Unicode` 编号大于`0xFFFF`的字符），`Javascript`会认为它们是`两`个字符。

```javascript
(function (log) {
    const s = '𠮷a';
    log(s.length) // 3
    log(s.charAt(0)) // �
    log(s.charAt(1)) // �

    log(s.charCodeAt(0)) // 55362
    log(s.charCodeAt(1)) // 57271
})(console.log)
```

上面代码中，汉字`𠮷`（注意，这个字不是`吉祥`的`吉`）的`码点`是`0x20BB7`，`UTF-16` 编码为`0xD842 0xDFB7`（十进制为`55362 57271`），需要`4`个字节储存。

对于这种`4`个字节的字符，`JavaScript` 不能正确处理，字符串长度会误判为`2`，而且`charAt`方法无法读取整个`字符`，`charCodeAt`方法只能分别返回前两个`字节`和后两个`字节`的`值`。

---

### 优化效果

`ES6`提供了`codePointAt` 方法，能够正确处理`4`个字节存储的字符，返回一个字符的`Unicode`码点。

```javascript
(function (log) {
    const s = '𠮷a';

    log(s.codePointAt(0)) // 134071
    log(s.codePointAt(1)) // 57271
    log(s.codePointAt(2)) // 97
})(console.log)
```

`codePointAt`方法的参数，是`字符`在`字符串`中的位置（从`0`开始）。

`JavaScript` 将`𠮷a`视为三个字符，`codePointAt` 方法在第一个字符上，正确地识别了`𠮷`，返回了它的十进制码点 `134071`（即十六进制的`20BB7`）。

在第二个`字符`（即`𠮷`的后两个`字节`）和第三个字符`a`上，`codePointAt`方法的结果与`charCodeAt`方法相同。

总之，`codePointAt`方法可以正确返回四字节`UTF-16`字符的`Unicode`码点。

对于那些`两个字节`存储的`常规字符`，它返回结果与`charCodeAt`方法相同。

---

### 结合for-of

你可能注意到了，`codePointAt`方法的参数，仍然是不正确的。

比如，下面代码中，字符`a`在字符串`s`的正确位置序号应该是 `1`，但是必须向`codePointAt`方法传入 `2`。

解决这个问题的一个办法是使用`for...of`循环，因为它会正确识别 `32` 位的 `UTF-16` 字符。

```javascript
(function (log) {
    const s = '𠮷a';

    log(s.codePointAt(0)) // 134071
    log(s.codePointAt(2)) // 97

    for (let ch of s) {
        log(ch.codePointAt(0));
    }
    // 134071
    // 97
})(console.log)
```

---

### 进制转换

`codePointAt`方法返回的是`码点`的`十进制值`，如果想要`其他进制`的值，可以使用`toString`方法转换一下。

```javascript
(function (log) {
    const s = '𠮷a';
    log(s.codePointAt(0)) // 134071
    log(s.codePointAt(2)) // 97

    log(s.codePointAt(0).toString(2)) // 100000101110110111
    log(s.codePointAt(2).toString(2)) // 1100001

    log(s.codePointAt(0).toString(8)) // 405667
    log(s.codePointAt(2).toString(8)) // 141

    log(s.codePointAt(0).toString(10)) // 134071
    log(s.codePointAt(2).toString(10)) // 97

    log(s.codePointAt(0).toString(16)) // 20bb7
    log(s.codePointAt(2).toString(16)) // 61

    log(s.codePointAt(0).toString(32)) // 42tn
    log(s.codePointAt(2).toString(32)) // 31
})(console.log)
```

### 判断是否是多字节

`codePointAt` 方法是`测试`一个字符由`两个字节`还是`四个字节`组成的最简单方法。

```javascript
(function (log) {
    function is32Bit(c) {
        return c.codePointAt(0) > 0xFFFF;
    }

    log(is32Bit('𠮷')); // true
    log(is32Bit('a')); // false
})(console.log)
```

---

## normalize()

### 产生原因

许多欧洲语言有`语调符号`和`重音符号`。为了表示它们，`Unicode` 提供了两种方法。

- 一种是直接提供带`重音符号`的字符，比如`Ǒ`（`\u01D1`）。
- 另一种是提供`合成符号`（`combining character`），即`原字符`与`重音符号`的合成，两个字符合成一个字符，比如`O`（`\u004F`）和`ˇ`（`\u030C`）合成`Ǒ`（`\u004F\u030C`）。

这两种表示方法，在`视觉`和`语义`上都`等价`，但是 `JavaScript` 不能识别。

```javascript
(function (log, S) {
    log('\u01D1' === '\u004F\u030C'); //false

    log('\u01D1'.length); // 1
    log('\u004F\u030C'.length); // 2
})(console.log, String)
```

上面代码表示，`JavaScript` 将`合成字符`视为`两个字符`，导致两种表示方法不相等。

---

### 优化效果

`ES6` 提供字符串实例的`normalize()`方法，用来将`字符`的不同`表示方法`统一为同样的`形式`，这称为 `Unicode` 正规化。

```javascript
(function (log, S) {
    const type1 = '\u01D1'.normalize();
    const type2 = '\u004F\u030C'.normalize();
    log(type1 === type2); // true
})(console.log, String)
```

---

### 参数详解

`normalize`方法可以接受一个`参数`来指定`normalize`的方式，参数的四个可选值如下。

| 形参值 | 形参值解释                                                                                                                                                                                                          |
| ------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `NFC`  | 默认参数，表示`标准等价合成`（`Normalization Form Canonical Composition`），返回多个`简单字符`的`合成字符`。所谓`标准等价`指的是`视觉`和`语义`上的等价。                                                            |
| `NFD`  | 表示`标准等价分解`（`Normalization Form Canonical Decomposition`），即在`标准等价`的前提下，返回`合成字符`分解的多个`简单字符`。                                                                                    |
| `NFKC` | 表示`兼容等价合成`（`Normalization Form Compatibility Composition`），返回`合成字符`。所谓`兼容等价`指的是`语义`上存在等价，但`视觉`上不等价，比如`囍`和`喜喜`。（这只是用来举例，`normalize`方法不能识别`中文`。） |
| `NFKD` | 表示`兼容等价分解`（`Normalization Form Compatibility Decomposition`），即在`兼容等价`的前提下，返回`合成字符`分解的多个`简单字符`。                                                                                |

```javascript
(function (log, S) {
    log('\u004F\u030C'.normalize('NFC').length); // 1
    log('\u004F\u030C'.normalize('NFD').length); // 2
})(console.log, String)
```

上面代码表示，`NFC`参数返回字符的`合成形式`，`NFD`参数返回字符的分解形式。

不过，`normalize`方法目前不能识别`三个`或`三个`以上`字符`的合成。这种情况下，还是只能使用`正则表达式`，通过 `Unicode 编号`区间判断。

---

## 判断字符串包含

传统上，`Javascript`中只有`indexOf`方法，可用来确定一个`字符串`是否`包含`在另一个`字符串`中。`ES6`又提供了三种`新方法`。

| 方法名                        | 方法解释                                         |
| :---------------------------- | :----------------------------------------------- |
| contains(findStr,findIndex)   | 返回布尔值，表示了是否找到参数字符串             |
| startsWith(findStr,findIndex) | 返回布尔值，表示参数字符串是否正在源字符串的头部 |
| endsWith(findStr,findIndex)   | 返回布尔值，表示参数字符串是否正在源字符串的尾部 |

- `findStr`，表示被检索的参数字符串

- `findIndex`，表示开始搜索的位置，`endsWith`的行为与其他两个方法有所不同，它针对前`findIndex`个字符，而其他两个`方法`则针对从`findIndex`个位置直到字符串结束的字符。

```javascript

(function (log, S) {
    const s1 = 'Hello world!';

    log(s1.startsWith('Hello')); // true
    log(s1.endsWith('!')); // true
    log(s1.includes('o')); // true

    // 这三个方法都支持第二个参数，表示开始搜索的位置。

    const s2 = 'Hello world!';

    log(s2.startsWith('world', 6)); // true
    log(s2.endsWith('Hello', 5)); // true
    log(s2.includes('Hello', 6)); // false
})(console.log, String)
```

---

## repeat

`repeat(repeatIndex)`返回一个`新字符串`，表示将`原字符串`重复`repeatIndex`次

### 基础使用

```javascript

(function (log, S) {
    log('x'.repeat(3)); // 'xxx'
    log('hello'.repeat(2)); // 'hellohello'
    log('na'.repeat(0)); // ''
})(console.log, String)
```

---

### 特殊形参值处理

> 参数值为小数

如果`repeat`的参数是`小数`，会被`取整`，注意这个`取整`对当前值不做任何`四舍五入`。

```javascript

(function (log, S) {
    log('na'.repeat(2.3)); // 'nana'
    log('na'.repeat(2.5)); // 'nana'
    log('na'.repeat(2.9)); // 'nana'

    log('na'.repeat(2.3999)); // 'nana'
    log('na'.repeat(2.599)); // 'nana'
    log('na'.repeat(2.999)); // 'nana'
})(console.log, String)
```

---

> 参数值为0

如果`repeat`的参数是`+0`,`-0`,`0`,`NaN`,`-1~0`之间或者`0~1`之间值，会返回一个`空字符串`。

`0` 到`-1` 之间的`小数`，则等同于 `0`，这是因为会先进行`取整`运算。

`0` 到`-1` 之间的`小数`，`取整`以后等于`-0`，`repeat`视同为 `0`。

```javascript
(function (log, S) {
    log('na'.repeat(+0)); // ''
    log('na'.repeat(-0)); // ''
    log('na'.repeat(-0)); // ''

    log('na'.repeat(NaN)); // ''

    log('na'.repeat(+0.2)); // ''
    log('na'.repeat(+0.5)); // ''
    log('na'.repeat(+0.8)); // ''

    log('na'.repeat(-0.2)); // ''
    log('na'.repeat(-0.5)); // ''
    log('na'.repeat(-0.8)); // ''
})(console.log, String)
```

---

> 参数值为`负数`或者`Infinity`

如果`repeat`的参数是`负数`或者`Infinity`，会报错。

```javascript
(function (log, S) {
    log('na'.repeat(Infinity)); // RangeError: Invalid count value
    log('na'.repeat(-1)); // RangeError: Invalid count value
})(console.log, String)
```

---

> 参数值为`字符串`

如果`repeat`的参数是`字符串`，则会先转换成`数字`。

- 如果`字符串`中没有包含`有效`的`数字`，则当作`0`处理。
- 如果`字符串`中包含`有效`的`数字`，同时又有`特殊字符`，`\s`，`\S`或者`字母`等，则当作`0`处理。
- 如果`字符串`中包含`有效`的`数字`，同时仅有`空格`，`\t`或者`\n`等，当作有效`参数`进行使用。
- 如果`字符串`中包含`其他进制值`的`字符串`，转换成`十进制`的`数值`之后，当作有效`参数`进行使用。
- 如果`字符串`中包含`符号`的`字符串`，转换为`数值`之后，如果为大于等于`1`的`正数`，当作有效`参数`进行使用；如果为`-1~1`，则当作`0`，进行使用；如果为小于等于`-1`的`负数`，则会抛出异常`RangeError: Invalid count value`。

```javascript
(function (log, S) {
    log(1, 'na'.repeat('na')); // 1 ''
    log(2, 'na'.repeat('3na')); // 2 ''
    log(3, 'na'.repeat('3，')); // 3 ''
    log(3, 'na'.repeat('3\s')); // 3 ''
    log(3, 'na'.repeat('3\S')); // 3 ''

    log(4, 'na'.repeat('3 ')); // 4 'nanana'
    log(4, 'na'.repeat('3\t')); // 4 'nanana'
    log(4, 'na'.repeat('\n3\n')); // 4 'nanana'

    log(5, 'na'.repeat('03')); // 5 'nanana'
    log(5, 'na'.repeat('0b1')); // 5 'na'
    log(5, 'na'.repeat('0o3')); // 5 'nanana'
    log(5, 'na'.repeat('0x3')); // 5 'nanana'

    // log('na'.repeat('-3')); // RangeError: Invalid count value

    log(6, 'na'.repeat('+3')); // 6 'nanana'
})(console.log, String)
```

---

> 参数值为`日期格式`

如果`repeat`的参数是`日期格式`，则会先转换成`数字`。

但是由于转换的`值`一般超过了可用的`字符串长度`，所以一般会直接报错`RangeError: Invalid string length`。

如果是传入日期格式`单独转换`的`天数`和`日期值`，`repeat`可以正常使用。

```javascript
(function (log, S) {
    const d = Reflect.construct(Date,[]);
    log(d.getTime()); // 1549895917860
    log(d.getDate()); // 11
    log(d.getDay()); // 1

    // log('na'.repeat(d)); // RangeError: Invalid string length
    // log('na'.repeat(d.getTime())); // RangeError: Invalid string length
    log('na'.repeat(d.getDate())); // nanananananananananana
    log('na'.repeat(d.getDay())); // na
})(console.log, String)
```

---

> 参数值为`数组`或者`对象`

如果`repeat`的参数是`数组`，`Map`，`WeakMap`，`Set`，`WeakSet`或者`对象`，则会统一当作`0`进行处理，返回`空字符串`。

```javascript
(function (log, S) {
    log(1, 'na'.repeat([])); // 1 ''
    log(1, 'na'.repeat([1, 2, 3])); // 1 ''
    log(1, 'na'.repeat([{
        z: 1
    }])); // 1 ''

    log(2, 'na'.repeat({})); // 2 ''
    log(2, 'na'.repeat({
        z: 1
    })); // 2 ''

    log(3, 'na'.repeat(new Map())); // 3 ''

    log(4, 'na'.repeat(new WeakMap())); // 4 ''

    log(5, 'na'.repeat(new Set())); // 5 ''

    log(6, 'na'.repeat(new WeakSet())); // 6 ''
})(console.log, String)
```

---

## 字符串补全

### 基础使用

`ES2017` 引入了字符串补全长度的功能。

如果某个`字符串`不够指定`长度`，会在`头部`或`尾部`补全。

| 方法名                          | 方法解释                           |
| :------------------------------ | :--------------------------------- |
| padStart(maxFillLength,fillStr) | 返回最后补全的字符串，用于头部补全 |
| padEnd(maxFillLength,fillStr)   | 返回最后补全的字符串，尾部补全     |

- `maxFillLength`是字符串补全生效的`最大长度`，
- `fillStr`是用来`补全`的`字符串`。

```javascript
(function (log, S) {
    const s = 'm';
    const fillStr = 'ab';
    log(s.padStart(5, fillStr)); // ababm
    log(s.padStart(4, fillStr)); // abam

    log(s.padEnd(5, fillStr)); // mabab
    log(s.padEnd(4, fillStr)); // maba
})(console.log, String)
```

---

### 最大长度值忽略情况

如果`原字符串`的`长度`，等于或大于`最大长度`，则`字符串`补全不生效，返回`原字符串`。

```javascript
(function (log, S) {
    const s = 'mm';
    const fillStr = 'ab';
    log(s.padStart(2, fillStr)); // 'mm'
    log(s.padEnd(2, fillStr)); // 'mm'
})(console.log, String)
```

---

### 补全字符串截取

如果用来补全的`字符串`与`原字符串`，两者的长度之和超过了`最大长度`，则会截去超出`位数`的`补全字符串`。

```javascript
(function (log, S) {
    const s = 'mm';
    const fillStr = '0123456789';
    log(s.padStart(10, fillStr)); // 01234567mm
    log(s.padEnd(10, fillStr)); // mm01234567
})(console.log, String)
```

---

### 补全字符串忽略

如果省略第二个`参数`，默认使用`空格`补全长度。

```javascript
(function (log, S) {
    const s = 'mm';
    log(s.padStart(10)); //         mm
    log(s.padEnd(10)); // mm        
})(console.log, String)
```

---

### 常见用途

> 数值补全指定位数

`padStart()`和`padEnd`的`常见用途`是为数值补全`指定位数`。

下面代码生成 `10` 位的`数值字符串`。

```javascript
(function (log, S) {
    const fillLength = 10;
    const fillStr = '0';
    log('1'.padStart(fillLength, fillStr)); // '0000000001'
    log('12'.padStart(fillLength, fillStr)); // '0000000012'
    log('123456'.padStart(fillLength, fillStr)); // '0000123456'

    log('1'.padEnd(fillLength, fillStr)); // '1000000000'
    log('12'.padEnd(fillLength, fillStr)); // '1200000000'
    log('123456'.padEnd(fillLength, fillStr)); // '1234560000'
})(console.log, String)
```

---

> 提示字符串格式

`padStart()`和`padEnd`的还可以用作`日期格式`的字符串`格式提示`。

下面代码生成`年月日`日期格式的字符串。

```javascript
(function (log, S) {

    log('12'.padStart(10, 'YYYY-MM-DD')); // 'YYYY-MM-12'
    log('09-12'.padStart(10, 'YYYY-MM-DD')); // 'YYYY-09-12'

    // log('12'.padEnd(10, 'YYYY-MM-DD')); // 12YYYY-MM-
    // log('09-12'.padEnd(10, 'YYYY-MM-DD')); // 09-12YYYY-
    log('2019'.padEnd(10, '-MM-DD')); // '2019-MM-DD'
})(console.log, String)
```

---

## matchAll

### 产生原因

如果一个`正则表达式`在字符串里面有多个`匹配`，现在一般使用`g`修饰符或`y`修饰符，在循环里面逐一取出。

```javascript
(function (log, S) {

    const regex = /t(e)(st(\d?))/g;
    const string = 'test1test2test3';

    const matches = [];
    let match;
    while (match = regex.exec(string)) {
      matches.push(match);
    }

    log(matches);
    //     [ [ 'test1',
    //     'e',
    //     'st1',
    //     '1',
    //     index: 0,
    //     input: 'test1test2test3',
    //     groups: undefined ],
    //   [ 'test2',
    //     'e',
    //     'st2',
    //     '2',
    //     index: 5,
    //     input: 'test1test2test3',
    //     groups: undefined ],
    //   [ 'test3',
    //     'e',
    //     'st3',
    //     '3',
    //     index: 10,
    //     input: 'test1test2test3',
    //     groups: undefined ] ]
})(console.log, String)
```

上面代码中，`while`循环取出每一轮的`正则匹配`，一共三轮。

---

### 优化结果

目前有一个`提案`，目前还未实现，增加了`String.prototype.matchAll`方法，可以一次性取出所有`匹配`。

不过，它返回的是一个遍历器（`Iterator`），而不是`数组`。

```javascript
(function (log, S) {
    // g 修饰符加不加都可以
    const regex = /t(e)(st(\d?))/g;
    const string = 'test1test2test3';

    if (string.matchAll) {
        for (const match of string.matchAll(regex)) {
            log(match);
        }
        // ["test1", "e", "st1", "1", index: 0, input: "test1test2test3"]
        // ["test2", "e", "st2", "2", index: 5, input: "test1test2test3"]
        // ["test3", "e", "st3", "3", index: 10, input: "test1test2test3"]
    } else {
        log('not support');
    }

    // not support
})(console.log, String)
```

上面代码中，由于`string.matchAll(regex)`返回的是`遍历器`，所以可以用`for...of`循环取出。

相对于返回`数组`，返回`遍历器`的好处在于，如果匹配`结果`是一个很大的`数组`，那么`遍历器`比较`节省资源`。

---

### 遍历器转换

`遍历器`转为`数组`是非常简单的，使用`...`运算符和`Array.from`方法就可以了。

```javascript
(function (log, S) {
    // g 修饰符加不加都可以
    const regex = /t(e)(st(\d?))/g;
    const string = 'test1test2test3';

    if (string.matchAll) {
        const matches = string.matchAll(regex);
        // 转为数组方法一
        log([...matches]);
        //     [ [ 'test1',
        //     'e',
        //     'st1',
        //     '1',
        //     index: 0,
        //     input: 'test1test2test3',
        //     groups: undefined ],
        //   [ 'test2',
        //     'e',
        //     'st2',
        //     '2',
        //     index: 5,
        //     input: 'test1test2test3',
        //     groups: undefined ],
        //   [ 'test3',
        //     'e',
        //     'st3',
        //     '3',
        //     index: 10,
        //     input: 'test1test2test3',
        //     groups: undefined ] ]

        // 转为数组方法二
        log(Array.from(matches));
        //     [ [ 'test1',
        //     'e',
        //     'st1',
        //     '1',
        //     index: 0,
        //     input: 'test1test2test3',
        //     groups: undefined ],
        //   [ 'test2',
        //     'e',
        //     'st2',
        //     '2',
        //     index: 5,
        //     input: 'test1test2test3',
        //     groups: undefined ],
        //   [ 'test3',
        //     'e',
        //     'st3',
        //     '3',
        //     index: 10,
        //     input: 'test1test2test3',
        //     groups: undefined ] ]

    } else {
        log('not support');
    }

    // not support
})(console.log, String)
```

---
