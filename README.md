# tsconfig.json-zh-CN

tsconfig.json 配置的中文翻译，参照 https://www.typescriptlang.org/v2/en/tsconfig

## 索引
- [typeRoots](#typeRoots)
- [types](#types)
- [target](#target)
- [module](#module)

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




