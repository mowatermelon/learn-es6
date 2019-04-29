# 严格模式

## [基本说明][use strict]

[ECMAScript 5][ES5]的[严格模式][use strict]是采用具有限制性`JavaScript`变体的一种方式，从而使代码显示地 脱离`马虎模式`/`稀松模式`/`懒散模式`（`sloppy`）模式。

[严格模式][use strict]不仅仅是一个子集：它的产生是为了形成与正常代码不同的语义。
不支持[严格模式][use strict]与支持[严格模式][use strict]的浏览器在执行[严格模式][use strict]代码时会采用不同行为。

所以在没有对`运行环境`展开特性测试来验证对于[严格模式][use strict]相关方面支持的情况下，就算采用了[严格模式][use strict]也不一定会取得预期效果。[严格模式][use strict]代码和非[严格模式][use strict]代码可以共存，因此项目脚本可以渐进式地采用[严格模式][use strict]。

[严格模式][use strict]对正常的 `JavaScript`语义做了一些更改。

- [严格模式][use strict]通过抛出错误来消除了一些原有`静默错误`。
- [严格模式][use strict]修复了一些导致 `JavaScript引擎`难以执行优化的缺陷：有时候，相同的代码，[严格模式][use strict]可以比非[严格模式][use strict]下运行得更快。
- [严格模式][use strict]禁用了在`ECMAScript`的未来版本中可能会定义的一些语法。

`主流浏览器`现在实现了`严格模式`。

但是不要盲目的依赖它，因为市场上仍然有大量的`浏览器`版本只部分支持`严格模式`或者根本就不支持（比如`IE10`之前的版本）。

`严格模式`改变了`语义`。依赖这些改变可能会导致没有实现`严格模式`的浏览器中出现`问题`或者`错误`。

谨慎地使用`严格模式`，通过检测相关代码的功能保证`严格模式`不出问题。

最后，记得在`支持`或者`不支持`严格模式的`浏览器`中测试你的`代码`。

如果你只在不支持`严格模式`的浏览器中`测试`，那么在`支持`的浏览器中就很有可能出问题，反之亦然。

---

## [开启严格模式][transition strict mode]

`严格模式`可以应用到整个`脚本`或个别`函数`中。

不要在封闭大括弧 `{}` 内这样做，在这样的上下文中这么做是没有效果的。在 `eval` 、`Function` 、`内联事件`处理属性、  `WindowTimers.setTimeout()` 方法中传入的`脚本字符串`，其行为类似于开启了`严格模式`的一个单独`脚本`，它们会如预期一样工作。

---

### 脚本开启

为整个`脚本文件`开启`严格模式`，需要在所有语句之前放一个特定语句 `'use strict'`; （或 `'use strict';`）

```javascript
// 整个脚本都开启严格模式的语法
'use strict';
const v = "Hi!  I'm a strict mode script!";
```

这种语法存在陷阱，[Amazon][big website]被它[坑倒][big website bug]了：不能`盲目`的合并`冲突代码`。

试想合并一个`严格模式`的`脚本`和一个`非严格模式`的`脚本`：合并后的`脚本代码`看起来是`严格模式`。

反之亦然：`非严格`合并`严格`看起来是`非严格`的。

合并均为`严格模式`的`脚本`或均为`非严格模式`的都没问题，只有在合并`严格模式`与`非严格模式`有可能有问题。

建议按一个个`函数`去开启`严格模式`（至少在学习的过渡期要这样做）.

您也可以将整个`脚本`的内容用一个`函数`包括起来，然后在这个`外部函数`中使用`严格模式`。这样做就可以`消除合并`的问题，但是这就意味着您必须要在`函数`作用域外声明一个`全局变量`。

---

### 函数开启

同样的，要给某个函数开启`严格模式`，得把 `"use strict";`  (或 `'use strict';` )声明`一字不漏`地放在`函数体`所有`语句`之前。

