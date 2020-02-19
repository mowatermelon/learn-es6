# 基础类型

为了让程序有价值，我们需要能够处理最简单的数据单元：`数字`，`字符串`，`结构体`，`布尔值`等。

`TypeScript`支持与`JavaScript`几乎相同的[数据类型][Primitive]，此外还提供了实用的`枚举`类型方便我们使用。

包含`Boolean`,`Number`,`String`,`Symbol`,`Array`,`Tuple`,`Enum`,`Any`,`Void`,`Null` , `Undefined`,`Never`和`Object`，十三种类型。

---

## 布尔值(Boolean)

最基本的数据类型就是简单的`true`/`false`值，在`JavaScript`和`TypeScript`里叫做`boolean`（其它语言中也一样）。

```typescript
const isDone: boolean = false;
```

---

## 数字(Number)

和`JavaScript`一样，`TypeScript`里的所有数字都是`浮点数`。 这些`浮点数`的类型是 `number`。 除了支持`十进制`和`十六进制`字面量，`TypeScript`还支持`ECMAScript 2015`中引入的`二进制`和`八进制`字面量。

```typescript
const decimal: number = 6;
const hex: number = 0xf00d;
const binary: number = 0b1010;
const octal: number = 0o744;
```

---

## 字符串(String)

`JavaScript`程序的另一项基本操作是处理网页或服务器端的文本数据。

像其它语言里一样，我们使用 `string`表示文本数据类型。 和`JavaScript`一样，可以使用双引号（ `"`）或单引号（`'`）表示字符串。

```typescript
let color: string = "blue";
color = 'red';
```

你还可以使用模版字符串，它可以定义多行文本和内嵌表达式。

这种字符串是被反引号包围（ \`），并且以`${ expr }`这种形式嵌入表达式

```typescript
const { log } = console;
const fullName: string = `Bob Bobbington`;
const age: number = 37;
const sentence1: string = `Hello, my name is ${ fullName }.

I'll be ${ age + 1 } years old next month.`;

// 和上文效果类似
const sentence2: string = "Hello, my name is " + fullName + ".\n\n" +
    "I'll be " + (age + 1) + " years old next month.";

log(sentence1); // Hello, my name is Bob Bobbington. I'll be 38 years old next month.
log(sentence2); // Hello, my name is Bob Bobbington. I'll be 38 years old next month.
log(sentence1 === sentence2); // true
```

---

## 标记(Symbol)

自`ECMAScript 2015`起，`symbol`成为了一种新的[原生类型][Primitive]，就像`number`和`string`一样。

详细说明请查看[MDN Symbol][Symbol]

### 基础说明

> `symbol`类型的值是通过`Symbol`构造函数创建的。

```typescript
// @param {Any} description 可选的，字符串类型。对symbol的描述，可用于调试但不是访问symbol本身
// Symbol([description])

const { log } = console;
const sym1 = Symbol();

const sym2 = Symbol("key"); // 可选的字符串key

log(sym1);// Uncaught (in promise) TypeError: Cannot convert a Symbol value to a string
log(sym2);// Uncaught (in promise) TypeError: Cannot convert a Symbol value to a string
```

`Symbol()`函数会返回`symbol`类型的值，该类型具有`静态属性`和`静态方法`。

它的`静态属性`会暴露几个`内建`的成员对象；它的`静态方法`会暴露全局的`symbol`注册，且类似于`内建对象类`。

上面使用`Symbol()` 函数的语法，不会在你的整个代码库中创建一个可用的全局`symbol`类型。 要创建跨文件可用的symbol，甚至跨域（每个都有它自己的全局作用域） , 使用 `Symbol.for()` 方法和  `Symbol.keyFor()` 方法从全局的`symbol`注册表设置和取得`symbol`。

---

> 不可用 new 关键词

但作为`构造函数`来说它并不完整，因为它不支持语法："`new Symbol()`"。

```typescript
const sym = new Symbol();// 'new' expression, whose target lacks a construct signature, implicitly has an 'any' type.
```

这会阻止创建一个显式的 `Symbol` 包装器对象而不是一个 `Symbol` 值。围绕原始数据类型创建一个`显式包装器`对象从 `ECMAScript 6` 开始不再被支持。 然而，现有的原始包装器对象，如 `new Boolean`、`new String`以及`new Number`，因为遗留原因仍可被创建。

如果你真的想创建一个 `Symbol` 包装器对象 (`Symbol wrapper object`)，你可以使用 `Object()` 函数

```typescript
const { log } = console;
const sym = Symbol("foo");
log(typeof sym);     // "symbol"
const symObj = Object(sym);
log(typeof symObj);  // "object"

