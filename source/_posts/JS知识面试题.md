---
title: JS知识面试题
top: 100
copyright: true
date: 2019-01-07 10:56:25
tags:
- 面试题类目
categories:
- 面试题类目
---

# 一、基础javascript篇

## 1、类的创建和继承

（1）类的创建（es5）：new一个function，在这个function的prototype里面增加属性和方法。

下面来创建一个Animal类：

```javascript
// 定义一个动物类
function Animal (name) {
  // 属性
  this.name = name || 'Animal';
  // 实例方法
  this.sleep = function(){
    console.log(this.name + '正在睡觉！');
  }
}
// 原型方法
Animal.prototype.eat = function(food) {
  console.log(this.name + '正在吃：' + food);
};

```

<!--more-->

这样就生成了一个Animal类，实力化生成对象后，有方法和属性。

（2）类的继承——原型链继承

```javascript
--原型链继承
function Cat(){ }
Cat.prototype = new Animal();
Cat.prototype.name = 'cat';
//&emsp;Test Code
var cat = new Cat();
console.log(cat.name);
console.log(cat.eat('fish'));
console.log(cat.sleep());
console.log(cat instanceof Animal); //true 
console.log(cat instanceof Cat); //true

```

+ 介绍：在这里我们可以看到new了一个空对象,这个空对象指向Animal并且Cat.prototype指向了这个空对象，这种就是基于原型链的继承。
+ 特点：基于原型链，既是父类的实例，也是子类的实例
+ 缺点：无法实现多继承

（3）构造继承：使用父类的构造函数来增强子类实例，等于是复制父类的实例属性给子类（没用到原型）

```javascript
function Cat(name){
  Animal.call(this);
  this.name = name || 'Tom';
}
// Test Code
var cat = new Cat();
console.log(cat.name);
console.log(cat.sleep());
console.log(cat instanceof Animal); // false
console.log(cat instanceof Cat); // true

```

+ 特点：可以实现多继承
+ 缺点：只能继承父类实例的属性和方法，不能继承原型上的属性和方法。

（4）实例继承和拷贝继承<br>
+ 实例继承：为父类实例添加新特性，作为子类实例返回
+ 拷贝继承：拷贝父类元素上的属性和方法
+ 上述两个实用性不强，不一一举例。

（5）组合继承：相当于构造继承和原型链继承的组合体。通过调用父类构造，继承父类的属性并保留传参的优点，然后通过将父类实例作为子类原型，实现函数复用
 
 ```javascript
function Cat(name){
  Animal.call(this);
  this.name = name || 'Tom';
}
Cat.prototype = new Animal();
Cat.prototype.constructor = Cat;
// Test Code
var cat = new Cat();
console.log(cat.name);
console.log(cat.sleep());
console.log(cat instanceof Animal); // true
console.log(cat instanceof Cat); // true

```

+ 特点：可以继承实例属性/方法，也可以继承原型属性/方法
+ 缺点：调用了两次父类构造函数，生成了两份实例

（6）寄生组合继承：通过寄生方式，砍掉父类的实例属性，这样，在调用两次父类的构造的时候，就不会初始化两次实例方法/属性

```javascript
function Cat(name){
  Animal.call(this);
  this.name = name || 'Tom';
}
(function(){
  // 创建一个没有实例方法的类
  var Super = function(){};
  Super.prototype = Animal.prototype;
  //将实例作为子类的原型
  Cat.prototype = new Super();
})();
// Test Code
var cat = new Cat();
console.log(cat.name);
console.log(cat.sleep());
console.log(cat instanceof Animal); // true
console.log(cat instanceof Cat); //true

```

+ 较为推荐

## 2、说说前端中的事件流

&emsp;&emsp;HTML中与javascript交互是通过事件驱动来实现的，例如鼠标点击事件onclick、页面的滚动事件onscroll等等，可以向文档或者文档中的元素添加事件侦听器来预订事件。想要知道这些事件是在什么时候进行调用的，就需要了解一下“事件流”的概念。<br>
&emsp;&emsp;什么是事件流：事件流描述的是从页面中接收事件的顺序,DOM2级事件流包括下面几个阶段。

+ 事件捕获阶段
+ 处于目标阶段
+ 事件冒泡阶段

