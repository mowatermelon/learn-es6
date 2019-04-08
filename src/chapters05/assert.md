# RegExp 断言

## 前言

`JavaScript` 语言的`正则表达式`，`ES5`只支持`先行断言`（`lookahead`）和`先行否定断言`（`negative lookahead`），不支持`后行断言`（`lookbehind`）和`后行否定断言`（`negative lookbehind`）。

---

## 先行断言

### 基础说明

`先行断言`（`lookahead`）指的是，`x`只有在`y`前面才匹配，必须写成`/x(?=y)/`。

`先行断言`括号之中的部分（`(?=%)`），是不计入返回结果的。

---

### 举例

只匹配`百分号`之前的`数字`。

```javascript
((log) => {
    const regex1 = /\d+(?=%)/;
    const regex2 = /\d+(?!%)/;
    const string = '100% of US presidents have been male';

    log(regex1.exec(string)); // ["100", index: 0, input: "100% of US presidents have been male", groups: undefined]
    log(regex2.exec(string)); // ["10", index: 0, input: "100% of US presidents have been male", groups: undefined]
})(console.log)
```

---

## 先行否定断言

### 基础说明

`先行否定断言`（`negative lookahead`）指的是，`x`只有不在`y`前面才匹配，必须写成`/x(?!y)/`。

`先行否定断言`括号之中的部分（`(?!%)`），是不计入返回结果的。

---

### 举例

只匹配不在`百分`号之前的`数字`。

```javascript
((log) => {
    const regex1 = /\d+(?=%)/;
    const regex2 = /\d+(?!%)/;
    const string = 'that’s all 44 of them';

    log(regex1.exec(string)); // null
    log(regex2.exec(string)); // ["44", index: 11, input: "that’s all 44 of them", groups: undefined]
})(console.log)
```

---

## 后行断言

### 基础说明

`ES2018` 引入`后行断言`（`lookbehind`），`V8` 引擎 `4.9` 版（`Chrome 62`）已经支持。

`后行断言`正好与`先行断言`相反，`x`只有在`y`后面才匹配，必须写成`/(?<=y)x/`。

---

### 举例

#### 美元匹配

只匹配`美元`符号之后的`数字`

```javascript
((log) => {
    const regex1 = /(?<=\$)\d+/;
    const regex2 = /(?<!\$)\d+/;
    const string = 'Benjamin Franklin is on the $100 bill';

    log(regex1.exec(string)); // ["100", index: 29, input: "Benjamin Franklin is on the $100 bill", groups: undefined]
    log(regex2.exec(string)); // ["00", index: 30, input: "Benjamin Franklin is on the $100 bill", groups: undefined]
})(console.log)
```

---

#### 字符串替换

使用`后行断言`进行`字符串`替换

```javascript
((log) => {
    const RE_DOLLAR_PREFIX_1 = /(?<=\$)foo/g;
    const RE_DOLLAR_PREFIX_2 = /(?<!\$)foo/g;
    const REPLACE_STR = 'bar';
    const string = '$foo %foo foo';

    // 只有在美元符号后面的foo才会被替换
    log(string.replace(RE_DOLLAR_PREFIX_1,REPLACE_STR)); // $bar %foo foo
    // 只有不在美元符号后面的foo才会被替换
    log(string.replace(RE_DOLLAR_PREFIX_2,REPLACE_STR)); // $foo %bar bar
})(console.log)
```

---

### tip

#### 组匹配顺序

`后行断言`的实现，需要先匹配`/(?<=y)x/`的`x`，然后再回到左边，匹配`y`的部分。

这种`先右后左`的执行顺序，与所有其他`正则`操作相反，导致了一些不符合预期的行为。

首先，`后行断言`的`组匹配`，与正常情况下结果是不一样的。

```javascript
((log) => {
    const regex1 = /(?<=(\d+)(\d+))$/;
    const regex2 = /^(\d+)(\d+)$/;
    const string = '1053';

    log(regex1.exec(string)); // ["", "1", "053", index: 4, input: "1053", groups: undefined]
    log(regex2.exec(string)); // ["1053", "105", "3", index: 0, input: "1053", groups: undefined]
})(console.log)
```

上面代码中，需要捕捉两个`组匹配`。

没有`后行断言`时，第一个括号是`贪婪模式`，第二个括号只能`捕获`一个字符，所以结果是`105`和`3`。

而`后行断言`时，由于执行顺序是从`右`到`左`，第二个括号是`贪婪模式`，第一个括号只能捕获一个`字符`，所以结果是`1`和`053`。

---

#### 反斜杠规范

`后行断言`的`反斜杠`引用，也与通常的`顺序`相反，必须放在对应的那个括号之前。

```javascript
((log) => {
    const regex1 = /(?<=(o)d\1)r/;
    const regex2 = /(?<=\1d(o))r/;
    const string = 'hodor';

    log(regex1.exec(string)); // null
    log(regex2.exec(string)); // ["r", "o", index: 4, input: "hodor", groups: undefined]
})(console.log)
```

上面代码中，如果`后行断言`的`反斜杠`引用（`\1`）放在括号的后面，就不会得到匹配结果，必须放在前面才可以。

因为`后行断言`是先从`左`到`右`扫描，发现匹配以后再回过头，从`右`到`左`完成`反斜杠`引用。

---

## 后行否定断言

### 基础说明

`后行否定断言`（`negative lookbehind`）则与`先行否定断言`相反，`x`只有不在`y`后面才匹配，必须写成`/(?<!y)x/`。

---

### 举例

只匹配不在`美元`符号后面的`数字`

```javascript
((log) => {
    const regex1 = /(?<=\$)\d+/;
    const regex2 = /(?<!\$)\d+/;
    const string = 'it’s is worth about €90';

    log(regex1.exec(string)); // null
    log(regex2.exec(string)); // ["90", index: 21, input: "it’s is worth about €90", groups: undefined]
})(console.log)
```

---