log(sym == symObj);// true,宽松匹配模式下两者相等
log(sym === symObj);// false，严格匹配模式下两者不相等
```

---

> `Symbols`是不可改变且唯一的。

```typescript
const { log } = console;
const sym2 = Symbol("key");
const sym3 = Symbol("key");

log(typeof sym2 === typeof sym3);// true

log(sym2 === sym3);// false, symbols是唯一的,每次都会创建一个新的 symbol类型
```

---

> 像字符串一样，`symbols`也可以被用做对象属性的键。

```typescript
const { log } = console;
const sym = Symbol();

const testObj = {aaa: 1};

const obj = {
    [sym]: "value",
    [`${String(sym)}1`]:2,
    [Symbol()]: 3,
    aaa: 1
};

log(obj[sym]); // "value"
log(Object.getOwnPropertySymbols(obj));// [Symbol(), Symbol()]
log(Object.getOwnPropertySymbols(testObj));// []

```

注意事项

- `String(sym)` conversion 的作用会像`symbol`类型调用 `Symbol.prototype.toString()` 一样，但是注意 `new String(sym)` 将抛出异常`TypeError: Cannot convert a Symbol value to a string`。
- `sym + 1`，将抛出异常`TypeError: Cannot convert a Symbol value to a string`，这会阻止你从一个 `symbol` 值隐式地创建一个新的 `string` 类型的属性名。

---

> `Symbols`也可以与计算出的`属性名`声明相结合，来声明对象的属性和类成员。

```typescript
const { log } = console;
const getClassNameSymbol = Symbol();

class C {
    [getClassNameSymbol](){
       return "C";
    }
}

const c = new C();
const className: string = c[getClassNameSymbol]();
log(className);// "C"
```

---

> `Symbols` 与 `JSON.stringify()`

当使用 `JSON.stringify()` 时，以 `symbol` 值作为键的属性会被完全忽略：

```typescript
const { log } = console;
log(JSON.stringify({[Symbol("foo")]: "foo"}));
// '{}'
```

---

> `Symbols` 与 `for...in` 迭代

`Symbols` 在 `for...in` 迭代中不可枚举。另外，`Object.getOwnPropertyNames()` 不会返回 `symbol` 对象的属性

```typescript
const { log } = console;
const obj= {
    [Symbol("a")]:"a",
    [Symbol.for("b")]:"b",
    ["c"]:"c",
    d:"d"
};

for (const i in obj) {
   log(i); // logs "c" and "d"
}
log(Object.getOwnPropertyNames(obj));// [ "c", "d" ]
log(Object.keys(obj)); // [ "c", "d" ]
```

---

### 静态属性说明

#### 迭代 symbols 静态属性

> Symbol.iterator

一个返回一个对象默认迭代器的方法。被 `for...of` 使用。

`Symbol.iterator` 属性的属性特性：

| 属性特性     | 属性特性值 |
| :----------- | :--------- |
| writable     | false      |
| enumerable   | false      |
| configurable | false      |

```typescript
const { log } = console;
const myIterable = {
    [Symbol.iterator] : async function*() {
        yield "hello";
        yield "async";
        yield "iteration!";
    }
};
log([...myIterable]);// [1, 2, 3]

