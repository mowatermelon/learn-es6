
# 枚举(enums)

## 基础介绍

使用`枚举`我们可以定义一些`带名字`的`常量`。 使用`枚举`可以清晰地表达意图或创建一组有区别的用例。

TypeScript支持`数字`的和基于`字符串`的`枚举`。

---

## 基础枚举

### 数字枚举

> 基础赋值

我们定义了一个`数字枚举`，当这边不使用`初始化器`，未对第一个`枚举项`进行赋值，则默认从`0`开始， `Red`使用初始化为 `0`。 其余的`成员值`会从 `0`开始`自动增长`。

换句话说，`Color.Red`的值为 `0`，`Green`为 `1`，`Blue`为 `2`。

```typescript
enum Color {Red, Green, Blue}
let c: Color = Color.Green;// 1
```

> 全部都采用手动赋值

我们定义了一个`数字枚举`，当这边使用`初始化器`，对每个`枚举项`进行赋值， `Red`使用初始化为 `1`。

在枚举项没有被`手动赋值`的前提下，其余的`成员值`会从 `1`开始`自动增长`。

换句话说，`Color.Red`的值为 `1`，`Green`为 `3`，`Blue`为 `4`。

```typescript
const { log } = console;
enum Color {Red = 1, Green = 3, Blue}
let c: Color = Color.Green;
log(c);// 3
```

> 枚举类型提供的一个便利是你可以由枚举的值得到它的名字。

例如，我们知道数值为`2`，但是不确定它映射到`Color`里的哪个名字，我们可以查找相应的名字。

```typescript
const { log } = console;
enum Color {Red = 1, Green, Blue}
let colorName: string = Color[2];

log(colorName);  // 显示'Green'因为上面代码里它的值是2
```

---

> 通过枚举的属性来访问枚举成员，和枚举的名字来访问枚举类型

```typescript
const { log } = console;
enum Melon {
    No = 0,
    Yes = 1,
}

function respond(recipient: string, message: Melon): void {
    log(recipient, message);
}

respond("Watermelon", Melon.No); // Watermelon,  0

respond("Watermelon", Melon.Yes); // Watermelon,  1

respond("Watermelon", 3);// Argument of type '3' is not assignable to parameter of type 'Response'.
```

---

> 不可以使用数组对象相关运算符，进行转译。

虽然可以通过`反向映射`，即`枚举值`，访问到对应的`枚举成员`，但是`枚举对象`，仍然不可以通过`展开运算符`进行展开。

也不可以通过`Array.from()`进行转译为数组对象。

```typescript

const { log } = console;
enum Color {Red = 1, Green, Blue}

const arr = [...Color];// Type 'typeof Color' must have a '[Symbol.iterator]()' method that returns an iterator.
const arr = Array.from(Color);
// No overload matches this call. Overload 1 of 6, '(iterable: Iterable<unknown> | ArrayLike<unknown>): unknown[]', gave the following error. Argument of type 'typeof Color' is not assignable to parameter of type 'Iterable<unknown> | ArrayLike<unknown>'. Property 'length' is missing in type 'typeof Color' but required in type 'ArrayLike<unknown>'. Overload 2 of 6, '(iterable: Iterable<unknown> | ArrayLike<unknown>): unknown[]', gave the following error. Argument of type 'typeof Color' is not assignable to parameter of type 'Iterable<unknown> | ArrayLike<unknown>'. Type 'typeof Color' is not assignable to type 'ArrayLike<unknown>'. Overload 3 of 6, '(arrayLike: ArrayLike<unknown>): unknown[]', gave the following error. Argument of type 'typeof Color' is not assignable to parameter of type 'ArrayLike<unknown>'.

```

---

### 字符串枚举

> 由于字符串枚举没有`自增长`的行为，`字符串枚举`允许你提供一个`运行时`有意义的，并且`可读`的值，独立于`枚举成员`的名字，字符串枚举可以很好的用于`序列化`。

一般要读一个`数字枚举`的运行时的值，这个值通常是很难读的，因为它并不能表达有用的信息。

