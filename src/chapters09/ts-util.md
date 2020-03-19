
# ts相关内置工具介绍

## 可选操作链(optional-chaining)

### 基础介绍

[可选操作链][optional-chaining]，目前处于[stage 4][stage 4]，

许多`API`返回一个对象或`null` / `undefined`，并且可能仅在结果不为`null`时才希望从结果中提取属性，`Optional Chaining Operator`允许开发人员处理许多情况，而无需重复自己和/或在临时变量中分配中间结果，`TS`中可以直接使用该`运算符`。

```typescript
const { log } = console;
let test = Math.random() > 0.5 ? ['ssss'] : null;

log(test?.length);

// 1
// undefined
// undefined
// 1
```

---

### 相关案例

> 基础案例

`?.`运算符左侧(`LHS`)的值为`undefined`或`null`，则表达式的值为`undefined`。否则，将正常执行后续`属性`访问，`方法`或`函数`调用。

```typescript
a?.b                          // undefined if `a` is null/undefined, `a.b` otherwise.
// a == null ? undefined : a.b

a?.[x]                        // undefined if `a` is null/undefined, `a[x]` otherwise.
// a == null ? undefined : a[x]

a?.b()                        // undefined if `a` is null/undefined
// a == null ? undefined : a.b()
// throws a TypeError if `a.b` is not a function
// otherwise, evaluates to `a.b()`

a?.()                        // undefined if `a` is null/undefined
// a == null ? undefined : a()  
// throws a TypeError if `a` is neither null/undefined, nor a function
// invokes the function `a` otherwise
```

|        | `a` is `null`/`undefined` | `a` is not `null`/`undefined` |
|:-------|:--------------------------|:------------------------------|
| a?.b   | `undefined`               | `a.b`                         |
| a?.[x] | `undefined`               | `a[x]`                        |
| a?.b() | `undefined`               | `a.b()`                       |
| a?.()  | `undefined`               | `a()`                         |

---

> 短路

如果，运算符左侧(`LHS`)，`?.`评估为`null`/`undefined`，则不评估运算符右侧(`RHS`)，这个过程称为`短路(Short-circuiting)`。

```typescript
a?.[++x]         // `x` is incremented if and only if `a` is not null/undefined
// a == null ? undefined : a[++x]
```

---

> 长时间短路

实际上，`短路`在被触发时不仅会跳过当前的`属性`访问，`方法`或`函数`调用，而且还会直接跳过`可选链接`运算符之后的`属性`访问，`方法`或`函数`调用的整个`执行链`。

```typescript
a?.b.c(++x).d  // if `a` is null/undefined, evaluates to undefined. Variable `x` is not incremented.
               // otherwise, evaluates to a.b.c(++x).d.
// a == null ? undefined : a.b.c(++x).d
```

因为先对`a`和`null`进行匹配检查。

- 如果，`a`为`null`，则直接短路返回`undefined`。
- 如果，`a`不为`null`，但`a.b`为空，则在试图访问`a.b`的`c`属性时，就会抛出一个`TypeError`错误。

该功能由例如`C#`和`CoffeeScript`来实现；参见[现有技术][prior-art]。

---

> 可选链堆

`Optional Chain`是`Optional Chaining`运算符，后跟一系列`属性`访问，`方法`或`函数`调用。即`可选链`之后可以跟随另一个`可选链`。

```typescript
a?.b[3].c?.(x).d
// a == null ? undefined : a.b[3].c == null ? undefined : a.b[3].c(x).d
// (as always, except that `a` and `a.b[3].c` are evaluated only once)
```

---

> 边缘限制

使用`括号运算符`可以限制了`短路`的判断范围。

```typescript
(a?.b).c
// (a == null ? undefined : a.b).c

```

因为先对`a`和`null`进行匹配检查。

- 如果，`a`为`null`，则直接短路返回`undefined`。
- 如果，`a`不为`null`，但`a.b`为空，则直接短路返回`undefined`，后续即访问`undefined`的`c`，就会抛出一个`TypeError`错误。

---

> 可选链 && 删除

`可选链`可以和`delete`运算符，用于限制`delete`运算符直接操作的内容范围。

```typescript
delete a?.b
// a == null ? true : delete a.b

```

---

### 不支持

尽管可以出于完整性考虑将它们包括在内，但由于缺乏实际用例或其他令人信服的原因，因此不支持以下内容

> 有关讨论，请参见[第22期][issues 22]和[第54期][issues 54]：