```

如果一个迭代器 `@@iterator` 没有返回一个迭代器对象，那么它就是一个不符合标准的迭代器，这样的迭代器将会在运行期抛出异常，甚至非常诡异的 Bug。

```typescript
const { log } = console;
const nonWellFormedIterable = {
    [Symbol.iterator] : () => 1
};
log([...nonWellFormedIterable] );// TypeError: Result of the Symbol.iterator method is not an object

```

---

> Symbol.asyncIterator

`Symbol.asyncIterator` 属性的属性特性：

| 属性特性     | 属性特性值 |
| :----------- | :--------- |
| writable     | false      |
| enumerable   | false      |
| configurable | false      |

一个返回对象默认的`异步迭代器`的方法，被 `for await of` 使用。

目前没有默认设定了[Symbol.asyncIterator]属性的`JavaScript`内建的对象。不过，`WHATWG`（网页超文本应用技术工作小组）Streams会被设定为第一批异步可迭代对象，[Symbol.asyncIterator] 最近已在设计规范中落地。

目前`TS`中未支持`Symbol.asyncIterator`。

```typescript
const { log } = console;
const myAsyncIterable = {
  [Symbol.asyncIterator] : async function*() {
      yield "hello";
      yield "async";
      yield "iteration!";
  }
};

(async () => {
  for await (const x of myAsyncIterable) {
      log(x);
      //    "hello"
      //    "async"
      //    "iteration!"
  }
})();

```

---

#### 正则表达式 symbols 静态属性

> Symbol.match

`Symbol.match` 属性的属性特性：

| 属性特性     | 属性特性值 |
| :----------- | :--------- |
| writable     | false      |
| enumerable   | false      |
| configurable | false      |

一个用于对字符串进行匹配的方法，也用于确定一个对象是否可以作为`正则表达式`使用。被 `String.prototype.match()` 使用。

比如， `String.prototype.startsWith()`，`String.prototype.endsWith()` 和 `String.prototype.includes()` 这些方法会检查其第一个参数是否是正则表达式，是正则表达式就抛出一个`TypeError`。现在，如果 `match symbol` 设置为 `false`（或者一个 `假值`），就表示该对象不打算用作`正则表达式`对象。

```typescript
const { log } = console;
const testStr = 'mowatermelon';
const testObj = { a : 1 };
const reg = /melon/;
const matchFn = reg[Symbol.match];

log(matchFn);// function [Symbol.match]() { [native code] }
// Matches a string with this regular expression, and returns an array containing the results of that search.
// const matchFn: (string: string) => RegExpMatchArray | null
log(typeof matchFn);// function

// log(testStr.startsWith(re)); // TypeError: First argument to String.prototype.startsWith must not be a regular expression

reg[Symbol.match] = false;
log(testStr.startsWith(reg)); // false
log(testStr.includes(reg)); // false
log("/melon/".endsWith(reg));   // true
```

---

> Symbol.replace

`Symbol.replace` 属性的属性特性：

| 属性特性     | 属性特性值 |
| :----------- | :--------- |
| writable     | false      |
| enumerable   | false      |
| configurable | false      |

一个替换`匹配字符串`的子串的方法. 被 `String.prototype.replace()` 使用。

```typescript
const { log } = console;
const reg = /melon/;

const replaceFn = reg[Symbol.replace];

log(replaceFn);
// const replaceFn: {
//     (string: string, replaceValue: string): string;
//     (string: string, replacer: (substring: string, ...args: any[]) => string): string;
// }
// Replaces text in a string, using this regular expression.
log(typeof replaceFn);// function

const testStr = 'mo';
const testStr2 = 'watermelon';
class Replace1 {
  constructor(value) {
    this.value = value;
  }
  [Symbol.replace](str) {
    return `${this.value}, ${str}!`;
  }
}


