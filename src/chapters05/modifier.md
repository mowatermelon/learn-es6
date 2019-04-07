# RegExp 修饰符

## g 修饰符

### 基础说明

全局匹配，找到所有匹配，而不是在第一个匹配后停止。

请注意只有开启了`g`修饰符，`lastIndex`的值才会被维护，和设置及时生效。

---

### 举例

下面案例中详细说明了，设置了`g`修饰符和未设置的区别。

当设置了`g`修饰符，`exec`会自动找到下一个`匹配项`，但是如果没有设置过，则不会寻找下一个`匹配项`。

```javascript
((log) => {
    const str = 'ababa';
    const reg1 = /a/;
    const reg2 = /a/g;
    log(reg1.exec(str)); // ["a", index: 0, input: "ababa", groups: undefined]
    log(reg1.lastIndex);  // 0

    reg1.lastIndex = 1;

    log(reg1.exec(str)); // ["a", index: 0, input: "ababa", groups: undefined]
    log(reg1.lastIndex);  // 1

    log(reg2.exec(str)); // ["a", index: 0, input: "ababa", groups: undefined]
    log(reg2.lastIndex);  // 1

    reg2.lastIndex = 1;

    log(reg2.exec(str)); // ["a", index: 2, input: "ababa", groups: undefined]
    log(reg2.lastIndex);  // 3
})(console.log)
```

---

## i 修饰符

### 基础说明

忽略需要匹配项中的字母大小写

---

### 举例

```javascript
((log) => {
    const str = 'aba';
    const reg1 = /aB/;
    const reg2 = /aB/i;
    log(reg1.exec(str)); // null

    log(reg2.exec(str)); // ["ab", index: 0, input: "aba", groups: undefined]

})(console.log)
```

---

## m 修饰符

### 基础说明

`m` 标志意味着一个`多行`输入字符串被看作`多行`。

例如，使用 `m`，`^` 和 `$` 将会从只匹配`正则字符串`的`开头`或`结尾`，变为匹配`字符串`中`任一行`的`开头`或`结尾`。

---

### 举例

```javascript
((log) => {
    const s = "Please yes\nmake my day!";
    log(s.match(/yes.*day/)); // null
    log(s.match(/yes[^]*day/)); // ["yes↵make my day", index: 7, input: "Please yes↵make my day!", groups: undefined]
})(console.log)
```

---

## u 修饰符

### 基础说明

正常情况下`\w` 或 `\W` 只会匹配基本的 `ASCII` 字符，如 `a` 到 `z`'、 `A` 到 `Z`、 `0` 到 `9` 及 `_`。

为了匹配其他语言中的字符，如`西里尔（Cyrillic）`或 `希伯来语（Hebrew）`，要使用 `\uhhhh`，`hhhh` 表示以`十六进制`表示的字符的 `Unicode` 值。

`ES6` 对`正则表达式`添加了`u`修饰符，含义为`Unicode 模式`，用来正确处理大于`\uFFFF`的 `Unicode` 字符。也就是说，会正确处理四个字节的 `UTF-16` 编码。

一旦加上`u`修饰符号，就会修改下面这些正则表达式的行为。

---

### Unicode 字符表示法

`ES6` 新增了使用`大括号`表示 `Unicode` 字符，这种`表示法`在`正则表达式`中必须加上`u`修饰符，才能识别当中的`大括号`，否则会被解读为前一个`匹配项`的`量词`。

```javascript
((log) => {
    log(/\u{61}/.test('a')); // false
    log(/\u{61}/u.test('a')); // true
    log(/\u{20BB7}/u.test('𠮷')); // true
})(console.log)
```

上面代码表示，如果不加`u`修饰符，`正则表达式`无法识别`\u{61}`这种表示法，只会认为这匹配 `61` 个连续的`u`。

---

### 举例

#### 正确处理UTF-16 编码值

```javascript
/^\uD83D/u.test('\uD83D\uDC2A') // false
/^\uD83D/.test('\uD83D\uDC2A') // true
```

上面代码中，`\uD83D\uDC2A`是一个四个字节的 `UTF-16` 编码，代表一个`字符`。

加了`u`修饰符以后，`ES6` 就会识别其为一个`字符`，所以第一行代码结果为`false`。

但是，`ES5` 不支持四个字节的 `UTF-16` 编码，会将其识别为两个`字符`，导致第二行代码结果为`true`。

---

