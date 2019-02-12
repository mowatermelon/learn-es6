
# 标签模板

## 入门案例

`模板字符串`的功能，不仅仅是上面这些。它可以紧跟在一个函数名后面，该函数将被调用来处理这个`模板字符串`。这被称为`标签模板`功能（`tagged template`）。

```javascript
(function (log, S) {
    log `123`;// [ '123' ]
    // 等同于
    log(123);// 123
})(console.log, String)
```

`标签模板`其实不是`模板`，而是`函数调用`的一种`特殊形式`。`标签`指的就是`函数`，紧跟在后面的`模板字符串`就是它的`参数`。

---

## 使用变量

但是，如果`模板字符`里面有`变量`，就不是简单的`调用`了，而是会将`模板字符串`先处理成多个`参数`，再调用`函数`。

```javascript
let a = 5;
let b = 10;

tag`Hello ${ a + b } world ${ a * b }`;
// 等同于
tag(['Hello ', ' world ', ''], 15, 50);
```

上面代码中，模板字符串前面有一个标识名`tag`，它是一个`函数`。

---

## 返回值说明

整个`表达式`的`返回值`，就是`tag`函数处理`模板字符串`后的`返回值`。

函数`tag`依次会接收到多个`参数`。

```javascript

function tag(stringArr, value1, value2){
  // ...
}

// 等同于

function tag(stringArr, ...values){
  // ...
}
```

`tag`函数的第一个`参数`是一个`数组`，该`数组`的`成员`是`模板字符串`中那些没有`变量替换`的部分。

也就是说，`变量替换`只发生在`数组`的第一个`成员`与第二个`成员`之间、第二个`成员`与第三个`成员`之间，以此类推。

---

## 其他参数说明

`tag`函数的其他`参数`，都是`模板字符串`各个变量被`替换`后的`值`。

由于本例中，模板字符串含有两个变量，因此`tag`会接受到`value1`和`value2`两个参数。

`tag`函数所有`参数`的`实际值`如下。

| 参数顺序标识 | 参数值说明                |
| ------------ | ------------------------- |
| 第一个参数   | ['Hello ', ' world ', ''] |
| 第二个参数   | 15                        |
| 第三个参数   | 50                        |

也就是说，`tag`函数实际上以下面的形式调用。

```javascript
tag(['Hello ', ' world ', ''], 15, 50)
```

---

## 自定义使用

我们可以按照需要编写`tag`函数的代码。下面是`tag`函数的一种写法，以及运行结果。

```javascript

let a = 5;
let b = 10;

function tag(s, v1, v2) {
  console.log(s[0]);
  console.log(s[1]);
  console.log(s[2]);
  console.log(v1);
  console.log(v2);

  return "OK";
}

tag`Hello ${ a + b } world ${ a * b}`;
// "Hello "
// " world "
// ""
// 15
// 50
// "OK"
```

---

## 复杂使用

下面是一个更`复杂`的例子。

```javascript

let total = 30;
let msg = passthru`The total is ${total} (${total*1.05} with tax)`;

function passthru(literals) {
  let result = '';
  let i = 0;

  while (i < literals.length) {
    result += literals[i++];
    if (i < arguments.length) {
      result += arguments[i];
    }
  }

  return result;
}

msg // "The total is 30 (31.5 with tax)"
```

上面这个例子展示了，如何将各个参数按照原来的位置拼合回去。

---

## 结合rest参数

`passthru`函数采用 `rest` 参数的写法如下。

```javascript

function passthru(literals, ...values) {
  let output = "";
  let index;
  for (index = 0; index < values.length; index++) {
    output += literals[index] + values[index];
  }

  output += literals[index]
  return output;
}
```

---

## 过滤 HTML 字符串

`标签模板`的一个重要应用，就是过滤 `HTML` 字符串，防止用户输入恶意内容。

```javascript

let message =
  SaferHTML`<p>${sender} has sent you a message.</p>`;

function SaferHTML(templateData) {
  let s = templateData[0];
  for (let i = 1; i < arguments.length; i++) {
    let arg = String(arguments[i]);

    // Escape special characters in the substitution.
    s += arg.replace(/&/g, "&amp;")
            .replace(/</g, "&lt;")
            .replace(/>/g, "&gt;");

    // Don't escape special characters in the template.
    s += templateData[i];
  }
  return s;
}
```

上面代码中，`sender`变量往往是用户提供的，经过`SaferHTML`函数处理，里面的特殊字符都会被转义。

```javascript

let sender = '<script>alert("abc")</script>'; // 恶意代码
let message = SaferHTML`<p>${sender} has sent you a message.</p>`;

message
// <p>&lt;script&gt;alert("abc")&lt;/script&gt; has sent you a message.</p>
```

---

## 多语言转换

标签模板的另一个应用，就是多语言转换（国际化处理）。

```javascript

i18n`Welcome to ${siteName}, you are visitor number ${visitorNumber}!`

// "欢迎访问xxx，您是第xxxx位访问者！"
```

模板字符串本身并不能取代 `Mustache` 之类的模板库，因为没有`条件判断`和`循环`处理功能，但是通过`标签函数`，你可以自己添加这些功能。

```javascript

// 下面的hashTemplate函数
// 是一个自定义的模板处理函数
let libraryHtml = hashTemplate`
  <ul>
    #for book in ${myBooks}
      <li><i>#{book.title}</i> by #{book.author}</li>
    #end
  </ul>
`;
```

---

## 嵌入其他语言

除此之外，你甚至可以使用`标签模板`，在 `JavaScript` 语言之中嵌入`其他语言`。

```javascript

jsx`
  <div>
    <input
      ref='input'
      onChange='${this.handleChange}'
      defaultValue='${this.state.value}' />
      ${this.state.value}
   </div>
`
```

上面的代码通过`jsx`函数，将一个 `DOM` 字符串转为 `React`对象。你可以在 `GitHub` 找到`jsx`函数的具体实现。

---

## 运行Java代码

下面则是一个假想的例子，通过`java`函数，在 `JavaScript` 代码之中运行 `Java` 代码。

```javascript
java`
class HelloWorldApp {
  public static void main(String[] args) {
    System.out.println("Hello World!"); // Display the string.
  }
}
`
HelloWorldApp.main();
```

---

## 第一个参数raw属性

`模板处理函数`的第一个`参数`（`模板字符串`数组），还有一个`raw`属性。

```javascript

console.log`123`
// ["123", raw: Array[1]]
```

上面代码中，`console.log`接受的参数，实际上是一个`数组`。

该`数组`有一个`raw属性`，保存的是`转义`后的`原字符串`。

请看下面的例子。

```javascript

tag`First line\nSecond line`

function tag(strings) {
  console.log(strings.raw[0]);
  // strings.raw[0] 为 "First line\\nSecond line"
  // 打印输出 "First line\nSecond line"
}
```

上面代码中，`tag`函数的第一个参数`strings`，有一个`raw`属性，也指向一个`数组`。

该`数组`的`成员`与`strings数组`完全一致。

比如，`strings数组`是`["First line\nSecond line"]`，那么`strings.raw`数组就是`["First line\\nSecond line"]`。

两者唯一的区别，就是字符串里面的`斜杠`都被`转义`了。

比如，`strings.raw` 数组会将`\n`视为`\\`和`n`两个`字符`，而不是`换行符`。

这是为了方便取得`转义`之前的`原始模板`而`设计`的。

---