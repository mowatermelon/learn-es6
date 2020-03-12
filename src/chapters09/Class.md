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

请注意下述五个修饰符，在`Class`中`属性`和`方法`声明中都可以同理使用。

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

抽象类中标记为抽象的方法不包含实现，必须在派生类中实现。抽象方法与接口方法具有相似的语法。两者都定义了方法的签名，而没有包括方法主体。但是，抽象方法必须包含`abstract`关键字，并且可以选择包含访问修饰符。

---

## 抽象类和接口对比

### 基础说明

> 接口和抽象类声明的区别

```typescript
interface iDemo {
    name: string;
    age: number;
    hello(name: string): string;
}

abstract class aDemo {
    #name: string = 'melon';
    abstract hello(name: string): string;
    greet():string{
        return 'hello~~~~~~~';
    }
    get sex(): string {
        return 'women'
    }
    set sex(value) {
        const DEFALUT_VALUE_ARR: string[] = ['man', 'woman'];
        DEFALUT_VALUE_ARR.indexOf(value) > -1 && (this.sex = value);
    }
}

const test01:iDemo = {name:'111',age:11,hello:()=> 'hello'};

const test02:aDemo = {sex:'woman',hello:()=> 'hello',greet:()=> 'greet'};
// Property '#name' is missing in type '{ sex: string; hello: () => string; greet: () => string; }' but required in type 'aDemo'.

const test03:aDemo = {'name':'melon',sex:'woman',hello:()=> 'hello',greet:()=> 'greet'};
// Type '{ name: string; sex: string; hello: () => string; greet: () => string; }' is not assignable to type 'aDemo'.
//   Object literal may only specify known properties, and ''name'' does not exist in type 'aDemo'.

const test04:aDemo = {'#name':'melon',sex:'woman',hello:()=> 'hello',greet:()=> 'greet'};
// Type '{ '#name': string; sex: string; hello: () => string; greet: () => string; }' is not assignable to type 'aDemo'.
//   Object literal may only specify known properties, and ''#name'' does not exist in type 'aDemo'.

// Cannot create an instance of an abstract class.
const test05:aDemo = new aDemo();

// 'iDemo' only refers to a type, but is being used as a value here.
const test06:aDemo = new iDemo();
```

可以看到

- `接口`中只能声明字段(`name`,`age`)，而`抽象类`中可以声明具有访问器的属性(`sex`)。
- `接口`中字段的声明不能使用(`public`,`private(#)`,`static`,`readonly`和`protected`),而`抽象类`中声明属性是可以使用修饰符的。
- `接口`中声明方法是完全不可以包括具体实现，但是`抽象类`中抽象方法不可以包括具体实现，但是普通方法还是可以写具体实现的。
- `接口`和 `抽象类`都是不可以直接被实例化的
- `接口`和 `抽象类`在用于变量的类型声明之后，变量的赋值中需要包括`接口`和 `抽象类`的所有属性和方法。

---

> 接口和抽象类的多重继承区别

```typescript
interface test01 {
}
interface test02 {
}
class test03 {

}
class test04 {

}

abstract class aDemo {
}
class iGreeter1 implements test01,test02 {
}

// Classes can only extend a single class.
class oGreeter1 extends test03,test04 {
}
// Classes can only extend a single class.
class oGreeter2 extends aDemo,test01 {
}

```

`implements`支持多重继承接口，`extends`只支持单类继承。

---

> 接口和抽象类继承属性的区别

