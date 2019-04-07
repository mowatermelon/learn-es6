# Unicode 属性类

## 基础说明

ES2018 引入了一种新的类的写法`\p{...}`和`\P{...}`，允许`正则表达式`匹配符合 `Unicode` 某种属性的所有`字符`。

`\P{…}`是`\p{…}`的`反向匹配`，即匹配不满足条件的`字符`。

注意，这两种类只对 `Unicode` 有效，所以使用的时候一定要加上`u`修饰符。

如果不加`u`修饰符，正则表达式使用`\p`和`\P`会报错，`ECMAScript` 预留了这两个类。

由于 `Unicode` 的各种属性非常多，所以这种新的类的表达能力非常强。

## 使用语法

> Unicode 属性类要指定属性名和属性值。

`\p{UnicodePropertyName=UnicodePropertyValue}`

> 对于某些属性，可以只写属性名，或者只写属性值。

```javascript
\p{UnicodePropertyName}
\p{UnicodePropertyValue}
```

| 属性名                | 属性含义                         |
| :-------------------- | :------------------------------- |
| Decimal_Number        | 匹配所有`十进制`字符             |
| Number                | 匹配所有`罗马数字`字符           |
| White_Space           | 匹配所有`空格`                   |
| Arrows                | 匹配所有`箭头`字符               |
| Alphabetic            | 匹配所有`字母`字符               |
| Mark                  | 匹配所有`符号`字符               |
| Connector_Punctuation | 匹配所有`连接标点符`字符         |
| Join_Control          | 匹配所有`控制`字符               |
| Emoji                 | 匹配所有`Emoji`字符              |
| Emoji_Modifier        | 匹配所有`Emoji Modifie`字符      |
| Emoji_Modifier_Base   | 匹配所有`基础表情`字符           |
| Emoji_Presentation    | 匹配所有`Emoji Presentation`字符 |

## 举例

### π

```javascript
((log) => {
    const regexGreekSymbol = /\p{Script=Greek}/u;
    log(regexGreekSymbol.test('π')); // true
})(console.log)
```

上面代码中，`\p{Script=Greek}`指定匹配一个希腊文字母，所以匹配`π`成功。

---

### Arrows

```javascript
((log) => {
    // 匹配所有的箭头字符 目前还没有支持
    const regexArrows = /^\p{Block=Arrows}+$/u;
    log(regexArrows.test('←↑→↓↔↕↖↗↘↙⇏⇐⇑⇒⇓⇔⇕⇖⇗⇘⇙⇧⇩')); // true
})(console.log)
```

---

### Decimal_Number

```javascript
((log) => {
    const regex = /^\p{Decimal_Number}+$/u;
    log(regex.test('𝟏𝟐𝟑𝟜𝟝𝟞𝟩𝟪𝟫𝟬𝟭𝟮𝟯𝟺𝟻𝟼')); // true
})(console.log)
```

上面代码中，属性类指定匹配所有`十进制`字符，可以看到各种字型的`十进制`字符都会匹配成功。

---

### Number

`\p{Number}`甚至能匹配`罗马数字`。

```javascript
((log) => {
    // 匹配所有数字
    const regex = /^\p{Number}+$/u;
    log(regex.test('²³¹¼½¾')); // true
    log(regex.test('㉛㉜㉝')); // true
    log(regex.test('ⅠⅡⅢⅣⅤⅥⅦⅧⅨⅩⅪⅫ')); // true
})(console.log)
```

---

### 所有字母

```javascript
((log) => {
    // 匹配各种文字的所有字母，等同于 Unicode 版的 \w
    [\p{Alphabetic}\p{Mark}\p{Decimal_Number}\p{Connector_Punctuation}\p{Join_Control}]

    // 匹配各种文字的所有非字母的字符，等同于 Unicode 版的 \W
    [^\p{Alphabetic}\p{Mark}\p{Decimal_Number}\p{Connector_Punctuation}\p{Join_Control}]
})(console.log)
```

---

### Emoji

```javascript
((log) => {

    // 匹配 Emoji
    const regexEmoji = /\p{Emoji_Modifier_Base}\p{Emoji_Modifier}?|\p{Emoji_Presentation}|\p{Emoji}\uFE0F/gu;

    const testEmoji = '😂😘😍😊😁😭😜😝😄';
    log(regexEmoji.test(testEmoji));// true
})(console.log)
```

---
