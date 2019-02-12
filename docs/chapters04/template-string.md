
# 模板字符串

## 产生原因

传统的 `JavaScript` 语言，输出`模板`通常是这样写的（下面使用了 `jQuery` 的方法）。

```javascript
$('#result').append(
  'There are <b>' + basket.count + '</b> ' +
  'items in your basket, ' +
  '<em>' + basket.onSale +
  '</em> are on sale!'
);
```

---

## 优化结果

### 基础使用

之前写法相当`繁琐`不方便，`ES6` 引入了`模板字符串`解决这个`问题`。

```javascript
$('#result').append(`
  There are <b>${basket.count}</b> items
   in your basket, <em>${basket.onSale}</em>
  are on sale!
`);
```

`模板字符串（template string）`是`增加版`的`字符串`，用`反引号`作为`标识`，它可以当作`普通字符串`使用，也可以用来定义`多行字符串`。

使用`模板字符串`表示`多行字符串`，所有的`空格`和`缩进`都会被保留在`输出`之中。

```javascript
(function (log, S) {
    // 普通字符串
    log(`In JavaScript '\n' is a line-feed.`);
    // In JavaScript '
    // ' is a line-feed.

    // 多行字符串
    log(`In JavaScript this is
 not legal.`);
    // In JavaScript this is
    // not legal.

    log(`string text line 1
string text line 2`);
    // string text line 1
    // string text line 2

    $('#list').html(`
        <ul>
        <li>first</li>
        <li>second</li>
        </ul>
    `);
})(console.log, String)
```

---

### 使用反引号

`代码`中的`模板字符串`，都是用`反引号`表示。

如果在`模板字符串`中需要使用`反引号`，则前面要用`反斜杠`转义。

