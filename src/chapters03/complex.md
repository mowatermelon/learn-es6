# 复杂数据结构

## 数组的解构赋值

### 基础用法

> 完全解构

```javascript
(function (log) {
    // 基础数组解构
    const [a, b, c] = [1, 2, 3];
    log(a); // 1
    log(b); // 2
    log(c); // 3

    // 嵌套数组解构
    const [foo, [[bar], baz]] = [1, [[2], 3]];
    log(foo); // 1
    log(bar); // 2
    log(baz); // 3

    const [ , , third] = ["foo", "bar", "baz"];
    log(third); // "baz"

    const [x, , y] = [1, 2, 3];
    log(x); // 1
    log(y); // 3

    const [head, ...tail] = [1, 2, 3, 4];
    log(head); // 1
    log(tail); // [2, 3, 4]

    const [e, f, ...g] = ['a'];
    log(e); // "a"
    log(f); // undefined
    log(g); // []
})(console.log)
```

---

> 不完全解构

另一种情况是`不完全解构`，即等号左边的模式，只匹配`一部分`的等号右边的数组。这种情况下，解构依然可以成功。

```javascript
(function (log) {
    const [x, y] = [1, 2, 3];
    log(x); // 1
    log(y); // 2

    const [a, [b], d] = [1, [2, 3], 4];
    log(a); // 1
    log(b); // 2
    log(d); // 4
})(console.log)
```

上面两个例子，都属于`不完全解构`，但是可以成功。

---

> Set结构解构

对于 `Set` 结构，也可以使用`数组`的解构赋值。

```javascript
(function (log) {
    const [x, y, z] = new Set(['a', 'b', 'c']);
    log(x); // "a"
})(console.log)
```

---

> Generator 函数解构

事实上，只要某种数据结构具有 `Iterator` 接口，都可以采用`数组`形式的`解构赋值`。

```javascript

(function (log) {
    function* fibs() {
        const a = 0;
        const b = 1;
        while (true) {
            yield a;
            [a, b] = [b, a + b];
        }
    }

    const [first, second, third, fourth, fifth, sixth,seventh] = fibs();
    // yield 等待的是未做值替换之前的a值
    // 第一次 a 为 0 ，b 为 1
    // 第二次 a 为 1 ，b 为 1
    // 第三次 a 为 1 ，b 为 2
    // 第四次 a 为 2 ，b 为 3
    // 第五次 a 为 3 ，b 为 5
    // 第六次 a 为 5 ，b 为 8
    log(first); // 0
    log(second); // 1
    log(third); // 1
    log(fourth); // 2
    log(fifth); // 3
    log(sixth); // 5
    log(seventh); // 8
})(console.log)
```

上面代码中，`fibs`是一个 `Generator` 函数（参见`《Generator 函数》`一章），原生具有 `Iterator` 接口。解构赋值会依次从这个`接口`获取值。

---

### 设置默认值

> 基础使用

解构赋值允许指定默认值。

```javascript
(function (log) {
    const [foo = true] = [];
    log(foo); // true

    const [x, y = 'b'] = ['a'];
    log(x);// 'a'
    log(y);// 'b'

    const [a, b = 'b'] = ['a', undefined];
    log(a);// 'a'
    log(b);// 'b'
})(console.log)
```

---

> 使用表达式

如果默认值是一个`表达式`，那么这个`表达式`是`惰性求值`的，即只有在用到的时候，才会`求值`。

```javascript
(function (log) {
    function f() {
        console.log('aaa');
    }

    const [x = f()] = [1];
    log(x);// 1
})(console.log)
```

上面代码中，因为`x`能取到值，所以函数`f`根本不会执行。上面的代码其实等价于下面的代码。

```javascript
(function (log) {
    const x;
    if ([1][0] === undefined) {
        x = f();
    } else {
        x = [1][0];
    }
    log(x);// 1
})(console.log)
```

---

> 引用其他变量值

`默认值`可以引用`解构赋值`的`其他变量`，但该`变量`必须`已经声明`。

