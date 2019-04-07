# 基础前置知识

## ECMAScript的历史

1996年11月，`JavaScript`的创造者`Netscape`公司，决定将`JavaScript`提交给国际标准化组织`ECMA`，希望这种语言能够成为国际标准。次年，`ECMA`发布`262`号标准文件（`ECMA-262`）的`第一版`，规定了浏览器脚本语言的标准，并将这种语言成为`ECMAScript`。这个版本就是`ECMAScript 1.0`版。

1998年6月，`ECMAScript 2.0`版发布。

1999年12月，`ECMAScript 3.0` 版发布，成为`JavaScript`大的通行标准，得到了广泛支持。`3.0` 版是一个巨大的成功，在业界得到广泛支持，成为通行标准，奠定了 `JavaScript` 语言的基本语法，以后的版本完全继承。直到今天，初学者一开始学习 `JavaScript`，其实就是在学 `3.0` 版的语法。

2000 年，`ECMAScript 4.0` 开始酝酿。这个版本最后没有通过，但是它的大部分内容被 `ES6` 继承了。因此，`ES6` 制定的起点其实是 `2000` 年。

2007年10月，`ECMAScript 4.0` 版 草案发布，对`3.0`版做了大幅升级，原计划次年8月发布正式版本。然而在草案发布后，由于`4.0`版的目标过于激进，各方对于是否通过这个标准，产生了严重分歧。以`Yahoo`，`Microsoft`，`Google`为首的大公司，反对`JavaScript`的大幅升级，主张小幅改动，而以`JavaScript`创造者`Brendan Eich`为首的`Mozilla`公司，则坚持当前的草案。

2008年7月，由于对于 下一个版本应该包含哪些功能，各方面分歧太大，争论过于激进，`ECMA`开会决定，中止`ECMAScript 4.0`的开发，将其中设计现有功能改善的一小部分，发布为`ECMAScript 3.1`，二将其他激进的设想扩大范围，放入以后的版本，鉴于会议的气氛，该版本的项目代号取名为`Harmony`（和谐），会后不久，`ECMAScript 3.1`就改名为`ECMAScript 5`。

2009年12月，`ECMAScript 5.0`版正式发布。`Harmony`项目则一分为二，一些较为可行的设想定名为`JavaScript.next`继续开发，后来演变成`ECMAScript 6`，一些不是很成熟的设想，则被视为`JavaScript.next.next`，在更远的将来再考虑推出。

2011年6月，`ECMAScript 5.1`版本发布，并且成为`ISO`国际标准（`ISO/IEC 16262:2011`）。

2013年3月，`ECMAScript 6` 草案冻结，不再添加新功能，新功能设想将被放到 `ECMAScript 7`。

2013年12月，`ECMAScript 6` 草案发布，此后是12个月的讨论期，以听取各方反馈意见。

2015年6月，`ES6` 的第一个版本发布，正式名称就是`《ECMAScript 2015 标准》`（简称 `ES2015`）。

2016年6月，小幅修订的`《ECMAScript 2016 标准》`（简称 `ES2016`）如期发布。

---

## 与 JavaScript 的关系

一个常见的问题是，`ECMAScript` 和 `JavaScript` 到底是什么关系？

要讲清楚这个问题，需要回顾历史。1996 年 11 月，`JavaScript` 的创造者 `Netscape` 公司，决定将 `JavaScript` 提交给标准化组织 `ECMA`，希望这种语言能够成为国际标准。次年，`ECMA` 发布 `262` 号标准文件（`ECMA-262`）的第一版，规定了`浏览器脚本语言`的标准，并将这种语言称为 `ECMAScript`，这个版本就是 `1.0` 版。

该标准从一开始就是针对 `JavaScript` 语言制定的，但是之所以不叫 `JavaScript`，有两个原因。一是商标，Java 是 Sun 公司的商标，根据授权协议，只有 `Netscape` 公司可以合法地使用 `JavaScript` 这个名字，且 `JavaScript` 本身也已经被 `Netscape` 公司注册为商标。二是想体现这门语言的制定者是 `ECMA`，不是 `Netscape`，这样有利于保证这门语言的`开放性`和`中立性`。

因此，`ECMAScript` 和 `JavaScript` 的关系是，前者是后者的规格，后者是前者的一种实现（另外的 `ECMAScript` 方言还有 `JScript` 和 `ActionScript`）。日常场合，这两个词是可以互换的。

---

## 与 ES2015 的关系

`ECMAScript 2015`（简称 `ES2015`）这个词，也是经常可以看到的。它与 `ES6` 是什么关系呢？

`2011` 年，`ECMAScript 5.1` 版发布后，就开始制定 `6.0` 版了。因此，`ES6` 这个词的原意，就是指 `JavaScript` 语言的下一个版本。

但是，因为这个版本引入的语法功能太多，而且制定过程当中，还有很多组织和个人不断提交新功能。事情很快就变得清楚了，不可能在一个版本里面包括所有将要引入的功能。常规的做法是先发布 `6.0` 版，过一段时间再发 `6.1` 版，然后是 `6.2` 版、`6.3` 版等等。

但是，标准的制定者不想这样做。他们想让标准的升级成为常规流程：任何人在任何时候，都可以向标准委员会提交新语法的提案，然后标准委员会每个月开一次会，评估这些提案是否可以接受，需要哪些改进。