#### 分离出Unicode字符

展示了怎样从一个`单词`中分离出 `Unicode` 字符。

```javascript
((log) => {
    const text = "Образец text на русском языке";
    const regex = /[\u0400-\u04FF]+/g;

    const match = regex.exec(text);
    log(match[0]);  // logs "Образец"
    log(regex.lastIndex);  // logs "7"

    const match2 = regex.exec(text);
    log(match2[0]);  // logs "на" [did not log "text"]
    log(regex.lastIndex);  // logs "15"

    // and so on
})(console.log)
```

这里有一个外部资源，用来获取 `Unicode` 中的不同区块范围：`Regexp-unicode-block`

---

#### 从 URL 中提取子域名节

```javascript
((log) => {
    const url = "http://xxx.domain.com";
    log(/[^.]+/.exec(url)[0].substr(7)); // logs "xxx"
})(console.log)
```

---

#### 结合点字符

点（`.`）字符在`正则表达式`中，含义是除了`换行符`以外的任意单个`字符`。对于`码点`大于`0xFFFF`的 `Unicode` 字符，`点字符`不能识别，必须加上`u`修饰符。

```javascript
((log) => {
    const s = '𠮷';

    log(/^.$/.test(s)); // false
    log(/^.$/u.test(s)); // true
})(console.log)
```

上面代码表示，如果不添加`u`修饰符，`正则表达式`就会认为字符串为两个`字符`，从而匹配失败。

---

#### 量词解析

使用`u`修饰符后，所有`量词`都会正确识别码点大于`0xFFFF`的 `Unicode` 字符。

```javascript
((log) => {
    log(/a{2}/.test('aa')); // true
    log(/a{2}/u.test('aa')); // true
    log(/𠮷{2}/.test('𠮷𠮷')); // false
    log(/𠮷{2}/u.test('𠮷𠮷')); // true
})(console.log)
```

---

#### 影响预定义模式

`u`修饰符也影响到`预定义模式`，能否正确识别码点大于`0xFFFF`的 `Unicode` 字符。

```javascript
((log) => {
    log(/^\S$/.test('𠮷')); // false
    log(/^\S$/u.test('𠮷')); // true
})(console.log)
```

上面代码的`\S`是预定义模式，匹配所有`非空白字符`。只有加了`u`修饰符，它才能正确匹配码点大于`0xFFFF`的 `Unicode` 字符。

利用这一点，可以写出一个正确返回字符串长度的函数。

```javascript
((log) => {
    const codePointLength = (text) => {
      let result = text.match(/[\s\S]/gu);
      return result ? result.length : 0;
    }

    const s = '𠮷𠮷';

    log(s.length); // 4
    log(codePointLength(s)); // 2
})(console.log)
```

---

#### 影响 i 修饰符

有些 `Unicode` 字符的编码不同，但是字型很相近，比如，`\u004B`与`\u212A`都是大写的`K`。

```javascript
((log) => {
    log(/[a-z]/i.test('\u212A')); // false
    log(/[a-z]/iu.test('\u212A')); // true
})(console.log)
```

上面代码中，不加`u`修饰符，就无法识别`非规范`的`K`字符。

## y 修饰符

### 基础说明

`ES6` 还为正则表达式添加了`y`修饰符，叫做`粘连`（sticky）修饰符。

`y`修饰符的作用与`g`修饰符类似，也是`全局匹配`，后一次匹配都从上一次匹配成功的下一个位置开始。

不同之处在于，`g`修饰符只要`剩余位置`中存在`匹配`就可，而`y`修饰符确保匹配必须从`剩余`的第一个位置开始，这也就是`粘连`的涵义。

---

### 与`g`修饰符关联

```javascript
((log) => {
    const s = 'aaa_aa_a';
    const r1 = /a+/g;
    const r2 = /a+/y;

    log(r1.exec(s)); // ["aaa", index: 0, input: "aaa_aa_a", groups: undefined]
    log(r2.exec(s)); // ["aaa", index: 0, input: "aaa_aa_a", groups: undefined]

    log(r1.exec(s)); // ["aa", index: 4, input: "aaa_aa_a", groups: undefined]
    log(r2.exec(s)); // null
})(console.log)
```

上面代码有两个正则表达式，一个使用`g`修饰符，另一个使用`y`修饰符。

这两个正则表达式各执行了两次，第一次执行的时候，两者行为相同，剩余字符串都是`_aa_a`。

