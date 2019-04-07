# String中正则相关方法

## ES5版相关方法

字符串对象共有 4 个方法，可以使用正则表达式：`match()`、`replace()`、`search()`和`split()`。

`ES6` 将这 4 个方法，在语言内部全部调用`RegExp`的实例方法，从而做到所有与正则相关的方法，全都定义在`RegExp`对象上。

- [String.prototype.match][match] 调用 `RegExp.prototype[Symbol.match]`
- [String.prototype.replace][replace] 调用 `RegExp.prototype[Symbol.replace]`
- [String.prototype.search][search] 调用 `RegExp.prototype[Symbol.search]`
- [String.prototype.split][split] 调用 `RegExp.prototype[Symbol.split]`

## 新提案

### 基础说明

[String.prototype.matchAll][matchAll]

如果一个`正则表达式`在字符串里面有多个`匹配`，现在一般使用`g`修饰符或`y`修饰符，在循环里面逐一取出。

```javascript
((log) => {
    const regex = /t(e)(st(\d?))/g;
    const string = 'test1test2test3';

    const matches = [];
    let match;
    while (match = regex.exec(string)) {
      matches.push(match);
    }

    log(matches);
  // [
  //   ["test1", "e", "st1", "1", index: 0, input: "test1test2test3"],
  //   ["test2", "e", "st2", "2", index: 5, input: "test1test2test3"],
  //   ["test3", "e", "st3", "3", index: 10, input: "test1test2test3"]
  // ]
})(console.log)

```

上面代码中，`while`循环取出每一轮的`正则匹配`，一共`三`轮。

目前有一个[提案][matchAll-notes]，增加了`String.prototype.matchAll`方法，可以一次性取出所有匹配。不过，它返回的是一个遍历器（`Iterator`），而不是数组。

```javascript
((log) => {
    const string = 'test1test2test3';

    // g 修饰符加不加都可以
    const regex = /t(e)(st(\d?))/g;

    for (const match of string.matchAll(regex)) {
      log(match);
    }
    // ["test1", "e", "st1", "1", index: 0, input: "test1test2test3"]
    // ["test2", "e", "st2", "2", index: 5, input: "test1test2test3"]
    // ["test3", "e", "st3", "3", index: 10, input: "test1test2test3"]
})(console.log)
```

上面代码中，由于`string.matchAll(regex)`返回的是遍历器，所以可以用`for...of`循环取出。

相对于返回数组，返回`遍历器`的好处在于，如果匹配结果是一个很大的数组，那么`遍历器`比较节省资源。

---

### 遍历器转为数组

`遍历器`转为数组是非常简单的，使用`...`运算符和`Array.from`方法就可以了。

```javascript
// 转为数组方法一
[...string.matchAll(regex)]

// 转为数组方法二
Array.from(string.matchAll(regex));
```

---

### tip

目前这个提案处于`stage 04`的阶段，但是预计实现年份是`2020`，所以暂时不要在生产环境中使用这个方法。

---

[match]: https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/String/match
[replace]: https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/String/replace
[search]: https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/String/search
[split]: https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/String/split
[matchAll]: https://github.com/tc39/String.prototype.matchAll
[matchAll-notes]: https://github.com/tc39/tc39-notes/blob/master/es9/2018-09/sept-25.md#update-on-stringprototypematchall