```javascript
(() => {
  const strict = ()=> {
    // 函数级别严格模式语法
    'use strict';
    const nested = () => { return "And so am I!"; }
    return "Hi!  I'm a strict mode function!  " + nested();
  }
  const notStrict = () => { return "I'm not strict."; }
})();
```

---

## 作用说明

`严格模式`同时`改变`了`语法`及运行时`行为`。

`变化`通常分为这几类

- 将问题直接转化为`错误`（如`语法错误`或`运行时错误`）
- 简化了如何为给定`名称`的特定变量计算
- 简化了 `eval` 以及 `arguments`, 将写安全`JavaScript`的`步骤`变得更`简单`，以及改变了预测未来`ECMAScript`行为的方式。

---

### 将过失错误转成异常

在严格模式下, 某些先前被接受的`过失错误`将会被认为是`异常`. `JavaScript`被设计为能使新人开发者更易于上手, 所以有时候会给本来错误操作赋予新的不报错误的语义(`non-error semantics`).

有时候这可以解决当前的问题, 但有时候却会给以后留下更大的问题. `严格模式`则把这些`失误`当成`错误`, 以便可以发现并立即将其改正.

---

### 简化变量的使用

> 禁用`with`

`严格模式`禁用 `with`.  

`with`所引起的问题是块内的任何名称可以`映射`(`map`)到`with`传进来的对象的`属性`, 也可以`映射`到`包围`这个块的`作用域`内的`变量`(甚至是`全局变量`), 这一切都是在运行时决定的: 在代码运行之前是无法得知的.

`严格模式`下, 使用 `with` 会引起`语法错误`, 所以就不会存在 `with` 块内的`变量`在运行是才决定`引用`到哪里的情况了:

```javascript
(() => {
  'use strict';
  const x = 17;
  with (obj) // Uncaught SyntaxError: Strict mode code may not include a with statement
  {
    // 如果没有开启严格模式，with中的这个x会指向with上面的那个x，还是obj.x？
    // 如果不运行代码，我们无法知道，因此，这种代码让引擎无法进行优化，速度也就会变慢。
    x;
  }
})()
```

一种取代 `with`的简单方法是，将`目标对象`赋给一个`短命名变量`，然后访问这个`变量`上的`相应属性`.

---

> 引入新变量

严格模式下的 `eval` 不再为上层范围(`surrounding scope`,注:包围`eval` 代码块的范围)引入`新变量`.

在`正常模式`下,  代码 `eval("var x;")` 会给上层函数(`surrounding function`)或者全局引入一个新的变量 `x` .

这意味着, 一般情况下,  在一个包含 `eval` 调用的`函数`内所有没有引用到`参数`或者`局部变量`的`名称`都必须在运行时才能被`映射`到`特定`的`定义` (因为 `eval` 可能引入的`新变量`会覆盖它的`外层变量`).

在严格模式下 `eval` 仅仅为被运行的代码`创建变量`, 所以 `eval` 不会使得名称`映射`到`外部变量`或者其他`局部变量`

```javascript
(() => {
  const {assert, log} = console;
  const x = 17;
  const evalX = eval("'use strict'; const x = 42; x");
  log(assert(x !== 17,'yes it is 17'));// Assertion failed: yes it is 17
  log(assert(evalX !== 42,'yes it is 42'));// Assertion failed: yes it is 42
})()
```

相应的, 如果函数 `eval` 被在`严格模式`下的`eval(...)`以表达式的形式调用时, 其代码会被当做`严格模式`下的代码执行.

当然也可以在代码中显式开启`严格模式`, 但这样做并不是必须的.