&emsp;&emsp;addEventListener：addEventListener 是DOM2 级事件新增的指定事件处理程序的操作，这个方法接收3个参数：要处理的事件名、作为事件处理程序的函数和一个布尔值。最后这个布尔值参数如果是true，表示在捕获阶段调用事件处理程序；如果是false，表示在冒泡阶段调用事件处理程序。

+ __IE只支持事件冒泡。__

## 3、如何让事件先冒泡后捕获

&emsp;&emsp;在DOM标准事件模型中，是先捕获后冒泡。但是如果要实现先冒泡后捕获的效果，对于同一个事件，监听捕获和冒泡，分别对应相应的处理函数，监听到捕获事件，先暂缓执行，直到冒泡事件被捕获后再执行捕获之间。

## 4、callee和caller的作用与区别

递归<br>
我们可能用到一些函数调用自身，即递归。下面是一个计算阶乘的。

```javascript
function factorial(x) {
	return x<=1 ? 1 : x*factorial(x-1);
}

```

运行后发现它很好的完成了我们的要求。可是还是存在一个问题，万一哪天有人重构这个函数改了函数名呢？修改不方便甚至漏改。

__arguments.callee__

使用callee 避免hard code 函数名。

```javascript
function factorial(x) {
	return x<=1 ? 1 : x * arguments.callee(x-1);
}

```

+ callee是arguments对象的一个属性，指向 arguments 对象的函数，即当前函数。在例子中是XX。

__caller__

函数对象的一个属性，指向调用当前函数的函数。比如 A() 调用 B()， 则在B()中 B.caller 指向A()。

```javascript
function B(){
	console.log(B.caller);
}
 
(function A(){
	B()
})()

```

+ 显然，只有当函数被调用时，该属性才会有值。不过当函数被全局调用时，该属性为null。


__callee和caller结合__

我们刚才在函数B() 中使用了 B.caller 。跟上面递归一样，将来如果有人重构改了函数名呢？ 下面用刚才说的 arguments.callee 替换。

```javascript
function B(){
	console.log(arguments.callee.caller);
}

```
到这是不是好多了。再执行A() ，发现跟刚才的输出一样。

## 5、给history的replaceState和pushState行为添加监听事件

具体做法为：

```javascript
var _wr = function(type) {
   var orig = history[type];
   return function() {
       var rv = orig.apply(this, arguments);
      var e = new Event(type);
       e.arguments = arguments;
       window.dispatchEvent(e);
       return rv;
   };
};
 history.pushState = _wr('pushState');
 history.replaceState = _wr('replaceState');
```

这样就创建了2个全新的事件，事件名为pushState和replaceState，我们就可以在全局监听：

```javascript
window.addEventListener('replaceState', function(e) {
  console.log('THEY DID IT AGAIN! replaceState 111111');
});
window.addEventListener('pushState', function(e) {
  console.log('THEY DID IT AGAIN! pushState 2222222');
});
```

这样就可以监听到pushState和replaceState行为。

# 二、进阶javascript篇

## 1、自己实现一个bind函数

__原理：通过apply或者call方法来实现。__

(1)初始版本

```javascript
Function.prototype.bind = function (obj, arg) {
  var context = this;
  var tmpArg = Array.prototype.slice.call(arguments, 1);
  
  return function(newArg) {
    var newArgs = tmpArg.concat(Array.prototype.slice.call(newArg))
    return context.apply(obj, newArgs)
  }
}
```

(2) 考虑到原型链
为什么要考虑？因为在new 一个bind过生成的新函数的时候，必须的条件是要继承原函数的原型

```javascript
Function.prototype.bind = function (obj, arg) {
  var context = this;
  var tmpArg = Array.prototype.slice.call(arguments, 1);
  
  var newBind = function(newArg) {
    var newArgs = tmpArg.concat(Array.prototype.slice.call(newArg))
    return context.apply(obj, newArgs)
  }
  
  var F = function() {}
  
  F.prototype = context.prototype;
  //这里需要一个寄生组合继承
  newBind.prototype = new F();
  
  return newBind
}
```

## 2、Macrotasks 和 Microtasks

Macrotask 和 microtask 都是属于上述的异步任务中的一种，我们先看一下他们分别是哪些 API ：<br>

+ __macrotasks__: `setTimeout`, `setInterval`, `setImmediate`, `I/O`, `UI rendering`
+ __microtasks__: `process.nextTick`, `Promises`, `Object.observe`(废弃), `MutationObserver`

