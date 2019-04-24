# 箭头函数

## 基本说明

`ES6` 允许使用`箭头`（`=>`）定义函数。`箭头函数表达式`的`语法`比函数表达式更`简洁`，并且没有自己的`this`，`arguments`，`super`或 `new.target`。这些`函数表达式`更适用于那些本来需要`匿名函数`的地方，并且它们不能用作`构造函数`。

[箭头函数基础分析图][how-to-use-arrow]

---

### 基础语法

```javascript
(param1, param2, …, paramN) => { statements }
// 等同于
// function(param1, param2, …, paramN){ return {statements}; }

(param1, param2, …, paramN) => expression
// 等同于
// function(param1, param2, …, paramN){ return expression; }

// Parentheses are optional when there's only one parameter name:
// 当只有一个参数时，圆括号是可选的
(singleParam) => { statements }
singleParam => { statements }

// 等同于
// function(singleParam){ return expression; }

// The parameter list for a function with no parameters should be written with a pair of parentheses.
// 没有参数的函数应该写成一对圆括号。
() => { statements }
```

---

### 高阶语法

```javascript
// Parenthesize the body of function to return an object literal expression:
// 加括号的函数体返回对象字面表达式：
params => ({foo: bar})

// Rest parameters and default parameters are supported
// 支持剩余参数和默认参数
(param1, param2, ...rest) => { statements }
(param1 = defaultValue1, param2, …, paramN = defaultValueN) => {
statements }

// Destructuring within the parameter list is also supported
// 同样支持参数列表解构
var f = ([a, b] = [1, 2], {x: c} = {x: a + b}) => a + b + c;
f(); // 6
```

如果`箭头函数`的`代码块`部分多于一条`语句`，就要使用`大括号`将它们括起来，并且使用`return`语句返回。

```javascript
(() => {
    const { log } = console;
    const sum = (num1, num2) => { return num1 + num2; }
})()
```

---

## 注意事项说明

### 返回值为对象

由于`大括号`被解释为`代码块`，所以如果`箭头函数`直接返回一个对象，必须在`对象`外面加上括号，否则会报错。

```javascript
(() => {
    const { log } = console;
    // 报错
    // let getTempItem = id => { id: id, name: "Temp" };

    // 不报错 但是没有取得指定返回值
    let getTempItem_1 = id => { id: id };

    // 不报错 可以取得指定返回值
    let getTempItem_2 = id => ({ id: id, name: "Temp" });

    log(getTempItem_1(123));// undefined
    log(getTempItem_2(12));// {id: 12, name: "Temp"}
})()
```

上面代码`getTempItem_1`中，原始意图是返回一个对象`{ id: id }`，但是由于引擎认为大括号是代码块，所以执行了一行语句`id: id`。

这时，`id`可以被解释为语句的标签，因此实际执行的语句是`id`;，然后函数就结束了，没有返回值。

---

### 省略大括号

如果`箭头函数`只有一行语句，且不需要返回值，可以采用下面的写法，就不用写`大括号`了。

```javascript
(() => {
    const { log } = console;
    let fn = () => void doesNotReturn();
})()
```

---

## 使用案例说明

### 结合变量解构

`箭头函数`可以与`变量解构`结合使用。

```javascript
(() => {
    const full = ({ first, last }) => first + ' ' + last;

    // 等同于
    function full(person) {
        return person.first + ' ' + person.last;
    }
})()
```

---

### 简洁基础函数

`箭头函数`使得表达更加简洁。

```javascript
(() => {
  const isEven = n => n % 2 === 0;
  const square = n => n * n;
})()
```

上面代码只用了两行，就定义了两个简单的`工具函数`。如果不用`箭头函数`，可能就要占用多行，而且还不如现在这样写醒目。

---

### 简洁回调函数

> `箭头函数`的一个用处是简化回调函数。