```typescript
const { log } = console;
enum Color { Red = 'rRed', Green = 'rGreen', Blue = 'rBlue' }

const c: Color = Color.Red;// 1
log(c);// rRed


function respond(recipient: string, message: Color): void {
    log(recipient, message);
}

respond("Watermelon ", Color.Green); // Watermelon ,  rGreen

respond("Watermelon ", Color.Blue); // Watermelon ,  rBlue

respond("Watermelon ", 3); // Watermelon,  Argument of type '3' is not assignable to parameter of type 'Color'.

```

---

> 必须完整赋值

请注意这边如果将枚举值指定为非`number`类型的值，则需要每个枚举值都进行`手动赋值`，否则就会报错`Enum member must have initializer.`。

```typescript
// Enum member must have initializer.
enum Color { Red = 'rRed', Green, Blue }
```

---

> 必须完整赋值为同一种类型

请注意这边如果将枚举值指定为非`number`类型的值，则需要每个枚举值都进行手动赋值，同时，除了`number`类型，手动赋值的类型需要为同一种类型。

```typescript
// fine
enum Color { Red = 0, Green = 'rGreen', Blue = 'rBlue' }

// Computed values are not permitted in an enum with string valued members.
enum Color { Red = true, Green = 'rGreen', Blue = 'rBlue' }
```

---

### 反向映射

> 反向映射

除了创建一个以属性名作为对象成员的对象之外，`数字枚举成员`还具有了 `反向映射`，从`枚举值`到`枚举名字`。

```typescript
const { log } = console;
enum Color { Red = 0, Green = 'rGreen', Blue = 'rBlue' }
log(Color[0]);// Red
log(Color.Red);// 0

log(Color[1]);// undefined
log(Color.Green);// rGreen

log(Color[2]);// undefined
log(Color.Blue);// rBlue
```

生成的代码中，`枚举类型`被编译成一个`对象`，它包含了`正向映射`（ `name` -> `value`）和`反向映射`（ `value` -> `name`）。

引用`枚举成员`总会生成为对`属性`访问，并且永远也不会`内联代码`。

```typescript
"use strict";
const { log } = console;
var Color;
(function (Color) {
    Color[Color["Red"] = 0] = "Red";
    Color["Green"] = "rGreen";
    Color["Blue"] = "rBlue";
})(Color || (Color = {}));
log(Color[0]); // Red
log(Color.Red); // 0

log(Color[1]); // undefined
log(Color.Green); // rGreen

log(Color[2]);// undefined
log(Color.Blue);// rBlue
```

要注意的是不会为`字符串枚举`成员生成`反向映射`。

---

### `const`枚举

大多数情况下，枚举是十分有效的方案。 然而在某些情况下需求很严格。

为了避免在额外生成的代码上的`开销`和额外的非直接的对`枚举成员`的访问，我们可以使用 `const`枚举。

`常量枚举`通过在枚举上使用 `const`修饰符来定义。

> 未使用到的常量枚举，在编译结果中是不存在的

```typescript
const { log } = console;
const enum Enum {
    A = 1,
    B = A * 2
}
```

编译后的代码

```typescript
"use strict";
const { log } = console;
```

---

> 内联结果

`常量枚举`只能使用`常量枚举`表达式，并且不同于常规的枚举，它们在`编译阶段`会被删除。

`常量枚举`成员在使用的地方会被`内联`进来。 之所以可以这么做是因为，`常量枚举`不允许包含`计算成员`。

```typescript
const { log } = console;

const enum Color { Red = 0, Green, Blue = 'rBlue' };

const arr = [Color.Red,Color.Green,Color.Blue];
log(arr);// // Array(3) [0: 0,1: 1,2: "rBlue",length: 3]
```

转译结果

```typescript
"use strict";
const { log } = console;
;
const arr = [0 /* Red */, 1 /* Green */, "rBlue" /* Blue */];
log(arr); // Array(3) [0: 0,1: 1,2: "rBlue",length: 3]

```

---

## 复杂枚举

### 常量枚举

`枚举成员`使用`常量枚举表达式`初始化。

`常量枚举表达式`是`TypeScript表达式`的`子集`，可以在编译时对其进行完全求值。

- 文字枚举表达式（基本上是字符串文字或数字文字）
- 对先前定义的常量枚举成员的引用（可以源自其他枚举）
- 带括号的常量枚举表达式
- 一个`+`，`-`，`~`一元运算符应用于`恒定`枚举表达。
- `+`，`-`，`*`，`/`，`%`，`<<`，`>>`，`>>>`，`&`，`|`，`^`二元算`常数`的枚举表达式作为操作数
- 使用`Number`对象相关静态属性值