```javascript
(() => {
  const { log } = console;
  const strict1 = (str)=>{
    'use strict';
    return eval(str); // str中的代码在严格模式下运行
  }
  const strict2 = (f, str)=>{
    'use strict';
    return f(str); // 没有直接调用eval(...): 当且仅当str中的代码开启了严格模式时
    // 才会在严格模式下运行
  }
  const nonStrict = (str)=>{
    return eval(str); // 当且仅当str中的代码开启了"use strict"，str中的代码才会在严格模式下运行
  }

  log(strict1("'Strict mode code!'")); // Strict mode code!
  log(strict1("'use strict'; 'Strict mode code!'")); // Strict mode code!
  log(strict2(eval, "'Non-strict code.'")); // Non-strict code.
  log(strict2(eval, "'use strict'; 'Strict mode code!'")); // Strict mode code!
  log(nonStrict("'Non-strict code.'")); // Non-strict code.
  log(nonStrict("'use strict'; 'Strict mode code!'")); // Strict mode code!
})()
```

因此，在 `eval` 执行的严格模式代码下，变量的行为与严格模式下非 `eval` 执行的代码中的变量相同。

---

> 严格模式禁止删除声明变量

`delete name` 在`严格模式`下会引起`语法错误`

```javascript
(() => {
  'use strict';

//   const x;// Uncaught SyntaxError: Missing initializer in const declaration
  let x;
  delete x; // Uncaught SyntaxError: Delete of an unqualified identifier in strict mode.

 // Uncaught SyntaxError: Delete of an unqualified identifier in strict mode.
  eval("let y; delete y;");
})()
```

---

### 严格控制`eval`和`arguments`

`严格模式`让`arguments`和`eval`少了一些奇怪的行为。

两者在通常的代码中都包含了很多奇怪的行为： `eval`会添加`删除绑定`，改变绑定好的值，还会通过用它`索引`过的`属性`给`形参`取别名的方式修改`形参`.