log(testStr.replace(new Replace1('hello'), 111));// hello, mo!
log(testStr2.replace(new Replace1('hello'), 111));// hello, watermelon!
```

---

> Symbol.search

`Symbol.search` 属性的属性特性：

| 属性特性     | 属性特性值 |
| :----------- | :--------- |
| writable     | false      |
| enumerable   | false      |
| configurable | false      |

`Symbol.search` 指定了一个搜索方法，这个方法接受用户输入的正则表达式，返回该正则表达式在字符串中匹配到的下标，这个方法由以下的方法来调用 `String.prototype.search()`。

```typescript
const { log } = console;
const reg = /melon/g;
const searchFn = reg[Symbol.search];

log(searchFn);
// const searchFn: (string: string) => number
// Finds the position beginning first substring match in a regular expression search using this regular expression.
log(typeof searchFn);// function


const testStr = 'mo';
const testStr2 = 'watermelon';
reg[Symbol.search] = function(str){
    return `this is ${str}'s rule, [${reg}]`;
}
class Search1 {
  constructor(value) {
    this.value = value;
  }
  [Symbol.search](str) {
    return `this is ${str}'s rule, [${this.value}]`;
  }
}

log(testStr.search(reg));// this is mo's rule, [/melon/g]
log(testStr2.search(reg));// this is watermelon's rule, [/melon/g]

log(testStr.search(new Search1(reg)));// this is mo's rule, [/melon/g]
log(testStr2.search(new Search1(reg)));// this is watermelon's rule, [/melon/g]

```

---

> Symbol.split

`Symbol.split` 属性的属性特性：

| 属性特性     | 属性特性值 |
| :----------- | :--------- |
| writable     | false      |
| enumerable   | false      |
| configurable | false      |

一个在匹配`正则表达式`的索引处拆分一个字符串的方法。被 `String.prototype.split()` 使用。

```typescript
const { log } = console;
const reg = /melon/g;
const splitFn = reg[Symbol.split];

log(splitFn);
// const splitFn: (string: string, limit?: number | undefined) => string[]
// Returns an array of substrings that were delimited by strings in the original input that match against this regular expression.
// If the regular expression contains capturing parentheses, then each time this regular expression matches, the results (including any undefined results) of the capturing parentheses are spliced.

log(typeof splitFn);// function

const testStr = 'mo';
const testStr2 = 'watermelon';
reg[Symbol.split] = function(str){
    return `this is ${str}'s rule, [${reg}]`;
}
class Split1 {
  constructor(value) {
    this.value = value;
  }
  [Symbol.split](str) {
    return `this is ${str}'s rule, [${this.value}]`;
  }
}

log(testStr.split(reg));// this is mo's rule, [/melon/g]
log(testStr2.split(reg));// this is watermelon's rule, [/melon/g]

log(testStr.split(new Split1(reg)));// this is mo's rule, [/melon/g]
log(testStr2.split(new Split1(reg)));// this is watermelon's rule, [/melon/g]

```

---

#### 其他 symbols 静态属性

> Symbol.hasInstance

`Symbol.hasInstance` 属性的属性特性：

| 属性特性     | 属性特性值 |
| :----------- | :--------- |
| writable     | false      |
| enumerable   | false      |
| configurable | false      |

一个确定一个`构造器对象`识别的对象是否为它的实例的`方法`。被 `instanceof` 使用。

```typescript
const { log } = console;

log(String[Symbol.hasInstance]);// ƒ [Symbol.hasInstance]() { [native code] }
log(Number[Symbol.hasInstance] === String[Symbol.hasInstance]);// true
log(Number[Symbol.hasInstance] === String[Symbol.hasInstance]);// true
log(Function[Symbol.hasInstance] === String[Symbol.hasInstance]);// true
log(Boolean[Symbol.hasInstance] === String[Symbol.hasInstance]);// true
log(Array[Symbol.hasInstance] === String[Symbol.hasInstance]);// true
log(Object[Symbol.hasInstance] === String[Symbol.hasInstance]);// true

