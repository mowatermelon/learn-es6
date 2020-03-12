# interface

`TypeScript`的核心原则之一是`类型检查`的重点是值的形状。有时称为`鸭式打字`或`结构子类型化`。在`TypeScript`中，接口充当命名这些类型的角色，并且是定义`代码内契约`以及`项目外代码契约`的有效方法。

## 基础说明

### 接口可索引类型

我们也可以描述可以`索引`到的类型，例如`a[10]`或`ageMap["daniel"]`。

`可索引类型`具有`索引签名`，该签名描述了可用于索引对象的`类型`，以及建立索引时对应的`返回类型`。

支持两种类型的索引签名：`字符串`和`数字`。可以同时支持两种类型的`索引器`，

```typescript
const { log } = console;
interface StringArray {
    [index: number]: string;
}

const myArray: StringArray = ["Bob", "Fred"];

const myStr: string = myArray[0];
log(myStr);// Bob
```

上面，我们有一个`StringArray`带有索引签名的接口。该索引签名指出，当添加 `StringArray`对象中索引名类型为`number`的字段时，这个时候对应的字段值应该为一个字符串`string`。

---

> 从数字索引器返回的`类型`，必须是从字符串索引器返回的类型的`子类型`。

这是因为当使用编制索引时，`JavaScript`实际上会在将其`string`编入对象之前将其隐式转换为`number`。

这意味着，与索引`100`（一`number`），与索引`100`（一`string`）是同样的运行效果，所以这两个要一致。

```typescript
const { log } = console;
class Animal {
    name: string;
    constructor(name: string) {
        this.name = name;
    }
}
class Dog extends Animal {
    breed: string;
    constructor(breed: string) {
        super(breed);
        this.breed = breed;
    }
}

interface NotOkay {
    [x: number]: Animal;// Numeric index type 'Animal' is not assignable to string index type 'Dog'.
    [x: string]: Dog;
}
```

---

> 尽管`字符串索引签名`是描述“字典”模式的强大方法，但它们还强制所有字段与其返回类型匹配。

这是因为字符串索引声明该字符串`obj.property`也可以作为`obj["property"]`。

在以下示例中，`name`的类型与`字符串索引`的类型不匹配，并且`类型检查器`给出错误

```typescript
interface NumberDictionary {
    [index: string]: number;
    length: number;    // ok, length is a number
    name: string;      // Property 'name' of type 'string' is not assignable to string index type 'number'.
}
```

但是，如果索引签名是字段类型的`并集`，则可以接受不同`类型`的`字段`。

```typescript
interface NumberOrStringDictionary {
    [index: string]: number | string;
    length: number;    // ok, length is a number
    name: string;      // ok, name is a string
}
```

---

> 与`readonly`进行结合

可以进行索引签名`readonly`以防止分配给它们的索引

```typescript
interface ReadonlyStringArray {
    readonly [index: number]: string;
}
const myArray: ReadonlyStringArray = ["Alice", "Bob"];
myArray[2] = "Mallory"; // Index signature in type 'ReadonlyStringArray' only permits reading.

```

---

### 接口扩展

> 单一接口扩展

像类一样，接口可以互相`扩展`。

可以将一个接口的成员复制到另一个接口中，从而在将接口分离为可重用组件的过程中提供了更大的灵活性。

```typescript
const { log } = console;
interface Shape {
    color: string;
}

interface Square extends Shape {
    sideLength: number;
}

const square = {} as Square;
square.color = "blue";
square.sideLength = 10;
log(square);// {color: "blue", sideLength: 10}
```

---

> 多个接口扩展

一个接口可以扩展多个接口，从而创建所有接口的组合。

```typescript
const { log } = console;
interface Shape {
    color: string;
}

interface PenStroke {
    penWidth: number;
}

interface Square extends Shape, PenStroke {
    sideLength: number;
}

const square = {} as Square;
square.color = "blue";
square.sideLength = 10;
square.penWidth = 5.0;
log(square);// {color: "blue", sideLength: 10, penWidth: 5}
```