```javascript
(function (log, S) {
    let greeting = `\`Yo\` World!`;
    log(greeting);// `Yo` World!
})(console.log, String)
```

---

### 使用变量

`模板字符串`中嵌入`变量`，需要将`变量名`写在`${}`之中。

```javascript
(function (log, S) {
    // 字符串中嵌入变量
    let name = "Bob",
        time = "today";
    log(`Hello ${name}, how are you ${time}?`);
    // Hello Bob, how are you today?

    function authorize(user, action) {
        if (!user.hasPrivilege(action)) {
            throw new Error(
            // 传统写法为
            // 'User '
            // + user.name
            // + ' is not authorized to do '
            // + action
            // + '.'
            `User ${user.name} is not authorized to do ${action}.`);
        }
    }
})(console.log, String)
```

---

### 使用表达式

> 简单表达式

`模板字符串`中`大括号`内部可以放入任意的 `JavaScript 表达式`，可以进行`运算`，以及引用对象`属性`。

```javascript
(function (log, S) {
    let x = 1;
    let y = 2;

    log(`${x} + ${y} = ${x + y}`);
    // "1 + 2 = 3"

    log(`${x} + ${y * 2} = ${x + y * 2}`);
    // "1 + 4 = 5"

    let obj = {
        x: 1,
        y: 2
    };
    log(`${obj.x + obj.y}`);
    // "3"
})(console.log, String)
```

---

> 调用函数

`模板字符串`之中还能调用`函数`。

```javascript
(function (log, S) {
    function fn() {
        return "Hello World";
    }

    log(`foo ${fn()} bar`);
    // foo Hello World bar
})(console.log, String)
```

---

> 字符串变量

由于`模板字符串`的`大括号内部`，就是执行 `JavaScript 代码`，因此如果`大括号内部`是一个`字符串`，将会`原样输出`。

```javascript
(function (log, S) {
    let msg = `Hello ${'Melon'}`;
    log(msg);// Hello Melon
})(console.log, String)
```

---

> 需要时执行

如果需要引用`模板字符串`本身，在`需要`时`执行`，可以像下面这样写。

```javascript
(function (log, S) {
    // 写法一
    let str1 = 'return ' + '`Hello ${name}!`';
    let func1 = new Function('name', str1);
    log(func1('Melon')); // "Hello Melon!"

    // 写法二
    let str2 = '(name) => `Hello ${name}!`';
    let func2 = eval.call(null, str2);
    log(func2('Melon')); // "Hello Melon!"
})(console.log, String)
```

---

### 使用对象变量

如果`大括号`中的值不是`字符串`，将按照一般的`规则`转为`字符串`。

比如，`大括号`中是一个`对象`，将默认调用`对象`的`toString`方法。

```javascript
(function (log, S) {
    let a = [1,3,4];
    let o = {melon:1};
    let d = Reflect.construct(Date,[]);

    log(`${a} + ${o} = ${d}`);
    // 1,3,4 + [object Object] = Mon Feb 11 2019 23:45:23 GMT+0800 (GMT+08:00)

})(console.log, String)
```

---

### 使用未声明变量

如果`模板字符串`中的`变量`没有`声明`，将`报错`。

```javascript
(function (log, S) {
    // 变量place没有声明
    let msg = `Hello, ${place}`;// ReferenceError: place is not defined
})(console.log, String)
```

---

### 模板嵌套

`模板字符串`可以`嵌套`。

下面`tmpl`方法中，`模板字符串`的`变量`之中，又嵌入了另一个`模板字符串`。

```javascript
(function (log, S) {
    const tmpl = addrs => `
    <table>
    ${addrs.map(addr => `
      <tr><td>${addr.first}</td></tr>
      <tr><td>${addr.last}</td></tr>
    `).join('')}
    </table>
  `;

    const data = [{
            first: '<Jane>',
            last: 'Bond'
        },
        {
            first: 'Lars',
            last: '<Croft>'
        },
    ];

    log(tmpl(data));
    // <table>
    //
    //   <tr><td><Jane></td></tr>
    //   <tr><td>Bond</td></tr>
    //
    //   <tr><td>Lars</td></tr>
    //   <tr><td><Croft></td></tr>
    //
    // </table>
})(console.log, String)
```

---

## 模板编译

### 准备需要转换的模板

```javascript
const template = `
<ul>
  <% for(let i=0; i < data.supplies.length; i++) { %>
    <li><%= data.supplies[i] %></li>
  <% } %>
</ul>
`;
```

上面代码在`模板字符串`之中，放置了一个`常规模板`。

该模板使用`<%...%>`放置 `JavaScript` 代码，使用`<%= ... %>`输出 `JavaScript` 表达式。

---

### 之前转换的方法

将其转换为 `JavaScript 表达式`字符串。

```javascript
(function (log, S) {
    function tmpl(data) {
        const arr = [];
        arr.push('<ul>');
        for (let i = 0; i < data.supplies.length; i++) {
            arr.push('\n\t<li>');
            arr.push(data.supplies[i]);
            arr.push('</li>');
        };
        arr.push('\n</ul>');
        return arr.join('');
    }

    const data = {
        supplies: ["broom", "mop", "cleaner"]
    };
    log(tmpl(data));
    // <ul>
    //     <li>broom</li>
    //     <li>mop</li>
    //     <li>cleaner</li>
    // </ul>
})(console.log, String)
```

---

### 正则加echo和eval

使用`正则表达式`将模板中`<%= ... %>`转换为`echo`字符串拼接方法，然后再结合`模板字符串`，转译最后的编译方法`完整体`，最后再调用`eval`动态执行`函数编译`。

```javascript
(function (log, S) {
    function compile(template) {
        const evalExpr = /<%=(.+?)%>/g;
        const expr = /<%([\s\S]+?)%>/g;

        template = template.replace(evalExpr, '`); \n  echo( $1 ); \n  echo(`')

        template = template.replace(expr, '`); \n $1 \n  echo(`');

        template = 'echo(`' + template + '`);';

        let script =
            `(function parse(data){
          let output = "";

          function echo(html){
            output += html;
          }

          ${ template }

          return output;
        })`;

        return script;
    }

    const data = {
        supplies: ["broom", "mop", "cleaner"]
    };

    const temp = `
    <ul>
        <% for(let i=0; i < data.supplies.length; i++) { %>
            <li><%= data.supplies[i] %></li>
        <% } %>
    </ul>
    `;

    // let tmpl = new Function(temp,compile);
    const compileStr = compile(temp);
    log(compileStr);

    // (function parse(data) {
    //     let output = "";

    //     function echo(html) {
    //         output += html;
    //     }

    //     echo(`
    //         <ul>
    //             `);
    //     for (let i = 0; i < data.supplies.length; i++) {
    //         echo(`
    //                 <li>`);
    //         echo(data.supplies[i]);
    //         echo(`</li>
    //             `);
    //     }
    //     echo(`
    //         </ul>
    //         `);

    //     return output;
    // })

    let tmpl = eval(compileStr);
    log(tmpl(data));
    // <ul>
    //     <li>broom</li>
    //     <li>mop</li>
    //     <li>cleaner</li>
    // </ul>
})(console.log, String)
```

---

### 正则加数组和eval

使用`正则表达式`将模板中`<%= ... %>`转换为`arr.push`字符串拼接方法，然后再结合`模板字符串`，转译最后的编译方法`完整体`，最后再调用`eval`动态执行`函数编译`。

```javascript
(function (log, S) {
    function compile(template) {
        const evalExpr = /<%=(.+?)%>/g;
        const expr = /<%([\s\S]+?)%>/g;

        template = template.replace(evalExpr, '`); \n  arr.push( $1 ); \n  arr.push(`');
        // 替换模板字符串中的<%=...%>中的值  
        // 比如将 '<%= data.supplies[i] %>'
        // 转换为 '`);\n  arr.push( data.supplies[i]); \n  arr.push(`'

        template = template.replace(expr, '`); \n $1 \n  arr.push(`');
        // 替换模板字符串中的<%...%>中的值  
        // 比如将 '<% for(let i=0; i < data.supplies.length; i++) { %>'
        // 转换为 '`);\n  for(let i=0; i < data.supplies.length; i++)  \n  arr.push(`'

        template = 'arr.push(`' + template + '`);'; // 做最外层的包裹

        let script =
            `(function parse(data){
                const arr = [];

                ${ template }

                return arr.join('');
            })`;

        return script;
    }

    const data = {
        supplies: ["broom", "mop", "cleaner"]
    };

    const temp = `
    <ul>
        <% for(let i=0; i < data.supplies.length; i++) { %>
            <li><%= data.supplies[i] %></li>
        <% } %>
    </ul>
    `;

    // let tmpl = new Function(temp,compile);
    const compileStr = compile(temp);
    log(compileStr);

    // (function parse(data){
    //     const arr = [];

    //     arr.push(`
    //         <ul>
    //     `);
    //     for(let i=0; i < data.supplies.length; i++) {  
    //         arr.push(`
    //             <li>`);
    //         arr.push(  data.supplies[i]  );
    //         arr.push(`</li>
    //         `);
    //     }  
    //     arr.push(`
    //     </ul>
    // `);

    // return arr.join('');
    // })

    let tmpl = eval(compileStr);
    log(tmpl(data));
    // <ul>
    //     <li>broom</li>
    //     <li>mop</li>
    //     <li>cleaner</li>
    // </ul>
})(console.log, String)
```

---

## 模板字符串的限制

前面提到`标签模板`里面，可以内嵌其他`语言`。

但是，`模板字符串`默认会将`字符串转义`，导致无法嵌入`其他语言`。

举例来说，标签模板里面可以嵌入 `LaTEX` 语言。

```javascript