class MyArray {  
  static [Symbol.hasInstance](instance) {
    return Array.isArray(instance);
  }
}
log([] instanceof MyArray); // true
```

---

> Symbol.isConcatSpreadable

`Symbol.isConcatSpreadable` 属性的属性特性：

| 属性特性     | 属性特性值 |
| :----------- | :--------- |
| writable     | false      |
| enumerable   | false      |
| configurable | false      |

`@@isConcatSpreadable` 符号 (`Symbol.isConcatSpreadable`) 可以直接定义为对象属性或继承而来，它是布尔类型。它可以控制数组或类似数组（`array-like`）的对象的行为：

对于数组对象，默认情况下，用于`concat`时，会按数组元素展开然后进行连接（数组元素作为新数组的元素）。

重置`Symbol.isConcatSpreadable`可以改变默认行为。

对于`类似数组`的对象，用于`concat`时，该对象整体作为新数组的元素，重置`Symbol.isConcatSpreadable`可改变默认行为。

```typescript
const { log } = console;

const arr1 = [1,2,3];
const arr2 = [4,5,6];
// A Boolean value that if true indicates that an object should flatten to its array elements by Array.prototype.concat.

log(arr1.concat(arr2));// [1, 2, 3, 4, 5, 6]

arr2[Symbol.isConcatSpreadable] = false;

log(arr1.concat(arr2));// [1, 2, 3, Array(3)]
```

---

> Symbol.unscopables

`Symbol.unscopables` 属性的属性特性：

| 属性特性     | 属性特性值 |
| :----------- | :--------- |
| writable     | false      |
| enumerable   | false      |
| configurable | false      |

可以在任何对象上定义 `@@unscopables symbol (Symbol.unscopables)`，用于排除属性名称并与 `with` 环境绑定在一起作为词法变量公开。

请注意，如果使用 `Strict mode`，语句将不可用，并且可能也不需要 `symbol`。

在 `unscopables` 对象上设置属性为 `true`，将使其 `unscopable` 并且因此该属性也将不会在词法环境变量中出现。 如果设置属性为 `false` ，则将使其可 `scopable` 并且该属性会出现在词法环境变量中。

```typescript
const { log } = console;
const keys = [];

with(Array.prototype) {
  keys.push("something");
}

Object.keys(Array.prototype[Symbol.unscopables]);
// ["copyWithin", "entries", "fill", "find", "findIndex", "includes", "keys", "values"]

// 也可以为你自己的对象设置 `unscopables`
const obj = {
  foo: 1,
  bar: 2
};

obj[Symbol.unscopables] = {
  foo: false,
  bar: true
};

with(obj) {
  log(foo); // 1
  log(bar); // ReferenceError: bar is not defined
}
```

---

> Symbol.species

`Symbol.species` 属性的属性特性：

| 属性特性     | 属性特性值 |
| :----------- | :--------- |
| writable     | false      |
| enumerable   | false      |
| configurable | false      |

`Symbol.species` 是个函数值属性，其被构造函数用以创建派生对象。`species` 访问器属性允许子类覆盖对象的默认构造函数。

```typescript
const { log } = console;
class MyArray extends Array {
  // 覆盖 species 到父级的 Array 构造函数上
  static get [Symbol.species]() { return Array; }
}
const a = new MyArray(1,2,3);
const mapped = a.map(x => x * x);

