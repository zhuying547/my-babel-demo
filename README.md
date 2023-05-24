# my-babel-demo

```shell
npm install
npm run build
```

## Guide

Babel 是一个工具链，主要用于将采用 ECMAScript 2015+ 语法编写的代码转换为向后兼容的 JavaScript 语法，以便能够运行在当前和旧版本的浏览器或其他环境中。
下面列出的是 Babel 能为你做的事情：

- 语法转换
- 通过 Polyfill 方式在目标环境中添加缺失的功能（通过引入第三方 polyfill 模块，例如 core-js）

```shell
npm install --save-dev @babel/core @babel/cli @babel/preset-env
```

Plugins:代码转换功能以插件的形式出现，插件是小型的 JavaScript 程序，用于指导 Babel 如何对代码进行转换。

```shell
./node_modules/.bin/babel src --out-dir lib --plugins=@babel/plugin-transform-arrow-functions
```

Preset: 一组预先设定的插件。

```shell
./node_modules/.bin/babel src --out-dir lib --presets=@babel/env
```

现在，名为 env 的 preset 只会为目标浏览器中没有的功能加载转换插件。

通过给 @babel/env 配置 useBuiltIns 和 corejs 属性，我们实现了 polyfill 方法的按需加载。

## QA

`@babel/polyfill` 会对全局范围（global scope）造成污染。

如果你确切知道所需的 polyfill，你可以直接从 core-js 中获取。把 useBuiltIns 设置为 usage，表示只包含所需的 polyfill。
Babel 将检查你的所有代码，以便查找目标环境中缺失的功能，然后只把必须的 polyfill 包含进来。

WARNING (@babel/preset-env): We noticed you're using the `useBuiltIns` option without declaring a core-js version. Currently, we assume version 2.x when no version is passed. Since this default version will likely change in future versions of Babel, we recommend explicitly setting the core-js version you are using via the `corejs` option.
注意到配置了 useBuiltIns 选项但是没有指定 core-js 的版本，这里默认选择 2.x 的版本，但是需要注意这个默认版本可能在未来发生改变，推荐在 corejs 选项中明确配置其版本。

@babel/preset-env 的 dependencies 中有 "core-js-compat": "^3.25.1",的包。

## Babel 和 core-js@3

### babel/polyfill

@babel/polyfill 是一个包裹的包，里面仅仅包含：

1. core-js 稳定版的引入（在 Babel 6 中也包含提案）
2. regenerator-runtime/runtime，用来转译 generators 和 async 函数

但是这个包没有提供从 core-js@2 到 core-js@3 平滑升级路径：因为这个原因，决定弃用 @babel/polyfill 代之以分别引入需要的 core-js 和 regenerator-runtime 。

```js
import "core-js/stable";
import "regenerator-runtime/runtime";
```

```shell
npm i --save core-js regenerator-runtime
```

设置 `useBuiltIns: entry` 模式的 `@babel/preset-env` 编译所有能够获得的 `core-js` 入口和他们的组合。它的意思是需要在入口文件手动引入。

设置 `useBuiltIns: usage`模式的 `@babel/preset-env` 自动导入所需内容，无需先手动导入.