- 可选结构： `new a?.()`
- 可选模板文字： `a?.\`string\``
- 构造函数或模板文字中/任选的链后：`new a?.b()`，`a?.b\`string\``

> 尽管以下内容有一些用例，但不支持以下内容：参见[第18期][issues 18]的讨论：

- 可选的属性分配： `a?.b = c`

> 至少在实践中不支持以下内容，因为它没有多大意义，参见[问题＃4（评论）][issues 4]：

- 可选父类继承：`super?.()`，`super?.foo`。
- 任何类似于属性访问或函数调用的内容，`new?.target`和`import?.('foo')`等不包含在内。

语法或静态语义将禁止上述所有情况，以便稍后添加支持。

---

### 常见问题

> obj?.[expr]和func?.(arg)看起来很丑陋。为什么不使用obj?[expr]和func?(arg)一样<语言X>？

我们不使用`obj?[expr]`和 `func?(arg)`语法，因为解析器很难有效地将这些形式，与条件运算符（例如`obj?[expr].filter(fun)`和`func?(x - 2) + 3`区分开。

这两种情况的替代语法各有其缺陷。而决定哪一个看上去最糟则主要是个人品味的问题。这是我们做出选择的方式：

- 选择最适合这种`obj?.prop`情况的语法，这种语法最常出现
- 将可识别`?.`的字符序列的使用扩展到其他情况：`obj?.[expr]`，`func?.(arg)`。

至于`<language X>`，它具有与`JavaScript`不同的语法约束，这是因为`<X不支持某些构造或X中的工作方式不同>`。

> 为什么`(null)?.b`会默认返回`undefined`，而不是`null`？

`a?.b`是`a`变量下是否有`b`属性，如果没有找到对应的`b`属性，则返回`undefined`，即因为`a.b = undefined`，所以，`a?.b = undefined`。

特别是，该值`null`被认为没有属性。因此，`(null)?.b`是返回`undefined`。

> 为什么`foo?.()`，会在在foo为`undefined` 或者 `null`时，抛出错误？

想象一下一个库，它将`onChange`在用户提供处理程序时调用它，例如。如果用户提供的是数字`3`而不是函数，则该库很可能会抛出错误的用法并通知用户。这正是所建议的语义要`onChange?.()`实现的。

此外，这确保了`?.`在所有情况下都具有一致的含义，无需在我们进行检查的特殊情况下，需要先执行`typeof foo === 'function'`，再执行后续值有效性校验，只需全面检查`foo == null`即可。

最后，请记住，可选链接不是[错误抑制机制][error-suppression]。

可选操作只会检测，对应的值是`undefined` 或者 `null`，不会捕获或抑制通过评估周围的代码引发的错误。

```typescript
(function () {
    "use strict"
    undeclared_var?.b    // ReferenceError: undeclared_var is not defined
    arguments?.callee    // TypeError: 'callee' may not be accessed in strict mode
    arguments.callee?.() // TypeError: 'callee' may not be accessed in strict mode
    true?.()             // TypeError: true is not a function
})()

```

> 更多疑问请查看[optional-chaining QA][optional-chaining QA]

---

## 空合并运算符(nullish-coalescing)

### 前因说明

执行属性访问时，通常希望提供`默认值`，如果该属性访问的结果为`null`或`undefined`。

当前，在`JavaScript`中表达此意图的典型方法是使用`||`运算符。

```typescript
const response = {
  settings: {
    nullValue: null,
    height: 400,
    animationDuration: 0,
    headerText: '',
    showSplashScreen: false
  }
};

const undefinedValue = response.settings.undefinedValue || 'some other default'; // result: 'some other default'
const nullValue = response.settings.nullValue || 'some other default'; // result: 'some other default'
```

这在`null`和`undefined`值的常见情况下效果很好，但是有一些虚假的值可能会产生令人惊讶的结果：

```typescript
const headerText = response.settings.headerText || 'Hello, world!'; // Potentially unintended. '' is falsy, result: 'Hello, world!'
const animationDuration = response.settings.animationDuration || 300; // Potentially unintended. 0 is falsy, result: 300
const showSplashScreen = response.settings.showSplashScreen || true; // Potentially unintended. false is falsy, result: true

```

[无效合并运算符][nullish-coalescing]旨在更好地处理这些情况，并用作对无效值（`null`或`undefined`）的相等性检查。

### 语法

