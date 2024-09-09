---
title: React 对比 Vue 如何做路由鉴权？
date: 2024-09-9 16:00:00
tags: [React, Vue]
categories: React
index_img: /img/路由鉴权.jpg
banner_img: /img/default.png
---


## 前言

学过 Vue 的小伙伴肯定知道，在 Vue 中，路由守卫可以帮助我们一站式进行路由鉴权。我们只需要在全局前置路由守卫 (`beforeEach`) 钩子函数中判断逻辑即可，方便快捷。

但是在 React 中，如何实现类似的功能呢？本文将对比 Vue 和 React 路由鉴权的方式，带你了解如何在 React 中配置路由并实现路由鉴权。

## 在Vue中如何实现

以 Vue3 为例

### 配置路由

这里我们简单配置了三个路由，首页、关于和登录页面，除了登录页外都需要登录。

```js
// router/index.js
import { createRouter, createWebHistory } from 'vue-router';
import Home from '@/views/Home.vue';
import About from '@/views/About.vue';
import Login from '@/views/Login.vue';

const routes = [
    {
        path: '/',
        name: 'Home',
        meta: {
            title: '首页',
            needLogin: true,
        },
        component: Home,
    },
    {
        path: '/about',
        name: 'About',
        meta: {
            title: '关于',
            needLogin: true,
        },
        component: About,
    },
    {
        path: '/login',
        name: 'Login',
        meta: {
            title: '登录',
        },
        component: Login,
    },
];

const router = createRouter({
    history: createWebHistory(),
    routes,
});

export default router;
```

这里我们在路由中添加了一个`meta`属性，它一般用来存储路由的元信息，什么意思，就是我们可以通过这个对象来判断某个路由是否需要登录权限、页面标题、特定布局等等信息。

这里我们设置 `meta.needLogin` 来标识某个路由是否需要登录权限，通过 `meta.title` 来设置页面标题，然后在路由守卫中动态更改 `document.title`即可。

### 路由守卫

为什么路由守卫能做？

路由守卫是 Vue Router 中用于控制路由导航行为的功能。它允许在用户从一个路由导航到另一个路由之前、期间或之后执行自定义逻辑。

而**全局前置路由守卫**是在每次路由导航开始前都会被调用。

这样我们只需在路由跳转前，即全局前置路由守卫中去判断要去的路由是否需要登陆，并且判断用户是否登录即可！！

```js
// main.js
import { createApp } from 'vue';
import App from './App.vue';
import router from './router';

const app = createApp(App);

router.beforeEach((to, from, next) => {
    const token = localStorage.getItem('my-token'); //用户登陆自动生成的token

    if (to.meta.title) {
        document.title = to.meta.title; //设置路由标题
    }

    if (to.meta.needLogin && !token) { // 如果需要登录但是未登录，跳转到登录页面
        next('/login');
    } else { //否则放行
        next();
    }
});

app.use(router).mount('#app');
```

在 Vue 中，实现路由拦截鉴权的核心是使用 Vue Router 提供的 `beforeEach` 全局前置守卫。在这个守卫函数中，我们可以根据路由的 `meta.needLogin` 属性，判断是否需要进行登录验证。如果用户未登录且访问了需要登录的页面，则重定向至登录页。


## 在React中怎么实现

Vue 中很容易实现，问题来了，React 中怎么做？

这里，我们使用 React Router 6的最新的路由配置写法，跟 Vue 简直不要太像

### 配置路由

首先第一步同样是配置路由

```js
// router/index.js
import { createBrowserRouter } from 'react-router-dom';

import Layout from '@/components/Layout';
import Home from '@/pages/Home';
import About from '@/pages/About';
import Login from '@/pages/Login';

export const routes = [
    {
        path: 'home',
        index: true,
        meta: {
            title: '首页',
            needLogin: true,
        },
        element: <Home />,
    },
    {
        path: 'about',
        meta: {
            title: '关于',
            needLogin: true,
        },
        element: <About />,
    },
    {
        path: '/login',
        element: <Login />,
    },
];

const router = createBrowserRouter(routes);

export default router;
```

这里路由对象写法跟 Vue Router 不能说很像，只能说一模一样了，道理都一样，设置`meta`属性，将需要登录的路由，设置`needLogin`为`true`。

### 1. 组件内部直接判断

