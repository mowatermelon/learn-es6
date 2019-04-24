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


### 箭头函数变量作用域

`箭头函数`内定义的`变量`及其`作用域`

```javascript
(() => {
  const { log } = console;
    // 常规写法
    const greeting_1 = () => {let now = new Date(); return ("Good" + ((now.getHours() > 17) ? " evening." : " day."));}
    log(greeting_1());          //"Good evening."
    log(now);    // ReferenceError: now is not defined 标准的let作用域

    // 参数括号内定义的变量是局部变量（默认参数）
    const greeting_2 = (now=new Date()) => "Good" + (now.getHours() > 17 ? " evening." : " day.");
    log(greeting_2());          //"Good evening."
    log(now);    // ReferenceError: now is not defined

    // 对比：函数体内{}不使用var定义的变量是全局变量
    const greeting_3 = () => {now = new Date(); return ("Good" + ((now.getHours() > 17) ? " evening." : " day."));}
    log(greeting_3());           //"Good evening."
    log(now);     // Wed Apr 24 2019 23:02:07 GMT+0800 (中国标准时间)

    // 对比：函数体内{} 用const定义的变量是局部变量
    const greeting_4 = () => {const now = new Date(); return ("Good" + ((now.getHours() > 17) ? " evening." : " day."));}
    log(greeting_4()); //"Good evening."
    log(now);    // ReferenceError: now is not defined
})()
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
const f = ([a, b] = [1, 2], {x: c} = {x: a + b}) => a + b + c;
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

### 与严格模式的关系

鉴于 `this` 是词法层面上的，`严格模式`中与 `this` 相关的规则都将被忽略。

```javascript
(() => {
    const { log } = console;
    function Person() {
      this.age = 0;
      const closure = "123"
      setInterval(function growUp() {
        this.age++;
        log(closure);
      }, 1000);
    }

    const p = new Person();

    function PersonX() {
      'use strict'
      this.age = 0;
      const closure = "123"
      setInterval(()=>{
        this.age++;
        log(closure);
      }, 1000);
    }

    const px = new PersonX();
    log(px);// PersonX {age: 0}
})()
```

`严格模式`的其他规则依然不变。

---

### call & apply

由于 `箭头函数`没有自己的`this`指针，通过 `call()` 或 `apply()` 方法调用一个`函数`时，只能传递参数（不能绑定`this`），他们的第一个参数会被`忽略`。（这种现象对于`bind`方法同样成立）

```javascript
(() => {
    const { log } = console;
    const adder = {
      base : 1,
      add : function(a) {
        const f = v => v + this.base;
        return f(a);
      },

      addThruCall: function(a) {
        const f = v => v + this.base;
        const b = {
          base : 2
        };
        return f.call(b, a);
      }
    };

    log(adder.add(1));         // 输出 2
    log(adder.addThruCall(1)); // 仍然输出 2（而不是3）
})()
```

---

## 主要解决问题

### 视觉优化

无参数`箭头函数`在视觉上容易分析

```javascript
(() => {
    const { log } = console;
    setTimeout( () => {
      log('I happen sooner');
      setTimeout( () => {
        // deeper code
        log('I happen later');
      }, 1);
    }, 1);
})()
```

---

### 更短的函数

```javascript
(() => {
    const { log } = console;
    const elements = [
      'Hydrogen',
      'Helium',
      'Lithium',
      'Beryllium'
    ];

    log(elements.map(function(element) { 
      return element.length; 
    })); // [8, 6, 7, 9]

    // 上面的普通函数可以改写成如下的箭头函数
    log(elements.map((element) => {
      return element.length;
    })); // [8, 6, 7, 9]

    // 当箭头函数只有一个参数时，可以省略参数的圆括号
    log(elements.map(element => {
    return element.length;
    })); // [8, 6, 7, 9]

    // 当箭头函数的函数体只有一个 `return` 语句时，可以省略 `return` 关键字和方法体的花括号
    log(elements.map(element => element.length)); // [8, 6, 7, 9]

    // 在这个例子中，因为我们只需要 `length` 属性，所以可以使用参数解构
    // 需要注意的是字符串 `"length"` 是我们想要获得的属性的名称，而 `lengthFooBArX` 则只是个变量名，
    // 可以替换成任意合法的变量名
    log(elements.map(({ "length": lengthFooBArX }) => lengthFooBArX)); // [8, 6, 7, 9]
})()
```

---

### 不绑定this

在`箭头函数`出现之前，每个新定义的`函数`都有它自己的 `this`值（在构造函数的情况下是一个`新对象`，在`严格模式`的函数调用中为 `undefined`，如果该函数被作为`对象方法`调用则为`基础对象`等）。`This`被证明是令人厌烦的面向对象风格的编程。

```javascript
(() => {
    const { log } = console;
    function Person() {
      // Person() 构造函数定义 `this`作为它自己的实例.
      this.age = 0;

      setInterval(function growUp() {
        // 在非严格模式, growUp()函数定义 `this`作为全局对象, 
        // 与在 Person()构造函数中定义的 `this`并不相同.
        this.age++;
      }, 1000);
    }

    const p = new Person();
    log(p);// Person {age: 0}
})()
```

在`ECMAScript 3/5`中，通过将`this`值分配给`封闭`的`变量`，可以解决`this`问题。

```javascript
(() => {
    const { log } = console;
    function Person() {
      const that = this;
      that.age = 0;

      setInterval(function growUp() {
        //  回调引用的是`that`变量, 其值是预期的对象. 
        that.age++;
      }, 1000);
    }
})()
```

或者，可以创建`绑定函数`，以便将预先分配的`this`值传递到绑定的`目标函数`（上述示例中的`growUp()`函数）。

`箭头函数`不会创建自己的`this`,它只会从自己的作用域链的上一层继承`this`。因此，在下面的代码中，传递给`setInterval`的函数内的`this`与封闭函数中的`this`值相同：

```javascript
(() => {
    const { log } = console;
    function Person(){
      this.age = 0;

      setInterval(() => {
        this.age++; // |this| 正确地指向 p 实例
      }, 1000);
    }

    const p = new Person();
    log(p); // Person {age: 0}
})()
```

---

### 不绑定arguments

箭头函数不绑定`Arguments` 对象。因此，在本示例中，`arguments`只是引用了封闭作用域内的`arguments`：

```javascript
(() => {
    const { log } = console;
    const arguments = [1, 2, 3];
    const arr = () => arguments[0];

    log(arr()); // 1

    function foo(n) {
      const f = () => arguments[0] + n; // 隐式绑定 foo 函数的 arguments 对象. arguments[0] 是 n
      return f();
    }

    log(foo(1)); // 2
})()
```

在大多数情况下，使用`剩余参数`是相较使用`arguments`对象的更好选择。

```javascript
(() => {
    const { log } = console;
    function foo(arg) {
      const f = (...args) => args[0];
      return f(arg);
    }
    log(foo(1)); // undefined

    function foo(arg1,arg2) {
        const f = (...args) => args[1];
        return f(arg1,arg2);
    }
    log(foo(1,2));  //2
})()
```

---

## 注意事项说明

### 方法函数

`箭头函数表达式`对`非方法函数`是最合适的。如果试着把它们作为`方法`时发生了什么。

```javascript
(() => {
    'use strict';
    const { log } = console;
    const obj = {
      i: 10,
      b: () => log(this.i, this),
      c: function() {
        log(this.i,this)
      }
    }
    log(obj.b());
    // undefined Window {postMessage: ƒ, blur: ƒ, focus: ƒ, close: ƒ, frames: Window, …}
    // undefined
    log(obj.c());
    // 10 {i: 10, b: ƒ, c: ƒ}
    // undefined
})()
```

`箭头函数`没有定义`this`绑定。

---

> Object.defineProperty()

涉及`Object.defineProperty()`的示例

```javascript
(() => {
    'use strict';
    const { log } = console;
    const obj = {
      a: 10
    };

    Object.defineProperty(obj, "b", {
      get: () => {
        log(this.a, typeof this.a, this);
        return this.a+10; // NaN
        // 代表全局对象 'Window', 因此 'this.a' 返回 'undefined'
      }
    });

    log(obj.b); // undefined   "undefined"   Window {postMessage: ƒ, blur: ƒ, focus: ƒ, close: ƒ, frames: Window, …}
})()
```

---

### new 操作符

箭头函数不能用作构造器，和 new一起用会抛出错误。

```javascript
(() => {
    const { log } = console;
    const Foo = () => {};
    const foo = new Foo(); // TypeError: Foo is not a constructor
})()
```

---

### prototype 属性

`箭头函数`没有`prototype`属性。

```javascript
(() => {
    const { log } = console;
    const Foo = () => {};
    log(Foo.prototype); // undefined
})()
```

---

### yield

`yield` 关键字通常不能在`箭头函数`中使用（除非是嵌套在允许使用的函数内）。因此，`箭头函数`不能用作`生成器`。

---

### 返回值为对象字面量

记住用`params => {object:literal}`这种简单的语法返回`对象字面量`是行不通的。

由于`大括号`被解释为`代码块`，所以如果`箭头函数`直接返回一个对象，必须在`对象字面量`外面加上括号，否则会报错。

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

上面代码`getTempItem_1`中，原始意图是返回一个对象`{ id: id }`，但是由于`引擎`认为`大括号`是`代码块`，所以执行了一行语句`id: id`。

这时，`id`可以被解释为`语句`的`标签`，因此实际执行的`语句`是`id`;，然后`函数`就结束了，没有返回值。

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

### 换行

`箭头函数`在`参数`和`箭头`之间不能换行。

```javascript
(() => {
  const func = ()
            => 1;
  // SyntaxError: expected expression, got '=>'
})()
```

---

### 解析顺序

虽然`箭头函数`中的`箭头`不是`运算符`，但`箭头函数`具有与`常规函数`不同的特殊[运算符优先级][Operator-Precedence]解析规则。

```javascript
(() => {
    const { log } = console;
    let callback;

    callback = callback || function() {}; // ok

    callback = callback || () => {};
    // SyntaxError: invalid arrow-function arguments

    callback = callback || (() => {});    // ok
})()
```

---

### 函数体

`箭头函数`可以有一个`简写体`或常见的`块体`。

在一个`简写体`中，只需要一个`表达式`，并附加一个`隐式`的`返回值`。在块体中，必须使用明确的`return`语句。

```javascript
(() => {
  var func = x => x * x;
  // 简写函数 省略return

  var func = (x, y) => { return x + y; };
  //常规编写 明确的返回值
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

### Promise

更简明的`Promise`链

```javascript
(() => {
  promise.then(a => {
    // ...
  }).then(b => {
    // ...
  });
})()
```

---

### array相关

方便数组`reduce`,`filter`,和`map`;

```javascript
(() => {
    const { log } = console;
    // Easy array filtering, mapping, ...

    const arr = [5, 6, 13, 0, 1, 18, 23];

    const sum = arr.reduce((a, b) => a + b);  
    log(sum); // 66

    const even = arr.filter(v => v % 2 == 0); 
    log(even); // [6, 0, 18]

    const double = arr.map(v => v * 2);
    log(double); // [10, 12, 26, 0, 2, 36, 46]
})()
```

---

### IIFE

```javascript
(() => {
    const { log } = console;
    // 空的箭头函数返回 undefined
    const empty = () => {};

    log((() => 'foobar')());// foobar
    // (这是一个立即执行函数表达式,可参阅 'IIFE'术语表)
})()
```

---

### 三元运算符

`箭头函数`也可以使用条件（`三元`）`运算符`

```javascript
(() => {
    const { log } = console;
    const simple = a => a > 15 ? 15 : a;
    log(simple(16)); // 15
    log(simple(10)); // 10

    let max = (a, b) => a > b ? a : b;
    log(max(4, 5));// 5
})()
```

---

### 闭包

`箭头函数`也可以使用`闭包`

```javascript
(() => {
  const { log } = console;
  // 标准的闭包函数
  function Add_1(){
        let i = 0;
        return function b(){
                return (++i);
        };
  };

  const v_1 = Add_1();
  log(v_1());    //1
  log(v_1());    //2


  //箭头函数体的闭包（ i=0 是默认参数）
  const Add_2 = (i=0) => {return (() => (++i) )};
  const v_2 = Add_2();
  log(v_2());           //1
  log(v_2());           //2

  //因为仅有一个返回，return 及括号（）也可以省略
  const Add_3 = (i=0)=> ()=> ++i;

  const v_3 = Add_3();
  log(v_3());    //1
  log(v_3());    //2
})()
```

---

### 箭头函数递归

```javascript
(() => {
    const { log } = console;
    const fact = (x) => ( x==0 ?  1 : x*fact(x-1) );
    log(fact(5));       // 120
})()
```

---

[arrow function]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions
[Church_encoding]: https://en.wikipedia.org/wiki/Church_encoding
[combinators]: https://en.wikipedia.org/wiki/Fixed-point_combinator#Strict_fixed_point_combinator
[λ-calculus]: https://en.wikipedia.org/wiki/Lambda_calculus
[how-to-use-arrow]: https://github.com/getify/You-Dont-Know-JS/blob/master/es6%20%26%20beyond/fig1.png
[Operator-Precedence]: https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Operator_Precedence