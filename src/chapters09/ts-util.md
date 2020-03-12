
# 装饰器


---

# mixins


---


# 类型断言

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

# 类型校验


---

# 泛型


---

# 高级类型


---


# Utility Types


---
