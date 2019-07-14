# 函数参数改进

## 函数参数的默认值

### 历史原因介绍

`ES6` 之前，不能直接为函数的参数指定`默认值`，只能采用变通的方法。

```javascript
function log(x, y) {
  y = y || 'World';
  console.log(x, y);
}

log('Hello') // Hello World
log('Hello', 'China') // Hello China
log('Hello', '') // Hello World
```

上面代码检查函数`log`的参数`y`有没有赋值，如果没有，则指定默认值为`World`。

这种写法的缺点在于，如果参数`y`赋值了，但是对应的布尔值为`false`，则该赋值不起作用。就像上面代码的最后一行，参数`y`等于空字符，结果被改为`默认值`。

为了避免这个问题，通常需要先判断一下参数`y`是否被赋值，如果没有，再等于`默认值`。

```javascript
if (typeof y === 'undefined') {
  y = 'World';
}
```

---

### ES6 改进

`ES6` 允许为函数的参数设置`默认值`，即直接写在参数定义的后面。

```javascript
function log(x, y = 'World') {
  console.log(x, y);
}

log('Hello') // Hello World
log('Hello', 'China') // Hello China
log('Hello', '') // Hello
```

可以看到，`ES6` 的写法比 `ES5` 简洁许多，而且非常自然。下面是另一个例子。

```javascript

function Point(x = 0, y = 0) {
  this.x = x;
  this.y = y;
}

const p = new Point();
p // { x: 0, y: 0 }
```

除了简洁，`ES6` 的写法还有两个好处：

- 阅读代码的人，可以立刻意识到哪些参数是可以省略的，不用查看函数体或文档。
- 有利于将来的代码优化，即使未来的版本在对外接口中，彻底拿掉这个参数，也不会导致以前的代码无法运行。

---

### 使用案例

> 与解构赋值结合使用

参数默认值可以与解构赋值的默认值，结合起来使用。

```javascript

function foo({x, y = 5}) {
  console.log(x, y);
}

foo({}) // undefined 5
foo({x: 1}) // 1 5
foo({x: 1, y: 2}) // 1 2
foo() // TypeError: Cannot read property 'x' of undefined
```

上面代码只使用了对象的`解构赋值`默认值，没有使用函数参数的默认值。只有当函数`foo`的参数是一个对象时，变量`x`和`y`才会通过`解构赋`值生成。

如果函数`foo`调用时没提供参数，变量`x`和`y`就不会生成，从而报错。通过提供函数参数的默认值，就可以避免这种情况。

```javascript

function foo({x, y = 5} = {}) {
  console.log(x, y);
}

foo() // undefined 5
```

上面代码指定，如果没有提供参数，函数`foo`的参数默认为一个`空对象`。

---

> 进阶案例

```javascript

// 写法一
function m1({x = 0, y = 0} = {}) {
  return [x, y];
}

// 写法二
function m2({x, y} = { x: 0, y: 0 }) {
  return [x, y];
}
```

上面两种写法都对函数的参数设定了默认值，区别是写法一函数参数的默认值是`空对象`，但是设置了对象`解构赋值`的`默认值`；写法二函数参数的默认值是一个有具体属性的对象，但是没有设置对象`解构赋值`的默认值。

```javascript

// 函数没有参数的情况
m1() // [0, 0]
m2() // [0, 0]

// x 和 y 都有值的情况
m1({x: 3, y: 8}) // [3, 8]
m2({x: 3, y: 8}) // [3, 8]

// x 有值，y 无值的情况
m1({x: 3}) // [3, 0]
m2({x: 3}) // [3, undefined]

// x 和 y 都无值的情况
m1({}) // [0, 0];
m2({}) // [undefined, undefined]

m1({z: 3}) // [0, 0]
m2({z: 3}) // [undefined, undefined]
```

---

> 结合fetch函数

[Fetch API][Fetch API] 提供了一个 `JavaScript`接口，用于访问和操纵`HTTP`管道的部分，例如`请求`和`响应`。它还提供了一个全局 `fetch()`方法，该方法提供了一种简单，合理的方式来跨网络异步获取资源。

这种功能以前是使用  `XMLHttpRequest`实现的。`Fetch`提供了一个更好的替代方法，可以很容易地被其他技术使用，例如 `Service Workers`。

`Fetch`还提供了单个逻辑位置来定义其他`HTTP`相关概念，例如`CORS`和`HTTP`的扩展。

请注意，`fetch`规范与`jQuery.ajax()`主要有两种方式的不同，牢记：

