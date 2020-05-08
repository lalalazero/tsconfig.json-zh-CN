# tsconfig.json-zh-CN
tsconfig.json 配置的中文翻译，参照 https://www.typescriptlang.org/v2/en/tsconfig


### typeRoots
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

### types
跟 `typeRoots` 一样，如果指定了 `types` 选项，只有指定的类型才会被包含。比如：
```json
{
  "compilerOptions": {
    "types": ["node", "lodash", "express"]
  }
}
```
这个配置包含了 `./node_modules/@types/node`,`./node_modules/@types/lodash`,`./node_modules/@types/express`,而没有其他类型如 `./node_modules/@types/*` 会被包含。
`types` 和 `typeRoots` 的区别在于前者专门用来指定类型，后者用来指定类型路径（文件夹位置）