log(mapped instanceof MyArray); // false
log(mapped instanceof Array);   // true
```

---

> Symbol.toPrimitive

`Symbol.toPrimitive` 属性的属性特性：

| 属性特性     | 属性特性值 |
| :----------- | :--------- |
| writable     | false      |
| enumerable   | false      |
| configurable | false      |

`Symbol.toPrimitive` 是一个内置的 `Symbol` 值，它是作为对象的函数值属性存在的，当一个对象转换为对应的`原始值`时，会调用此函数。

```typescript
const { log } = console;
// 一个没有提供 Symbol.toPrimitive 属性的对象，参与运算时的输出结果
const obj1 = {};
log(+obj1);     // NaN
log(`${obj1}`); // "[object Object]"
log(obj1 + ""); // "[object Object]"

// 接下面声明一个对象，手动赋予了 Symbol.toPrimitive 属性，再来查看输出结果
const obj2 = {
  [Symbol.toPrimitive](hint) {
    if (hint == "number") {
      return 10;
    }
    if (hint == "string") {
      return "hello";
    }
    return true;
  }
};
log(+obj2);     // 10      -- hint 参数值是 "number"
log(`${obj2}`); // "hello" -- hint 参数值是 "string"
log(obj2 + ""); // "true"  -- hint 参数值是 "default"
```

---

> Symbol.toStringTag

`Symbol.toStringTag` 属性的属性特性：

| 属性特性     | 属性特性值 |
| :----------- | :--------- |
| writable     | false      |
| enumerable   | false      |
| configurable | false      |

`Symbol.toStringTag` 是一个内置 `symbol`，它通常作为对象的`属性键`使用，对应的`属性值`应该为`字符串`类型。

这个字符串用来表示该对象的`自定义类型`标签，通常只有内置的 `Object.prototype.toString()` 方法会去读取这个标签并把它包含在自己的`返回值`里。

许多内置的 `JavaScript` 对象类型即便没有 `toStringTag` 属性，也能被 `toString()` 方法识别并返回特定的类型标签。

```typescript
const { log } = console;
log(Object.prototype.toString.call('foo'));     // "[object String]"
log(Object.prototype.toString.call([1, 2]));    // "[object Array]"
log(Object.prototype.toString.call(3));         // "[object Number]"
log(Object.prototype.toString.call(true));      // "[object Boolean]"
log(Object.prototype.toString.call(undefined)); // "[object Undefined]"
log(Object.prototype.toString.call(null));      // "[object Null]"
```

另外一些对象类型则不然，`toString()` 方法能识别它们是因为引擎为它们设置好了 `toStringTag` 标签

```typescript
const { log } = console;
log(Object.prototype.toString.call(new Map()));       // "[object Map]"
log(Object.prototype.toString.call(function* () {})); // "[object GeneratorFunction]"
log(Object.prototype.toString.call(Promise.resolve())); // "[object Promise]"
```

但你自己创建的类不会有这份特殊待遇，`toString()` 找不到 `toStringTag` 属性时只好返回默认的 `Object`标签，加上 `toStringTag` 属性，你的类也会有自定义的类型标签了。

```typescript
const { log } = console;
class ValidatorClass1 {}

log(Object.prototype.toString.call(new ValidatorClass1())); // "[object Object]"

class ValidatorClass2 {
  get [Symbol.toStringTag]() {
    return "Validator";
  }
}

log(Object.prototype.toString.call(new ValidatorClass2())); // "[object Validator]"
```

---

## 泛型(Any)

有时候，我们会想要为那些在编程阶段还不清楚类型的变量指定一个类型。 这些值可能来自于动态的内容，比如来自用户输入或第三方代码库。

这种情况下，我们不希望`类型检查器`对这些值进行检查而是直接让它们通过`编译阶段`的检查。

那么我们可以使用 `any`类型来标记这些变量

```typescript
const notSure: any = 4;
notSure = "maybe a string instead";
notSure = false; // okay, definitely a boolean
```

在对现有代码进行改写的时候，`any`类型是十分有用的，它允许你在编译时可选择地包含或移除类型检查。

你可能认为 `Object`有相似的作用，就像它在其它语言中那样。

但是 `Object`类型的变量只是允许你给它赋任意值，但是却不能够在它上面调用任意的方法，即便它真的有这些方法：

```typescript
const notSure: any = 4;
notSure.ifItExists(); //  TSLint will not throw Error, okay, ifItExists might exist at runtime
notSure.toFixed(); // TSLint will not throw Error, okay, toFixed exists (but the compiler doesn't check)

