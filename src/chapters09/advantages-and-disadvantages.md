# ts背景

`TypeScript` 起源于开发应用程序规模的 `JavaScript` 应用程序的需求。`Microsoft`的语言开发者们，说内部以及外部的客户都表示他们构建 `JavaScript` 代码的问题。

很多最终依赖于 `JavaScript` 的开发者通常用编译为 `JavaScript` 代码的另一种语言写脚本，例如 `CoffeeScript` 和 `Script#` (读作 `ScriptSharp`)。

一个明显的`劣势`是也许无法从那另一种语言使用任何 `JavaScript` 的具体的语言特性，如果那种语言不支持它的话。

在 `Microsoft` 内部，它导致了自定义工具以简化 `JavaScript` 组件的编写的需求。

---

# ts官网

https://www.typescriptlang.org/index.html

目前最新的版本是`3.7`

---

# ts定义

```text
TypeScript is a typed superset of JavaScript that compiles to plain JavaScript. Any browser. Any host. Any OS. Open source.
```

`TypeScript` 是 `JavaScript` 的一个`超集`，主要提供了类型系统和对 `ES6` 的支持，它由 `Microsoft` 开发，代码开源于 `GitHub` 上。

---

# 优点

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

# 缺点

- 短期可能会增加一些开发成本，毕竟要多写一些类型的定义，不过对于一个需要长期维护的项目，TypeScript 能够减少其维护成本
- 有一定的学习成本，需要理解接口（Interfaces）、泛型（Generics）、类（Classes）、枚举类型（Enums）等前端工程师可能不是很熟悉的概念
- ts的生态相对于js的生态圈还是小一些
- 而且一些工具对于ts的支持度不友好，导致使用起来非常脆，不鲁棒，强耦合于AST格式
- 现有的一些第三方库，基于能力扩展性考虑，很多都是自定义的类型和TS这块类型系统不兼容，导致使用体验不好。
- 对于短时间需要上线并且生命周期中比较短的页面项目，使用ts的时间开销成本相对较高。
- 集成到构建流程需要一些工作量

---