主要使用`??`运算符，如果左侧的表达式值为`undefined`或`null`的时候，则返回其右侧表达式值。

```typescript
const response = {
  settings: {
    nullValue: null,
    height: 400,
    animationDuration: 0,
    headerText: '',
    showSplashScreen: false
  }
};

const undefinedValue = response.settings.undefinedValue ?? 'some other default'; // 'some other default'
const nullValue = response.settings.nullValue ?? 'some other default'; // 'some other default'
const headerText = response.settings.headerText ?? 'Hello, world!'; // ''
const animationDuration = response.settings.animationDuration ?? 300; // 0
const showSplashScreen = response.settings.showSplashScreen ?? true; // false
```

---

## 装饰器(decorator)

### 基础介绍

设计模式中有种模式是`装饰者模式`，`decorator`的实现和这个设计模式很相近，主要做一些`非侵入式`的能力扩展。

在 `ES6` 之前，`装饰器`可能并没有那么重要，因为你只需要加一层 `wrapper` 就好了，但是现在，由于语法糖 `class` 的出现，它们不支持`类`所需的一些常见行为，这个时候想要去在多个`类`之间共享或者扩展一些`方法`的时候，代码会变得错综复杂，难以维护，而这，也正式我们 `decorator` 的用武之地。

注意目前[decorators][proposal-decorators]，还处于第二阶段中，之后语法可能会变化，注意及时关注[decorators-github][decorators-github]查看最新进度。

如果需要开启 `decorator`这项`experimental`支持,需要手动开启`experimentalDecorators`选项。

```sh
tsc --target ES5 --experimentalDecorators
tsconfig.json:
```

或者修改`tsconfig.json`配置。

```json
{
  "compilerOptions": {
    "target": "ES5",
    "experimentalDecorators": true
  }
}
```

---

### 原理

`decorator`其实是一个语法糖，背后通过拦截`es5`的`Object.defineProperty(target,name,descriptor)`进行装饰功能，详细了解`Object.defineProperty`可以查看[MDN文档][Object.defineProperty]。

```typescript
class Melon(){
  @readonly
  name
}

```

在属性上的修饰符，会在`Object.defineProperty`为`Melon`原型上注册对应属性之前，执行以下代码。

```typescript
let descriptor = {
  value:specifiedFunction,
  enumerable:false,
  configurable:true,
  writeable:true
};

descriptor = readonly(Melon.prototype,'name',descriptor) || descriptor;
Object.defineProperty(Melon.prototype,'name',descriptor);
```

从上面的伪代码我们可以看出，`decorator`只是在`Object.defineProperty`为`Melon.prototype`注册`属性`之前，执行了一个`装饰函数`，属于一个类对`Object.defineProperty`的拦截。

也就是说在利用`decorator`进行能力扩展时，主要是根据`装饰`的`目标`不同，取到对应的`target`,`name`,和`descriptor`实参，做相关修改和扩展。

> target

要在其上定义属性的对象。

> name

要定义或修改的属性的名称。

> descriptor中主要有`configurable`,`enumerable`,`value`,`writable`,`get`,和`set`属性可配置

| 属性名       | 属性描述                                                                                                                                                                                                 |
|:-------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| configurable | 通用配置项，当且仅当该属性的 `configurable` 为 `true` 时，该属性描述符才能够被改变，同时该属性也能从对应的对象上被删除。默认为 `false`。                                                                                       |
| enumerable   | 通用配置项，当且仅当该属性的`enumerable`为`true`时，该属性才能够出现在对象的枚举属性中。默认为 `false`。                                                                                                                      |
| value        | 数据描述专有配置项，该属性对应的值。可以是任何有效的 `JavaScript` 值（数值，对象，函数等）。默认为 `undefined`。                                                                                                                        |
| `writable`     | 数据描述专有配置项，当且仅当该属性的`writable`为`true`时，`value`才能被赋值运算符改变。默认为 `false`。                                                                                                                                |
| get          | 数据存取专有配置项，一个给属性提供 `getter` 的方法，如果没有 `getter` 则为 `undefined`。当访问该属性时，该方法会被执行，方法执行时没有参数传入，但是会传入`this`对象（由于继承关系，这里的`this`并不一定是定义该属性的对象）。默认为 `undefined`。 |
| set          | 数据存取专有配置项，一个给属性提供 `setter` 的方法，如果没有 `setter` 则为 `undefined`。当属性值修改时，触发执行该方法。该方法将接受唯一参数，即该属性新的参数值。默认为 `undefined`。                                                      |