---

### 类与接口继承

> 基础实现字段接口继承

在`TypeScript`中，还可以使用`C＃`和`Java`等语言中接口的最常见用法之一，即`显式强制类满足特定协定`。

```typescript
interface ClockInterface{
  currTime : Date;
}
class Clock implements ClockInterface{
    currTime: Date = new Date();
    // if not declare currTime property,it will throw error.
    // Class 'Clock' incorrectly implements interface 'ClockInterface'.
    // Property 'currTime' is missing in type 'Clock' but required in type 'ClockInterface'.
    constructor(h: number, s: number) { }
   // 'h' is declared but its value is never read.
   // 's' is declared but its value is never read.
}
```

注意，接口中声明了公共类的字段，在类继承的时候一定要做对应字段声明，否则这边会抛出相关错误。

---

> 基础实现方法接口继承

还可以像`setTime`在下面的示例中所做的那样，描述在类中实现的`接口`中的`方法`。

```typescript
interface ClockInterface {
    currentTime: Date;
    setTime(d: Date): void;
}

class Clock implements ClockInterface {
    currentTime: Date = new Date();
    setTime(d: Date) {
        this.currentTime = d;
    }
    constructor(h: number, m: number) { }
}
```

`接口`描述了类的公共`字段`和`方法`，而不是公共和私有中的所有`字段`和`方法`。

即如果是特定子类中才需要进行实例的`字段`和`方法`，应该放到具体的子类中进行相关实现，而不是在`公共接口`中实现。

这主要是禁止使用它们来检查`类`是否还具有针对该类实例的`私有类`的`特定类型`。

---

### 静态和实例差异

当使用类和接口时，请记住一个类有两种类型：`静态端`的`类型`和`实例端`的`类型`。

如果使用`构造签名(new)`创建`接口`，并尝试创建实现该接口的类，则会抛出错误。

```typescript
interface ClockConstructor {
    new(hour: number, minute: number);
    // Construct signature, which lacks return-type annotation, implicitly has an 'any' return type.
}

class Clock implements ClockConstructor {
// Class 'Clock' incorrectly implements interface 'ClockConstructor'.
    currentTime: Date = new Date();
    constructor(h: number, m: number) { }
}
```

这是因为当类实现`接口`时，仅检查该类的`实例侧`。由于`构造函数`位于`静态端`，因此它不包含在此检查中。

即类在实现接口的时候，只检测是否有实现可继承的实例字段和实例方法，而构造函数属于类的静态方法，不属于检测访问内，即不可用于继承，所以这边不可以做正确继承。

---

> 如何绕过构造签名检测，修改构造签名的接口不可以直接用于类继承，但是还可以用作类型检测

在此示例中，在`ClockConstructor`接口中修改了`构造签名`，在`ClockInterface`接口中添加了`实例方法`。

然后，为方便起见，我们定义一个构造函数`createClock`，该函数创建传递给它的类型的实例，这种绕过静态检测的写法，不建议使用。

```typescript
const { log } = console;
interface ClockConstructor {
    new (hour: number, minute: number): ClockInterface;
}


interface ClockInterface {
    tick(): void;
}

function createClock(ctor: ClockConstructor, hour: number, minute: number): ClockInterface {
    return new ctor(hour, minute);
}

class DigitalClock implements ClockInterface {
    h: number;
    m: number;
    constructor(h: number, m: number) { 
        this.h = h;
        this.m = m;
    }
    tick() {
        log(`beep beep,the time is ${this.h}:${this.m}`);
    }
}
class AnalogClock implements ClockInterface {
    h: number;
    m: number;
    constructor(h: number, m: number) { 
        this.h = h;
        this.m = m;
    }
    tick() {
        log(`tick tock is ${this.h}:${this.m}`);
    }
}

const digital = createClock(DigitalClock, 12, 17);
const analog = createClock(AnalogClock, 7, 32);

// log(ClockConstructor);// 'ClockConstructor' only refers to a type, but is being used as a value here.
// log(ClockInterface);// 'ClockInterface' only refers to a type, but is being used as a value here.
log(DigitalClock);
// class DigitalClock {
//     constructor(h, m) { }
//     tick() {
//         console.log("beep beep");
//     }
// }
log(AnalogClock);
// class AnalogClock {
//     constructor(h, m) { }
//     tick() {
//         console.log("tick tock");
//     }
// }

log(digital.tick());// beep beep,the time is 12:17
log(analog.tick());// tick tock is 7:32
```

