# 尾调用优化

## 尾调用

### 基础说明

`尾调用（Tail Call）`是函数式编程的一个重要概念，本身非常简单，一句话就能说清楚，就是指某个函数的最后一步是调用另一个函数。

```javascript
function f(x){
  return g(x);
}
```

上面代码中，函数`f`的最后一步是调用函数`g`，这就叫`尾调用`。

以下三种情况，都不属于`尾调用`。

```javascript
// 情况一
function f(x){
  let y = g(x);
  return y;
}

// 情况二
function f(x){
  return g(x) + 1;
}

// 情况三
function f(x){
  g(x);
}
```

上面代码中，情况一是调用函数`g`之后，还有赋值操作，所以不属于`尾调用`，即使语义完全一样。

情况二也属于调用后还有操作，即使写在一行内。情况三等同于下面的代码。

```javascript
function f(x){
  g(x);
  return undefined;
}
```

`尾调用`不一定出现在函数尾部，只要是最后一步操作即可。

```javascript
function f(x) {
  if (x > 0) {
    return m(x)
  }
  return n(x);
}
```

上面代码中，函数`m`和`n`都属于`尾调用`，因为它们都是函数`f`的最后一步操作。

---

### 原理说明

`尾调用`之所以与其他调用不同，就在于它的特殊的调用位置。

我们知道，函数调用会在内存形成一个`调用记录`，又称`调用帧`（call frame），保存调用位置和内部变量等信息。

如果在函数`A`的内部调用函数`B`，那么在`A`的调用帧上方，还会形成一个`B`的调用帧。等到`B`运行结束，将结果返回到`A`，`B`的调用帧才会消失。

如果函数`B`内部还调用函数`C`，那就还有一个`C的`调用帧，以此类推。所有的调用帧，就形成一个`调用栈`（call stack）。

`尾调用`由于是函数的最后一步操作，所以不需要保留`外层函数`的`调用帧`，因为`调用位置`、`内部变量`等信息都不会再用到了，只要直接用`内层函数`的`调用帧`，取代`外层函数`的`调用帧`就可以了。

---

### 优化说明

```javascript
function f() {
  let m = 1;
  let n = 2;
  return g(m + n);
}
f();

// 等同于
function f() {
  return g(3);
}
f();

// 等同于
g(3);
```

上面代码中，如果函数`g`不是`尾调用`，函数`f`就需要保存内部变量`m`和`n`的值、`g`的调用位置等信息。但由于调用`g`之后，函数`f`就结束了，所以执行到最后一步，完全可以删除`f(x)`的调用帧，只保留`g(3)`的调用帧。

这就叫做`尾调用优化`（Tail call optimization），即只保留`内层函数`的`调用帧`。如果所有函数都是`尾调用`，那么完全可以做到每次执行时，`调用帧`只有一项，这将大大节省内存。这就是`尾调用优化`的意义。

注意，只有不再用到`外层函数`的`内部变量`，`内层函数`的`调用帧`才会取代`外层函数`的`调用帧`，否则就无法进行`尾调用优化`。

```javascript
function addOne(a){
  var one = 1;
  function inner(b){
    return b + one;
  }
  return inner(a);
}
```

上面的函数不会进行`尾调用优化`，因为内层函数`inner`用到了外层函数`addOne`的内部变量`one`。

---

## 尾递归

### 基础说明

函数调用自身，称为`递归`。如果尾调用自身，就称为`尾递归`。

递归非常耗费内存，因为需要同时保存成千上百个调用帧，很容易发生`栈溢出`错误（stack overflow）。但对于`尾递归`来说，由于只存在一个调用帧，所以永远不会发生`栈溢出`错误。

```javascript

function factorial(n) {
  if (n === 1) return 1;
  return n * factorial(n - 1);
}

factorial(5) // 120
```

上面代码是一个阶乘函数，计算`n`的阶乘，最多需要保存`n`个调用记录，复杂度 `O(n)` 。

---

### 优化案例

> 阶乘优化方案一

`尾递归`写阶乘，只保留一个调用记录，复杂度 `O(1)` 。

```javascript

function factorial(n, total) {
  if (n === 1) return total;
  return factorial(n - 1, n * total);
}

factorial(5, 1) // 120
```

---

> 阶乘优化方案二

`尾递归`的实现，往往需要改写递归函数，确保最后一步只调用自身。做到这一点的方法，就是把所有用到的内部变量改写成函数的参数。

比如上面的例子，阶乘函数 `factorial` 需要用到一个中间变量`total`，那就把这个中间变量改写成函数的参数。这样做的缺点就是不太直观，第一眼很难看出来，为什么计算`5`的阶乘，需要传入两个参数`5`和`1`？

在`尾递归`函数之外，再提供一个正常形式的`函数`。

```javascript

function tailFactorial(n, total) {
  if (n === 1) return total;
  return tailFactorial(n - 1, n * total);
}

function factorial(n) {
  return tailFactorial(n, 1);
}

factorial(5) // 120
```

上面代码通过一个正常形式的阶乘函数`factorial`，调用尾递归函数`tailFactorial`，看起来就正常多了。

---

> 阶乘优化方案三

函数式编程有一个概念，叫做`柯里化（currying）`，意思是将`多参数`的`函数`转换成`单参数`的形式。这里也可以使用`柯里化`，进行相关改造。

