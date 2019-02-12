# const 命令

## 基本用法

`const`声明一个`只读`的`常量`。

一旦声明，`常量`的值就不能`改变`。

```javascript

const PI = 3.1415;
PI // 3.1415

PI = 3;
// TypeError: Assignment to constant variable.
```

上面代码表明改变常量的值会报错。

`const`声明的变量不得改变值，这意味着，`const`一旦声明变量，就必须立即初始化，不能留到以后赋值。

---

## 实现原理

`const`实际上保证的，并不是`变量`的`值`不得`改动`，而是`变量`指向的那个`内存地址`所保存的`数据`不得`改动`。

对于简单类型的数据（`数值`、`字符串`、`布尔值`），`值`就保存在`变量`指向的那个`内存地址`，因此等同于`常量`。

但对于复合类型的数据（主要是`对象`和`数组`），`变量`指向的`内存地址`，保存的只是一个指向`实际数据`的`指针`，`const`只能保证这个`指针`是`固定`的（即总是指向另一个`固定`的`地址）`，至于它指向的`数据结构`是不是`可变`的，就完全不能控制了。

因此，将一个对象声明为`常量`必须非常小心。

> 声明对象常量

```javascript

const foo = {};

// 为 foo 添加一个属性，可以成功
foo.prop = 123;
foo.prop // 123

// 将 foo 指向另一个对象，就会报错
foo = {}; // TypeError: "foo" is read-only
```

上面代码中，`常量foo`储存的是一个`地址`，这个`地址`指向一个`对象`。

`不可变`的只是这个`地址`，即不能把`foo`指向另一个`地址`，但`对象`本身是`可变`的，所以依然可以为其添加`新属性`。

  ---

> 声明数组常量

```javascript

const a = [];
a.push('Hello'); // 可执行
a.length = 0;    // 可执行
a = ['Dave'];    // 报错
```

上面代码中，常量`a`是一个数组，这个`数组`本身是可写的，但是如果将另一个`数组`赋值给`a`，就会报错。

  ---

> 冻结对象案例

如果真的想将对象`冻结`，应该使用`Object.freeze`方法。

```javascript

const foo = Object.freeze({});

// 常规模式时，下面一行不起作用；
// 严格模式时，该行会报错
foo.prop = 123;
```

上面代码中，常量`foo`指向一个`冻结`的`对象`，所以添加新属性不起作用，`严格模式`时还会报错。

除了将`对象`本身`冻结`，`对象`的`属性`也应该`冻结`。

下面是一个将对象彻底冻结的函数。

```javascript

var constantize = (obj) => {
  Object.freeze(obj);
  Object.keys(obj).forEach( (key, i) => {
    if ( typeof obj[key] === 'object' ) {
      constantize( obj[key] );
    }
  });
};
```

---

## 注意事项

### 不允许声明不赋值 

```javascript

const foo;
// SyntaxError: Missing initializer in const declaration
```

上面代码表示，对于`const`来说，只声明不赋值，就会报错。

  ---

### 不存在变量提升

`const`的作用域与`let`命令相同：只在声明所在的`块级作用域`内有效。

```javascript

if (true) {
  const MAX = 5;
}

MAX // Uncaught ReferenceError: MAX is not defined
```

  ---

### 暂时性死区问题

`const`命令声明的常量也是`不提升`，同样存在`暂时性死区`，只能在`声明`的位置后面使用。

```javascript

if (true) {
  console.log(MAX); // ReferenceError
  const MAX = 5;
}
```

上面代码在常量`MAX`声明之前就调用，结果报错。

  ---

### 不允许重复声明

`const`声明的常量，也与`let`一样`不可重复声明`。

```javascript

var message = "Hello!";
let age = 25;

// 以下两行都会报错
const message = "Goodbye!";
const age = 30;
```