const prettySure: Object = 4;
prettySure.toFixed(); // TSLint will throw Error: Property 'toFixed' does not exist on type 'Object'.
```

当你只知道一部分数据的类型时，`any`类型也是有用的。 比如，你有一个数组，它包含了不同的类型的数据

```typescript
const list: any[] = [1, true, "free"];

list[1] = 100;
```

---

## 空值(Void)

某种程度上来说，`void`类型像是与`any`类型相反，它表示没有任何类型。

当一个函数没有返回值时，你通常会见到其返回值类型是 `void`：

```typescript
const { log } = console;
function warnUser(): void {
  log("This is my warning message");
}

```

声明一个`void`类型的变量没有什么大用，因为你只能为它赋予`undefined`和`null`：

```typescript
const unusable: void = undefined;
```

> fine写法

```typescript
// fine
function testBreak(): void {
    while (true) {
        break;
    }
}
// fine
function testIf(): void {
    if (true) {
    }
}
// fine
function testDoWhile(x:number): void {
    do {
    }while(x>3)
}
// fine
function testAny(test:any):void{
  return test;// fine
}
const { log } = console;
log(testAny(1));// 1

```

> error写法

```typescript

function testString(test:string):void{
  return test;// Type 'string' is not assignable to type 'void'.
}
function testNumber(test:number):void{
  return test;// Type 'number' is not assignable to type 'void'.
}
function testBoolean(test:boolean):void{
  return test;// Type 'boolean' is not assignable to type 'void'.
}
function testUndefined(test:undefined):void{
  return test;// Type 'undefined' is not assignable to type 'void'.
}
function testNull(test:null):void{
  return test;// Type 'null' is not assignable to type 'void'.
}

```

---

## Null(Null) 和 Undefined(Undefined)

`TypeScript`里，`undefined`和`null`两者各自有自己的类型分别叫做`undefined`和`null`。

和 `void`相似，它们的本身的类型用处不是很大。

```typescript
// Not much else we can assign to these variables!
const u: undefined = undefined;
const n: null = null;
```

默认情况下`null`和`undefined`是所有类型的`子类型`。

就是说你可以把 `null`和`undefined`赋值给`number`类型的变量。

然而，当你指定了`--strictNullChecks`标记，`null`和`undefined`只能赋值给`void`和它们各自。

```typescript
// null
const x1: string = null;// Type 'null' is not assignable to type 'string'.
const x2: number = null;// Type 'null' is not assignable to type 'number'.
const x3: boolean = null;// Type 'null' is not assignable to type 'boolean'.
const x4: undefined = null;// Type 'null' is not assignable to type 'undefined'.
const x5: never = null;// Type 'null' is not assignable to type 'never'.
const x6: any = null;
const x7: null = null;

// undefined
const x1: string = undefined;// Type 'undefined' is not assignable to type 'string'.
const x2: number = undefined;// Type 'undefined' is not assignable to type 'number'.
const x3: boolean = undefined;// Type 'undefined' is not assignable to type 'boolean'.
const x4: null = undefined;// Type 'undefined' is not assignable to type 'null'.
const x5: never = undefined;// Type 'undefined' is not assignable to type 'never'.
const x6: any = undefined;
const x7: undefined = undefined;
```

这能避免很多常见的问题。 也许在某处你想传入一个 `string`或`null`或`undefined`，你可以使用联合类型`string` | `null` | `undefined`。

> 注意：我们鼓励尽可能地使用`--strictNullChecks`，但在本手册里我们假设这个标记是关闭的。

---

## 非存在类型(Never)

`never`类型表示的是那些永不存在的值的类型。

例如， `never`类型是那些总是会抛出异常或根本就不会有返回值的函数表达式或箭头函数表达式的返回值类型；

变量也可能是 `never`类型，当它们被永不为真的类型保护所约束时。

下面是一些返回`never`类型的函数：

```typescript
function testNever(test:never):never{
  return test;
}

