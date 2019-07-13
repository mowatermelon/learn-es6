# 语句进阶说明

`JavaScript` 遵循了一般编程语言的`语句 - 表达式`结构，多数编程语言都是这样设计的。

在 `JavaScript` 标准中，把语句分成了两种：`声明`和`语句`，这里主要分为了`普通型语句`和`声明型语句`。

## 普通型语句

### 语句块

如果简单理解，语句块就是一对大括号。

```javascript
{
    var x, y;
    x = 10;
    y = 20;
}

```

`语句块`的意义和好处在于：可以把`多行语句`视为同一行语句，这样，`if`、`for` 等语句定义起来就比较简单了。

不过，需要注意的是，`语句块`会产生`作用域`。

```javascript
{
    let x = 1;
}
console.log(x); // 报错

```

因为 `let` 声明，仅仅对`语句块`作用域生效，于是在`语句块`外试图访问语句块内的变量 `x` 就会`报错`。

---

### 空语句

`空语句`就是一个独立的`分号`，实际上没什么大用 ，`空语句`的存在仅仅是从`语言设计`完备性的角度考虑，允许插入多个`分号`而不抛出错误。

```javascript
;

```

---

### 控制型语句

#### if语句

`if 语句`的作用是，在满足条件时执行它的`内容语句`，这个语句可以是一个`语句块`，这样就可以实现有条件地执行多个语句了。

`if` 语句还有 `else` 结构，用于不满足条件时执行，一种常见的用法是，利用语句的嵌套能力，把 `if` 和 `else` 连写成多分支条件判断：

```javascript
if(a < 10) {
    //...
} else if(a < 20) {
    //...
} else if(a < 30) {
    //...
} else {
    //...
}

```

---

#### switch语句

switch 语句继承自 `Java`，`Java` 中的 `switch` 语句继承自 `C` 和 `C++`，原本 `switch` 语句是跳转的变形，所以我们如果要用它来实现分支，必须要加上 `break`。

其实 `switch` 原本的设计是类似 `goto` 的思维。我们看一个例子：

```javascript
switch(num) {
case 1:
    print(1);
case 2:
    print 2;
case 3:
    print 3;
}

```

这段代码当 `num` 为 `1` 时输出 `1 2 3`，当 `num` 为 `2` 时输出 `2 3`，当 `num` 为 `3` 时输出 `3`。如果我们要把它变成分支型，则需要在每个 `case` 后加上 `break`。

```javascript
switch(num) {
case 1:
    print 1;
    break;
case 2:
    print 2;
    break;
case 3:
    print 3;
    break;
}

```

在 `C` 时代，`switch` 生成的汇编代码性能是略优于 `if else` 的，但是对 `JavaScript` 来说，则无本质区别。

---

#### for循环语句

> for loop

注意现在一般使用基础遍历循环中设置循环索引，一般使用`let`声明。

```javascript

for(i = 0; i < 100; i++)
    console.log(i);

for(var i = 0; i < 100; i++)
    console.log(i);

for(let i = 0; i < 100; i++)
    console.log(i);

```

---

> for in loop

`for in` 循环枚举`对象`的`属性`，这里体现了属性的 `enumerable` 特征。

```javascript
let o = { a: 10, b: 20}
Object.defineProperty(o, "c", {enumerable:false, value:30})

for(let p in o)
    console.log(p);

```

这段代码中，我们定义了一个对象 `o`，给它添加了不可枚举的属性 `c`，之后我们用 `for in` 循环枚举它的属性，我们会发现，输出时得到的只有 `a` 和 `b`。

如果定义 `c` 这个属性时，`enumerable` 为 `true`，则 `for in` 循环中也能枚举到它。

---

> for of loop

基于 iterator 机制，`for of`可以用于数组遍历。

```javascript
for(let e of [1, 2, 3, 4, 5])
    console.log(e);// 1, 2, 3, 4, 5

```

我们可以给任何一个对象添加 `iterator`，使它可以用于 `for of` 语句：

```javascript
let o = {  
    [Symbol.iterator]:() => ({
        _value: 0,
        next(){
            if(this._value == 10)
                return {
                    done: true
                }
            else return {
                value: this._value++,
                done: false
            };
        }
    })
}
for(let e of o)
    console.log(e); // 0,1,2,3,4,5,6,7,8,9

```

上段代码展示了如何为一个对象添加 `iterator`。

在实际操作中，我们一般不会这样定义 `iterator`，可以使用 `generator function`。