由于`g`修饰没有位置要求，所以第二次执行会返回结果，而`y`修饰符要求匹配必须从头部开始，所以返回`null`。

### 举例

#### 头部匹配

实际上，`y`修饰符号隐含了头部匹配的标志`^`。

即保证每次都能`头部匹配`，`y`修饰符就会返回结果了。

```javascript
((log) => {
    log(/b/y.exec('aba')); // null

    const s = 'aaa_aa_a';
    const r = /a+_/y;

    log(r.exec(s)); // ["aaa_", index: 0, input: "aaa_aa_a", groups: undefined]
    log(r.exec(s)); // ["aa_", index: 4, input: "aaa_aa_a", groups: undefined]
})(console.log)
```

`第一段代码`由于不能保证`头部匹配`，所以返回`null`。`y`修饰符的设计本意，就是让`头部匹配`的标志`^`在`全局匹配`中都有效。

#### 关联lastIndex属性

下面代码中，`lastIndex`属性指定每次搜索的开始位置，`g`修饰符从这个位置开始向后搜索，直到发现匹配为止。

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

#### 结合replace

下面代码中，最后一个`a`因为不是出现在下一次匹配的`头部`，所以不会被替换。

```javascript
((log) => {
    const REGEX = /a/gy;
    log('aaxa'.replace(REGEX, '-')); // '--xa'4
})(console.log)
```

---

#### 结合match

单单一个`y`修饰符对`match`方法，只能返回第一个`匹配`，必须与`g`修饰符联用，才能返回所有匹配。

```javascript
((log) => {
    log('a1a2a3'.match(/a\d/y)); // ["a1"]
    log('a1a2a3'.match(/a\d/gy)); // ["a1", "a2", "a3"]
})(console.log)
```

---

#### 结合sticky

结合`正则表达式`上使用 `sticky` 标志，用来匹配多行输入的单独行。

```javascript
((log) => {
    const text = "First line\nsecond line";
    const regex = /(\S+) line.?/ys;

    const match = regex.exec(text);
    log(match);  // ["First line↵", "First", index: 0, input: "First line↵second line", groups: undefined]
    log(match[1]);  // "First"
    log(regex.lastIndex); // 11

    const match2 = regex.exec(text);
    log(match2); // ["second line", "second", index: 11, input: "First line↵second line", groups: undefined]
    log(match2[1]);  // "Second"
    log(regex.lastIndex); // "22"

    const match3 = regex.exec(text);
    log(match3 === null); // "true"
})(console.log)
```

---

#### 提取token

是从字符串提取 `token`（词元），`y`修饰符确保了匹配之间不会有漏掉的`字符`。

```javascript
((log) => {
    const tokenize = (TOKEN_REGEX, str) => {
        const result = [];
        let match;
        while (match = TOKEN_REGEX.exec(str)) {
            result.push(match[1]);
        }
        return result;
    }
    const TOKEN_Y = /\s*(\+|[0-9]+)\s*/y;
    const TOKEN_G  = /\s*(\+|[0-9]+)\s*/g;

    log(tokenize(TOKEN_Y, '3 + 4'));
    // [ '3', '+', '4' ]
    log(tokenize(TOKEN_G, '3 + 4'));
    // [ '3', '+', '4' ]

})(console.log)
```

上面代码中，如果字符串里面没有非法字符，`y`修饰符与`g`修饰符的提取结果是一样的。

但是，一旦出现非法字符，两者的行为就不一样了。

```javascript
((log) => {
    const tokenize = (TOKEN_REGEX, str) => {
        const result = [];
        let match;
        while (match = TOKEN_REGEX.exec(str)) {
            result.push(match[1]);
        }
        return result;
    }
    const TOKEN_Y = /\s*(\+|[0-9]+)\s*/y;
    const TOKEN_G  = /\s*(\+|[0-9]+)\s*/g;

    log(tokenize(TOKEN_Y, '3x + 4'));
    // [ '3' ]
    log(tokenize(TOKEN_G, '3x + 4'));
    // [ '3', '+', '4' ]

})(console.log)
```

上面代码中，`g`修饰符会忽略非法字符，而`y`修饰符不会，这样就很容易发现错误。

---

## s 修饰符

### 基础说明

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
    log(/foo.bar/.test('foo\nbar')); // false
    log(/foo.bar/s.test('foo\nbar')); // true
})(console.log)
```

---