如果经过多次会议以后，一个提案足够成熟了，就可以正式进入标准了。这就是说，标准的版本升级成为了一个不断滚动的流程，每个月都会有变动。

标准委员会最终决定，标准在每年的 6 月份正式发布一次，作为当年的正式版本。

接下来的时间，就在这个版本的基础上做改动，直到下一年的 6 月份，草案就自然变成了新一年的版本。这样一来，就不需要以前的版本号了，只要用年份标记就可以了。

`ES6` 的第一个版本，就这样在 2015 年 6 月发布了，正式名称就是《`ECMAScript 2015` 标准》（简称 `ES2015`）。2016 年 6 月，小幅修订的《`ECMAScript 2016` 标准》（简称 `ES2016`）如期发布，这个版本可以看作是 `ES6.1` 版，因为两者的差异非常小（只新增了数组实例的includes方法和指数运算符），基本上是同一个标准。根据计划，`2017` 年 `6` 月发布 `ES2017` 标准。

因此，`ES6` 既是一个历史名词，也是一个泛指，含义是 `5.1` 版以后的 `JavaScript` 的下一代标准，涵盖了 `ES2015`、`ES2016`、`ES2017` 等等，而 `ES2015` 则是正式名称，特指该年发布的正式版本的语言标准。本书中提到 `ES6` 的地方，一般是指 `ES2015` 标准，但有时也是泛指`下一代 JavaScript 语言`。

---

## 语法提案的批准流程

任何人都可以向标准委员会（又称 `TC39` 委员会）提案，要求修改语言标准。

一种新的语法从提案到变成正式标准，需要经历五个阶段。每个阶段的变动都需要由 `TC39` 委员会批准。

更详细内容，请查看[官方文档说明](https://tc39.github.io/process-document/)。

- Stage 0 - Strawman（展示阶段）
- Stage 1 - Proposal（征求意见阶段）
- Stage 2 - Draft（草案阶段）
- Stage 3 - Candidate（候选人阶段）
- Stage 4 - Finished（定案阶段）

一个提案只要能进入 `Stage 2`，就差不多肯定会包括在以后的正式标准里面。`ECMAScript` 当前的所有提案，可以在 `TC39` 的官方网站`gitHub.com/tc39/ecma262`查看。

本书的写作目标之一，是跟踪 `ECMAScript` 语言的最新进展，介绍 `5.1` 版本以后所有的新语法。对于那些明确或很有希望，将要列入标准的新语法，都将予以介绍。

---

## ES6支持情况

各大浏览器的最新版本，对 `ES6` 的支持可以查看`kangax.github.io/compat-table/es6/`。随着时间的推移，支持度已经越来越高了，超过 `90%`的 `ES6` 语法特性都实现了。

![image](https://user-images.githubusercontent.com/18508817/52532590-c401ad80-2d62-11e9-98ed-af035ac605fe.png)

`Node` 是 `JavaScript` 的服务器运行环境（`runtime`）。它对 `ES6` 的支持度更高。除了那些默认打开的功能，还有一些语法功能已经实现了，但是默认没有打开。使用下面的命令，可以查看 `Node` 已经实现的 `ES6` 特性。

```sh
// Linux & Mac
$ node --v8-options | grep harmony

// Windows
$ node --v8-options | findstr harmony

node --v8-options | findstr harmony
  --es-staging (enable test-worthy harmony features (for internal use only))
  --harmony (enable all completed harmony features)
  --harmony-shipping (enable all shipped harmony features)
  --harmony-do-expressions (enable "harmony do-expressions" (in progress))
  --harmony-class-fields (enable "harmony fields in class literals" (in progress))
  --harmony-static-fields (enable "harmony static fields in class literals" (in progress))
  --harmony-array-flatten (enable "harmony Array.prototype.flat{ten,Map}" (in progress))
  --harmony-locale (enable "Intl.Locale" (in progress))
  --harmony-public-fields (enable "harmony public fields in class literals")
  --harmony-private-fields (enable "harmony private fields in class literals")
  --harmony-numeric-separator (enable "harmony numeric separator between digits")
  --harmony-string-matchall (enable "harmony String.prototype.matchAll")
  --harmony-string-trimming (enable "harmony String.prototype.trim{Start,End}")
  --harmony-sharedarraybuffer (enable "harmony sharedarraybuffer")
  --harmony-regexp-named-captures (enable "harmony regexp named captures")
  --harmony-regexp-property (enable "harmony Unicode regexp property classes")
  --harmony-function-tostring (enable "harmony Function.prototype.toString")
  --harmony-promise-finally (enable "harmony Promise.prototype.finally")
  --harmony-optional-catch-binding (enable "allow omitting binding in catch blocks")
  --harmony-import-meta (enable "harmony import.meta property")
  --harmony-bigint (enable "harmony arbitrary precision integers")
  --harmony-dynamic-import (enable "harmony dynamic import")
  --harmony-array-prototype-values (enable "harmony Array.prototype.values")


```

`阮大`写了一个工具 [ES-Checker](https://github.com/ruanyf/es-checker)，用来检查各种运行环境对 `ES6` 的支持情况。访问`ruanyf.github.io/es-checker`，可以看到您的浏览器支持 `ES6` 的程度。运行下面的命令，可以查看你正在使用的 `Node` 环境对 `ES6` 的支持程度。

```sh
$ npm install -g es-checker
$ es-checker

=========================================
Passes 24 feature Detections
Your runtime supports 57% of ECMAScript 6
=========================================
```

---
