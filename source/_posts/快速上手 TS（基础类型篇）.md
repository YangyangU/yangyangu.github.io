---
title: 快速上手 TS（基础类型篇）
date: 2024-07-02 16:00:00
tags: [TypeScript]
categories: TypeScript
index_img: /img/ts.png
banner_img: /img/default.png
---

## 前言

- [快速上手 TS（环境篇）- 掘金 (juejin.cn)](https://juejin.cn/post/7383643463532953619)

上一篇中我介绍了 TS 从安装到运行的环境配置，希望能够对你带来帮助。今天我们来学习一下 TS 的一些基本知识，TS 知识可多了，一篇文章当然不能涵盖，所以我会写多篇文章进行介绍，希望我能坚持更新，更希望你能坚持看下去！

## 基本用法

你在写 JS 的时候有没有想过这样一个问题：为什么 JS 不用写类型，不写类型带来的后果是什么？如果你尝试写过任意一门后端语言，你就会发现，每个类型都有自己声明的方式，而不是一味的 let、const，换句话说，弱类型会导致什么后果？

JS 设计之初的目标是成为一种简单易用的脚本语言，能够让非专业程序员快速学习，因此，JS 遵循动态类型系统，如果代码出现错误，只能在运行时才能发现，而 TS 跟一些后端语言一样提供的静态类型检测，能够在编译时就发现错误，避免一些不必要的失误。

### 类型声明

TS 允许我们在 JS 的基础上对变量进行声明

```ts
let str: string;
```

这段代码中，TS 允许我们在声明的变量后面用`:`接类型，表示我们声明了一个什么类型的变量。

如果你给变量赋的值不属于该类型，就会报错

```ts
let str: string = 123; //报错
```

这里我们把数字`123`赋给一个声明为`string`的变量，就会报错。

另外，如果变量在赋值之前被使用了，也会报错

```ts
let str: string;
console.log(str); //报错
```

如果在 JS 环境则不会报错，而是打印 `undefined`

### 类型推断

类型声明不是必须的，因为 TS 可以根据你的赋值推断出类型，这是 TS 比较厉害的一点

如果你正常的像

```ts
let num = 123;
```

你把鼠标放到上面就能看到它被推断出的类型

<img src="https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/8916287c61914b15a727d482e6434583~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=259&h=96&s=4237&e=png&b=1f1f1f" alt="image.png" width="30%" />

另外，它还能推断出返回值的类型

```ts
const fn = (str: string) => {
  return Number(str);
};
```

我们只需要知道这里用 TS 声明了一个函数（关于函数具体如何声明后面会讲），接收一个 string 类型的参数 str，但是没有声明返回值的类型，TS 自己推断出来了

<img src="https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/2e045d1f88e548c0885a85125fbd455f~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=472&h=133&s=12681&e=png&b=1f1f1f" alt="image.png" width="50%" />

这里可以看出，类型声明是可选的，你可以加，也可以不加，只是不能保证 TS 会正确推断出类型。

## 类型系统

TS 继承了 JS 的类型，在这个基础上进行升级，形成了一套自己的类型系统

### 基本类型

我们知道 JS 有七大基本类型和对象类型，下面我会将每一个类型都拎出来讲

```ts
const num: number = 123;
const str: string = "123";
const flag: boolean = true;
const bigint: bigint = 123n;
const symbol: symbol = Symbol("123");
const un: undefined = undefined;
const nu: null = null;
```

#### number 类型

number 类型包含所有整数和浮点数

```ts
const x: number = 123;
const y: number = 3.14;
const z: number = 0xffff;
```

#### string 类型

普通字符串和模板字符串都属于 string 类型

```ts
const x: string = "hello";
const y: string = `${x} world`;
```

#### boolean 类型

boolean 类型只包含`true`和`false`，就没什么好说的了

```ts
const x: boolean = true;
const y: boolean = false;
```

#### bigint 类型

bigint 类型包含所有的大整数

```ts
const x: bigint = 123n;
const y: bigint = 0xffffn;
```

但是要注意的是 bigint 与 number 相互不兼容，意思是当给 bigint 类型赋值 number 时会报错，反过来也是。

#### symbol 类型

symbol 类型包含所有的 Symbol 值，用的较少，咱们就一笔带过

```ts
const x: symbol = Symbol();
```

#### undefined 和 null 类型

如果没有声明类型的变量，被赋值为`undefined`或`null`，默认情况下它们的类型会被推断为`any`。

```ts
let a = undefined; // any
const b = undefined; // any

let c = null; // any
const d = null; // any
```

如果希望避免这种情况，则需要打开编译选项`strictNullChecks`。

```ts
// 打开编译设置 strictNullChecks
let a = undefined; // undefined
const b = undefined; // undefined

let c = null; // null
const d = null; // null
```

将所有类型都列出来大概就这么多，看起来好像跟 JS 没多大差别，就加了个类型声明罢了，真的是这样吗，咱们接着往下看

### any、unknown、never 类型

TS 新增了 any、unknown 和 never 三种类型，分别有着不同的应用场景

#### any 类型

any 类型的变量可以被赋为任何值

```ts
let x: any;

x = 1;
x = "阳阳羊";
x = true;
```

这个类型一般不推荐使用，因为 TS 不会对 any 类型的变量进行类型检查，即使有明显的类型错误，只要句法正确，都不会报错。意思你声明的 string 类型变量甚至可以拿来当函数给调用掉，并且都不会报错。

```ts
let x: any = "阳阳羊";
x(1);
```

如果每个变量类型都是 any，就失去了 TypeScript 的意义，直接写 JS 就好了 😂

#### unknown 类型

`unknown`类型的变量，不能直接赋值给其他类型的变量（除了`any`类型和`unknown`类型）

避免了污染问题

不能直接调用`unknown`类型变量的方法和属性

可以赋值给任意其他类型

#### never 类型

意为空类型，不可能有这样的值，不可能赋给它任何值，否则都会报错。后面会用交叉类型进行举例

### 对象类型

在 JS 中我们将非基本类型的类型统称为对象类型，这其中包括了对象、数组、函数、包装类、正则等等

#### Object 与 object 类型

为什么要区分大小写呢？这是因为我们将 Object 称为广义上的对象，它囊括了几乎所有的值，原始类型值、对象、数组、函数都是合法的`Object`类型，除了`undefined`和`null`不能转为对象

```ts
let obj: Object;

obj = true;
obj = "hi";
obj = 1;
obj = { foo: 123 };
obj = [1, 2];
obj = (a: number) => a + 1;
obj = undefined; // 报错
obj = null; // 报错
```

而小写的 object 就是我们狭义说的对象，即可以用字面量表示的对象，只包含对象、数组和函数，不包括原始类型的值。

```ts
const x: object = { foo: 123 };
const y: object = [1, 2, 3];
const z: object = (n: number) => n + 1;
```

#### 包装类对象

由于包装对象的存在，导致每一个原始类型的值都有包装对象和字面量两种情况。

```ts
"hello"; // 字面量
new String("hello"); // 包装类对象
```

为了进行区分，TS 对五种原始类型分别提供了大写和小写两种类型。

- Boolean 和 boolean
- String 和 string
- Number 和 number
- BigInt 和 bigint
- Symbol 和 symbol

大写类型同时包含包装对象和字面量两种情况，小写类型只包含字面量，不包含包装对象。

```ts
const s1: String = "hello"; // 正确
const s2: String = new String("hello"); // 正确

const s3: string = "hello"; // 正确
const s4: string = new String("hello"); // 报错
```

这里`String`类型可以赋值为字符串的字面量，也可以赋值为包装对象。但是，`string`类型只能赋值为字面量，赋值为包装对象就会报错。

### 值类型

TS 允许一个值成为一个类型

```ts
const x: "hello" = "hello";
```

用`const`声明的变量，如果没有注明类型，则会把它推断为值类型

<img src="https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/ffd019c1c25744bea099dad24fedd652~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=286&h=88&s=5528&e=png&b=1f1f1f" alt="image.png" width="30%" />

而`let`则不会

<img src="https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/05d2a56546814fd2b3121395ad2ec747~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=234&h=94&s=4604&e=png&b=1f1f1f" alt="image.png" width="30%" />

这个原因显而易见，因为`const`声明的变量不可修改，即常量，但如果声明是对象的话，那就另外谈了，因为对象存的是地址，属性值可以改变，所以并不会推断为值类型。

<img src="https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/06c5d56dd8c845f78aeb2dd3fd8b8ea5~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=373&h=191&s=9037&e=png&b=1e1e1e" alt="image.png" width="50%" />

### 联合类型

联合类型指的是多个类型组成的一个新类型，使用符号`|`拼接。我们知道`|`是`或`的意思，所以`A|B`的意思是只要属于`A`或`B`其中一个就行了。

```ts
let x: string | number;

x = 123; // 正确
x = "abc"; // 正确
```

当联合类型与值类型结合，就可以表示一个变量可能有多个值。

```ts
let color: "红" | "黄" | "绿";
```

### 交叉类型

有`|`当然有`&`，不出所料这个意思应该是`与`。`A&B`表示，任何一个类型必须同时属于`A`和`B`。

```ts
let z: number & string;
```

什么类型能兼具`number`和`string`？

当然是不存在的，TS 新增了一个类型表示不存在的`never`，这里也可以用它来声明

<img src="https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/1df706673b3a4e2a928eb67083144c21~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=271&h=99&s=5356&e=png&b=1f1f1f" alt="image.png" width="30%" />

交叉类型的主要用途是表示对象的合成。

```ts
let person: { name: string } & { age: number };

person = {
  name: "阳阳羊",
  age: 21,
};
```

## 参考

- [阮一峰 TypeScript 教程](https://typescript.p6p.net/typescript-tutorial/types.html)
- [TypeScript 官方文档](https://www.typescriptlang.org/)

## 最后

以上是对 TS 类型的简单介绍，对于新同学来说应该很容易入手，后面我会继续更新进阶知识，关于 type、元祖、接口、泛型等等，我会持续更新。