虽然在未来的`ECMAScript`版本解决这个问题之前，是不会有`补丁`来完全修复这个问题，但严格模式下将[eval](#eval相关的区别) 和`arguments`作为`关键字`对于此问题的解决是很有帮助的。

> 名称 `eval` 和 `arguments` 不能通过程序语法被绑定(`be bound`)或`赋值`. 以下的所有尝试将引起语法错误:

```javascript
(() => {
  'use strict';
  eval = 17; // Uncaught SyntaxError: Unexpected eval or arguments in strict mode
  arguments++; // Uncaught SyntaxError: Unexpected eval or arguments in strict mode
  ++eval; // Uncaught SyntaxError: Unexpected eval or arguments in strict mode
  const obj = { set p(arguments) { } }; // Uncaught SyntaxError: Unexpected eval or arguments in strict mode
  const eval; // Uncaught SyntaxError: Unexpected eval or arguments in strict mode
  try { } catch (arguments) { } // Uncaught SyntaxError: Unexpected eval or arguments in strict mode
  function x(eval) { } // Uncaught SyntaxError: Unexpected eval or arguments in strict mode
  function arguments() { } // Uncaught SyntaxError: Unexpected eval or arguments in strict mode
  const y = function eval() { }; // Uncaught SyntaxError: Unexpected eval or arguments in strict mode
  const f = new Function("arguments", "'use strict'; return 17;"); // Uncaught SyntaxError: Unexpected eval or arguments in strict mode
})()
```

---

> [arguments对象属性更新](#arguments对象属性更新)

`严格模式`下，参数的值不会随 `arguments` 对象的值的改变而变化。

在`正常模式`下，对于第一个参数是 `arg` 的函数，对 `arg` 赋值时会同时赋值给 `arguments[0]`，反之亦然（除非没有参数，或者 `arguments[0]` 被删除）。严格模式下，函数的 `arguments` 对象会保存函数被调用时的`原始参数`。

`arguments[i]` 的值不会随与之相应的参数的值的`改变`而`变化`，`同名参数`的值也不会随与之相应的 `arguments[i]` 的值的`改变`而`变化`。

```javascript
(() => {
  const {assert} = console;
  function f(a){
    'use strict';
    a = 42;
    return [a, arguments[0]];
  }
  const pair = f(17);
  assert(pair[0] === 42);
  assert(pair[1] === 17);
})()
```

---

> arguments对象和函数属性

在`严格模式`下,访问`arguments.callee`, `arguments.caller`, `anyFunction.caller`以及`anyFunction.arguments`都会抛出`异常`.

---

> arguments.callee

`正常模式`下，`arguments.callee` 指向当前正在执行的函数。这个作用很小，唯一合法的使用应该是给`匿名函数`声明并且重用之。

此外，`arguments.callee` 十分不利于`优化`，例如`内联函数`，因为 `arguments.callee` 会依赖对非`内联函数`的引用。

在`严格模式`下，`arguments.callee` 是一个`不可删除属性`，而且`赋值`和`读取`时都会抛出`异常`。

```javascript
// example taken from vanillajs: http://vanilla-js.com/
const s = document.getElementById('thing').style;
s.opacity = 1;
(()=>{
  if((s.opacity-=.1) < 0){
    s.display="none";
  }
  else{
    setTimeout(arguments.callee, 40);
  }
})();
```

可以重新写成:

```javascript
(() => {
  'use strict';
  const s = document.getElementById('thing').style;
  s.opacity = 1;
  (function fadeOut(){ // name the function
    if((s.opacity-=.1) < 0){
      s.display = "none";
    }else{
      setTimeout(fadeOut, 40); // use the name of the function
    }
  })();
})();
```

---

> arguments.caller

在一些旧时的`ECMAScript`实现中`arguments.caller`曾经是一个对象，里面`存储`的属性指向那个函数的`变量`。

这是一个`安全隐患`，因为它通过函数抽象打破了本来被`隐藏`起来的`保留值`；它同时也是引起大量`优化`工作的原因。

出于这些原因，现在的`浏览器`没有实现它。但是因为它这种历史遗留的功能，`arguments.caller`在严格模式下同样是一个不可被`删除`的`属性`，在`赋值`或者`取值`时会`报错`

```javascript
(() => {
  'use strict';
  function fun(a, b)
  {
    'use strict';
    var v = 12;
    return arguments.caller; // 抛出类型错误
  }
  fun(1, 2); // 不会暴露v（或者a，或者b）
})();
```

---

> anyFunction.caller

在`严格模式`中再也不能通过广泛实现的`ECMAScript`扩展`游走于`JavaScript的栈中。

在普通模式下用这些扩展的话，当一个叫`fun`的函数正在被调用的时候，`fun.caller`是最后一个调用`fun`的函数，而且`fun.arguments`包含调用`fun`时用的形参。

这两个扩展接口对于`安全`JavaScript而言都是有问题的，因为他们允许`安全的`代码访问`专有`函数和他们的（通常是没有经过保护的）形参。

如果`fun`在严格模式下，那么`fun.caller`和`fun.arguments`都是不可删除的`属性`而且在`存值`、`取值`时都会报错

```javascript
(() => {
  function restricted()
  {
    'use strict';
    // Script snippet #6:5 Uncaught TypeError: 'caller', 'callee', and 'arguments' properties may not be accessed on strict mode functions or the arguments objects for calls to them
    restricted.caller;    // 抛出类型错误 
    restricted.arguments; // 抛出类型错误
  }
  function privilegedInvoker()
  {
    return restricted();
  }
  privilegedInvoker();
})();
```

---

### "安全"的JavaScript

`严格模式`下更容易写出`安全`的`JavaScript`。

现在有些网站提供了方式给用户编写能够被网站其他用户执行的`JavaScript`代码。

在浏览器环境下，`JavaScript`能够获取用户的`隐私信息`，因此这类`Javascript`必须在运行前部分被转换成需要申请访问`禁用功能`的`权限`。

没有很多的`执行`时检查的情况，`Javascript`的`灵活性`让它无法有效率地做这件事。

一些语言中的`函数`普遍出现，以至于执行时检查他们会引起严重的`性能损耗`。

做一些在`严格模式`下发生的小改动，要求用户提交的`JavaScript`开启`严格模式`并且用特定的方式调用，就会大大减少在执行时进行检查的`必要`。

在`严格模式`下通过`this`传递给一个`函数`的值不会被`强制`转换为一个`对象`。

对一个普通的`函数`来说，`this`总会是一个对象：不管调用时`this`它本来就是一个`对象`；还是用`布尔值`，`字符串`或者`数字`调用`函数`时`函数`里面被封装成对象的`this`；还是使用`undefined`或者`null`调用函数式`this`代表的全局对象（使用`call`, `apply`或者`bind`方法来指定一个确定的`this`）。

这种`自动转化`为对象的过程不仅是一种性能上的`损耗`，同时在`浏览器`中暴露出`全局对象`也会成为`安全隐患`，因为`全局对象`提供了访问那些所谓安全的`JavaScript`环境必须限制的功能的途径。

所以对于一个开启`严格模式`的函数，指定的`this`不再被封装为`对象`，而且如果[没有指定`this`的话它值是`undefined`](#函数调用中的this)。

```javascript
(() => {
  const { assert, log } = console;
  'use strict';
  function fun() { return this; }
  log(fun());// Window {postMessage: ƒ, blur: ƒ, focus: ƒ, close: ƒ, frames: Window, …}
  assert(fun() === undefined, 'it is not undefined');// Assertion failed: it is not undefined
  log(fun.call(2));// Window {postMessage: ƒ, blur: ƒ, focus: ƒ, close: ƒ, frames: Window, …}
  assert(fun.call(2) === 2, 'it is not 2');// Assertion failed: it is not 2
  log(fun.apply(null));// Window {postMessage: ƒ, blur: ƒ, focus: ƒ, close: ƒ, frames: Window, …}
  assert(fun.apply(null) === null, 'it is not null');// Assertion failed: it is not null
  log(fun.call(undefined));// Window {postMessage: ƒ, blur: ƒ, focus: ƒ, close: ƒ, frames: Window, …}
  assert(fun.call(undefined) === undefined);// Assertion failed: it is not undefined
  log(fun.bind(true));// ƒ fun() { return this; }
  assert(fun.bind(true)() === true, 'it is not true');// Assertion failed: it is not true
})();
```

---

### 为之后规范铺路

为未来的`ECMAScript`版本铺平道路

未来版本的`ECMAScript`很有可能会引入`新语法`，`ECMAScript5`中的`严格模式`就提早设置了一些限制来减轻之后版本改变产生的影响。如果提早使用了`严格模式`中的`保护机制`，那么做出改变就会变得更容易。

> 新增保留关键字

在`严格模式`中一部分字符变成了`保留`的`关键字`。

这些字符包括`implements`, `interface`, `let`, `package`, `private`, `protected`, `public`, `static`和`yield`。

在`严格模式`下，你不能再用这些名字作为`变量名`或者`形参名`。

```javascript
(() => {
  function package(protected){ // !!!
    'use strict';
    var implements; // !!!

    interface: // !!!
    while (true)
    {
      break interface; // !!!
    }

    function private() { } // !!!
  }
  function fun(static) { 'use strict'; } // !!!
})();
```

两个针对`Mozilla`开发的警告：第一，如果你的JavaScript版本在1.7及以上（你的`chrome`代码或者你正确使用了`<script type="">`）并且开启了`严格模式`的话，因为`let`和`yield`是最先引入的关键字，所以它们会起作用。

但是网络上用`<script src="">`或者`<script>...</script>`加载的代码，`let`或者`yield`都不会作为关键字起作用；

第二，尽管`ES5`无条件的保留了`class`, `enum`, `export`, `extends`, `import`和`super`关键字，在`Firefox 5`之前，`Mozilla`仅仅在`严格模式`中保留了它们。

---

> 禁止内部函数声明

`严格模式`禁止了不在`脚本`或者`函数`层面上的`函数声明`。

在`浏览器`的`普通代码`中，在`所有地方`的函数声明都是合法的。

这并不在`ES5`规范中（甚至是`ES3`）！这是一种针对不同`浏览器`中不同语义的一种`延伸`。

未来的`ECMAScript`版本很有希望制定一个新的，针对不在`脚本`或者`函数`层面进行`函数`声明的语法。

在`严格模式`下禁止这样的`函数`声明对于将来`ECMAScript`版本的推出扫清了障碍：

```javascript
(() => {
  'use strict';
  if (true){
    function f() { } // !!! 语法错误
    f();
  }

  for (var i = 0; i < 5; i++){
    function f2() { } // !!! 语法错误
    f2();
  }

  function baz() { // 合法
    function eit() { } // 同样合法
  }
})();
```

这种禁止放到`严格模式`中并不是很合适，因为这样的`函数声明`方式从`ES5`中延伸出来的。但这是`ECMAScript`委员会推荐的做法，`Mozilla浏览器`实现了这一点，但是在`chrome`中没有实现。

---

## 错误说明

### [语法错误][TypeError]

如果代码中使用`'use strict'`开启了`严格模式`,则下面的情况都会在`脚本`运行之前抛出`SyntaxError`异常:

- 八进制语法:`const n = 023`和`const s = "\047"`。
- `with`语句。
- 使用[delete][Operators delete]删除一个`变量名`(而不是`属性名`):`delete myVariable`。
- 使用`eval`或`arguments`作为`变量名`或`函数名`。
- 使用`未来保留字`(也许会在`ECMAScript 6`中使用):`implements`, `interface`, `let`, `package`, `private`, `protected`, `public`, `static`,和`yield`作为`变量名`或`函数名`。
- 在`语句块`中使用`函数声明`:`if(a<b){ function f(){} }`。
- 其他错误
  - `对象字面量`中使用两个相同的`属性名`:`{a: 1, b: 3, a: 7}`。
  - `函数形参`中使用两个相同的`参数名`:`function f(a, b, b){}`。

这些`错误`是有利的，因为可以揭示`简陋`的`错误`和坏的`实践`，这些`错误`会在代码运行前被`抛出`

---

> 严格模式下无法再意外创建全局变量

在普通的`JavaScript`里面给一个错误命名的`变量名`赋值会使`全局对象`新增一个属性并继续`工作`（尽管将来可能会失败：在现代的`JavaScript`中有可能）。

`严格模式`中意外创建`全局变量`被抛出错误替代：

```javascript
(() => {
  'use strict';
  mistypedVaraible = 17;
  // 这一行代码就会抛出 ReferenceError
  // Uncaught ReferenceError: mistypedVaraible is not defined
})()
```

---

> 重名属性

在`Gecko`版本`34`之前，`严格模式`要求一个对象内的所有`属性名`在对象内必须唯一。
`正常模式`下`重名属性`是允许的, `重名参数`名会掩盖之前的`重名参数`，最后一个`重名`的`属性`决定其`属性值`。

之前的参数仍然可以通过 `arguments[i]` 来访问, 还不是完全无法访问.

因为只有最后一个`属性`起作用，当代码要去改变`属性值`而不是修改最后一个`重名属性`的时候，复制这个对象就产生一连串的`bug`。

然而, 这种隐藏`毫无意义`而且可能是`意料之外`的 (比如它可能本来是打错了), 所以在`严格模式`下`重名参数`被认为是`语法错误`。

`严格模式`中意外创建`全局变量`被抛出错误替代：

```javascript
'use strict';
// 假如有一个全局变量叫做mistypedVariable
mistypedVaraible = 17; // 因为变量名拼写错误
// 这一行代码就会抛出 ReferenceError
// Uncaught ReferenceError: mistypedVaraible is not defined
```

---

### [运行时错误][ReferenceError]

`JavaScript`曾经会在一些`上下文`的某些情况中`静默`的失败，`严格模式`会在这些情况下抛出错误。

如果你的代码包含这样的场景，请务必测试以确保没有代码受到影响。

再说一次，`严格模式`是可以设置在`代码粒度`下的。

---

> 未声明的变量赋值

```javascript
(() => {
  const f = (x) => {
    'use strict';
    const a = 12;
    b = a + x*35; // Uncaught ReferenceError: b is not defined
  }
  f();
})()
```

`改变`一个`全局对象`的值可能会造成`不可预期`的后果。

如果你真的想设置一个`全局对象`的值，把他作为一个`参数`并且`明确`的把它作为一个`属性`：

```javascript
(() => {
  const global = this;
  // in the top-level context,
  // "this" always refers the global object
  const f = (x) => {
    'use strict';
    const a = 12;
    global.b = a + x * 35;
  }
  f();
})()
```

---

> 尝试删除一个不可配置的属性

```javascript
(() => {
  'use strict';
  delete Object.prototype; // Uncaught TypeError: Cannot delete property 'prototype' of function Object() { [native code] }
})()
```

在`非严格模式`中,这样的代码只会`静默失败`,这样可能会导致`用户`误以为`删除`操作成功了.

---

> 尝试修改一个不可修改的变量

```javascript
(() => {
  'use strict';
  NaN = 111; // 抛出TypeError错误
  // Uncaught TypeError: Cannot assign to read only property 'NaN' of object '#<Window>'
})()
```

在`非严格模式`中,这样的代码只会`静默失败`,这样可能会导致`用户`误以为`修改`操作成功了.

---

> 尝试修改一个不可配置的属性

任何在`正常模式`下引起`静默失败`的赋值操作 (给`不可写属性`赋值, 给只读属性(`getter-only`)赋值, 给`不可扩展对象`(`non-extensible object`)的新属性赋值) 都会抛出异常:

```javascript
(() => {
  'use strict';
  // 给不可写属性赋值
  const obj1 = {};
  Object.defineProperty(obj1, "x", { value: 42, writable: false });
  obj1.x = 9; // 抛出TypeError错误
  // Uncaught TypeError: Cannot assign to read only property 'x' of object '#<Object>'

  // 给只读属性赋值
  const obj2 = { get x() { return 17; } };
  obj2.x = 5; // 抛出TypeError错误
  // Uncaught TypeError: Cannot set property x of #<Object> which has only a getter

  // 给不可扩展对象的新属性赋值
  const fixed = {};
  Object.preventExtensions(fixed);
  fixed.newProp = "ohai"; // 抛出TypeError错误
  // Uncaught TypeError: Cannot add property newProp, object is not extensible
})()
```

在`非严格模式`中,这样的代码只会`静默失败`,这样可能会导致`用户`误以为`设置`操作成功了.

---

> 严格模式要求函数的参数名唯一

在`正常模式`下, 最后一个`重名参数`名会掩盖之前的`重名参数`.

之前的参数仍然可以通过 `arguments[i]` 来访问, 还不是完全无法访问.

然而, 这种隐藏`毫无意义`而且可能是`意料之外`的 (比如它可能本来是打错了), 所以在`严格模式`下重名参数被认为是`语法错误`:

```javascript
(() => {
  // Uncaught SyntaxError: Duplicate parameter name not allowed in this context
  const sum = (a, a, c) => { // !!! 语法错误
    'use strict';
    return a + a + c; // 代码运行到这里会出错
  }
})()
```

---

> `ECMAScript 6`中的严格模式禁止设置`primitive`值的属性.

不采用严格模式,设置属性将会简单忽略(`no-op`),采用`严格模式`,将抛出`TypeError`错误

```javascript
(() => {
  'use strict';

  false.true = "";              // Uncaught TypeError: Cannot create property 'true' on boolean 'false'
  (14).sailing = "home";        // Uncaught TypeError: Cannot create property 'sailing' on number '14'
  "with".you = "far away";      // Uncaught TypeError: Cannot create property 'you' on string 'with'

})();
```

---

### [ES6改动说明][SyntaxError]

`ES2016` 做了一点修改，规定只要`函数参数`使用了`默认值`、`解构赋值`、或者`扩展运算符`，那么函数内部就不能显式设定为`严格模式`，否则会报错。

```javascript
(() => {
  // 报错
  const doSomething = (a, b = a) => {
    'use strict';
    // code
    // Uncaught SyntaxError: Illegal 'use strict' directive in function with non-simple parameter list
  }

  // 报错
  const doSomething = ({a, b}) => {
    'use strict';
    // code
    // Uncaught SyntaxError: Illegal 'use strict' directive in function with non-simple parameter list
  };

  // 报错
  const doSomething = (...a) => {
    'use strict';
    // code
    // Uncaught SyntaxError: Illegal 'use strict' directive in function with non-simple parameter list
  };

  const obj = {
    // 报错
    doSomething({a, b}) {
      'use strict';
      // code
      // Uncaught SyntaxError: Illegal 'use strict' directive in function with non-simple parameter list
    }
  };
})()
```

这样规定的原因是，函数内部的`严格模式`，同时适用于`函数体`和`函数参数`。

但是，`函数`执行的时候，先执行`函数参数`，然后再执行`函数体`。

这样就有一个不合理的地方，只有从`函数体`之中，才能知道参数是否应该以`严格模式`执行，但是参数却应该先于`函数体`执行。

---

## 语义差异

这些差异都是一些`微小`的`差异`。

有可能`单元测试`没办法捕获这种微小的`差异`。你很有必要去小心地审查你的代码，来确保这些`差异`不会影响你代码的语义。

幸运的是，这种小心地`代码审查`可以很大程度上避免。

---

### 函数调用中的this

在普通的函数调用`f()`中,`this`的值会指向`全局对象`.在`严格模式`中,`this`的值会指向`undefined`.

当函数通过`call`和`apply`调用时,如果传入的`thisValue`参数是一个`null`和`undefined`除外的`原始值`(`字符串`,`数字`,`布尔值`),则`this`的值会成为那个`原始值`对应的`包装对象`,如果`thisValue`参数的值是`undefined`或`null`,则`this`的值会指向`全局对象`.

在`严格模式`中,`this`的值就是`thisValue`参数的值,没有任何类型转换.

---

### arguments对象属性更新

`arguments`对象属性不与对应的`形参变量`同步更新

在`非严格模式`中,修改`arguments`对象中某个`索引属性`的值,和这个属性对应的`形参变量`的值也会同时变化,反之亦然.

这会让`JavaScript`的`代码混淆引擎`让代码变得更`难读`和`理解`。

在`严格模式`中`arguments` 对象会以`形参变量`的拷贝的形式被`创建`和`初始化`，因此 `arguments` 对象的`改变`不会影响`形参`。

---

### eval相关的区别

在`严格模式`中,`eval`不会在当前的`作用域`内创建新的`变量`.另外,传入`eval`的`字符串参数`也会按照`严格模式`来解析.

你需要`全面测试`来确保没有代码受到影响。另外，如果你并不是为了解决一个非常实际的`解决方案`中，尽量不要使用`eval`。

---

[transition strict mode]: https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Strict_mode/Transitioning_to_strict_mode
[use strict]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Strict_mode
[ES5]: http://www.ecma-international.org/publications/standards/Ecma-262.htm
[big website]: https://bugzilla.mozilla.org/show_bug.cgi?id=627531
[big website bug]: https://bugzilla.mozilla.org/show_bug.cgi?id=579119
[Operators delete]: https://developer.mozilla.org/zh-CN/docs/JavaScript/Reference/Operators/delete
[TypeError]: https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/TypeError
[ReferenceError]: https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/ReferenceError
[SyntaxError]: https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/SyntaxError