`createClock`的第一个参数的类型为`ClockConstructor`，`createClock(AnalogClock, 7, 32)`和`createClock(DigitalClock, 12, 17)`，因此会检查`AnalogClock`和`DigitalClock`是否具有正确的`构造函数签名`和对应的构造返回值。

这两个类都是继承了`ClockInterface`，同时又有正确的`构造函数签名`，所以这边检测通过。

---

> 如何绕过构造签名检测，使用类表达式强制声明继承。

这种在严格模式检测中，也是会报错，这种绕过静态检测的写法，不建议使用。

```typescript
const { log } = console;
interface ClockConstructor {
    new(hour: number, minute: number);
  // Construct signature, which lacks return-type annotation, implicitly has an 'any' return type.
}

interface ClockInterface {
  tick(): void;
}

const Clock: ClockConstructor = class Clock implements ClockInterface {
    h: number;
    m: number;
    constructor(h: number, m: number) { 
        this.h = h;
        this.m = m;
    }
    tick() {
        log(`beep beep,the time is ${this.h}:${this.m}`);
    }
}

log((new Clock(7,33)).tick())// beep beep,the time is 7:33
```

---

### 类私有字段继承

首先要注意`类与类`，`接口和类`和`接口与接口`之间的继承是通过`extends`关键词，类与接口的继承是通过`implements`关键词。

当`接口`扩展`类`类型时，它将继承该`类`的`成员`，但不继承其实现。

好像该`接口`声明了该`类`的`所有成员`，而没有提供`实现`。

`接口`甚至继承基类的`私有成员`和`受保护成员`。这意味着，当您创建一个扩展带有`私有`或`受保护成员`的`类`的`接口`时，该接`口`类型只能由该`类`或其`子类`实现。

当具有较大的继承`层次`结构，但要指定您的代码仅适用于具有某些字段的子类时，这很有用。`子类`除了从`基类`继承外不必关联。

```typescript
const { log } = console;
class Control {
    private state: any;
    constructor(state: string) { this.state = state; }
    getState(): string{
        return this.state;
    }
}

interface SelectableControl extends Control {
    select(): void;
}

class Button extends Control implements SelectableControl {
    select() {
        log(`that's state is ${this.getState()}`)
    }
}

class TextBox extends Control {
    select() {
        log(`that's state is ${this.getState()}`)
    }
}

// Class 'ImageConstructor' incorrectly implements interface 'SelectableControl'.
//   Types have separate declarations of a private property 'state'.
// Property 'getState' is missing in type 'ImageConstructor' but required in type 'SelectableControl'.
class ImageConstructor implements SelectableControl {
    private state: any;
    type: string = 'Image';// can declare common property
    readonly style: string = ''; // can declare readonly property
    select() {
        log(`that's state is ${this.state},that's type is ${this.type}`);
    }
}

class LocationConstructor {
    private state: any;
    type: string = 'Location';// can declare common property
    readonly style: string = ''; // can declare readonly property
    select() {
        log(`that's state is ${this.state},that's type is ${this.type}`);
    }
}

const oButton = new Button('Button');
const oTextBox = new TextBox('TextBox');
log(oButton.select());// that's state is Button
log(oTextBox.select());// that's state is TextBox