- 当接收到一个代表错误的 `HTTP` 状态码时，从 `fetch()`返回的 `Promise` 不会被标记为 `reject`， 即使该`HTTP` 响应的状态码是 `404` 或 `500`。相反，它会将 `Promise` 状态标记为 `resolve` （但是会将 `resolve` 的返回值的 `ok` 属性设置为 `false` ），仅当网络故障时或请求被阻止时，才会标记为 `reject`。
- 默认情况下，`fetch` 不会从服务端发送或接收任何 `cookies`, 如果站点依赖于用户 `session`，则会导致未经认证的请求（要发送 `cookies`，必须设置 `credentials` 选项）。自从2017年8月25日后，默认的`credentials`政策变更为`same-origin`，`Firefox`也在`61.0b13`中改变默认值

```javascript

const postData = (url, {body,method})=> {
  // Default options are marked with *
  return fetch(url, {
    body: body || JSON.stringify(data), // must match 'Content-Type' header
    cache: 'no-cache', // *default, no-cache, reload, force-cache, only-if-cached
    credentials: 'same-origin', // include, same-origin, *omit
    headers: {
      'user-agent': 'Mozilla/4.0 MDN Example',
      'content-type': 'application/json'
    },
    method: method || 'POST', // *GET, POST, PUT, DELETE, etc.
    mode: 'cors', // no-cors, cors, *same-origin
    redirect: 'follow', // manual, *follow, error
    referrer: 'no-referrer', // *client, no-referrer
  })
  .then(response => response.json()) // parses response to JSON
}

postData('http://example.com', {})
  .then(data => console.log(data)) // JSON from `response.json()` call
  .catch(error => console.error(error))

postData('http://example.com')
  .then(data => console.log(data))
  .catch(error => console.error(error))
// 报错
```

上面代码中，如果函数`postData`的第二个参数是一个对象，就可以为它的三个属性设置默认值。这种写法不能省略第二个参数，如果结合函数参数的默认值，就可以省略第二个参数。这时，就出现了双重默认值。

```javascript

const postData = (url, {body,method} = {})=> {
  // Default options are marked with *
  return fetch(url, {
    body: body || JSON.stringify(data), // must match 'Content-Type' header
    cache: 'no-cache', // *default, no-cache, reload, force-cache, only-if-cached
    credentials: 'same-origin', // include, same-origin, *omit
    headers: {
      'user-agent': 'Mozilla/4.0 MDN Example',
      'content-type': 'application/json'
    },
    method: method || 'POST', // *GET, POST, PUT, DELETE, etc.
    mode: 'cors', // no-cors, cors, *same-origin
    redirect: 'follow', // manual, *follow, error
    referrer: 'no-referrer', // *client, no-referrer
  })
  .then(response => response.json()) // parses response to JSON
}

postData('http://example.com')
  .then(data => console.log(data)) // JSON from `response.json()` call
  .catch(error => console.error(error))
```

上面代码中，函数`postData`没有第二个参数时，函数参数的默认值就会生效，然后才是`解构赋值`的默认值生效，变量`method`才会取到对应的接口给回值`response.json()`。

---

> 利用参数默认值，可以指定某一个参数不得省略，如果省略就抛出一个错误。

```javascript
function throwIfMissing() {
  throw new Error('Missing parameter');
}

function foo(mustBeProvided = throwIfMissing()) {
  return mustBeProvided;
}

foo()
// Error: Missing parameter
```

上面代码的`foo`函数，如果调用的时候没有参数，就会调用默认值`throwIfMissing`函数，从而抛出一个错误。

从上面代码还可以看到，参数`mustBeProvided`的默认值等于`throwIfMissing`函数的运行结果（注意函数名`throwIfMissing`之后有一对圆括号），这表明参数的默认值不是在定义时执行，而是在运行时执行。如果参数已经赋值，默认值中的函数就不会运行。

---

> 将参数默认值设为undefined，表明这个参数是可以省略的。

```javascript
function foo(optional = undefined) { ··· }
```

---

### 注意事项

> 参数默认值只会与`undefined`进行对比

如果传入`undefined`，将触发该参数等于默认值，`null`则没有这个效果。

```javascript
function foo(x = 5, y = 6) {
  console.log(x, y);
}

foo(undefined, null)
// 5 null
```

上面代码中，`x`参数对应`undefined`，结果触发了默认值，`y`参数等于`null`，就没有触发默认值。

---

> 参数变量是默认声明的，所以不能用let或const再次声明。

```javascript

function foo(x = 5) {
  let x = 1; // error
  const x = 2; // error
}
```