```javascript
function* foo(){
    yield 0;
    yield 1;
    yield 2;
    yield 3;
}
for(let e of foo())
    console.log(e);// 0,1,2,3

```

---

> for await of loop

`JavaScript` 为异步生成器函数还配备了异步的 `for of`。

```javascript
function sleep(duration) {
    return new Promise(function(resolve, reject) {
        setTimeout(resolve,duration);
    })
}
async function* foo(){
    i = 0;
    while(true) {
        await sleep(1000);
        yield i++;
    }

}
for await(let e of foo())
    console.log(e);

```

这段代码定义了一个异步生成器函数，异步生成器函数每隔一秒生成一个数字，这是一个无限的生成器。

接下来，我们使用 `for await of` 来访问这个异步生成器函数的结果，我们可以看到，这形成了一个每隔一秒打印一个数字的无限循环。

但是因为我们这个循环是异步的，并且有时间延迟，所以，这个无限循环的代码可以用于显示时钟等有意义的操作。

但是目前这个功能各大浏览器厂商，还没有做统一支持。

---

#### while循环语句

注意，这里 `do while` 循环无论如何至少会执行一次。

> while loop

```javascript
let a = 100
while(a--) {
    console.log("*");
}

```

---

> do while loop

```javascript
let a = 101;
do {
    console.log(a);
} while(a < 100)


```

---

#### break语句

`break` 语句用于跳出循环语句或者 `switch` 语句，需要注意的是，支持带标签的用法。

带标签的 `break` 可以控制自己被外层的哪个语句结构消费，这可以跳出复杂的语句结构。

```javascript
outer:for(let i = 0; i < 100; i++)
    inner:for(let j = 0; j < 100; j++)
        if( i == 50 && j == 50)
            break outer;

```

---

#### continue语句

`continue` 语句用于结束本次循环并继续循环，需要注意的是，支持带标签的用法。

带标签的 `continue` 可以控制自己被外层的哪个语句结构消费，这可以跳出复杂的语句结构。

```javascript
outer:for(let i = 0; i < 100; i++)
    inner:for(let j = 0; j < 100; j++)
        if( i >= 50 && j == 50)
            continue outer;

```

---

#### return语句

`return` 语句用于函数中，它终止函数的执行，并且指定函数的`返回值`。

```javascript
function squre(x){
    return x * x;
}

```

---

#### throw语句

一般来说，`throw` 用于抛出异常，通常与`try` 语句组合使用，但是单纯从语言的角度，我们可以抛出任何值，也不一定是异常逻辑，但是为了保证语义清晰，不建议用 `throw` 表达任何非异常逻辑。

```javascript
try {
    throw new Error("error");
} catch(e) {
    console.log(e);
} finally {
    console.log("finally");
}
```

---

#### try语句

try 语句用于处理异常，通常与throw 语句组合使用。在大型应用中，异常机制非常重要。

```javascript
try {
    throw new Error("error");
} catch(e) {
    console.log(e);
} finally {
    console.log("finally");
}

```

`try` 语句用于捕获异常，用 `throw` 抛出的异常，可以在 `try` 语句的结构中被处理掉：`try` 部分用于标识捕获异常的代码段，`catch` 部分则用于捕获异常后做一些处理，而 `finally` 则是用于执行后做一些必须执行的清理工作。

`catch` 结构会创建一个局部的作用域，并且把一个变量写入其中，需要注意，在这个作用域，不能再声明变量 `e` 了，否则会出错。

在 `catch` 中重新抛出错误的情况非常常见，在设计比较底层的函数时，常常会这样做，保证抛出的错误能被理解。

`finally` 语句一般用于释放资源，它一定会被执行，我们在前面的课程中已经讨论过一些 `finally` 的特征，即使在 `try` 中出现了 `return`，`finally` 中的语句也一定要被执行。

---

### debugger语句

`debugger` 语句的作用是：通知调试器在此断点。在没有调试器挂载时，它不产生任何效果。

```javascript
if(condition){
    debugger;
    // blabla
}
```

---

### with语句

`with` 语句是个非常巧妙的设计，把对象的属性在它内部的作用域内变成变量。

但它把 `JavaScript` 的变量引用关系变得不可分析，所以一般都认为这种语句都属于糟粕。

但是历史无法改写，现在已经无法去除 `with` 了。

```javascript
let o = {a:1, b:2}
with(o){
    console.log(a, b);
}

```

---

### 带标签语句

实际上，任何 `JavaScript` 语句是可以加标签的，在语句前加冒号即可

