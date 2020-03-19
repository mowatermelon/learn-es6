# ts优势与劣势

## ts背景

`TypeScript` 起源于开发应用程序规模的 `JavaScript` 应用程序的需求。`Microsoft`的语言开发者们，说内部以及外部的客户都表示他们构建 `JavaScript` 代码的问题。

很多最终依赖于 `JavaScript` 的开发者通常用编译为 `JavaScript` 代码的另一种语言写脚本，例如 `CoffeeScript` 和 `Script#` (读作 `ScriptSharp`)。

一个明显的`劣势`是也许无法从那另一种语言使用任何 `JavaScript` 的具体的语言特性，如果那种语言不支持它的话。

在 `Microsoft` 内部，它导致了自定义工具以简化 `JavaScript` 组件的编写的需求。

---

## ts官网

https://www.typescriptlang.org/index.html

目前最新的版本是`3.7`

---

## ts定义

```text
TypeScript is a typed superset of JavaScript that compiles to plain JavaScript. Any browser. Any host. Any OS. Open source.
```

`TypeScript` 是 `JavaScript` 的一个`超集`，主要提供了类型系统和对 `ES6` 的支持，它由 `Microsoft` 开发，代码开源于 `GitHub` 上。

---

## 优点

- TypeScript可以编译为干净，简单的JavaScript代码，该代码可在任何浏览器，Node.js或任何支持ECMAScript 3（或更高版本）的JavaScript引擎中运行。
- 类型使JavaScript开发人员可以在开发JavaScript应用程序时使用高效的开发工具和做法，例如静态检查和代码重构。
- 类型是可选的，并且类型推论允许一些类型的注释对代码的静态验证产生很大的影响。
- 类型使您可以定义软件组件之间的接口，并深入了解现有JavaScript库的行为。
- TypeScript支持最新和不断发展的JavaScript功能，包括ECMAScript 2015中的功能以及未来的提案（例如异步功能和装饰器），以帮助构建可靠的组件。
- 没有类型隐式转换，加上预编译报错，可以减少开发投入到测试中的时间开销。
- 类型强检测可以减少编写文档和开发对接的时间，TypeScript可以清楚记录每一个对象的属性，方法的参数等，有助于函数的文档化、阐明用法和减少认知开销。
- 增强的OO，可以用更多的设计模式，比如IOC，AOP。
- 在编辑器中进行开发的时候，这边就可以直接提示相关方法和属性的类型，提高开发效率。

---

## 缺点

- 短期可能会增加一些开发成本，毕竟要多写一些类型的定义，不过对于一个需要长期维护的项目，TypeScript 能够减少其维护成本
- 有一定的学习成本，需要理解接口（Interfaces）、泛型（Generics）、类（Classes）、枚举类型（Enums）等前端工程师可能不是很熟悉的概念
- ts的生态相对于js的生态圈还是小一些
- 而且一些工具对于ts的支持度不友好，导致使用起来非常脆，不鲁棒，强耦合于AST格式
- 现有的一些第三方库，基于能力扩展性考虑，很多都是自定义的类型和TS这块类型系统不兼容，导致使用体验不好。
- 对于短时间需要上线并且生命周期中比较短的页面项目，使用ts的时间开销成本相对较高。
- 集成到构建流程需要一些工作量

---

## 注意事项

> 一般类型

不要用真实的类型`Number`，`String`，`Boolean`，`Symbol`，或`Object` ，`TS`中用于变量类型声明的指的是，几乎从来没有在`JavaScript`代码中正确使用非基本盒装的`对象`，如`number`，`string`，`boolean`。

```typescript
/* WRONG */
function reverse(s: String): String;
/* OK */
function reverse(s: string): string;
```

注意，不要把`symbol`当为可使用的类型。

---

> 回调返回类型

不要将返回类型`any`，用于声明其值将被忽略的回调函数，推荐使用返回类型`void`进行声明。


```typescript
/* WRONG */
function fn(x: () => any) {
  x();
}
/* OK */
function fn(x: () => void) {
  x();
}

```

