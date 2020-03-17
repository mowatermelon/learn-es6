
# 衍生类型

## 数组(Array)

### 数组基础介绍

`TypeScript`像`JavaScript`一样可以操作数组元素。

有两种方式可以定义数组。

### 数组代码示例

第一种，可以在元素类型后面接上 `[]`，表示由此类型元素组成的一个数组：

```typescript
const list: number[] = [1, 2, 3];
```

第二种方式是使用数组泛型，`Array<元素类型>`：

```typescript
const list: Array<number> = [1, 2, 3];
```

---

## 元组(Tuple)

### 元组基础介绍

`元组`类型允许表示一个已知元素数量和`类型`的数组，各元素的类型不必相同。

### 元组代码示例

比如，你可以定义一对值分别为 `string`和`number`类型的元组。

```typescript
// Declare a tuple type
const x: [string, number];
// Initialize it
x = ["hello", 10]; // OK
// Initialize it incorrectly
x = [10, "hello"]; // Error
```

当访问一个已知索引的元素，会得到正确的类型：

```typescript
const { log } = console;
log(x[0].substring(1)); // OK
log(x[1].substring(1)); // Error, 'number' does not have 'substring'
```

当访问一个越界的元素，会使用联合类型替代：

```typescript
const { log } = console;
x[3] = "world"; // Error, Property '3' does not exist on type '[string, number]'.

log(x[5].toString()); // Error, Property '5' does not exist on type '[string, number]'.
```

---

## 对象(Object)

### 对象基础介绍

`object`表示非原始类型，也就是除`number`，`string`，`boolean`，`symbol`，`null`或`undefined`之外的类型。

### 对象代码示例

使用`object`类型，就可以更好的表示像`Object.create`这样的API。例如：

```typescript
declare function create(o: object | null): void;

create({ prop: 0 }); // OK
create(null); // OK

create(42); // Error
create("string"); // Error
create(false); // Error
create(undefined); // Error
```

---

## 枚举(enums)

### 枚举基础介绍

使用`枚举`我们可以定义一些`带名字`的`常量`。 使用`枚举`可以清晰地表达意图或创建一组有区别的用例。

TypeScript支持`数字`的和基于`字符串`的`枚举`。

### 枚举详细内容

[详细内容请看](./衍生类型学习-Enum.md)

---

## 联合类型(union types)

### String Literal Types

字符串文字类型(String Literal Types)与联合类型(union types)，类型保护和类型别名很好地结合在一起，将这些功能一起使用，就获得类似于字符串的枚举行为。

```typescript
type Easing = "ease-in" | "ease-out" | "ease-in-out";
class UIElement {
  animate(dx: number, dy: number, easing: Easing) {
    if (easing === "ease-in") {
      // ...
    } else if (easing === "ease-out") {
    } else if (easing === "ease-in-out") {
    } else {
      // error! should not pass null or undefined.
    }
  }
}

const button = new UIElement();
button.animate(0, 0, "ease-in");
button.animate(0, 0, "uneasy"); // Argument of type '"uneasy"' is not assignable to parameter of type 'Easing'.
```

可以传递三个允许的字符串中的任何一个，传递其他字符串会抛出错误。

---

### Numeric Literal Types

```typescript
type rollNumber = 1 | 2 | 3 | 4 | 5 | 6;
function rollDice(): rollNumber {
  // ...
  return '222';// Type '"222"' is not assignable to type 'rollNumber'.
}
```

---

## 接口(interface)

### 接口基础介绍

`TypeScript`的核心原则之一是`类型检查`的重点是值的形状。有时称为`鸭式打字`或`结构子类型化`。在`TypeScript`中，接口充当命名这些类型的角色，并且是定义代码内契约以及项目外代码契约的有效方法。

TypeScript支持接口用于类型检测和类继承。

### 接口详细内容

[详细内容请看](./衍生类型学习-interface.md)

---

## 类(Class)

### 类基础介绍

传统的`JavaScript`使用函数和基于原型的继承来构建可重用的组件，但是对于程序员来说，使用`类`继承功能，并且对象是从这些`类`中构建对象的面向对象的方法可能感到有点尴尬。

从`ECMAScript 2015`（也称为`ECMAScript 6`）开始，`JavaScript`程序员将能够使用这种面向对象的基于`类`的方法来构建其应用程序。

在`TypeScript`中，我们允许开发人员现在使用这些技术，并将其编译为可在所有主要浏览器和平台上使用的`JavaScript`，而不必等待下一个`JavaScript`版本。

### 类详细内容