// 返回never的函数必须存在无法达到的终点
function error(message: string): never {
    throw new Error(message);
}

// 推断的返回值类型为never
function fail() {
    return error("Something failed");
}

// 返回never的函数必须存在无法达到的终点
function infiniteLoop(): never {
    while (true) {
    }
}
function testDoWhile(): never {
    do {
    }while(true)
}
```

`never`类型是任何类型的子类型，也可以赋值给任何类型；然而，没有类型是`never的`子类型或可以赋值给`never`类型（除了`never`本身之外）。 即使 `any`也不可以赋值给`never`。

```typescript
const x1: string = never;// 'never' only refers to a type, but is being used as a value here.
const x2: number = never;// 'never' only refers to a type, but is being used as a value here.
const x3: boolean = never;// 'never' only refers to a type, but is being used as a value here.
const x4: null = never;// 'never' only refers to a type, but is being used as a value here.
const x5: never = never;// 'never' only refers to a type, but is being used as a value here.
const x6: any = never;// 'never' only refers to a type, but is being used as a value here.
const x7: undefined = never;// 'never' only refers to a type, but is being used as a value here.
```

> 返回`never`的函数必须存在无法达到的终点

```typescript

// A function returning 'never' cannot have a reachable end point.
function testBreak(): never {
    while (true) {
        break;
    }
}
// A function returning 'never' cannot have a reachable end point.
function testIf(): never {
    if (true) {
    }
}
// A function returning 'never' cannot have a reachable end point.
function testDoWhile(x:number): never {
    do {
    }while(x>3)
}

function testString(test:string):never{
  return test;// Type 'string' is not assignable to type 'never'.
}
function testNumber(test:number):never{
  return test;// Type 'number' is not assignable to type 'never'.
}
function testBoolean(test:boolean):never{
  return test;// Type 'boolean' is not assignable to type 'never'.
}
function testUndefined(test:undefined):never{
  return test;// Type 'undefined' is not assignable to type 'never'.
}
function testNull(test:null):never{
  return test;// Type 'null' is not assignable to type 'never'.
}
function testAny(test:any):never{
  return test;// Type 'any' is not assignable to type 'never'.
}


```

---

# 相关网站

> 官方

- [ts官网][ts 官网]
- [ts playground][ts playground]
- [ts-ast-viewer][ts-ast-viewer]

> 第三方

- [ts 文档中文版][ts 文档中文版]
- [gitbook ts文档][gitbook ts文档]
- [github ts使用手册][github ts使用手册]
- [菜鸟驿站 ts文档][github ts使用手册]

[ts 官网]: http://www.typescriptlang.org
[ts playground]: http://www.typescriptlang.org/v2/en/play
[ts 文档中文版]: https://www.tslang.cn
[ts-ast-viewer]: https://ts-ast-viewer.com/
[gitbook ts文档]: https://ts.xcatliu.com/introduction/hello-typescript
[菜鸟驿站 ts文档]: https://www.runoob.com/typescript/ts-tutorial.html
[github ts使用手册]: https://github.com/zhongsp/TypeScript
[Primitive]: https://developer.mozilla.org/zh-CN/docs/Glossary/Primitive
[Symbol]: https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Symbol
[tsconfig]: http://json.schemastore.org/tsconfig
[TS和JS混合开发]: https://mp.weixin.qq.com/s?__biz=MzAxODE2MjM1MA==&mid=2651557811&idx=1&sn=aae17b82347310edd4a57fa47065bd65