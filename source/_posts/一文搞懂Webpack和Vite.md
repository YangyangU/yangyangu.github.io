---
title: 一文搞懂 Webpack 和 Vite
date: 2024-04-25 16:00:00
tags: [Webpack, Vite]
categories: 工程化
index_img: /img/default.png
banner_img: /img/default.png
---

![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/2b07f91e750c4751ab4e412a06fb8c9f~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=771&h=318&s=214903&e=png&b=fec644)

## 前言

Webpack 和 Vite 都是前端工程化工具。Webpack 作为老大哥，在前端社区也算站稳了脚跟，它提供了许多配置和插件，让开发者定制化构建项目。随着 Vite 的出现，Webpack 在启动时间和热更新方面的缺陷就慢慢暴露，Vite 逐渐开始替代 Webpack。

## Webpack

### 使用步骤

**1. 初始化项目**

```bash
yarn init -y
```

**2. 安装依赖**

```bash
yarn add webpack webpack-cli -D
```

**3. 在项目中创建 src 目录，编写代码**

**4. 打包**

```bash
yarn webpack
```

### 配置文件

Webpack 提供配置项和插件系统，允许我们在`webpack.config.js`文件中进行各种配置，从而帮助我们定制化构建项目。

#### mode

设置打包的模式，`production`为生产模式，`development`为开发模式

```js
module.exports = {
  mode: "production",
};
```

#### entry

指定打包入口文件，默认为`./src/index.js`

```js
module.exports = {
  entry: "./src/a.js",
};
```

指定打包入口文件为`./src/a.js`

```js
entry: ["./src/a.js", "./src/b.js"];
```

使用数组可以设置多个入口文件，但是最后始终打包成一个文件

```js
entry:{
    aa:'./src/a.js',
    bb:'./src/b.js'
},
```

对象形式也可设置多个入口文件，但是会打包成多个文件。Webpack 会将`value`文件打包成`key.js`文件，比如上述例子会将`'./src/a.js'`文件打包成`aa.js`文件、`'./src/b.js'`件打包成`bb.js`文件。

#### output

对打包后的文件进行配置，默认打包文件地址为`./dist/main.js`

```js
const path = require("path");

module.exports = {
  output: {
    filename: "bundle.js", //打包后的文件名
    clean: true, //每次打包前清除上次打包的文件
    path: path.resolve(__dirname, "hello"), //打包后的文件存放路径，必须要绝对路径
  },
};
```

在`filename`中 Webpack 提供模版字符串的形式，可以自动生成唯一文件名：

- `filename: "[name].js"` 打包多个文件时匹配对应的文件名,`[name]`与 entry 中的 key 对应。
- `filename: "[hash].js"` 会随机生成哈希值，一般用于区分版本。