```typescript

interface iDemo {
    name: string;
    age: number;
}

abstract class aDemo {
    #name: string = 'melon';
    abstract sex:string = 'women';

}
class iGreeter implements iDemo {
    // Class 'iGreeter' incorrectly implements interface 'iDemo'.
    // Type 'iGreeter' is missing the following properties from type 'iDemo': name, age
    hello(name: string, age: string): string {
        return `hello ${age} ${name || this.name} `;
        // Property 'name' does not exist on type 'iGreeter'.
    }
}
class oGreeter extends aDemo {
    // Non-abstract class 'oGreeter' does not implement inherited abstract member 'sex' from class 'aDemo'.
    hello(name: string, age: string): string {
        return `hello ${age} ${name || this.name} `;
        //Property 'name' does not exist on type 'oGreeter'. Did you mean '#name'?
    }
    greet():string{
        return `hello ${this.sex} ${name || this.#name} `;
        //Property '#name' is not accessible outside class 'aDemo' because it has a private identifier.
    }
}
```

可以看到

- 类继承`接口`，`接口`中的字段一定要在类中声明的，否则这边就会报错，说明`missing the following properties`。
- 类继承`抽象类`，`抽象类`中声明的普通属性，类中可以不用强制声明，但是在`抽象类`中声明的抽象属性，类中必须要进行声明。
- 类在继承`接口`和`抽象类`中属性的时候，类型声明都需要和原有保持一致，否则就会报错`not assignable to the same property in base type`。
- 类在继承`抽象类`的时候，如果没有按照规范实现属性声明，但是在类方法中访问对应的抽象属性，不会报错，但是如果是继承`接口`，未按照`接口`字段继承配置，在类方法中访问对应的属性，是会抛出错误的。

---

> 接口和抽象类声明方法的区别

```typescript
const { log } = console;
interface iDemo {
    hello(name: string): string;
    greet(name: string):void;
}
class iGreeter1 implements iDemo {
    //Class 'iGreeter1' incorrectly implements interface 'iDemo'.
    //Type 'iGreeter1' is missing the following properties from type 'iDemo': hello, greet
    helloEx(name: string, age: string) {
    }
    greetEx():string{
        return `hello melon~~~`;
    }
}
class iGreeter2 implements iDemo {
    // Property 'hello' in type 'iGreeter' is not assignable to the same property in base type 'iDemo'.
    // Type '(name: string, age: string) => void' is not assignable to type '(name: string) => string'.
    hello(name: string, age: string) {
    }
    greetEx():string{
        return `hello ${this.greet()} melon~~~`;
    }
}
class iGreeter3 implements iDemo {
    hello(name: string): string{
        return `hello ${name}`;
    }
    greet():string{
        return `hello melon~~~`;
    }
}
```

可以看到类在继承`接口`的时候

- `接口`中的方法不必要全部都实现，但是如果一个方法都不实现，这边会报错。
- `接口`中的方法如果规定了形参个数和类型，类在继承实现的时候，需要严格按照规范实现，否则会报错。
- `接口`中的方法如果未规定返回值类型，或者规定的返回类型为`void`，则在继承实现的时候，可以修改形参个数，类型和返回值类型。

```typescript
const { log } = console;
abstract class aDemo {
    abstract hello(name: string): string;
    greet(name: string):void{
        log(`hello`);
    }

}
class oGreeter1 extends aDemo {
    // Non-abstract class 'oGreeter' does not implement inherited abstract member 'hello' from class 'aDemo'.
    helloEx(name: string, age: string){
    }
    greetEx():string{
        return `hello ${this.greet()} melon~~~`;
    }
}
class oGreeter2 extends aDemo {
    // Property 'hello' in type 'oGreeter' is not assignable to the same property in base type 'aDemo'.
    // Type '(name: string, age: string) => void' is not assignable to type '(name: string) => string'.
    hello(name: string, age: string){
    }
    greetEx():string{
        return `hello ${this.greet()} melon~~~`;
    }
}
class oGreeter3 extends aDemo {
    hello(name: string): string{
        return `hello ${name}`;
    }
    greet():string{
        return `hello melon~~~`;
    }
}
```

可以看到类在继承`抽象类`的时候

- `抽象类`中的方法不必要全部都实现，但是抽象方法必须实现，否则这边会报错。
- `抽象类`中的方法如果规定了形参个数和类型，类在继承实现的时候，需要严格按照规范实现，否则会报错。
- `抽象类`中的方法如果未规定返回值类型，或者规定的返回类型为`void`，则在继承实现的时候，可以修改形参个数，类型和返回值类型。
- `抽象类`里可以没有`抽象方法`
- 如果一个类里有`抽象方法`，那么这个类只能是`抽象类`
- `抽象方法`要被实现，所以不能是静态的，也不能是私有的。

---

### 总结

> 相同点：

- 都是抽象类型。
- 都可以有实现方法。
- 都可以不需要实现类或者继承者去实现所有方法。
- `抽象类`和`接口`都不能直接通过`new`关键词来进行实例化。
- 如果变量类型来进行实例化，变量必须实现所有`接口`或者`抽象类`的属性和方法。
- 在继承`接口`和`抽象类`方法时，除了规定了返回值为`void`的方法可以进行自定义实现，其他继承的方法必须按照规范实现。

> 不同点：

- `接口`中只能声明没有访问器的字段，`抽象类`中可以声明有访问器的属性。
- `接口`中字段声明不可以使用相关`修饰符`，抽象类中属性声明可以，并且属性还可以赋予默认值。
- `接口`中的方法在继承的时候不会强制显示必须都要实现，但是`抽象类`中的抽象方法在继承的时候，必须都要实现。
- `抽象类`不可以多重继承，`接口`可以。

---