因为使用返回类型`void`更为安全，因为它可以防止`x`，以未经检查的方式意外使用返回值。

```typescript
function fn(x: () => void) {
  var k = x(); // oops! meant to do something else
  k.doSomething(); // error, but would be OK if the return type had been 'any'
}
```

---

> 回调中的可选参数

除非是真的业务需求，否则不要在回调中使用`可选参数`

```typescript
/* WRONG */
interface Fetcher {
  getObject(done: (data: any, elapsedTime?: number) => void): void;
}
/* OK */
interface Fetcher {
  getObject(done: (data: any, elapsedTime: number) => void): void;
}
```

---

> 重载和回调

不要编写仅在回调函数上有所不同的单独重载，请使用最大`Arity`编写单个重载

```typescript
/* WRONG */
declare function beforeAll(action: () => void, timeout?: number): void;
declare function beforeAll(
  action: (done: DoneFn) => void,
  timeout?: number
): void;

/* OK */
declare function beforeAll(
  action: (done: DoneFn) => void,
  timeout?: number
): void;
```

因为案例中，忽略参数始终是合法的，因此不需要较短的重载。可以提供一个较短的回调，允许输入错误类型的函数，与第一个进行重载匹配。

---

> 重载函数顺序

不要将更抽象的重载放在更具象的重载之前，重载函数顺序应该是从最具象的重载逐渐放到最抽象的重载。

```typescript
/* WRONG */
declare function fn(x: any): any;
declare function fn(x: HTMLElement): number;
declare function fn(x: HTMLDivElement): string;

var myElem: HTMLDivElement;
var x = fn(myElem); // x: any, wat?

/* OK */
declare function fn(x: HTMLDivElement): string;
declare function fn(x: HTMLElement): number;
declare function fn(x: any): any;

var myElem: HTMLDivElement;
var x = fn(myElem); // x: string, :)
```

因为，`TypeScript` 在解决函数调用时选择第一个匹配的重载。当较早的过载比较晚的过载`更抽象`时，之后的过载实际上是隐藏的，无法调用。

---

> 重载函数与可选参数

如果这边可以用可选参数实现的，不要使用函数重载。

```typescript
/* WRONG */
interface Example {
  diff(one: string): number;
  diff(one: string, two: string): number;
  diff(one: string, two: string, three: boolean): number;
}

/* OK */
interface Example {
  diff(one: string, two?: string, three?: boolean): number;
}
```

原因有两个：

- `TypeScript`通过查看是否可以使用源的参数，调用目标的任何签名来解决签名兼容性， 并允许无关的参数。

例如，下面代码仅在使用`可选参数`正确编写签名后，才暴露错误。

```typescript
function fn(x: (a: string, b: number, c: number) => string) { }
const x = ()=>[];
// When written with overloads, OK -- used first overload
// When written with optionals, correctly an error
fn(x);
// Argument of type '() => never[]' is not assignable to parameter of type '(a: string, b: number, c: number) => string'.
//   Type 'never[]' is not assignable to type 'string'.
```

- 使用TypeScript的`严格空值检查`功能。由于未指定的参数`undefined`在`JavaScript`中显示，因此最好将显式值传递`undefined`给带有可选参数的函数。例如，此代码在`严格空值检查`下应该可以。

```typescript
var x: Example;
// When written with overloads, incorrectly an error because of passing 'undefined' to 'string'
// When written with optionals, correctly OK
x.diff("something", true ? undefined : "hour");
```

---

> 重载函数与联合类型

不要只因为参数类型，而写的函数重载。

```typescript
/* WRONG */
interface Moment {
  utcOffset(): number;
  utcOffset(b: number): Moment;
  utcOffset(b: string): Moment;
}
/* OK */
interface Moment {
  utcOffset(): number;
  utcOffset(b: number | string): Moment;
}
```

请注意，`b`由于签名的返回类型不同，我们在此处未设置为`可选`。

---
