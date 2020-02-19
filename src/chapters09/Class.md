# Class

## 基础介绍

传统的`JavaScript`使用函数和基于原型的继承来构建可重用的组件，但是对于程序员来说，使用`类`继承功能，并且对象是从这些`类`中构建对象的面向对象的方法可能感到有点尴尬。

从`ECMAScript 2015`（也称为`ECMAScript 6`）开始，`JavaScript`程序员将能够使用这种面向对象的基于`类`的方法来构建其应用程序。

在`TypeScript`中，我们允许开发人员现在使用这些技术，并将其编译为可在所有主要浏览器和平台上使用的`JavaScript`，而不必等待下一个`JavaScript`版本。

---

## 声明

### new

类必须使用`new`调用，否则会报错。这是它跟普通构造函数的一个主要区别，后者不用`new`也可以执行。

```typescript
class Foo {
  constructor() {
    return Object.create(null);
  }
}

Foo();
// Value of type 'typeof Foo' is not callable. Did you mean to include 'new'?
```

---

### constructor

`constructor`方法是类的默认方法，通过`new`命令生成对象实例时，自动调用该方法。

一个类必须有`constructor`方法，如果没有`a`，一个空的`constructor`方法会被默认添加。

```typescript
const { log } = console;
class Foo {
  constructor() {
    return Object.create(null);
  }
}

log(new Foo() instanceof Foo);
// false
```

`constructor`方法默认返回`初始化`完毕，对应的实例对象（即`this`），当然可以指定返回其他一个`对象`。

上面代码中，`constructor`函数返回一个全新的`对象`，结果导致实例对象不是`Foo`类的实例。

---

### this

当我们引用`Class`中的某个`成员`时，我们会将其前置`this.`，这表示它是成员`访问权限`。

```typescript
class Greeter {
  greeting: string;
  constructor(message: string) {
    this.greeting = message;
  }
  greet() {
    return `Hello, ${this.greeting}!`;
  }
}
```

---

### 属性声明

不使用任何`修饰符`，进行`属性`声明的时候，一般会默认`属性`状态为`public`，即在`Class`内部可以通过`this`读取，`实例对象`也可以通过`字面量`的形式进行访问。

> 基础声明

不使用任何`修饰符`，直接通过`{{propName}}: {{propType}}`进行声明。同时在`constructor`方法中添加`属性`值的赋值。

```typescript
const { log } = console;
class Greeter {
    greeting: string;
    constructor(message: string) {
        this.greeting = message;
    }
}

const hello = new Greeter('watermelon');
log(`Hello, ${hello.greeting}!`);// Hello, watermelon!
```

请注意，如果在`constructor`方法中未添加`属性`值的赋值，这边就会抛出错误。

```typescript
class Greeter {
    greeting: string;// Property 'greeting' has no initializer and is not definitely assigned in the constructor.
}
```

---

> 赋值声明

不使用任何`修饰符`，直接通过`{{propName}}: {{propType}} = {{defaultValue}}`进行声明，即在`属性声明`的同时，添加相关`默认值`。

```typescript
const { log } = console;
class Greeter {
    greeting: string = 'watermelon';
}
const hello = new Greeter();
log(`Hello, ${hello.greeting}!`);// Hello, watermelon!
```

请注意，这个时候在`constructor`方法中未添加`属性`值的赋值，这边不会抛出错误。

---

> 特殊声明

使用`TypeScript 3.8`，`TypeScript`支持JavaScript中`专用字段`的新语法

