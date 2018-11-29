---
title: 理解applyMiddleware和createStore之间的关系
date: 2017-08-01 11:26:23
top: 100
copyright : true
tags:
- react
categories:
- react
- redux
---

#### 前言
不得不佩服redux的作者`Dan Abramov`，看完`applyMiddleware`的源码,我的感受就是：还有这种操作？
与其说是理解applyMiddleware，不如说是理清`createStore`，`applyMiddleware`和一些中间件的关系，和为什么中间件都是`export const createThunkMiddleware = ({ dispatch, getState }) => next => action => {...}`三层嵌套关系。  

<!--more-->

```javascript  
export default function applyMiddleware() {
  for (var _len = arguments.length, middlewares = Array(_len), _key = 0; _key < _len; _key++) {
    middlewares[_key] = arguments[_key];
  }

  return function (createStore) {
    return function (reducer, preloadedState, enhancer) {
      var store = createStore(reducer, preloadedState, enhancer);
      var _dispatch = store.dispatch;
      var chain = [];

      var middlewareAPI = {
        getState: store.getState,
        dispatch: function dispatch(action) {
          return _dispatch(action);
        }
      };
      chain = middlewares.map(function (middleware) {
        return middleware(middlewareAPI);
      });
      _dispatch = compose.apply(undefined, chain)(store.dispatch);

      return _extends({}, store, {
        dispatch: _dispatch
      });
    };
  };
}
```  

精简的30行代码，设计思想强无敌。不难看出applyMiddleware这个函数接收一个createStore函数返回一个接收参数和createStore一样的函数。如下图是createStore的部分源码：  
<img src="/images/createStore1.png">
enhancer就是applyMiddleware执行完第一层后的函数，传一个createStore函数，再次返回一个函数，但此时只传外层的reducer和preloadedState，却没在传第三个enhancer参数。这里要看回到applyMiddleware函数
<img src="/images/applyMiddleware1.png">
两层形参对应上面的实参，可以看到applyMiddleware内部还调用了createStore，整个一个完整的逻辑如下（部分重要代码）：  


`首先是调用createStore，这个enhancer其实就是applyMiddleware(middleware1, middleware2)。`  

```javascript  
const store = createStore(
    reducer,
    undefined,
    enhancer
  )
```  

`其次是createStore里判断执行enhancer，如果传了enhancer，那就对enhancer执行再执行，且第二次执行不传enhancer，这里return的其实也是applyMiddleware函数的返回值，但是等下在applyMiddleware里还会在执行一次createStore。`  

```javascript
if (typeof enhancer !== 'undefined') {
    if (typeof enhancer !== 'function') {
      throw new Error('Expected the enhancer to be a function.');
    }

    return enhancer(createStore)(reducer, preloadedState);
  }
```  


`最后是applyMiddleware里的，由于createStore上面最后一次执行未传enhancer，所以applyMiddleware里的createStore是不会再次执行enhancer,不然就死循环了。拿到返回的sotre对象，重新改造dispatch并返回，这就是中间件的实现的一个大体流程。`
```javascript
  return function (createStore) {
    return function (reducer, preloadedState, enhancer) {
      var store = createStore(reducer, preloadedState, enhancer);
      //省略一万行
    }
  }
```  

至此，相信大家也差不多理解了createStore和applyMiddleware相互之间的关系了，其实关于中间件为什么嵌套三层的问题也自然而然的解开了，一张图片来说明吧。
<img src="/images/applyMiddleware2.png">

#### 菜鸟学习笔记，如有不对，还希望高手指点。如有造成误解，还希望多多谅解。

著作权归作者所有。
商业转载请联系作者获得授权,非商业转载请注明出处。






