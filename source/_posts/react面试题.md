---
title: react面试题
top: 100
copyright: true
date: 2018-11-29 13:55:00
tags:
- 面试题类目
categories:
- 面试题类目
---

# 1、dispatch触发action，react视图更新过程

> 当我们dispatch 一个 action 的时候， 调用的其实是 store.dispatch，这个都没问题，store.dispatch 会去跑一遍所有注册在 `createStore` 中的 reducer， 找到对应的 type 更新数据，返回一个新的 state。
   
   <!--more-->
     
     而我们的组件想拿到 store 的数据必须通过 `connect(mapStateToProps, mapDispatchToProps)(App)` 像这样，react-redux 中的 Connect 组件会在 `componengDidMount` 的时候去 调用 一个 `trySubscribe` 的方法，其内部调用 `store.subscrib`e 去订阅一个 handleChange 的方法。
     
     所以当你 dispatch action 的时候，就会触发 Connect 组件中的方法， Connect 组件中也维护了一个叫做 `storeState` 的 state，每次拿到新的 sotre 就去调用 setState， 触发 render 函数， render 函数会根据你 connect 中传入的 mapStateToProps， mapDispatchToProps，包括可选参数 mergeProps， 去做一个 props 的合并动作， 最终在 Connect 组件内部 return 出来一个 `createElement(WrappedComponent,this.mergedProps)` 这样的东西，而 createElement 第二个参数就是你组件的 props， 那么每次 props 变了，就会驱动视图的更新。这就是 Redux 其中的中做原理。

# 2、redux中间件
> 中间件提供第三方插件的模式，自定义拦截 action -> reducer 的过程。变为 action -> middlewares -> reducer 。这种机制可以让我们改变数据流，实现如异步 action ，action 过滤，日志输出，异常报告等功能。
        常见的中间件：
        redux-logger：提供日志输出
        redux-thunk：处理异步操作
        redux-promise：处理异步操作，actionCreator的返回值是promise
    

# 3、redux有什么缺点
> 1.一个组件所需要的数据，必须由父组件传过来，而不能像flux中直接从store取。
> 2.当一个组件相关数据更新时，即使父组件不需要用到这个组件，父组件还是会重新render，可能会有效率影响，或者需要写复杂的shouldComponentUpdate进行判断。
  
# 4、react组件的划分业务组件技术组件？

> 根据组件的职责通常把组件分为UI组件和容器组件。

> UI 组件负责 UI 的呈现，容器组件负责管理数据和逻辑。

> 两者通过React-Redux 提供connect方法联系起来。

具体使用可以参照如下链接：
[http://www.ruanyifeng.com/blog/2016/09/redux_tutorial_part_three_react-redux.html]


# 5、为什么虚拟dom会提高性能?

> 虚拟dom相当于在js和真实dom中间加了一个缓存，利用dom diff算法避免了没有必要的dom操作，从而提高性能

具体实现步骤如下：
    用 JavaScript 对象结构表示 DOM 树的结构；然后用这个树构建一个真正的 DOM 树，插到文档当中
    当状态变更的时候，重新构造一棵新的对象树。然后用新的树和旧的树进行比较，记录两棵树差异把2所记录的差异应用到步骤1所构建的真正的DOM树上，视图就更新了。
    
参考链接：

[https://www.zhihu.com/question/29504639?sort=created]

# 6、diff算法?

把树形结构按照层级分解，只比较同级元素。
给列表结构的每个单元添加唯一的key属性，方便比较。
React 只会匹配相同 class 的 component（这里面的class指的是组件的名字）合并操作，调用 component 的 setState 方法的时候, React 将其标记为 dirty.到每一个事件循环结束, React 检查所有标记 dirty 的 component 重新绘制.
选择性子树渲染。开发人员可以重写shouldComponentUpdate提高diff的性能。

参考链接：

[https//segmentfault.com/a/1190000000606216]

# 7、react性能优化方案

 (1）重写shouldComponentUpdate来避免不必要的dom操作。

（2）使用 production 版本的react.js。

（3）使用key来帮助React识别列表中所有子组件的最小变化。

参考链接：

[https://segmentfault.com/a/1190000006254212]