const oImage = new ImageConstructor();
const oLocation = new LocationConstructor();
log(oImage.select());// that's state is undefined,that's type is Image
log(oLocation.select());// that's state is undefined,that's type is Location
```

`private`字段和方法，只能在声明的类内部进行访问和修改，后续继承的子类和子接口都无法通过`字面量`的形式访问。

在以上示例中，`SelectableControl`接口继承了`Control`类，包括私有`state`字段和`getState`方法。

由于`state`是私有成员，因此只有`Control`的后代，才能实现`SelectableControl`。

这是因为只有`Control`的后代，会拥有源自同一声明的私有成员`state`，这是私有成员必须兼容的要求。

---

## 作用说明

### 形参类型校验

```typescript
const { log } = console;
function printLabel(labeledObj: { label: string }) {
  log(labeledObj.label);
}

const myObj = {size: 10, label: "Size 10 Object"};
printLabel(myObj);// Size 10 Object

const testObj = { size: 1 };
printLabel(testObj);
// Argument of type '{ size: number; }' is not assignable to parameter of type '{ label: string; }'.
//   Property 'label' is missing in type '{ size: number; }' but required in type '{ label: string; }'.
```

类型检查器检查对的调用`printLabel`。该`printLabel`函数具有一个`参数`，该参数要求传入的对象具有一个名为`label` 值类型为`string`的字段。

请注意，我们的对象实际上具有比此更多的字段，但是编译器仅检查是否至少存在所需的字段并与所需的类型匹配。

```typescript
const { log } = console;
interface LabeledValue {
    label: string;
}

function printLabel(labeledObj: LabeledValue) {
  log(labeledObj.label);
}

const testObj = { size: 1 };
printLabel(testObj);
// Argument of type '{ size: number; }' is not assignable to parameter of type '{ label: string; }'.
//   Property 'label' is missing in type '{ size: number; }' but required in type '{ label: string; }'.

```

接口`LabeledValue`是一个名称，我们现在可以在前面的示例中使用它来描述需求。它仍然表示具有一个称为`label` 的单个字段`string`。

注意，我们不必明确地说要传递给该对象，可以像使用其他语言一样实现此接口`printLabel`。在这里，只有`类型`很重要。

如果我们传递给该函数的对象满足列出的要求，则允许它进行传递。

值得指出的是，类型检查器不需要这些字段以任何`顺序`出现，而仅要求接口存在的字段具有必需的`类型`。

---

### 可选字段

并非接口的所有字段都是必需的。有些在某些条件下存在或根本不存在。

具有可选字段的接口与其他接口的编写方式相似，每个可选字段，在声明中字段名称的末尾用`?`表示。

```typescript
const { log } = console;
interface SquareConfig {
    color?: string;
    width?: number;
}

function createSquare(config: SquareConfig): {color: string; area: number} {
    const newSquare = {color: "white", area: 100};
    if (config.color) {
        newSquare.color = config.color;
    }
    if (config.width) {
        newSquare.area = config.width * config.width;
    }
    return newSquare;
}

const mySquare = createSquare({color: "black"});
log(mySquare);// {color: "black", area: 100}
```

> 基础字段拼写检测

可选字段的优点在于，您可以描述这些可能的可用字段，同时仍然可以防止使用不属于接口的`字段`。

例如，如果我们在中错误键入了`color`字段的名称`createSquare`，则会收到一条错误消息，通知我们：

```typescript
const { log } = console;
interface SquareConfig {
    color?: string;
    width?: number;
}

function createSquare(config: SquareConfig): { color: string; area: number } {
    const newSquare = {color: "white", area: 100};
    if (config.clor) {
        // Property 'clor' does not exist on type 'SquareConfig'. Did you mean 'color'
        newSquare.color = config.clor;
    }
    if (config.width) {
        newSquare.area = config.width * config.width;
    }
    return newSquare;
}