```javascript
(function (log) {
    const [x1 = 1, y1 = x1] = [];
    log(x1);// 1
    log(y1);// 1

    const [x2 = 1, y2 = x2] = [2];
    log(x2);// 2
    log(y2);// 3

    const [x3 = 1, y3 = x3] = [1, 2];
    log(x3);// 1
    log(y3);// 2

    const [x4 = y4, y4 = 1] = [];     // ReferenceError: y is not defined
})(console.log)
```

上面最后一个`表达式`之所以会报错，是因为`x4`用`y4`做默认值时，`y4`还没有`声明`。

---

### 注意事项

> 解构不成功

如果解构不成功，变量的值就等于`undefined`。

```javascript

const [foo] = [];
const [bar, foo] = [1];
```

以上两种情况都属于解构不成功，`foo`的值都会等于`undefined`。

---

> 不可遍历结构

如果等号的右边不是`数组`（或者严格地说，不是`可遍历`的结构，参见`《Iterator》`一章），那么将会报错。

```javascript
(function (log) {
    // 报错
    const [foo1] = 1;// TypeError: 1 is not iterable
    const [foo2] = false;// TypeError: false is not iterable
    const [foo3] = NaN;// TypeError: NaN is not iterable
    const [foo4] = undefined;// TypeError: undefined is not iterable
    const [foo5] = null;// TypeError: null is not iterable
    const [foo6] = {};// TypeError: {} is not iterable
})(console.log)
```

上面的语句都会`报错`，因为等号右边的值，要么转为`对象`以后不具备 `Iterator` 接口（前五个表达式），要么`本身`就不具备 `Iterator` 接口（最后一个表达式）。

---

## 对象的解构赋值

### 基础用法

> 基础使用

`解构`不仅可以用于`数组`，还可以用于`对象`。

`对象`的`解构`与`数组`有一个重要的不同。数组的元素是按`次序`排列的，`变量`的`取值`由它的`位置`决定；

而`对象`的`属性`没有`次序`，`变量`必须与`属性`同名，才能取到`正确`的值。

```javascript
(function (log) {
    const {
        bar,
        foo
    } = {
        foo: "aaa",
        bar: "bbb"
    };
    log(foo); // "aaa"
    log(bar); // "bbb"

    const {
        baz
    } = {
        foo: "aaa",
        bar: "bbb"
    };
    log(baz); // undefined
})(console.log)
```

上面代码的第一个例子，等号左边的两个变量的`次序`，与等号右边两个同名属性的`次序`不一致，但是对取值完全没有影响。

第二个例子的`变量`没有对应的同名`属性`，导致取不到值，最后等于`undefined`。

> 变量名与属性名不一致

如果`变量名`与`属性名`不一致，必须写成下面这样。

```javascript
(function (log) {
    const {
        foo: baz
    } = {
        foo: 'aaa',
        bar: 'bbb'
    };
    log(baz); // "aaa"

    const obj = {
        first: 'hello',
        last: 'world'
    };
    const {
        first: f,
        last: l
    } = obj;
    log(f); // 'hello'
    log(l); // 'world'
})(console.log)
```

这实际上说明，`对象`的`解构赋值`是下面形式的`简写`（参见`《对象的扩展》`一章）。

```javascript
(function (log) {
    const {
        foo: foo,
        bar: bar
    } = {
        foo: "aaa",
        bar: "bbb"
    };
    log(foo); // "aaa"
    log(bar); // "bbb"
})(console.log)
```

也就是说，对象的`解构赋值`的`内部机制`，是先找到`同名属性`，然后再赋给对应的`变量`。

真正被`赋值`的是`后者`，而不是`前者`。

```javascript
(function (log) {
    const {
        foo: baz
    } = {
        foo: "aaa",
        bar: "bbb"
    };
    log(baz); // "aaa"
    log(foo); // ReferenceError: foo is not defined
})(console.log)
```

上面代码中，`foo`是匹配的`模式`，`baz`才是`变量`。

真正被`赋值`的是变量`baz`，而不是模式`foo`。

---

> 解构对象方法

`对象`的`解构赋值`，可以很方便地将现有`对象`的方法，赋值到某个`变量`。