`descriptor`中主要分两种形式来做`数据描述符`和`存取描述符`。

- 通过`Object.defineProperty`方法做具体属性值定义，称为`数据描述符`行为，比如`装饰器`中对于`类属性`的复写，就是属于这种情况。
- 通过`Object.defineProperty`方法做属性的访问器相关定义，称为`存取描述符`行为，比如`装饰器`中对于`类整体`和`类方法`的复写，就是属于这种情况。vue 2.x之前的版本数据双向观测联动也是通过这个来实现的。

|            | configurable | enumerable | value | writable | get | set |
|:-----------|:-------------|:-----------|:------|:---------|:----|:----|
| 数据描述符 | Yes          | Yes        | Yes   | Yes      | No  | No  |
| 存取描述符 | Yes          | Yes        | No    | No       | Yes | Yes |

---

### 语法说明

`decorator`是一种特殊种类的声明，可被附连到一个`类声明`，`方法`，`访问器`，`属性`，或`参数`。

装饰器使用形式是`@expression`，在其中`expression`必须是一个函数，该函数将在运行时使用有关修饰声明的信息来调用。

在一行上使用多个装饰器。

```text
@f @g x
```

在多行上使用多个装饰器。

```text
@f
@g
x
```

当多个修饰符应用于一个声明时，它们的求值类似于数学中的函数组成。

上述代码中，是将`f` 和`g`两个方法进行能力组合, 组合结果是 `f(g(x))`。

因此，在`TypeScript`中的单个声明上评估多个装饰器时，将执行以下步骤：

- 从顶部到底部评估每个装饰器的表达式。
- 然后将结果从下到上称为函数。

```typescript
const { log } = console;

function f() {
    log("f(): evaluated");
    return function (target, propertyKey: string, descriptor: PropertyDescriptor) {
        log(target, propertyKey, "f(): called");
    };
}

function g() {
    log("g(): evaluated");
    return function (target, propertyKey: string, descriptor: PropertyDescriptor) {
        log(target, propertyKey, "g(): called");
    };
}

class C {
    @f()
    @g()
    method() { }
}

const testObj: C = new C();
testObj.method();

// f(): evaluated
// g(): evaluated
// {},  'method',  g(): called
// {},  'method',  f(): called
```

---

### 代码示例

`decorator`是一种特殊种类的声明，可被附连到一个`类声明`，`类方法`，`类访问器`，`类属性`，或`类方法参数`。

装饰器使用形式是`@expression`，在其中`expression`必须是一个函数，该函数将在运行时使用有关修饰声明的信息来调用。

> class declaration && decorator

在类声明之前声明一个`类装饰器`。

`类装饰器`应用于类的`构造函数`，可用于`观察`，`修改`或`替换`类定义。

不能在`声明文件`，或任何其他环境上下文（例如，在`declare`类中）中使用`类装饰器`。

`类装饰器`的表达式将在运行时作为函数调用，装饰类的`构造函数`为其唯一参数。

下面是一个有关如何重写`构造函数`的示例。

```typescript
function classDecorator<T extends { new (...args: any[]): {} }>(
  constructor: T
) {
  return class extends constructor {
    newProperty = "new property";
    hello = "override";
  };
}

@classDecorator
class Greeter {
  property = "property";
  hello: string;
  constructor(m: string) {
    this.hello = m;
  }
}

console.log(new Greeter("world"));

```

如果，`类装饰器`返回一个值，它将用提供的`构造函数`替换类声明。

注意，如果是选择返回新的`构造函数`，则必须注意维护`原始原型`，运行时应用装饰器的逻辑不会为执行此操作。

---

> property declaration && decorator

`属性装饰器`是在属性声明之前声明。

`属性装饰器`不能在`声明文件`，`重载`或任何其他环境上下文（例如在`declare`类中）中使用。

属性装饰器的表达式将在运行时作为函数调用，并带有以下两个参数：

- 静态成员的类的构造函数或实例成员的类的原型。
- 成员的名称。

注意，由于在`TypeScript`中未初始化`属性装饰器`，因此未将`属性描述符`作为`参数`提供给`属性装饰器`。

这是因为当前在定义原型成员时没有描述`实例属性`的机制，也没有`观察`或`修改`该属性的`初始化程序`的方法。