跟 Vue 的区别来了，React 可没有路由守卫这一说法，但是道理都一样，我们只需在路由跳转页面展示前一刻判断是否需要登录以及用户是否登录。

但是React怎么实现在跳转前判断逻辑呢？

第一种方法，也是最好想的方法，直接在组件内部的 `useEffect` 钩子中进行判断，条件不满足重定向去登录即可。

我们知道`useEffect`在函数组件中充当特定的生命周期，就能在组件渲染前进行逻辑判断

以 Home 页面为例

```js
import React, { useEffect } from 'react';
import { useNavigate } from 'react-router-dom';

const Home = () => {
  const navigate = useNavigate();
  const token = localStorage.getItem('my-token');

  useEffect(() => {
    if (!token) {
      navigate('/login');
    }
  }, [navigate, token]);

  return <div>Home Page</div>;
};

export default Home;
```

这里我们使用`useEffect`钩子，当页面初次加载或依赖项更新的时候就会重新执行，能够达到我们目的。

我们直接在组件加载前判断用户是否登录，哪里需要登录就在哪里判断，甚至省去了路由配置中的`meta`属性。

⚠️但是这种方法太笨了，每个需要登录的页面都需要单独写一套这样的代码，很明显可以复用。于是便有了下面这个方法。

### 2. 高阶组件

高阶组件本质上是一个函数，它接受一个组件作为输入，然后返回一个新的组件。我们可以在这个新组件渲染之前，即在父组件中操作，进行鉴权逻辑判断以实现类似 Vue 前置路由守卫的功能。

```tsx
// router/Auth.jsx
import React from 'react';
import { Navigate, useLocation } from 'react-router-dom';
import { routes, RouterWithTabAuthObject } from './';

// 获取当前路由的元数据
const getRouteMeta = (path, routes) => {
    for (const route of routes) {
        if (route.path === path) return route;
        if (route.children) {
            const childRoute = getRouteMeta(path, route.children);
            if (childRoute) return childRoute;
        }
    }
    return undefined;
};

const Auth = (props) => {
    const location = useLocation();
    const token = localStorage.getItem('my-token');

    const currentRoute = getRouteMeta(location.pathname, routes);

    // 设置页面标题
    if (currentRoute?.meta?.title) {
        document.title = currentRoute.meta.title;
    }

    // 如果需要登录但没有 token，重定向到登录页
    if (currentRoute?.meta?.needLogin && !token) {
        return <Navigate to="/login" replace />;
    }
    
    return <>{props.children}</>;
};

export default Auth;
```

这里逻辑其实跟前置路由守卫是一致，只不过Vue帮你做了封装，显得代码没有那么多，比较简单。

我们来归纳一下，`beforeEach`可以直接通过`to.meta`直接拿到元数据，但是 React 需要判断当前`loaction`和`routes`路由对象进行查找才能找到对应的路由对象，即此处的`getRouteMeta`方法，如果有`children`还需要递归查找，其实 Vue 底层也是这样实现的。

然后通过条件判断和重定向实现鉴权，最后如果不需要重定向，正常渲染 `props.children`，即子组件。

#### 全局注册

`Auth`组件已经完成，还需要做一步操作，我们需要用这个高阶将所有路由包裹起来，才能发挥作用。

```jsx
import { Layout } from 'antd';
import { Outlet } from 'react-router-dom';
import Aside from '@/components/Aside';
import Header from '@/components/Header';
import Auth from '@/router/Auth';

const View = () => {
    return (
        <Auth>
            <Layout>
                <Aside />
                    <Header />
                    <Content>
                        <Outlet />
                    </Content>
            </Layout>
        </Auth>
    );
};

export default View;
```

这里我一般习惯于将`Auth`包裹`<Layout>`组件，如果你没用到`Layout`，包裹`App`也是可以的。

## 总结

- 在`Vue`中，实现路由拦截鉴权的核心就是使用 `beforeEach` 全局前置守卫。

- 在`React`中，暂且不考虑第一种组件内部判断的话，实现路由拦截鉴权就是将**每个**路由组件使用**高阶组件**进行包裹，在这个高阶组件里面进行权限相关逻辑的判断。

总而言之，其实不管是 Vue 还是 React 两者的核心思想相同，都是在用户访问受保护页面前，检查其登录状态，并根据检查结果决定是否允许访问，如何找到这的时间点才是关键！