const mySquare = createSquare({ clor: "black" });
// Argument of type '{ clor: string; }' is not assignable to parameter of type 'SquareConfig'.
//   Object literal may only specify known properties, but 'clor' does not exist in type 'SquareConfig'. Did you mean to write 'color'?
```

### 只读字段

某些字段仅在首次创建对象时才可修改。您可以通过将`readonly`字段名称放在前面来指定此名称。

```typescript
interface Point {
    readonly x: number;
    readonly y: number;
}
```

您可以`Point`通过分配对象文字来构造一个。转让后，`x`和`y`字段都不能更改。

```typescript
interface Point {
    readonly x: number;
    readonly y: number;
}
const p1: Point = { x: 10, y: 20 };
p1.x = 5; // Cannot assign to 'x' because it is a read-only property.
```

---

> `ReadonlyArray<T>`

`TypeScript`的`ReadonlyArray<T>`类型`Array<T>`与删除的所有变异方法相同，因此可以确保创建后不更改数组。

```typescript
let a: number[] = [1, 2, 3, 4];
const ro: ReadonlyArray<number> = a;
ro[0] = 12; // Index signature in type 'readonly number[]' only permits reading.
ro.push(5); // Property 'push' does not exist on type 'readonly number[]'.
ro.length = 100; // Cannot assign to 'length' because it is a read-only property.
a = ro; // The type 'readonly number[]' is 'readonly' and cannot be assigned to the mutable type 'number[]'.
```

在代码片段的最后一行，可以看到，即使将整个`ReadonlyArray`数组分配回`普通数组`也是非法的。

但是，仍然可以使用`类型断言`来覆盖它：

```typescript
const { log } = console;
let a: number[] = [1, 2, 3, 4];
const ro: ReadonlyArray<number> = a;
a = ro as number[];
log(a);// [1, 2, 3, 4]
```

---

> `readonly` vs `const`

记住在确认是使用`readonly`还是`const`时，最简单的方法询问是否在`变量`或`字段`上使用它。使用`变量`就是`const`，而使用`字段`就是`readonly`。

---

### 模糊字段检测

> `字符串索引`签名

如果确定对象可以具有某些以特殊方式使用的`额外字段`，则更好的方法可能是添加`字符串索引`签名。

如果`SquareConfig`可以具有上述类型的`color`和`width`字段，但也可以具有任意数量的`其他字段`，那么我们可以这样定义它：

```typescript
const { log } = console;
interface SquareConfig {
    color?: string;
    width?: number;
    [propName: string]: any;
}
function createSquare(config: SquareConfig): { color: string; area: number } {
    const newSquare = {color: "white", area: 100};
    if (config.clor) {
        newSquare.color = config.clor;
    }
    if (config.width) {
        newSquare.area = config.width * config.width;
    }
    return newSquare;
}

const mySquare = createSquare({ clor: "black" });
log(mySquare);// {color: "black", area: 100}
```

上面代码声明了一个`SquareConfig`可以具有任意数量的`字段`，只要它们不是`color`或者`width`，它们的类型就没有关系。

---

> 重新赋值变量

解决这些检查的最后一种方法（可能有点令人惊讶）是将对象分配给另一个变量：由于`squareOptions`不会进行过多的字段检查，因此编译器不会报误。

```typescript

const { log } = console;
interface SquareConfig {
    color?: string;
    width?: number;
}
function createSquare(config: SquareConfig): { color: string; area: number } {
    const newSquare = {color: "white", area: 100};
    if (config.color) {
        newSquare.color = config.color;
    }
    if (config.width) {
        newSquare.area = config.width * config.width;
    }
    return newSquare;
}
const squareOptions = { colour: "red", width: 100 };
const mySquare = createSquare(squareOptions);
log(mySquare);// {color: "white", area: 10000}
```

因为在上面示例中变量已经包含了`width`字段，即只要您在`squareOptions`和`SquareConfig`之间具有相同的字段，上述变通办法就会起作用。

但是，如果变量没有任何公共对象`字段`，它将失败。例如：

```typescript
interface SquareConfig {
    color?: string;
    width?: number;
}
function createSquare(config: SquareConfig): { color: string; area: number } {
    const newSquare = {color: "white", area: 100};
    if (config.color) {
        newSquare.color = config.color;
    }
    if (config.width) {
        newSquare.area = config.width * config.width;
    }
    return newSquare;
}
const squareOptions = { colour: "red" };
const mySquare = createSquare(squareOptions);
// Type '{ colour: string; }' has no properties in common with type 'SquareConfig'.