`返回值`也将被忽略。因此，`属性装饰器`只能用于`观察`已为类声明了`特定名称`的`属性`。

可以使用此信息来记录有关该属性的`元数据`，`@format`装饰器和`getFormat`函数应用于`Greeter`类上方法的`属性装饰器`的示例。

```typescript
import "reflect-metadata";

const formatMetadataKey = Symbol("format");

function format(formatString: string) {
  return Reflect.metadata(formatMetadataKey, formatString);
}

function getFormat(target: any, propertyKey: string) {
  return Reflect.getMetadata(formatMetadataKey, target, propertyKey);
}

class Greeter {
  @format("Hello, %s")
  greeting: string;

  constructor(message: string) {
    this.greeting = message;
  }
  greet() {
    let formatString = getFormat(this, "greeting");
    return formatString.replace("%s", this.greeting);
  }
}
```

当`@format("Hello, %s")`被调用时，通过`Reflect.metadata`函数，增加了使用属性元数据条目。

当`getFormat`被调用时，它读取的格式中的`元数据值`。

注意此示例需要`reflect-metadata`库。有关库的更多信息，请参见元数据[reflect-metadata][reflect-metadata]。

---

> method declaration && decorator

在`方法`声明之前声明`方法装饰器`。`装饰器`将应用于方法的属性描述符，并可用于观察，修改或替换方法定义。

`方法装饰器`不能在`声明文件`，`重载`或任何其他环境上下文（例如在`declare`类中）中使用。

`方法装饰器`的表达式将在运行时作为函数调用，并带有以下三个参数

- 静态成员的类的构造函数或实例成员的类的原型。
- 成员的名称。
- 成员的属性描述符。

注：如果你的`js`编译版本低于是小于`ES5`，属性描述将会是`undefined`。

如果`方法装饰器`返回一个值，它将用作`方法`的`属性描述符`。

以下是`@enumerable`应用于`Greeter`类上方法的`方法装饰器`的示例

```typescript
const { log } = console;

function enumerable(value: boolean) {
  return function(
    target: any,
    propertyKey: string,
    descriptor: PropertyDescriptor
  ) {
    descriptor.enumerable = value;
  };
}

class Greeter {
  greeting: string;
  constructor(message: string) {
    this.greeting = message;
  }

  @enumerable(false)
  greet() {
    return "Hello, " + this.greeting;
  }
}

const testObj: Greeter = new Greeter('melon');
log(testObj.greet());// Hello, melon
log(testObj.greeting);// melon
```

我们可以`@enumerable`使用以下函数声明来定义`方法装饰器`，当`@enumerable(false)`装饰器被调用时，它修改`enumerable`的属性描述符的属性。

---

> accessor declaration && decorator

在`访问器`声明之前，就声明了一个`访问器装饰器`。`访问器修饰符`应用于`访问器`的属性描述符，可用于`观察`，`修改`或`替换访问器`的定义。

`访问装饰器`不能在`声明文件`，`重载`或任何其他环境上下文（例如在`declare`类中）中使用。

注意，`TypeScript`不允许装饰单个成员的`get`和`set`访问器。而是，该成员的所有装饰器必须应用于按文档顺序指定的第一个访问器。

这是因为`访问器装饰器`适用于`Property Descriptor`，它组合了`get`和`set`访问器，而不是分别合并每个声明。

`访问器装饰器`的表达式将在运行时作为函数调用，并带有以下三个参数

- 静态成员的类的构造函数或实例成员的类的原型。
- 成员的名称。
- 成员的属性描述符。

注：如果你的`js`编译版本低于是小于`ES5`，属性描述将会是`undefined`。

如果`访问器装饰器`返回一个值，它将用作`成员`的`属性描述符`。

以下是`@configurable`应用于`Point`类成员的访问器修饰符的示例

```typescript
const { log } = console;

function configurable(value: boolean) {
  return function(
    target: any,
    propertyKey: string,
    descriptor: PropertyDescriptor
  ) {
    descriptor.configurable = value;
  };
}

class Point {
  private _x: number;
  private _y: number;
  constructor(x: number, y: number) {
    this._x = x;
    this._y = y;
  }

  @configurable(false)
  get x() {
    return this._x;
  }

  @configurable(false)
  get y() {
    return this._y;
  }
}

const testObj: Point = new Point(3,4);
log(testObj.x);// 3
log(testObj.y);// 4

testObj.x = 4;// Cannot assign to 'x' because it is a read-only property.
testObj.y = 5;// Cannot assign to 'y' because it is a read-only property.

```