上面代码中，参数变量`x`是默认声明的，在函数体中，不能用`let`或`const`再次声明，否则会报错。

> 使用参数默认值时，函数不能有同名参数

```javascript

// 不报错
function foo(x, x, y) {
  // ...
}

// 报错
function foo(x, x, y = 1) {
  // ...
}
// SyntaxError: Duplicate parameter name not allowed in this context
```

另外，一个容易忽略的地方是，参数默认值不是传值的，而是每次都重新计算默认值表达式的值。也就是说，参数默认值是惰性求值的。

```javascript

let x = 99;
function foo(p = x + 1) {
  console.log(p);
}

foo() // 100

x = 100;
foo() // 101
```

上面代码中，参数`p`的默认值是`x + 1`。这时，每次调用函数`foo`，都会重新计算`x + 1`，而不是默认`p`等于 `100`。

---

> 参数默认值一般放在可以缺省的尾部

通常情况下，定义了默认值的参数，应该是函数的尾参数。因为这样比较容易看出来，到底省略了哪些参数。如果非尾部的参数设置默认值，实际上这个参数是没法省略的。

```javascript
// 例一
function f(x = 1, y) {
  return [x, y];
}

f() // [1, undefined]
f(2) // [2, undefined])
f(, 1) // 报错
f(undefined, 1) // [1, 1]

// 例二
function f(x, y = 5, z) {
  return [x, y, z];
}

f() // [undefined, 5, undefined]
f(1) // [1, 5, undefined]
f(1, ,2) // 报错
f(1, undefined, 2) // [1, 5, 2]
```

上面代码中，有默认值的参数都不是尾参数。这时，无法只省略该参数，而不省略它后面的参数，除非显式输入`undefined`。

---

> 函数的 length 属性

指定了默认值以后，函数的`length`属性，将返回没有指定默认值的参数个数。也就是说，指定了默认值后，`length`属性将失真。

```javascript
(function (a) {}).length // 1
(function (a = 5) {}).length // 0
(function (a, b, c = 5) {}).length // 2
```

上面代码中，`length`属性的返回值，等于函数的参数个数减去指定了默认值的参数个数。比如，上面最后一个函数，定义了 `3` 个参数，其中有一个参数`c`指定了默认值，因此`length`属性等于`3`减去`1`，最后得到`2`。

这是因为`length`属性的含义是，该函数预期传入的参数个数。某个参数指定默认值以后，预期传入的参数个数就不包括这个参数了。

如果设置了默认值的参数不是尾参数，那么`length`属性也不再计入后面的参数了。

```javascript
(function (a = 0, b, c) {}).length // 0
(function (a, b = 1, c) {}).length // 1
```

---

> 作用域限制

一旦设置了参数的默认值，函数进行声明初始化时，参数会形成一个单独的作用域（`context`）。等到初始化结束，这个作用域就会消失。这种语法行为，在不设置参数默认值时，是不会出现的。

```javascript
var x = 1;

function f(x, y = x) {
  console.log(y);
}

f(2) // 2
```

上面代码中，参数`y`的默认值等于变量`x`。调用函数f时，参数形成一个单独的作用域。在这个作用域里面，默认值变量`x`指向第一个参数`x`，而不是全局变量`x`，所以输出是2。

再看下面的例子。

```javascript
let x = 1;

function f(y = x) {
  let x = 2;
  console.log(y);
}

f() // 1
```

上面代码中，函数`f`调用时，参数`y = x`形成一个单独的作用域。这个作用域里面，变量`x`本身没有定义，所以指向外层的全局变量`x`。函数调用时，函数体内部的局部变量`x`影响不到默认值变量`x`。

如果此时，全局变量`x`不存在，就会报错。

```javascript
function f(y = x) {
  let x = 2;
  console.log(y);
}

f() // ReferenceError: x is not defined
```

下面这样写，也会报错。

```javascript
var x = 1;

function foo(x = x) {
  // ...
}

foo() // ReferenceError: x is not defined
```

上面代码中，参数`x = x`形成一个单独作用域。实际执行的是`let x = x`，由于`暂时性死区`的原因，这行代码会报错`x 未定义`。

如果参数的默认值是一个`函数`，该函数的作用域也遵守这个规则。请看下面的例子。

```javascript
let foo = 'outer';

function bar(func = () => foo) {
  let foo = 'inner';
  console.log(func());
}

bar(); // outer
```

上面代码中，函数`bar`的参数`func`的默认值是一个匿名函数，返回值为变量`foo`。函数参数形成的单独作用域里面，并没有定义变量`foo`，所以`foo`指向外层的全局变量`foo`，因此输出`outer`。