```javascript
(function (log) {
    const {
        log: log1,
        sin,
        cos
    } = Math;
    log(log1); // [Function: log]
    log(sin); // [Function: sin]
    log(cos); // [Function: cos]
})(console.log)
```

上面代码将`Math`对象的`对数`、`正弦`、`余弦`三个方法，`赋值`到对应的`变量`上，使用起来就会方便很多。

---

> 属性名表达式

由于`数组`本质是特殊的`对象`，因此可以对`数组`进行`对象属性`的`解构`。

```javascript
(function (log) {
    const arr = [1, 2, 3];
    const {
        0: first,
        [arr.length - 1]: last
    } = arr;
    log(first); // 1
    log(last); // 3
})(console.log)
```

上面代码对数组进行对象解构。数组`arr`的`0`键对应的值是`1`，`[arr.length - 1]`就是`2`键，对应的值是`3`。

`方括号`这种写法，属于`属性名表达式`（参见`《对象的扩展》`一章）。

---

> 基础嵌套对象解构

与`数组`一样，解构也可以用于`嵌套结构`的`对象`。

```javascript
(function (log) {
    const obj = {
        p: [
            'Hello',
            {
                y: 'Melon'
            }
        ]
    };

    const {
        p: [x, {
            y
        }]
    } = obj;
    log(x); // "Hello"
    log(y); // "Melon"
})(console.log)
```

注意，这时`p`是`模式`，不是`变量`，因此不会被`赋值`。

如果`p`也要作为`变量`赋值，可以写成下面这样。

```javascript
(function (log) {
    const obj = {
        p: [
            'Hello',
            {
                y: 'Melon'
            }
        ]
    };

    const {
        p,
        p: [x, {
            y
        }]
    } = obj;
    log(x); // "Hello"
    log(y); // "Melon"
    log(p); // [ 'Hello', { y: 'Melon' } ]
})(console.log)
```

---

> 多重嵌套对象解构

与`数组`一样，解构也可以用于`嵌套结构`的`对象`。

```javascript
(function (log) {
    const node = {
        loc: {
            start: {
                line: 1,
                column: 5
            }
        }
    };

    const {
        loc,
        loc: {
            start
        },
        loc: {
            start: {
                line
            }
        }
    } = node;
    log(line); // 1
    log(loc); // { start: { line: 1, column: 5 } }
    log(start); // { line: 1, column: 5 }
})(console.log)
```

上面代码有三次解构赋值，分别是对`loc`、`start`、`line`三个`属性`的`解构赋值`。

注意，最后一次对`line`属性的`解构赋值`之中，只有`line`是`变量`，`loc`和`start`都是`模式`，不是`变量`。

---

> 结合数组进行嵌套赋值

```javascript
(function (log) {
    const obj = {};
    const arr = [];

    ({
        foo: obj.prop,
        bar: arr[0]
    } = {
        foo: 123,
        bar: true
    });

    log(obj); // { prop: 123 }
    log(arr); // [ true ]
})(console.log)
```

---

### 设置默认值

`对象`的`解构`也可以指定`默认值`。

```javascript
(function (log) {
    const {
        x1 = 3
    } = {};
    log(x1); // 3

    const {
        x2,
        y2 = 5
    } = {
        x2: 1
    };
    log(x2); // 1
    log(y2); // 5

    const {
        x3: y3 = 3
    } = {};
    // log(x3); // ReferenceError: x3 is not defined
    log(y3); // 3

    const {
        x4: y4 = 3
    } = {
        x: 5
    };
    // log(x4); // ReferenceError: x4 is not defined
    log(y4); // 3

    const {
        message: msg = 'Something went wrong'
    } = {};
    log(msg); // "Something went wrong"
})(console.log)
```

`默认值`生效的条件是，对象的`属性值`严格等于`undefined`。

```javascript
(function (log) {
    const {
        x1 = 3
    } = {
        x1: undefined
    };
    log(x1); // 3

    const {
        x2 = 3
    } = {
        x2: null
    };
    log(x2); // null

})(console.log)
```

上面代码中，属性`x`等于`null`，因为`null`与`undefined`不严格相等，所以是个有效的`赋值`，导致默认值`3`不会生效。