```javascript
    firstStatement: var i = 1;

```

大部分时候，这个东西类似于注释，没有任何用处。唯一有作用的时候是：与 [完成记录][Completion Record by ECMA] 类型中的 `target` 相配合，用于跳出多层循环。

```javascript
    outer: while(true) {
      inner: while(true) {
          break outer;
      }
    }
    console.log("finished")

```

`break/continue` 语句如果后跟了关键字，会产生带 target 的完成记录。一旦[完成记录][Completion Record by ECMA]带了 `target`，那么只有拥有对应 `label` 的循环语句会消费它。

---

## 声明型语句

### var声明

`var` 声明语句是古典的 `JavaScript` 中声明变量的方式。而现在，在绝大多数情况下，`let` 和 `const` 都是更好的选择。

```javascript
var x = 1, y = 2;
doSth(x, y);

for(var x = 0; x < 10; x++)
    doSth2(x);

```

如果仍然想要使用 `var`，我的个人建议是，把它当做一种`保障变量是局部`的逻辑，遵循以下三条规则：

- 声明同时必定初始化；
- 尽可能在离使用的位置近处声明；
- 不要在意重复声明。

---

### let声明

这里具体就不细讲了，需要看详细内容，请查看[第二章](./let.html)。

```javascript
let a = 2;

```

---

### const声明

这里具体就不细讲了，需要看详细内容，请查看[第三章](./const.html)。

```javascript
const a = 2;
if(true){
    const a = 1;
    console.log(a);
}
console.log(a);

```

---

### Class声明

`class` 最基本的用法只需要 `class` 关键字、名称和一对大括号。

它的声明特征跟 `const` 和 `let` 类似，都是作用于`块级作用域`，预处理阶段则会屏蔽外部变量。

注意`Class`声明内部，默认开启了严格模式。

`class` 内部，可以使用 `constructor` 关键字来定义构造函数。还能定义 `getter/setter` 和方法。

```javascript
class Rectangle {
  constructor(height, width) {
    this.height = height;
    this.width = width;
  }
  // Getter
  get area() {
    return this.calcArea();
  }
  // Method
  calcArea() {
    return this.height * this.width;
  }
}

```

---

### 函数声明

> 普通函数声明

function 关键词，事件名，参数组，和成对的大括号。

```javascript

function foo(){

}

const foo = () => {

}

```

---

> async函数声明

`async` 函数是可以暂停执行，等待异步操作的函数，它的底层是 `Promise` 机制。

```javascript
async function foo(){
    await sleep(3000);
}

```

---

> generator函数声明

带 `*` 的函数是 `generator`，生成器函数可以理解为返回一个`序列`的`函数`，它的底层是 `iterator` 机制。

```javascript
function* foo(){
    yield 1;
    yield 2;
    yield 3;
}

```

---

> async generator 函数声明

异步生成器函数则是`Promise` 机制和`iterator` 机制的结合。

```javascript
async function* foo(){
    await sleep(3000);
    yield 1;
}
```

---

## 语句的Completion Record

除了七种语言类型(`Undefined` `Null` `Boolean` `String` `Number` `Symbol` `Object`)，还有一些语言的实现者更关心的规范类型。

- List 和 Record： 用于描述函数传参过程。
- Set：主要用于解释字符集等。
- Completion Record：用于描述异常、跳出等语句执行过程。
- Reference：用于描述对象属性访问、delete 等。
- Property Descriptor：用于描述对象的属性。
- Lexical Environment 和 Environment Record：用于描述变量和作用域。
- Data Block：用于描述二进制数据。

---

### 基础说明

> [Completion Record by ECMA]

The Completion type is a Record used to explain the runtime propagation of values and control flow such as the behaviour of statements (break, continue, return and throw) that perform nonlocal transfers of control.

完成类型是一个记录，用于解释值和控制流的运行时传播，例如执行非本地控制传输的语句(`break`、`continue`、`return`和`throw`)的行为。

Values of the Completion type are Record values whose fields are defined as by Table 8. Such values are referred to as Completion Records.

补全类型的值是记录值，其字段定义如下图所示。这些值称为完成记录。

| Field Name | Value                                            | Meaning                                          |
| :--------- | :----------------------------------------------- | :----------------------------------------------- |
| [[Type]]   | One of normal, break, continue, return, or throw | The type of completion that occurred.            |
| [[Value]]  | any ECMAScript language value or empty           | The value that was produced.                     |
| [[Target]] | any ECMAScript string or empty                   | The target label for directed control transfers. |