如果写成下面这样，就会报错。

```javascript
function bar(func = () => foo) {
  let foo = 'inner';
  console.log(func());
}

bar() // ReferenceError: foo is not defined
```

上面代码中，匿名函数里面的`foo`指向函数外层，但是函数外层并没有声明变量`foo`，所以就报错了。

下面是一个更复杂的例子。

```javascript
var x = 1;
function foo(x, y = function() { x = 2; }) {
  var x = 3;
  y();
  console.log(x);
}

foo() // 3
x // 1
```

上面代码中，函数`foo`的参数形成一个单独作用域。这个作用域里面，首先声明了变量`x`，然后声明了变量`y`，`y`的默认值是一个匿名函数。

这个匿名函数内部的变量`x`，指向同一个作用域的第一个参数`x`。函数`foo`内部又声明了一个内部变量`x`，该变量与第一个参数`x`由于不是同一个作用域，所以不是同一个变量，因此执行`y`后，内部变量`x`和外部全局变量`x`的值都没变。

如果将`var x = 3`的`var`去除，函数`foo`的内部变量`x`就指向第一个参数`x`，与匿名函数内部的`x`是一致的，所以最后输出的就是`2`，而外层的全局变量`x`依然不受影响。

```javascript
var x = 1;
function foo(x, y = function() { x = 2; }) {
  x = 3;
  y();
  console.log(x);
}

foo() // 2
x // 1
```

## rest 参数

### 基础说明

`ES6` 引入 `rest 参数`（形式为`...变量名`），用于获取函数的多余参数，这样就不需要使用`arguments`对象了。rest 参数搭配的变量是一个数组，该变量将多余的参数放入数组中。

---

### 案例说明

> 基础案例

`add`函数是一个求和函数，利用 `rest` 参数，可以向该函数传入任意数目的参数。

```javascript
function add(...values) {
  let sum = 0;

  for (var val of values) {
    sum += val;
  }

  return sum;
}

add(2, 5, 3) // 10
```

---

> rest 参数代替arguments变量

```javascript
// arguments变量的写法
function sortNumbers() {
  return Array.prototype.slice.call(arguments).sort();
}

// rest参数的写法
const sortNumbers = (...numbers) => numbers.sort();
```

上面代码的两种写法，比较后可以发现，`rest` 参数的写法更自然也更简洁。

`arguments`对象不是数组，而是一个类似`数组`的对象。所以为了使用`数组`的方法，必须使用`Array.prototype.slice.call`先将其转为`数组`。

---

> rest 参数改写数组push方法

```javascript
(()=>{
  const {log,error} = console;
  function push(array, ...items) {
    items.forEach(function(item) {
      array.push(item);
    });
    return array;
  }

  var a = [];
  log(push(a, 1, 2, 3))// [1, 2, 3]
})()
```

---

### 注意事项

> 函数的length失真

`rest` 参数也不会计入`length`属性。

```javascript
(function(a) {}).length  // 1
(function(...a) {}).length  // 0
(function(a, ...b) {}).length  // 1
```

---

> rest 参数之后不能再有其他参数

rest 参数之后不能再有其他参数（即只能是最后一个参数），否则会报错。

```javascript
// Uncaught SyntaxError: Rest parameter must be last formal parameter
function f(a, ...b, c) {
  // ...
}
```

---

## 函数参数的尾逗号

`ES2017` 允许函数的最后一个参数有尾逗号（`trailing comma`）。

此前，函数定义和调用时，都不允许最后一个参数后面出现逗号。

```javascript
function clownsEverywhere(
  param1,
  param2
) { /* ... */ }

clownsEverywhere(
  'foo',
  'bar'
);
```

上面代码中，如果在`param2`或`bar`后面加一个逗号，就会报错。

如果像上面这样，将参数写成多行（即每个参数占据一行），以后修改代码的时候，想为函数`clownsEverywhere`添加第三个参数，或者调整参数的次序，就势必要在原来最后一个参数后面添加一个逗号。

这对于`版本管理系统`来说，就会显示添加逗号的那一行也发生了变动。这看上去有点冗余，因此新的语法允许定义和调用时，尾部直接有一个逗号。

```javascript
function clownsEverywhere(
  param1,
  param2,
) { /* ... */ }

clownsEverywhere(
  'foo',
  'bar',
);
```

这样的规定也使得，`函数参数`与`数组`和`对象`的`尾逗号`规则，保持一致了。

---

[Fetch API]: https://developer.mozilla.org/zh-CN/docs/Web/API/Fetch_API/Using_Fetch
