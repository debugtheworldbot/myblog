# Vue

## Vue React的区别？

相同

* 对视图封装，R用类和函数表示一个组件，V通过构造选项构造一个组件
* 提供了createElement的xml缩写，R用JSX语法，V用模板写法

不同

* 
* 
* 常见语法

```
<div v-text='n+1' /> === {{n+1}}
<div v-html='x' /> //显示粗体的内容
v-for="(value,key) in 对象或数组" :key="key" // 必须加key
```



##  watch  computed methods的区别

watch:{ [key: string]: string | Function | Object | Array } 是监听数据 无缓存，watch是异步的，一般着重于执行一个函数。watch里不可以用箭头函数，因为this会指向window

computed:{ [key: string]: Function | { get: Function, set: Function } } 是计算属性，有缓存，着重于依赖之间的变化和缓存

methods:{ [key: string]: Function }

```js
new Vue({
 data:{
 	obj:{
 		a:'a'
 	}
 }
}
watch:{
	obj(){
		handler:{
			console.log('obj changed')
		}
		deep:true
// 如果改变obj.a的值，obj也会变化。相当于监听了obj里面的所有属性。“该回调会在任何被侦听的对象的 property 改变时被调用，不论其被嵌套多深”
	}
}).$mount(#app)
```



* computed 和 methods 相比，最大区别是 computed 有缓存：如果 computed 属性依赖的属性没有变化，那么 computed 属性就不会重新计算。methods 则是看到一次计算一次。
* watch 和 computed 相比，computed 是计算出一个属性（废话），而 watch 则可能是做别的事情（如上报数据)

computed和watch的区别：

* computed是计算属性，watch是监听属性
* computed调用时不需要加括号，根据依赖自动==缓存==，如果 computed 属性==依赖==的属性没有变化，那么 computed 属性就不会重新计算。
* watch有两个选项：immediate，表示第一次渲染是否要执行，deep，是否要==监听==对象里面的属性。变化了就会执行里面的函数

## Vue 的生命周期钩子函数

* beforeCreate
* created
* beforeMount
* mounted:一般在此周期内请求数据
* beforeUpdate
* updated
* beforeDestory
* destoryed

## Vue 实现组件间通信

* 父子组件：使用 v-on 通过事件通信 
  * 组件不能修改props外部数据
  * `$emit('update:value',value+1)`可以触发事件并传参
  * `$event`可以获取`$emit`的参数
* 爷孙组件：使用两次 v-on 通过爷爷爸爸通信，爸爸儿子通信实现爷孙通信
* 任意组件：使用 eventBus = new Vue() 来通信，eventBus.$on 和 eventBus.$emit 是主要API
* 任意组件：使用 Vuex 通信

## Vue 数据响应式

* 是vue实现UI的核心思路，当用户改变数据时，UI就会自动更新
* 使用 Object.defineProperty 把这些属性全部转为 getter/setter。Object.defineProperty是es5无法shim（shim可以将新的API引入到旧的环境中，而且仅靠就环境中已有的手段实现。），所以vue不支持IE8以下的版本
* 由于 JavaScript 的限制，Vue 不能检测到对象属性的添加或删除，解决方法是手动调用 Vue.set 或者 this.$set。使用 `Vue.set(object, propertyName, value)` 方法向嵌套对象添加响应式 property。
* 缺点：对于一些新增的属性无法响应，因为无法监听它。vue3解决了这个问题
* data：内部数据，props：外部数据



`Vue.set( target, propertyName/index, value )===this.$set`

作用：

1. 新增key
2. 自动创建代理和监听
3. 触发UI更新（但不会立即更新）

举例 ： `this.$set(this.obj,’b’,100)`

向响应式对象中添加一个 property，并确保这个新 property 同样是响应式的，且触发视图更新。它必须用于向响应式对象上添加新 property，因为 Vue 无法探测普通的新增 property (比如 `this.myObject.newProperty = 'hi'`)

## 进阶属性

### Mixin

mixin就是**复制**,并且会智能合并，可以同时存在组件和本身的内容

作用：减少data，methods，钩子的重复

```
import mixin from ...
export default{
	mixins:[mixin]
}
```

### provide inject

大范围，隔N代的数据共用（methods，data），祖先提供，后代注入

## Vuex

解决了两个问题

* 多个组件依赖于同一状态时，对于多层嵌套的组件的传参将会非常繁琐，并且对于兄弟组件间的状态传递无能为力。

* 来自不同组件的行为需要变更同一状态。以往采用父子组件直接引用或者通过事件来变更和同步状态的多份拷贝。以上的这些模式非常脆弱，通常会导致无法维护的代码。

Vuex 是一个专为 Vue.js 应用程序开发的**状态管理模式**。有五个核心概念