---

> parameter declaration && decorator

在`参数`声明之前声明`参数装饰器`。`参数装饰器`应用于类构造函数，或方法声明的函数。

`参数装饰器`不能在`声明文件`，`重载`或任何其他环境上下文（例如在`declare`类中）中使用。

`参数装饰器`的表达式将在运行时作为函数调用，并带有以下三个参数

- 静态成员的类的构造函数或实例成员的类的原型。
- 成员的名称。
- 函数的参数列表中参数的序号索引。

注意，`参数装饰器`只能用于观察已在方法上声明了的`参数`，`参数装饰器`的返回值将被忽略。

以下是`@required`应用于`Greeter`类上方法的`参数装饰器`的示例

```typescript
import "reflect-metadata";

const requiredMetadataKey = Symbol("required");

function required(
  target: Object,
  propertyKey: string | symbol,
  parameterIndex: number
) {
  let existingRequiredParameters: number[] =
    Reflect.getOwnMetadata(requiredMetadataKey, target, propertyKey) || [];
  existingRequiredParameters.push(parameterIndex);
  Reflect.defineMetadata(
    requiredMetadataKey,
    existingRequiredParameters,
    target,
    propertyKey
  );
}

function validate(
  target: any,
  propertyName: string,
  descriptor: TypedPropertyDescriptor<Function>
) {
  let method = descriptor.value;
  descriptor.value = function() {
    let requiredParameters: number[] = Reflect.getOwnMetadata(
      requiredMetadataKey,
      target,
      propertyName
    );
    if (requiredParameters) {
      for (let parameterIndex of requiredParameters) {
        if (
          parameterIndex >= arguments.length ||
          arguments[parameterIndex] === undefined
        ) {
          throw new Error("Missing required argument.");
        }
      }
    }

    return method.apply(this, arguments);
  };
}

class Greeter {
  greeting: string;

  constructor(message: string) {
    this.greeting = message;
  }

  @validate
  greet(@required name: string) {
    return "Hello " + name + ", " + this.greeting;
  }
}
```

该`@required`装饰器用于标记该参数的`元数据条目`。然后，`@validate`装饰器是将现有`greet`方法包装在一个函数中，该函数在调用原始方法之前先验证`参数`。

注意此示例需要`reflect-metadata`库。有关库的更多信息，请参见元数据[reflect-metadata][reflect-metadata]。

---

### 注意事项

因为存在函数提升，这边目前`装饰器`更多是应用于修改一个`类声明`，`类方法`，`类访问器`，`类属性`，或`类方法参数`中，不能直接装饰具体`方法`。

```typescript
let counter = 0;

const add = function () {
  counter++;
};

@add
function foo() {
}
```

上面的代码，意图是执行后`counter`等于 `1`，但是实际上结果是`counter`等于 `0`。因为`函数提升`，使得实际执行的代码是下面这样。

```typescript
@add
function foo() {
}

let counter;
let add;

counter = 0;

add = function () {
  counter++;
};

```

下面是另一个例子。

```typescript
const readOnly = require("some-decorator");

@readOnly
function foo() {
}
```

上面代码也有问题，因为实际执行是下面这样。

```typescript
let readOnly;

@readOnly
function foo() {
}

readOnly = require("some-decorator");
```

总之，由于存在函数提升，使得`装饰器`不能用于函数。类是不会存在`函数提升`的，所以就没有这方面的问题。

> 另一方面，如果一定要装饰函数，可以采用高阶函数的形式直接执行。

```typescript
function doSomething(name) {
  console.log('Hello, ' + name);
}

function loggingDecorator(wrapped) {
  return function() {
    console.log('Starting');
    const result = wrapped.apply(this, arguments);
    console.log('Finished');
    return result;
  }
}

const wrapped = loggingDecorator(doSomething);
```

---

## mixins


---

## DOM Manipulation

### 基础介绍

自标准化以来的20多年来，`JavaScript`已经走了很长一段路。尽管在2020年，`JavaScript`可以在`服务器`，`数据科学`甚至`IoT设备`上使用，但更多的场景还是在Web浏览器。

网站由`HTML`或`XML`文档组成。这些文档是静态的，它们不会更改。document对象模型（`DOM`）是`浏览器`，为了方便操作静态页面提供的编程接口。

