---
title: IOC设计理念
top: 100
copyright: true
date: 2019-01-04 15:26:45
tags:
- 设计模式
categories:
- 设计模式
---

# 什么是 IoC

`IoC` 的全称叫做 `Inversion of Control`，可翻译为为「**控制反转**」或「**依赖倒置**」，它主要包含了三个准则：

>    1、高层次的模块不应该依赖于低层次的模块，它们都应该依赖于抽象
>    2、抽象不应该依赖于具体实现，具体实现应该依赖于抽象
>    3、面向接口编程 而不要面向实现编程

## 依赖注入

------
所谓的依赖注入，简单来说就是把高层模块所依赖的模块通过传参的方式把依赖「注入」到模块内部，上面的代码可以通过依赖注入的方式改造成如下方式：

<!--more-->

```javascript
// app.js
class App {
    constructor(options) {
        this.options = options;
        this.router = options.router;
        this.track = options.track;

        this.init();
    }

    init() {
        window.addEventListener('DOMContentLoaded', () => {
            this.router.to('home');
            this.track.tracking();
            this.options.onReady();
        });
    }
}

// index.js
import App from 'path/to/App';
import Router from './modules/Router';
import Track from './modules/Track';

new App({
    router: new Router(),
    track: new Track(),
    onReady() {
        // do something here...
    },
});

```

但是如果产品说再加个分享功能，肯定要再加个`this.share = options.share`,这明显不是我们所期望的。

看下面的代码:

```javascript
class App {
    static modules = []
    constructor(options) {
        this.options = options;
        this.init();
    }
    init() {
        window.addEventListener('DOMContentLoaded', () => {
            this.initModules();
            this.options.onReady(this);
        });
    }
    static use(module) {
        Array.isArray(module) ? module.map(item => App.use(item)) : App.modules.push(module);
    }
    initModules() {
        App.modules.map(module => module.init && typeof module.init == 'function' && module.init(this));
    }
}

```


经过改造后 `App` 内已经没有「具体实现」了，看不到任何业务代码了，那么如何使用 `App` 来管理我们的依赖呢：

```javascript
// modules/Router.js
import Router from 'path/to/Router';
export default {
    init(app) {
        app.router = new Router(app.options.router);
        app.router.to('home');
    }
};
// modules/Track.js
import Track from 'path/to/Track';
export default {
    init(app) {
        app.track = new Track(app.options.track);
        app.track.tracking();
    }
};

// index.js
import App from 'path/to/App';
import Router from './modules/Router';
import Track from './modules/Track';

App.use([Router, Track]);

new App({
    router: {
        mode: 'history',
    },
    track: {
        // ...
    },
    onReady(app) {
        // app.options ...
    },
});

```

这样通过注入到modules里初始化，将 `App` 的this传入modules注入的方法中，实现绑定到 `App` 的对象上

## 总结
------

`App` 模块此时应该称之为「容器」比较合适了，跟业务已经没有任何关系了，它仅仅只是提供了一些方法来辅助管理注入的依赖和控制模块如何执行。
控制反转（`Inversion of Control`）是一种「思想」，依赖注入（`Dependency Injection`）则是这一思想的一种具体「实现方式」，而这里的 `App` 则是辅助依赖管理的一个「容器」。