```javascript
function currying(fn, n) {
  return function (m) {
    return fn.call(this, m, n);
  };
}

function tailFactorial(n, total) {
  if (n === 1) return total;
  return tailFactorial(n - 1, n * total);
}

const factorial = currying(tailFactorial, 1);

factorial(5) // 120
```

---

> 阶乘优化方案四

这里也可以采用 `ES6` 的函数默认值。

```javascript

function factorial(n, total = 1) {
  if (n === 1) return total;
  return factorial(n - 1, n * total);
}

factorial(5) // 120
```

上面代码中，参数`total`有默认值`1`，所以调用时不用提供这个值。

---

> Fibonacci 数列

非尾递归的 `Fibonacci` 数列实现如下。

```javascript
function Fibonacci (n) {
  if ( n <= 1 ) {return 1};

  return Fibonacci(n - 1) + Fibonacci(n - 2);
}

Fibonacci(10) // 89
Fibonacci(100) // 超时
Fibonacci(500) // 超时
```

尾递归优化过的 `Fibonacci` 数列实现如下。

```javascript
function Fibonacci2 (n , ac1 = 1 , ac2 = 1) {
  if( n <= 1 ) {return ac2};

  return Fibonacci2 (n - 1, ac2, ac1 + ac2);
}

Fibonacci2(100) // 573147844013817200000
Fibonacci2(1000) // 7.0330367711422765e+208
Fibonacci2(10000) // Infinity
```

由此可见，`尾调用优化`对`递归`操作意义重大，所以一些函数式编程语言将其写入了语言规格。

ES6 亦是如此，第一次明确规定，所有 `ECMAScript` 的实现，都必须部署`尾调用优化`。这就是说，ES6 中只要使用`尾递归`，就不会发生`栈溢出`（或者层层递归造成的超时），相对节省内存。

### 优化总结

总结一下，递归本质上是一种循环操作。纯粹的`函数式编程语言`没有循环操作命令，所有的循环都用递归实现，这就是为什么尾递归对这些语言极其重要。对于其他支持`尾调用优化`的语言（比如 `Lua`，`ES6`），只需要知道循环可以用递归代替，而一旦使用递归，就最好使用`尾递归`。

---

### 替换方案

`尾递归优化`只在`严格模式`下生效，那么正常模式下，或者那些不支持该功能的环境中，有没有办法也使用尾递归优化呢？回答是可以的，就是自己实现`尾递归优化`。

它的原理非常简单。`尾递归`之所以需要优化，原因是`调用栈`太多，造成`溢出`，那么只要减少`调用栈`，就不会`溢出`。

怎么做可以减少`调用栈`呢？就是采用`循环`换掉`递归`。

下面是一个正常的`递归函数`。

```javascript

function sum(x, y) {
  if (y > 0) {
    return sum(x + 1, y - 1);
  } else {
    return x;
  }
}

sum(1, 100000)
// Uncaught RangeError: Maximum call stack size exceeded(…)
```

上面代码中，`sum`是一个`递归函数`，参数`x`是需要累加的值，参数`y`控制递归次数。一旦指定`sum`递归 `100000` 次，就会报错，提示超出`调用栈`的最大次数。

`蹦床函数（trampoline）`可以将`递归`执行转为`循环`执行。

```javascript

function trampoline(f) {
  while (f && f instanceof Function) {
    f = f();
  }
  return f;
}
```

上面就是`蹦床函数`的一个实现，它接受一个函数`f`作为参数。只要`f`执行后返回一个函数，就继续执行。

注意，这里是返回一个`函数`，然后执行该`函数`，而不是`函数`里面调用`函数`，这样就避免了`递归执行`，从而就消除了`调用栈`过大的问题。

然后，要做的就是将原来的`递归函数`，改写为每一步返回另一个函数。

```javascript

function sum(x, y) {
  if (y > 0) {
    return sum.bind(null, x + 1, y - 1);
  } else {
    return x;
  }
}
```

上面代码中，`sum`函数的每次执行，都会返回自身的另一个版本。

现在，使用`蹦床函数`执行`sum`，就不会发生`调用栈溢出`。

```javascript

trampoline(sum(1, 100000))
// 100001
```

`蹦床函数`并不是真正的`尾递归优化`，下面的实现才是。

```javascript

function tco(f) {
  var value;
  var active = false;
  var accumulated = [];

  return function accumulator() {
    accumulated.push(arguments);
    if (!active) {
      active = true;
      while (accumulated.length) {
        value = f.apply(this, accumulated.shift());
      }
      active = false;
      return value;
    }
  };
}

var sum = tco(function(x, y) {
  if (y > 0) {
    return sum(x + 1, y - 1)
  }
  else {
    return x
  }
});

sum(1, 100000)
// 100001
```

上面代码中，`tco`函数是`尾递归优化`的实现，它的奥妙就在于状态变量`active`。

默认情况下，这个变量是不激活的。一旦进入`尾递归优化`的过程，这个变量就激活了。

然后，每一轮递归`sum`返回的都是`undefined`，所以就避免了递归执行。

而`accumulated`数组存放每一轮`sum`执行的参数，总是有值的，这就保证了`accumulator`函数内部的`while`循环总是会执行。

这样就很巧妙地将`递归`改成了`循环`，而后一轮的参数会取代前一轮的参数，保证了`调用栈`只有一层。

---