[详细内容请看](./衍生类型学习-Class.md)

---

## 方法(function)

### 声明函数

#### function 关键词

```typescript
// Named function
function add(x: number, y: number) :number{
  return x + y;
}
```

---

#### 字面量基础赋值

```typescript
// Anonymous function
const add = function(x: number, y: number)  :number{
  return x + y;
};

```

---

#### 箭头函数赋值

```typescript
// arrow function
const add = (x: number, y: number) :number =>{
  return x + y;
};

```

---

#### 函数类型的函数赋值

函数的类型具有相同的两个部分：`参数的类型`和`返回类型`。写出整个函数类型时，两个部分都是必需的。

```typescript
// arrow function
const add: (x: number, y: number) => number = function(
  x: number,
  y: number
): number {
  return x + y;
};

```

> 参数类型

可以像参数列表一样写参数类型，为每个参数指定`名称`和`类型`，也可以将名称写的更语义化，可以提高代码可读性。

```typescript
// arrow function
const add: (baseValue: number, increment: number) => number = (
    x: number,
    y: number
): number => {
    return x + y;
};

```

只要将`参数类型`排列在一起，就会将其视`为函数`的`有效类型`，无论在`函数类型`中为`参数`指定什么`名称`。

> 返回类型

我们通过`=>`在`参数`和`返回类型`之间使用粗箭头（`=>`）来弄清楚哪个是`返回类型`。如前所述，这是`函数类型`的必需部分。

如果函数不写`返回类型`，则会默认`返回类型`为`void`。

> 类型推导

在处理示例时，即使只有在等式的一侧设置`参数类型`和`返回类型`，`TypeScript`编译器也可以自动推断对应的`参数类型`和`返回类型`。

```typescript
// add has the full function type
const add = (x: number, y: number): number =>{
  return x + y;
};

// The parameters 'x' and 'y' have the type number
const add: (baseValue: number, increment: number) => number = function(x, y) {
  return x + y;
};

```

这称为`上下文类型化`，一种`类型推断`，这有助于减少保持程序键入的工作量。

> 注意事项

值得注意的是，只有`参数`和`返回`类型构成`函数类型`。

实际上，捕获的变量名是不会反映在类型中，只是对应函数的`隐藏状态`的一部分，并且不构成其`API`。

---

### 形参修饰符

#### 形参类型

在方法中的参数类型声明中，可以用基础类型(`string`,`number`,`boolean`,和`any`),和衍生类型(`数组`，`元组`，`对象`，`interface`，`Class`和`namespace`)，进行相关声明。

```typescript
const add = function(x: number, y: number){
  return x + y;
};

```

---

#### 可选形参

在`TypeScript`中，会默认函数每个参数都是必传的。

虽然实参值可以是`null`或`undefined`，但是当调用函数时，`编译器`将检查用户是否为每个`参数`提供了一个`值`。

```typescript

function buildName(firstName: string, lastName: string) {
  return firstName + " " + lastName;
}

const result1 = buildName("Bob"); // error, too few parameters
const result2 = buildName("Bob", "Adams", "Sr."); // error, too many parameters
const result3 = buildName("Bob", "Adams"); // ah, just right

```

可以在希望成为可选参数的参数末尾添加`?` 。例如，可以设置上述案例中的`lastName`是可选的。

```typescript

function buildName(firstName: string, lastName?: string) {
  if (lastName) return firstName + " " + lastName;
  else return firstName;
}

const result1 = buildName("Bob"); // works correctly now
const result2 = buildName("Bob", "Adams", "Sr."); // error, too many parameters
const result3 = buildName("Bob", "Adams"); // ah, just right
```

一般在形参定义中，一般是先声明必传参数，最后再写可选形参。

如果上例中`firstName`成为可选的，而不是`lastName`，则需要更改`函数`中`参数`的顺序，将`firstName`放在列表的最后，即`buildName(lastName: string, firstName?: string)`。

---

#### 形参默认值

> 基础配置

在`TypeScript`中，会默认函数形参数量是`固定`的，即预判赋予函数的`参数数量`必须与函数期望的`参数数量`匹配。

但是，如果形参设置了`默认值`或者设置为`可选参数`，则在计算形参默认需要传递参数数量时不考虑在内。

```typescript
function buildName(firstName: string, lastName = "Smith") {
  return firstName + " " + lastName;
}

const result1 = buildName("Bob"); // works correctly now, returns "Bob Smith"
const result2 = buildName("Bob", undefined); // still works, also returns "Bob Smith"
const result3 = buildName("Bob", "Adams", "Sr."); // error, too many parameters
const result4 = buildName("Bob", "Adams"); // ah, just right

```