* State：Vuex 使用**单一状态树**，用一个对象就包含了全部的应用层级状态。至此它便作为一个“唯一数据源 ([SSOT](https://en.wikipedia.org/wiki/Single_source_of_truth))”而存在。这也意味着，每个应用将仅仅包含一个 store 实例。
* Getter：Vuex 允许我们在 store 中定义“getter”（可以认为是 store 的计算属性）。就像计算属性一样，getter 的返回值会根据它的依赖被缓存起来，且只有当它的依赖值发生了改变才会被重新计算。
* Mutation：更改 Vuex 的 store 中的状态的唯一方法是提交 mutation。Vuex 中的 mutation 非常类似于事件：每个 mutation 都有一个字符串的 **事件类型 (type)** 和 一个 **回调函数 (handler)**。
* Action：Action 类似于 mutation，不同在于：
  - Action 提交的是 mutation，而不是直接变更状态。
  - Action 可以包含任意异步操作。
* Module：Vuex 允许我们将 store 分割成**模块（module）**。每个模块拥有自己的 state、mutation、action、getter、甚至是嵌套子模块——从上至下进行同样方式的分割。

## VueRouter

Vue Router 是 [Vue.js](http://cn.vuejs.org/) 官方的路由管理器。它和 Vue.js 的核心深度集成，让构建单页面应用变得易如反掌。

`vue-router` 默认 hash 模式 —— 使用 URL 的 hash 来模拟一个完整的 URL，当 URL 改变时，页面不会重新加载。

**history 模式**，这种模式充分利用 `history.pushState` API 来完成 URL 跳转而无须重新加载页面。

### 导航守卫

 导航表示路由正在发生改变。

`vue-router` 提供的导航守卫主要用来通过跳转或取消的方式守卫导航。有多种机会植入路由导航过程中：全局的, 单个路由独享的, 或者组件级的。

### 路由懒加载

当打包构建应用时，JavaScript 包会变得非常大，影响页面加载。如果我们能把不同路由对应的组件分割成不同的代码块，然后当路由被访问的时候才加载对应组件，这样就更加高效了。

结合 Vue 的[异步组件](https://cn.vuejs.org/v2/guide/components-dynamic-async.html#异步组件)和 Webpack 的[代码分割功能](https://doc.webpack-china.org/guides/code-splitting-async/#require-ensure-/)，轻松实现路由组件的懒加载。

`import('./Foo.vue')`

### 常用api

* router-link： 组件支持用户在具有路由功能的应用中 (点击) 导航。 通过 `to` 属性指定目标地址，默认渲染成带有正确链接的 `<a>` 标签，可以通过配置 `tag` 属性生成别的标签.。另外，当目标路由成功激活时，链接元素自动设置一个表示激活的 CSS 类名。

* router-view: 组件是一个 functional 组件，渲染路径匹配到的视图组件。 渲染的组件还可以内嵌自己的 `<router-view>`，根据嵌套路径，渲染嵌套组件。

* this.$router.push

* this.$router.replace 

  ```js
  router.push(location, onComplete?, onAbort?)
  router.push(location).then(onComplete).catch(onAbort)
  router.replace(location, onComplete?, onAbort?)
  router.replace(location).then(onComplete).catch(onAbort)
  ```

* 

* this.$route.params:一个 key/value 对象，包含了动态片段和全匹配片段，如果没有路由参数，就是一个空对象。

# dom

事件捕获：外=>内找监听函数

事件冒泡：内=>外找监听函数

target是用户触发的，currentTarget是开发者用于监听的

```
target.addEventListener(type('click'), listener(fn()), useCapture(boll));
```

boll如果不填/为falsy就默认是冒泡，为true就是捕获

代码示例：

[http://js.jirengu.com/zirozujedu/1/edit?html,js,output]: 

如果每个level.addEventListener的第三个参数都为true的话，那么是从外向内移除x的class的，反之则是从内向外。

如果同时有捕获和冒泡，那么先执行捕获，在执行冒泡。ps：冒泡可以阻止

如果是同级元素，即监听的元素就是用户触发的元素，那么按顺序执行（即先监听的执行）

## 捕获不可取消，冒泡可以

`e.stopPropagation()`可以中断冒泡

有些元素无法阻止冒泡，如scroll event

![image-20200602210949265](C:\Users\HY\AppData\Roaming\Typora\typora-user-images\image-20200602210949265.png)

Cancelable为No即代表无法阻止。那我就是不信，非要阻止滚动该怎么实现呢？

`x.addEventListener('scroll',(e)=>{e.stopPropagation})`这样是不行哒

可以换一种思路，阻止不了scroll，阻止wheel的默认事件不就行了

`x.addEventListener('wheel',(e)=>{e.preventDefault})`果然滚不了了

然后可以在css中隐藏滚动条`::webkit-scrollbar{width:0}`就行了

在手机页面可以使用`x.addEventListener('touchstart',(e)=>{e.preventDefault})`来实现

## 自定义事件

``````js
const event=new CustomEvent('name',{
'detail':{age:18},
bubbles:true,  //可以冒泡
cancelable:false}) // 无法阻止冒泡
// 如果要让click后执行event
button.addEventListener('click',()=>{
	button.dispatchEvent(event)
})
// 然后就可以使用啦
button.addEventListener('name',(e)=>{
	console.log(e.detail) //output:{age:18}
})
``````

## 事件委托

实现一个函数`on('click','#div1','button',(e)=>{console.log("button was clicked")})`

```js
function on(eventType,element,selector,fn){
	if(!(element instanceof Element)){
		element = document.querySelector(element)
	} 
	element.addEventListener(eventType,(e)=>{
		const t=e.target
		if(t.matches(selector)){
			fn(e)
		}
	})
}
```

不过还是有点小问题，就是假如button里面有一个span，那么点击span是无法触发事件的，因为e.target为span，而selector为button，所以fn无法执行，解决方法就是 <del>不要这么做</del> 使用递归判断，有点麻烦。

## 事件委托的优缺点

优点是可以把事件监听放在祖先元素上

* 可以节约监听数量
* 可以监听动态生成的元素，当元素删除时也不用处理函数的解绑

缺点是事件委托是基于冒泡的

* 如果某些事件不支持冒泡，如onfoucs和onblur，那么也就不支持事件委托。
* 如果层级过多，冒泡过程中，可能会被某层阻止掉（建议就近委托）

# ajax

异步的javascript和xml  AJAX 是一种用于创建快速动态网页的技术。  ajax用来与后台交互

```js
const request=new XMLHttpRequest
request.open("get",url)
request.onreadystatechange=()=>{
    if(request.readyState===4 && request.status === 200){
        console.log(request.response)
    }else{
        console.log('failed')
    }
}
request.send()
```



## 跨域

出于浏览器的同源策略限制，浏览器会拒绝跨域请求。

非同源请求，均为跨域。名词解释：*同源 —— 如果两个页面拥有相同的协议（protocol），端口（port）和主机（host），那么这两个页面就属于同一个源（origin）*

why？ 工程服务化后，不同职责的服务分散在不同的工程中，往往这些工程的域名是不同的，但一个需求可能需要对应到多个服务，这时便需要调用不同服务的接口，因此会出现跨域。

### CORS

增加一些 HTTP 头，让服务器能声明允许的访问来源。

在server（服务器端）中加入下面一句话即可

`response.setHeader('Access-Control-Allow-Origin','url')`即可。如果要让所有的域名都能访问，可以将url代替为request.headers['referer']

![image-20200519223814679](C:\Users\HY\AppData\Roaming\Typora\typora-user-images\image-20200519223814679.png)

### jsonp

#####   



![image-20200519225536414](C:\Users\HY\AppData\Roaming\Typora\typora-user-images\image-20200519225536414.png)

<img src="C:\Users\HY\AppData\Roaming\Typora\typora-user-images\image-20200519225608283.png" alt="image-20200519225608283" style="zoom:50%;" />

原理：动态创建一个script标签。利用script标签的src属性不受同源策略限制。因为所有的src属性和href属性都不受同源策略限制。可以请求第三方服务器数据内容。

不能使用CORS情况下的一种解决方案。通过创建一个script请求一个js文件，js会执行一个回调，回调里面就有需要的数据（callback可以是个随机数）。jsonp正是利用这个特性来实现的。 

优点：兼容ie，可以跨域

缺点：由于使用的script标签，所以读不到像ajax一样拿到状态码；只能发get请求，不支持post

jsonp指定网站访问：

![image-20200520084642678](C:\Users\HY\AppData\Roaming\Typora\typora-user-images\image-20200520084642678.png)



## 前端路由

简单的说，就是在保证只有一个 HTML 页面，且与用户交互时不刷新和跳转页面的同时，为 SPA (single page web application)中的每个视图展示形式匹配一个特殊的 url。在刷新、前进、后退和SEO时均通过这个特殊的 url 来实现。

### hash

这里的 hash 就是指 url 后的 # 号以及后面的字符。

### history



## cookie session

Cookie 是服务器发送到用户浏览器并保存在本地的一小块数据，它会在浏览器下次向同一服务器再发起请求时被携带并发送到服务器上。

Session 代表着服务器和客户端一次会话的过程。当用户在应用程序的 Web 页之间跳转时，存储在 Session 对象中的变量将不会丢失，而是在整个用户会话中一直存在下去。当客户端关闭会话，或者 Session 超时失效时会话结束。

* 作用范围不同，Cookie 保存在客户端（浏览器），Session 保存在服务器端。
* 有效期不同，Cookie 可设置为长时间保持，比如我们经常使用的默认登录功能，Session 一般失效时间较短，客户端关闭或者 Session 超时都会失效。
* 隐私策略不同，Cookie 存储在客户端，比较容易遭到非法获取，早期有人将用户的登录名和密码存储在 Cookie 中导致信息被窃取；Session 存储在服务端，安全性相对 Cookie 要好一些。
* 存储大小不同， 单个 Cookie 保存的数据不能超过 4K，Session 可存储数据远高于 Cookie。

# 原型和原型链

原型和实例的关系：每个构造函数(constructor)都有一个原型对象(prototype),原型对象都包含一个指向构造函数的指针,而实例(instance)都包含一个指向原型对象的内部指针。一个对象的原型指的是这个对象与其他同类对象的公有属性的集合。一个对象的原型的地址存在这个对象的 `__proto__ `属性里

如果试图引用对象(实例instance)的某个属性,会首先在对象内部寻找该属性,直至找不到,然后才在该对象的原型(instance.prototype)里去找这个属性，如果还找不到，就向原型的原型寻找...直至Object的原型对象。

`instanceof`,返回Boolean。用于检测构造函数的prototype（`constractor.prototype`）是否出现在某个实例对象的原型链上

### 原型链的问题

1. 当原型链中包含引用类型值的原型时,该引用类型值会被所有实例共享;
2. 在创建子类型时,不能向其构造函数中传递参数.

解决方法：借用构造函数

```
function Father(){
	this.colors = ["red","blue","green"];
}
function Son(){
	Father.call(this);//继承了Father,且向父类型传递参数
}
var instance1 = new Son();
instance1.colors.push("black");
console.log(instance1.colors);//"red,blue,green,black"

var instance2 = new Son();
console.log(instance2.colors);//"red,blue,green" 可见引用类型值是独立的

```

1. 保证了原型链中引用类型值的独立,不再被所有实例共享;

2. 子类型创建时也能够向父类型传递参数.

### new运算符

new主要做了三件事情：

```
var obj  = {};
obj.__proto__ = F.prototype; // 我们将这个空对象的__proto__成员指向了F函数对象prototype成员对象;
F.call(obj); //我们将F函数对象的this指针替换成obj，然后再调用F函数.
```

# promise

Promise 是异步编程的一种解决方案，可以解决传统的回调地狱的问题。比传统的异步解决方案【回调函数】和【事件】更合理、更强大。

Promise构造函数执行时立即调用`executor` 函数，executor是带有 `resolve` 和 `reject` 两个参数的函数 。一旦异步操作执行完毕(可能成功/失败)，要么调用resolve函数来将promise状态改成*fulfilled*，要么调用`reject` 函数将promise的状态改为rejected。

三种状态：pending：初始状态，fullfilled，rejected

因为 `Promise.prototype.then` 和 `Promise.prototype.catch` 方法返回promise 对象， 所以它们可以被链式调用。

缺点：**无法取消 Promise ，错误需要通过回调函数来捕获**

## async await

**优点是：代码清晰，不用像 Promise 写一大堆 then 链，处理了回调地狱的问题**

**缺点：await 将异步代码改造成同步代码，如果多个异步操作没有依赖性而使用 await 会导致性能上的降低**

### 为什么要用async？

promise().then(f1,f2).then(f3,f4)它不香吗？为什么还需要用awite呢？

简单来说，就是对同步的追求,虽然写起来麻烦，但是看起来更易懂。

async是可以和promise结合使用的函数，同时它可以将异步函数变得**更像是标准的同步函数**。

普通的promise函数：

```
doSomething()
.then(result => doSomethingElse(value))
.then(newResult => doThirdThing(newResult))
.then(finalResult => console.log(`Got the final result: ${finalResult}`))
.catch(failureCallback);

复制代码
```

利用async和await：

```
async function foo() {
  try {
    const result = await doSomething();
    const newResult = await doSomethingElse(result);
    const finalResult = await doThirdThing(newResult);
    console.log(`Got the final result: ${finalResult}`);
  } catch(error) {
    failureCallback(error);
  }
}
// 更像是同步函数
复制代码
```

如果要使用await，那么必须用async函数把它**包裹起来**。

await函数无法同时接受两个promise，即还需要promise.all或promise.race来实现，async/await更像是promise的语法糖，使基本的promise函数更加易懂。

# React

遵循组件设计模式、声明式编程范式和函数式编程概念

函数式编程，函数是数据，你可以像保存变量一样在应用程序中保存、检索和传递这些函数。核心概念如下

* 不可变性(Immutability)： 如果要改变或更改数据，则必须复制数据副本来更改。
* 纯函数(Pure Functions)：纯函数是始终接受一个或多个参数并计算参数并返回数据或函数的函数。 它没有副作用，例如设置全局状态，更改应用程序状态，它总是将参数视为不可变数据。
* 数据转换(Data Transformations)
* 高阶函数 (Higher-Order Functions)：高阶函数是将函数作为参数或返回函数的函数，或者有时它们都有。 这些高阶函数可以操纵其他函数。
* 递归：最好使用递归而不是循环。
* 组合

## 最难的技术问题？

1. 想在react上用sass
2. 要安装node-sass，会出现各种问题，一堆报错
3. 尝试用dart-sass代替，但是react不支持
4. Google之后我发现npm 6.9 支持一个新功能，package alias
5. 用yarn add node-sass@npm:dart-sass`实现偷梁换柱
6. done

## config

使用CSS-in-JS，香

yarn add --dev @types/styled-conponents

安装支持ts的styled-components

就可以在tsx文件里使用带标签的模板字符串

const Button=styled.button`

​	color:red

`

然后在return的html中直接使用即可

这样使用该标签就自带样式，可以复用，不用想className

## react-router

yarn add --dev @types/react-router-dom

<switch>里面的内容是router的渲染区域

无后台服务器，使用hashrouter

HashRouter就是在网址后自动添加锚点/#/，不需要后台支持就可以实现页面切换

## 布局

手机上尽量不要用fixed定位，有很多很多问题

css使用绝对定位之后宽度会变成0 ，要加width:100%

如果父元素有flex布局，那么其子元素使用float布局是就不用清除浮动

### svg symbols

自定义webpack config

* yarn eject
* svg-sprite-loader,在webpackconfig.js的351行加入svg

TreeShaking：没有用过的函数就会被删除

```jsx
import y from "icons/tag.svg";
```

若不log y ，那么则无法使用，因为treeShaking。用require即可，因为treeShaking不适用于require



## function

react onChange在输入时就会触发

html onchange在失去焦点时才会触发，早于onBlur

react核心是不可变数据，想要更改数据最好拷贝之后修改拷贝

## 类组件和函数组件

元素：`const div=React.createElement('div',...)`

组件：`const Div=()=>React.createElement('div',...)`

有两种组件：函数组件和类组件

### 函数组件

函数或无状态组件是一个纯函数，它可接受接受参数，并返回react元素。这些都是没有任何副作用的纯函数。这些组件**没有状态或生命周期**方法

```jsx
function Welcome(props){
 const [n,setN]=React.useState(0)
 return <div>hello,{props.name}|| n:{n}</div>
}
// 使用
<Welcome name='niko' />
```

读取外部数据props用`{props.xxx}`

### 函数组件的hooks

Hooks 让我们在函数组件中可以使用state 和其他功能。

useState，useEffect，useContext，useReducer等。

- Hooks 应该在外层使用，不应该在循环，条件或嵌套函数中使用

- Hooks 应该只在函数组件中使用。

  ##### 问题

  * 没有state：用useState解决
  * 没有生命周期：用useEffect解决

#### useState

```jsx
const [state, setState] = useState(initialState);
```

读取内部数据state用`useState`返回数组，第一项读，第二项写。第二项setN不会改变n，而是返回一个新的n。setN不会自动合并属性（不可局部更新）。需要用`setN(...xxx,n+1)`来复制未改变的属性值

#### useEffect

`useEffect(fn,[])`，如果第二个参数为空数组就是只在第一次渲染时调用（仅在组件挂载和卸载时执行），相当于componentDidMount。如果第二个参数填需要监控的变量就是相当于componentDidUpdate，不填就是监控所有的state变化。

fn里面return一个函数就可以模拟类似componentWillUnmount

#### useRef

```jsx
const refContainer = useRef(initialValue);
```

`useRef` 返回一个可变的 ref 对象，其 `.current` 属性被初始化为传入的参数（`initialValue`）。返回的 ref 对象在组件的整个生命周期内保持不变，也就是只有一个current值。

current改变时不会重新渲染。

#### useContext

```jsx
const ThemeContext = React.createContext(themes.light);
    <ThemeContext.Provider value={themes.dark}>
     ... //这里面的内容才可以使用theme组件（可以看成是个作用域）
    </ThemeContext.Provider>
```

作用域里的组件都可以调用里面的方法，其实没什么卵用，基本可以用useRef代替

#### useReducer

```jsx
const [state, dispatch] = useReducer(reducer, initialArg, init);
```



### 类组件

```jsx
class Welcome extends React.Component{
	constractor(props){
	super(props)
	this.state={}
	}
	
	render(){
	return <div>hello,{this.props.name}</div>
	}
} 
// 使用
<Welcome name='niko' />
```

读取外部数据props用`{this.props.xxx}`

读取内部数据state用`this.state`读 ，用`this.setState`写。ps：setState是一个**异步**更新UI的函数，setState会自动合并第一层属性（shallow merge）

类组件用起来很麻烦，还是函数组件香~

## props作用

* 接受外部数据：只读属性，外部数据由父组件传递
* 接受外部函数：合适时调用函数，一般是父组件的函数

## 受控组件 V.S. 非受控组件

### 受控组件

受控组件是在 React 中处理输入表单的一种技术

使 React 的 state 成为“唯一数据源”。渲染表单的 React 组件还控制着用户输入过程中表单发生的操作。被 React 以这种方式控制取值的表单输入元素就叫做“受控组件”。

react控制input的value和onchange

```jsx
 class NameForm extends React.Component {
  constructor(props) {
    super(props);
    this.state = {value: ''};
    this.handleChange = this.handleChange.bind(this);
  }

  handleChange(event) {
    this.setState({value: event.target.value});
  }
 ...
  <input type="text" value={this.state.value} onChange={this.handleChange} />
```

### 非受控组件

如果不是为每个状态更新都编写数据处理函数，你可以 [使用 ref](https://zh-hans.reactjs.org/docs/refs-and-the-dom.html) 来从 DOM 节点中获取表单数据。也就是`Ref`用于直接从`DOM`访问表单值，而不是事件处理程序。

类组件：`React.createRef()`; 函数组件：useRef()

`<input type="text" ref={refInput} defaultValue={value}/>`

因为非受控组件将真实数据储存在 DOM 节点中，所以在使用非受控组件时，反而更容易同时集成 React 和非 React 代码。如果希望快速编写代码，使用非受控组件往往可以减少你的代码量。否则，你应该使用受控组件。

```jsx
class NameForm extends React.Component {
  constructor(props) {
    super(props);
    this.handleSubmit = this.handleSubmit.bind(this);
    this.input = React.createRef(); 
    }

  handleSubmit(event) {
    alert('A name was submitted: ' + this.input.current.value);    event.preventDefault();
  }

  render() {
    return (
      <form onSubmit={this.handleSubmit}>
        <label>
          Name:
          <input type="text" ref={this.input} />        </label>
        <input type="submit" value="Submit" />
      </form>
    );
  }
}
```

## REACT生命周期

![img](https://user-gold-cdn.xitu.io/2019/12/15/16f0a0b3e20fa9aa?imageslim)

### 钩子简述

#### shouldCompenentUpdate

shouldCompenentUpdate(nextProps,nextState){} 是否需要更新组件

可以手动判断是否要进行组件更新，可根据场景灵活设置返回值，宜避免不必要的更新

可以在类组件定义时使用`extends PureComponent`

PureComponent会在 render 之前对比新 state 和旧 state 的每一个 key，以及新 props 和旧 props 的每一个 key。如果所有 key 的值全都一样，就不会 render；如果有任何一个 key 的值不同，就会 render。

#### render

* 用来展示视图
* 只能有一个根元素
* 如果想要有一个以上的根元素，可以用`<React.Fragment>`包起来，简写为`<>`

#### componentDidMount

* 在元素插入页面后执行代码，依赖DOM，它没有参数
* 在这里发起**加载**数据的AJAX请求
* 首次渲染会执行此钩子

#### componentDidUpdate

* 在视图更新后执行代码
* 可以发起**更新**数据的AJAX请求
* 首次渲染不会执行此钩子

#### componentWillUnmount

* 组件将要被**移除页面然后被销毁**时执行的代码
* unmount过的代码不会再次mount
* 应该取消那些长期有效的请求（AJAX，timer等）



## React 实现组件间通信

props，context（useContext/createContext）

## 虚拟 DOM

虚拟 DOM 就是用来模拟 DOM 的一个对象，这个对象拥有一些重要属性，并且更新 UI 主要就是通过对比（DIFF）旧的虚拟 DOM 树 和新的虚拟 DOM 树的区别，并将这些变化更新到实际DOM，一旦真正的DOM更新，就会更新UI

![img](https://user-gold-cdn.xitu.io/2019/5/31/16b0b4220341b8ae?imageslim)

DOM 完全不属于 Javascript (也不在 Javascript 引擎中存在).。Javascript 其实是一个非常独立的引擎，DOM 其实是浏览器引出的一组让 Javascript 操作 HTML 文档的 API 而已。在即时编译的时代，调用 DOM 的开销是很大的。而 Virtual DOM 的执行完全都在 Javascript 引擎中，完全不会有这个开销。

## 高阶组件

高阶组件（HOC：higherOrderComponent）是 React 中用于复用组件逻辑的一种高级技巧。HOC 自身不是 React API 的一部分，它是一种基于 React 的组合特性而形成的设计模式。

## DOM DIFF

​	对比两棵虚拟dom树的区别，然后找出其中不同的部分

1. Web UI 中 DOM 节点跨层级的移动操作特别少，可以忽略不计。
2.  拥有相同类的两个组件将会生成相似的树形结构，拥有不同类的两个组件将会生成不同的树形结构。
3.  对于同一层级的一组子节点，它们可以通过唯一 id 进行区分。

以上三个策略，React 分别对 tree diff、component diff 以及 element diff 进行算法优化，它保证了整体界面构建的性能。

### tree diff

基于策略一，React 对树的算法进行了简洁明了的优化，即对树进行分层比较，两棵树只会对同一层次的节点进行比较。

如果出现dom节点跨层级的移动操作，因为该节点已经不在原来的dom树层， 所以会直接删除该节点，在移动后的dom层重建该节点， 可见这种操作的性能代价非常大，所以不推荐这样做。 可以通过css样式控制节点的隐藏和显示来代替节点跨层级移动的操作。

### component diff

如果是同一类型的组件，按照原策略继续比较 virtual DOM tree。

如果不是，则将该组件判断为 dirty component，从而替换整个组件下的所有子节点。

可以通过 shouldComponentUpdate() 来判断该组件是否需要进行 diff。

### element diff

当节点处于同一层级时，React diff 提供了三种节点操作，分别为：INSERT_MARKUP（插入）、MOVE_EXISTING（移动）和 REMOVE_NODE（删除）。

* **INSERT_MARKUP**，新的 component 类型不在老集合里， 即是全新的节点，需要对新节点执行插入操作。
* **MOVE_EXISTING**，在老集合有新 component 类型，且 element 是可更新的类型，generateComponentChildren 已调用 receiveComponent，这种情况下 prevChild=nextChild，就需要做移动操作，可以复用以前的 DOM 节点。
* **REMOVE_NODE**，老 component 类型，在新集合里也有，但对应的 element 不同则不能直接复用和更新，需要执行删除操作，或者老 component 不在新集合里的，也需要执行删除操作。

## redux

Redux 是 JavaScript 状态容器，提供可预测化的**状态管理**。

Redux 是 React的一个状态管理库，它基于flux。 Redux简化了React中的单向数据流。 Redux将状态管理完全从React中抽象出来。

* Action/
* Reducer/
* Store/
* 单向数据流

## connect 原理

react-redux 库提供的一个 API，connect 的作用是让你把组件和store连接起来，产生一个新的组件（connect 是高阶组件）

## 三次握手

# 闭包简述

**闭包**（Closure）是由函数和与其相关的引用环境组合而成的实体。例如在实现深约束时，需要创建一个能显式表示引用环境的东西，并将它与相关的子程序捆绑在一起，这样捆绑起来的整体被称为闭包。也就是 **「函数」和「函数内部能访问到的变量」（也叫环境）的总和**

闭包只是形式和表现上像函数，但是并不是函数。函数在被定义之后就确定了，不会在执行时发生变化，所以函数只有一个实例。闭包在运行时可以有多个实例，不同的引用环境和相同的函数组合可以产生不同的实例。所谓引用环境是指在程序执行中的某个点所有处于活跃状态的约束所组成的集合。其中的约束是指一个变量的名字和其所代表的对象之间的联系。

## 创建闭包

只要在一个函数中再定义一个函数，这个内部函数就是一个闭包。

```
function add(y){
    return function(x){
        return x+y
    }
}
复制代码
```

function(x)的返回值，依赖于外部的自由变量y，add（y）就是包含自由变量y的环境，且js允许内部函数访问外部函数的的局部变量。此时add（y）就对function（x）构成了闭包。

看下一个例子：

```
function makeFunc() {
    var name = "Mozilla";
    function displayName() {
        alert(name);
    }
    return displayName;
}

var myFunc = makeFunc();
myFunc();
复制代码
```

和之前不同的是，内部函数displayName() 在执行前，就从外部函数返回。而代码可以正常运行。因为myFunc 是执行 makeFunc 时创建的 displayName 函数实例的引用。displayName 的实例维持了一个对它的词法环境（变量 name 存在于其中）的引用。因此，当 myFunc 被调用时，变量 name 仍然可用，其值 Mozilla 就被传递到alert中。

加速：

```
function makeAdder(x) {
  return function(y) {
    return x + y;
  };
}

var add5 = makeAdder(5);
var add10 = makeAdder(10);

console.log(add5(2));  // 7
console.log(add10(2)); // 12
复制代码
```

makeAdder(x) 函数接受一个参数 x ，并返回一个新的函数。返回的函数接受一个参数 y，并返回x+y的值

## 闭包的作用

闭包很有用，因为它允许将函数与其所操作的某些数据（环境）关联起来。这显然类似于面向对象编程。在面向对象编程中，对象允许我们将某些数据（对象的属性）与一个或者多个方法相关联。

因此，通常你使用只有一个方法的对象的地方，都可以使用闭包。

在 Web 中，这种情况特别常见。大部分我们所写的 JavaScript 代码都是基于事件的 — 定义某种行为，然后将其添加到用户触发的事件之上（比如点击或者按键）。我们的代码通常作为回调：为响应事件而执行的函数。

## 闭包的缺点

如果不是某些特定任务需要使用闭包，在其它函数中创建函数是不明智的，因为闭包在处理速度和内存消耗方面对脚本性能具有负面影响。

例如，在创建新的对象或者类时，方法通常应该关联于对象的原型，而不是定义到对象的构造器中。原因是这将导致每次构造器被调用时，方法都会被重新赋值一次（也就是，每个对象的创建）。

# 函数节流和函数防抖

## 函数节流

规定一个单位时间，在这个单位时间内，只能有一次触发事件的回调函数执行，如果在同一个单位时间内某事件被触发多次，只有一次能生效。

一个形象的例子就是游戏中**技能的CD**时间。

```
function throttle(fn,delay){
    let canUse=true
    return function(){
        if(canUse){
            fn.apply(this,arguments)
            canUse=false
            setTimeout(()=>canUse=true,delay)
        }
    }
}

const throttled  = throttle(()=>console.log('hi'))
throttled()
throttled()
throttled()
 // 只会输出一个hi，因为在CD
复制代码
```

## 函数防抖

在事件被触发n秒后再执行回调，如果在这n秒内又被触发，则重新计时。

有点像坐电梯，假设每个人都会按电梯按钮，那么每次有人进来时电梯的关门时间就会重置。

```
function debounce(fn,delay){
    let timerId = null
    return function(){
        const context = this
        if(timerId){window.clearTimeout(timerId)}
        timerId=>setTimeout(()=>{
        fn.apply(context,arguments)
        timerId=null
        },delay)
    }
}

const debounced = debounce(()=>console.log('hi'))
debounced()
debounced()
```

# async/await的用法



# 浅拷贝与深拷贝

常用的拷贝方法：

![img](https://user-gold-cdn.xitu.io/2020/5/10/171fc94cdb1ba5c6?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

其中Array.prototype.concat()和Array.prototype.slice()修改新对象会改到原对象，也就是浅拷贝



### 基础的深拷贝

```
function clone(source) {
    var target = {};
    for(var i in source) {
        if (source.hasOwnProperty(i)) {
            if (typeof source[i] === 'object') {
                target[i] = clone(source[i]); // 注意这里
            } else {
                target[i] = source[i];
            }
        }
    }

    return target;
}
复制代码
```

基本方法就是递归+判断类型，但是还是有很多问题

### 一行代码实现深拷贝

JSON.parse(JSON.stringify())实现深拷贝，原理是 用JSON.stringify将对象转成JSON字符串，再用JSON.parse()把字符串解析成对象，一去一来，新的对象产生了，而且对象会开辟新的栈，实现深拷贝。

该方法使用的是递归的方式，同时JSON.stringify内部做了循环引用的检测,所以不会因为死循环导致栈溢出：

```
let a = {};
a.a = a;

cloneJSON(a) // Uncaught TypeError: Converting circular structure to JSON
复制代码
```

这种方法虽然可以实现数组或对象深拷贝,但**不能处理函数**。这是因为JSON.stringify() 方法是将一个JavaScript值(对象或者数组)转换为一个 JSON字符串，不能接受函数

```
let arr = [1, 3, {
    username: ' kobe'
},function(){}];
let arr4 = JSON.parse(JSON.stringify(arr));
arr4[2].username = 'duncan'; 
console.log(arr, arr4)

复制代码
```



![img](https://user-gold-cdn.xitu.io/2020/5/10/171fca520ddd2130?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)



arr4的函数为null

# QA

## 写 React / Vue 项目时为什么要在列表组件中写 key，其作用是什么？

vue和react都是采用diff算法来对比新旧虚拟节点，从而更新节点。
在交叉对比中，当新节点跟旧节点**头尾交叉对比**没有结果时，会根据新节点的key去对比旧节点数组中的key，从而找到相应旧节点。如果没找到就认为是一个新增节点。而如果没有key，那么就会采用遍历查找的方式去找到对应的旧节点。一种一个map映射，另一种是遍历查找。相比而言。map映射的速度更快。

## 防抖和节流？有什么区别？如何实现？

**防抖**：触发高频事件后n秒内函数只会执行一次，如果n秒内高频事件再次被触发，则重新计算时间。

  		每次触发事件时都取消之前的延时调用方法

**节流**：高频事件触发，但在n秒内只会执行一次，所以节流会稀释函数的执行频率

​			每次触发事件时都判断当前是否有等待执行的延时函数

## Set、Map、WeakSet 和 WeakMap 的区别

### set

1. 成员不能重复，可以存储任意类型的唯一值
2. 只有健值，没有健名，有点类似数组。
3.  可以遍历，方法有add, delete,has,size,clear等

### weakset

1. 成员都是对象
2. 成员都是弱引用，如果没有其他的对`WeakSet`中对象的引用，那么这些对象会被当成垃圾回收掉。 可以用来保存DOM节点，不容易造成内存泄漏
3. 不能遍历，方法有add, delete,has

### map

1. 本质上是健值对的集合，类似集合
2. 可以遍历，方法很多，可以转换成多种数据类型

### weakmap

1. 直接受对象作为健名（null除外），不接受其他类型的值作为健名
2. 健名所指向的对象，不计入垃圾回收机制
3. 不能遍历，方法同get,set,has,delete

## ES5/ES6 的继承除了写法以外还有什么区别

1. `class` 声明会提升，但不会初始化赋值。`Foo` 进入暂时性死区，类似于 `let`、`const` 声明变量。
2. `class` 声明内部会启用严格模式。
3. `class` 的所有方法（包括静态方法和实例方法）都是不可枚举的。
4. `class` 的所有方法（包括静态方法和实例方法）都没有原型对象 prototype，所以也没有`[[construct]]`，不能使用 `new` 来调用。
5. 必须使用 `new` 调用 `class`。
6. `class` 内部无法重写类名。

### setTimeout、Promise、Async/Await 的区别

// 在事件循环中的区别，事件循环中分为**宏任务队列**和**微任务队列**。
settimeout的回调函数放到宏任务队列里，等到执行栈清空以后执行；

promise.then里的回调函数会放到相应宏任务的微任务队列里，等宏任务里面的同步代码执行完再执行； 微任务响应速度相比setTimeout（setTimeout是task宏任务）会更快，因为无需等待渲染。

async函数表示函数里面可能会有异步方法，await后面跟一个表达式，async方法执行时，遇到await会立即执行表达式，然后把表达式后面的代码放到微任务队列里，让出执行栈让同步代码先执行。

## 数组去重

```
var arr = [ [1, 2, 2], [3, 4, 5, 5], [6, 7, 8, 9, [11, 12, [12, 13, [14] ] ] ], 10];

1. Array.from(new Set(arr.toString().split(',').sort((a,b)=>{return a-b})))

2. Array.from(new Set(arr.flat(Infinity))).sort((a,b)=>{ return a-b})
```

## TCP三次握手四次挥手

**三次握手**是建立一个TCP连接时，需要客户端和服务器总共发送3个包

是保证client和server均让对方知道自己的接收和发送能力没问题而保证的最小次数。

1. client => server  发送一个syn报文并指明客户端的初始化序列号 ISN(c) 只能server判断出client具备发送能力
2. server => client 发送自己的SYN 报文作为应答，并且也是指定了自己的初始化序列号 ISN(s)，同时会把客户端的 ISN + 1 作为ACK 的值。 
3. client => server 客户端收到 SYN 报文之后，会发送一个 ACK 报文，当然，也是一样把服务器的 ISN + 1 作为 ACK 的值，表示已经收到了服务端的 SYN 报文

其中，为了保证后续的握手是为了应答上一个握手，每次握手都会带一个标识 seq，后续的ACK都会对这个seq进行加一来进行确认。

TCP 的连接的拆除需要发送四个包，因此称为**四次挥手**，客户端或服务器均可主动发起挥手动作。

1. 客户端发送一个 FIN 报文，报文中会指定一个序列号。此时客户端处于 `FIN_WAIT1` 状态。
2. 服务端收到 FIN 之后，会发送 ACK 报文，且把客户端的序列号值 +1 作为 ACK 报文的序列号值，表明已经收到客户端的报文了，此时服务端处于 `CLOSE_WAIT` 状态。
3. 如果服务端也想断开连接了，和客户端的第一次挥手一样，发给 FIN 报文，且指定一个序列号。此时服务端处于 `LAST_ACK` 的状态。
4. 客户端收到 FIN 之后，一样发送一个 ACK 报文作为应答，且把服务端的序列号值 +1 作为自己 ACK 报文的序列号值，此时客户端处于 `TIME_WAIT` 状态。

## 重绘和回流

- **重绘是** 当节点需要**更改外观而不会影响布局**的，比如改变 color、background-color、visibility等就叫称为重绘
- **回流是 布局或者几何属性需要改变** 就称为回流。

**回流必定会发生重绘，重绘不一定会引发回流。回流所需的成本比重绘高的多，改变深层次的节点很可能导致父节点的一系列回流。**

导致回流的操作：

- 页面首次渲染
- 浏览器窗口大小发生改变
- 元素尺寸或位置发生改变
- 元素内容变化（文字数量或图片大小等等）
- 元素字体大小变化
- 添加或者删除**可见**的`DOM`元素
- 激活`CSS`伪类（例如：`:hover`）
- 查询某些属性或调用某些方法

