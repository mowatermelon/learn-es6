
# 用途

## 交换变量的值

```javascript
(function (log) {
    let x = 1;
    let y = 2;
    log(x);// 1
    log(y);// 2
    [x, y] = [y, x];
    log(x);// 2
    log(y);// 1

})(console.log)
```

上面代码交换变量`x`和`y`的值，这样的写法不仅`简洁`，而且`易读`，`语义`非常清晰。

---

## 从函数返回多个值

`函数`只能返回一个`值`，如果要返回多个`值`，只能将它们放在`数组`或者`对象`里返回。

有了`解构赋值`，取出这些`值`就非常方便。

```javascript
(function (log) {
    // 返回一个数组
    function example1() {
        return [1, 2, 3];
    }

    let [a, b, c] = example1();
    log(a); // 1
    log(b); // 2
    log(c); // 3

    // 返回一个对象
    function example2() {
        return {
            foo: 1,
            bar: 2
        };
    }
    let {
        foo,
        bar
    } = example2();

    log(foo); // 1
    log(bar); // 2
})(console.log)

```

---

## 函数参数的定义

`解构赋值`可以方便地将一组`参数`与`变量名`对应起来。

```javascript

// 参数是一组有次序的值
function f([x, y, z]) { ... }
f([1, 2, 3]);

// 参数是一组无次序的值
function f({x, y, z}) { ... }
f({z: 3, y: 2, x: 1});
```

---

## 函数参数的默认值

指定参数的`默认值`，就避免了在函数体内部再写`var foo = config.foo || 'default foo';`这样的语句。

```javascript
jQuery.ajax = function (url, {
  async = true,
  beforeSend = function () {},
  cache = true,
  complete = function () {},
  crossDomain = false,
  global = true,
  // ... more config
} = {}) {
  // ... do stuff
};
```

---

## 提取 JSON 数据

`解构赋值`对提取 `JSON` 对象中的`数据`，尤其有用。

```javascript

(function (log) {
    const jsonData = {
        id: 42,
        status: "OK",
        data: [867, 5309]
    };

    const {
        id,
        status,
        data: number
    } = jsonData;

    log(id, status, number);
    // 42, "OK", [867, 5309]
})(console.log)

```

上面代码可以`快速`提取 `JSON` 数据的值。

---

## 遍历 Map 结构

任何部署了 `Iterator 接口`的对象，都可以用`for...of`循环遍历。`Map` 结构原生支持 `Iterator 接口`，配合`变量`的`解构赋值`，获取`键名`和`键值`就非常方便。

```javascript
(function (log) {
    const map = new Map();
    map.set('first', 'hello');
    map.set('second', 'melon');

    for (let [key, value] of map) {
        log(key + " is " + value);
    }
    // first is hello
    // second is melon
})(console.log)

```

如果只想获取`键名`，或者只想获取`键值`，可以写成下面这样。

```javascript
// 获取键名
for (let [key] of map) {
  // ...
}

// 获取键值
for (let [,value] of map) {
  // ...
}

```

---

## 获取指定模块的指定方法

加载`模块`时，往往需要指定需要获取哪些`方法`，`解构赋值`使得获取`语句`非常清晰。

```javascript

const {a,b} = require('xxx');

const { SourceMapConsumer, SourceNode } = require("source-map");
```

---