---
title: 只会Vue，快速上手React（React Router篇）
date: 2024-06-12 16:00:00
tags: [React]
categories: React
index_img: /img/default.png
banner_img: /img/default.png
---

## 前言

在上文中（[只会 Vue 的我，上班第一天就要我写 React+TS，是种什么样的体验？](https://juejin.cn/post/7377320107929829388)），我们讲述了 React 从项目创建到如何实现自定义 Hooks ，比较偏基础，今天这篇文章将带大家学习 React Router。

## React Router

如果你对 Vue Router 有了解的话，那么 React Router 肯定也不在话下，因为他们实在太相似了。我们知道前端路由是一个在单页应用中管理不同页面的导航，无需向服务器发送请求重新加载整个页面。即一个`path`对应一个`component`。所以 Router 在不管是 Vue 还是 React 亦或是 Angular 都是前端单页应用不可或缺的一部分。

### 安装

选择自己喜欢的包管理工具，这里需要注意安装的是 `react-router-dom`，后面有个 dom，跟 `react-dom` 一个道理，React Router 库分为多个包，其中`react-router-dom`是专门用于在浏览器中运行 React 的路由方案，适合 Web 开发。如果是移动端开发应该用`react-router-native`。

```bash
npm install react-router-dom
```

### 路由模式

我们知道在 Vue 中有两种路由模式 History 跟 Hash 模式，React 同样。下表是我对这两者的简单对比，想必大家背八股的时候就已经熟记于心了。

| 模式    | url         | 原理                          | 后端支持 |
| ------- | ----------- | ----------------------------- | -------- |
| history | url/login   | history 对象 + pushState 事件 | 需要     |
| hash    | url/#/login | hashChange 事件               | 不需要   |

### 基本使用

主要文件目录如下

```lua
src/
|-- pages/
|   |-- Login.jsx
|   |-- About.jsx
|-- router/
|   |-- index.jsx
|-- App.jsx
|-- main.jsx
```

我们一般都在`src`下新建目录`router`，然后在`index.jsx`中创建路由。

```jsx
// router/index.jsx
import { createBrowserRouter } from "react-router-dom";
import Login from "@/views/Login";
import Home from "@/views/Home";

const router = createBrowserRouter([
  //创建 history 路由
  {
    path: "/home",
    element: <Home />,
  },
  {
    path: "/login",
    element: <Login />,
  },
]);

export default router;
```

`react-router-dom`提供`createBrowserRouter`API 跟 Vue Router 提供的`createWebHistory`一样，允许我们创建 History 模式路由，Hash 模式 React 与 Vue 分别对应`createHashRouter`与`createWebHashHistory`，如果记混了可以自行查文档。

```js
// app.jsx
import "./App.css";
import { RouterProvider } from "react-router-dom";
import router from "./router";

function App() {
  return <RouterProvider router={router}></RouterProvider>;
}

export default App;
```

创建好了就该使用了，在 Vue 中是直接在`main.js`中 App 根组件直接`use`掉，而在 React 中则有所不同， React Router 专门提供一个组件 `RouterProvider`，用于将路由配置传递给应用。它接受一个`router`prop，该 prop 是在刚刚创建的路由实例。

### 导航跳转

#### 声明式导航

通过在模板中使用`<Link />`组件，通过`to`属性指定要跳转的路径，跟 Vue 的`router-link`很类似，

🌰 现在我们要实现从登录页跳转至首页：

```js
import React from "react";
import { Link } from "react-router-dom";

const Login = () => {
  return (
    <div>
      Login
      <Link to="/home">跳转至首页</Link>
    </div>
  );
};

export default Login;
```

我们先手动去到`/login`路径。

<img src="https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/a6feb8ec6848414e84874849c93a663c~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=657&h=364&s=25231&e=gif&f=6&b=fefefe" alt="GIF 2024-6-12 10-42-24.gif" width="50%" />

#### 编程式导航

通过`useNavigate`钩子拿到导航方法，再通过调用命令式进行跳转。类似于 Vue 的`router.push()`

🌰 一样的场景:

```jsx
import React from "react";
import { useNavigate } from "react-router-dom";

const Login = () => {
  const navigate = useNavigate();
  return (
    <div>
      <h1>Login</h1>
      <button onClick={() => navigate("/home")}>跳转</button>
    </div>
  );
};

export default Login;
```

通过事件驱动跳转，更加灵活。

<img src="https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/8f6cfd379e4b4af28cedbeccd34e2914~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=620&h=402&s=15187&e=gif&f=4&b=fefefe" alt="GIF 2024-6-12 10-51-15.gif" width="50%" />

### 导航传参

React 提供了两个钩子允许我们进行导航传参。

#### useSearchParams 传参

这里我们在 Login 页面进行跳转，并用`?`+`参数名`=`参数`拼接，多个参数用`&`拼接。

```jsx
// login.jsx
import React from "react";
import { useNavigate } from "react-router-dom";

const Login = () => {
  const navigate = useNavigate();
  return (
    <div>
      <h1>Login</h1>
      <button onClick={() => navigate("/home?id=1&name=yangyang")}>
        跳转传参
      </button>
    </div>
  );
};

export default Login;
```

`useSearchParams`运行我们解构出一个对象，这个对象包含了所有通过`?`后的参数

```jsx
// home.jsx
import React from "react";
import { useSearchParams } from "react-router-dom";

const Home = () => {
  const [params] = useSearchParams();
  console.log(params);
  return (
    <div>
      <h1>Home</h1>
      <div>id:{params.get("id")}</div>
      <div>name:{params.get("name")}</div>
    </div>
  );
};

export default Home;
```

我们打印 params 对象看看

<img src="https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/70e2dac1326f474990f1a4f45d64c293~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=931&h=692&s=98095&e=png&b=ffffff" alt="image.png" width="70%" />

它是一个`URLSearchParams` 对象，包含了一系列方法，我们可以使用`get`方法查询参数

<img src="https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/3f193eca1b6147709683f5f511bfeab0~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=975&h=431&s=59017&e=gif&f=10&b=fefefe" alt="GIF 2024-6-12 12-16-59.gif" width="50%" />

#### useParams

这里我们还是在 Login 页面进行跳转，我们直接在路径后面继续接`/`+`参数`，

```jsx
// login.jsx
import React from "react";
import { useNavigate } from "react-router-dom";

const Login = () => {
  const navigate = useNavigate();
  return (
    <div>
      <h1>Login</h1>
      <button onClick={() => navigate("/home/1/yangyang")}>跳转传参</button>
    </div>
  );
};

export default Login;
```

不仅如此，还需要修改`router`配置，将补全`path`用`:`+`参数名`，这样才能一一匹配。

```jsx
{
    path: '/home/:id/:name',
    element: <Home />,
},
```

最后，在 Home 页面接收参数，调用`useParams`得到一个对象，对象的每个属性就是我们传递的参数

```jsx
// home.jsx
import { useParams } from "react-router-dom";

const Home = () => {
  const params = useParams();
  console.log(params);
  return (
    <div>
      <h1>Home</h1>
      <div>id:{params.id}</div>
      <div>name:{params.name}</div>
    </div>
  );
};

export default Home;
```

<img src="https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/1cf0aba3e4f04eb2a44b825b255ab089~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=930&h=146&s=15495&e=png&b=ffffff" alt="image.png" width="70%" />

<img src="https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/122da242b0d642c8a54c722b65722c58~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=819&h=431&s=22894&e=gif&f=3&b=fefefe" alt="GIF 2024-6-12 12-30-26.gif" width="50%" />

### 嵌套路由

跟 Vue 一样，React 同样允许你在一个路由组件的`children`属性中配置子路由。

```js
// router/index.js
const router = createBrowserRouter([
  {
    path: "/home",
    element: <Home />,
    children: [
      {
        index: true, //默认二级路由
        path: "board",
        element: <Board />,
      },
      {
        path: "about",
        element: <About />,
      },
    ],
  },
  {
    path: "/login",
    element: <Login />,
  },
]);
```

二级路由的`path`如果不写`/`，会默认拼接在父级路由后面，配置`index:true`表示默认显示该路由。

```js
// home.js
import { Link, Outlet } from "react-router-dom";

const Home = () => {
  return (
    <div>
      <h1>Home</h1>
      <Link to="board">面板</Link>|<Link to="about">关于</Link>
      <Outlet />
    </div>
  );
};

export default Home;
```

我们通过`Link`组件进行跳转，`Outlet`组件是二级路由的出口，你定义在哪，那么组件就会在哪显示，跟 Vue 的`router-view`类似。

<img src="https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/7dea9a7b1b404ce2883e79efc33d5304~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=719&h=437&s=38430&e=gif&f=16&b=fefefe" alt="GIF 2024-6-12 15-00-00.gif" width="50%" />

### 懒加载

React 提供了`React.lazy`和`Suspense`组件来实现懒加载。`React.lazy`用于定义懒加载的组件，`Suspense`用于在组件加载时显示。

```js
import { createBrowserRouter } from "react-router-dom";
import { Suspense, lazy } from "react";

const Home = lazy(() => import("@/pages/Home"));
const Login = lazy(() => import("@/pages/Login"));
const Board = lazy(() => import("@/pages/Board"));
const About = lazy(() => import("@/pages/About"));

const router = createBrowserRouter([
  {
    path: "/login",
    element: (
      <Suspense fallback={"加载中"}>
        <Login />
      </Suspense>
    ),
  },
  {
    path: "/home",
    element: (
      <Suspense fallback={"加载中"}>
        <Home />
      </Suspense>
    ),
    children: [
      {
        index: true,
        path: "board",
        element: (
          <Suspense fallback={"加载中"}>
            <Board />
          </Suspense>
        ),
      },
      {
        path: "about",
        element: (
          <Suspense fallback={"加载中"}>
            <About />
          </Suspense>
        ),
      },
    ],
  },
]);

export default router;
```

通过套一层`lazy`就可以实现懒加载，`Suspense`提供 loading 内容。

## 总结

不管是 Vue 还是 React ，前端路由的大致理念都是一样的，我们如果知道一个，很容易融汇贯通。还是那句话，多看文档，多敲代码，多实践！

## 参考

- [React-中文官网](https://zh-hans.react.dev/learn)
- [React-Router-官网](https://reactrouter.com/en/main/start/overview)

## 最后

已将学习代码上传至 [github](https://github.com/YangyangU/CodeSpace)，欢迎大家学习指正！