在所有其他情况下，`枚举成员`使用表达式进行赋值，都视为`计算枚举成员`。

```typescript
const { log } = console;

enum MelonAccess {
    // constant members
    a1 = 1,
    // a2 = 'melon', 注意如果要使用相关计算运算符，枚举成员中不能有字符串成员
    a3 = a1,
    a4 = +a1,
    a5 = ~a1,
    a6 = -a1,
    a7 = a1 + 1,
    a8 = a1 - 1,
    a9 = a1 * 1,
    a10 = a1 / 1,
    a11 = a1 % 1,
    a12 = a1 << 1,
    a13 = a1 >> 1,
    a14 = a1 >>> 1,
    a15 = a1 & 1,
    a16 = a1 | 1,
    a17 = ^a1,

    a18 = Number.EPSILON,
    a19 = Number.MAX_SAFE_INTEGER,
    a20 = Number.MAX_VALUE,
    a21 = Number.MIN_SAFE_INTEGER,
    a22 = Number.MIN_VALUE,
    a23 = Number.NEGATIVE_INFINITY,
    a24 = Number.POSITIVE_INFINITY
}

log(MelonAccess);
//  { "0": "a14", "1": "a17", "2": "a12", "a1": 1, "a3": 1, "a4": 1, "a5": -2, "-2": "a5", "a6": -1, "-1": "a6", "a7": 2, "a8": 0, "a9": 1, "a10": 1, "a11": 0, "a12": 2, "a13": 0, "a14": 0, "a15": 1, "a16": 1, "a17": 1, "a18": 2.220446049250313e-16, "2.220446049250313e-16": "a18", "a19": 9007199254740991, "9007199254740991": "a19", "a20": 1.7976931348623157e+308, "1.7976931348623157e+308": "a20", "a21": -9007199254740991, "-9007199254740991": "a21", "a22": 5e-324, "5e-324": "a22", "a23": null, "-Infinity": "a23", "a24": null, "Infinity": "a24" }
```

> 注意

将`常量`枚举表达式计算为`NaN`，这边TS内部会将值转换为`null`。

```typescript
const { log } = console;

enum MelonAccess {
    a1 = Number.NaN,
    a2 = Number('melon') // 实际值是NAN,但是，TS这边内部会将值转译为null
}

log(MelonAccess);
// { "a1": null, "a2": null }
```

---

### 文字枚举

文字枚举成员是没有`初始化值`，或具有初始化为的值的`常量`枚举成员

- 任何字符串文字（例如"foo"，"bar，"baz"）
- 任何数字文字（例如1，100）
- 应用于任何数字文本一个一元减号（例如-1，-100）

---

### 计算枚举

> 基础案例

```typescript
enum E {
    G = "123".length
}
```

---

> 在运行时枚举

枚举是在运行时存在的真实对象

```typescript
const { log } = console;

enum E {
    X, Y, Z
}


function f(obj: { X: number }) {
    return obj.X;
}

// Works, since 'E' has a property named 'X' which is a number.
log(f(E));// 0
```

---

> 在编译时枚举

尽管`枚举`是运行时存在的`真实对象`，但`keyof`关键字的工作方式与您对`典型对象`的预期不同。

而是使用`keyof typeof`获取一个将所有`Enum`键表示为字符串的`Type`。

```typescript
const { log } = console;

enum LogLevel {
    ERROR, WARN, INFO, DEBUG
}

/**
 * This is equivalent to:
 * type LogLevelStrings = 'ERROR' | 'WARN' | 'INFO' | 'DEBUG';
 */
type LogLevelStrings = keyof typeof LogLevel;

function printImportant(key: LogLevelStrings, message: string) {
    const num = LogLevel[key];
    if (num <= LogLevel.WARN) {
       log('Log level key is: ', key);// Log level key is: ,  ERROR
       log('Log level value is: ', num);// Log level value is: ,  0
       log('Log level message is: ', message);// Log level message is: ,  This is a message
    }
}
printImportant('ERROR', 'This is a message');
```

---