```

---

> 总结

请记住，对于上述简单代码，您可能不应该试图`绕开`这些检查。对于具有方法和保持状态的更复杂的`对象常量`，您可能需要牢记这些技术，

但是大多数`多余的字段错误`，实际上也是一种`错误`。这意味着，如果遇到有很多类似字段的字段检查问题，则可能需要修改一些类型声明。

在这种情况下，如果可以将同时具有`color`或`colour`字段的对象传递给`createSquare`，则应该修正的定义`SquareConfig`以反映这一点。


---

### 函数类型约束

为了描述带有接口的函数类型，我们给接口一个调用签名。这就像只声明参数列表和返回类型的函数声明。参数列表中的每个参数都需要名称和类型。

```typescript
interface SearchFunc {
    (source: string, subString: string): boolean;
}


```

一旦定义，我们可以像使用其他接口一样使用此`函数类型接口`。在这里，我们展示了如何创建函数类型的`变量`，并为其分配`相同类型`的`函数值`。

```typescript
interface SearchFunc {
    (source: string, subString: string): boolean;
}
let mySearch: SearchFunc;
mySearch = function(source: string, subString: string) {
    let result = source.search(subString);
    return result > -1;
}

```

---

> 形参名称可以不一样

为了使函数类型正确键入检查，`参数`名称不需要`完全匹配`。

```typescript
const { log } = console;
interface SearchFunc {
    (source: string, subString: string): boolean;
}
let mySearch: SearchFunc;
mySearch = function(src: string, sub: string): boolean {
    let result = src.search(sub);
    return result > -1;
}
log(mySearch('watermelon', 'melon'));// true

```

---

> 隐式类型推导

一次检查一个功能参数，每个对应参数位置的类型相互检查。

如果根本不想指定类型，那么`TypeScript`的上下文类型可以推断`参数类型`，因为函数值直接分配给`type`变量`SearchFunc`。

同样，在这里，函数表达式的返回类型也由其返回的值（此处`false`和`true`）所隐含。

```typescript
const { log } = console;
interface SearchFunc {
    (source: string, subString: string): boolean;
}
let mySearch: SearchFunc;
mySearch = function(src, sub) {
    let result = src.search(sub);
    return result > -1;
}
log(mySearch(1,2));// Argument of type '1' is not assignable to parameter of type 'string'.

```

---

> 类型匹配失败，会进行报错

如果函数表达式返回数字或字符串，则`类型检查器`将产生一个错误，指示`返回类型`与`SearchFunc`接口中描述的返回类型不匹配。

```typescript
interface SearchFunc {
    (source: string, subString: string): boolean;
}

let mySearch: SearchFunc;

// error: Type '(src: string, sub: string) => string' is not assignable to type 'SearchFunc'.
// Type 'string' is not assignable to type 'boolean'.
mySearch = function(src, sub) {
  let result = src.search(sub);
  return "string";
};
```

---

### 第三方交互

`接口`可以描述现实世界`JavaScript`中存在的`丰富类型`。

由于`JavaScript`具有动态和灵活的特性，因此有时可能会遇到一个`对象`，该`对象`可以作为某些`类型`的组合使用。

下述的示例是一个既具有`功能`又具有`对象特性`的`对象`，还具有`其他字段`。

```typescript
const { log } = console;
interface Counter {
    (start: number): string;
    interval: number;
    reset(): void;
}

function getCounter(): Counter {
    const counter = (function (start: number) { }) as Counter;
    counter.interval = 123;
    counter.reset = function () { };
    return counter;
}

const c = getCounter();
c(10);
log(c.interval);// 123
c.reset();
log(c.interval);// 123
c.interval = 5.0;
log(c.interval);// 5
```

与第三方`JavaScript`交互时，可能需要使用上述模式来完全描述类型。

---