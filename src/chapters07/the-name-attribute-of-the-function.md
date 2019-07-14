# 函数的name属性

## 基础说明

函数的`name`属性，返回该函数的函数名。

```javascript
function foo() {}
foo.name // "foo"
```

这个属性早就被浏览器广泛支持，但是直到 `ES6`，才将其写入了标准。

---

## 案例说明

### 具名函数name值

如果将一个`具名函数`赋值给一个变量，则 `ES5` 和 `ES6` 的`name`属性都返回这个`具名函数`原本的名字。

```javascript
const bar = function baz() {};

// ES5
bar.name // "baz"

// ES6
bar.name // "baz"
```

### Function构造的函数实例

`Function`构造函数返回的函数实例，`name`属性的值为`anonymous`。

```javascript
(new Function).name // "anonymous"
```

### bind返回的函数name值

`bind`返回的函数，`name`属性值会加上`bound`前缀。

```javascript
function foo() {};
foo.bind({}).name // "bound foo"

(function(){}).bind({}).name // "bound "

```

## 注意事项

需要注意的是，`ES6` 对这个属性的行为做出了一些修改。如果将一个`匿名函数`赋值给一个变量，`ES5` 的`name`属性，会返回空字符串，而 `ES6` 的`name`属性会返回实际的函数名。

```javascript
var f = function () {};

// ES5
f.name // ""

// ES6
f.name // "f"
```

上面代码中，变量`f`等于一个匿名函数，`ES5` 和 `ES6` 的`name`属性返回的值不一样。

---