> 第一个枚举成员未使用初始化器，其他成员必须要进行初始化

不带初始化器的枚举或者被放在第一的位置，或者被放在使用了数字常量，或其它常量初始化了的枚举后面。

换句话说，下面的情况是不被允许的：

```typescript
const getSomeValue = ()=> 11;

enum E {
    A = getSomeValue(),
    B, // error! 'A' is not constant-initialized, so 'B' needs an initializer
}
```

---

### 异构枚举

从技术上讲，枚举可以与`字符串`和`数字`成员混合。

但是，一般推荐一个枚举对象中，都是`同一类型`的。

```typescript
enum Color { Red = 0, Green, Blue = 'rBlue' };
```

---

> 指定枚举值为布尔值

```typescript
// Type 'true' is not assignable to type 'Color'
// Type 'false' is not assignable to type 'Color'
enum Color { Red = 0, Green = true, Blue = false }
```

---

> 指定枚举值为`null`和`undefined`

```typescript
// Type 'null' is not assignable to type 'Color'
// Type 'undefined' is not assignable to type 'Color'
enum Color { Red = 0, Green = null, Blue = undefined }
```

---

> 指定枚举值为`array`和`object`

```typescript
// Type 'never[]' is not assignable to type 'Color'
// Type '{}' is not assignable to type 'Color'
enum Color { Red = 0, Green = [], Blue = {} }
```

---

> 指定枚举值为`symbol`

```typescript
const sys = Symbol();
// Type 'unique symbol' is not assignable to type 'Color'.
// Type '{}' is not assignable to type 'Color'
enum Color { Red = 0, Green = sys, Blue }
```

---

### 联合枚举

当`枚举`中的所有`成员`都具有[文字枚举][文字枚举]值时，就会使用一些特殊的语义。
文字枚举成员。

> 枚举成员也将成为类型

例如，我们可以说某些`成员`只能具有`枚举成员`的值

```typescript
enum ShapeKind {
    Circle,
    Square,
}

interface Circle {
    kind: ShapeKind.Circle;
    radius: number;
}

interface Square {
    kind: ShapeKind.Square;
    sideLength: number;
}

const c: Circle = {
    kind: ShapeKind.Square, // Error! Type 'ShapeKind.Square' is not assignable to type 'ShapeKind.Circle'.
    radius: 100,
}
```

---

> 枚举类型本身有效地，成为`联合`枚举成员。

```typescript
const { log } = console;
enum E {
    Foo,
    Bar,
}

function f(x: E.Foo | E.Bar) {
    if (x !== E.Foo){
        log('111111', x);
    }else{
        log('222222', x);
    }
}

f(0); // 222222,  0
f(1); //  111111,  1
f(-1); //  111111,  -1

f('melon'); //  Argument of type '"melon"' is not assignable to parameter of type 'E'.

```

因为`TypeScript`能知道`枚举`本身中存在的`确切值集`，所以，`TypeScript`也可以捕获`愚蠢`的`条件错误`，例如：

```typescript
enum E {
    Foo,
    Bar,
}

function f(x: E) {// 类似 x 只能为 E.Foo || E.Bar
    if (x !== E.Foo || x !== E.Bar) {
        //             ~~~~~~~~~~~
        // Error! This condition will always return 'true' since the types 'E.Foo' and 'E.Bar' have no overlap.
    }
}
```

在该示例中，我们首先检查是否`x`是没有 `E.Foo`。如果该检查成功，则我们`||`的电路将短路，`if`的`主体`将运行。

但是，如果检查没有成功，则`x`又只可能为`E.Foo`，所以`x !== E.Bar`是没有意义的。

---

### 环境枚举

`环境枚举`用于描述已经存在的`枚举类型`的`形状`。

```typescript
enum Enum {
    A = 1,// 已计算
    B,// 常量
    C = 2// 已计算
}


declare enum Enum {
    A = 1,// 常量
    B,// 已计算
    C = 2// 常量
}
```

`环境枚举`和`非环境枚举`之间的一个重要区别是，在`常规枚举`中，如果没有其`初始值`设定项的成员被视为`常量`，则该`成员`将被视为`常量`。

在`环境枚举`中相反，始终将没有`初始化`程序的环境（和非`const`）`枚举成员`视为`已计算`。

---
