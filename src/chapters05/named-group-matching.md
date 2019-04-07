# 具名组匹配

## 历史原因

```javascript
const RE_DATE = /(\d{4})-(\d{2})-(\d{2})/;
```

上面代码中，正则表达式里面有三组`圆括号`。

使用`exec`方法，就可以将这三组匹配结果提取出来。

```javascript

const RE_DATE = /(\d{4})-(\d{2})-(\d{2})/;

const matchObj = RE_DATE.exec('1999-12-31');
const year = matchObj[1]; // 1999
const month = matchObj[2]; // 12
const day = matchObj[3]; // 31
```

`组匹配`的一个问题是，每一组的`匹配含义`不容易看出来，而且只能用`数字序号`（比如`matchObj[1]`）引用，要是组的`顺序`变了，引用的时候就必须修改`序号`。

## 基础说明

`ES2018` 引入了`具名组匹配（Named Capture Groups）`，允许为每一个组匹配指定一个名字，既便于阅读代码，又便于引用。

```javascript
((log) => {
    const RE_DATE = /(?<year>\d{4})-(?<month>\d{2})-(?<day>\d{2})/;

    const matchObj = RE_DATE.exec('1999-12-31');
    log(matchObj.groups.year); // 1999
    log(matchObj.groups.month);// 12
    log(matchObj.groups.day); // 31
})(console.log)
```

上面代码中，`具名组匹配`在圆括号内部，模式的头部添加`问号 + 尖括号 + 组名`（`?<year>`），然后就可以在`exec`方法返回结果的`groups`属性上引用该`组名`。

同时，数字序号（`matchObj[1]`）依然有效。

`具名组匹配`等于为每一组匹配加上了 `ID`，便于描述匹配的目的。

如果组的`顺序`变了，也不用改变匹配后的`处理代码`。

## 未匹配说明

如果`具名组`没有`匹配`，那么对应的`groups`对象属性会是`undefined`。

```javascript
((log) => {
    const RE_OPT_A = /^(?<as>a+)?$/;
    const matchObj = RE_OPT_A.exec('');

    log(matchObj.groups.as); // undefined
    log('as' in matchObj.groups); // true
})(console.log)
```

上面代码中，具名组`as`没有找到匹配，那么`matchObj.groups.as`属性值就是`undefined`，并且`as`这个键名在`groups`是始终存在的。

## 引用说明

如果要在正则表达式内部引用某个`具名组匹配`，可以使用`\k<组名>`的写法。

```javascript
((log) => {
    const RE_TWICE = /^(?<word>[a-z]+)!\k<word>$/;
    log(RE_TWICE.test('abc!abc')); // true
    log(RE_TWICE.test('abc!ab')); // false
})(console.log)
```

数字引用（`\1`）依然有效。

```javascript
((log) => {
    const RE_TWICE = /^(?<word>[a-z]+)!\1$/;
    log(RE_TWICE.test('abc!abc')); // true
    log(RE_TWICE.test('abc!ab')); // false
})(console.log)
```

这两种引用语法还可以同时使用。

```javascript
((log) => {
    const RE_TWICE = /^(?<word>[a-z]+)!\k<word>!\1$/;
    log(RE_TWICE.test('abc!abc!abc')); // true
    log(RE_TWICE.test('abc!abc!ab')); // false
})(console.log)
```

## 解构赋值和替换

有了`具名组匹配`以后，可以使用`解构赋值`直接从匹配结果上为`变量赋值`。

### 解构赋值

```javascript
((log) => {
    const {groups: {one, two}} = /^(?<one>.*):(?<two>.*)$/u.exec('foo:bar');
    log(one);  // foo
    log(two);  // bar
})(console.log)

```

---

### 字符串组名替换

字符串`替换`时，使用`$<组名>`引用具名组。

```javascript
((log) => {
    const re = /(?<year>\d{4})-(?<month>\d{2})-(?<day>\d{2})/u;
    log('2015-01-02'.replace(re, '$<day>/$<month>/$<year>')); // '02/01/2015'
})(console.log)

```

上面代码中，`replace`方法的第二个参数是一个`字符串`，而不是`正则表达式`。

---

### 函数组名替换

`replace`方法的第二个参数也可以是`函数`，该`函数`的参数序列如下。

```javascript
((log) => {
    const re = /(?<year>\d{4})-(?<month>\d{2})-(?<day>\d{2})/u;
    const res = '2015-01-02'.replace(re, (
        matched, // 整个匹配结果 2015-01-02
        capture1, // 第一个组匹配 2015
        capture2, // 第二个组匹配 01
        capture3, // 第三个组匹配 02
        position, // 匹配开始的位置 0
        S, // 原字符串 2015-01-02
        groups // 具名组构成的一个对象 {year, month, day}
        ) => {
        const {day, month, year} = groups;
        return `${day}/${month}/${year}`;
   });
   log(res);// 02/01/2015
})(console.log)
```

`具名组匹配`在原来的基础上，新增了最后一个`函数参数`：`具名组`构成的一个`对象`。

`函数`内部可以直接对这个对象进行`解构赋值`。