很多相关UI库这边都是[DOM API][HTMLElement 通用规范]可用于更改文档结构，样式和内容，实现相关操作。

`TypeScript`是`JavaScript`的类型化超集，它附带`DOM API`的类型定义。这些定义可以在任何默认的`TypeScript`项目中轻松获得，在`TypeScript`可以通过`HTMLElement`类型进行`DOM`相关操作。

在此处探索`DOM`类型定义的源代码：[HTMLElement 实现][HTMLElement 实现]。

---

### 代码案例

下面示例，如何在`TypeScript`中，将`<p>Hello, World</p>`元素添加到`#app`元素中。

```typescript
// 1. Select the div element using the id property
const app = document.getElementById("app");

// 2. Create a new <p></p> element programmatically
const p = document.createElement("p");

// 3. Add the text content
p.textContent = "Hello, World!";

// 4. Append the p element to the div element
app?.appendChild(p);
```

编译并运行`index.html`页面后，生成的`HTML`将为

```html
<div id="app">
  <p>Hello, World!</p>
</div>
```

---

### 接口介绍

`TypeScript`代码的第一行使用全局变量`document`，检查该变量是否显示它是由`lib.dom.d.ts`文件中的`Document`接口定义的。

> Document.getElementById

向其传递元素`ID`字符串，如果能够在页面上找到对应元素，将返回`HTMLElement`，如果找不到，则会返回`null`。

```typescript
getElementById(elementId: string): HTMLElement | null;
```

它充当所有其他元素接口的基础接口，例如，`p`代码示例中变量的`类型`为`HTMLParagraphElement`。

因为，该方法`返回值`无法在运行前确定，所以，可以结合`可选链`运算符来调用`HTMLElement`接口。

---

> Document.createElement

向其传递元素任何内容`string`，将返回标准`HTMLElement`。开发人员可以通过这个接口是创建唯一的`HTML`元素标签。

例如

- `document.createElement('a')`，那么它将是`type`的元素`HTMLAnchorElement`。
- `document.createElement('xyz')`返回一个`<xyz></xyz>`元素，显然不是`HTML`规范指定的元素。

```typescript
createElement<K extends keyof HTMLElementTagNameMap>(tagName: K, options?: ElementCreationOptions): HTMLElementTagNameMap[K];
createElement(tagName: string, options?: ElementCreationOptions): HTMLElement;
```

上面是一个重载的函数定义。第二个重载是最简单的，并且与该`getElementById`方法非常相似。

对于`createElement`的第一个定义，它使用了一些高级通用模式，最好将其分解为多块。

- 从通用表达式开始：`<K extends keyof HTMLElementTagNameMap>`。该表达式定义了一个通用参数`K`，该参数被限制在接口的键上`HTMLElementTagNameMap`。

`HTMLElementTagNameMap`映射接口包含每个指定的`HTML`标记名称及其对应的类型接口，例如，这是前5个映射值：

```typescript
interface HTMLElementTagNameMap {
    "a": HTMLAnchorElement;
    "abbr": HTMLElement;
    "address": HTMLElement;
    "applet": HTMLAppletElement;
    "area": HTMLAreaElement;
        ...
}

```

有些元素不具有唯一的属性，因此它们只是返回`HTMLElement`，而其他类型的确具有唯一的属性和方法，因此它们返回其特定的接口（将从扩展或实现`HTMLElement`）。

- 现在，在`createElement`定义的其余部分：`(tagName: K, options?: ElementCreationOptions): HTMLElementTagNameMap[K]`。

第一个参数`tagName`定义为通用参数`K`。`TypeScript`解释器足够聪明，可以从此参数推断出通用参数。这意味着开发人员在使用该方法时实际上不必指定泛型参数。传递给`tagName`参数的任何值都将被推断为`K`，因此可以在定义的其余部分中使用。

返回值`HTMLElementTagNameMap[K]`接受`tagName`参数，并使用它返回相应的`类型`。

---

> Node.appendChild

具体`HTMLElement`对象执行`appendChild`方法，向其传递元素任何内容`string`，将返回标准`HTMLElement`。

```typescript
appendChild<T extends Node>(newChild: T): T;
```

此方法的工作方式与从`createElement`参数`T`推断出通用参数的方法类似`newChild`。`T`被限制在另一个基本接口上`Node`。

---

> querySelector && querySelectorAll

这两种方法都是获取适合更多唯一约束的`dom`元素列表的出色工具。它们在`lib.dom.d.ts`中定义为