---

### 注意事项

> 解构不成功

如果解构不成功，变量的值就等于`undefined`。

```javascript
(function (log) {
    const {foo} = {bar: 'baz'};
    log(foo); // undefined
})(console.log)
```

---

> 父属性不存在

如果解构模式是`嵌套`的对象，而且`子对象`所在的`父属性`不存在，那么将会`报错`。

```javascript
(function (log) {
    // 报错
    const {foo: {bar}} = {baz: 'baz'};// TypeError: Cannot destructure property `bar` of 'undefined' or 'null'.

    const _tmp = {baz: 'baz'};
    _tmp.foo.bar // Cannot read property 'bar' of undefined
})(console.log)
```

上面代码中，等号左边对象的`foo`属性，对应一个`子对象`。该`子对象`的`bar`属性，`解构`时会报错。

原因很简单，因为`foo`这时等于`undefined`，类似于上文的`_tmp.foo.bar`,再取`子属性`就会报错。

---

> 已经声明的变量

如果要将一个已经`声明`的`变量`用于`解构赋值`，必须非常小心。

```javascript
(function (log) {
    // 错误的写法
    const x;
    {x} = {x: 1};
    // SyntaxError: Unexpected token =
})(console.log)
```

上面代码的写法会报错，因为 `JavaScript` 引擎会将`{x}`理解成一个代码块，从而发生`语法错误`。

只有不将`大括号`写在`行首`，避免 `JavaScript` 将其解释为`代码块`，才能解决这个问题。

```javascript
(function (log) {
    // 正确的写法
    const x;
    ({x} = {x: 1});
    log(x);// 1
})(console.log)
```

上面代码将整个`解构赋值`语句，放在一个`圆括号`里面，就可以正确执行。

---

## 函数的解构赋值

### 基础用法

> 基础使用

`函数`的`参数`也可以使用`解构赋值`。

```javascript
(function (log) {
    function add([x, y]) {
        return x + y;
    }

    log(add([1, 2])); // 3
})(console.log)
```

上面代码中，函数`add`的`参数`表面上是一个`数组`，但在传入`参数`的那一刻，`数组参数`就被解构成变量`x`和`y`。对于`函数内部`的代码来说，它们能感受到的参数就是`x`和`y`。

---

> 结合箭头函数使用

`函数`的`参数`也可以使用`解构赋值`。

```javascript
(function (log) {
    const arr = [
        [1, 2],
        [3, 4]
    ].map(([a, b]) => a + b);
    log(arr); // [ 3, 7 ]
})(console.log)
```

---

### 设置默认值

`函数参数`的`解构`也可以使用`默认值`。

> 函数对象参数属性使用默认值

```javascript
(function (log) {
    function move({
        x = 0,
        y = 0
    } = {}) {
        return [x, y];
    }

    log(move({
        x: 3,
        y: 8
    })); // [3, 8]
    log(move({
        x: 3
    })); // [3, 0]
    log(move({})); // [0, 0]
    log(move()); // [0, 0]
})(console.log)
```

上面代码中，函数`move`的`参数`是一个`对象`，通过对这个`对象`进行`解构`，得到变量`x`和`y`的值。如果`解构`失败，`x`和`y`等于`默认值`。

---

> 函数对象参数使用默认值

```javascript
(function (log) {
    function move({
        x,
        y
    } = {
        x: 0,
        y: 0
    }) {
        return [x, y];
    }

    log(move({
        x: 3,
        y: 8
    })); // [3, 8]
    log(move({
        x: 3
    })); // [3, undefined]
    log(move({})); // [undefined, undefined]
    log(move()); // [0, 0]
})(console.log)
```

上面代码是为函数`move`的`参数`指定`默认值`，而不是为变量`x`和`y`指定`默认值`，所以会得到与前一种写法不同的结果。

---

### 注意事项

`undefined`就会触发`函数参数`的`默认值`。

```javascript
(function (log) {
    const arr = [1, undefined, 3].map((x = 'yes') => x);
    log(arr); // [ 1, 'yes', 3 ]
})(console.log)
```

---