The term “abrupt completion” refers to any completion with a `[[Type]]` value other than normal.

“突然补全”一词是指除正常补全外，任何具有`[[类型]]`值的补全。

> [Completion Record by Winter]

`Completion Record` 表示一个语句执行完之后的结果，它有三个字段。

| Field Name | Meaning                                                                |
| :--------- | :--------------------------------------------------------------------- |
| [[type]]   | 表示完成的类型，有 break continue return throw 和 normal 几种类型；    |
| [[value]]  | 表示语句的返回值，如果语句没有，则是 empty；                           |
| [[target]] | 表示语句的目标，通常是一个 JavaScript 标签，如果语句没有，则是 empty。 |

`JavaScript` 正是依靠语句的 `Completion Record` 类型，方才可以在语句的复杂嵌套结构中，实现各种控制。比如，Chrome 控制台显示的正是语句的 Completion Record 的 `[[value]]`。

---

### 原理分析

接下来我们要来了解一下 `JavaScript` 使用 `Completion Record` 类型，控制语句执行的过程。

语句块本身并不复杂，我们需要注意的是语句块内部的语句的 `Completion Record` 的 `[[type]]` 如果不为 `normal`，会打断语句块后续的语句执行。

> return [[type]]

`return` 语句可能产生 `return` 或者 `throw` 类型的 `Completion Record`。

比如下面案例，一个内部为`普通语句`的语句块：

```javascript
{
  var i = 1; // normal, empty, empty
  i ++; // normal, 1, empty
  console.log(i) //normal, undefined, empty
} // normal, undefined, empty

```

在每一行的注释中，写出了语句的 `Completion Record`。

我们看到，在一个 `block` 中，如果每一个语句都是 `normal` 类型，那么它会顺次执行。接下来我们加入 `return` 试试看。

```javascript
{
  var i = 1; // normal, empty, empty
  return i; // return, 1, empty
  i ++;
  console.log(i)
} // return, 1, empty

```

但是假如我们在 `block` 中插入了一条 `return` 语句，产生了一个非 `normal` 记录，那么整个 `block` 会成为非 `normal`。

这个结构就保证了`非 normal` 的完成类型可以`穿透`复杂的语句嵌套结构，产生控制效果。

---

> break continue throw  [[type]]

控制型语句带有 `if`、`switch` 关键字，它们会对不同类型的 `Completion Record` 产生反应。

控制类语句分成两部分，

- 对其内部造成影响，如 `if`、`switch`、`while/for`、`try`。
- 另一类是对外部造成影响如 `break`、`continue`、`return`、`throw`。

这两类语句的配合，会产生控制代码执行顺序和执行逻辑的效果，这也是我们编程的主要工作。

一般来说， `for/while` - `break/continue` 和 `try - throw` 这样比较符合逻辑的组合，是大家比较熟悉的，但是，实际上，我们需要控制语句跟 `break` 、`continue` 、`return` 、`throw` 四种类型与控制语句两两组合产生的效果。

| 语句名    | break    | continue | return   | throw |
| :-------- | :------- | :------- | :------- | :---- |
| if        | 穿透     | 穿透     | 穿透     | 穿透  |
| switch    | 消费     | 穿透     | 穿透     | 穿透  |
| for/while | 消费     | 消费     | 穿透     | 穿透  |
| function  | 报错     | 报错     | 消费     | 穿透  |
| try       | 特殊处理 | 特殊处理 | 特殊处理 | 消费  |
| catch     | 特殊处理 | 特殊处理 | 特殊处理 | 穿透  |
| finally   | 特殊处理 | 特殊处理 | 特殊处理 | 穿透  |

因为 `finally` 中的内容必须保证执行，所以 `try/catch` 执行完毕，即使得到的结果是`非 normal` 型的完成记录，也必须要执行 `finally`。

而当 `finally` 执行也得到了`非 normal` 记录，则会使 `finally` 中的记录作为整个 `try` 结构的结果。

因为 `JavaScript` 语句存在着嵌套关系，所以执行过程实际上主要在一个树形结构上进行， 树形结构的每一个节点执行后产生 `Completion Record`，根据语句的结构和 `Completion Record`，`JavaScript` 实现了各种`分支`和跳出`逻辑`。

[Completion Record by ECMA]: http://www.ecma-international.org/ecma-262/#_ref_123
[Completion Record By Winter]: https://time.geekbang.org/column/article/83860