&emsp;&emsp;`setTimeout` 的 macrotask ,和 `Promise` 的 microtask 有什么不同呢？ 我们通过下面的代码来展现他们的不同点：

```javascript
console.log('script start');
setTimeout(function() {
  console.log('setTimeout');
}, 0);
Promise.resolve().then(function() {
  console.log('promise1');
}).then(function() {
  console.log('promise2');
});
console.log('script end');
```

在这里，`setTimeout`的延时为0，而`Promise.resolve()`也是返回一个被`resolve`了`promise`对象，即这里的`then`方法中的函数也是相当于异步的立即执行任务，那么他们到底是谁在前谁在后？<br>


我们看看最终的运行结果（node 7.7.3)：

```javascript
"script start"
"script end"
"promise1"
"promise2"
"setTimeout"
```

这里的运行结果是Promise的立即返回的异步任务会优先于setTimeout延时为0的任务执行。<br>

原因是任务队列分为 macrotasks 和 microtasks，而Promise中的then方法的函数会被推入 microtasks 队列，而setTimeout的任务会被推入 macrotasks 队列。在每一次事件循环中，macrotask 只会提取一个执行，而 microtask 会一直提取，直到 microtasks 队列清空。<br>

**注：一般情况下，macrotask queues 我们会直接称为 task queues，只有 microtask queues 才会特别指明。**<br>

那么也就是说如果我的某个 microtask 任务又推入了一个任务进入 microtasks 队列，那么在主线程完成该任务之后，仍然会继续运行 microtasks 任务直到任务队列耗尽。<br>

**而事件循环每次只会入栈一个 macrotask ，主线程执行完该任务后又会先检查 microtasks 队列并完成里面的所有任务后再执行 macrotask**

## 3、手写实现Promise

```javascript
// myPromise 

function Promise(excutor) {
	let _this = this;
	_this.status = 'pending';
	_this.value = undefined;
	_this.reason = undefined;
	_this.onResolvedCallbacks = []; // 存放then成功的回调
	_this.onRejectedCallbacks = []; // 存放then失败的回调


	function resolve(val) {
		if(_this.status === 'pending') {
			_this.status = 'resolved';
			_this.value = val;

			_this.onResolvedCallbacks.forEach(function(resolveFn) {
				if(resolveFn) {
					resolveFn()
				}
			})
		}
		
	}

	function reject(reasonValue) {
		if(_this.status === 'pending') {
			_this.status = 'rejected';
			_this.reason = reasonValue;

			_this.onRejectedCallbacks.forEach(function(rejectFn) {
				if(rejectFn) {
					rejectFn()
				}
			})
		}
	}

	excutor(resolve, reject)
}


Promise.prototype.then = function (onFulfilled, onRjected) {
    //成功和失败默认不传给一个函数，解决了问题8
    onFulfilled = typeof onFulfilled === 'function' ? onFulfilled : function (value) {
        return value;
    }
    onRjected = typeof onRjected === 'function' ? onRjected : function (err) {
        throw err;
    }
    let _this = this;
    let promise2; //返回的promise
    if (_this.status === 'resolved') {
        promise2 = new Promise(function (resolve, reject) {
            // 当成功或者失败执行时有异常那么返回的promise应该处于失败状态
            setTimeout(function () {// 根据规范让那俩家伙异步执行
                try {
                    let x = onFulfilled(_this.value);//这里解释过了
                    // 写一个方法统一处理问题1-7
                    resolvePromise(promise2, x, resolve, reject);
                } catch (e) {
                    reject(e);
                }
            })
        })
    }
    if (_this.status === 'rejected') {
        promise2 = new Promise(function (resolve, reject) {
            setTimeout(function () {
                try {
                    let x = onRjected(_this.reason);
                    resolvePromise(promise2, x, resolve, reject);
                } catch (e) {
                    reject(e);
                }
            })
        })
    }
    if (_this.status === 'pending') {
        promise2 = new Promise(function (resolve, reject) {
            _this.onResolvedCallbacks.push(function () {
                setTimeout(function () {
                    try {
                        let x = onFulfilled(_this.value);
                        resolvePromise(promise2, x, resolve, reject);
                    } catch (e) {
                        reject(e)
                    }
                })
            });
            _this.onRejectedCallbacks.push(function () {
                setTimeout(function () {
                    try {
                        let x = onRjected(_this.reason);
                        resolvePromise(promise2, x, resolve, reject);
                    } catch (e) {
                        reject(e);
                    }
                })
            });
        })
    }
    return promise2;
}


function resolvePromise(promise2, x, resolve, reject) {
    // 接受四个参数，新Promise、返回值，成功和失败的回调
    // 有可能这里返回的x是别人的promise
    // 尽可能允许其他乱写
    if (promise2 === x) { //这里应该报一个类型错误，来解决问题4
        return reject(new TypeError('循环引用了'))
    }
    // 看x是不是一个promise,promise应该是一个对象
    let called; // 表示是否调用过成功或者失败，用来解决问题7
    //下面判断上一次then返回的是普通值还是函数，来解决问题1、2
    if (x !== null && (typeof x === 'object' || typeof x === 'function')) {
        // 可能是promise {},看这个对象中是否有then方法，如果有then我就认为他是promise了
        try {
            let then = x.then;// 保存一下x的then方法
            if (typeof then === 'function') {
                // 成功
                //这里的y也是官方规范，如果还是promise，可以当下一次的x使用
                //用call方法修改指针为x，否则this指向window
                then.call(x, function (y) {
                    if (called) return //如果调用过就return掉
                    called = true
                    // y可能还是一个promise，在去解析直到返回的是一个普通值
                    resolvePromise(promise2, y, resolve, reject)//递归调用，解决了问题6
                }, function (err) { //失败
                    if (called) return
                    called = true
                    reject(err);
                })
            } else {
                resolve(x)
            }
        } catch (e) {
            if (called) return
            called = true;
            reject(e);
        }
    } else { // 说明是一个普通值1
        resolve(x); // 表示成功了
    }
}
```