function latex(strings) {
  // ...
}

let document = latex`
\newcommand{\fun}{\textbf{Fun!}}  // 正常工作
\newcommand{\unicode}{\textbf{Unicode!}} // 报错
\newcommand{\xerxes}{\textbf{King!}} // 报错

Breve over the h goes \u{h}ere // 报错
`
```

上面代码中，变量`document`内嵌的`模板字符串`，对于 `LaTEX` 语言来说完全是`合法`的。

但是 `JavaScript` 引擎会报错。原因就在于`字符串`的`转义`。

`模板字符串`会将`\u00FF`和`\u{42}`当作 `Unicode` 字符进行转义，所以`\unicode`解析时报错；

而`\x56`会被当作`十六进制`字符串转义，所以`\xerxes`会报错。

也就是说，`\u`和`\x`在 `LaTEX` 里面有`特殊含义`，但是 `JavaScript` 将它们转义了。

为了解决这个问题，`ES2018` 放松了对`标签模板`里面的`字符串转义`的限制。

如果遇到`不合法`的`字符串转义`，就返回`undefined`，而不是报错，并且从`raw`属性上面可以得到`原始字符串`。

```javascript

function tag(strs) {
  strs[0] === undefined
  strs.raw[0] === "\\unicode and \\u{55}";
}
tag`\unicode and \u{55}`
```

上面代码中，`模板字符串`原本是应该报错的，但是由于`放松`了对`字符串转义`的`限制`，所以不报错了，`JavaScript` 引擎将第一个字符设置为`undefined`/

但是`raw`属性依然可以得到`原始字符串`，因此`tag`函数还是可以对`原字符串`进行处理。

注意，这种对`字符串转义`的`放松`，只在`标签模板解析`字符串时生效，不是`标签模板`的场合，依然会`报错`。

```javascript

let bad = `bad escape sequence: \unicode`; // 报错
```
