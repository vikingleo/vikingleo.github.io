---
title: React入门记录（2）——路由的基本使用
date: 2020-01-08 16:40:55
tags: react
category: React入门系列
---

# 前言
> `react-create-app`并没有集成`react router`，需要自行安装。附地址 [https://reacttraining.com/react-router/web/guides/quick-start](https://reacttraining.com/react-router/web/guides/quick-start)

# 安装
事实上，只需要安装`react-router-dom`即可
```bash
npm i react-router-dom
```

# 使用
基础的几个组件
```js
import {BrowserRouter, Router, Route, Link} from 'react-router-dom'
```
**BrowserRouter**
> 官方释义：<br> A <Router> that uses the HTML5 history API (pushState, replaceState and the popstate event) to keep your UI in sync with the URL.

> 使用HTML5历史记录API（pushState，replaceState和popstate事件）的<Router>，以使您的UI与URL保持同步。
<br> —— 谷歌翻译

使用后即是这样：
![](http://g-gallery.vkleo.com/img/20190330160605.png)

**App.js 改造后**
```js
import React, {Component} from 'react';
import {BrowserRouter, Route, Link} from 'react-router-dom'
import About from './views/About'
import Home from './views/Home'


class App extends Component {
    render() {
        return (
            <BrowserRouter>
                <div className="App">
                    <Link to="/">Home</Link>
                    <Link to="/about" style={{marginLeft: 30 + 'px'}}>About</Link>
                    <Route exact path="/" component={Home}></Route>
                    <Route path="/about" component={About}></Route>
                </div>
            </BrowserRouter>
        );
    }
}

export default App;

```

**特别说明**

`exact`属性是指定根路由唯一显示，如果不指定，那么子路由就会累加显示。

例如，跳到`about`时，就会这样：
![](http://g-gallery.vkleo.com/img/20190330161111.png)

可以看到，子路由会被显示在父路由内容下，所以加上`exact`，就可以作为页面单个显示。