> 共享类型

`可选参数`和`尾随默认参数`将在其`类型`上共享通用性，因此两者

```typescript
function buildName(firstName: string, lastName?: string) {
  // ...
}
和

function buildName(firstName: string, lastName = "Smith") {
  // ...
}

```

共享同一类型`(firstName: string, lastName?: string) => string`。默认值`lastName`消失在类型中，仅留下参数是可选的事实。

> 默认形参顺序

如果`默认初始化参数`位于`必传参数`之前，则需要`显式`传递`undefined`以获取默认`初始化值`。

如下例中，`firstName`是`默认初始化参数`，但是它是第一个形参，所以需要`显式`传递`undefined`，以获取默认`初始化值`--`Will`。

```typescript
function buildName(firstName = "Will", lastName: string) {
  return firstName + " " + lastName;
}

const result1 = buildName("Bob"); // error, too few parameters
const result2 = buildName("Bob", "Adams", "Sr."); // error, too many parameters
const result3 = buildName("Bob", "Adams"); // okay and returns "Bob Adams"
const result4 = buildName(undefined, "Adams"); // okay and returns "Will Adams"

```

---

#### 剩余形参

有的时候，会需要将多个参数作为一个组来使用，或者可能不知道一个函数最终将使用多少个参数。

在`TypeScript`中，可以通过`...`将这些参数一起收集到一个变量中，`剩余形参`会被视为无数`可选参数`。

```typescript
function buildName(firstName: string, ...restOfName: string[]) {
  return firstName + " " + restOfName.join(" ");
}

// employeeName will be "Joseph Samuel Lucas MacKinzie"
const employeeName = buildName("Joseph", "Samuel", "Lucas", "MacKinzie");

```

在为`剩余形参`传递参数时，可以使用任意数量的参数，数量也可以为0。

编译器将构建一个以省略号（`...`）后给定的名称，作为参数传入的数组，在函数中可以正常使用。

---

### this

#### this指向

在`js`中默认是`this`指向`window`对象。在严格模式下，`this`将是`undefined`而不是`window`）。

这样导致在代码在执行过程中，不是按照人预期进行执行的。

```typescript
const deck = {
  suits: ["hearts", "spades", "clubs", "diamonds"],
  cards: Array(52),
  createCardPicker: function() {
    return function() {
      const pickedCard = Math.floor(Math.random() * 52);
      const pickedSuit = Math.floor(pickedCard / 13);

      return { suit: this.suits[pickedSuit], card: pickedCard % 13 };
    };
  }
};

const cardPicker = deck.createCardPicker();
const pickedCard = cardPicker();

alert("card: " + pickedCard.card + " of " + pickedCard.suit);// throw Err
```

为此，这边推荐将函数表达式更改为使用`ECMAScript 6`箭头语法。`箭头函数`会捕获`this`创建函数的位置，而不是调用函数的位置。

```typescript
const deck = {
  suits: ["hearts", "spades", "clubs", "diamonds"],
  cards: Array(52),
  createCardPicker: function() {
    // NOTE: the line below is now an arrow function, allowing us to capture 'this' right here
    return () => {
      const pickedCard = Math.floor(Math.random() * 52);
      const pickedSuit = Math.floor(pickedCard / 13);

      return { suit: this.suits[pickedSuit], card: pickedCard % 13 };
    };
  }
};

const cardPicker = deck.createCardPicker();
const pickedCard = cardPicker();

alert("card: " + pickedCard.card + " of " + pickedCard.suit);
```

而且如果将`--noImplicitThis`标志传递给编译器，当在写这种存在二义性代码的时候，`TypeScript`会警告您，指出`this`in `this.suits[pickedSuit]`是类型`any`。

#### this参数

不幸的是，类型`this.suits[pickedSuit]`仍然是`any`。那是因为`this`来自对象文字内部的`函数表达式`。

要解决此问题，可以提供一个显式`this参数`。

第一步，伪造`this参数`，在函数的`参数列表`中添加`this参数`。

```typescript
function f(this: void) {
  // make sure `this` is unusable in this standalone function
}
```

第二步，在上面的示例`Card`中添加几个接口`Deck`，以使类型更清晰，更易于重用：

