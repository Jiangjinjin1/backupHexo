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

## 1、dispatch触发action，react视图更新过程

+ 答: 当我们dispatch 一个 action 的时候， 调用的其实是 store.dispatch，这个都没问题，store.dispatch 会去跑一遍所有注册在 `createStore` 中的 reducer， 找到对应的 type 更新数据，返回一个新的 state。
   
   <!--more-->
     
     而我们的组件想拿到 store 的数据必须通过 `connect(mapStateToProps, mapDispatchToProps)(App)` 像这样，react-redux 中的 Connect 组件会在 `componengDidMount` 的时候去 调用 一个 `trySubscribe` 的方法，其内部调用 `store.subscrib`e 去订阅一个 handleChange 的方法。
     
     所以当你 dispatch action 的时候，就会触发 Connect 组件中的方法， Connect 组件中也维护了一个叫做 `storeState` 的 state，每次拿到新的 sotre 就去调用 setState， 触发 render 函数， render 函数会根据你 connect 中传入的 mapStateToProps， mapDispatchToProps，包括可选参数 mergeProps， 去做一个 props 的合并动作， 最终在 Connect 组件内部 return 出来一个 `createElement(WrappedComponent,this.mergedProps)` 这样的东西，而 createElement 第二个参数就是你组件的 props， 那么每次 props 变了，就会驱动视图的更新。这就是 Redux 其中的中做原理。