```javascript
(() => {
    const { log } = console;
  // 正常函数写法
  [1,2,3].map(function (x) {
    return x * x;
  });

  // 箭头函数写法
  [1,2,3].map(x => x * x);
})()
```

> 另一个例子是

```javascript
(() => {
  // 正常函数写法
  const result = values.sort(function (a, b) {
    return a - b;
  });

  // 箭头函数写法
  const result = values.sort((a, b) => a - b);
})()
```

---

### 结合`rest`参数

下面是 `rest` 参数与箭头函数结合的例子。

```javascript
(() => {
  const { log } = console;
  const numbers = (...nums) => nums;

  log(numbers(1, 2, 3, 4, 5));
  // [1,2,3,4,5]

  const headAndTail = (head, ...tail) => [head, tail];

  log(headAndTail(1, 2, 3, 4, 5));
  // [1,[2,3,4,5]]
})()
```

---

### λ演算

`Using arrows to pierce the dark heart of computer science`

`箭头函数`有许多实际用途。还有一个可能用例：`ES6 arrow`作为一种学习工具，可以发现深入了解`计算本质`的东西。

`1936`年，`Alonzo Church和Alan Turing`独立开发了强大的`计算数学模型`。

图灵称他的模型为`a-machines`，但每个人都立即开始称他们为图灵机。教会写的是关于功能的。他的模型被称为[λ演算][λ-calculus]。

（`λ`是小写的希腊字母`lambda`。）这项工作是`Lisp`使用这个词`LAMBDA`来表示函数的原因，这就是我们今天将函数表达式称为`lambdas`的原因。

但什么是[λ演算][λ-calculus]？什么是`计算模型`应该是什么意思？

用几句话来解释很难，[λ演算][λ-calculus]是最早的编程语言之一。

它不是设计是一门`编程语言`，毕竟，存储程序计算机不会为十年或二十年，而是足够的`简单`，`精简`，语言的纯粹的数学思想，可以表达任何形式的一起走你希望做的计算。

教会希望这个`模型`能够证明一般的`计算方法`。

他发现他的系统只需要一件事：`functions`。

想想这个说法有多么特别。如果没有`对象`，没有`数组`，没有`数字`，没有`if语句`，`while循环`，`分号`，`赋值`，`逻辑运算符`或`事件循环`，就可以重建`JavaScript`只能使用`函数`从头开始执行的各种计算。

这是一个数学家可以使用`Church`的`λ`表示法编写的`程序`的例子：

`fix = λf.(λx.f(λv.x(x)(v)))(λx.f(λv.x(x)(v)))`

等效的`JavaScript`函数如下所示：

```javascript
var fix = f => (x => f(v => x(x)(v)))
               (x => f(v => x(x)(v)));
```

也就是说，`JavaScript`包含实际运行的[λ演算][λ-calculus]的实现。

[λ演算][λ-calculus]是用`JavaScript`编写的。

关于`Alonzo Church`和后来的研究人员对[λ演算][λ-calculus]做了什么的故事，以及它如何悄悄地将自己暗示到几乎所有主要的编程语言，都超出了这篇文章的范围。

但是，如果你对`计算机科学`的基础感兴趣，或者你只是想看看除了`函数`之外什么都没有的语言可以做`循环`和`递归`之类的事情，那么你可能再看一下 [Church numerals][Church_encoding] 和 [fixed-point combinators][combinators]。

借助`ES6箭头`的优势，`JavaScript`可以合理地声称是探索[λ演算][λ-calculus]的`最佳语言`。

---

[arrow function]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions
[Church_encoding]: https://en.wikipedia.org/wiki/Church_encoding
[combinators]: https://en.wikipedia.org/wiki/Fixed-point_combinator#Strict_fixed_point_combinator
[λ-calculus]: https://en.wikipedia.org/wiki/Lambda_calculus
[how-to-use-arrow]: https://github.com/getify/You-Dont-Know-JS/blob/master/es6%20%26%20beyond/fig1.png