```typescript
interface Card {
  suit: string;
  card: number;
}
interface Deck {
  suits: string[];
  cards: number[];
  createCardPicker(this: Deck): () => Card;
}
const deck: Deck = {
  suits: ["hearts", "spades", "clubs", "diamonds"],
  cards: Array(52),
  // NOTE: The function now explicitly specifies that its callee must be of type Deck
  createCardPicker: function(this: Deck) {
    return () => {
      const pickedCard = Math.floor(Math.random() * 52);
      const pickedSuit = Math.floor(pickedCard / 13);

      return { suit: this.suits[pickedSuit], card: pickedCard % 13 };
    };
  }
};

const cardPicker = deck.createCardPicker();
const pickedCard = cardPicker();

alert("card: " + pickedCard.card + " of " + pickedCard.suit);
```

然后，`TypeScript`编译器这边就知道了，`createCardPicker`期望在`Deck`对象上被调用。

即现在`this`是类型`Deck`，不是`any`，所以`--noImplicitThis`不会引起任何错误。

---

#### this 回调中的参数

`this`当将函数传递给以后会调用它们的`三方库`时，会存在可能在`in`回调中遇到错误。

因为调用回调的`三方库`只是像`普通函数`一样调用它，所以`this`它将为`undefined`。

但是可以通过一些辅助手段，也可以使用`this参数`来防止`回调错误`。

> 第一步，库作者需要使用以下`注释`

`this: void`表示`addClickListener`期望`onclick`是不需要`this`类型的函数。

```typescript
interface UIElement {
  addClickListener(onclick: (this: void, e: Event) => void): void;
}
```

> 第二步，在调用代码中声明`this`的类型。

不能指定`this`类型为`Handler`，`TypeScript`会检测到`addClickListener`需要具有的函数`this: void`。

```typescript
class Handler {
  info: string;
  onClickBad(this: Handler, e: Event) {
    // oops, used `this` here. using this callback would crash at runtime
    this.info = e.message;
  }
}
const h = new Handler();
uiElement.addClickListener(h.onClickBad); // error!
```

要解决该错误，请更改`this`类型为`void`。

```typescript
class Handler {
  info: string;
  onClickGood(this: void, e: Event) {
    // can't use `this` here because it's of type void!
    console.log("clicked!");
  }
}
const h = new Handler();
uiElement.addClickListener(h.onClickGood);
```

将`onClickGood`其中`this`类型指定为`void`之后，这边`addClickListener`就可以正常调用了。

> 第三步，配合箭头函数

上述两个步骤还不能正常使用`this`属性，如`this.info`。

使用箭头功能

```typescript
class Handler {
  info: string;
  onClickGood = (e: Event) => {
    this.info = e.message;
  };
}
```

之所以有效，是因为箭头函数使用外部函数`this`，因此总是可以将它们传递给需要的东西`this: void`。

> 不利的一面

每个`Handler`类型的对象，都会需要使用箭头功能。

另一方面，方法仅创建一次，并附加到`Handler`的原型，它们在`Handler`类型的所有对象之间`共享`。

---

### 函数返回值

#### 无返回值

可以通过`void`和`never`关键词显示声明无返回值，前置

- `void`类型的函数，只能手动返回`undefined`，或者无返回值。

- `never`类型是那些总是会抛出异常，根本就不会有返回值的函数表达式，和箭头函数表达式的返回值类型。

```typescript
function error(message: string): never {
    throw new Error(message);
}

function testUndefined(): void {
    return undefined;
}

```

---

#### 返回基础类型

可以返回基础类型(`string`,`number`,`boolean`,和`any`),和衍生类型(`数组`，`元组`，和`对象`)，进行相关返回类型声明。

注意实际返回值一定要和声明返回类型是一样的。

```typescript
function buildName(firstName = "Will", lastName: string): string {
  return firstName + " " + lastName;
}

```

---

#### 返回自定义结构

可以返回自定义结构(`interface`，`Class`和`namespace`)，进行相关返回类型声明。

注意实际返回值一定要和声明返回类型是一样的。

```typescript
interface Name {
  firstName: string;
  lastName: string;
}
function buildName(firstName = "Will", lastName: string) {
  return {firstName, lastName};
}

```

---

### 函数重载

> 基础案例

如果当前函数没有指定返回类型，这边在做函数赋值的时候，可以再以显式声明的写法指明返回值。

```typescript
function buildName(firstName: string, ...restOfName: string[]) {
  return firstName + " " + restOfName.join(" ");
}

const buildNameFun: (fname: string, ...rest: string[]) => string = buildName;

```

`JavaScript`本质上是一种非常动态的语言。单个`JavaScript`函数根据传入`参数`的类型，返回不同值的情况并不少见。

