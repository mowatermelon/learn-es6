
# 数组(Array)

`TypeScript`像`JavaScript`一样可以操作数组元素。 

有两种方式可以定义数组。

第一种，可以在元素类型后面接上 `[]`，表示由此类型元素组成的一个数组：

```typescript
const list: number[] = [1, 2, 3];
```

第二种方式是使用数组泛型，`Array<元素类型>`：

```typescript
const list: Array<number> = [1, 2, 3];
```

---

# 元组(Tuple)

`元组`类型允许表示一个已知元素数量和`类型`的数组，各元素的类型不必相同。

比如，你可以定义一对值分别为 `string`和`number`类型的元组。

```typescript
// Declare a tuple type
let x: [string, number];
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

`object`表示非原始类型，也就是除`number`，`string`，`boolean`，`symbol`，`null`或`undefined`之外的类型。

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

# 枚举(enums)

## 基础介绍

使用`枚举`我们可以定义一些`带名字`的`常量`。 使用`枚举`可以清晰地表达意图或创建一组有区别的用例。

TypeScript支持`数字`的和基于`字符串`的`枚举`。[详细内容请看](./衍生类型学习-Enum.md)

---

# 接口(interface)

`TypeScript`的核心原则之一是`类型检查`的重点是值的形状。有时称为`鸭式打字`或`结构子类型化`。在`TypeScript`中，接口充当命名这些类型的角色，并且是定义代码内契约以及项目外代码契约的有效方法。

TypeScript支持接口用于类型检测和类继承。[详细内容请看](./衍生类型学习-interface.md)

---

# 类(Class)

传统的`JavaScript`使用函数和基于原型的继承来构建可重用的组件，但是对于程序员来说，使用`类`继承功能，并且对象是从这些`类`中构建对象的面向对象的方法可能感到有点尴尬。

从`ECMAScript 2015`（也称为`ECMAScript 6`）开始，`JavaScript`程序员将能够使用这种面向对象的基于`类`的方法来构建其应用程序。

在`TypeScript`中，我们允许开发人员现在使用这些技术，并将其编译为可在所有主要浏览器和平台上使用的`JavaScript`，而不必等待下一个`JavaScript`版本。

[详细内容请看](./衍生类型学习-Class.md)

---

# 方法(function)

## 声明函数

### function 关键词

```typescript


```

---

### 字面量基础赋值

```typescript


```

---

### 箭头函数赋值

```typescript


```

---
## 形参修饰符

### 形参类型

```typescript


```

---

### 可选形参

```typescript


```

---

### 形参默认值

```typescript


```

---

### 剩余形参

```typescript


```

---

## 函数返回值

### 无返回值

```typescript


```

---

### 返回基础类型

```typescript


```

---

### 返回自定义结构

```typescript


```

---

## 函数重载


```typescript


```

---

# 命名空间(namespace)

```typescript

namespace Validation {
    export interface StringValidator {
        isAcceptable(s: string): boolean;
    }

    const lettersRegexp = /^[A-Za-z]+$/;
    const numberRegexp = /^[0-9]+$/;

    export class LettersOnlyValidator implements StringValidator {
        isAcceptable(s: string) {
            return lettersRegexp.test(s);
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
validators["Letters only"] = new Validation.LettersOnlyValidator();

// Show whether each string passed each validator
for (const s of strings) {
    for (const name in validators) {
        console.log(`"${ s }" - ${ validators[name].isAcceptable(s) ? "matches" : "does not match" } ${ name }`);
    }
}

```

---