等等，详情参考官方文档 [Output | template-strings](https://webpack.docschina.org/configuration/output/#template-strings)

#### loader

Webpack 默认情况下，只会处理 js 文件，如果我们希望它可以处理其他类型的文件，则要为其引入 loader

以 css 为例，如果我们不进行任何操作

```js
//直接将css引入到js中
import "./style/index.css";

document.body.insertAdjacentHTML("beforeend", "<h1>hello webpack</h1>");
```

直接打包

![image.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/b79dfc906c304c919c48740c96fca4f0~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=745&h=222&s=21476&e=png&b=1e1e1e)

报错了，Webpack 说读不懂。

我们需要安装解析 css 文件的 loader：

```bash
yarn add css-loader
```

配置 loader，`rules`是一个数组，允许我们配置多个 loader，`test`属性是一个正则表达式，用于匹配对应 loader 的文件，`use`属性则是匹配对应 loader。

```js
module: {
  rules: [
    {
      test: /\.css$/, //匹配文件
      use: "css-loader",
    },
  ];
}
```

配置完毕，成功打包，但是页面上并没有样式，但是打包文件中是有样式的啊，为什么就没有显示到页面上呢？

![image.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/f78ba8c1067840159bffdb596152f8f4~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=481&h=61&s=4104&e=png&b=1f1f1f)

因为 loader 遵循职责单一原则，一个 loader 只做一件事情，如果你想在打包的文件中使用上 css，光有解析 loader 还不够，还需要安装另一个 loader：

```bash
yarn add style-loader
```

然后配置`style-loader`：

```js
module.exports = {
  module: {
    rules: [
      {
        test: /\.css$/, //匹配文件
        use: ["style-loader", "css-loader"], //从后往前执行,有顺序要求
      },
    ],
  },
};
```

这里需要注意 ⚠️，如果我们配置多个 loader，则用数组存放，存放的顺序遵循**从右往左**，先执行的放右边，否则会报错。这里先编译 css 再使用，所以顺序为`'style-loader','css-loader'`。

##### babel-loader

在编写 js 代码时，经常需要使用一些 js 中的新特性，而新特性在旧的浏览器中兼容性并不好。但是我们现在希望能够使用新的特性，我们可以采用折中的方案。依然使用新特性编写代码，但是代码编写完成时我们通过一些工具将新代码转换为旧代码。

[Babel](https://babel.nodejs.cn/) 就是这样一个工具，可以将新的 js 语法转换为旧的 js，以提高代码的兼容性。我们如果希望在 Webpack 支持 babel，则需要向 Webpack 中引入 babel-loader。

```js
document.body.onclick(() => {
  alert("hello webpack");
});
```

这里我们使用一个箭头函数，在不做任何处理的情况下进行打包

![image.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/9a10db0edca94d77872bd99660885a22~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=582&h=61&s=6024&e=png&b=1f1f1f)

打包后发现还是箭头函数。

于是我们安装`babel-loader`以及一些配置：

```bash
yarn add -D babel-loader @babel/core @babel/preset-env
```

配置

```js
module: {
  rules: [
    {
      test: /\.m?js$/, //以.mjs或js后缀的文件
      exclude: /(node_modules|bower_components)/, //排除node_modules中的文件
      use: {
        loader: "babel-loader",
        options: {
          presets: ["@babel/preset-env"], //根据目标浏览器或运行时环境自动选择合适的转换规则
        },
      },
    },
  ];
}
```

再试试呢：

![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/7b1a28dfbd914275971397476bf870c9~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=608&h=60&s=6398&e=png&b=1f1f1f)

成功将箭头函数打包成普通函数。

###### 配置兼容性

我们可以在`package.json`文件中配置需要兼容的浏览器，详情见 [github](https://github.com/browserslist/browserslist)

```json
"browserslist": [
  "defaults", //默认
  "ie <= 11", //ie版本低于11
  "last 2 versions", //至少存在两个版本
  "> 1%", //市场占有率大于1%
  "iOS 7",
]
```

#### plugin

plugin 的作用是 Webpack 扩展功能。loader 可以理解为转换器，用于处理模块之间的转换，plugin 则用于执行更广泛的任务，它可以访问 Webpack 的生命周期，在合适的时机执行插件的功能。

举个例子 🌰：

我想在打包目录生成 html 文件，用于访问打包的 js 文件，我们可以手动创建，但是我们不建议直接操作`dist`打包目录，我们可以通过 plugin 自动生成该文件。

安装自动生成 html 文件的插件

```bash
yarn add -D html-webpack-plugin
```

配置 plugin

```js
plugins: [
  //自动创建html文件
  new HTMLPlugin({
    title: "Hello Webpack", //配置html文件的标题
    template: "./src/index.html", //以哪个文件为模版创建
  }),
];
```

当我们再次打包，就会自动生成包含对应配置的 html 文件。

![image.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/87bfecda699449a5979753c9c4d6d3bd~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=200&h=90&s=3005&e=png&b=252526)

### 服务器环境

`webpack-cli`提供了许多命令帮助我们打包运行项目

```bash
yarn webpack
```

每次修改源码都需要重新打包，太麻烦了

```bash
yarn webpack --watch
```

加上`--watch`后每次修改源码都会被监听到，并且重新打包。

![image.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/6329df87bb8e4f91a2b344f2d239a561~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=544&h=73&s=11212&e=png&b=1e1e1e)

但是这个命令不能完美还原项目上线的场景，因为它访问的是文件目录，而不是服务器。

![image.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/041b8d5d3b9a4785a96f6810536c74fa~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=704&h=176&s=11852&e=png&b=fefefe)

为此，我们可以安装一个 Webpack 服务：

```bash
yarn add -D webpack-dev-server
```

当我们运行以下命令，它能够将项目部署到一个开发服务器上：

```bash
yarn webpack serve --open
```

当我们访问本地 8080 端口，就能够看到我们的项目。

![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/a39e13a757994cbf8ebd21a9be5870a5~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=421&h=149&s=8115&e=png&b=fdfdfd)

⚠️ 需要注意的是：这个服务它只会将我们的项目打包并运行在这个服务器，但是本地看不到这个`dist`的，所以当我们项目调试完毕，还需要自己手动打包一下。

### 快捷命令

我们一般会将常用的命令配置一下，使用起来更方便：

```json
"scripts": {
  "build": "webpack",
  "watch": "webpack --watch",
  "dev": "webpack serve --open"
},
```

## Vite

Webpack 是先打包再运行，而 Vite 开发时并不打包，而是直接采用 ES Module 运行项目，部署的时候再打包，开箱即用。

### 使用步骤

**1. 初始化项目**

```bash
yarn init -y
```

**2. 安装 Vite**

```bash
yarn add -D vite
```

**3. Vite 的源码目录就是项目根目录，创建`index.html`文件，以 ES Module 的方式引入 js 文件**

```html
<script type="module" src="./index.js"></script>
```

**4. 在本地`5173`端口启动一个开发服务器，进行项目调试**

```bash
yarn vite
```

**5. 打包代码**

```bash
yarn vite build
```

### 快速创建项目

参考 [开始 | Vite 官方中文文档 (vitejs.cn)](https://vitejs.cn/vite3-cn/guide/#scaffolding-your-first-vite-project)

```bash
yarn create vite
```

然后按照提示操作一步步配置即可！

如果你选的是原生 JS，你就会得到一个这样的完整项目路径：

![image.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/dc878253b972445ca40eee285985308a~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=235&h=280&s=11761&e=png&b=252526)

### 配置文件

首先在根目录下创建`vite.config.js`文件，⚠️ 注意这里的抛出语法与 Webpack 不同，Webpack 使用 CommonJS 语法，而 Vite 使用的是 ES Module 语法。

```js
//webpack
module.exports = {
    ...
}
```

```js
//vite
export default = {
    ...
}
```

这里 Vite 还提供了一个可选配置项`defineConfig`

```js
import { defineConfig } from "vite";//需不需要提示

export default defineConfig({
    ...
})
```

它的作用是在我们写配置的时候会不会有提示。

加了：

![image.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/e8b3281b4ea0411ab6db84977ed8da49~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=506&h=174&s=14896&e=png&b=1f1f1f)

没加：

![image.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/a649bc2692564ce4a4822396eb14e8f6~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=398&h=164&s=8082&e=png&b=1e1e1e)

对比不难发现区别。

#### Vite 需要配置 loader 吗？

试验一下：

```css
h2 {
  background-color: pink;
}
```

```js
import "./index.css";
document.body.insertAdjacentHTML("beforeend", "<h2>hello vite</h2>");
```

这里我们在 js 中引入 css 样式，然后`yarn vite` 运行一下

![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/0d4e62e6bcfd4ac3b2c04c7da18e9198~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=461&h=171&s=7138&e=png&b=fefefe)

结果显而易见，vite 不用配置 loader 就能编译和使用 css。

#### 插件

Vite 可以使用插件进行扩展，这得益于 Rollup 优秀的插件接口设计和一部分 Vite 独有的额外选项。详情参见 [使用插件 | Vite 官方中文文档 (vitejs.cn)](https://vitejs.cn/vite3-cn/guide/using-plugins.html)

举个例子 🌰：

要想为传统浏览器提供支持，类似于 Babel，需要引入官方插件`@vitejs/plugin-legacy`和压缩工具`terser`。

```bash
yarn add -D @vitejs/plugin-legacy terser
```

然后配置插件：

```js
import { defineConfig } from "vite"; //需不需要提示
import legacy from "@vitejs/plugin-legacy";

export default defineConfig({
  plugins: [
    legacy({
      targets: ["ie 11"], //兼容IE 11
    }),
  ],
});
```

我们来个箭头函数检验一下

```js
document.body.onclick = () => {
  alert("hello vite");
};
```

`yarn vite build`一下

![image.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/b9c237136b5f417f8d75a4a7a83d562c~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=349&h=165&s=10660&e=png&b=262627)

我们可以看到 Vite 给我们生产了三个 js 文件和一个 html 文件，来分析一下：

`index.js`：

![image.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/ff6d50da1f7c431ca55d249120ebc223~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=468&h=52&s=4671&e=png&b=1e1e1e)

这个文件中的箭头函数并没有转为普通函数；

`index-legacy.js`：

![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/3153971c059148c0852a3917640edde0~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=522&h=48&s=5193&e=png&b=1f1f1f)

这个文件中的箭头函数成功转成了普通函数；

`polyfills-legacy.js`：

![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/f289559ffa1c4faca4339661c488cbc8~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=706&h=123&s=21850&e=png&b=1e1e1e)

这个文件的作用是用于向下兼容旧版浏览器，确保旧版浏览器能够读懂新特性。

`index.html`：

![image.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/51f54a5761f14494a5c12830c47d7028~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=1044&h=299&s=49070&e=png&b=1e1e1e)

这个文件的意图就很明显了，如果浏览器支持 ESModule（即现代浏览器），则一般可以支持新语法，因此就可以直接引入新语法文件；但是如果浏览器不支持 ESModule，则可能是旧版本的浏览器，它们可能不支持新特性，这时候就需要引入兼容性代码来填补这些功能的缺失。在这种情况下，一般会引入 polyfills，以及旧版文件，来确保应用在旧版浏览器中的正常运行。

vite 不像 webpack 只生成一个降级之后的文件，而是两个都生成，再根据浏览器的兼容性，动态地确定加载哪个版本的代码。对于现代浏览器来说，直接加载现代版本的代码可以获得更快的加载速度和更好的性能。而对于不支持 ES Module 的旧版浏览器，则加载降级版本的代码以确保兼容性。

以上是对兼容性插件`legacy`的分析，更多插件可参见 [插件 | Vite 官方中文文档 (vitejs.cn)](https://vitejs.cn/vite3-cn/plugins/)

### 快捷命令

```json
"scripts": {
  "dev": "vite",
  "build": "vite build",
  "preview": "vite preview"
},
```

## 对比总结

1.  **构建方式：** Webpack 通过构建整个项目的依赖图，将所有资源打包成一个或多个 bundle 文件，每次重启都需要打包。Vite 采用了即时编译的方式，在开发模式下通过浏览器原生支持的 ES Module 特性进行加载，不需要打包。
1.  **开发体验：** Webpack 需要较多的配置，对复杂的项目来说，需要花费时间和精力来配置各种 loader 和 plugin。Vite 开箱即用，不需要复杂的配置即可快速启动项目，支持各种插件以满足特定需求。
1.  **热更新：** Webpack 的热更新通常需要借助 webpack-dev-server 等插件，在一些情况下配置起来比较复杂。Vite 内置了基于浏览器原生模块热更新的开发服务器，无需额外配置即可实现快速的热更新。

## 最后

已将学习代码上传至 [github](https://github.com/YangyangU/CodeSpace)，欢迎大家学习指正！