```typescript
/**
 * Returns the first element that is a descendant of node that matches selectors.
 */
querySelector<K extends keyof HTMLElementTagNameMap>(selectors: K): HTMLElementTagNameMap[K] | null;
querySelector<K extends keyof SVGElementTagNameMap>(selectors: K): SVGElementTagNameMap[K] | null;
querySelector<E extends Element = Element>(selectors: string): E | null;

/**
 * Returns all element descendants of node that match selectors.
 */
querySelectorAll<K extends keyof HTMLElementTagNameMap>(selectors: K): NodeListOf<HTMLElementTagNameMap[K]>;
querySelectorAll<K extends keyof SVGElementTagNameMap>(selectors: K): NodeListOf<SVGElementTagNameMap[K]>;
querySelectorAll<E extends Element = Element>(selectors: string): NodeListOf<E>;
```

该`querySelectorAll`定义类似于`getElementByTagName`，但它返回一个新类型：`NodeListOf`。

此返回类型本质上是标准`JavaScript list`元素的自定义实现。可以说，替换`NodeListOf<E>`为`E[]`会带来非常相似的用户体验。

`NodeListOf`只有实现了以下属性和方法：`length`，`item(index)`，`forEach((value, key, parent) => void)`，和`数字索引`。

注意，此方法返回`元素列表`，而不是`节点列表`，这是`NodeList`是取的`Node.childNodes`属性。

要查看实际使用的这些方法，请将现有代码修改为：

```html
<ul>
  <li>First :)</li>
  <li>Second!</li>
  <li>Third times a charm.</li>
</ul>;

const first = document.querySelector("li"); // returns the first li element
const all = document.querySelectorAll("li"); // returns the list of all li elements
```

---

## 类型断言

有时候你会遇到这样的情况，你会比`TypeScript`更了解某个值的详细信息。

通常这会发生在你清楚地知道一个`实体`具有比它现有类型更确切的`类型`。

通过`类型断言`这种方式可以告诉编译器，"相信我，我知道自己在干什么"。

`类型断言`好比其它语言里的`类型转换`，或者类型装箱，但是不进行特殊的`数据检查`和`解构`。

它没有运行时的影响，只是在`编译阶段`起作用。 `TypeScript`会假设你已经进行了必须的检查。

`类型断言`有两种形式。

> 其一是"尖括号"语法：

```typescript
const someValue: any = "this is a string";

const strLength: number = (<string>someValue).length;

// 当然直接写也是可以的。
const strLength2: number = someValue.length;// 16

```

> 另一个为as语法：

```typescript
const someValue: any = "this is a string";

const strLength: number = (someValue as string).length;
```

两种形式是等价的。 至于使用哪个大多数情况下是凭个人喜好；然而，当你在`TypeScript`里使用`JSX`时，只有`as`语法断言是被允许的。

---

## 类型校验


---

## 泛型


---

## 高级类型


---


## Utility Types


---

[proposal-decorators]: https://tc39.es/proposal-decorators/
[decorators-github]: https://github.com/tc39/proposal-decorators
[Object.defineProperty]: https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/defineProperty
[HTMLElement 实现]: https://github.com/microsoft/TypeScript/blob/master/lib/lib.dom.d.ts
[HTMLElement 通用规范]:https://developer.mozilla.org/zh-CN/docs/Web/API/HTMLElement
[DOM]: https://developer.mozilla.org/zh-CN/docs/Web/API/Document_Object_Model/Introduction
[optional-chaining]: https://github.com/tc39/proposal-optional-chaining
[stage 4]: https://github.com/tc39/proposals/blob/master/finished-proposals.md
[optional-chaining QA]: https://github.com/tc39/proposal-optional-chaining#faq
[error-suppression]: https://github.com/tc39/proposal-optional-chaining#is-this-error-suppression
[issues 18]: https://github.com/tc39/proposal-optional-chaining/issues/18
[issues 22]: https://github.com/tc39/proposal-optional-chaining/issues/22
[issues 54]: https://github.com/tc39/proposal-optional-chaining/issues/54
[issues 4]: https://github.com/tc39/proposal-optional-chaining/issues/4
[nullish-coalescing]: https://github.com/tc39/proposal-nullish-coalescing
[prior-art]: https://github.com/tc39/proposal-optional-chaining#prior-art
[reflect-metadata]: https://www.typescriptlang.org/v2/docs/handbook/decorators.html#metadata
