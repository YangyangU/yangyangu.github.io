---
title: 只会Vue的我，上班第一天就要我写React+TS，是种什么样的体验？
date: 2024-06-07 16:00:00
tags: [React]
categories: React
index_img: /img/react.png
banner_img: /img/default.png
---

## 前言

**只会 Vue 的我，上班第一天就要我写 React+TS，是种什么样的体验？**

入职第一天，坐公交来到指定报道处，拿号排队准备办理入职，拿装备去工位，认识 Leader 跟 Mentor，自己看文档装环境，屁股都没坐热，mentor 就问我技术栈是什么，我说 Vue，他又问 React 用过吗？我开始有不祥的预感，他反手就甩了个链接，叫我装完环境看一下，装完 vscode、node、git，我直接 pull 下来，看了一下目录 pnpm 咱也没用过啊，怎么全是 ts ，用的 Vite？这个我熟，直接安装依赖 run dev！

首先，这个项目就两个页面，我第一感觉是：怎么看着还不如我自己的项目好看 😢，但是当我打开代码，脑子直接缺氧，啥都看不懂。什么 type 什么 useMemo，闻所未闻。

赶紧开始自学 React + TS ……

晚上回到出租屋，突然有一种压力油然而生，我是不是太蠢了，Mentor 会不会看不起我 ……

看了一天视频，文章，我觉得我已经了解了 React ，至少不会看不懂了。

这篇文章就来总结一下我是如何快速上手的，分享一下我心得体会。如果你也跟我一样，下面我分享一些我的经验，希望能够对您有所帮助。

## 项目创建

在 Vue 中，我们常用的脚手架是 Vue-CLI 以及尤大大推荐的 Vite ；在 React 中，我们通常使用 creat-react-app（CRA）创建项目，当然 Vite 同样也支持我们创建 React 项目。

### 使用 CRA

```bash
npx create-react-app my-react-app
```

### 使用 Vite

```bash
yarn create vite my-react-app --template react
```

### 其他创建方式

