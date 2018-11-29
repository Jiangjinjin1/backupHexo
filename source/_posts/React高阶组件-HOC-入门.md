---
title: React高阶组件(HOC)入门
date: 2018-11-16 15:24:47
top: 100
copyright : true
tags:
- react
categories:
- react
---

# 前言


> &nbsp;&nbsp;&nbsp;&nbsp;之前的文章[React Mixins入门指南](https://segmentfault.com/a/1190000008814336)介绍了React Mixin的使用。在实际使用中React Mixin的作用还是非常强大的，能够使得我们在多个组件中共用相同的方法。但是工程中大量使用Mixin也会带来非常多的问题。[Dan Abramov](https://twitter.com/dan_abramov)在文章[Mixins Considered Harmful](https://reactjs.org/blog/2016/07/13/mixins-considered-harmful.html)介绍了Mixin带来的一些问题,总结下来主要是以下几点:

<!--more-->

+ 破坏组件封装性: Mixin可能会引入不可见的属性。例如在渲染组件中使用Mixin方法，给组件带来了不可见的属性(props)和状态(state)。并且Mixin可能会相互依赖，相互耦合，不利于代码维护。

+ 不同的Mixin中的方法可能会相互冲突

+ 为了处理上述的问题，React官方推荐使用高阶组件(High Order Component)

# 高阶组件(HOC)
---

  &nbsp;&nbsp;&nbsp;&nbsp;刚开始学习高阶组件时，这个概念就透漏着高级的气味，看上去就像是一种先进的编程技术的一个深奥术语，毕竟名字里就有"高阶"这种字眼，实质上并不是如此。高阶组件的概念应该是来源于JavaScript的高阶函数:
  
> `高阶函数就是接受函数作为输入或者输出的函数`

  这么看来[柯里化](https://segmentfault.com/a/1190000008193605)也是高阶函数了。React官方定义高阶组件的概念是:
  
> `A higher-order component is a function that takes a component and returns a new component.`

  &nbsp;&nbsp;&nbsp;&nbsp;(参照翻译了React官方文档的[Advanced Guides](https://github.com/MrErHu/React-Advanced-Guides-CN)部分，官方的高阶组件中文文档戳[这里](https://link.juejin.im/?target=https%3A%2F%2Fgithub.com%2FMrErHu%2FReact-Advanced-Guides-CN%2Fblob%2Fmaster%2Fdoc%2FHigher%2520Order%2520Components.md))
  
  &nbsp;&nbsp;&nbsp;&nbsp;这么看来，高阶组件仅仅只是是一个接受组件组作输入并返回组件的函数。看上去并没有什么，那么高阶组件能为我们带来什么呢？首先看一下高阶组件是如何实现的，通常情况下，实现高阶组件的方式有以下两种:

  1. 属性代理(Props Proxy)
  2. 反向继承(Inheritance Inversion)

## 属性代理
&nbsp;&nbsp;&nbsp;&nbsp;又是一个听起来很高大上的名词，实质上是通过包裹原来的组件来操作props，举个简单的例子:

```javascript

import React, { Component } from 'React';
//高阶组件定义
const HOC = (WrappedComponent) =>
  class WrapperComponent extends Component {
    render() {
      return <WrappedComponent {...this.props} />;
    }
}
//普通的组件
class WrappedComponent extends Component{
    render(){
        //....
    }
}

//高阶组件使用
export default HOC(WrappedComponent)
```

&nbsp;&nbsp;&nbsp;&nbsp;上面的例子非常简单，但足以说明问题。我们可以看见函数HOC返回了新的组件(WrapperComponent)，这个组件原封不动的返回作为参数的组件(也就是被包裹的组件:WrappedComponent)，并将传给它的参数(props)全部传递给被包裹的组件(WrappedComponent)。这么看起来好像并没有什么作用，其实属性代理的作用还是非常强大的。

## 操作props

　　&nbsp;&nbsp;&nbsp;&nbsp;我们看到之前要传递给被包裹组件WrappedComponent的属性首先传递给了高阶组件返回的组件(WrapperComponent)，这样我们就获得了props的控制权(这也就是为什么这种方法叫做属性代理)。我们可以按照需要对传入的props进行增加、删除、修改(当然修改带来的风险需要你自己来控制)，举个例子:

```javascript
const HOC = (WrappedComponent) =>
    class WrapperComponent extends Component {
        render() {
            const newProps = {
                name: 'HOC'
            }
            return <WrappedComponent
                {...this.props}
                {...newProps}
            />;
        }
    }
```

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;在上面的例子中，我们为被包裹组件(WrappedComponent)新增加了固定的name属性，因此WrappedComponent组件中就会多一个name的属性。

## 获得`refs`的引用

&nbsp;&nbsp;&nbsp;&nbsp;我们在属性代理中，可以轻松的拿到被包裹的组件的实例引用(`ref`)，例如:

```javascript
import React, { Component } from 'React';
　
const HOC = (WrappedComponent) =>
    class wrapperComponent extends Component {
        storeRef(ref) {
            this.ref = ref;
        }
        render() {
            return <WrappedComponent
                {...this.props}
                ref = {::this.storeRef}
            />;
        }
    }
```

&nbsp;&nbsp;&nbsp;&nbsp;上面的例子中，wrapperComponent渲染接受后，我们就可以拿到WrappedComponent组件的实例，进而实现调用实例方法的操作(当然这样会在一定程度上是反模式的，不是非常的推荐)。

## 抽象state

&nbsp;&nbsp;&nbsp;&nbsp;属性代理的情况下，我们可以将被包裹组件(WrappedComponent)中的状态提到包裹组件中，一个常见的例子就是实现不受控组件到受控的组件的转变(关于不受控组件和受控组件戳[这里](https://github.com/MrErHu/React-Advanced-Guides-CN/blob/master/doc/Uncontrolled%20Components.md))

```javascript
class WrappedComponent extends Component {
    render() {
        return <input name="name" {...this.props.name} />;
    }
}

const HOC = (WrappedComponent) =>
    class extends Component {
        constructor(props) {
            super(props);
            this.state = {
                name: '',
            };

            this.onNameChange = this.onNameChange.bind(this);
        }

        onNameChange(event) {
            this.setState({
                name: event.target.value,
            })
        }

        render() {
            const newProps = {
                name: {
                    value: this.state.name,
                    onChange: this.onNameChange,
                },
            }
            return <WrappedComponent {...this.props} {...newProps} />;
        }
    }
```

&nbsp;&nbsp;&nbsp;&nbsp;上面的例子中通过高阶组件，我们将不受控组件(WrappedComponent)成功的转变为受控组件.

## 用其他元素包裹组件

&nbsp;&nbsp;&nbsp;&nbsp;我们可以通过类似:

```javascript
    render(){
        <div>
            <WrappedComponent {...this.props} />
        </div>
    }
```

&nbsp;&nbsp;&nbsp;&nbsp;这种方式将被包裹组件包裹起来，来实现布局或者是样式的目的。<br/>

&nbsp;&nbsp;&nbsp;&nbsp;在属性代理这种方式实现的高阶组件，以上述为例，组件的渲染顺序是: 先WrappedComponent再WrapperComponent(执行ComponentDidMount的时间)。而卸载的顺序是先WrapperComponent再WrappedComponent(执行ComponentWillUnmount的时间)。

## 反向继承

&nbsp;&nbsp;&nbsp;&nbsp;反向继承是指返回的组件去继承之前的组件(这里都用WrappedComponent代指)

```javascript
const HOC = (WrappedComponent) =>
  class extends WrappedComponent {
    render() {
      return super.render();
    }
  }
```

&nbsp;&nbsp;&nbsp;&nbsp;我们可以看见返回的组件确实都继承自WrappedComponent,那么所有的调用将是反向调用的(例如:`super.render()`)，这也就是为什么叫做反向继承。

## 渲染劫持

&nbsp;&nbsp;&nbsp;&nbsp;渲染劫持是指我们可以有意识地控制WrappedComponent的渲染过程，从而控制渲染控制的结果。例如我们可以根据部分参数去决定是否渲染组件:

```javascript
const HOC = (WrappedComponent) =>
  class extends WrappedComponent {
    render() {
      if (this.props.isRender) {
        return super.render();
      } else {
        return null;
      }
    }
  }
```

&nbsp;&nbsp;&nbsp;&nbsp;甚至我们可以修改修改render的结果:

```javascript
//例子来源于《深入React技术栈》

const HOC = (WrappedComponent) =>
    class extends WrappedComponent {
        render() {
            const elementsTree = super.render();
            let newProps = {};
            if (elementsTree && elementsTree.type === 'input') {
                newProps = {value: 'may the force be with you'};
            }
            const props = Object.assign({}, elementsTree.props, newProps);
            const newElementsTree = React.cloneElement(elementsTree, props, elementsTree.props.children);
            return newElementsTree;
    }
}
class WrappedComponent extends Component{
    render(){
        return(
            <input value={'Hello World'} />
        )
    }
}
export default HOC(WrappedComponent)
//实际显示的效果是input的值为"may the force be with you"
```

&nbsp;&nbsp;&nbsp;&nbsp;上面的例子中我们将WrappedComponent中的input元素value值修改为:`may the force be with you。`我们可以看到前后elementTree的区别:
elementsTree:

<img src="/images/react/react1.png">

&nbsp;&nbsp;&nbsp;&nbsp;newElementsTree:

<img src="/images/react/react2.png">

&nbsp;&nbsp;&nbsp;&nbsp;在反向继承中，我们可以做非常多的操作，修改state、props甚至是翻转Element Tree。反向继承有一个重要的点: **反向继承不能保证完整的子组件树被解析**，开始我对这个概念也不理解，后来在看了[React Components](https://reactjs.org/blog/2015/12/18/react-components-elements-and-instances.html), [Elements](https://reactjs.org/blog/2015/12/18/react-components-elements-and-instances.html), and [Instances](https://reactjs.org/blog/2015/12/18/react-components-elements-and-instances.html)这篇文章之后对这个概念有了自己的一点体会。React Components, Elements, and Instances这篇文章主要明确了一下几个点:

&nbsp;&nbsp;&nbsp;&nbsp; + 元素(element)是一个是用DOM节点或者组件来描述屏幕显示的纯对象，元素可以在属性(props.children)中包含其他的元素，一旦创建就不会改变。我们通过`JSX`和`React.createClass`创建的都是元素。
&nbsp;&nbsp;&nbsp;&nbsp; + 组件(component)可以接受属性(props)作为输入，然后返回一个元素树(element tree)作为输出。有多种实现方式:Class或者函数(Function)。

&nbsp;&nbsp;&nbsp;&nbsp;所以，**反向继承不能保证完整的子组件树被解析**的意思的解析的元素树中包含了组件(函数类型或者Class类型)，就不能再操作组件的子组件了，这就是所谓的**不能完全解析**。举个例子:

```javascript
import React, { Component } from 'react';

const MyFuncComponent = (props)=>{
    return (
        <div>Hello World</div>
    );
}

class MyClassComponent extends Component{

    render(){
        return (
            <div>Hello World</div>
        )
    }

}

class WrappedComponent extends Component{
    render(){
        return(
            <div>
                <div>
                    <span>Hello World</span>
                </div>
                <MyFuncComponent />
                <MyClassComponent />
            </div>

        )
    }
}

const HOC = (WrappedComponent) =>
    class extends WrappedComponent {
        render() {
            const elementsTree = super.render();
            return elementsTree;
        }
    }

export default HOC(WrappedComponent);
```

<img src="/images/react/react3.png">

<img src="/images/react/react4.png">

&nbsp;&nbsp;&nbsp;&nbsp;我们可以查看解析的元素树(element tree)，`div`下的`span`是可以被完全被解析的，但是`MyFuncComponent`和`MyClassComponent`都是组件类型的，其子组件就不能被完全解析了。

## 操作props和state

&nbsp;&nbsp;&nbsp;&nbsp;在上面的图中我们可以看到，解析的元素树(element tree)中含有`props`和`state`(例子的组件中没有state),以及`ref`和`key`等值。因此，如果需要的话，我们不仅可以读取`props`和`state`,甚至可以修改增加、修改和删除。

&nbsp;&nbsp;&nbsp;&nbsp;在某些情况下，我们可能需要为高阶属性传入一些参数，那我们就可以通过柯里化的形式传入参数，例如:

```javascript
import React, { Component } from 'React';

const HOCFactoryFactory = (...params) => {
    // 可以做一些改变 params 的事
    return (WrappedComponent) => {
        return class HOC extends Component {
            render() {
                return <WrappedComponent {...this.props} />;
            }
        }
    }
}
```

&nbsp;&nbsp;&nbsp;&nbsp;可以通过下面方式使用:

```javascript
HOCFactoryFactory(params)(WrappedComponent)
```

&nbsp;&nbsp;&nbsp;&nbsp;这种方式是不是非常类似于`React-Redux`库中的`connect`函数，因为`connect`也是类似的一种高阶函数。反向继承不同于属性代理的调用顺序，组件的渲染顺序是: 先WrappedComponent再WrapperComponent(执行ComponentDidMount的时间)。而卸载的顺序也是先WrappedComponent再WrapperComponent(执行ComponentWillUnmount的时间)。

## HOC和Mixin的比较
&nbsp;&nbsp;&nbsp;&nbsp;借用《深入React技术栈》一书中的图:

<img src="/images/react/react5.png">

&nbsp;&nbsp;&nbsp;&nbsp;高阶组件属于函数式编程(functional programming)思想，对于被包裹的组件时不会感知到高阶组件的存在，而高阶组件返回的组件会在原来的组件之上具有功能增强的效果。而Mixin这种混入的模式，会给组件不断增加新的方法和属性，组件本身不仅可以感知，甚至需要做相关的处理(例如命名冲突、状态维护)，一旦混入的模块变多时，整个组件就变的难以维护，也就是为什么如此多的React库都采用高阶组件的方式进行开发。
