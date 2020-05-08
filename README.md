# tsconfig.json-zh-CN

tsconfig.json 配置的中文翻译，参照 https://www.typescriptlang.org/v2/en/tsconfig

## 索引
- [typeRoots](#typeRoots)
- [types](#types)
- [target](#target)
- [module](#module)
- [strictNullChecks](#strictNullChecks)
- [strictFunctionTypes](#strictFunctionTypes)
- [esModuleInterop](#esModuleInterop)
- [allowSyntheticDefaultImports](#allowSyntheticDefaultImports)

## Module Resolution 模块化解析

### **typeRoots**

默认情况下所有的位于 `node_modules/@types` 目录下的包都被认为是可见的，ts 编译的时候都会去查找可见的 `types` 。也就是说 `./node_modules/@types` 目录和 `../node_modules/@types` 目录和 `../../node_modules/@types` 目录等。
如果指定了 `typeRoots` 选项，那么只有指定目录下的包会被包含。
```json
{
  "compilerOptions": {
    "typeRoots": ["./typings", "./vendor/types"]
  }
}
```
这个配置指定了包含 `./typings` 目录和 `./vendor/types` 目录下的包，而没有 `./node_modules/@types` 目录下的包。所有的路径都是相对于 `tsconfig.json` 而言的。 

### **types**

跟 `typeRoots` 一样，如果指定了 `types` 选项，只有指定的类型才会被包含。比如：
```json
{
  "compilerOptions": {
    "types": ["node", "lodash", "express"]
  }
}
```
这个配置包含了 `./node_modules/@types/node`， `./node_modules/@types/lodash`，`./node_modules/@types/express`，而没有其他类型如 `./node_modules/@types/*` 会被包含。
`types` 和 `typeRoots` 的区别在于前者专门用来指定类型，后者用来指定类型路径（文件夹位置）


## Project Options 工程配置

### **target**

```json
{
  "compilerOptions": {
    "target": "es5"
  }
}
```

`target` 选项用来配置 `ts` 会用什么样的 `es` 标准来编译。比如 `es3` `es5` `es6`。比如设定 `target: es5`，那么 es6 的语法箭头函数 `() => {}` 就会被编译成相同的 `function` 表达式。

设置 `target` 同样会改变 `lib` 的默认值，可以同时指定 `target` 和 `lib`，方便起见也可以单独指定 `target`。

对于 `Node.js` 环境，推荐的 `target` 和 `Node` 版本对应关系为：

| name | supported target |
| ---- | ---------------- |
| Node 8 | ES2017 |
| Node 10 | ES2018 |
| Node 12 | ES2019 |

这些是根据 [node.green](https://node.green/) 的数据来定的。

`target` 的默认值是 `es3`[1]，或者 `es5`[2], `es6`（等同于 `es2015`）。可选值有 `es7(es2016)` `es2017` `es2018` `es2019` `es2020` `esNext`。 `esNext` 表示 `es` 的最高标准，应当谨慎使用，可能会导致升级时无法预知的问题。

注：[1] 在使用 `tsc` 命令行编译时；[2] `tsconfig.json` 中默认配置（经验）

### **module**

```json
{
  "compilerOptions": {
    "module": "commonjs"
  }
}
```

设定编译后的 js 模块化标准，当 `target` 是 `es3` 或者 `es5` 时，`module` 默认值是 `commonjs`。如果 `target: es6`，那么 `module: es6` 或者 `module: es2015`，两者是等同的。

对于如下的 ts 内容不同的模块化标准对应的输出不同。
```ts
// @filename: index.ts
import { valueOfPi } from "./constants";

export const twoPi = valueOfPi * 2;
```

CommonJS 输出
```js
const constants_1 = require("./constants");
exports.twoPi = constants_1.valueOfPi * 2;
```

UMD 输出
```js
(function (factory) {
    if (typeof module === "object" && typeof module.exports === "object") {
        var v = factory(require, exports);
        if (v !== undefined) module.exports = v;
    }
    else if (typeof define === "function" && define.amd) {
        define(["require", "exports", "./constants"], factory);
    }
})(function (require, exports) {
    "use strict";
    Object.defineProperty(exports, "__esModule", { value: true });
    // @filename: index.ts
    const constants_1 = require("./constants");
    exports.twoPi = constants_1.valueOfPi * 2;
});
```

AMD 输出
```js
define(["require", "exports", "./constants"], function (require, exports, constants_1) {
    "use strict";
    Object.defineProperty(exports, "__esModule", { value: true });
    exports.twoPi = constants_1.valueOfPi * 2;
});
```

System 输出
```js
System.register(["./constants"], function (exports_1, context_1) {
    "use strict";
    var constants_1, twoPi;
    var __moduleName = context_1 && context_1.id;
    return {
        setters: [
            function (constants_1_1) {
                constants_1 = constants_1_1;
            }
        ],
        execute: function () {
            exports_1("twoPi", twoPi = constants_1.valueOfPi * 2);
        }
    };
});
```

ESNext 输出
```js
// @filename: index.ts
import { valueOfPi } from "./constants";
export const twoPi = valueOfPi * 2;
```

ES2020 输出
```js
// @filename: index.ts
import { valueOfPi } from "./constants";
export const twoPi = valueOfPi * 2;
```

### **allowSyntheticDefaultImports**

当有些模块类库并没有指定一个默认导出的时候，开启这个选项可以让你写这样的代码：
```ts
import React from 'react'
```
而不是这样的代码：
```ts
import * as React from 'react'
```

这个选项并不会影响ts编译出的js内容，只是用来做类型检查。这个选项可以让 ts 保持和 babel 一样，那就是在使用一个模块的默认导出时能更符合人类思维直觉。

注：翻译无能...

### **esModuleInterop**

开启这个选项 ts 会通过给所有导入的对象创建命名空间来增加 commonjs 和 esModule 之间互操作性。对于 esModule 来说，如果要使用 `import React from 'react'` 导入，那么就必须要有一个 `export default` 导出，这点 ts 跟 esModule 是保持统一的的。但是 commonjs 没有默认导出(`default`属性）这个概念，导出的所有东西都是 `module.exports` 这个对象。所以如果不开启 `esModuleInterop` 这个属性，下面的代码是无法运行的[1]。
```ts
// commonjs 文件 a.js
module.exports = {
  xxx: 123
}
// esModule b.ts
import xxx from 'a.ts' // 根本就没有 default 对象让你可以 import 进来
```
打开这个选项同样会打开 `allowSyntheticDefaultImports` 

注：[1]在实践的时候发现这个代码还报错 `ts2306 isnt a module`，哎


## Strict Checks 严格检查

### **strictNullChecks**

当 `strictNullChecks` 设置成 `false` 时，可以将 `null` 和 `undefined` 赋值给精准的类型。比如 
```ts
let a:string = 'hello'
a = null
```

如果设置为 `true`，`null` 和 `undefined` 都有各自的类型而且不能被赋值给精准的类型，比如 `string`，只能 `null` 型赋值给 `null` 型，`undefined` 同理。

官网的例子：
```ts
declare const loggedInUsername: string;

const users = [
  { name: "Oby", age: 12 },
  { name: "Heera", age: 32 },
];

const loggedInUser = users.find((u) => u.name === loggedInUsername);
console.log(loggedInUser.age); // 这里会报错
// loggedInUser 有可能是 undefined，是不能赋值给精准的类型 User 的 
```

### **strictFunctionTypes**

开启这个选项后，函数的参数校验会更精确，参数不兼容的函数不能互相赋值。比如：
```ts
// 不开启检查
function fn(x: string) {
  console.log("Hello, " + x.toLowerCase());
}

type StringOrNumberFunc = (ns: string | number) => void;

// 不安全的赋值
let func: StringOrNumberFunc = fn;
// 不安全的函数调用，会导致错误，number 没有 toLowerCase() 方法
func(10);
```
开启严格检查后，会提示错误。
```ts
function fn(x: string) {
  console.log("Hello, " + x.toLowerCase());
}

type StringOrNumberFunc = (ns: string | number) => void;

// 不安全的赋值被阻止了，提示错误
let func: StringOrNumberFunc = fn;
// Type '(x: string) => void' is not assignable to type 'StringOrNumberFunc'.
//   Types of parameters 'x' and 'ns' are incompatible.
//     Type 'string | number' is not assignable to type 'string'.
//       Type 'number' is not assignable to type 'string'.
```

注：ts 团队在开发这个函数检查功能时发现历史遗留很多不安全的类继承关系，甚至有些 DOM 元素上也存在问题。因此，`strictFunctionTypes` 检查只会适用于 function syntax 写的函数，而不是 method syntax 写的函数.(这句话不知道怎么翻译，原文是 Because of this, the setting only applies to functions written in function syntax, not to those in method syntax)

如下是例子：
```ts
type Methodish = {
  func(x: string | number): void;
};

function fn(x: string) {
  console.log("Hello, " + x.toLowerCase());
}

// 仍旧是不安全的赋值，但是不会被检测到
const m: Methodish = {
  func: fn,
};
m.func(10);
```