## 4、Function._proto_(getPrototypeOf)是什么？

获取一个对象的原型，在chrome中可以通过__proto__的形式，或者在ES6中可以通过Object.getPrototypeOf的形式。<br>

那么Function.proto是什么么？也就是说Function由什么对象继承而来，我们来做如下判别。

```javascript
Function.__proto__==Object.prototype //false
Function.__proto__.__proto__==Object.prototype //true
Function.__proto__==Function.prototype//true
```

我们发现Function的原型也是Function。<br>

我们用图可以来明确这个关系：

<img src="/images/jsImg_1.png">

+ 此处借图于[https://juejin.im/post/5b44a485e51d4519945fb6b7]

## 5、观察者或订阅模式的简单实现

```javascript
function Events(){
this.on=function(eventName,callBack){
  if(!this.handles){
    this.handles={};
  }
  if(!this.handles[eventName]){
    this.handles[eventName]=[];
  }
  this.handles[eventName].push(callBack);
}
this.emit=function(eventName,obj){
   if(this.handles[eventName]){
     for(var i=0;o<this.handles[eventName].length;i++){
       this.handles[eventName][i](obj);
     }
   }
}
return this;
}

```

这样我们就定义了Events，现在我们可以开始来调用：

```javascript
 var events=new Events();
 events.on('say',function(name){
    console.log('Hello',nama)
 });
 events.emit('say','Jony yu');
 //结果就是通过emit调用之后，输出了Jony yu

```

## 6、装饰器（Decorators）

> 我们知道继承模式是丰富子元素“内涵”的一种重要方式，不管是继承接口还是子类继承基类。而装饰者模式可以在不改变继承关系的前提下，包装先有的模块，使其内涵更加丰富，并不会影响到原来的功能。与继承相比，更加的灵活

__(1)、类方法的装饰器__

下面来介绍一下用装饰器来修饰函数，首先来看一个例子：

```javascript
let temple;
function log(target, key, descriptor) {
  console.log(`${key} was called!`);
  temple = target;
}
class P {
    @log
    foo() {
      console.log('Do something');
    }
}

const p = new P()
p.foo()
console.log(P.prototype === temple) //true
```

上述是实例方法foo中我们用log函数修饰，log函数接受三个参数，通过P.prototype === temple(target)可以判断，在类的实例函数的装饰器函数第一个参数为类的原型，第二个参数为函数名本身，第三个参数为该函数的描述属性。

具体总结如下，对于类的函数的装饰器函数，依次接受的参数为：

+ target：如果修饰的是类的实例函数，那么target就是类的原型。如果修饰的是类的静态函数，那么target就是类本身。
+ key： 该函数的函数名。
+ descriptor：该函数的描述属性，比如 configurable、value、enumerable等。

从上述的例子中我们可以看到，用装饰器来修饰相应的类的函数十分方便：

```javascript
@log
foo() {
  ...
}
```

__(2)、类的装饰器__

装饰函数也可以直接修饰类：

```javascript
let temple
function foo(target){
   console.log(target);
   temple = target
}
@foo
class P{
   constructor(){
     
   }
}

const p = new P();
temple === P //true
```

当装饰函数直接修饰类的时候，装饰函数接受唯一的参数，这个参数就是该被修饰类本身。上述的例子中，输出的target就是类P的本身。

此外，在修饰类的时候，如果装饰函数有返回值，该返回值会重新定义这个类，也就是说当装饰函数有返回值时，其实是生成了一个新类，该新类通过返回值来定义。

举例来说：

```javascript
function foo(target){
   return class extends target{
      name = 'Jony';
      sayHello(){
         console.log("Hello "+ this.name)
      }
   }
}
@foo
class P{
   constructor(){
     
   }
}

const p = new P();
p.sayHello(); // 会输出Hello Jony
```

上面的例子可以看到，当装饰函数foo有返回值时，实际上P类已经被返回值所代表的新类所代替，因此P的实例p拥有sayHello方法。

__(3)、类的属性的装饰器__

下面我们来看类的属性的装饰器,装饰函数修饰类的属性时，在类实例化的时候调用属性的装饰函数，举例来说：

```javascript
function foo(target,name){
   console.log("target is",target);
   console.log("name is",name)
}
class P{
   @foo
   name = 'Jony'
}
const p = new P();
//会依次输出 target is f P()  name is Jony
```

这里对于类的属性的装饰器函数接受两个参数，对于静态属性而言，第一个参数是类本身，对于实例属性而言，第一个参数是类的原型，第二个参数是指属性的名字。

__(4)、类函数参数的装饰器__

接着来看类函数参数的装饰器，类函数的参数装饰器可以修饰类的构建函数中的参数，以及类中其他普通函数中的参数。该装饰器在类的方法被调用的时候执行，下面来看实例：

```javascript
function foo(target,key,index){
   console.log("target is",target);
   console.log("key is",key);
   console.log("index is",index)
}
class P{
   test(@foo a){
   }
}
const p = new P();
p.test("Hello Jony")
// 依次输出 f P() , test , 0 
```

类函数参数的装饰器函数接受三个参数，依次为类本身，类中该被修饰的函数本身，以及被修饰的参数在参数列表中的索引值。上述的例子中，会依次输出 f P() 、test和0。再次明确一下修饰函数参数的装饰器函数中的参数含义：

+ target： 类本身
+ key：该参数所在的函数的函数名
+ index： 该参数在函数参数列表中的索引值

从上面的Typescrit中在基类中常用的装饰器后，我们发现：

__装饰器可以起到分离复杂逻辑的功能，且使用上极其简单方便。与继承相比，也更加灵活，可以从装饰类，到装饰类函数的参数，可以说武装到了“牙齿”。__

## 7、理解Object.defineProperty的作用

对象是由多个名/值对组成的无序的集合。对象中每个属性对应任意类型的值。
定义对象可以使用构造函数或字面量的形式：

```javascript
var obj = new Object;  //obj = {}
obj.name = "张三";  //添加描述
obj.say = function(){};  //添加行为
```

除了以上添加属性的方式，还可以使用**Object.defineProperty**定义新属性或修改原有的属性。

语法：

```javascript
Object.defineProperty(obj, prop, descriptor)
```

参数说明：

> obj：必需。目标对象 
> prop：必需。需定义或修改的属性的名字
> descriptor：必需。目标属性所拥有的特性

返回值：

> 传入函数的对象。即第一个参数obj

针对属性，我们可以给这个属性设置一些特性，比如是否只读不可以写；是否可以被for..in或Object.keys()遍历。

给对象的属性添加特性描述，目前提供两种形式：数据描述和存取器描述。

### 数据描述
当修改或定义对象的某个属性的时候，给这个属性添加一些特性：

```javascript
var obj = {
    test:"hello"
}
//对象已有的属性添加特性描述
Object.defineProperty(obj,"test",{
    configurable:true | false,
    enumerable:true | false,
    value:任意类型的值,
    writable:true | false
});
//对象新添加的属性的特性描述
Object.defineProperty(obj,"newKey",{
    configurable:true | false,
    enumerable:true | false,
    value:任意类型的值,
    writable:true | false
});
```

数据描述中的属性都是可选的，来看一下设置每一个属性的作用

#### value
属性对应的值,可以使任意类型的值，默认为undefined
```javascript
var obj = {}
//第一种情况：不设置value属性
Object.defineProperty(obj,"newKey",{

});
console.log( obj.newKey );  //undefined
------------------------------
//第二种情况：设置value属性
Object.defineProperty(obj,"newKey",{
    value:"hello"
});
console.log( obj.newKey );  //hello
```

#### writable
属性的值是否可以被重写。设置为true可以被重写；设置为false，不能被重写。默认为false。
```javascript
var obj = {}
//第一种情况：writable设置为false，不能重写。
Object.defineProperty(obj,"newKey",{
    value:"hello",
    writable:false
});
//更改newKey的值
obj.newKey = "change value";
console.log( obj.newKey );  //hello

//第二种情况：writable设置为true，可以重写
Object.defineProperty(obj,"newKey",{
    value:"hello",
    writable:true
});
//更改newKey的值
obj.newKey = "change value";
console.log( obj.newKey );  //change value
```

#### enumerable
此属性是否可以被枚举（使用for...in或Object.keys()）。设置为true可以被枚举；设置为false，不能被枚举。默认为false。

```javascript
var obj = {}
//第一种情况：enumerable设置为false，不能被枚举。
Object.defineProperty(obj,"newKey",{
    value:"hello",
    writable:false,
    enumerable:false
});

//枚举对象的属性
for( var attr in obj ){
    console.log( attr );  
}
//第二种情况：enumerable设置为true，可以被枚举。
Object.defineProperty(obj,"newKey",{
    value:"hello",
    writable:false,
    enumerable:true
});

//枚举对象的属性
for( var attr in obj ){
    console.log( attr );  //newKey
}
```

#### configurable
是否可以删除目标属性或是否可以再次修改属性的特性（writable, configurable, enumerable）。设置为true可以被删除或可以重新设置特性；设置为false，不能被可以被删除或不可以重新设置特性。默认为false。

这个属性起到两个作用：
> 1、目标属性是否可以使用delete删除
  
> 2、目标属性是否可以再次设置特性
```javascript
//-----------------测试目标属性是否能被删除------------------------
var obj = {}
//第一种情况：configurable设置为false，不能被删除。
Object.defineProperty(obj,"newKey",{
    value:"hello",
    writable:false,
    enumerable:false,
    configurable:false
});
//删除属性
delete obj.newKey;
console.log( obj.newKey ); //hello

//第二种情况：configurable设置为true，可以被删除。
Object.defineProperty(obj,"newKey",{
    value:"hello",
    writable:false,
    enumerable:false,
    configurable:true
});
//删除属性
delete obj.newKey;
console.log( obj.newKey ); //undefined

//-----------------测试是否可以再次修改特性------------------------
var obj = {}
//第一种情况：configurable设置为false，不能再次修改特性。
Object.defineProperty(obj,"newKey",{
    value:"hello",
    writable:false,
    enumerable:false,
    configurable:false
});

//重新修改特性
Object.defineProperty(obj,"newKey",{
    value:"hello",
    writable:true,
    enumerable:true,
    configurable:true
});
console.log( obj.newKey ); //报错：Uncaught TypeError: Cannot redefine property: newKey

//第二种情况：configurable设置为true，可以再次修改特性。
Object.defineProperty(obj,"newKey",{
    value:"hello",
    writable:false,
    enumerable:false,
    configurable:true
});

//重新修改特性
Object.defineProperty(obj,"newKey",{
    value:"hello",
    writable:true,
    enumerable:true,
    configurable:true
});
console.log( obj.newKey ); //hello
```

**提示：一旦使用Object.defineProperty给对象添加属性，那么如果不设置属性的特性，那么configurable、enumerable、writable这些值都为默认的false**

```javascript
var obj = {};
//定义的新属性后，这个属性的特性中configurable，enumerable，writable都为默认的值false
//这就导致了neykey这个是不能重写、不能枚举、不能再次设置特性
//
Object.defineProperty(obj,'newKey',{

});

//设置值
obj.newKey = 'hello';
console.log(obj.newKey);  //undefined

//枚举
for( var attr in obj ){
    console.log(attr);
}
```

__设置的特性总结：__
>value: 设置属性的值
 writable: 值是否可以重写。true | false
 enumerable: 目标属性是否可以被枚举。true | false
 configurable: 目标属性是否可以被删除或是否可以再次修改特性 true | false
 
#### 存取器描述

当使用存取器描述属性的特性的时候，允许设置以下特性属性：

```javascript
var obj = {};
Object.defineProperty(obj,"newKey",{
    get:function (){} | undefined,
    set:function (value){} | undefined
    configurable: true | false
    enumerable: true | false
});
```

__注意：当使用了getter或setter方法，不允许使用writable和value这两个属性__

##### getter/setter

当设置或获取对象的某个属性的值的时候，可以提供getter/setter方法。

    + getter 是一种获得属性值的方法
      
    +  setter是一种设置属性值的方法。

在特性中使用get/set属性来定义对应的方法。

```javascript
var obj = {};
var initValue = 'hello';
Object.defineProperty(obj,"newKey",{
    get:function (){
        //当获取值的时候触发的函数
        return initValue;    
    },
    set:function (value){
        //当设置值的时候触发的函数,设置的新值通过参数value拿到
        initValue = value;
    }
});
//获取值
console.log( obj.newKey );  //hello

//设置值
obj.newKey = 'change value';

console.log( obj.newKey ); //change value
```

__注意：get或set不是必须成对出现，任写其一就可以。如果不设置方法，则get和set的默认值为undefined configurable和enumerable同上面的用法。__

#### 兼容性
在ie8下只能在DOM对象上使用，尝试在原生的对象使用 Object.defineProperty()会报错。


## 8、类型判断

判断 Target 的类型，单单用 typeof 并无法完全满足，这其实并不是 bug，本质原因是 JS 的万物皆对象的理论。因此要真正完美判断时，我们需要区分对待:

+ 基本类型(`null`): 使用 `String(null)`
+ 基本类型(`string / number / boolean / undefined`) + `function`: 直接使用 `typeof`即可
+ 其余引用类型(`Array / Date / RegExp Error`): 调用`toString`后根据`[object XXX]`进行判断

**很稳的判断封装:**
```javascript
let class2type = {}
'Array Date RegExp Object Error'.split(' ').forEach(e => class2type[ '[object ' + e + ']' ] = e.toLowerCase()) 

function type(obj) {
    if (obj == null) return String(obj)
    return typeof obj === 'object' ? class2type[ Object.prototype.toString.call(obj) ] || 'object' : typeof obj
}

```


# 三、http、html和浏览器篇

## 1、http和https

https的SSL加密是在传输层实现的。

__(1)http和https的基本概念__

+ http: 超文本传输协议，是互联网上应用最为广泛的一种网络协议，是一个客户端和服务器端请求和应答的标准（TCP），用于从WWW服务器传输超文本到本地浏览器的传输协议，它可以使浏览器更加高效，使网络传输减少。

+ https: 是以安全为目标的HTTP通道，简单讲是HTTP的安全版，即HTTP下加入SSL层，HTTPS的安全基础是SSL，因此加密的详细内容就需要SSL。

+ https协议的主要作用是：建立一个信息安全通道，来确保数组的传输，确保网站的真实性。

__(2)http和https的区别？__

+ http传输的数据都是未加密的，也就是明文的，网景公司设置了SSL协议来对http协议传输的数据进行加密处理，简单来说https协议是由http和ssl协议构建的可进行加密传输和身份认证的网络协议，比http协议的安全性更高。
主要的区别如下：

+ Https协议需要ca证书，费用较高。

+ http是超文本传输协议，信息是明文传输，https则是具有安全性的ssl加密传输协议。

+ 使用不同的链接方式，端口也不同，一般而言，http协议的端口为80，https的端口为443

+ http的连接很简单，是无状态的；HTTPS协议是由SSL+HTTP协议构建的可进行加密传输、身份认证的网络协议，比http协议安全。

__(3)https协议的工作原理__

客户端在使用HTTPS方式与Web服务器通信时有以下几个步骤。

+ 客户使用https url访问服务器，则要求web 服务器建立ssl链接。

+ web服务器接收到客户端的请求之后，会将网站的证书（证书中包含了公钥），返回或者说传输给客户端。

+ 客户端和web服务器端开始协商SSL链接的安全等级，也就是加密等级。

+ 客户端浏览器通过双方协商一致的安全等级，建立会话密钥，然后通过网站的公钥来加密会话密钥，并传送给网站。

+ web服务器通过自己的私钥解密出会话密钥。

+ web服务器通过会话密钥加密与客户端之间的通信。

__(4)https协议的优点__

+ 使用HTTPS协议可认证用户和服务器，确保数据发送到正确的客户机和服务器；

+ HTTPS协议是由SSL+HTTP协议构建的可进行加密传输、身份认证的网络协议，要比http协议安全，可防止数据在传输过程中不被窃取、改变，确保数据的完整性。

+ HTTPS是现行架构下最安全的解决方案，虽然不是绝对安全，但它大幅增加了中间人攻击的成本。

+ 谷歌曾在2014年8月份调整搜索引擎算法，并称“比起同等HTTP网站，采用HTTPS加密的网站在搜索结果中的排名将会更高”。

__(5)https协议的缺点__

+ https握手阶段比较费时，会使页面加载时间延长50%，增加10%~20%的耗电。

+ https缓存不如http高效，会增加数据开销。

+ SSL证书也需要钱，功能越强大的证书费用越高。

+ SSL证书需要绑定IP，不能再同一个ip上绑定多个域名，ipv4资源支持不了这种消耗。

## 2、http2.0
首先补充一下，http和https的区别，相比于http,https是基于ssl加密的http协议
简要概括：http2.0是基于1999年发布的http1.0之后的首次更新。
   
+ 提升访问速度（可以对于，请求资源所需时间更少，访问速度更快，相比http1.0）
+ 允许多路复用：多路复用允许同时通过单一的HTTP/2连接发送多重请求-响应信息。改善了：在http1.1中，浏览器客户端在同一时间，针对同一域名下的请求有一定数量限制（连接数量），超过限制会被阻塞。
+ 二进制分帧：HTTP2.0会将所有的传输信息分割为更小的信息或者帧，并对他们进行二进制编码
+ 首部压缩
+ 服务器端推送

## 3、一个图片url访问后直接下载怎样实现？

请求的返回头里面，用于浏览器解析的重要参数就是OSS的API文档里面的返回http头，决定用户下载行为的参数。

下载的情况下：

```http request
  1. x-oss-object-type:
         Normal
  2. x-oss-request-id:
         598D5ED34F29D01FE2925F41
  3. x-oss-storage-class:
         Standard

```

## 4、iframe是什么？有什么缺点？

定义：iframe元素会创建包含另一个文档的内联框架
提示：可以将提示文字放在之间，来提示某些不支持iframe的浏览器

缺点：

+ 会阻塞主页面的onload事件

+ 搜索引擎无法解读这种页面，不利于SEO（search engine optimization） __搜索引擎优化__

+ iframe和主页面共享连接池，而浏览器对相同区域有限制所以会影响性能。

## 5、Doctype作用? 严格模式与混杂模式如何区分？它们有何意义?

Doctype声明于文档最前面，告诉浏览器以何种方式来渲染页面，这里有两种模式，严格模式和混杂模式。

+ 严格模式的排版和 JS 运作模式是 以该浏览器支持的最高标准运行。

+ 混杂模式，向后兼容，模拟老式浏览器，防止浏览器无法兼容页面。

## 6、Cookie如何防范XSS攻击

XSS（跨站脚本攻击）是指攻击者在返回的HTML中嵌入javascript脚本，为了减轻这些攻击，需要在HTTP头部配上，set-cookie：

+ httponly-这个属性可以防止XSS,它会禁止javascript脚本来访问cookie。

+ secure - 这个属性告诉浏览器仅在请求为https的时候发送cookie。

结果应该是这样的：Set-Cookie=.....

## 7、viewport和移动端布局

看了小姐姐的文章挺好的：

[响应式布局的常用解决方案对比(媒体查询、百分比、rem和vw/vh）](https://github.com/forthealllight/blog/issues/13)

# 四、CSS相关

## 1、画一条0.5px的线

+ 采用meta viewport的方式

+ 采用 border-image的方式

+ 采用transform: scale()的方式

## 2、link标签和import标签的区别

+ link属于html标签，而@import是css提供的

+ 页面被加载时，link会同时被加载，而@import引用的css会等到页面加载结束后加载。

+ link是html标签，因此没有兼容性，而@import只有IE5以上才能识别。

+ link方式样式的权重高于@import的。

## 3、多行元素的文本省略号

```css
 display: -webkit-box
-webkit-box-orient:vertical
-web-line-clamp:3
overflow:hidden

```
