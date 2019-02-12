# 通过Traceur使用ES6

`Google`公司的`Traceur`编译器 ，可以将`ES6`代码编译为`ES5`代码，`https//github.com/google/traceur-compiler`。

## Node 环境的用法

`Traceur` 的 `Node` 用法如下（假定已安装`traceur`模块）。

```javascript
var traceur = require('traceur');
var fs = require('fs');

// 将 ES6 脚本转为字符串
var contents = fs.readFileSync('es6-file.js').toString();

var result = traceur.compile(contents, {
  filename: 'es6-file.js',
  sourceMap: true,
  // 其他设置
  modules: 'commonjs'
});

if (result.error)
  throw result.error;

// result 对象的 js 属性就是转换后的 ES5 代码
fs.writeFileSync('out.js', result.js);
// sourceMap 属性对应 map 文件
fs.writeFileSync('out.js.map', result.sourceMap);
```

---

## 命令行转换

作为命令行工具使用时，`Traceur` 是一个 `Node` 的模块，首先需要用 `npm` 安装。

```sh
$ npm install -g traceur
```

安装成功后，就可以在命令行下使用 `Traceur` 了。

`Traceur` 直接运行 `ES6` 脚本文件，会在标准输出显示运行结果，以前面的`calc.js`为例。

```sh
$ traceur calc.js
Calc constructor
9
```

如果要将 `ES6` 脚本转为 `ES5` 保存，要采用下面的写法。

```sh
$ traceur --script calc.es6.js --out calc.es5.js
```

上面代码的`--script`选项表示指定输入文件，`--out`选项表示指定输出文件。

为了防止有些特性编译不成功，最好加上`--experimental`选项。

```sh
$ traceur --script calc.es6.js --out calc.es5.js --experimental
```

命令行下转换生成的文件，就可以直接放到浏览器中运行。

  ---

## 浏览器环境

`Traceur` 允许将 `ES6` 代码直接插入网页。首先，必须在网页头部加载 `Traceur` 库文件。

```javascript
<script src="https://google.github.io/traceur-compiler/bin/traceur.js"></script>
<script src="https://google.github.io/traceur-compiler/bin/BrowserSystem.js"></script>
<script src="https://google.github.io/traceur-compiler/src/bootstrap.js"></script>
<script type="module">
  import './Greeter.js';
</script>
```

上面代码中，一共有 `4` 个`script`标签。第一个是加载 `Traceur` 的`库文件`，第二个和第三个是将这个库文件用于`浏览器环境`，第四个则是`加载用户脚本`，这个脚本里面可以使用 `ES6` 代码。

注意，第四个`script`标签的`type`属性的值是`module`，而不是`text/javascript`。这是 `Traceur` 编译器识别 `ES6` 代码的标志，编译器会自动将所有`type=module`的代码编译为 `ES5`，然后再交给浏览器执行。

除了引用外部 `ES6` 脚本，也可以直接在网页中放置 `ES6` 代码。

```javascript
<script type="module">
  class Calc {
    constructor() {
      console.log('Calc constructor');
    }
    add(a, b) {
      return a + b;
    }
  }

  var c = new Calc();
  console.log(c.add(4,5));
</script>
```

正常情况下，上面代码会在控制台打印出`9`。

如果想对 `Traceur` 的行为有精确控制，可以采用下面参数配置的写法。

```javascript
<script>
  // Create the System object
  window.System = new traceur.runtime.BrowserTraceurLoader();
  // Set some experimental options
  var metadata = {
    traceurOptions: {
      experimental: true,
      properTailCalls: true,
      symbols: true,
      arrayComprehension: true,
      asyncFunctions: true,
      asyncGenerators: exponentiation,
      forOn: true,
      generatorComprehension: true
    }
  };
  // Load your module
  System.import('./myModule.js', {metadata: metadata}).catch(function(ex) {
    console.error('Import failed', ex.stack || ex);
  });
</script>
```

上面代码中，首先生成 `Traceur` 的全局对象`window.System`，然后`System.import`方法可以用来加载 `ES6`。加载的时候，需要传入一个配置对象`metadata`，该对象的`traceurOptions`属性可以配置支持 ES6 功能。如果设为`experimental: true`，就表示除了 `ES6` 以外，还支持一些实验性的新功能。

  ---

## 在线转换

`Traceur` 也提供一个[在线编译器](http://google.github.io/traceur-compiler/demo/repl.html)，可以在线将 `ES6` 代码转为 `ES5` 代码。转换后的代码，可以直接作为 `ES5` 代码插入网页运行。

![image](https://user-images.githubusercontent.com/18508817/52532829-bac61000-2d65-11e9-90f4-83c0de61b2fa.png)

上面的例子转为 `ES5` 代码运行，就是下面这个样子。

```javascript
<script src="https://google.github.io/traceur-compiler/bin/traceur.js"></script>
<script src="https://google.github.io/traceur-compiler/bin/BrowserSystem.js"></script>
<script src="https://google.github.io/traceur-compiler/src/bootstrap.js"></script>
<script>
$traceurRuntime.ModuleStore.getAnonymousModule(function() {
  "use strict";
  (function() {
    var $--0;
    var a = [1, 2, 3, 45, 5, 6];
    ($--0 = console).log.apply($--0, $traceurRuntime.spread(a));
  });
  return {};
});
</script>
```

---