```typescript
const suits = ["hearts", "spades", "clubs", "diamonds"];

function pickCard(x): any {
  // Check to see if we're working with an object/array
  // if so, they gave us the deck and we'll pick the card
  if (typeof x == "object") {
    const pickedCard = Math.floor(Math.random() * x.length);
    return pickedCard;
  }
  // Otherwise just const them pick the card
  else if (typeof x == "number") {
    const pickedSuit = Math.floor(x / 13);
    return { suit: suits[pickedSuit], card: x % 13 };
  }
}

const myDeck = [
  { suit: "diamonds", card: 2 },
  { suit: "spades", card: 10 },
  { suit: "hearts", card: 4 }
];
const pickedCard1 = myDeck[pickCard(myDeck)];
alert("card: " + pickedCard1.card + " of " + pickedCard1.suit);

const pickedCard2 = pickCard(15);
alert("card: " + pickedCard2.card + " of " + pickedCard2.suit);

```

上例种`pickCard`函数，根据用户传递的内容判断，返回了两种不同的内容。

上述写法，也可以用`类型系统`来描述，为同一功能提供多种功能类型，作为`重载列表`。

该`列表`是编译器将用来解析函数调用的`列表`。

如下例中创建一个`重载列表`，包括`参数类型`和`返回类型`的重载，以描述我们`pickCard`接受和返回的内容。

```typescript
const suits = ["hearts", "spades", "clubs", "diamonds"];
// 重载列表
function pickCard(x: { suit: string; card: number }[]): number;
function pickCard(x: number): { suit: string; card: number };
// 函数实际声明部分
function pickCard(x): any {
  // Check to see if we're working with an object/array
  // if so, they gave us the deck and we'll pick the card
  if (typeof x == "object") {
    const pickedCard = Math.floor(Math.random() * x.length);
    return pickedCard;
  }
  // Otherwise just const them pick the card
  else if (typeof x == "number") {
    const pickedSuit = Math.floor(x / 13);
    return { suit: suits[pickedSuit], card: x % 13 };
  }
}

const myDeck = [
  { suit: "diamonds", card: 2 },
  { suit: "spades", card: 10 },
  { suit: "hearts", card: 4 }
];
const pickedCard1 = myDeck[pickCard(myDeck)];
alert("card: " + pickedCard1.card + " of " + pickedCard1.suit);

const pickedCard2 = pickCard(15);
alert("card: " + pickedCard2.card + " of " + pickedCard2.suit);

```

通过此更改，解析器可以通过重载列表，对该`pickCard`函数进行`类型检查`调用。

为了使编译器选择正确的`类型检查`，它遵循与基础`JavaScript`相似的过程。

编译器会查看`重载列表`，并在第一次重载之前，尝试使用提供的参数调用该函数。

如果找到`形参类型匹配项`，它将选择此`重载类型`作为正确的`形参类型`。因此，习惯上将重载从`最具体`到`最不具体`排序。

请注意，该`function pickCard(x): any`片段不是`重载列表`的一部分，这是具体的函数声明部分。

`pickCard`函数的`重载列表`，`x`只支持`对象`和`数字`形式，使用其他任何参数类型进行调用都会导致错误。

---

> 基于String Literal Types的方法重载

可以使用相同的方式使用字符串文字类型来区分重载，当传递参数值为某个具体值，也可以具体指定返回类型。

```typescript

function createElement(tagName: "img"): HTMLImageElement;
function createElement(tagName: "input"): HTMLInputElement;
// ... more overloads ...
function createElement(tagName: string): Element {
  // ... code goes here ...
}
```

---

## 命名空间(namespace)

```typescript

namespace Validation {
    export interface StringValidator {
        isAcceptable(s: string): boolean;
    }

    const consttersRegexp = /^[A-Za-z]+$/;
    const numberRegexp = /^[0-9]+$/;

    export class consttersOnlyValidator implements StringValidator {
        isAcceptable(s: string) {
            return consttersRegexp.test(s);
        }
    }

    export class ZipCodeValidator implements StringValidator {
        isAcceptable(s: string) {
            return s.length === 5 && numberRegexp.test(s);
        }
    }
}

// Some samples to try
const strings = ["Hello", "98052", "101"];

// Validators to use
const validators: { [s: string]: Validation.StringValidator; } = {};
validators["ZIP code"] = new Validation.ZipCodeValidator();
validators["constters only"] = new Validation.consttersOnlyValidator();

// Show whether each string passed each validator
for (const s of strings) {
    for (const name in validators) {
        console.log(`"${ s }" - ${ validators[name].isAcceptable(s) ? "matches" : "does not match" } ${ name }`);
    }
}

```

---
