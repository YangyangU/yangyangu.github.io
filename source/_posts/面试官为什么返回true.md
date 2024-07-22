---
title: 面试官 ：[] == ! [] 为什么返回 true ？
date: 2023-12-04 16:00:00
tags: [JavaScript]
categories: JavaScript
index_img: /img/default.png
banner_img: /img/default.png
---

> 本文的讲解是建立在类型转换的基础上，如果对类型转换机制不了解的同学可以先阅读一下我的上一篇文章（[看了一天的官方文档，终于搞懂了 JS 类型转换机制 - 掘金 (juejin.cn)](https://juejin.cn/post/7307857937699405862)）

在 JavaScript 学习过程中，往往出现一些与我们常理相违背的结果，比如我们今天的论题`[] == ![]` ，它的返回的结果竟然是`true` ，这着实让初学者感到困惑。因为根据直觉，`[]`和`![]`两者应该是不相等的。面试官恰恰就会抓住这一点，对程序员们进行考察。

而本文将结合官方文档对该题进行深度剖析。

## 看看官方文档怎么说

### 关于 `==`

关于`==`，文档这样描述：[Annotated ES5 11.9.1](https://es5.github.io/#x11.9.1)

![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/974ce8282bb642cfbf0f14bdcff5f088~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=1037&h=284&s=36877&e=png&b=ffffff)

![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/6ad3699c380045b89d0047d7fec7df8b~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=1217&h=932&s=123351&e=png&b=ffffff)

根据官方文档，我总结了以下 `==` 的隐式转换规则，一些简单的就忽略掉了：

1.  **类型相同的比较：**

    （1）如果类型是 Undefined 或 Null，返回 `true`。

    ```js
    null == null; // true
    ```

    （2）如果一个是 +0，另一个是 -0，返回 `true`：

    ```js
    +0 == -0; // true
    ```

    （3）如果类型是对象，二者引用同一个对象，返回 `true`，反之返回 `false`。

    ```js
    {} == {}; // false
    ```

2.  **null 和 undefined 比较：** 仅当它们之间的比较时，返回 `true`。

```js
null == undefined; // true
```

3.  **NaN 比较：** NaN 与任何值比较都返回 `false`，包括 NaN 自己。

```js
NaN == NaN; // false
```

4.  **字符串与数字比较：** 如果其中一个操作数是字符串，另一个是数字，将字符串转换为数字，然后进行比较。

```js
"5" == 5; // true，将字符串 '5' 转换为数字 5 进行比较
```

5.  **布尔值与非布尔值比较：** 布尔值无法直接比较，需先将布尔值转换为数字（`true` 转换为 `1`，`false` 转换为 `0`），非布尔值也需要转换成数字再比较。

```js
true == 1; // true，将布尔值 true 转换为数字 1 进行比较
false == "123"; // false，将布尔值 false 转换为数字 0 ，将字符串 '123' 转换为数字 123 进行比较
```

6.  **对象与原始类型比较：** 如果其中一个是对象，另一个是原始类型，将对象通过 `ToPrimitive` 转换为原始类型，然后进行比较。_（即如果原始类型为字符串，则对象转换成字符串再比较；如果原始类为布尔值，则将布尔值与对象都转换成数字进行比较；如果原始类为数字，则将对象转换成数字进行比较。）_

举个例子：

```js
{} == 1;  //false
```

`ToPrimitive` 在我的上篇文章（[看了一天的官方文档，终于搞懂了 JS 类型转换机制](https://juejin.cn/post/7307857937699405862)）中详细介绍过，这里我们直接当做公式套用。

> ToPrimitive(obj, Number) ==> Number({})
>
> 1.  如果 obj 是基本类型，直接返回
> 1.  否则，调用 valueOf 方法，如果得到原始值，则返回
> 1.  否则，调用 toString 方法，如果得到原始值，则返回
> 1.  否则，报错

首先`{}`先被`ToPrimitive`转换成字符串`"[object Object]"`，就相当于直接判断 `"[object Object]" == 1`，字符串与数字的比较中，又要将字符串转换成数字，"[object Object]"转换成数字为 `NaN`，而`NaN` 与任何值比较都为 `false`。

所以 `{} == 1` 返回 `false`。

7. **关系转换图如下：**

<p align=center><img src="https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/eaf399a47c6f44ee8bcb54996ba8cc28~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=637&h=327&s=12357&e=png&b=ffffff" alt="image.png" width="70%" /></p>

---

回到`[] == ! [] `

这里判断`[]`与`![]`，两边都是对象，那怎么比呢，我们发现右边还有一个`!`，我们知道`!`的优先级是要大于`==`的，那么先进行非运算。

### 关于 `!`

关于`!`，文档这样描述：[Annotated ES5 11.4.9](https://es5.github.io/#x11.4.9)
![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/744fe88550fe4c1898cb0531dfd3ff05~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=804&h=264&s=29364&e=png&b=ffffff)

![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/04d61953adad4811aaae747aac5bc178~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=912&h=395&s=36695&e=png&b=fafafa)

上表为各数据类型转换成布尔值的结果。

简而言之，`!`这个运算符会进行两步操作：

> 1. **对`!`后面的操作数转换成布尔值**
>
> 2. **将这个布尔值取反**

于是我们判断`![]`，将`[]`转换成布尔值，我们在官方文档中就知道了，任何对象转换成布尔值都得到`ture`，然后在取反，得到`![]`为`false`。

## 最终判断

原式`[] == ![]`经过`!`运算将等式右边转换成了 `false`。即`[] == false`。

接着，根据`==`隐式转换规则，等式两边为对象和布尔，那么它们都应该转换成数字进行比较。

`[]`经过`ToPrimitive`会被转换成字符串`""`。再将等号两边的字符串`""`和布尔值`false`转换成数字 `0` ，得到 `0 == 0` 。打印得到 `true`。

# 总结

所以实际在进行`[] == ! []`判断时，在 JS 引擎内部，会将这行代码执行成这个样子：

```js
[] == ![]

[] == !true // 将空数组这个对象类型转换成布尔值

[] == false // ! 运算符对 true 进行取反

'' == false // 对 [] 进行 ToPrimitive 操作，返回一个空对象

0 == 0 // 将等号两边都转换成数字类型
```

# 最后

**看到这里，恭喜你又拿下一道大厂面试题！**

希望这篇文章能够为你提供帮助，如果你还有不懂之处，可以反复阅读，或者在评论区留言，学习是一个循序渐进，敢于试错的过程，我们顶峰相见！
