---
title: 快速上手 TS（环境篇）
date: 2024-06-24 16:00:00
tags: [TypeScript]
categories: TypeScript
index_img: /img/ts.png
banner_img: /img/default.png
---

## 前言

1. [只会 Vue 的我，上班第一天就要我写 React+TS，是种什么样的体验？](https://juejin.cn/post/7377320107929829388)
2. [只会 Vue，快速上手 React（React Router 篇）](https://juejin.cn/post/7379157261426655273)

在前面两篇文章中，我分享了自己从 Vue 转 React 的心得，希望对大家有所帮助，今天再给大家分享一下如何搭建 TS 的学习环境。

## 为什么要学习 TS

从铺天盖地的 TS 教程，到 Vue3 被 TS 重写，种种迹象就可以见得 TS 的地位越来越重，TS 给我们开发到底带来了什么？用 JS 就不行吗？

TS 的静态类型系统允许我们在开发阶段就可以发现错误，通过显示类型注解和类型推断，让我们在团队开发中协作，TS 引入面向对象编程特效，接口泛型让我们可以写更 👍 的代码。

## TS 和 JS 的关系

一句话解释就是 TS 是 JS 的超集。

TS 与 C、Java 等众多后端语言类似，都需要先对代码进行编译才能运行。具体来说，TS 会将代码编译成浏览器或者 Node 能够读懂的 JS 代码。每次运行 TS 代码，首先需要通过 tsc 将其编译成 JS 文件，可能会生成不止一个 JS 文件。然后，我们就能够利用 TS 提供的高级特性来编写代码，同时确保最终生成的 JS 代码仍然能够成功运行。

## 环境搭建

### 安装 TS

TS 支持全局安装和本地安装，全局顾名思义就是我们可以在任意控制台运行 TS，适合个人开发配置。而本地安装则更适用于团队开发，确保项目中使用相同版本的 TS。

#### 全局安装

```bash
npm i typescript -g
```

安装完毕，现在控制台允许我们使用 ts 编译器，执行 tsc 命令，输入以下指令查看 TS 版本，如果可以看到，则说明安装成功

```bash
tsc -v
```

![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/d96e0282663d48bea7ea3396119af66b~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=271&h=37&s=2623&e=png&b=1e1e1e)

#### 本地安装

```bash
npm i --save-dev typescript
```

然后我们可以使用 npx 来运行 TS 编译器

```bash
npx tsc -v
```

同样，如果可以看到 ts 版本，则说明安装成功

### 初始化

在运行 TS 代码之前，我们需要对目录进行初始化，执行以下命令会生成一个`tsconfig.json`配置文件

```bash
tsc --init
```

![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/04a56ebd53724658bd1cd70ec938995f~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=669&h=227&s=11860&e=png&b=1e1e1e)
![image.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/f310e1e5007b464094d4caec62ae12b6~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=207&h=68&s=2742&e=png&b=252526)

#### 配置文件说明

`tsconfig.json`顾名思义允许我们对 TS 进行配置，其中一些重要的配置项

一个典型的`tsconfig.json`文件结构如下：

```json
{
  "compilerOptions": {
    // 编译选项
  },
  "include": [
    // 包含的文件或目录
  ],
  "exclude": [
    // 排除的文件或目录
  ],
  "files": [
    // 需要编译的特定文件
  ]
}
```

`compilerOptions`可以配置 TS 编译器行为，常用配置项包括以下几个：

```json
"target": "es2016", //指定编译后代码的ES版本
"module": "commonjs", //指定生成的模块系统
"strict": true, //开启严格模式
"outDir": "./", //指定编译后的文件输出的目录
"jsx": "react", //指定JSX代码的编译方式
"allowJs": true  //允许编译JS文件
```

其实这些在`tsconfig.json`文件都有注解，这里就不过多赘述

![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/7c1714b0fcce4285adb4b4e7b88dd828~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=1268&h=409&s=76064&e=png&b=1e1e1e)

### 运行代码

首先我们在目录下创建一个`.ts`文件，在里面书写我们需要运行的代码。

🙋‍♀️🌰 创建一个 ts 文件，内容如下：

```ts
//1.ts
let nickname: string = "阳阳羊";
console.log(nickname);
```

接着我们在控制台输入`tsc`命令，目录会生成一个对应的 JS 文件

![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/306dca1cb3054ddc9ea339aa6e58230b~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=286&h=41&s=2761&e=png&b=1e1e1e)
![image.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/1cd21a3abacd432f85a0028156f0784b~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=148&h=89&s=2641&e=png&b=252526)

tsc 将 ts 文件编译成了 js 文件

```js
//1.js
"use strict";
let nickname = "阳阳羊";
console.log(nickname);
```

但是每当我们修改 TS 代码又需要重新执行一次`tsc`，很麻烦，TS 提供了一个指令`tsc --watch`（简写`tsc -w`）执行这段命令后，编译器会持续监听文件内容的变化，然后更新编译后的 JS 文件，就不用每次手动编译。

拿到编译好的 JS 文件，我们执行 node 命令运行这个编译后的文件即可。

#### 三方运行工具

如果你觉得以上运行方式太复杂了，你可以安装第三方工具，这里我介绍两种，如果有好用的，欢迎大家在评论区分享！

##### ts-node

在控制台输入以下命令

```bash
npm i ts-node -g
```

安装完毕后，在控制台输入命令`ts-node`加文件名即可

```bash
ts-node 1.ts
```

![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/54151bf579ea40688ef039e8964d451f~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=326&h=37&s=2526&e=png&b=1e1e1e)

##### Code Runner

还有一个是 VScode 中的一个插件

![image.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/94ee9c12eae8425f9698d6617f8d6897~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=629&h=149&s=16670&e=png&b=1e1e1e)

安装完这个插件我们直接在文件右上角点击运行即可

![image.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/a0a12dbad54144cf947eecd5024c7e6b~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=806&h=144&s=13264&e=png&b=1e1e1e)

这样我们可以在输出窗口看到代码运行结果

![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/ab6912e2e2b94dfb9b71817610908034~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=601&h=118&s=14971&e=png&b=1e1e1e)

## 总结

以上就是《快速上手 TS（环境篇）》的全部内容了，如果你也对 TS 感兴趣，可以关注加三连，后期我会继续更新 TS 的相关知识。

## 最后

已将学习代码上传至 [github](https://github.com/YangyangU/CodeSpace)，欢迎大家学习指正！
