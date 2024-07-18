---
title: 快速上手TS（进阶篇）
date: 2023-07-10 16:00:00
tags: [TypeScript]
categories: TypeScript
---

![image.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/c92154b7fc1549e98c18ac66b04f5410~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=1082&h=453&s=606549&e=png&b=030303)

## 前言

- [快速上手 TS（环境篇）- 掘金 (juejin.cn)](https://juejin.cn/post/7383643463532953619)
- [快速上手 TS（基础类型篇）- 掘金 (juejin.cn)](https://juejin.cn/post/7386701961808904204)

前两篇中我介绍了 TS 从安装到运行的环境配置，以及 TS 的基础类型介绍，希望能够对你带来帮助。今天我们来学习一下 TS 的进阶知识，数组、元组、函数、接口等等，具体可以参考目录，还是那句话希望我能坚持更新，更希望你能坚持看下去！

## 数组

在 JS 中数组就是数组，但 TS 中的数组被分为了数组和元组

### 数组

TS 数组有一个根本的特征，所有的成员类型必须相同，成员数量可以不确定。

数组有两种声明方式，类型后面加`[]`或者使用内置`Array`接口（下面会详细介绍接口 interface）

```ts
let arr: number[] = [1, 2, 3];
let arr2: Array<number> = [1, 2, 3];
```

如果成员类型有不同类型，可以使用联合类型

```ts
let arr: (number | string)[] = [1, "阳阳羊", 3];
let arr2: Array<number | string> = [1, 2, "阳阳羊"];
```

#### 类型推断

数组的类型推断与前文讲的有所不同，它会根据每个成员的类型而变化

如果没有声明数组类型，TS 会自动进行类型推断，当数组为空时，会推断为`any`类型的数组

<img src="https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/89c40b0e3b3c48e1ba5fb62597e78793~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=295&h=86&s=4591&e=png&b=1f1f1f" alt="image.png" width="30%" />

当我们修改成员时，会自动进行更新。

这里我们`push`一个数字，会自动推断为`number`数组

<img src="https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/1e381d85cb3742d282c8e2c8385aac75~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=302&h=143&s=7453&e=png&b=1f1f1f" alt="image.png" width="30%" />

我们再`push`一个字符串，自动推断成联合类型`string | number`的数组

<img src="https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/9ed3af34f1a943718984afa42f7d5525~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=405&h=164&s=11903&e=png&b=1e1e1e" alt="image.png" width="50%" />

### 元组

元组是 TS 新增的数据类型，它表示成员类型可以自由设置的数组。

```ts
const tuple: [string, number, boolean] = ["阳阳羊", 123, true];
```

写法上与数组有所不同，数组的类型是写在`[]`的左边，而元组的类型是写在`[]`内部。

使用元组时必须给出类型声明，不然会被 TS 自动推断为数组

<img src="https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/cd3c5599c3b04805a490f0d506373b24~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=578&h=87&s=9894&e=png&b=1f1f1f" alt="image.png" width="50%" />

#### `?`可选符

元组成员的类型可以添加问号后缀（`?`），表示该成员是可选的。

```ts
const tuple: [string, number, boolean?] = ["阳阳羊", 123];
```

需要注意的是可选成员要放在尾部，即所有可选成员必须在必选成员之后

如果放在前面会报错

<img src="https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/3a91064aa2774f42ab530ec2ef5cee14~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=740&h=150&s=22684&e=png&b=1f1f1f" alt="image.png" width="70%" />

#### `...`扩展运算符

一般情况下，元组的成员数量是固定的，有多少个类型声明就有多少个成员，但是`...`扩展运算符允许我们表示不定成员数量的元组。

```ts
const tuple: [string, ...number[]] = ["阳阳羊", 123, 456];
```

扩展运算符用在元组的任意位置都可以，但是只能扩展数组或元组。

## 函数

函数的类型声明，需要给出参数和返回值的类型

```ts
function fn(a: number, b: number): number {
  return a + b;
}
```

这里两个参数`a`、`b`类型都为`number`，以及返回值的类型`number`，返回值类型写在参数列表的圆括号后面。

我们也可以使用函数表达式进行声明

```ts
//方式一
const fn = function (a: number, b: number): number {
  return a + b;
};
//方式二
const fn: (a: number, b: number) => number = function (a, b) {
  return a + b;
};
```

这里的两种方式都可以实现，第一种是通过等号右边来推断`fn`的类型，第二种则是直接指明了`fn`的类型。

返回值类型通常可以不写，TS 会自己推断出来，如果没写`return`语句，会推断为没有返回值

<img src="https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/d54abea603554da3a2b998bbf727de04~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=494&h=138&s=13079&e=png&b=1f1f1f" alt="image.png" width="50%" />

### void 类型

void 类型表示函数没有返回值。如果你学过 c 语言，那你一定不会陌生。

在 TS 中`void`并不代表不能写`return`语句，它允许返回`undefined`或`null`（`null`需要关闭严格模式），因为它俩在 TS 看来也是相当于没有返回值。

```ts
function fn3(a: number, b: number): void {
  return undefined;
}
```

但是如果你返回其它类型就会报错

```ts
function fn(a: number, b: number): void {
  return a + b; //报错
}
```

<img src="https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/76765d6f43fc4fabb8edf79d0ebc8efa~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=569&h=162&s=18617&e=png&b=272728" alt="image.png" width="70%" />

### 函数重载

有些函数可以接受不同类型或不同个数的参数，并且根据参数的不同，会有不同的函数行为。这种根据参数类型不同，执行不同逻辑的行为，称为函数重载。

这里我们想实现一个函数，不管你传入的是数组还是字符串，我都希望它返回一个反转后的结果

```ts
reverse("abc"); // 'cba'
reverse([1, 2, 3]); // [3, 2, 1]
```

首先我们逐一定义每一种情况的类型

```ts
function reverse(str: string): string;
function reverse(arr: any[]): any[];
```

然后给予函数完整的类型声明

```ts
function reverse(stringOrArray: string | any[]): string | any[] {
  if (typeof stringOrArray === "string")
    return stringOrArray.split("").reverse().join("");
  else {
    return stringOrArray.slice().reverse();
  }
}
```

效果如下

```ts
console.log(reverse(["🐂", "🐑", "🐎", "🐒"])); //[ '🐒', '🐎', '🐑', '🐂' ]
console.log(reverse("牛羊马猴")); //猴马羊牛
```

## type 命令

TS 允许我们用 type 来定义一个类型别名，我们习惯使用大驼峰命名方式进行命名。

```ts
type Name = string;
let name: Name = "阳阳羊";
```

这样`Age`就能像使用`number`一样作为类型来使用

别名不允许重名，拿值类型来举例

```ts
type Color = "红";
type Color = "绿"; //报错
```

如果一些类型定义很冗长，我们往往会用 type 命令为函数类型定义一个别名。

```ts
type MyFunc = (a: string) => void;

const fn: MyFunc = function (a) {
  console.log("hello " + a);
};
```

## interface 接口

interface 是对象的模板，可以看作是一种类型约定，中文译为“接口”。使用了某个模板的对象，就拥有了指定的类型结构。

### 属性

```ts
interface Person {
  name: string;
  age: number;
}
```

这里我们定义了一个接口`Person`，两个属性`name`和`age`，任何实现这个接口的对象都必须具有这两个属性，并符合规定类型。

```ts
const me: Person = {
  name: "阳阳羊",
  age: 21,
};
```

如果属性和类型有一个不符就会报错。

如果你希望属性是可选的，则可以使用`?`可选操作符，只读可以使用`readonly`修饰符

```ts
interface Person {
  name?: "阳阳羊";
  readonly age: 21;
}
```

### 方法

除了属性外，接口还允许我们定义对象的方法

```ts
interface Person {
  say(x: string): string;
}
```

### 函数

接口也可以用来声明独立的函数

```ts
interface Add {
  (x: number, y: number): number;
}
const myAdd: Add = (x, y) => x + y;
```

### 继承

接口可以使用`extends`关键字实现继承

```ts
interface Animal {
  name: string;
  age: number;
}

interface Dog extends Animal {
  color: string;
}
```

`extends`关键字会从继承的接口里面拷贝属性类型，这样就不必书写重复的属性

如果子接口与父接口存在同名属性，那么子接口的属性会覆盖父接口的属性。前提是不能有冲突，属性方法他们的类型得是一致的，不然会报错

```ts
interface Animal {
  name: string;
  age: number;
}

interface Dog extends Animal {
  color: string;
  age: bigint;
}
```

这里存在同名属性，但是类型不一致

![image.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/3634c66ca59942e287f2cde4ae39687c~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=519&h=226&s=30080&e=png&b=272728)

### 合并

多个同名接口会合并成一个接口

```ts
interface Box {
  height: number;
  width: number;
}

interface Box {
  length: number;
}
```

两个`Box`接口会合并成一个接口，同时有`height`、`width`和`length`三个属性。前提仍然是不能有冲突。

### 和 type 的区别

（1）`type`能够表示非对象类型，而`interface`只能表示对象类型（包括数组、函数等）。

（2）`interface`可以继承其他类型，`type`不支持继承。

（3）同名`interface`会自动合并，同名`type`则会报错。也就是说，TypeScript 不允许使用`type`多次定义同一个类型。

## 参考

- [阮一峰 TypeScript 教程](https://typescript.p6p.net/typescript-tutorial/types.html)
- [TypeScript 官方文档](https://www.typescriptlang.org/)

## 最后

以上是对 TS 进阶类型的基本介绍，内容相对于基础，对于新同学来说比较友好，后面我会继续更新进阶知识，关于类、泛型、Enum 类型等等，可以收藏本站，我会持续更新。

已将学习代码上传至 [github](https://github.com/YangyangU/CodeSpace)，欢迎大家学习指正！