```typescript
const { log } = console;
class Greeter {
    #greeting: string = 'watermelon';
    greet() {
      return `Hello, ${this.#greeting}!`;
    }
}
const hello = new Greeter();
log(hello.greet());// Hello, watermelon!
log(hello.#greeting);// Property '#greeting' is not accessible outside class 'Greeter' because it has a private identifier.
```

该语法内置在`JavaScript`运行时中，可以更好地保证每个`私有字段`的隔离，和`private`修饰符的作用比较像。

目前只在TS`3.8.0-beta`版中，才可以使用。

---

### 方法声明

> 基础声明

由于`ES6`中对象声明的优化，所以，在`Class`中声明方法从原本的`{{functionName}}:function(){}`变为现在的`{{functionName}}(){}`。

```typescript
const { log } = console;
class Greeter {
    greet() {
      log(`Hello, watermelon!`);
    }
}
const hello = new Greeter();
hello.greet();// Hello, watermelon!

```

---

> 带形参的声明

基本的语法是`{{functionName}}({{paramName}}: {{paramType}}){}`，形参相关声明和在`普通`的`function`中声明形参一样。

```typescript
const { log } = console;
class Greeter {
    greet(message: string) {
      log(`Hello, ${message}!`);
    }
}
const hello = new Greeter();
hello.greet('watermelon');// Hello, watermelon!
```

---

> 带返回值的声明

基本的语法是`{{functionName}}(): {{returnType}}{}`，返回值相关声明和在`普通`的`function`中声明返回值一样。

```typescript
const { log } = console;
class Greeter {
    greet(): string{
      return 'Hello, watermelon!';
    }
}
const hello = new Greeter();
log(hello.greet());// Hello, watermelon!
```

---

### 相关修饰符

请注意下述四个修饰符，在`Class`中`属性`和`方法`声明中都可以同理使用。

> 静态修饰符---static

`static`关键词用于创建类的`静态成员`，这些`静态成员`在类`本身`而不是`实例`上可见。

在`Class`内部和外部，都可以通过`{{className}}.`对静态成员进行访问。

```typescript
const { log } = console;
class Greeter {
    static greeting: string = 'melon';
    static greet(message?: string) {
      log(`Hello, ${message || Greeter.greeting}!`);
    }
    greetEx(message: string){
      Greeter.greet(message);
    }
}
Greeter.greet();// Hello, melon!
log(Greeter.greeting);// melon

const hello = new Greeter();
hello.greetEx('watermelon');// Hello, watermelon!
```

---

> 只读修饰符---readonly

`readonly`关键字将`属性`设置为`只读`。请注意这个`修饰符`不能用于`方法`声明。

`只读属性`必须在其声明或构造函数中进行`初始化`。

注意有且仅有在`constructor`方法的形参声明中，可以使用`readonly`关键词。

```typescript
const { log } = console;
class Greeter {
    readonly greeting: string = 'melon';
    constructor(readonly name: string) {
      log(`Hey, ${name}!`);
    }
    greet(message?: string) {
      log(`Hello, ${message || this.greeting}!`);
    }
}

const hello = new Greeter('watermelon');// Hey, watermelon! 
hello.greet();// Hello, melon!
log(hello.greeting);// melon

hello.greeting = 'watermelon';// Cannot assign to 'greeting' because it is a read-only property.

```

如果未在其声明或构造函数中进行`初始化`，这边会抛出错误。

```typescript
class Greeter {
    readonly greeting: string;
    // Property 'greeting' has no initializer and is not definitely assigned in the constructor.
}

```

如果在实例方法的形参声明中，使用`readonly`关键词，这边会抛出错误。

```typescript
const { log } = console;
class Greeter {
    // A parameter property is only allowed in a constructor implementation.
    greet(readonly message: string) {
      log(`Hello, ${message}!`);
    }
}

```

---

> 保护修饰符---protected

`protected`修饰符的行为很像`private`，不同的是，声明成员设置为`protected`之后，在`派生类`也中进行访问，但是，设置为`private`之后，在`派生类`中不可以进行访问。

注意有且仅有在`constructor`方法的形参声明中，可以使用`protected`关键词。

```typescript
const { log } = console;
class Greeter {
    protected greeting: string = 'melon';
    constructor(protected name: string) {
      log(`Hey, ${name}!`);
    }
    protected greet(message?: string) {
      log(`Hello, ${message || this.greeting}!`);
    }
}

class Bird extends Greeter{
    constructor(name: string) {
      super(name);
      log(`Hey, ${this.greeting}!`);
    }
}

const hello = new Greeter('watermelon');// Hey, watermelon!
hello.greet();// Property 'greet' is protected and only accessible within class 'Greeter' and its subclasses.
log(hello.greeting);// Property 'greet' is protected and only accessible within class 'Greeter' and its subclasses.

hello.greeting = 'watermelon';// Property 'greeting' is protected and only accessible within class 'Greeter' and its subclasses.

const littleBird = new Bird('melonHero');
// Hey, melonHero!
// Hey, melon!

```

也可以标记构造函数`protected`。这意味着该类不能在其包含的类之外`实例化`，但是可以`扩展`。

```typescript
const { log } = console;
class Greeter {
    protected constructor(name: string) {
      log(`Hey, ${name}!`);
    }
}

class Bird extends Greeter{
    constructor(name: string) {
      super(name);
    }
}

const littleBird = new Bird('melonHero');// Hey, melonHero!

const hello = new Greeter('watermelon');
// Constructor of class 'Greeter' is protected and only accessible within the class declaration.

```

如果在实例方法的形参声明中，使用`protected`关键词，这边会抛出错误。

```typescript
const { log } = console;
class Greeter {
    // A parameter property is only allowed in a constructor implementation.
    greet(protected message: string) {
      log(`Hello, ${message}!`);
    }
}

```

要使两种实例对象进行兼容赋值，如果其中一种具有`protected`成员，则另一种必须具有完全一样个数的`protected`成员，否则就会赋值不成功。

```typescript
const { log } = console;
class Greeter {
    protected greeting: string = 'melon';
    constructor(protected name: string) {
      log(`Hey, ${name}!`);
    }
    protected greet(message?: string) {
      log(`Hello, ${message || this.greeting}!`);
    }
}

class Bird extends Greeter{
    constructor(name: string) {
      super(name);
      log(`Hey, ${this.greeting}!`);
    }
}

class Cat{
    constructor(name: string) {
      log(`Hey, ${name}!`);
    }
}
let hello = new Greeter('watermelon');// Hey, watermelon!
let littleBird = new Bird('melonHero');
let littleCat = new Cat('melonHero');

hello = littleBird;
hello = littleCat;// Type 'Cat' is missing the following properties from type 'Greeter': greeting, name, greet

```

---

> 私有修饰符---private

`private`关键字将`属性`设置为`私有`，无法从其包含类的`外部`进行访问。

```typescript
const { log } = console;
class Greeter {
    private greeting: string = 'melon';
    constructor(private name: string) {
      log(`Hey, ${name}!`);
    }
    private greet(message?: string) {
      log(`Hello, ${message || this.greeting}!`);
    }
}

const hello = new Greeter('watermelon');// Hey, watermelon!
hello.greet();// Property 'greet' is private and only accessible within class 'Greeter'.
log(hello.greeting);// Property 'greet' is private and only accessible within class 'Greeter'.

```

要使两种实例对象进行兼容赋值，如果其中一种具有`private`成员，则另一种必须具有完全一样个数的`private`成员，否则就会赋值不成功。

```typescript
const { log } = console;
class Greeter {
    private greeting: string = 'melon';
    constructor(private name: string) {
      log(`Hey, ${name}!`);
    }
    private greet(message?: string) {
      log(`Hello, ${message || this.greeting}!`);
    }
}

class Cat{
    private greeting: string = 'melon';
    constructor(name: string) {
      log(`Hey, ${name}!`);
    }
}
let hello = new Greeter('watermelon');// Hey, watermelon!
let littleCat = new Cat('melonHero');

hello = littleCat;// Type 'Cat' is missing the following properties from type 'Greeter': name, greet
```

---

> 公有修饰符---public

在`TypeScript`中，默认情况下每个成员都是`public`状态，当然还是可以，通过`public`修饰符的明确标记成员为`公开成员`。

```typescript
const { log } = console;
class Greeter {
    public greeting: string = 'melon';
    public constructor(public name: string) {
      log(`Hey, ${name}!`);
    }
    public greet(message?: string) {
      log(`Hello, ${message || this.greeting}!`);
    }
}

const hello = new Greeter('watermelon');// Hey, watermelon! 
hello.greet();// Hello, melon!
log(hello.greeting);// melon
```

---

### getter&setter

`TypeScript`支持`getters`/`setter`方法，以拦截对`对象成员`的访问，这让我们可以更好地控制如何在每个对象上访问成员，强制执行一些约束。

```typescript
const { log } = console;
class Greeter {
    private _greeting: string = 'melon';
    private fullGreetMaxLength:number = 10
    greet(message?: string) {
      log(`Hello, ${message || this._greeting}!`);
    }
    get greeting(): string {
        log('now is getting greeting attr');
        return this._greeting;
    }

    set greeting(newGreet: string) {
        if (newGreet && newGreet.length > this.fullGreetMaxLength) {
            throw new Error("greet has a max length of " + this.fullGreetMaxLength);
        }
        this._greeting = newGreet;
    }
}

const hello = new Greeter();
log(hello.greeting);// now is getting greeting attr 
// melon

hello.greeting = 'watermelon!!!!!!!!!!!!!!!!!!!!!!!';// greet has a max length of 10

```

如上述示例，在`greeting`属性获取和设置的时候，这边都做了相关的挟持，并且在属性赋值的时候做了长度校验。

案例中属性赋值超过了限制长度，则这边进行了错误抛出。

> 注意事项

首先，访问器要求您将编译器设置为输出`ECMAScript 5`或更高版本。不支持降级为`ECMAScript 3`。

其次，一个属性如果只写了get处理，没有写set处理，这边会自动推断该属性为readonly属性。

`.d.ts`从代码中生成文件时，这很有用，因为这边编辑器会直接提示该属性无法修改。

---

### 抽象类声明

> 基础说明

`抽象类`是可以从中派生其他类的基类。它们可能无法直接实例化。与接口不同，抽象类可能包含其成员的实现详细信息。

该`abstract`关键字用于抽象类中定义`抽象类`以及`抽象方法`。

```typescript
const { log } = console;
abstract class Melon {
    abstract makeSound(): void;
    move(): void {
        log("roaming the earth...");
    }
}

const hello = new Greeter();
log(hello.greeting);// now is getting greeting attr 
// melon

hello.greeting = 'watermelon!!!!!!!!!!!!!!!!!!!!!!!';// greet has a max length of 10

```

抽象类中标记为抽象的方法不包含实现，必须在派生类中实现。抽象方法与接口方法具有相似的语法。两者都定义了方法的签名，而没有包括方法主体。但是，抽象方法必须包含abstract关键字，并且可以选择包含访问修饰符。

---

> 抽象类和接口对比

- 相同点：

1、都是抽象类型。
2、都可以有实现方法；
3、都可以不需要实现类或者继承者去实现所有方法。
4、抽象类和接口都不能直接实例化，如果要实例化，抽象类变量必须指向实现所有抽象方法的子类对象，接口变量必须指向实现所有接口方法的类对象。
5、抽象类里的抽象方法必须全部被子类所实现，如果子类不能全部实现父类抽象方法，那么该子类只能是抽象类。同样，一个类实现接口的时候，如不能全部实现接口方法，那么该类也只能为抽象类。（以前是）

- 不同点：

1、抽象类不可以多重继承，接口可以（接口和抽象类相比，最大的区别就在于子类上，接口的子类可以同时实现多个接口，但抽象类的子类只能实现单根继）。
2、抽象类要被子类继承，接口要被类实现。
3、接口里定义的变量只能是公共的静态的常量，抽象类中的变量是普通变量
4、抽象方法只能申明，不能实现，接口是设计的结果 ，抽象类是重构的结果

- 总结

抽象类里可以没有抽象方法
如果一个类里有抽象方法，那么这个类只能是抽象类
抽象方法要被实现，所以不能是静态的，也不能是私有的。

---