参考 [React | 启动一个新项目](https://zh-hans.react.dev/learn/start-a-new-react-project)

### 注意

值得注意的是使用 cra 创建的项目中，JS 文件就是正常的`.js`后缀，而 Vite 创建项目的文件后缀是`.jsx`。

- Vite 基于 ESModule，它提供了对 JSX 的原生支持，使用`.jsx`作为源文件的后缀，以便我们直接编写 JSX 语法的 React 组件。
- CRA 默认没有启用 JSX 预处理器，而是通过 Babel 和 Webpack 等工具来处理 JSX 语法。

在写法上没有区别，不用考虑太多。

## 项目目录

以下是 Vite 创建的项目目录结构：

![image.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/af365556f65d4b75ba8b3656d79b105f~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=195&h=338&s=14100&e=png&b=252526)

其中入口文件是`main.jsx`，如果是 CRA 创建的项目则是`index.js`。

让我来看看它的代码：

```jsx
import React from "react";
import ReactDOM from "react-dom/client";
import App from "./App.jsx";
import "./index.css";

ReactDOM.createRoot(document.getElementById("root")).render(
  <React.StrictMode>
    <App />
  </React.StrictMode>
);
```

这跟 Vue 的`main.js`的作用一样，将`App`根组件挂载到`id`为`root`的 DOM 元素上。

**我猜你可能会想，这里引入一个 React 又引入一个 ReactDom 干嘛，它们两个有什么区别？**

我们在`package.json`中也可以看到这两个依赖。

![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/0df638d47a88442e8f77dabd6a41a1c0~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=292&h=115&s=5549&e=png&b=1e1e1e)

这是因为 React 作者将 React 源码拆成了两部分：

- `React` 库提供构建用户界面的各种 API 和组件，比如创建 React 组件、处理组件生命周期、状态管理、事件处理等核心功能。

- `ReactDom` 库是 React 的 DOM 渲染器，只负责将 React 组件渲染到浏览器的 DOM 中，提供一些管理组件的更新、挂载和卸载等操作的 API。

## JSX

我们`return`出来的 JSX 模板，如果要换行的话，需要用`()`包裹一下，然后这个 JSX 表达式必须具有一个父元素，什么意思呢？就是下面这种写法会报错，JSX 表达式规定了最外层只能有一个父元素，这种情况下我们一般会在最外面套一个`div`或者空标签`<></>`。

```jsx
return (
  {/*报错*/}
  <div>
    <h1>hello</h1>
  </div>
  <div>
    <h1>你好</h1>
  </div>
)

return (
  <>
   <div>
    <h1>hello</h1>
   </div>
   <div>
    <h1>你好</h1>
   </div>
  </>
)
```

在 JSX 表达式允许我们只能在`{}`中写 JS 代码

## 创建组件

在 React 中有两种创建组件的方式，函数组件和类组件，类组件是。现在界内大部分都使用函数组件，函数组件相比于类组件，性能更好，React 18 虽然还支持类组件，但是更推荐使用函数组件。

### 函数组件

#### 语法

函数组件是通过函数来定义的，函数的返回值是 JSX 模板，可以使用 `function` 关键字或箭头函数定义。

```jsx
const App = () => {
  return (
    <div>
      <h1>Hello Function Component</h1>
    </div>
  );
};

export default App;
```

![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/2d21eaa75fc34fcaac6638fb39e28de6~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=454&h=157&s=8769&e=png&b=ffffff)

#### 状态管理

函数组件通过 React 提供的 Hooks 函数（官方提供的一般以`use`开头的 API），比如我们通常使用`useState` 钩子来管理状态。

`useState` 返回一个由两个值组成的数组：

1.  当前的`state`，初始值为传递给`useState`的参数。
2.  `set`  函数，它可以让你将`state`更新为不同的值并触发重新渲染。

🙋‍ 举个例子 🌰，这里我们实现一个计数器，点击`count`自增`1`：

```jsx
import { useState } from "react";

const App = () => {
  let [count, setCount] = useState(0);
  const handleClick = () => {
    setCount(count + 1);
  };
  return (
    <div>
      <h1 onClick={handleClick}>{count}</h1>
    </div>
  );
};

export default App;
```

这里我们解构得到`count`跟它的`set`方法，我们每次点击触发`handleClick`函数，函数内部通过`set`方法修改状态并重新渲染。

<img src="https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/91d93bd5149b47a095840c25ac501823~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=538&h=272&s=25427&e=gif&f=27&b=fefefe" alt="GIF 2024-6-6 17-23-01.gif" width="50%" />

#### 生命周期

React 16.8 之前，函数组件没有生命周期方法，但是从 React 16.8 开始，可以使用`useEffect` 这个 Hook 函数来执行类似生命周期的逻辑，用于替代 `componentDidMount`、`componentDidUpdate` 和 `componentWillUnmount`这些方法。

##### useEffect

`useEffect`是 React 中用于执行副作用操作的 Hook，并且具有类似于生命周期方法的功能。

`useEffect` 接受两个参数：副作用函数和依赖数组。

1.  **副作用函数**：第一个参数是一个函数，用于执行副作用操作。
1.  **依赖数组**：第二个参数是一个数组，包含了副作用函数中所依赖的变量。如果省略这个参数，那么副作用函数会在每次组件重新渲染时都执行，可以充当`componentDidUpdate`这个生命周期；如果传入空数组 `[]`，则副作用函数只会在组件挂载时执行，相当于 `componentDidMount`；如果依赖数组中包含了某些变量，则只有这些变量发生变化时，副作用函数才会重新执行。如果我们在其中`return`一个函数，这个函数将会在组件卸载时除非，相当于`componentWillUnmount`。

我总结了一下副作用函数执行时机与依赖项的关系如下：

| 依赖项         | 副作用函数执行时机                  |
| -------------- | ----------------------------------- |
| 没有依赖项     | 组件初始渲染 + 组件更新时执行       |
| 空数组依赖项   | 只在初次渲染时执行一次              |
| 添加特定依赖项 | 组件初始渲染 + 特定依赖项变化时执行 |

举例说明 🌰：

```jsx
//没有依赖项数组
import React, { useState, useEffect } from "react";

const App = () => {
  const [count, setCount] = useState(0);
  useEffect(() => {
    console.log("组件更新");
  });

  return (
    <div>
      <p>点击了 {count} 次</p>
      <button onClick={() => setCount(count + 1)}>点击</button>
    </div>
  );
};

export default App;
```

这里我们没有依赖项数组，刷新页面，组件初次渲染打印（打印两次是因为 React 源码自己执行了一次，应该是跟严格模式有关，这里我们不用深究），每次点击都会打印，可以充当`componentDidUpdate`生命周期。

<img src="https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/4ed443e30b2e45629bbe20cf6064e775~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=696&h=468&s=52307&e=gif&f=21&b=fefefe" alt="GIF 2024-6-6 19-32-33.gif" width="50%" />

---

依赖项为空数组呢？

```jsx
//依赖项为空数组呢？
import React, { useState, useEffect } from "react";

const App = () => {
  const [count, setCount] = useState(0);
  useEffect(() => {
    console.log("组件挂载");
  }, []);

  return (
    <div>
      <p>点击了 {count} 次</p>
      <button onClick={() => setCount(count + 1)}>点击</button>
    </div>
  );
};

export default App;
```

这里我们的依赖项数组为空，刷新页面，只在初次渲染时打印，点击不打印，可以充当`componentDidMount`生命周期。

<img src="https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/746f9e4ce2254e8cbe223791767e28c3~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=696&h=468&s=68204&e=gif&f=28&b=fefefe" alt="GIF 2024-6-6 19-33-14.gif" width="50%" />

---

依赖项数组不为空呢？

很容易联想到，如果数组里面有会发生改变的项，则会触发副作用函数，否则不会触发，这里就不演示了。

---

来看最后一个，在副作用函数中`return`一个函数：

```jsx
import React, { useState, useEffect } from "react";

const App = () => {
  const [count, setCount] = useState(0);

  useEffect(() => {
    console.log("组件更新");
    return () => {
      console.log("组件卸载");
    };
  }, [count]);

  return (
    <div>
      <p>点击了 {count} 次</p>
      <button onClick={() => setCount(count + 1)}>点击</button>
    </div>
  );
};

export default App;
```

这里我们`return`一个函数打印卸载，表示组件卸载时会执行的操作。我们通常在这里进行组件卸载时的执行的操作，可以充当`componentWillUnmount`生命周期。

<img src="https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/84e36073cdfe4f27b271fd53ded4a814~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=800&h=567&s=51838&e=gif&f=22&b=fefefe" alt="GIF 2024-6-6 19-51-19.gif" width="50%" />

一个很常见的例子 🌰：

我们通常在组件卸载时清除一些副作用函数，如定时器

```jsx
import React, { useState, useEffect } from "react";

const App = () => {
  const [count, setCount] = useState(0);

  useEffect(() => {
    let timer = setInterval(() => {
      setCount(count + 1);
    }, 1000);
    return () => {
      clearInterval(timer);
    };
  }, [count]);

  return (
    <div>
      <p>点击了 {count} 次</p>
      <button onClick={() => setCount(count + 1)}>点击</button>
    </div>
  );
};

export default App;
```

如果我们不及时清除定时器，会导致资源浪费。

而且由于 `setCount` 是一个异步操作，它不会立即更新 `count` 的值，而是在下一次渲染时才会生效。因此，即使你设置了定时器每隔一秒钟执行一次，但 `count` 的值并不会每秒增加一次。相反，当定时器触发时，`count` 的值仍然是上一次渲染时的值。等一段时间后就会看到下面这个情况：

<img src="https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/29923fc094d648879b1013c2c93e0f02~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=572&h=324&s=27666&e=gif&f=18&b=fefefe" alt="GIF 2024-6-6 20-06-38.gif" width="50%" />

### 类组件

😁**可以不用但不能不学！**

#### 语法

类组件通过继承 `React.Component` 或其子类来创建，类组件中有一个 `render()` 方法，用来返回 JSX 语法。

拿代码说事：

```jsx
import { Component } from "react";

class App extends Component {
  constructor() {
    super();
  }
  render() {
    return (
      <div>
        <h1>Hello Class Component</h1>
      </div>
    );
  }
}

export default App;
```

类组件的语法就是声明一个类，这个类继承`React.Component` 或者子类，`render()`方法返回 JSX 模板。然后将我们的 App 组件挂载到页面上就可以了。

![image.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/04452e5cd75c48dc9e2c1f9cd6f7d1a2~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=458&h=167&s=9583&e=png&b=ffffff)

#### 状态管理

类组件拥有内部状态`state`，通过 `this.state` 来访问组件的状态，使用 `setState()`方法来更新状态，触发组件的重新渲染。

```jsx
import { Component } from "react";

class App extends Component{
  constructor(){
    super();
    this.state={
      name:"张三"
    }
  }
  handleClick(){

  }
  render(){
    return(
      <div>
        <h1 onClick={()=>this.setState({name:"李四"})>{this.state.name}</h1>
      </div>
    )
  }
}

export default App;
```

我们绑定`onClick`事件，当点击时修改`name`的状态。

<img src="https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/f05edfaefa984490ba816a83af73136d~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=467&h=332&s=17537&e=gif&f=5&b=fefefe" alt="GIF 2024-6-6 17-04-25.gif" width="30%" />

#### 生命周期

类组件中可以使用生命周期方法，常见的有`componentDidMount`、`componentDidUpdate`、=跟`componentWillUnmount`，它们分别对应 Vue3 中的 `onMounted`、`onUpdated` 跟 `onBeforeUnmount`。

这个网站可以很清楚看到 React 的生命周期，[React 生命周期](https://projects.wojtekmaj.pl/react-lifecycle-methods-diagram/)，这里就不过多赘述。

![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/1c876571316c4779bf0528aca4cef60a~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=1180&h=424&s=40943&e=png&b=fbf8f7)

### 组件通讯

在 Vue 中组件传值一般通过`props & emit`、`provide & inject`以及 Vue2 中的`EventBus`，或者我们可以直接使用状态管理工具`Vuex`或`Pinia`，React 其实大差不差，同样也有自己的状态管理工具。

#### 父传子

父组件通过`props`传递任意的数字、字符串、布尔值、数组、对象、函数甚至是 JSX，子组件通过`props`参数接收。

🙋‍♀️🌰：

```jsx
const Son = (props) => {
  return (
    <>
      <p>{props.name}</p>
      <p>{props.age}</p>
      <p>{props.cb()}</p>
      <p>{props.child}</p>
    </>
  );
};

const Father = () => {
  return (
    <Son
      name={"阳阳羊"}
      age={20}
      cb={function cb() {
        console.log(123);
      }}
      child={<span>i am jsx</span>}
    ></Son>
  );
};
```

<img src="https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/2f7493c702384844b509095a3f5f3972~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=789&h=415&s=27428&e=png&b=fefefe" alt="image.png" width="50%" />

⚠️ 需要注意的是：子组件只能读取`props`中的数据，不能直接进行修改，父组件的数据只能由父组件修改，或者通过父组件传过来的函数进行修改。如果在子标签中嵌套元素，可以在子组件的`props.children`中获取。

#### 子传父

子传父通过最原始的回调函数方式，父组件给子组件传一个函数，子组件调用这个函数然后传入实参，在函数内部用`set`方法修改父组件的状态。

🙋‍♀️🌰：

```jsx
const Son = (props) => {
  return <button onClick={() => props.cb("小羊")}>点击传递</button>;
};

const Father = () => {
  let [name, setName] = useState("阳阳羊");
  let cb = (name) => {
    setName(name);
  };
  return (
    <>
      <Son cb={cb}></Son>
      {name}
    </>
  );
};
```

<img src="https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/44f24ffe31fa4c8b84aada591cf2e534~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=549&h=284&s=14214&e=gif&f=3&b=fdfdfd" alt="GIF 2024-6-7 11-20-34.gif" width="50%" />

⚠️ 这里需要注意：`onClick`事件处理程序是一个函数，写成`onClick={() => props.cb('小羊')}`时，实际上创建了一个匿名函数，当点击按钮时会调用这个函数，然后调用`props.cb('小羊')`，如果直接写成`onClick={props.cb('小羊')}`，在渲染时就会立即调用`props.cb('小羊')`，而不是在点击时调用。

#### 兄弟组件通信

兄弟组件通信贯用手段就是**状态提升**，说白话就是将数据都交给父组件，然后父组件作为中间商派发给大家。

🙋‍♀️🌰：

```jsx
const Son1 = (props) => {
  return (
    <>
      子组件1-
      <button onClick={() => props.cb("小羊")}>传给子组件2</button>
    </>
  );
};

const Son2 = (props) => {
  return (
    <>
      <p>子组件2-{props.name}</p>
    </>
  );
};

const Father = () => {
  let [name, setName] = useState("Son2");
  const cb = (newName) => {
    setName(newName);
  };
  return (
    <>
      <Son1 cb={cb}></Son1>
      <Son2 name={name}></Son2>
    </>
  );
};
```

这里子`组件1`通过触发父组件传递过来的函数，并传递参数，然后父组件接收到参数修改数据源，传递给`组件2`。这样就完成了兄弟组件通信。

<img src="https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/3eb12209347142b59eea4b418c9507dc~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=547&h=273&s=24884&e=gif&f=6&b=fdfcfc" alt="GIF 2024-6-7 11-16-47.gif" width="50%" />

#### 夸层组件通讯

思路是将两个组件放在同一个上下文中就能实现数据共享。步骤如下：

1. 使用`createContext`方法创建一个上下文对象，将组件`Provider`，`Consumer`抛出

```js
import React from "react";
const { Provider, Consumer } = React.createContext();

export { Provider, Consumer };
```

2. 在顶层组件(App)中通过`Provider`组件提供数据

3. 在底层组件(B)中通过`Consumer`组件获取对应数据

```jsx
import { Provider, Consumer } from "./provider";

const ComponentB = () => {
  return (
    <Consumer>
      {(value) => (
        <div>
          <h1>底层组件——{value}</h1>
        </div>
      )}
    </Consumer>
  );
};

const App = () => {
  const value = "顶层组件的数据";
  return (
    <Provider value={value}>
      <h1>顶层组件</h1>
      <ComponentB />
    </Provider>
  );
};
```

效果如下：

<img src="https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/2c488273f449470fbfbdccb8e36b31d3~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=813&h=369&s=31043&e=png&b=fefefe" alt="image.png" width="50%" />

#### 状态管理工具

常见的 React 状态管理工具有 Redux、Mobx、Rcoil 等等，如果有感兴趣的同学，我可以单独出一篇文章详细聊聊。本篇就不展开聊了。

## 自定义 Hooks

除了 React 官方提供的一些 Hooks 之外，还允许我们自定义自己的 Hooks，我们需要遵循以下规则：

1. 声明以`use`开头的函数
2. 在函数体内封装可复用的逻辑
3. 把组件中使用到的状态`return`出去
4. 在哪里用到就在里面执行解构

🙋‍♀️🌰：

我们设计来设计一个开关 Hooks，作用是对值取反，从而控制元素的显示隐藏：

```jsx
import { useState } from "react";

function useToggle() {
  const [value, setValue] = useState(true);
  const toggle = () => {
    setValue(!value);
  };
  return [value, toggle];
}

const App = () => {
  const [value, toggle] = useToggle();
  return (
    <div>
      <h1>{value ? "开" : "关"}</h1>
      <button onClick={toggle}>按钮</button>
    </div>
  );
};
export default App;
```

这里我们借助`useState`进行封装，将需要用到的变量进行抛出，在需要使用的地方解构拿到，通过三目运算符进行显示隐藏。

<img src="https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/c183f8bc55224444b6bb384239bd4c08~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=613&h=369&s=29609&e=gif&f=12&b=fefefe" alt="GIF 2024-6-7 12-07-46.gif" width="50%" />

### Hooks 使用规则

Hooks 并不是然后场景下都能用，像比如在类组件中是不能直接使用 Hooks 的，除非使用高阶函数进行包装，以及其他许多规则：

- 不能在组件外部使用
- 不能在判断语句中使用

详见 [React 中文官网](https://zh-hans.react.dev/reference/rules/rules-of-hooks)

## 总结

还是那句话，不管是 Vue 跟 React 都有自己的优缺点，我们不能断言说哪个好哪个差，在我看来 Vue 更适合第一次接触前端框架的小白，它封装了许多 API，对新手很友好，上手即用；而 React 相对复杂，更考验开发者的思维，对于一些复杂需求能够自定义进行封装，更接近原生 JS 开发。大家按需求选择就好。

## 参考

[React-中文官网](https://zh-hans.react.dev/learn)

## 最后

已将学习代码上传至 [github](https://github.com/YangyangU/CodeSpace)，欢迎大家学习指正！
