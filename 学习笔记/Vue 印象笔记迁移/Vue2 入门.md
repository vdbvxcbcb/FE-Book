## 认识 Vue.js

<img src="https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1652709416902-b98ab374-31e6-48e1-a9c3-1c3935a65458.jpeg" alt="img" style="zoom: 67%;" />

<img src="https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1652709404772-4b9d00ce-a353-430b-9da1-be9e0040b849.jpeg" alt="img" style="zoom:67%;" />

<img src="https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1652709404844-ca679e84-ee2c-425a-9fe3-7f3f13df56f5.jpeg" alt="img" style="zoom:67%;" />

<img src="https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1652709404843-2a2ccb5e-dac3-45aa-a18d-8a381bc0dadb.jpeg" alt="img" style="zoom:67%;" />

<img src="https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1652709404800-04a8710b-ec99-4020-8844-81687cb40d17.jpeg" alt="img" style="zoom:67%;" />

为 Angular 工作之后，Vue 的作者尤雨溪开发出了 Vue 框架。

他声称自己的思路是提取 Angular 中为自己所喜欢的部分，构建出一款相当轻量的框架。

Vue 最早发布于 2014 年 2 月，是三大框架中最晚发布的。

Vue 是一套用于构建用户界面的渐进式（由浅入深、由简单到复杂）框架

### 库

库本质上是一些函数的集合。

每次调用函数，实现一个特定的功能，接着把控制权交给使用者。

库分为两类：一类是方法库（jQuery），一类是 UI 组件库（Element UI）

Bootstrap 是一个 Toolkit (工具箱)，它的组件原生是用 jQuery 写的，不适用于现在主流的前端

● 代表：jQuery

● jQuery 这个库的核心是：DOM 操作，即：封装 DOM 操作，简化 DOM 操作

### 框架

框架也是一系列预先定义好的数据结构和函数，使用框架的时候，需要把你的代码放到框架合适的地方，框架会在合适的时机调用你的代码。

好比一个架子，可以放书，可以放花，可以放 DVD，但是不能在上面睡觉；预先规定好在什么地方做什么事情

● 框架规定了自己的编程方式，是一套完整的解决方案

● 使用框架的时候，由框架控制一切，我们只需要按照规则写代码

框架和库的最大区别在于“控制反转”，当你使用一个库，你会调用库中的代码，而当你使用一个框架，框架会调用你的代码。

### 框架的好处

● 组件化：其中以 React 的组件化最为彻底，甚至可以到函数级别的原子组件 ，高度的组件化可以是我们的工程易于维护、易于组合拓展。

● 天然分层：jQuery 时代的代码大部分情况下是面条代码 ，耦合严重 ，现代框架不管是 MVC、MVP 还是 MVVM 模式都能帮助我们进行分层，代码解耦更易于读写。

● 生态：现在主流前端框架都自带生态 ，不管是数据流管理架构还是 UI 库都有成熟的解决方案

所谓的前端三大框架，只有 Angular 算作严格意义上的 Framework，而且是一个 Opinionated Framework，其他两个不算

Vue 和 React 都是关注于 View 这一层的 Library

也正因如此，用 React 或 Vue 搭建项目就还需要一系列其他周边库做补充支持。

例如 React 本身没有实现路由导航，就需要 react-router 等等，还有很多，俗称全家桶。

### MVC、MVP、MVVM是什么

UI 层的设计模式

#### MVC


● M: Model 数据模型（专门用来操作数据，数据的CRUD）

● V：View 视图（对于前端来说，就是页面）

● C：Controller 控制器（是视图和数据模型沟通的桥梁，用于处理业务逻辑）

**MVC的一般流程是这样的：**

View（界面）触发事件 --> Controller（业务）处理了业务，然后触发了数据更新 --> 不知道谁更新了 Model 的数据 --> Model（带着数据）回到了 View --> View 更新数据

**MVP设计模式与MVVM设计模式**

MVP 设计模式核心是 P（原控制器）层，大部分代码都是在这操作 DOM 。

MVP 设计模式切断了 View 和 Model 的联系，让 View 只和 Presenter（原 Controller）交互，减少在需求变化中需要维护的对象的数量

### jQuery 实现 TodoList

```js
// V 层（视图层）
<div id="app">
    <input id="input" type="text">
    <button id="btn">提交</button>
    <ul id="list"></ul>
</div>


// P 层（控制器）
function Page () {
}

$.extend(Page.prototype, {
    init: function () {
        this.bindEvents();
    }, 
    bindEvents: function () {
        var btn = $('#btn');
        btn.on('click', $.proxy(this.handleBtnClick, this)); // 让 this.handleBtnClick 的 this 指向 btn，而不是 page
    },
    handleBtnClick: function () {
        var inputElem = $("#input"); // 提高性能的写法，不用每写一次就找一次DOM
        var inputValue = inputElem.val();
        var ulElem = $("#list");
        uLElem.append('<li>' + inputValue + '</li>');
        inputElem.val('');
    }
});

var page = new page();
page.init();

// M 模型层 

// 控制器可以发送 AJAX 请求 
```

<img src="https://cdn.nlark.com/yuque/0/2022/gif/1614731/1652709701583-38fa7624-37fc-441f-8c00-5c6975370a29.gif" alt="img" style="zoom:67%;" />

### MVVM 组成

● MVVM ===> M / V / VM

● M：Model 数据模型

● V：View 视图

● VM：ViewModel 视图模型

### MVVM 设计模式

MVVM 设计模式核心是 M 层，数据是核心。

没有控制器，取而代之的是 VM 层，VM 层是 Vue 自带的，不需要自己编写，只需要关注 V 层和 M 层。

Vue 虽然没有完全遵循 MVVM 模型，但是在文档中经常会使用 vm ( ViewModel 的缩写) 这个变量名表示 Vue 实例。

为什么官方要说 Vue 没有完全遵循 MVVM 思想呢？

严格的 MVVM 要求 View 不能和 Model 直接通信，

而 Vue 提供了$refs 这个属性，让 Model 可以直接操作 View，违反了这一规定，

所以说 Vue 没有完全遵循 MVVM。

MVVM 设计模式，用数据“绑定”的形式让数据更新的事件不需要开发人员手动去编写，

而是自动地做到数据（M层）和视图（V层）的同步。

这大大简化了对 DOM 的操作，显著减少代码量（30%~70%）。

<img src="https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1652709809456-30055066-4150-431d-b20f-7300ddc679b1.jpeg" alt="img" style="zoom:67%;" />

<img src="https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1652709809471-8e4c2b46-6674-48e4-95af-c0f7bc24e30f.jpeg" alt="img" style="zoom:67%;" />

![img](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1652709809488-3fb4ec80-c6d2-4d3f-bc6a-dae000c51cf4.jpeg)

<img src="https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1652709809484-cefd4498-39a2-41a8-b88a-e8b363c8beaa.jpeg" alt="img" style="zoom:67%;" />

## Vue 的 MVVM

<img src="https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1652709923696-486cd7b6-21c0-493b-81c3-b23256cb7d90.jpeg" alt="img" style="zoom:67%;" />

<img src="https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1652709923742-2a0d75bd-da7d-4c2a-8854-fd26d10902d0.jpeg" alt="img" style="zoom: 80%;" />

<img src="https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1652709923740-ec03db45-2383-484b-938e-b4f858a89301.jpeg" alt="img" style="zoom:67%;" />

```js
// V层（视图层）
<div id="app">
    <input type="text" v-model="inputVal">
    <button v-on:click="btnClick">提交</button>
    <ul>
        <li v-for="item of list" :key="item">{{item}}</li>
    </ul>
</div>


// Vue 实例 就是 VM 层，监听 V 层(视图层) Dom 和 M 层(模型层)数据 的变化
var app = new Vue({
    el: '#app',
    // M 层（模型层）
    data: {
        inputVal: '',
        list: []
    },
    methods: {
        btnClick: function () {
            this.list.push(this.inputVal);
            this.inputVal = ''
        }
    }
})
```

<img src="https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1652710007906-5f32dc29-56aa-44e3-aff8-586ae70493aa.jpeg" alt="img" style="zoom: 80%;" />

## 学习 Vue 要转化思想

命令式编程（告诉浏览器每一步怎么做） => 声明式编程（告诉浏览器你要显示什么） 

<img src="https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1652710049967-9ec87bb6-8a47-4a08-a0f8-04d71b3282ab.jpeg" alt="img" style="zoom: 80%;" />

数据响应式：数据修改后页面自动更新

不要在想着怎么操作 DOM，而是想着如何操作数据！！！

现在是 数据驱动 + 组件化 的前端开发

<img src="https://cdn.nlark.com/yuque/0/2022/png/1614731/1652710136218-df234041-845c-49f9-8610-b702ea3276f8.png" alt="img" style="zoom: 67%;" />

<img src="https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1652710136313-e1e68b84-66bd-4d01-803c-9bc06db66d37.jpeg" alt="img" style="zoom:67%;" />

<img src="https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1652710136208-b3972f31-1461-4993-b294-9897bba52aff.jpeg" alt="img" style="zoom:67%;" />

<img src="https://cdn.nlark.com/yuque/0/2022/png/1614731/1652710136244-7075d55d-dd34-43f9-aacf-a7f57735f24d.png" alt="img" style="zoom:67%;" />

<img src="https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1652710136201-eb50b296-7fa6-4df9-a3fa-c9a7b20bb825.jpeg" alt="img" style="zoom:67%;" />

## 挂载点、模板与实例的关系

**Vue.js 的核心是一个允许采用简洁的 模板语法 来 声明式 地将数据渲染进 DOM 的系统：**

命令式渲染通常意味着手动操作 DOM

**声明式渲染实现的是，DOM 随状态（数据）更新而更新**

重绘是一项十分耗费性能的操作

**自行用代码操作 DOM，常常频繁地触发重绘，**

**如果把更新 DOM 的操作交给 Vue、React 去做，让虚拟 DOM 发挥作用，可有效提高页面性能**

**虚拟 DOM**

是一种预先通过 JS 把 DOM 操作计算出来并进行优化的技术，

这个操作属于预处理操作，并没有真实地操作 DOM ，大幅度提高 DOM 操作渲染效率。

虚拟 DOM 使用虚拟的数据结构表示 DOM 结构，是为了解决浏览器性能问题才被设计出来的。

**好处：DOM 一旦改变，不需要全部重新渲染。**

例如，

一次操作中有 10 次更新 DOM 的动作，虚拟 DOM 不会立即操作 DOM，而是将这 10 次更新的 diff 内容保存到本地一个 JS 对象中，

最终将这个 JS 对象一次性 attach 到 DOM 树上，再进行后续操作，避免大量无谓的计算量，

页面的更新可以先全部反映在 JS 对象(虚拟 DOM )上，操作内存中的 JS 对象的速度显然要更快，

等更新完成后，再将最终的 JS 对象映射成真实的 DOM，交由浏览器去绘制。

```js
<div id="app"></div>

// 创建一个 Vue 实例 ，可以传入一个 option 选项对象，接收一些配置项作为参数
 
// 挂载点：el 指定 dom 元素，是 Vue实例 负责接管的 dom 元素区域，通常我们也把它叫做边界 数据只在边界内部解析，原理是使用 `document.querySelector()`

// 模板：dom 元素下的内容，如果不定义 template 就会把挂载点下所有的内容作为模板。

// Vue 实例 发现 template 内容有 插值表达式 {{content}} 就会去 data 寻找 对应属性名 的 属性值 替换掉 {{content}}

// 实例：Vue 自动结合模板和数据生成展示内容，放到挂载点中。

var app = new Vue({
    el: '#app',
    template: '<h1>hello{{content}}{{num}}</h1>',
    data: {
        content: ' word!',
        num: '123'
    }
}); 
   
setTimeout(function () {
    app.$data.content = ' china!';
}, 2000)

// 面向 dom 编程 => 面向数据(vm)编程
```

![img](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1652710300309-07378f02-d7b6-4a92-8f28-102f99188aa8.jpeg)

![img](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1652710300334-f808df6a-c76d-4856-9a80-7019b3326008.jpeg)

案例：计数器

```vue
<div id="app">
    <div>当前计数是：{{counter}}</div>
    <button @click="add">+</button>
    <button @click="sub">-</button>       
</div>

<script src="vue.js"></script>  
<script>
    // Model层 语法糖：通过 proxy 把 obj 所有的数据放进 new vue() 的 data 里 （简写）
    let obj = {
        counter: 0
    };
    const app = new Vue({
      el: '#app',
      data: obj,
      methods: {
        add() {
            this.counter++
        },
        sub() {
            this.counter--
        }
      }
   })   
</script>
```

![img](https://cdn.nlark.com/yuque/0/2022/gif/1614731/1652710431801-3c51f211-65fd-4953-80ae-d4b0ce642c29.gif)

## Vue 实例中的数据

data：Vue 实例的数据对象。Vue 会使用递归将 data 的属性转换为 getter / setter，从而让 data 的属性能够响应数据变化。（是后面所有 Vue 特性的基石）

vm：当一个 Vue 实例被创建时，它将 data 对象中的所有的属性加入到 Vue 的响应式系统中。当这些属性的值发生改变时，视图将会产生“响应”，即匹配更新为新的值。

Vue 允许开发者声明式地将 DOM 绑定至底层 Vue 实例的数据。

在底层的实现上，Vue 将 模板 编译成 虚拟 DOM 渲染函数。

```js
var vm = new Vue({
  data: {
    msg: '大家好，...'
  }
})

vm.$data.msg === vm.msg // true
```

**● 注意 1：先在 data 中声明数据，再使用数据**

**● 注意 2：通过 vm.$data 可以访问到 data 的所有属性，vm.msg 可以直接访问 data 的 msg 属性**

Vue 的实例属性与方法都有前缀 $，以便与用户定义的特性区分开来

```js
var data = { a: 1 }
var vm = new Vue({ 
  el: '#example', 
  data: data 
})

vm.$data === data // => true
vm.$el === document.getElementById('example') // => true

// $watch 是一个实例方法 
vm.$watch('a', function (newValue, oldValue) { 
    // 这个回调将在 `vm.a` 改变后调用 
})
```

创建 Vue 时，options（可选参数）可以放

● el

● template

● data

● methods

这些都是 Vue 实例属性

### 模板语法/ 数据绑定（DOM 中显示文本内容）

#### 插值（Mustache 语法）内容绑定

**● 最常用的方式：Mustache(插值语法)，也就是 {{}} （小胡子语法）**

**● 解释：{{}} 只能放在标签内容里，表示从数据对象 data 的属性中直接获取数据**

**● 说明：数据对象的属性值发生了改变，插值处的内容都会更新**

```vue
<h1>Hello, {{ msg }}.</h1>

<p>{{ 1 + 2 }}</p>

<p>{{ isOk ? 'yes': 'no' }}</p>
```

下面的例子都不会生效：

**● 注意：Mustache 语法不能作用在 HTML 元素的特性上，这种情况应该使用 v-bind 指令**

**● 注意：{{}}中只能解析 JS 表达式，而不能解析 JS 语句**

```vue
<!-- ！！！错误示范！！！ -->
<h1 title="{{ err }}"></h1>

<!-- 这是语句，不是表达式 -->
{{ var a = 1 }}

<!-- 流程控制语句也不会生效，请使用三元表达式 -->
{{ if (ok) { return message } }}
```

## 指令

指令 (Directives) 是带有 v- 前缀的特殊 attribute 特性。

作用：当表达式的值改变时，将其产生的连带影响，响应式地作用于 DOM

### Vue 的内置指令

<img src="https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1652710873904-11f371a1-f00d-40dc-afef-d5c2649d4b88.jpeg" alt="img" style="zoom: 67%;" />

#### v-text 与 v-html指令

v-text 指令（代替插值表达式）意思是告诉 h1：

h1 的值，即要显示的文本内容在 data 对象的同名属性里！

```vue
<div id="app">
    <h1 v-text="num"></h1>
</div>

var app = new Vue({
    el: '#app',
    data: {
        num: '123'
    }
});  
```

![img](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1652710959957-505b14ea-4c12-434e-8c8a-7ac1ac565570.jpeg)

#### v-text 与 v-html 的区别

v-text 指令显示所有文本内容

```vue
<div id="app">
    <div v-text="content"></div>
</div>

var app = new Vue({
    el: '#app',
    data: {
        content: '<h1>hello world</h1>'
    }
});
```

![img](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1652711034855-b474e5ff-d508-425f-818b-55033d1cbc5f.jpeg)

```vue
<div id="app">
    <div v-text="content">123</div>
</div>

var app = new Vue({
    el: '#app',
    data: {
        content: '<h1>hello world</h1>'
    }
});
```

![img](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1652711034855-b474e5ff-d508-425f-818b-55033d1cbc5f.jpeg)

v-html 指令解析文本内容的 HTML 标签

```vue
<div id="app">
    <div v-html="content"></div>
</div>

var app = new Vue({
    el: '#app',
    data: {
        content: '<h1>hello world</h1>'
    }
});
```

![img](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1652711155094-51bf8c80-f56b-4821-8a45-bc2c797658a7.jpeg)

**注意：插值表达式、v-text 与 v-html 都是模板语法，v-text 与 v-html 指令的内容都可以使用 JS 表达式**

```html
<div>{{content + 'ok'}}</div>

<div v-text="content + 'ok'"></div>

<div v-html="content + 'ok'"></div>
```

#### v-once 指令

![img](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1652711219668-0791fddd-48fe-44c7-a0ba-98f70d72ec6f.jpeg)

属性绑定、样式绑定与双向数据绑定

属性绑定应用场景：

比如轮播图的 src 不是写死的，需要接收一个服务器返回的 json 数组的 url 来显示图片

<img src="https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1652711257377-aac0a840-6e5b-492f-9595-c14b81a412e3.jpeg" alt="img" style="zoom: 80%;" />

### 属性绑定（v-bind:title="title"）

**属性绑定是单向绑定，数据改变、视图才跟着变**

**字面意思是属性绑定，**

**其实指的是一个组件的 data 属性与 html 特性绑定（也可以是自定义 html 特性，自定义 html 特性用于组件传值），**

**html 特性 才会有 data 属性 的值，**

**也就是将 data 对象的属性 绑定到=> 某个 html 特性 / 自定义 html 特性上**

**加 : 冒号前缀，属性值仍使用引号包裹，但引号里不是字符串，而是 js 变量！**

数据：data 对象中的所有的属性会加入到 Vue 的响应式系统，当这些属性的值发生改变时，视图将会产生“响应”，即更新为新的值

```vue
<div v-bind:title="title">hello world</div>

var app = new Vue({
  el: '#app',
  data: {
    title: 'this is hello world',
  }
});
```

v-bind:title="title"  的意思是：这个元素节点的 title 特性（v-bind:title） 和 Vue 实例 data 的 title 属性（"title" ）保持一致，

特性的值与属性的值一样，它的值在 data 对象中的同名属性找！

**如果绑定的属性值是 `null` 或者 `undefined`，那么该 attribute 将会从渲染的元素上移除。**

输入 app.title = '新标题'，改变数据，就会看到这个绑定了 title 特性的 元素节点 已经进行了更新。

**注意：**

**1、v-bind:title 的 v-bind 可以简写成 : ，即 v-bind:title === :title。**

**2、v-bind:title="title" 表示传递 data 的 title 属性 给 title 特性，没有冒号 : 的 title="title" 表示传递一个字符串 title 给 title 特性！**

**3、v-bind: 使用场景分别是绑定 data 的属性，绑定 class 或 style，绑定 prop （当 attribute 是自定义时）**

#### 静态 prop vs 动态 Prop

**组件能够接收任意类型的 JavaScript 值作为 props，甚至是 html 字符串**

静态 prop 

**很多像这样的静态值（字符串）形式的 props (字符串绑定 props)：**

template

```html
<BlogPost title="My journey with Vue" />
```

动态 Prop

**相应地，还有使用 `v-bind` 或缩写 `:` 来进行动态绑定的 props ( JavaScript 表达式绑定 props)：**

template

```html
<!-- 根据一个变量的值动态传入 -->
<BlogPost :title="post.title" />

<!-- 根据一个更复杂表达式的值动态传入 -->
<BlogPost :title="post.title + ' by ' + post.author.name" />
```

#### 布尔型 Attribute

`v-bind` 在这种场景下的行为略有不同：

```vue
<button :disabled="isButtonDisabled">Button</button>
```

当 `isButtonDisabled` 为[真值](https://developer.mozilla.org/en-US/docs/Glossary/Truthy)或一个空字符串 (即 `<button disabled="">`) 时，元素会包含这个 `disabled` attribute。

而当其为[假值](https://developer.mozilla.org/en-US/docs/Glossary/Falsy)时 attribute 将被忽略。

#### 动态绑定多个值

如果有像这样的一个包含多个 attribute 的 JavaScript 对象：

```js
const objectOfAttrs = {
  id: 'container',
  class: 'wrapper'
}
```

通过不带参数的 `v-bind`，可以将它们绑定到单个元素上：

```vue
<div v-bind="objectOfAttrs"></div>
```

#### 一个对象绑定多个 prop

如果想要将一个对象的所有属性都当作 props 传入，

可以使用[没有参数的 `v-bind`](https://cn.vuejs.org/guide/essentials/template-syntax.html#dynamically-binding-multiple-attributes)，即只使用 `v-bind` 而非 `:prop-name`。

例如，这里有一个 `post` 对象：

```js
const post = {
  id: 1,
  title: 'My Journey with Vue'
}
```

以及下面的模板：

template

```html
<BlogPost v-bind="post" />
```

这实际上等价于：

template

```html
<BlogPost :id="post.id" :title="post.title" />
```

#### 使用 JavaScript 表达式

Vue 在所有的数据绑定中都支持完整的 JavaScript 表达式：

template

```vue
{{ number + 1 }}

{{ ok ? 'YES' : 'NO' }}

{{ message.split('').reverse().join('') }}

<div :id="`list-${id}`"></div>
```

这些表达式都会被作为 JavaScript ，以组件为作用域解析执行。

在 Vue 模板内，JavaScript 表达式可以在如下场景使用：

- 在文本插值中 (双大括号 `{{ }}`)
- 在任何 Vue 指令 (以 `v-` 开头的特殊 attribute) attribute 的值中

#### 调用函数

可以在绑定的表达式中使用一个组件暴露的方法：

template

```vue
<span :title="toTitleDate(date)">
  {{ formatDate(date) }}
</span>
```

#### 动态参数

可以用方括号括起来的 JavaScript 表达式作为一个指令的参数

template

```vue
<!--
注意，参数表达式有一些约束，
参见下面“动态参数值的限制”与“动态参数语法的限制”章节的解释
-->
<a v-bind:[attributeName]="url"> ... </a>

<!-- 简写 -->
<a :[attributeName]="url"> ... </a>
```

这里的 `attributeName` 会作为一个 JavaScript 表达式进行动态求值，计算得到的值会被用作最终的参数。

举例来说，如果你的组件实例有一个数据属性 `attributeName`，其值为 `"href"`，那么这个绑定就等价于 `v-bind:href`。

还可以将一个函数绑定到动态的事件名称上：

template

```vue
<a v-on:[eventName]="doSomething"> ... </a>

<!-- 简写 -->
<a @[eventName]="doSomething">
```

在此示例中，当 `eventName` 的值是 `"focus"` 时，`v-on:[eventName]` 就等价于 `v-on:focus`。

#### 动态参数值的限制

动态参数中表达式的值应当是一个字符串，或者是 `null`。

特殊值 `null` 意为显式移除该绑定。

其它非字符串的值会触发警告。

#### 动态参数语法的限制

动态参数表达式因为某些字符的缘故有一些语法限制，

比如空格和引号，在 HTML attribute 名称中都是不合法的。

例如下面的示例：

template

```vue
<!-- 这会触发一个编译器警告 -->
<a :['foo' + bar]="value"> ... </a>
```

**如果需要传入一个复杂的动态参数，推荐使用计算属性替换复杂的表达式**

### 样式绑定

我们可以用 v-bind 处理样式绑定

其实就是 data 对象的属性绑定到 class / style 特性上。

● class 绑定时表达式返回的结果可以是：字符串、对象、数组，并且数组中可包含字符串与对象类型。

● style 绑定时表达式返回的结果是：对象、数组，数组中包含的是对象。

![img](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1652711378809-713cfff9-bce1-4dd1-9ac5-32271e6d8453.jpeg)

#### class 的对象绑定（推荐）

```js
:class="{ 类名: 布尔值 }, { 类名: 布尔值 }" // 类名加不加单引号 '' 都行
```

```js
:class="{ activated: isActivated }"
```

名为 activated 的 class 存在与否取决于 data属性 isActive 的布尔值

```js
:class="hello ? 'apple' : 'banana'"
```

根据 data属性 hello 选择类名是 apple 还是 banana

```js
:class = "{ isActive: currentIndex === index }"
```

名为 isActive 的 class 存在与否取决于 data 属性 currentIndex 是否与 index 相同

```vue
<style type="text/css">
    .activated {
        color: red;
    }
</style>

<div id="app">  
    <div :class="{activated: isActivated}" @click="handleDivClick">Hello World</div>
</div>

var app = new Vue({
    el: '#app',
    data: {
        isActivated: false
    },
    methods: {
        handleDivClick: function () {
            this.isActivated = !this.isActivated;
        }
    }
});
```

也可以写成：

```vue
<style type="text/css">
    .activated {
        color: red;
    }
</style>

<div id="app">  
    <div :class="classObject" @click="handleDivClick">Hello World</div>
</div>

var app = new Vue({
    el: '#app',
    data: {
        classObject: {
            activated: false
        }    
    },
    methods: {
        handleDivClick: function () {
            this.classObject.activated = !this.classObject.activated;
        }
    }
});
```

![img](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1652711794783-4fb62919-f43f-4bde-b4ce-693818ea566c.jpeg)

点击之后：

![img](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1652711821500-abf4f9fc-7cda-4e78-8491-727424bad959.jpeg)

![img](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1652711804157-5869439f-f313-4680-af06-0c1f28ae1830.jpeg)

v-bind:class 指令也可以与普通的 class 特性（这里指的是名为 static 的class）共存

当有如下模板：

```vue
<div>
  class="static"
  v-bind:class="{ active: isActive, 'text-danger': hasError }"
></div>
```

和如下 data：

```vue
data: { isActive: true, hasError: false }
```

结果会渲染为：

```html
<div class="static active"></div>
```

我们也可以在这里绑定一个返回对象的[计算属性](https://v2.cn.vuejs.org/v2/guide/computed.html)。这是一个常用且强大的模式：

```html
<div v-bind:class="classObject"></div>
```

```js
data: {
  isActive: true,
  error: null
},
computed: {
  classObject: function () {
    return {
      active: this.isActive && !this.error,
      'text-danger': this.error && this.error.type === 'fatal'
    }
  }
}
```

#### class 的数组绑定（较少使用）

```vue
:class="[data的变量, data的变量]"
```

```vue
<style type="text/css">
    .activated {
        color: red;
    }
</style>

<div id="app">  
    <div :class="[activated, otherColor]" @click="handleDivClick">Hello World</div>
</div>

var app = new Vue({
    el: '#app',
    data: {
        activated: '',
        otherColor: 'otherColor'
    },
    methods: {
        handleDivClick: function () {
            this.activated = this.activated === 'activated' ? '' : 'activated';
        }
    }
});
```

![img](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1652711982386-d76c6c7b-3496-4379-8bed-de3c45f5ee04.jpeg)

![img](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1652711982399-0753d6b0-128f-4eb3-be34-588154f687b3.jpeg)

点击之后：

![img](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1652711995537-f2e0cdc2-3d4a-447c-be29-1f33d9beb9fd.jpeg)

![img](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1652711995537-553491b5-cda5-4da0-8aa5-33785b174ec9.jpeg)

在数组语法中也可以使用对象语法：

```html
<div v-bind:class="[{ active: isActive }, errorClass]"></div>
```

#### style 的对象绑定（推荐）

v-bind:style 的对象语法十分直观 —— 看着非常像 CSS，但其实是一个 JavaScript 对象。

```js
:style="{属性名: '属性值', 属性名: '属性值'}"
```

表示对这个DOM元素使用样式：{属性名: '属性值', 属性名: '属性值'}

![img](https://cdn.nlark.com/yuque/0/2022/png/1614731/1652712295312-2e45b546-b60b-4868-b62e-d3b522e1c1da.png)

简化写法：

![img](https://cdn.nlark.com/yuque/0/2022/png/1614731/1652712303127-47b7c9f2-57e2-413d-8d92-e82921aca44b.png)

![img](https://cdn.nlark.com/yuque/0/2022/png/1614731/1652712303130-cffd2aaa-3b98-4a19-bdbc-e96b2d0f9e63.png)

直接绑定一个样式对象可以使模板更加简洁：

```js
:style="对象变量"
```

```vue
<div id="app">  
    <div :style="styleObj" @click="handleDivClick">Hello World</div>
</div>

var app = new Vue({
    el: '#app',
    data: {
        styleObj: {
            color: 'black'
        }
    },
    methods: {
        handleDivClick: function () {
            this.styleObj.color = this.styleObj.color === 'black' ? 'red' : 'black';
        }
    }
}); 
```

![img](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1652712363533-4f9f2cbf-9646-4f0b-a2bd-30e0af7a36ea.jpeg)

![img](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1652712363544-6166bbd3-79c5-42f9-a54a-147ca63b6376.jpeg)

点击之后：

![img](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1652712372404-b499569f-c42f-473b-a61e-f3d55301adc3.jpeg)

![img](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1652712372414-a86ad360-da0b-49b3-8cd4-82f52740ef18.jpeg)

#### style 的数组绑定（较少使用）

CSS 属性名要用驼峰式 (camelCase)  来命名

数组语法可以将多个拥有样式的对象应用到同一个元素：

```vue
<div id="app">  
    <div :style="[styleObj, {fontSize: '20px'}]" @click="handleDivClick">Hello World</div>
</div>

var app = new Vue({
    el: '#app',
    data: {
        styleObj: {
            color: 'black'
        }
    },
    methods: {
        handleDivClick: function () {
            this.styleObj.color = this.styleObj.color === 'black' ? 'red' : 'black';
        }
    }
});
```

![img](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1652712444816-54c62e86-5e69-47e1-99c2-6b2bc3c2e251.jpeg)

#### 组件上使用 class

**在一个自定义组件上使用 class property 时，这些 class 将被添加到该组件的根元素上面，**

**并与该元素上已有的 class 合并，该元素上已经存在的 class 不会被覆盖。**

举例来说，如果你声明了一个组件名叫 `MyComponent`，模板如下：

template

```html
<!-- 子组件模板 -->
<p class="foo bar">Hi!</p>
```

在使用时添加一些 class：

template

```html
<!-- 在使用组件时 -->
<MyComponent class="baz boo" />
```

渲染出的 HTML 为：

template

```html
<p class="foo bar baz boo">Hi</p>
```

Class 的绑定也是同样的：

template

```html
<MyComponent :class="{ active: isActive }" />
```

当 `isActive` 为真时，被渲染的 HTML 会是：

template

```html
<p class="foo bar active">Hi</p>
```

**如果你的组件有多个根元素，你将需要指定哪个根元素来接收这个 class。你可以通过组件的 `$attrs` 属性来实现指定：**

template

```html
<!-- MyComponent 模板使用 $attrs 时 -->
<p :class="$attrs.class">Hi!</p>
<span>This is a child component</span>
```

template

```html
<MyComponent class="baz" />
```

这将被渲染为：

html

```html
<p class="baz">Hi!</p>
<span>This is a child component</span>
```

### 表单绑定（双向数据绑定）（v-model="content"）

v-model 可以取 data 的属性 或 computed 的属性 作为初始值。

双向数据绑定指的是数据的改变会引起 DOM 的改变，DOM 的改变也会引起数据的改变。

也就是说，从 data 中取值后，DOM 更新也会相应地更新 data 。

看起来是 data 对象的属性 绑定到 v-model，

其实就是 data 对象的属性 绑定到 某个表单元素的 value / checked / selected 特性 和 某个 input / change 事件上，

data 对象的属性 提供初始值给 表单的 value 特性，

而表单的 value 特性发生变化，触发事件，先改变 data 对象的属性，data 对象的属性 再改变 表单的 value 特性 。

总而言之：

v-model 里的 data 与 绑定到 某个表单元素的 value / checked / selected 特性强绑定，

特有事件将表单的值传给 v-model 里对应的 data，触发 data 的响应式更新

#### v-model 原理

<img src="https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1652712479386-494f5474-cc11-4beb-8d69-01a7fb3807b2.jpeg" alt="img" style="zoom:80%;" />

<img src="https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1652712479406-7345bbdf-610d-40f0-8635-d685db00a5a0.jpeg" alt="img" style="zoom:80%;" />

message 同时绑在 value 特性（v-bind:value）和 input 事件（v-on:input）上

<img src="https://cdn.nlark.com/yuque/0/2022/png/1614731/1652712510466-b4a82de1-ee07-4452-a037-698303e214c1.png" alt="img" style="zoom:80%;" />

<img src="https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1652712510380-88100193-103a-42da-ace1-2eef7fb4a9a1.jpeg" alt="img" style="zoom:67%;" />

v-model 指令在表单 `<input>`、`<textarea>` 及 `<select> `元素上可以轻松地实现双向数据绑定。

v-model 在内部会根据所使用的元素自动使用对应的 DOM 属性和事件组合：

- 文本类型的 `<input>` 和 `<textarea>` 元素会绑定 `value` property 并侦听 `input` 事件；
- `<input type="checkbox">` 和 `<input type="radio">` 会绑定 `checked` property 并侦听 `change` 事件；
- `<select>` 会绑定 `value` property 并侦听 `change` 事件。

```vue
<div id="app">
    <input v-model="content">
    <div>{{content}}</div>
</div>

var app = new Vue({
     el: '#app',
     data: {
         content: 'this is hello world'
     }
});
```

![img](https://cdn.nlark.com/yuque/0/2022/gif/1614731/1652712580919-9f4aec93-b794-4771-95ba-4de4d859548e.gif)

#### v-model 结合 radio 类型

```vue
<div id="app">
  <!-- label的 for 特性值与 id 特性值同名，可以点击文字来选择  -->
  <label for="male">
    <!-- 当 v-model 绑定的是 sex 时，input 的 name 特性其实不需要了（input 的 name 特性起互斥作用，作为一个 key 发送到服务器，value 特性作为值） -->
    <input type="radio" id="male" name="sex" v-model="sex" value="男"/>男
  </label>
  <label for="female">
    <input type="radio" id="female" name="sex" v-model="sex" value="女"/>女
  </label>
  <h2>您选择的性别是：{{sex}}</h2>
</div>

const app = new Vue({
  el: '#app',
  data: {
    sex: '男' // 设置默认初始值
  }
})
```

![img](https://cdn.nlark.com/yuque/0/2022/gif/1614731/1652712621069-bf7ff5fb-5330-4ce0-9c7d-7b1aa02fb22c.gif)

**checkbox 为单选框时**

```vue
<div id="app">
  <label for="license">
    <input type="checkbox" id="license" v-model="isAgree">同意协议   
  </label>
  <h2>您选择的是：{{show}}</h2>
  <button :disabled="!isAgree">下一步</button>
</div>

const app = new Vue({
    el: '#app',
    data: {
      isAgree: false
    },
    computed: {
      show() {
        let show;
        if (this.isAgree === true) {
          return show = '同意'  
        }
        else {
          return show = '不同意'
        }
      }
    }
})
```

![img](https://cdn.nlark.com/yuque/0/2022/gif/1614731/1652712709677-b2189a38-0ab0-4eeb-a11c-ee3721e82eaf.gif)

**checkbox 为多选框时**

```vue
<div id="app">
  <label for="hobbies">
    <input type="checkbox" id="hobbies" v-model="hobbies" value="篮球">篮球  
    <input type="checkbox" id="hobbies" v-model="hobbies" value="足球">足球
    <input type="checkbox" id="hobbies" v-model="hobbies" value="乒乓球">乒乓球
  </label>
  <h2>您选择的是:{{hobbies}}</h2>
</div>

const app = new Vue({
    el: '#app',
    data: {
      hobbies: []
    }
})
```

值绑定（v-bind 在 input 上的应用）的写法：

```js
data 的 optionalHobby -> item -> value 的 item-> v-model 的 hobby -> data 的 hobby -> {{ hobby }}
```

```vue
<div id="app">
  <label v-for="item in optionalHobby" :for="item">
    <input type="checkbox" :id="item" :value="item" v-model="hobby">{{ item }}
  </label>
  <h2>您选择的是:{{ hobby }}</h2>
</div>

const app = new Vue({
  el: '#app',
  data: {
    hobby: [],
    optionalHobby: ['篮球', '足球', '乒乓球']
  }
})
```

![img](https://cdn.nlark.com/yuque/0/2022/gif/1614731/1652712796119-67ec4da8-6a28-46ce-a467-06bda9221484.gif)

#### v-model 结合 select 类型

```vue
<div id="app">
  <select v-model="fruit">
    <option value="苹果">苹果</option>
    <option value="香蕉">香蕉</option>
    <option value="雪梨">雪梨</option>
    <option value="榴莲">榴莲</option>
    <option value="橙子">橙子</option>
  </select>
  <h2>您选择的是：{{fruit}}</h2>
</div>

const app = new Vue({
    el: '#app',
    data: {
      fruit: '香蕉'
    }
})
```

![img](https://cdn.nlark.com/yuque/0/2022/gif/1614731/1652712855449-b2070bc2-41e3-456e-9572-af7a2df00788.gif)

#### v-model 的修饰符

<img src="https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1652712923024-3281153c-3cde-40d4-a0a6-b063fb5448e0.jpeg" alt="img" style="zoom: 67%;" />

注意：

v-model 会忽略所有表单元素的 value、checked、selected 的 attribute 特性 的初始值，而总是将 Vue 实例的数据作为数据来源。

应该通过 JavaScript 在组件的 data 选项中声明初始值（一般为空字符或空数组）作为数据来源。

### Vue 中的 .sync 修饰符（:money.sync="total"）

`.sync`可以帮我们实现父组件向子组件传递的数据的双向绑定，

子组件接收到数据后可以直接修改，并且会同时修改父组件的数据。

```vue
// Parent.vue
<template>
  <child :page.sync="page"></child>
</template>
<script>
export default {
  data(){
    return {
      page:1
    }
  }
}

// Child.vue
export default {
  props: ["page"],
  computed(){
    currentPage {
      get(){
        return this.page
      },
      // 当我们在子组件里修改它的 currentPage 时，它的父组件 data 的 page 也会随之改变
      set(newVal){
        this.$emit("update:page", newVal)
      }
    }
  }
}
</script>
```

由于无法通过修改子组件的 props 来更改父组件，所以子组件只能通过 emit 通知父组件更新子组件的某个 props 

**.sync 是一个语法糖，为了让子组件通知父组件更新传入自己的某个 prop 的值**

**一个组件可以多个属性用 .sync 修饰符，可以同时双向绑定多个“prop”，不像 v-model 那样，一个组件只能有一个 v-model  。**

子组件

<img src="https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1652757432549-754a1d5e-847c-4f54-b67b-df23fec7c934.jpeg" alt="img" style="zoom:80%;" />

父组件

<img src="https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1652757442234-a372f576-e29e-4574-bd16-ba11ebdaea33.jpeg" alt="img" style="zoom: 80%;" />

**其实 .sync 就是是属性绑定与自定义 update 事件监听结合的简写**

父组件接收一个 update 事件

```vue
<Child v-bind:money="total" v-on:update:money="total = $event" />

或者

<Child :money="total" @update:money="total = $event" />

简写为

<Child :money.sync="total" />
```

子组件触发 update 事件

```html
<button @click="$emit('update:money', money - 100)"> <button>
```

示例1：（父组件.vue）

```vue
<comp :foo.sync="bar"></comp>
```

会被扩展为：

```vue
<comp :foo="bar" @update:foo="val => bar = val"></comp>
```

当子组件需要更新 foo 的值时，子组件需要显式地触发一个更新事件：

```js
this.$emit('update:foo', newValue)
```

示例2：

$emit('update:show', false) => :show.sync='valueChild'

false => 父组件 data 的 valueChild

父组件

```vue
<template>
    <div class="details">
        <myComponent :show.sync='valueChild' style="padding: 30px 20px 30px 5px;border:1px solid #ddd;margin-bottom: 10px;"></myComponent>
        <button @click="changeValue">toggle</button>
    </div>
</template>

<script>
import Child from './Child.vue' 

export default{
   components: {
     Child
   },
   data() {
     return {
       valueChild: true,
     }
   },
   methods: {
     changeValue(){
       this.valueChild = !this.valueChild
     }
   }
}
</script>
```

子组件

```vue
<template>
    <div v-if="show">
      <p>默认初始值是{{show}}，所以是显示的</p>
      <button @click.stop="closeDiv">关闭</button>
   </div>
</template>

<script>
export default{
   props: ['show'],
   methods: {
     closeDiv() {
       this.$emit('update:show', false); // 点击关闭触发事件，并传入新的 show 值为 false
       // 子组件通知父组件更新传入自己的 props 值，即更新父组件 data 的 valueChild 改为 false
     }
   }
}
</script>
```

效果图：

![img](https://cdn.nlark.com/yuque/0/2022/gif/1614731/1652777450913-96c96d12-af73-41d6-a8ec-e3a748427c85.gif)

v-model 针对更多的是最终操作结果，是双向绑定的结果，是 value，是一种 change 操作。

比如：输入框的值、多选框的 value 值列表、树结构最终绑定的 id 值列表（ant design和 element 都是）等等...

**.sync 针对更多的是各种各样的状态，是状态的互相传递，是 status，是一种 update 操作。**

**比如：组件 loading 状态、子菜单和树结构展开列表（状态的一种）、某个表单组件内部验证状态、等等....。**

### 计算属性与侦听器

#### 计算属性（computed）

<img src="https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1652712999329-1ed78f68-e829-457e-835d-f6cf65ca99b8.jpeg" alt="img" style="zoom: 67%;" />

```vue
<div id="example">
    {{ message.split('').reverse().join('') }}
</div>
```

在这个地方，模板不再是简单的声明式逻辑。

你必须看一段时间才能意识到，这里是想要显示变量 message 的翻转字符串。

当你想要在模板中多次引用此处的翻转字符串时，就会更加难以处理。

```vue
<div id="app">
    姓：<input v-model="firstName">
    名：<input v-model="lastName">
    <div>{{ firstName + ' ' + lastName }}</div>
</div>

var app = new Vue({
   el: '#app',
   data: {
     firstName: 'Bruce',
     lastName: 'Wayne',
     fullName: 'Bruce Wayne' // 冗余数据
   }
});
```

在模板中放入太多的逻辑会让模板过重且难以维护。

为了不在模板使用逻辑，让模板只负责显示，同时也为了不让数据冗余，

所以当数据需要经过一系列变化才使用时，就需要使用计算属性。

**计算属性（computed）与方法（methods）语法一样，也是一个 function 函数，区别在于：**

计算属性（computed）的方法名不是动词，而是名词，因为计算属性是属性。

使用时，如：

```js
{{ fullName }}
```

后面不用加括号。


计算属性（computed）：

**● 计算属性**

   **计算属性是使用一个函数对 v-bind / v-model 里的 data 数据或 某个对象的数据进行重新计算得来的另一个属性，**

   **告诉你某个属性的值怎么来的，可以产生新的 data 数据给模板使用，**

   **至于最后将计算的结果（变量的值）像 data 一样传给哪个 指令 和 `{{}}` 则自己具体定。**

   一旦 Computed 没有在模板中使用，vue 是不会为我们计算 Computed 内容的。

   可以将函数定义为只读属性, 也可以定义为可读写属性，即： get / set    

   好处： 从模板中拆分复杂逻辑，由计算属性管理，对于任何复杂逻辑，都应当使用计算属性！

**● 内置缓存**

   **当计算属性任一依赖的数据变化时只会调用一次，而且不会被其他无关数据的修改干扰而重新渲染，性能比方法（methods）更好。**

   **当任一依赖的数据没变的时候不会重新计算，会使用上一次的缓存值来显示。**

   计算属性默认只有 getter，可以在需要的时候自己设定 setter 。

   Computed 里的属性在 getter 执行后是会缓存的，只有当任一依赖的数据改变才会重新调用对应的 getter 计算。

   这意味着 lastName 或 firstName 没有发生改变，多次访问 fullName 计算属性都是返回之前的计算结果，而不会再次执行函数。

   **如果没有缓存，我们将不可避免的多次执行函数进行计算。**

**我们为什么需要缓存？**

假设我们有一个性能开销比较大的计算属性 A，它需要遍历一个巨大的数组并做大量的计算。

然后我们可能有其他的计算属性又依赖于 A。

如果没有缓存，我们将不可避免的多次执行 A 的 getter！

如果你不希望有缓存，请用方法来替代。

**特点1：**

**只在最初加载时和其中依赖的属性（数据）改变时运行**

**特点2：**

**无法处理异步**

**使用场景：**

**如果一个属性是由其他属性（数据）计算而来的，也就是说**

**这个属性依赖其他属性（数据），是一个属性依赖多个属性（一赖多，一个数据依赖多个数据）时，一般用 computed**

```vue
<div id="app">
    姓：<input v-model="firstName">
    名：<input v-model="lastName">
    <div>{{ fullName }}</div>
</div>

var app = new Vue({
  el: '#app',
  data: {
    firstName: '',
    lastName: '',
    age: 18
  },
  computed: {
    fullName: function () { 
      console.log('计算了一次');
      return this.firstName + ' ' + this.lastName;
    } // 该函数将用作属性 app.fullName 的 getter 函数
  }
});
```

改变不是计算属性依赖的数据 age，不会重新计算：

![img](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1652713660127-40e7b927-a1ef-47da-ab85-bc4eee977d00.jpeg)

只有计算属性依赖的数据变化时，才会重新计算：

![img](https://cdn.nlark.com/yuque/0/2022/png/1614731/1652713667285-e4404f10-d433-4a33-90de-f97e8d4fef1b.png)

computed 中的内部方法在重复的调用中，只要依赖数据不变，直接取缓存中的计算结果。

只有依赖的数据发生改变，computed 才会重新计算：

```vue
<p>姓名：{{ fullName }}</p>
<p>姓名：{{ fullName }}</p>
<p>姓名：{{ fullName }}</p>
<p>姓名：{{ fullName }}</p>
<p>姓名：{{ fullName }}</p>

<script>
export default {
  data() {
    return {
      firstName: '张',
      lastName: '三'
    }
  },
  computed: {
    fullName() {
      console.log('computed') // 控制台只打印了一次
      return this.firstName + this.lastName
    }
  }
};
</script>
```

methods 方法也可以实现计算，但是没有缓存：

```vue
<div id="app">
    姓：<input v-model="firstName">
    名：<input v-model="lastName">
    <div>{{ fullName() }}{{ age }}</div>
</div>

var app = new Vue({
  el: '#app',
  data: {
    firstName: '',
    lastName: '',
    age: 18
  },
  methods: {
    fullName: function () {
      console.log('计算了一次');
      return this.firstName + ' ' + this.lastName;
    }
  }
});
```

![img](https://cdn.nlark.com/yuque/0/2022/png/1614731/1652713744067-a2dd3463-3375-440e-aa73-c7ba6c42baf7.png)

![img](https://cdn.nlark.com/yuque/0/2022/png/1614731/1652713744030-e8e4c978-44dd-4783-b2e1-ca8d277c40a7.png)

侦听器（watch）也可以实现计算，没有缓存，而且代码复杂：

```vue
<div id="app">
    姓：<input v-model="firstName">
    名：<input v-model="lastName">
    <div>{{ fullName }}{{ age }}</div>
</div>

var app = new Vue({
  el: '#app',
  data: {
    firstName: '',
    lastName: '',
    fullName: '',
    age: 18
  },
  watch: {
    firstName: function () {
      console.log('计算了一次');
      this.fullName = this.firstName + '' + this.lastName;
    },
    lastName: function () {
      console.log('计算了一次');
      this.fullName = this.firstName + '' + this.lastName;
    }
  }
});
```

![img](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1652713785987-eafaa19f-aa9d-43d8-a7ac-9f884ac85e29.jpeg)

![img](https://cdn.nlark.com/yuque/0/2022/png/1614731/1652713785987-c4ba0e9d-c07a-4ad5-9478-29b6962bd5f2.png)

#### 计算属性的复杂操作

![img](https://cdn.nlark.com/yuque/0/2022/png/1614731/1652713833951-06c35314-45ef-45e3-8173-5a2604f8f26b.png)

也可以用 ES6 的 for 循环写法：

<img src="https://cdn.nlark.com/yuque/0/2022/png/1614731/1652713856143-5a922c6b-7068-4b30-93a2-ca5f24746cc0.png" alt="img" style="zoom: 67%;" />

```js
for (let book of this.books) {
  result += book.price
}
```

如果想要显示一个数组经过过滤或排序后的结果，可以创建一个计算属性，来返回过滤或排序后的数组：

```vue
<li v-for="n in evenNumbers">{{ n }}</li>

data: {
  numbers: [ 1, 2, 3, 4, 5 ]
},
computed: {
  evenNumbers: function () {
    return this.numbers.filter(function (number) {
      return number % 2 === 0
    })
  }
}
```

计算属性的 getter 与 setter（计算属性的真面目）

getter（取值函数）  当计算属性读取数据时执行

setter（赋值函数 ） 当计算属性修改数据时执行 （计算属性如果不显示定义 setter ，默认情况下其实是不写 setter 的）

```vue
<div id="app">  
    {{ fullName }}
</div>

var app = new Vue({
    el: '#app',
    data: {
        firstName: 'Mike',
        lastName: 'Wang'
    },
    computed: {
        fullName: {
            // getter 
            get: function () {
                return this.firstName + ' ' + this.lastName;
            },
            // setter
            set: function (value) {
                console.log(value);
                var arr = value.split(' ');
                this.firstName = arr[0]; // 当fullName依赖的数据变化就会引起计算属性重新计算
                this.lastName = arr[1];
            }   
        }
    }
});
```

执行 getter：

![img](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1652714093434-b61afbec-9486-42ba-b893-539d8fccd31b.jpeg)

执行 setter：

黑色字符串是 cosnole.log(value) 的结果，红色字符串是 setter 的返回结果

![img](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1652714100764-192e941c-afb4-4339-864a-2acc731b9b57.jpeg)

在 computed 中，所有的属性都有一个 get() 和一个 set()，默认先走 get() ，

当我们直接修改 fullName 时，则会调用 set() 方法：

```vue
<p>姓名：{{ fullName }}</p>
<button @click="handleClick">更改姓名</button>

<script>
export default {
  data() {
    return {
      firstName: '张',
      lastName: '三'
    }
  },
  computed: {
    fullName: {
      get() {
        return this.firstName + this.lastName
      },
      set(val) {
        console.log(val) // 李四
      }
    }
  }
  methods: {
    handleClick() {
      this.fullName = '李四'
    }
  }
};
</script>
```

#### 侦听器（watch）

**侦听哪个属性，侦听器对象里属性就取什么名称**

**watch 每个属性值可以是一个函数，该函数有两个参数，第一个参数是最新的值，第二个参数是输入之前的旧值，**

**顺序一定是先新值再旧值，如果只写一个参数，那就是新值。**

**正常 watch 在 mounted 周期后触发 data 更新， 晚于 created 执行，**

**设置 immediate：true；的 watch 早于 created 执行，**

**在初始值声明的时候去执行监听计算，官网的生命周期图中，init reactivity 是晚于 beforeCreate 但是早于 created 的。**

特点1：监听某个数据或计算属性发生的变化，一旦某个数据或计算属性发生变化就执行函数的内容！

特点2：当需要在数据变化时执行异步操作或开销较大的操作时，这个方式是最有用的！

注意：watch 的计算早于同依赖的 computed

注意：不应该使用箭头函数来定义 watcher 函数 (例如 searchQuery: newValue => this.updateAutocomplete(newValue))。

理由是箭头函数绑定了父级作用域的上下文，所以 this 将不会按照期望指向 Vue 实例。

使用场景：

一个值变化后会引起一系列操作，或者一个值变化会引起一系列值的变化（一变多，一个数据引起多个数据变化）使用 watch

```vue
<div id="app">
    姓：<input v-model="firstName">
    名：<input v-model="lastName">
    <div>{{fullName}}</div>
    <div>{{count}}</div>
</div>

var app = new Vue({
     el: '#app',
     data: {
        firstName: '',
        lastName: '',
        count: 0
     },
     computed: {
         fullName: function () {
             return this.firstName + ' ' + this.lastName;
         }
     },
     watch: {
         fullName: function () {
             this.count++;
         }
     }
});
```

![img](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1652714262142-d0541896-cd35-4aa4-b4ac-bc616c8d622a.jpeg)

除了 watch 选项之外，还可以使用命令式的 vm.$watch API。

#### computed 和 watch 有什么区别?

![img](https://cdn.nlark.com/yuque/0/2022/png/1614731/1652714338604-efc0e202-7b66-435d-9351-eb4c82e97c85.png)

![img](https://cdn.nlark.com/yuque/0/2022/png/1614731/1652714338621-eb55000c-4847-4f4b-9c4a-2091a52576b9.png)

computed：

可以监听的数据来源：初始化显示或者相关的 data、props 等属性数据发生变化的时候调用

1. computed 是计算属性,也就是计算值,它更多用于计算值的场景

2. computed 具有缓存性, computed 的值在 getter 执行后是会缓存的，

   只有在它依赖的属性值改变之后，下一次获取 computed 的值时才会重新调用对应的 getter 来计算

3. computed 不支持异步

4. computed 适用于计算比较消耗性能的计算场景

watch：

可以看作是 computed 和 methods 的结合体；

可以监听的数据来源：data，props，computed 内的数据；

1.  更多的是「观察」的作用，类似于某些数据的监听回调，用于观察 props 、 $emit 或者本组件的值，

    当数据变化时来执行回调进行后续操作

2.  没有缓存，数据变化或者触发重新渲染时，直接会触发相应的操作。

3.  watch 支持异步

当数据变化时，触发其他操作，

watch 默认浅监听(浅监听指只能监听到基础类型值的变化), watch 监听引用类型，

拿不到 oldVal，无法进行深度监听(深度监听指无法正确监听引用类型的值变化)

有两个参数，

immediate：组件加载立即触发回调函数执行，

watch 当值第一次绑定的时候，不会执行监听函数，只有值发生改变才会执行。

如果我们需要在第一次绑定值的时候也执行函数，就需要用到 immediate 。

deep: 深度监听，为了发现对象内部值的变化，也可以监听数组中的对象内部值的改变。（用于监听对象与对象数组）

注意：deep 无法监听到数组的变动和对象的新增，参考 vue 数组变异,只有以响应式的方式触发才会被监听到。

所以，简单数组的变动和对象的新增需要通过 this.$set 方法更改，watch 才可以监听到数组发生的变化

```vue
<div>
  <input type="text" v-model="name" />
  <input type="text" v-model="info.city" />
  <button @click="handleClickName">更改姓名</button>
  <button @click="handleClickInfo">更改信息</button>
</div>
<script>
export default {
  data() {
    return {
      name: '哪吒',
      info: {
        city: '北京'
      }
    }
  },
  watch: {
    name(newVal, oldVal) {
      // 值类型，可正常拿到
      console.log('watch name', newVal, oldVal) // 姜子牙，哪吒
    },
    info(newVal, oldVal) {
       // 引用类型，拿不到
      console.log('watch info', newVal, oldVal) // 控制台无输出结果
    },
    info: {
      handler(newVal, oldVal) {
        console.log('watch info', newVal, oldVal)
      },
      deep: true // 深度监听
    }
  },
  methods: {
    handleClickName() {
      this.name = '姜子牙'
    },
    handleClickInfo() {
      this.info.city = '上海'
    }
  }
}
</script>
```

小结：

1. 当一个数据依赖于其他数据，那么把这个数据设计为 computed

2. 如果你需要在某个数据变化时做一些事情，使用 watch 来观察这个数据变化

### Vue 事件绑定（v-on:click="handleClick"）

v-on 指令表示添加一个事件处理器，在 methods 对象里要有对应的事件处理函数进行处理

**推荐始终使用 kebab-case (短横线连字符) 作为事件名命名方式**。

支持的事件：https://www.jianshu.com/p/e8a7dde5e47d

事件处理器的值可以是：

1. **内联事件处理器**：事件被触发时执行的内联 JavaScript 语句 (与 `onclick` 类似)。
2. **方法事件处理器**：一个指向组件上定义的方法的属性名或是路径。

#### 内联事件处理器

内联事件处理器通常用于简单场景，例如：

```html
<div id="example-1">
  <button v-on:click="counter += 1">Add 1</button>
  <p>The button above has been clicked {{ counter }} times.</p>
</div>
```

```js
var example1 = new Vue({
  el: '#example-1',
  data: {
    counter: 0
  }
})
```

#### 方法事件处理器

```html
<div id="example-2">
  <!-- `greet` 是在下面定义的方法名 -->
  <button v-on:click="greet">Greet</button>
</div>
```

```js
var example2 = new Vue({
  el: '#example-2',
  data: {
    name: 'Vue.js'
  },
  // 在 `methods` 对象中定义方法
  methods: {
    greet: function (event) {
      // `this` 在方法里指向当前 Vue 实例
      alert('Hello ' + this.name + '!')
      // `event` 是原生 DOM 事件
      if (event) {
        alert(event.target.tagName)
      }
    }
  }
})
```

#### 方法与内联事件判断

模板编译器会通过检查 `v-on` 的值是否是合法的 JavaScript 标识符或属性访问路径来断定是何种形式的事件处理器。

**举例来说，`foo`、`foo.bar` 和 `foo['bar']` 会被视为方法事件处理器，**

**而 `foo()` 和 `count++` 会被视为内联事件处理器。**

#### 内联事件处理器中调用方法

```html
<div id="example-3">
  <button v-on:click="say('hi')">Say hi</button>
  <button v-on:click="say('what')">Say what</button>
</div>
```

```js
new Vue({
  el: '#example-3',
  methods: {
    say: function (message) {
      alert(message)
    }
  }
})
```

#### 内联事件处理器中访问事件参数

有时也需要在内联处理器中访问原始的 DOM 事件。可以用特殊变量 `$event` 把它传入方法：

```html
<button v-on:click="warn('Form cannot be submitted yet.', $event)">
  Submit
</button>
```

```js
// ...
methods: {
  warn: function (message, event) {
    // 现在我们可以访问原生事件对象
    if (event) {
      event.preventDefault()
    }
    alert(message)
  }
}
```

#### 使用方式总结

```vue
1、

v-on:click='fn' 或 @clck='fn'

v-on:click="函数名"，也可以 @click="函数名" 

2、

可以传递参数：

@click='fn(a, b)'

@click="(a, b) => handleFn(a, b)"

3、

绑定多个事件：

v-on="{mousedown: fn1, mouseup: fn2}"

@click="fn1(), fn2()"
```

**注意：**

**不应该使用箭头函数来定义 method 对象里的函数 (例如 plus: () => this.a++)。**

**原因是箭头函数绑定了父级作用域的上下文，所以 this 不会按照期望指向 Vue 实例，this.a 将是 undefined。**

```vue
<div id="app">
    <div v-on:click="handleclick">{{content}}</div>
</div>

var app = new Vue({
     el: '#app',
     data: {
         content: 'hello word!',
     },
     methods: {
         handleclick: function () {
             alert('123');
         }
     }
});
```

![img](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1652714451370-39e60541-32ba-4b48-bd95-046a23f78720.jpeg)

```vue
<div id="app">
    <div v-on:click="handleclick">{{content}}</div>
</div>

var app = new Vue({
     el: '#app',
     data: {
         content: 'hello word!',
     },
     methods: {
         handleclick: function () {
             this.content = 'Good job!'; // this 表示Vue实例的data，改变数据后实例会监听变化，然后自动更新模板
         }
     }
});
```

![img](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1652714479700-ed29cbaf-854d-47e6-9101-628bc13d00de.jpeg)

![img](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1652714479684-946479df-6c82-45b3-b0b6-ce1133a8a5c0.jpeg)

注意：v-on:click 的 v-on 可以简写成 @，即v-on:click === @click

可以为事件处理器传参数（这个参数可以从其他指令中取）：

```vue
<div id="example-3">
  <button v-on:click="say('hi')">Say hi</button>
  <button v-on:click="say('what')">Say what</button>
</div>

new Vue({
  el: '#example-3',
  methods: {
    say: function (message) {
      alert(message)
    }
  }
})
```

![img](https://cdn.nlark.com/yuque/0/2022/gif/1614731/1652714521596-30402a04-3108-412a-be07-fec074024d7e.gif)

要访问原生的 DOM 事件，可以用特殊变量 $event 把它传入方法：

```vue
<button v-on:click="warn('Form cannot be submitted yet.', $event)">
  Submit
</button>

// ...
methods: {
  warn: function (message, event) {
    // 现在我们可以访问原生事件对象
    if (event) event.preventDefault()
    alert(message)
  }
}
```

**注意点**

```vue
<div id="app">
  <!-- 在事件定义时省略小括号，但是方法本身是需要一个参数的，
       这个时候，Vue 会默认将浏览器生产的 event 事件对象作为参数传入到方法 btn1Click
   -->
  <button @click="btn1Click">按钮1</button>
   <!-- 1、方法中参数 abc 不加引号会当成变量处理，所以需要先在 data 里定义 abc
        2、方法中需要使用 $event 来获取浏览器的 event 事件对象
   -->
  <button @click="btn2Click(abc, $event)">按钮2</button>
</div>

var app = new Vue({
  el: '#app',
  data: {
    abc: 'abc'
  },
  methods: {
    // abc 是方法的第一个参数，event 事件对象会传给 abc
    btn1Click(abc) {
      console.log(abc);
    },
    btn2Click(abc, event) {
      console.log('-----', abc, event);
    }
  }
})
```

![img](https://cdn.nlark.com/yuque/0/2022/gif/1614731/1652714584087-b0dfbc2d-1b8b-4642-9973-c3c8adfd0897.gif)

method 的方法也可以放在 {{}} 里，直接取值

#### 事件修饰符

修饰符 (modifier) 是以半角句号 . 指明的特殊后缀，用于指出一个指令应该以特殊方式绑定。

例如，.prevent 修饰符告诉 v-on 指令对于触发的事件调用 event.preventDefault()：

```html
<form v-on:submit.prevent="onSubmit">...</form>
```

JavaScript 里手动绑定事件，你的 ViewModel （Vue 实例） 代码可以是非常纯粹的逻辑，和 DOM 完全解耦，更易于测试。

当一个 ViewModel 被销毁时，所有的事件处理器都会自动被删除。你无须担心如何清理它们。

事件修饰符

● .stop        阻止事件继续传播

● .prevent   阻止事件的默认行为

● .capture   事件监听器使用事件捕获模式

● .self          事件不从内部元素触发

```html
<!-- 单击事件将停止传递 -->
<a @click.stop="doThis"></a>

<!-- 提交事件将不再重新加载页面 -->
<form @submit.prevent="onSubmit"></form>

<!-- 修饰符可以使用链式书写 -->
<a @click.stop.prevent="doThat"></a>

<!-- 也可以只有修饰符 -->
<form @submit.prevent></form>

<!-- 仅当 event.target 是元素本身时才会触发事件处理器 -->
<!-- 例如：事件处理器不来自子元素 -->
<div @click.self="doThat">...</div>
```

> 使用修饰符时需要注意调用顺序，因为相关代码是以相同的顺序生成的。
>
> **因此使用 `@click.prevent.self` 会阻止元素及其子元素的所有点击事件的默认行为**
>
> **而 `@click.self.prevent` 则只会阻止对元素本身的点击事件的默认行为。**

● .once        事件只会触发一次

```vue
<!-- 点击事件将只会触发一次 -->
<a v-on:click.once="doThis"></a>
```

● .passive  事件的默认行为会立即触发

```vue
<!-- 滚动事件的默认行为 (即滚动行为) 将会立即触发 -->
<!-- 而不会等待 `onScroll` 完成， 这其中包含 `event.preventDefault()` 的情况-->
<div v-on:scroll.passive="onScroll">...</div>
```

修饰符可以串联

```vue
<!-- 修饰符可以串联，相应的代码会以同样的顺序产生-->
<a v-on:click.stop.prevent="doThat"></a>
```

#### 按键修饰符

Vue 允许为 v-on 在监听键盘事件时添加按键修饰符：

```vue
<!-- 只有在 `key` 是 `Enter` 时调用 `vm.submit()` -->
<input v-on:keyup.enter="submit">
```

#### 按键别名

Vue 为一些常用的按键提供了别名：

- `.enter`
- `.tab`
- `.delete` (捕获“Delete”和“Backspace”两个按键)
- `.esc`
- `.space`
- `.up`
- `.down`
- `.left`
- `.right`

#### 系统按键修饰符

你可以使用以下系统按键修饰符来触发鼠标或键盘事件监听器，只有当按键被按下时才会触发。

- `.ctrl`
- `.alt`
- `.shift`
- `.meta`

#### 鼠标按键修饰符

- `.left`
- `.right`
- `.middle`

#### 将原生事件绑定到组件

**想要在一个组件的根元素上直接监听一个原生事件。**

这时，可以使用 `v-on` 的 `.native` 修饰符：

```html
<base-input v-on:focus.native="onFocus"></base-input>
```

在有的时候这是很有用的，不过在你尝试监听一个类似 `<input>` 的非常特定的元素时，这并不是个好主意。

比如上述 `<base-input>` 组件可能做了如下重构，所以根元素实际上是一个 `<label>` 元素：

```html
<label>
  {{ label }}
  <input
    v-bind="$attrs"
    v-bind:value="value"
    v-on:input="$emit('input', $event.target.value)"
  >
</label>
```

这时，父级的 `.native` 监听器将静默失败。

虽然它不会产生任何报错，但是 `onFocus` 处理函数不会被调用。

为了解决这个问题，**Vue 提供了一个 `$listeners` property，它是一个对象，里面包含了作用在这个组件上的所有监听器。**

例如：

```js
{
  focus: function (event) { /* ... */ }
  input: function (value) { /* ... */ },
}
```

**有了这个 `$listeners` property，就可以配合 `v-on="$listeners"` 将所有的事件监听器指向这个组件的某个特定的子元素。**

对于希望类似 `<input>` 可以配合 `v-model` 工作的组件来说，

为这些监听器创建一个类似下述 `inputListeners` 的计算属性通常是非常有用的：

```js
Vue.component('base-input', {
  inheritAttrs: false,
  props: ['label', 'value'],
  computed: {
    inputListeners: function () {
      var vm = this
      // `Object.assign` 将所有的对象合并为一个新对象
      return Object.assign({},
        // 我们从父级添加所有的监听器
        this.$listeners,
        // 然后我们添加自定义监听器，
        // 或覆写一些监听器的行为
        {
          // 这里确保组件配合 `v-model` 的工作
          input: function (event) {
            vm.$emit('input', event.target.value)
          }
        }
      )
    }
  },
  template: `
    <label>
      {{ label }}
      <input
        v-bind="$attrs"
        v-bind:value="value"
        v-on="inputListeners"
      >
    </label>
  `
})
```

现在 `<base-input>` 组件是一个**完全透明的包裹器**了，

也就是说它可以完全像一个普通的 `<input>` 元素一样使用了：

**所有跟它相同的 attribute 和监听器都可以工作，不必再使用 `.native` 监听器。**

### 条件渲染

#### v-if、v-show指令

v-if 指令用于条件性地渲染一块内容。这块内容只会在指令的表达式返回 truthy 值的时候被渲染。

因为 v-if 是一个指令，所以必须将它添加到一个元素上。

v-if="show"表示只有当 show 为 true 时，该元素才会显示，否则隐藏。

```vue
<div id="app">
    <h1 v-if="show">{{content}}</h1>
    <button @click="handleClick">toggle</button>
</div>

var app = new Vue({
     el: '#app',
     data: {
        content: 'hello world',
        show: true
     },
     methods: {
         handleClick: function () {
             this.show = !this.show;
         }
     }
});
```

![img](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1652751461637-0881a327-00b6-41c7-8478-ad37ea12d3f1.jpeg)

点击之后：

![img](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1652751468866-096e620d-3e26-4805-9345-9607cd0d48db.jpeg)

**v-if（项目中常用）**

**v-if 与 v-show 可以互换，但是区别如下：v-show 只是简单地切换元素的 CSS 属性 display 为 none。**

**如果显示隐藏点击频率高，v-show 性能更好，因为渲染开销小！**

v-if

![img](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1652751544877-18e653b9-f3d0-4fda-9a11-dbaeb2e1db87.jpeg)

v-show（性能好）

![img](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1652751551486-90095fd7-37cc-41a6-a51e-b2df2941ffdb.jpeg)

##### v-show 指令算是重排吗？

重排：当渲染树中的一部分(或全部)因为元素的规模尺寸，布局，隐藏等改变而需要重新构建

因为 v-show 指令会改变 display 的属性，会发生重排。

扩展：

display: none; 、visibility: hidden; 、 opacity: 0; 三者之间的区别

<img src="https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1652751630247-dc9cbc64-336c-4804-a90c-8be4e582c3ec.jpeg" alt="img" style="zoom:67%;" />

**v-if、v-else-if （比较少使用，因为逻辑复杂时应该使用计算属性）与 v-else 指令**

**v-if、v-else-if 与 v-else 必须连在一起，中间不能有任何标签，否则失效**

```vue
<div id="app">
    <div v-if="show === 'a'">This is A</div>
    <div v-else-if="show === 'b'">This is B</div>
    <div v-else>This is others</div>
</div>

var app = new Vue({
  el: '#app',
  data: {
    show: 'a'
  }
});  
```

![img](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1652751681656-ebd4c4f4-bb9a-460c-817f-69d9850419ea.jpeg)

![img](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1652751681674-955585eb-2fb3-4260-a4f2-9b02e476b45d.jpeg)

##### 用 key 管理可复用的元素

```vue
<div id="app">
    <div v-if="show">用户名:<input/></div>
    <div v-else>邮箱名：<input/></div>
</div>

var app = new Vue({
  el: '#app',
  data: {
    show: false
  }
});
```

![img](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1652751745406-5605e82c-fe19-43cc-943e-21ba3c5ec568.jpeg)

![img](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1652751745402-5541068c-861b-4143-a762-9ce24ea6d929.jpeg)

出现问题：邮箱名变成用户名后，input 框没有清空，`<input>` 不会被替换掉 —— 只会替换它前面的文本内容。

**原因：Vue 的虚拟 DOM 会尽可能高效地渲染元素，会尽量尝试复用页面已经存在的 DOM 元素而不是从头开始渲染。**

**解决方案：**

**给每一个 input 添加一个唯一的 key 值，让 Vue 知道“这两个元素是完全独立的，不要复用它们”，**

两个元素 key 值不一样 Vue 就不会复用了，如果 key 相同就会复用。

原理：这是虚拟 DOM 的 diff 算法的内容，key 是为了高效利用虚拟 DOM 的 diff 算法。

2.2.0+ 的版本里，当在组件上使用 v-for 时，key 现在是必须的。

如果后台接口返回的循环数据没有唯一值，那么可以在客户端利用 map 循环对原数组内容进行遍历，

然后返回经处理过带有唯一值的数组列表，那么在 v-for 循环的时候依旧设置的是唯一值数据。

```vue
<div id="app">
    <div v-if="show">用户名：<input key="username"/></div>
    <div v-else>邮箱名：<input key="email"/></div>
</div>

var app = new Vue({
  el: '#app',
  data: {
    show: false
  }
});  
```

**当 v-if 与 v-for 一起使用时，v-for 具有比 v-if 更高的优先级，但是不推荐同时使用 v-if 和 v-for！（Vue 2.x）**

**Vue 中 v-for 与 v-if 能否一起使用？（Vue 2.x）**

不能，详情请见[文档](https://v2.cn.vuejs.org/v2/style-guide/#%E9%81%BF%E5%85%8D-v-if-%E5%92%8C-v-for-%E7%94%A8%E5%9C%A8%E4%B8%80%E8%B5%B7%E5%BF%85%E8%A6%81)

● 这需要考虑到 v-for 与 v-if 的优先级顺序问题，

   当它们处于同一节点，v-for 的优先级比 v-if 更高

这意味着 `v-if` 将分别重复运行于每个 `v-for` 循环中。当你只想为*部分*项渲染节点时，这种优先级的机制会十分有用，如下：

```vue
<li v-for="todo in todos" v-if="!todo.isComplete">
  {{ todo }}
</li>
```

上面的代码将只渲染未完成的 todo。

而如果你的目的是有条件地跳过循环的执行，那么可以将 `v-if` 置于外层元素 (或  `<template>`) 上。如：

```vue
<ul v-if="todos.length">
  <li v-for="todo in todos">
    {{ todo }}
  </li>
</ul>
<p v-else>No todos left!</p>
```

● 如果对于整体循环内容的控制，建议将 v-if 条件判断编写至 v-for 循环的外部

● 如果需要对每个循环内容进行条件判断的话，建议编写 computed 属性计算进行对应内容的控制

##### `<template>` 上的  v-if

因为 `v-if` 是一个指令，必须依附于某个元素。但如果我们想要切换不止一个元素呢？

在这种情况下我们可以在一个 `<template>` 元素上使用 `v-if`，

这只是一个不可见的包装器元素，最后渲染的结果并不会包含这个 `<template>` 元素。

template

```html
<template v-if="ok">
  <h1>Title</h1>
  <p>Paragraph 1</p>
  <p>Paragraph 2</p>
</template>
```

### 列表渲染

#### v-for 指令

使用 v-for 渲染的元素列表时，如果数据项的顺序被改变，Vue 将不会移动 DOM 元素来匹配数据项的顺序，

而是就地更新每个元素，并且确保它们在每个索引位置正确渲染。

遍历 data 里的某个数组，使一个 DOM 元素多次渲染，也可以循环展示数组的每一项。

一般情况是取出数据使用插值语法 `{{}}` 在模板中输出渲染数据 或 取出数据作为 v-on 的参数，亦或给其他指令使用。

v-for="(item, index)" 索引声明的时候就相当于给元素附上 this.index。

循环出来的 item, 和 index 可以放进  v-for 同一行的元素特性上，也可以放进其它指令中。

v-for 指令需要使用 item in items 形式的特殊语法，其中 items 是源数据数组，而 item 则是被迭代的数组元素的别名。

也可以用 of 替代 in 作为分隔符，它更接近 JavaScript 迭代器的语法。

v-for 指令可以绑定数组的数据来渲染一个列表：

```vue
<div id="app">
    <ul>
        <li v-for="item of list" :key="item"> {{item}} </li>
    </ul>
</div>

var app = new Vue({
     el: '#app',
     data: {
        list: [1, 2, 3]
     }
});
```

![img](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1652751890203-7e02771c-cece-440e-b395-19eb7d8d0800.jpeg)

v-for="item of list" :key="item"

表示循环数据项 list，每次循环把对应循环项放进变量 item，配合 :key 会提升每一项渲染的效率，:key 值要求每次循环都不能相同，必须是唯一的。

:key 是为了给 Vue 一个提示，以便它能跟踪每个节点的身份，从而重用和重新排序现有元素，它是 Vue 识别节点的一个通用机制。

v-for 还支持一个可选的第二个参数，即当前项的索引 index：

第二个参数 index 是内容在数组的下标：

```vue
<div id="app">
    <div v-for="(item, index) of list">{{item}} -- {{index}}</div>
</div>

var app = new Vue({
    el: '#app',
    data: {
        list: ['a', 'b', 'c', 'd', 'e']
    }
})
```

![img](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1652751929645-8b98cb8f-d925-4ecf-be0a-0b907a5e2696.jpeg)

##### 在 `v-for` 里使用解构

实际上，你也可以在定义 `v-for` 的变量别名时使用解构，和解构函数参数类似：

template

```html
<li v-for="{ message } in items">
  {{ message }}
</li>

<!-- 有 index 索引时 -->
<li v-for="({ message }, index) in items">
  {{ message }} {{ index }}
</li>
```

##### 在 `v-for` 里使用范围值

`v-for` 可以直接接受一个整数值。

在这种用例中，会将该模板基于 `1...n` 的取值范围重复多次。

template

```vue
<span v-for="n in 10">{{ n }}</span>
```

注意此处 `n` 的初值是从 `1` 开始而非 `0`。

##### 多层嵌套的 v-for

对于多层嵌套的 `v-for`，作用域的工作方式和函数的作用域很类似。每个 `v-for` 作用域都可以访问到父级作用域：

template

```html
<li v-for="item in items">
  <span v-for="childItem in item.children">
    {{ item.message }} {{ childItem }}
  </span>
</li>
```

##### 在 `v-for` 里使用对象

```js
new Vue({
  el: '#v-for-object',
  data: {
    object: {
      title: 'How to do lists in Vue',
      author: 'Jane Doe',
      publishedAt: '2016-04-10'
    }
  }
})
```

```vue
<div v-for="(value, name, index) in object">
  {{ index }}. {{ name }}: {{ value }}
</div>
```

> **遍历对象时，遍历的顺序会基于对该对象调用 `Object.keys()` 的返回值来决定。**

##### `<template>` 上的 v-for

与模板上的 `v-if` 类似，你也可以在 `<template>` 标签上使用 `v-for` 来渲染一个包含多个元素的块。例如：

template

```html
<ul>
  <template v-for="item in items">
    <li>{{ item.msg }}</li>
    <li class="divider" role="presentation"></li>
  </template>
</ul>
```

##### key 值

v-for="(item, index) of list" :key="index" 

可以使用 index 作为 :key 值，但是进行排序、频繁操作 DOM 时会有问题，所以尽量不要使用 index 或 item 作为:key值

因为必须让元素与 :key 一一对应，保证: key 是唯一的

在实际项目中通常使用 id 作为 :key 值

例如，在数组中间插入一个元素：

<img src="https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1652751951376-b9ba5b20-c083-43fc-af3c-94d1f15c7035.jpeg" alt="img" style="zoom: 80%;" />

没有 key 时的算法（F 替代 C，后面的 li 元素都要往后退）：

<img src="https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1652751960402-35b4647a-b256-4b9b-8858-4e5d7e753a0a.jpeg" alt="img" style="zoom:80%;" />

有 key 时的算法（对比不同后，直接插入）：

![img](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1652752007735-11e5cc2e-43ce-4a72-9911-a4a2ec0f7925.jpeg)

##### 组件上使用 v-for

我们可以直接在组件上使用 `v-for`，和在一般的元素上使用没有区别 (别忘记提供一个 `key`)：

template

```html
<MyComponent v-for="item in items" :key="item.id" />
```

但是，这不会自动将任何数据传递给组件，因为组件有自己独立的作用域。为了将迭代后的数据传递到组件中，我们还需要传递 props：

template

```vue
<MyComponent
  v-for="(item, index) in items"
  :item="item"
  :index="index"
  :key="item.id"
/>
```

不自动将 `item` 注入组件的原因是，这会使组件与 `v-for` 的工作方式紧密耦合。明确其数据的来源可以使组件在其他情况下重用。

##### 数组更新注意

由于 JavaScript 的限制，Vue 不能检测以下数组的变动：

不能使用方括号表示法（下标）或者修改数组 length 的的方式改变数组，数据变了，但是视图不会变

解决方案

1、使用数组的变异方法（pop、push、shift、unshift、splice、sort、reverse）来改变数组，

变异方法会改变调用了这些方法的原始数组。

![img](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1652752070850-a985a3a8-a2df-4d64-8ea4-74cf0de96a86.jpeg)

2、改变引用来改变数组

![img](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1652752082820-0ad72ea0-fbf3-4e7c-8701-4dd9f89d9915.jpeg)

3、使用 set 方法来改变数组

![img](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1652752095074-6f6a9aab-caec-4579-8c9b-27b631d1da9a.jpeg)

##### 对象变动注意

Vue 无法检测到对象属性的添加或删除

由于 Vue 会在初始化实例时对 data 的 property 执行 getter/setter 转化，

所以 property 必须在 data 对象上存在才能让 Vue 将它转换为响应式的。

```js
var vm = new Vue({ 
  data:{ a:1 } 
}) 
// `vm.a` 是响应式的 

vm.b = 2 
// `vm.b` 是非响应式的
```

Vue.set() 不仅可以设置数组的变动也可以设置对象的变动

Vue.set( target, propertyName/index, value )

● 参数：

  ● {Object | Array} target

  ● {string | number} propertyName/index

  ● {any} value

● 返回值：设置的值。

● 用法：

向响应式对象中添加一个 property，并确保这个新 property 同样是响应式的，且触发视图更新。

它必须用于向响应式对象上添加新 property，因为 Vue 无法探测普通的新增 property (比如 this.myObject.newProperty = 'hi') 。

对于已经创建的实例，Vue 不允许动态添加根级别的响应式 property。

但是，可以使用 Vue.set(object, propertyName, value) 方法向嵌套对象添加响应式 property。

例如：

```js
Vue.set(vm.someObject, 'b', 2)
```

您还可以使用 vm.$set 实例方法，这也是全局 Vue.set 方法的别名：

```js
this.$set(this.someObject,'b',2)
```



```vue
<div id="app">
    <div v-for="(item, key, index) of userInfo">{{item}} -- {{key}} -- {{index}}</div>
</div>

var app = new Vue({
    el: '#app',
    data: {
        userInfo: {
            name: '小黑',
            age: 28,
            gender: 'male'
        }
    }
})
```

![img](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1652752316472-8a4d14a9-bf3e-433c-996f-c3afbba798f7.jpeg)

点表示法可以修改对象的属性，但是不可以为对象添加属性：

![img](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1652752329032-437ccc47-e1db-440d-984f-5cc338c78099.jpeg)

解决方案

1、改变对象的引用来添加对象属性

![img](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1652752338863-141c30da-9127-4102-877a-e9144b869390.jpeg)

2、使用 set 方法来添加对象属性

![img](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1652752347330-ce9fb0b6-9421-43b9-97ca-acaf8d19ee25.jpeg)

如果需要为已有对象赋值多个新属性，应该这样做：

```js
vm.userProfile = Object.assign({}, vm.userProfile, {
    age: 27,
    favoriteColor: 'Vue Green'
})
```

Vue 不能检测对象属性的添加或删除，

但利用 Vue.set(object, key, value) / Vue.delete(Object, key) 方法可以向嵌套对象添加响应式属性，

还可以使用 vm.$set / vm.$delete 实例方法，它只是全局 Vue.set / Vue.delete 的别名。



如果不仅要循环取值给 div，还要循环取值给 span 呢？

可以使用模板占位符，模板占位符可以包裹元素但模板占位符不会渲染在页面上

```vue
<div id="app">
    <template v-for="(item, index) of list">
        <div>{{item}} -- {{index}}</div>
        <span>{{item}}</span>
    </template>
</div>

var app = new Vue({
    el: '#app',
    data: {
        list: ['a', 'b', 'c', 'd', 'e']
    }
})
```

![img](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1652752470529-9747ff57-7d0c-4a2b-931d-93ed00b17b08.jpeg)

也可以用 v-for 来遍历一个对象的属性

```vue
<div v-for="(value, name, index) in object">
    {{ index }}. {{ name }}: {{ value }}
</div>
```

不过 for-in 与 for-of 在 vue 里面没区别

### 小案例

#### 点击列表项变色

v-for 与 v-bind 结合( v-bind 可以取 v-for 遍历的值） 

```css
<style>
  /*点击文字变颜色*/
  .isActive{
    color: red;
  }
</style>
```

```vue
<div id='app'>
    <ul>
      <li v-for="(item, index) in movies" @click="btnClick(index)" :class="{isActive:currentIndex === index}">{{item}}</li>
    </ul>
</div>

const app = new Vue({
  el: '#app',
  data: {
    currentIndex: '',
    movies: ["海贼王", "阿凡达", "进击的巨人"]
  },
  methods: {
    btnClick (index) {
      this.currentIndex = index   
    }
  }
})
```

![img](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1652752606764-427723e1-4f63-4499-b3f3-9eb6a8e12494.jpeg)

![img](https://cdn.nlark.com/yuque/0/2022/gif/1614731/1652752606790-b5ccdcc5-39eb-454e-9bf7-229bf41a3698.gif)

#### Vue 制作简单的购物车

index.html

```html
<body>
  <div id="app">
    <div v-if="books.length">
      <table>
        <thead>
          <tr>
            <th></th>
            <th>书籍名称</th>
            <th>出版日期</th>
            <th>价格</th>
            <th>购买数量</th>
            <th>操作</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="(item, index) in books">
            <td>{{item.id}}</td>
            <td>{{item.name}}</td>
            <td>{{item.date}}</td>
            <td>{{item.price | fixed}}</td>
            <td>
              <button @click="decrease(index)" :disabled="item.count <= 1">-</button>
              {{item.count}}
              <button @click="increase(index)">+</button>
            </td>
            <td>
              <button @click="remove(index)">移除</button>
            </td>
          </tr>
        </tbody>
      </table>
      <h2>总价格：{{totalPrice | fixed}}</h2>
    </div>    
    <h2 v-else>购物车为空</h2>
  </div>
  <script src="vue.js"></script>
  <script src="main.js"></script>
</body>
```

main.js

```js
const app = new Vue({
  el: "#app",
  data: {
    books: [
      {
        id: 1,
        name: '算法导论',
        date: "2006-9",
        price: 85.00,
        count: 1
      },
      {
        id: 2,
        name: 'UNIX编程艺术',
        date: "2006-2",
        price: 59.00,
        count: 1
      },
      {
        id: 3,
        name: '编程珠玑',
        date: "2008-10",
        price: 39.00,
        count: 1
      },
      {
        id: 4,
        name: '代码大全',
        date: "2006-03",
        price: 128.00,
        count: 1
      }
    ]
  },
  methods: {
    increase(index) {
      this.books[index].count++;
    },
    decrease(index) {
      this.books[index].count--;
    },
    remove(index) {
      this.books.splice(index, 1);
    }
  },
  filters: { // 过滤器
    fixed(price) { // 管道符号前面的属性会作为参数传给 price
      return '¥' + price.toFixed(2) // 使价格保留两位小数
    }
  },
  computed: {
    totalPrice() {
      let totalPrice = 0;
      for (let i = 0; i < this.books.length; i++) {
        totalPrice += this.books[i].count * this.books[i].price
      }
      // 可简写为
      // for (let item of this.books) {
      //   totalPrice += item.count * item.price
      // }
      return totalPrice
      // 或者最简短形式
      // return this.books.reduce((prev, book) => prev + book.price * book.count, 0)
    }         
  }
})
```

style.css

```css
table{
  border: 1 px solid #e9e9e9;
  border-collapse: collapse;
  border-spacing: 0;
}

th, td{
  padding: 8px 16px;
  border:1 px solid #e9e9e9;
  text-align: left;
}

th{
  background-color: #f7f7f7;
  color: #5c6b77;
  font-weight:600;
}
```

![img](https://cdn.nlark.com/yuque/0/2022/gif/1614731/1652752700522-bc89e63a-ceab-4e2f-b38c-fa58c4dea1de.gif)

### 过滤器

可被用于一些常见的文本格式化。

过滤器可以用在两个地方：双花括号插值和 v-bind 表达式 (后者从 2.1.0+ 开始支持)。

过滤器应该被添加在 JavaScript 表达式的尾部，由“管道”符号指示：

```vue
<!-- 在双花括号中 -->
{{ message | capitalize }}

<!-- 在 `v-bind` 中 -->
<div v-bind:id="rawId | formatId"></div>
```

可以在一个组件的 options 选项中定义本地的过滤器：

```js
filters: {
  capitalize: function (value) {
    if (!value) return ''
    value = value.toString()
    return value.charAt(0).toUpperCase() + value.slice(1)
  }
}
```

在上述例子中，capitalize 过滤器函数将会收到 message 的值作为第一个参数 value。

过滤器函数总接收表达式的值 (之前的操作链的结果) 作为第一个参数。

下面这个例子用到了 capitalize 过滤器：

![img](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1652752775973-0714d893-feb5-4017-8e46-19027d6d42b5.jpeg)

过滤器可以串联：

```vue
{{ message | filterA | filterB }}
```

在这个例子中，filterA 被定义为接收单个参数的过滤器函数，表达式 message 的值将作为参数传入到函数中。

然后继续调用同样被定义为接收单个参数的过滤器函数 filterB，将 filterA 的结果传递到 filterB 中。

过滤器是 JavaScript 函数，因此可以接收参数：

```vue
{{ message | filterA('arg1', arg2) }}
```

这里，filterA 被定义为接收三个参数的过滤器函数。其中 message 的值作为第一个参数，普通字符串 'arg1' 作为第二个参数，表达式 arg2 的值作为第三个参数。

### Vue 制作简单的 todo-list

v-model、v-on、v-for 综合运用

```html
<div id="app">
    <input type="text" v-model="inputVal">
    <button v-on:click="btnClick">提交</button>
    <ul>
        <li v-for="item of list" :key="item">{{item}}</li>
    </ul>
</div>
```

```js
var app = new Vue({
    el: '#app',
    data: {
        inputVal: '',
        list: []
    },
    methods: {
        btnClick: function () {
            this.list.push(this.inputVal);
            this.inputVal = ''
        }
    }
})
```

![img](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1652752859603-42f79412-6f4c-4e5e-b810-5b776758ab77.jpeg)

## 组件化

<img src="https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1652752921630-53c4ca3d-1c1b-4e92-9f3a-2a26c91fe481.jpeg" alt="img" style="zoom: 67%;" />

<img src="https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1652752968306-7690b39d-c87e-49cf-bca8-c8b80a9c8253.jpeg" alt="img" style="zoom:67%;" />

组件与实例

组件：独立、可重复使用、并且可以和其他对象进行交互的对象

前端组件化：每个组件就是页面上的某一部分或某一区域

<img src="https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1652752976482-235b05a4-dbf1-4ccf-aefe-c3f6167942bf.jpeg" alt="img" style="zoom:67%;" />

每个组件都是一个 Vue 实例，每个 Vue 实例都是一个组件

所以组件和实例一样接受相同的选项（option）对象 (一些根实例特有的选项除外)，也就是说，组件和实例可以使用相同的属性

Vue 组件非常类似于自定义元素

Vue 的组件系统允许我们使用小型、独立和通常可复用的组件构建大型应用

几乎任意类型的应用界面都可以抽象为一个组件树：

顶层的一个绿盒子表示左图浅灰色的整个网页（根实例），

中间那层的三个绿盒子表示左图深灰色的页头、内容、侧边栏，

最下面一层分别表示左图的黑色显示部分（像博文、导航链接之类的东西）

<img src="https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1652752982766-85bbe157-83d3-4022-ab30-42e359ea8993.jpeg" alt="img" style="zoom:67%;" />

### 组件的注册

<img src="https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1652753084332-a88bcb7e-4a2c-4286-915b-e33ca0c508b1.jpeg" alt="img" style="zoom:67%;" />

<img src="https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1652753084354-5f5fc46d-ef9e-4e5d-8e93-83f582de5a11.jpeg" alt="img" style="zoom:67%;" />

<img src="https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1652753084551-0c6d0860-e23a-45ef-a93e-7a3f7bcf243f.jpeg" alt="img" style="zoom:67%;" />

![img](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1652753084314-474b0f5a-0023-4bd4-a536-4f1782d8177f.jpeg)

#### 定义组件名的方式

kebab-case (短横线分隔命名，推荐)，例如 `<my-component-name>`。

PascalCase (首字母大写命名)，例如 `<MyComponentName>`。

**在单文件组件中，推荐为子组件使用 `PascalCase` 的标签名**，以此来和原生的 HTML 元素作区分。

虽然原生 HTML 标签名是不区分大小写的，但 Vue 单文件组件是可以在编译中区分大小写的。

我们也可以使用 `/>` 来关闭一个标签。

如果你是直接**在 DOM 中书写模板 (原生 DOM )，模板的编译需要遵从浏览器中 HTML 的解析行为。**

**在这种情况下，你应该需要使用 `kebab-case` (短横线连字符) 形式并显式地关闭这些组件的标签。**

**无论是 PascalCase 形式的组件名称、camelCase 形式的 prop 名称还是 v-on 的事件名称，**

**都需要转换为相应等价的 `kebab-case` (短横线连字符)  形式**

> **请注意上面所说只适用于直接在 DOM 中编写模板的情况。如果你使用来自以下来源的字符串模板，就不需要顾虑这些限制了：**
>
> - **单文件组件**
> - **内联模板字符串 (例如 `template: '...'`)**
> - **`<script type="text/x-template">`**

template

```html
<!-- 如果是在 DOM 中书写该模板 -->
<button-counter></button-counter>
<button-counter></button-counter>
<button-counter></button-counter>
```


两种组件的注册类型：全局注册和局部注册

#### 全局组件

```js
Vue.component('组件标签名', {组件构造器对象})
```

全局组件只是意味着可以在多个 Vue 实例下使用！

全局组件可以用在其它任何 (通过 new Vue) 新创建的 Vue 根实例的模板中，也包括其组件树中的所有子组件的模板中。

一个 prop 被注册之后，你就可以像这样把 数据 作为一个自定义 attribute 传递给 props：

```html
<div id="app"> 
  <blog-post title="My journey with Vue"></blog-post> 
  <blog-post title="Blogging with Vue"></blog-post> 
  <blog-post title="Why Vue is so fun"></blog-post>
</div>
```

```vue
Vue.component('blog-post', {
  props: ['title'],
  template: '<h3>{{ title }}</h3>'
})

new Vue({
  el: '#app'
})
```

```vue
// 在 JavaScript 中是 camelCase 的
<blog-post post-title="hello!"></blog-post>

Vue.component('blog-post', {
  // 在 JavaScript 中是 camelCase 的
  props: ['postTitle'],
  template: '<h3>{{ postTitle }}</h3>'
})
```

**props 就是在组件上注册的一些自定义 attribute 。**

**当一个值传递给一个 prop 的 attribute 的时候，它就变成了那个组件实例的一个 property。**

**在上述模板中，你会发现我们能够在组件实例中访问这个值，就像访问 data 中的值一样（使用 {{}} 或 this.）。**

**props 可以是一个数组，一个组件默认可以拥有任意数量的 prop。**

**通过 v-bind 动态赋值，任何类型的值都可以传递给一个 prop。**

效果图

![img](https://cdn.nlark.com/yuque/0/2022/png/1614731/1652753293117-4382a74b-d235-456e-ab93-6ae607659013.png)

![img](https://cdn.nlark.com/yuque/0/2022/png/1614731/1652753293120-c40339cf-bbcd-43b1-bf74-78aa3a98509c.png)

注意

1、全局组件要放在 new Vue 的实例里才能渲染

2、全局组件要在 Vue 实例之前就注册好，也就是说 Vue.component() 要放在 new Vue() 之前

​     如果 blog-post 是在 app 之后注册，那么当 app 挂载到页面时，blog-post  还没有注册，

​     那么自然 app 中就不能使用 my-component 这个组件，控制台中会报错：

![img](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1652753363090-a3ba9583-0a34-408a-893d-80b2722f919a.jpeg)

**记住：**

**不加 v-bind: 时，是 字符串绑定 prop，而不是 data属性 或任意数据类型 绑定 prop！**

**一般采用 v-bind: 形式从 data 取出数据传递给 prop！**

#### 局部组件

```
components: {
    TodoItem: TodoItem // 组件标签名: 组件构造器对象
}
```

对于 components 对象中的每个 property 来说，

其 property 名就是自定义元素（组件）的名字，其 property 值就是这个组件的选项对象。

局部组件需要在 Vue实例 里面使用 components 属性进行注册，

只能在该 Vue实例 挂载元素 的模板中使用，在其它 Vue实例 挂载元素 的模板中无法使用：

```vue
<div id="app">
    <input type="text" v-model="inputVal">
    <button v-on:click="btnClick">提交</button>
    <ul>
        <!-- <li v-for="item of list" :key="item">{{item}}</li> -->
        <todo-item v-bind:content="item" v-for="item of list" ></todo-item>
    </ul>
</div>

var TodoItem = {
    props: ['content'],
    template: '<li>{{content}}</li>'
}

var app = new Vue({
    el: '#app',
    components: {
        TodoItem: TodoItem // 表示 局部组件TodoItem 在该实例中可以使用 todo-item 作为标签
    },
    data: {
        inputVal: '',
        list: []
    },
    methods: {
        btnClick: function () {
            this.list.push(this.inputVal);
            this.inputVal = ''
        }
    }
})  
```

![img](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1652753500115-79e3ed5f-2331-4f99-8151-39de45ee256e.jpeg)

<img src="https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1652753500196-d13b757d-baca-4ed9-867c-59c4dbadfe48.jpeg" alt="img" style="zoom: 80%;" />

#### 父子组件

```vue
<div id="app">
  <cpn2></cpn2>
  <!-- <cpn1></cpn1> // cpn1 这个子组件只能在 cpn2 里被识别。
                        机制：会先从 cpn2 里找有没有注册过这个局部组件；如果没有就去全局组件找；都没有就会报错-->
</div>

// 1、创建第一个组件构造器  
const cpn1 = Vue.extend({
  template:
  `<div>
     <h2>我是标题1</h2>
     <p>我是内容No.1</p>
   </div> `
})

// 2、创建第二个组件构造器  
const cpn2 = Vue.extend({
  template:
    `<div>
     <h2>我是标题2</h2>
     <p>我是内容No.2</p>
     <cpn1></cpn1>
   </div> `,
   components: {
     cpn1: cpn1 // cpn1 注册为 cpn2 的局部组件，那么 cpn2 是父组件，cpn1 是子组件
   }
})

// app 是根组件  
const app = new Vue({
  el: '#app',
  components: {
    cpn2: cpn2
  }
})
```

<img src="https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1652753575523-d2c6b1f8-fe51-4421-a464-3ced9395f1c1.jpeg" alt="img" style="zoom:67%;" />

<img src="https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1652753575528-ff459976-6a4c-4859-a4d9-f1cf1a3a8f2e.jpeg" alt="img" style="zoom:67%;" />

**注册组件的语法糖形式：**

```vue
<div id="app">
  <cpn2></cpn2>
  <!-- <cpn1></cpn1> // cpn1 这个子组件只能在 cpn2 里被识别。机制：会先从 cpn2 里找有没有注册过这个局部组件；如果没有就去全局组件找；都没有就会报错-->
</div>

const cpn1 = Vue.component('cpn1', {
  template:
  `<div>
     <h2>我是标题1</h2>
     <p>我是内容No.1</p>
   </div> `
})

const cpn2 = Vue.component('cpn2', {
  template:
    `<div>
     <h2>我是标题2</h2>
     <p>我是内容No.2</p>
     <cpn1></cpn1>
   </div> `,
   components: {
     cpn1: cpn1 // cpn2是父组件，cpn1是子组件
   }
})

// app是根组件  
const app = new Vue({
  el: '#app',
  components: {
    cpn2: cpn2
  }
})
```

组件模板的分离写法

字符串形式

```vue
<div id="app">
  <cpn1></cpn1>
</div>

<template id="cpn1">
  <div>
    <h2>我是标题1</h2>
    <p>我是内容No.1</p>
  </div>
</template>

const cpn1 = Vue.component('cpn1', {
  template: '#cpn1'
})

const app = new Vue({
  el: '#app',
  components: {
    cpn1: cpn1
  }
})
```

`<script type="text/x-template"> 形式`

<img src="https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1652753879054-70359e7d-b7cd-472e-8162-8677c268c813.jpeg" alt="img"  />

### 组件使用的注意点

#### 组件使用注意点1：解析 DOM 模板时

**HTML 元素，诸如 `<ul>、<ol>、<table>` 和  `<select>`，**

**对于哪些元素可以出现在其内部是有严格限制的：只能规定诸如 `<li>、<tr> `和 `<option>`元素出现在它的内部。**

**这会导致我们使用这些有约束条件的元素时遇到一些问题：自定义组件会被作为无效的内容提升到外部，并导致最终渲染结果出错。**

```vue
<div id="app">
    <table>
        <tbody>
            <row></row>
            <row></row>
            <row></row>
        </tbody>
    </table>
</div>

Vue.component('row', {
  template: '<tr><td>this is row</td></tr>'
})    

var app = new Vue({
    el: '#app'  
})
```

`<tr>在<table>外部：`

![img](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1652754013546-7503dbeb-f0ed-4582-b24c-3efe465e82db.jpeg)

特殊的 is 特性给了我们一个解决办法：

```vue
<div id="app">
    <table>
        <tbody>
            <tr is="row"></tr>
            <tr is="row"></tr>
            <tr is="row"></tr>
        </tbody>
    </table>
</div>

Vue.component('row', {
  template: '<tr><td>this is row</td></tr>'
})    
var app = new Vue({
    el: '#app'  
})
```

`<tr>在<table>内部：`

![img](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1652754070071-4d7d1c85-f438-46dd-a71c-33dd71b7e499.jpeg)

**如果是从以下来源使用模板的话，这条限制是不存在的：**

**● 字符串 (例如：template: '...')**

**● 单文件组件，即 SFC (.vue)**

**● `<script type="text/x-template">`**

#### 组件使用注意点2：非根组件的 data 必须是一个函数

一个非根组件的 data 选项必须是一个函数，因为这样，每个实例可以维护一份被返回对象的独立的拷贝：

```vue
// 定义一个名为 button-counter 的新组件 
Vue.component('button-counter', { 
    data: function () { 
        return { 
            count: 0 
        } 
    }, 
    template: '<button v-on:click="count++">You clicked me {{ count }} times.</button>' 
})
```

如果 Vue 没有这条规则，点击一个按钮就会影响到其它所有按钮实例：

![img](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1652754181898-febe7de8-7b65-479d-8119-7c48b9f7f066.jpeg)

**为什么 Vue 要求 非根组件的 data 必须是一个函数？**

如果 data 不是函数而是对象，所有组件实例引用会是同一个 data 对象，保存的是相同的内存地址；

如果 data 是函数每次返回一个新的 data 对象的话，保存的是不同的内存地址，所有组件实例就不会互相影响。

Vue 根组件没有此限制的原因是在一个 Vue 项目中，根组件不会出现被复用的情况，因此不要求根组件的 data 一定是一个返回对象的函数

#### 组件使用注意点3：单个根元素

组件的 template 里，每个组件必须只有一个根元素（即 template 里元素超过一个时必须有个根元素）

#### 组件使用注意点4：单向数据流（子组件不可以修改父组件传的值）

为了避免影响到其他组件 `<counter>`

父组件 app 可以随意地向子组件 counter 传递参数，也可以修改要传递的参数的值

但是子组件 counter 不可以（要避免）修改父组件 app 传过来的 props: ['count'] 参数值，只能由父组件 app 修改，

如果子组件和父组件可以同时修改，会产生代码混乱

props: ['count'] 参数值 只负责展示

如果实在要改变父组件的 prop 值，

可以在子组件 data 里面定义一个变量并用子组件 prop 的值初始化它，之后用 $emit 通知父组件去修改

例如，下面代码不可以在 handleClick 修改 count，错误提示 props 的 count 被修改

```vue
<div id="app">
    <counter :count="0"></counter>
    <counter :count="0"></counter>
</div>

var counter = {  
    props: ['count'],
    template: "<div @click='handleClick'>{{number}}</div>",
    methods: {
        handleClick: function () {
            this.count++;  
        }
    }
}

var app = new Vue({
    el: '#app',
    components: {
        counter: counter
    }
})
```

![img](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1652754474210-726caf4d-79b6-4c5e-aa75-cb89630fa9b3.jpeg)

解决办法是子组件使用 data 作为函数，克隆出一个 count 副本，使用自己的 number

```vue
<div id="app">
    <counter :count="0"></counter>
    <counter :count="0"></counter>
</div>

var counter = {  
    props: ['count'],
    data: function () {
        return {
            number: this.count
        }
    },
    template: "<div @click='handleClick'>{{number}}</div>",
    methods: {
        handleClick: function () {
            this.number++;  
        }
    }
}

var app = new Vue({
    el: '#app',
    components: {
        counter: counter
    }
})
```

这样，控制台就不会报错了：

![img](https://cdn.nlark.com/yuque/0/2022/gif/1614731/1652754752313-4c6b0f7e-9fe6-49a4-bf8f-90e994627d96.gif)

#### 组件使用注意点5：子组件向父组件传值（$emit 可以传多个参数）

```vue
<div id="app">
    <counter :count="0" @inc="handleIncrease"></counter> // <counter>子组件接收 inc 事件，然后 inc 事件会继续向外传播，交给 app 父组件的 handleIncrease 事件处理
    <counter :count="0" @inc="handleIncrease"></counter>
    <div>{{total}}</div>
</div>

var counter = {  
    props: ['count'],
    data: function () {
        return {
            number: this.count
        }
    },
    template: "<div @click='handleClick'>{{number}}</div>",
    methods: {
        handleClick: function () {
            this.number = this.number + 2;
            this.$emit('inc', 2, 'plus 2');  // <counter>子组件向外触发 inc 事件，表示我己经增加了，然后传递两个参数：2 和 'plus 2'
        }
    }
}

var app = new Vue({
    el: '#app',
    data: {
        total: 0
    },
    components: {
        counter: counter
    },
    methods: {
        handleIncrease: function (step, thing) { // 两个参数：2 和 'plus 2' ，分别赋给 step 和 thing
            console.log(thing);
            this.total += step;
        }
    }
})
```

![img](https://cdn.nlark.com/yuque/0/2022/gif/1614731/1652754793056-d7387b1a-eb06-4a57-86a8-aebace7a6833.gif)

#### 组件使用注意点6：ref 表示赋予一个DOM元素节点或子组件的引用别名，方便以后父组件直接访问该元素

$refs 是对象类型，默认是一个空的对象 必须要在组件上加上 ref="xxx"

```vue
<div id="app">
    <div ref="greet" @click="handleClick">
      hello world!
    </div>
</div>

var app = new Vue({
    el: '#app',
    methods: {
        handleClick: function () {
            alert(this.$refs.greet.innerText);
        }  
    }
})
```

![img](https://cdn.nlark.com/yuque/0/2022/gif/1614731/1652754879591-5d2a6ce5-9942-44e3-996d-0af6405d5ee5.gif)

```vue
<div id="app">
    <counter ref="numOne"  @change="handleChange"></counter>
    <counter ref="numTwo"  @change="handleChange"></counter>
    <div>{{total}}</div>
</div>

Vue.component('counter', {
    data: function () {
        return {
            number: 0
        }
    },
    template: '<div @click="handleClick">{{number}}</div>',
    methods: {
        handleClick: function () {
            this.number++;
            this.$emit('change');
        }
    }
})  

var app = new Vue({
    el: '#app',
    data: {
        total: 0
    },
    methods: {
        handleChange: function () {
            this.total = this.$refs.numOne.number + this.$refs.numTwo.number;
        }
    }
})
```

![img](https://cdn.nlark.com/yuque/0/2022/gif/1614731/1652754913761-d1382b8a-f2e8-4669-83eb-8b7cd57862e4.gif)

`ref` 被用来给元素或子组件注册引用信息。引用信息将会注册在父组件的 `$refs` 对象上。

如果在普通的 DOM 元素上使用，引用指向的就是 DOM 元素；

如果用在子组件上，引用就指向组件实例：

```vue
<!-- `vm.$refs.p` will be the DOM node -->
<p ref="p">hello</p>

<!-- `vm.$refs.child` will be the child component instance -->
<child-component ref="child"></child-component>
```

**当 `v-for` 用于元素或组件的时候，引用信息将是包含 DOM 节点或组件实例的数组。**

关于 ref 注册时间的重要说明：

因为 ref 本身是作为渲染结果被创建的，在初始渲染的时候你不能访问它们 - 它们还不存在！

`$refs` 也不是响应式的，因此你不应该试图用它在模板中做数据绑定。

## 组件通信

### 父子组件通信

<img src="https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1652755004809-d4d5ef81-55d0-42e2-8ffe-0ab2d969e093.jpeg" alt="img"  />

**应用场景**

![img](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1652755018521-d47f1b7c-75cb-4d93-bab1-65a40043b3cc.jpeg)

<img src="https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1652755018504-14955541-1f85-44ec-adef-09cc01cdd5fb.jpeg" alt="img"  />

<img src="https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1652755018565-a28a1d97-c9f3-4afb-8a8f-57afb9cef733.jpeg" alt="img" style="zoom: 67%;" />

#### 父组件向子组件传值

props（properties 属性的简写）

[Prop 的使用](https://v2.cn.vuejs.org/v2/guide/components-props.html)

<img src="https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1652755134269-73fdcc15-c127-41b6-b293-762c89a3841d.jpeg" alt="img"  />

#### 具体的父组件向子组件传值

```vue
<div id="app">
    <input type="text" v-model="inputVal">
    <button v-on:click="btnClick">提交</button>
    <ul>
        <!-- <li v-for="item of list" :key="item">{{item}}</li> -->
        <todo-item v-bind:content="item" v-for="item of list" ></todo-item>
    </ul>
</div>

Vue.component('TodoItem', {
    props: ['content'],
    template: '<li>{{content}}</li>'
})  
```

Vue.component('TodoItem')  是全局组件，在任何容器（Vue实例）里都可以使用

Vue.component('TodoItem')  === `<todo-item>`

相对于 `<div id="app">` 组件 来说， `<todo-item> `是它的一个子组件；

当子组件 `<todo-item> `使用 v-for，

想让 Vue.component('TodoItem')  的 `<template> `显示对应的内容，需要使用 v-bind: 获取 `<div id="app">`组件 data 的数据

通过属性绑定将 item 传递给 content，content 再传递给 Vue.component('TodoItem')

Vue.component('TodoItem') 使用 props: ['content'] 接收 `<todo-item>` 自定义特性 content ，

就可以像 使用自己的 data 属性 一样使用 `<div id="app">`组件 data 的数据

`<template>`可以显示接收的值了


就这样，完美地实现了 `<div id="app">` 父组件 传值给 `<todo-item>` 子组件

过程如下：

子组件 `<todo-item>` 通过 v-for 从父组件 app 的 data 中 将 list 数组中的元素 item 取出

子组件 `<todo-item>` 将 item 传给  v-bind: 的 content

v-bind: 的 content 接着传给 子组件 Vue.component 的 props['content']，子组件中访问这个 content，就像访问 data 中的 content 一样

Vue.component 的 template 使用 

```js
{{content}} 
```

渲染出 `<todo-item>` 的内容

```vue
<div id="app">
    <input type="text" v-model="inputVal">
    <button v-on:click="btnClick">提交</button>
    <ul>
        <!-- <li v-for="item of list" :key="item">{{item}}</li> -->
        <todo-item v-bind:content="item" v-for="item of list" ></todo-item>
    </ul>
</div>

Vue.component('TodoItem', {
    props: ['content'],
    template: '<li>{{content}}</li>'
})    

var app = new Vue({
    el: '#app',
    data: {
        inputVal: '',
        list: []
    },
    methods: {
        btnClick: function () {
            this.list.push(this.inputVal);
            this.inputVal = ''
        }
    }
})
```

![img](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1652756274239-d30e82af-4606-463e-bf91-8da939daa6e6.jpeg)

**注意：如果不使用 v-bind，结果是没有冒号 : 的 content="item"，content 特性寻找并接收的就是字符串 item 而不是变量 item，**

**再次重复，静态 prop 与动态 Prop 的区别，加深理解！**

#### 父传子

```vue
<div id="app">
  <cpn :number1="num1" :number2="num2"></cpn>
</div>

<template id="cpn">
  <div>
    <h2>{{number1}}</h2>
    <h2>{{number2}}</h2>
  </div>
</template>

const app = new Vue({
  el: '#app',
  data: {
    num1: 1,
    num2: 2
  },
  components: {
    cpn: {
      template: '#cpn',
      props: {
        number1:  Number,
        number2:  Number
      }
    }
  }
})
```

从父组件 app 的 data 中取出 num1，num1 传给注册的子组件`<cpn>`，`<cpn>`子组件上通过 v-bind 传递 num1 到自定义特性 number1

`<cpn>` 子组件 用 props 属性接收 number1，最后以 

```js
{{number1}} 
```

的形式在 template 里使用

父组件 data -> v-bind ->子组件 props 

![img](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1652756362595-931112e5-66f4-4a2e-9bad-a5b8e11c12c0.jpeg)

#### 父传子 与 v-model

```vue
<div id="app">
  <cpn :number1="num1" :number2="num2"></cpn>
</div>

<template id="cpn">
  <div>
    <h2>props:{{number1}}</h2>
    <h2>data:{{dataNumber1}}</h2>
    <!-- <input type="text" v-model="number1">Vue不推荐子组件修改props -->
    <input type="text" v-model="dataNumber1">
    <!-- <input type="text" v-model="number2">Vue不推荐子组件修改props -->
    <h2>props:{{number2}}</h2>
    <h2>data:{{dataNumber2}}</h2>
    <input type="text" v-model="dataNumber2">   
  </div>
</template>

const app = new Vue({
  el: '#app',
  data: {
    num1: 1,
    num2: 2
  },
  components: {
    cpn: {
      template: '#cpn',
      props: {
        number1:  Number,
        number2:  Number
      },
      // 因为子组件里要避免直接修改父组件传的值，所以 v-model 只能绑定 dataNumber1 和 dataNumber2
      data() {
        return {
          dataNumber1: this.number1,
          dataNumber2: this.number2
        }
      }
    }
  }
})
```

![img](https://cdn.nlark.com/yuque/0/2022/gif/1614731/1652756485819-aeebf1e4-99e4-4948-b541-b6916f0bd369.gif)

注意：

Vue 不推荐在子组件的模板里修改自己的 props，只推荐父组件来修改 props，

如果两个地方都修改的话，props 值会乱套，变得不可预测（再次重复，加深印象）

![img](https://cdn.nlark.com/yuque/0/2022/png/1614731/1652756515325-9b21ff7a-b2b9-46bf-a1cb-a59a4892d1c0.png)

**总结：**

**父组件 将自己的值 利用 v-bind 绑定到自定义特性，子组件通过 props 接收 自定义特性，** 

**完成父组件向子组件传值（需要的数据），props 数据与 data 数据的使用方式一样（用在 template 和 script 时）**

### props 的校验 与 非 props 特性

#### props 的校验 

props 验证在开发一个会被别人用到的组件时尤其有帮助。

为了定制 props 的验证方式，可以为 props 中的值提供一个带有验证需求的对象，而不是一个字符串数组。

props 验证时会先接收这个属性（验证时顺便接收，一举两得）

![img](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1652756831889-f4109e40-d34f-4d66-b7a1-b8feaefdaa39.jpeg)

props 特性

当父组件标签向子组件传递自定义属性时，子组件的 props 有对应的属性接收，这就是 props 特性。

它的特点是：

1、不会在 DOM节点 中显示特性；

2、可以在子组件中通过 

```js
{{content}} 
```

或 this.content 的方式取得 content 的内容

例如：

```vue
<div id="app">
    <child content="hello"></child>
</div>

Vue.component('child', {
    props: {
        content: {
            // content可以是多个可能的类型(`null` 和 `undefined` 会通过任何类型验证)
            type: [Number, String],
            // content必须有值
            required: true,
            // 当没有传值时content的默认值为'default value'
            default: 'default value',
            // 自定义验证函数，content的值长度必须大于3
            validator: function (value) {
                return (value.length > 3);
            }  
        }
    },
    template: '<div>{{content}}</div>'
})

var app = new Vue({
    el: '#app'
})
```

![img](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1652756885029-9c7ca71b-a57e-4545-883d-9131baeb92a3.jpeg)

验证失败的时候，(开发环境构建版本的) Vue 将会产生一个控制台的警告。

#### 非 props 特性（透传 attribute，剩余 props）

**当父组件向子组件传递自定义特性时，子组件没有 props 接收，这就是非 props 特性。**

**这时，子组件可以接受任意个非 props 的 attribute，而这些 attribute 会被添加到子组件的根元素上。**

例如，想象一下你通过一个 Bootstrap 插件使用了一个第三方的 `<bootstrap-date-input>` 组件，

这个插件需要在其 `<input>` 上用到一个 `data-date-picker` attribute。我们可以将这个 attribute 添加到你的组件实例上：

```html
<bootstrap-date-input data-date-picker="activated"></bootstrap-date-input>
```

然后**这个 `data-date-picker="activated"` attribute 就会自动添加到 `<bootstrap-date-input>` 的根元素上。**

##### 替换/合并已有的 Attribute

假如 `<bootstrap-date-input>` 的模板是这样的：

```html
<input type="date" class="form-control">
```

为了给我们的日期选择器插件定制一个主题，我们可能需要像这样添加一个特别的类名：

```html
<bootstrap-date-input
  data-date-picker="activated"
  class="date-picker-theme-dark"
></bootstrap-date-input>
```

**在这种情况下，我们定义了两个不同的 `class` 的值：**

- **`form-control`，这是在组件的模板内设置好的**
- **`date-picker-theme-dark`，这是从组件的父级传入的**

**对于绝大多数 attribute 来说，从外部提供给组件的值会替换掉组件内部设置好的值。**

**如果传入 `type="text"` 就会替换掉 `type="date"` 并把它破坏！**

**`class` 和 `style` attribute 会稍微智能一些，即两边的值会被合并起来，从而得到最终的值：`form-control date-picker-theme-dark`。**

##### 禁用 Attribute 继承

**如果不希望组件的根元素继承 attribute，可以在组件的选项中设置 `inheritAttrs: false`。**

例如：

```js
Vue.component('my-component', {
  inheritAttrs: false,
  // ...
})
```

**这尤其适合配合实例的 `$attrs` property 使用，该 property 包含了传递给一个组件的 attribute 名和 attribute 值**，

例如：

```js
{
  required: true,
  placeholder: 'Enter your username'
}
```

**有了 `inheritAttrs: false` 和 `$attrs`，就可以手动决定这些 attribute 会被赋予哪个元素。**

```js
Vue.component('base-input', {
  inheritAttrs: false,
  props: ['label', 'value'],
  template: `
    <label>
      {{ label }}
      <input
        v-bind="$attrs"
        v-bind:value="value"
        v-on:input="$emit('input', $event.target.value)"
      >
    </label>
  `
})
```

**注意 `inheritAttrs: false` 选项不会影响 `style` 和 `class` 的绑定。**

这个模式允许在使用基础组件的时候更像是使用原始的 HTML 元素，而不会担心哪个元素是真正的根元素：

```html
<base-input
  label="Username:"
  v-model="username"
  required
  placeholder="Enter your username"
></base-input>
```

相当于绑定到组件内部：

```html
<label>
  {{ label }}
  <input
    required
    placeholder="Enter your username"
    v-bind:value="value"
    v-on:input="$emit('input', $event.target.value)"
   >
</label>
```

因为 label 和 value 明确有 props 接收，所以不会绑定到 `<input>` 上

例如：

```vue
<div id="app">
    <child content="hello"></child>
</div>

Vue.component('child', {
    template: '<div>{{content}}</div>'
})

var app = new Vue({
    el: '#app'
})
```

它的特点是：

1、报错：content 属性未定义，无法使用；

2、content 属性会显示在DOM节点中

![img](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1652756933714-d9a9436e-aadf-4621-a9e8-474e2f906fa2.jpeg)

![img](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1652756933745-234bec91-af6d-4762-ba65-14dfc4ecf72a.jpeg)

### 子组件通过 事件绑定 和 $emit 向 父组件 传值

![img](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1652756986063-a788617a-930a-49d3-abe5-0ce35df92ab1.jpeg)

<img src="https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1652756986210-ec877b34-9657-43c7-a540-e965d67efbc8.jpeg" alt="img" style="zoom:67%;" />

<img src="https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1652756986185-968f9849-0074-459e-b40b-9eea9d4ee316.jpeg" alt="img"  />

![img](https://cdn.nlark.com/yuque/0/2022/png/1614731/1669384582901-649c4af7-6067-4299-a584-9aca04ecdf5a.png)

#### 具体的子组件向父组件传值

在后面的 todo 小案例里

因为数据放在父组件里，父组件决定子组件到底显示多少条数据，

所以删除子组件时我们需要点击子组件，子组件把对应的值传给父组件，让父组件去改变数据

父组件数据改变了，因为 v-for，子组件就会消失或者增加

在父组件的模板中创建子组件的同时也监听子组件删除事件，一旦删除事件被触发就执行 handleItemDelere

例子

```vue
<div id="app">
    <input type="text" v-model="inputVal">
    <button @click="btnClick">提交</button>
    <ul>
        <!-- <li v-for="item of list" :key="item">{{item}}</li> -->
        <todo-item :content="item"
                   :index="index"
                   v-for="(item, index) of list"
                   :key="item"
                   @delete="handelItemDelete">
        </todo-item>
    </ul>
</div>

var TodoItem = {
    props: ['content', 'index'],
    template: '<li @click="handleItemClick">{{content}}</li>',
    methods: {
        handleItemClick: function () {
            this.$emit('delete', this.index); // 发射自定义事件
        }
    }
}

var app = new Vue({
    el: '#app',
    components: {
        TodoItem: TodoItem
    },
    data: {
        inputVal: '',
        list: []
    },
    methods: {
        btnClick: function () {
            this.list.push(this.inputVal);
            this.inputVal = '';
        },
        handelItemDelete: function (index) {
            this.list.splice(index, 1);
        }
    }
})
```

过程如下：

子组件 TodoItem 组件构造器对象在 template 里的 `<li>` 元素监听 click 事件，触发自己的 handleItemClick 原生事件，

在 handleItemClick 事件里，通过 $emit 发射出自定义的 delete 事件并把参数 this.index 传给 `<todo-item>`，

`<todo-item>` 将 delete 事件和参数 this.index 继续向上传播，交给父组件 app 的 handelItemDelete 处理。

子组件 @click => handleItemClick => $emit 发射 delete 事件 => 父组件 @delete 接收 delete 事件 => handleDelete 

$emit 表示触发当前实例上的事件，附加的参数都会传给监听器回调。

Todo 的删除操作完成

![img](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1652757078213-e7c77035-7a23-420c-b089-71d8619381db.jpeg) 

子组件的 template 里某个元素绑定事件，事件触发子组件 methods 的方法，子组件 methods 的方法通过 $emit 发射自定义事件和参数

父组件的 template 使用子组件时，通过 v-on 接收并监听子组件的自定义事件 ，

触发父组件 的 methods ，子组件的自定义事件和参数 交给 父组件 的 methods 处理

这里的父组件是 app

注意：

@delete="handelItemDelete"  event 事件对象不会作为 handelItemDelete 的第一个参数传过去，

$emit 附加的参数将作为第一个参数传入事件处理函数 handelItemDelete 。

父级组件监听这个事件的时候，可以通过 $event 访问到 $emit 附加的参数

#### 子传父

```vue
<div id="app">
  <cpn @item-click="handleClick"></cpn>
</div>  

<template id="cpn">
  <div>
    <button v-for="item in categories" @click="btnClick(item.name)">{{item.name}}</button>
  </div>
</template>


const cpn = {
  template: '#cpn',
  data() {
    return {
      categories: [
        {id: '1', name: '热门推荐'},
        {id: '2', name: '手机数码'},
        {id: '3', name: '家用家电'},
        {id: '4', name: '电视办公'}
      ]
    }
  },
  methods: {
    btnClick(name) {
      this.$emit('item-click', name)
    }
  }
}

const app = new Vue({
  el: '#app',
  methods: {
    handleClick(name) {
      console.log('clicked', name)
    }
  },
  components: {
    cpn
  }
})
```

![img](https://cdn.nlark.com/yuque/0/2022/gif/1614731/1652757143422-4b28e293-9042-42fb-a91d-a45149b5f038.gif)

#### 子传父 与 v-model

```vue
<div id="app">
  <cpn
    :number1="num1"
    :number2="num2"
    @num1-change='num1Change'
    @num2-change='num2Change'>
  </cpn>
</div>

<template id="cpn">
  <div>
    <h2>父组件的data和子组件的props:{{number1}}</h2>
    <h2>子组件的data:{{dataNumber1}}</h2>
    <input type="text" :value="dataNumber1" @input="num1Input">
    <h2>父组件的data和子组件的props:{{number2}}</h2>
    <h2>子组件的data:{{dataNumber2}}</h2>
    <input type="text" :value="dataNumber2" @input="num2Input">   
  </div>
</template>

const app = new Vue({
  el: '#app',
  data: {
    num1: 1,
    num2: 2
  },
  methods: {
    num1Change(val) {
      // $emit默认传的参数类型为string
      this.num1 = parseFloat(val)
    },
    num2Change(val) {
      this.num2 = parseFloat(val)
    }
  },
  components: {
    cpn: {
      template: '#cpn',
      props: {
        number1:  Number,
        number2:  Number
      },
      // 因为子组件里要避免直接修改父组件传的值，所以 v-model 只能绑定 dataNumber1 和 dataNumber2
      data() {
        return {
          dataNumber1: this.number1, // 子组件的 data 通过 props 获得父组件的 data
          dataNumber2: this.number2
        }
      },
      methods: {
        // 改变父组件和子组件的 data
        num1Input(event) {
          this.dataNumber1 = event.target.value;       // 输入框1 子组件 cpn 的 dataNumber1 为输入的值 （输入框1 的value） 
          this.$emit('num1-change', this.dataNumber1); // 输入框1 父组件 app 的 num1 是 dataNumber1 的 parseFloat         
          this.dataNumber2 = this.dataNumber1 * 100;   // 输入框2 子组件 cpn 的 dataNumber2 为 dataNumber1 的 100倍
          this.$emit('num2-change', this.dataNumber2)  // 输入框2 父组件 app 的 num2 是 dataNumber2 的 parseFloat
        },
        num2Input(event) {
          this.dataNumber2 = event.target.value;        // 输入框2 子组件 cpn 的 dataNumber2 为输入的值（输入框2 的value）
          this.$emit('num2-change', this.dataNumber2);  // 输入框2 父组件 app 的 num1 是 dataNumber2 的 parseFloat
          this.dataNumber1 = this.dataNumber2 / 100;    // 输入框1 子组件 cpn 的 dataNumber1 为 dataNumber2 的 1 / 100
          this.$emit('num1-change', this.dataNumber1)   // 输入框1 父组件 app 的 num2 是dataNumber2 的 parseFloat
        }
      }
    }
  }
})
```

![img](https://cdn.nlark.com/yuque/0/2022/gif/1614731/1652757251210-ced453a6-43a3-4ab4-b02e-ebf9322d2699.gif)

![img](https://cdn.nlark.com/yuque/0/2022/png/1614731/1652757267587-e61228f1-3f17-4863-843b-de0d925f1383.png)

另一种写法

```vue
<div id="app">
  <cpn
    :number1="num1"
    :number2="num2"
    @num1-change='num1Change'
    @num2-change='num2Change'>
  </cpn>
</div>

<template id="cpn">
  <div>
    <h2>父组件的data和子组件的props:{{number1}}</h2>
    <h2>子组件的data:{{dataNumber1}}</h2>
    <input type="text" v-model="dataNumber1">
    <h2>父组件的data和子组件的props:{{number2}}</h2>
    <h2>子组件的data:{{dataNumber2}}</h2>
    <input type="text" v-model="dataNumber2">   
  </div>
</template>

const app = new Vue({
  el: '#app',
  data: {
    num1: 1,
    num2: 2
  },
  methods: {
    num1Change(val) {
      // $emit 默认传的参数类型为 string
      this.num1 = parseFloat(val)
    },
    num2Change(val) {
      this.num2 = parseFloat(val)
    }
  },
  components: {
    cpn: {
      template: '#cpn',
      props: {
        number1:  Number,
        number2:  Number
      },
      // 因为子组件里要避免直接修改父组件传的值，所以 v-model 只能绑定 dataNumber1 和 dataNumber2
      data() {
        return {
          dataNumber1: this.number1,
          dataNumber2: this.number2
        }
      },
      watch: {
        dataNumber1(newValue) {
          this.dataNumber2 = newValue * 100;  // newValue 为 dataNumber1 的 newValue
          this.$emit('num1-change', newValue)
        },
        dataNumber2(newValue) {
          this.dataNumber1 = newValue / 100;  // newValue 为 dataNumber2 的 newValue
          this.$emit('num2-change', newValue)
        }
      }
    }
  }
})
```

```js
watch: {
  name(newValue, oldValue) {
  }
}
```

### 非父子组件的传值 （Bus / 总线 / 发布订阅模式）

一个组件通过 事件绑定 和 bus.$emit、bus.$on 给另一个组件（使用 bus.$on 接收）传值

eventBus 又称为事件总线（发布/订阅模式），通过一个空的 Vue 实例作为中央事件总线，用它来发送事件和接收事件

eventBus 就像是所有组件共用的事件中心，其他组件可以向该中心订阅事件或接收事件， eventBus 可以通知其他组件

巧妙而轻量地实现了任何组件间的通信，包括父子、兄弟、跨级

当项目较大时，eventBus 容易造成难以维护的灾难，可以选择更好的状态管理解决方案 vuex 。

![img](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1652758099876-59de64b0-8690-481c-8193-f64ad2718719.jpeg)

现实生活中的发布-订阅模式：

小红最近在淘宝网上看上一双鞋子，但是联系到卖家后，发现这双鞋卖光了，

但是小红对这双鞋又非常喜欢，所以联系卖家，问卖家什么时候有货，

卖家告诉她：“要等一个星期后才有货，要是你喜欢的话，你可以收藏我们的店铺，等有货的时候再通知你”，所以小红收藏了此店铺；

但与此同时，小明，小花也喜欢这双鞋，也收藏了该店铺；

卖家等来货的时候就依次会通知他们；

上面的故事就是一个典型的发布订阅模式，卖家是属于发布者，小红，小明，小花属于订阅者

卖家作为发布者，当鞋子到了的时候，会依次通知 小红，小明，小花，依次使用旺旺等工具给他们发布消息。

1、小红，小明，小花不需要天天逛淘宝网看鞋子到了没有，

在合适的时间点，发布者（卖家）有货的时候，会通知该订阅者(小红，小明，小花)

2、订阅者不管发布者如何改变，只监听发布者的事件名，只要发布者的事件名不变

卖家（发布者）只需要将鞋子来货的这件事告诉订阅者(买家)，

不管买家到底买还是不买，还是买其他卖家的，只要鞋子到货了就通知订阅者即可

现在的发布订阅模式中，称为发布者的消息发送者不会将消息直接发送给订阅者，这意味着发布者和订阅者不知道彼此的存在。

在发布者和订阅者之间存在第三个组件，称为调度中心或事件通道，

它维持着发布者和订阅者之间的联系，过滤所有发布者传入的消息并相应地分发它们给订阅者。

举一个例子，你在微博上关注了A，同时其他很多人也关注了A，那么当A发布动态的时候，微博就会为你们推送这条动态。

A就是发布者，你是订阅者，微博就是调度中心，你和A是没有直接的消息往来的，全是通过微博来协调的

**发布订阅模式与观察者模式的区别**

![img](https://cdn.nlark.com/yuque/0/2022/png/1614731/1652758108255-00230fe2-1334-4e6b-83b9-cb10595c37d7.png)

$on(eventName, eventHandler) 监听一个事件，因为不确定何时会触发事件，一般会在 mounted 或 created 钩子中来监听。

问题：当一个 child 组件点击时，另一个 child 组件的内容变成该组件的内容（即两个兄弟组件间传值）

```vue
<div id="app">
    <child content="hello"></child>
    <child content="world"></child>
</div>

// 正常的 bus，一般会挂载一个空的 Vue 对象到 Vue 的 prototype 上，方便全局调用。
// 让每个 Vue 实例都有一个 bus
Vue.prototype.bus = new Vue(); 
 
Vue.component('child', {
    props: {
        content: String
    },
    data: function () {
        return {
            selfContent: this.content
        }
    },
    template: '<div @click="handleClick">{{selfContent}}</div>',
    methods: {
        handleClick: function () {
            this.bus.$emit('change', this.selfContent);
        }
    },
    mounted: function () {
        var this_ = this; 
        this.bus.$on('change', function (msg) {            
            this_.selfContent = msg; // 如果不用 this_ 存 child 实例，this 会指向 bus
        })
    }
})

var app = new Vue({
    el: '#app'
})
```

过程：

selfContent 为两个 child 组件各自 content 特性的值 

点击一个 child 组件 ---> @click 触发 handleClick 事件 ---> handleClick $emit change 事件 ---> 

点击的 child 组件作为 bus 使用 $emit('change', 参数) 专门发布 change 事件并携带数据内容 selfContent

--->  两个 child 组件作为两个 bus 使用 $on('change', 事件处理函数) 专门接收 change 事件和 数据内容 selfContent

bus 就是一个有话筒和收音机的电台

$emit 是一部专门广播新闻的话筒，只有点击的那个 child 组件（主播）才有

$on 是一部专门收听新闻的收音机，在这个例子里，两个 child 组件（主播和听众）都有

![img](https://cdn.nlark.com/yuque/0/2022/gif/1614731/1652758179870-2a3ce46b-5811-4909-852e-109dab988f6d.gif)

1、建立事件总线

```js
Vue.prototype.bus = new Vue();
```

2、两个组件的 methods 发布事件（其中一个组件作发布者）

```js
this.bus.$emit('change', this.selfContent)
```

这句的意思是点击的组件作为 bus 事件中心发布  change 事件 携带 selfContent 内容

3、两个组件的 mounted 或 created 钩子中 接收事件（两个组件作订阅者）

```js
var this_ = this;
this.bus.$on('change', function (msg) {            
    this_.selfContent = msg; // 如果不用this_存实例，this会指向bus
})
```

这句的意思是两个组件监听（接收）bus 事件中心发布的 msg 内容（即 selfContent）

**为什么出现两次弹窗？**

bus 是一个 new Vue 的实例，所以  bus 上有 $emit 和 $on。

其中一个 child 组件触发 change 事件时，两个 child 组件通过 $on 对同一个 change 事件进行监听 ，

监听到该事件后，两个组件都会弹出 change 事件所传递过来的内容

#### $emit 和 $on

$on 监听并定义事件

$emit 发布并触发事件

使用 $emit 和 $on 的好处是可以将事件的定义和触发分开，实现逻辑的解耦

使用需注意 this 指向：https://blog.csdn.net/qq_41694291/article/details/106976409

如果所传入的回调函数是普通函数，那么函数内的 this 将指向 bus 实例，而不是注册事件的那个组件实例，

使用箭头函数时不会有这个问题，它内部的 this 指向当前组件实例

可以在子组件中直接调用

```vue
<div id="root">
  <button @click="boost">触发事件</button>
</div>

new Vue({
  el: '#root',
  data() {
    return {
      message: 'hello vue'
    }
  },
  created() {
    this.$on('my_events', this.handleEvents)
    this.$on('my_events', this.handleEvents2)
  },
  methods: {
    handleEvents(e) {
      console.log(this.message, e)
    },
    handleEvents2(e) {
      console.log('handleEvents2', e)
    },
    boost() {
      this.$emit('my_events', 'my params')            
    }
  }
})
```

@click bootst => $emit('my_events', 'my params') => $on('my_events', this.handleEvents) => handleEvents(e) 

handleEvents 前半部分打印 this.message，即 hello vue

handleEvents 后半部分打印参数 e ，即 my params

**$on 先定义的事件先触发，后定义的事件后触发**（具体调试时看源码即可）

<img src="https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1652758375960-e55e1e10-d4fb-484d-99a2-8d41935782b8.jpeg" alt="img" style="zoom:67%;" />

#### 组件通信 provide() 和 inject()

```vue
<div id="root">
  <Test></Test>
</div>

function registerPlugin() {
  Vue.component('Test', {
    template: '<div>{{message}}<Test2 /></div>',
    provide() {
      return {
        elTest: this
      }
    }, // function 的用途是为了获取运行时环境，否则 this 将指向 window
    data() {
      return {
        message: 'message from Test'
      }
    },
    methods: {
      change(component) {
        this.message = 'message from ' + component
      }
    }
  })

  Vue.component('Test2', {
    template: '<Test3 />'
  })

  Vue.component('Test3', {
    template: '<button @click="changeMessage">change</button>',
    inject: ['elTest'],
    methods: {
      changeMessage() {
        this.elTest.change(this.$options._componentTag)
      }
    }
  })
}

Vue.use(registerPlugin);

new Vue({
  el: '#root'
})
```

**效果图**

![img](https://cdn.nlark.com/yuque/0/2022/gif/1614731/1652773535056-9e77dcf1-3ef2-424c-b3da-98ee9a720ad5.gif)

#### 访问父组件和根组件（很少使用，耦合度高造成组件复用性差）

$parent 访问父组件

$root 访问根组件

<img src="https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1652758469702-fabe65d5-c3ac-4d54-921c-fb2342f18ad2.jpeg" alt="img" style="zoom: 67%;" />

## 生命周期函数

好比一个人出生到老去，每个阶段都会做一些事，如：学说话、上学、工作、退休

### 生命周期执行顺序

生命周期(父子组件)

示例：

<img src="https://cdn.nlark.com/yuque/0/2022/png/1614731/1652758602610-4d3ca5fa-4c8a-49c8-8f37-ee6fc17e0eee.png" alt="img" style="zoom:50%;" />

**before 的都是先父后子，**

**非 before 的只有创建是先父后子，挂载、更新和销毁是先子后父**

1. 加载渲染过程

父 beforeCreate -> 父 created  -> 父 beforeMount -> 子 beforeCreate -> 子created ->子 beforeMount -> 子 mounted -> 父 mounted

2. 子组件更新过程

父 beforeUpdate -> 子 beforeUpdate -> 子 updated -> 父 updated

3. 父组件更新过程

父 beforeUpdate -> 父updated

4. 销毁过程

父 beforeDestroy -> 子 beforeDestroy -> 子 destroyed -> 父 destroyed

**创建挂载更新销毁顺序是：**

父组件创建完成 -> 子组件创建完成 ->

子组件渲染完成 -> 父组件渲染完成 ->

子组件更新数据 -> 父组件更新数据 -> 

子组件销毁完成 -> 父组件销毁完成

### 生命周期使用注意

**创建前/后：** 

beforeCreate 阶段：

vue 实例的挂载元素 el 和数据对象 data 都为 undefined，还未初始化。

data、methods、computed、watch 都没有，但是 $route 对象是存在的，可以根据路由信息进行重定向之类的操作。

created 阶段：

vue 实例的数据对象 data 有了，

可访问操作 data、methods、computed、watch 上的方法和数据，el  为 undefined，还未初始化。

**挂载前/后：**

beforeMount 阶段：

vue 实例的 el 和 data 都初始化了，但还是挂载之前为虚拟的 dom 节点，data.message 还未替换。  

render 函数首次被调用，模板编译，基本用不上这个生命周期

ssr 中不可用

mounted 阶段：

vue 实例挂载完成，data.message 成功渲染。（data 之间互相赋值可以在此进行，也可以监听 DOM 事件）

ssr 中不可用

**更新前/后：**

组件 data 更新变化时，触发 beforeUpdate 和 updated 方法。执行 diff 算法，比对改变是否需要触发 UI 更新。

beforeUpdate 中 data 数据还是旧的。

updated 可以配合 $nextTick 获取更新后的 DOM 。

不要在此时修改 data，否则会继续触发 beforeUpdate、updated 这两个生命周期，进入死循环！

**销毁前/后：**

beforeDestroy

data、methods、computed、watch 等都可用

移除 echarts 的所有鼠标事件、清空会话

destroyed

执行 destroyed 方法后，对 data 的改变不会再触发生命周期函数，data、methods、computed、watch 等都步可用

移除监听事件、销毁定时器、移除对dom的操作

销毁 EventBus 定义的事件

销毁 Vuex 中存储的数据

清空浏览器 localStorage 的某些变量



每个 Vue 实例在被创建时都要经过一系列的初始化过程——

例如，需要设置数据监听、编译模板、将实例挂载到 DOM 并在数据变化时更新 DOM 等。

这个过程中会运行生命周期函数。

生命周期函数（ 钩子）是Vue实例在某一个时间点会自动执行的函数，可以在不同阶段添加自己的代码，只会运行一次。

生命周期函数（ 钩子）的 this 上下文指向调用它的 Vue 实例。

生命周期函数不放在 methods 里。

### created 与 mounted 区别

**created：**

实例创建完成后被立即调用，vue 实例已经被初始化，但是还没有挂载至 $el 上。

主要是用来初始化数据，ajax 请求最好放在 created 里面。

如果请求信息过多，页面会长时间处于白屏状态，DOM 节点没出来，无法操作 DOM 节点。

在 mounted 不会这样，这时使用 mounted 比较好，可以访问 this。

在 template 模板渲染成 html 前调用，即通常初始化某些属性值，然后再渲染成视图。

此时如果直接去操作 html 的 dom 节点，一定找不到相关的元素。

created() 钩子函数进行的 DOM 操作一定要放在 Vue.nextTick() 的回调函数中。

**mounted：**

vue 的 template 成功挂载在 $el 上。

dom 的操作要放在 mounted 里面，可以访问 this。

把 data 的数据 和 template 模板渲染成 html 后调用，通常是初始化页面完成后，再对 html 的 dom 节点进行一些需要的操作。



有改变 dom 的时候，就用 mounted 

没有改变 dom 操作，就用 created



### nextTick

在数据变化后要执行的某个操作，而这个操作需要使用随数据改变而改变的 DOM 结构的时候，

这个操作都应该放进 Vue.nextTick() 的回调函数中。

nextTick 相当于放进第一次事件循环的异步微任务队列，DOM 更新也在异步微任务队列中但是在 nextTick 之前，

实在不行就放进第二次事件循环的异步宏任务队列

```js
// 改变数据
vm.message = 'changed'

// 想要立即使用更新后的 DOM。这样不行，因为设置 message 后 DOM 还没有更新
console.log(vm.$el.textContent) // 并不会得到'changed'

// 这样可以，nextTick 里面的代码会在 DOM 更新后执行
Vue.nextTick(function(){
    console.log(vm.$el.textContent) // 可以得到'changed'
})
```

<img src="https://cdn.nlark.com/yuque/0/2022/png/1614731/1652758797030-54b53747-703a-4514-a010-7950cf7c34b2.png" alt="img" style="zoom: 80%;" />

事件循环：

第一个 tick（图例中第一个步骤，即'本次更新循环'，同步宏）：

1. 首先修改数据，这是同步任务。同一事件循环的所有的同步任务都在主线程上执行，形成一个执行栈，此时还未涉及 DOM 。

2. Vue 开启一个异步队列，并缓冲在此事件循环中发生的所有数据改变。如果同一个 watcher 被多次触发，只会被推入到队列中一次。

第二个 tick（图例中第二个步骤，即'下次更新循环'，异步微）：

同步任务执行完毕，开始执行异步 watcher 队列的任务，更新 DOM 。

Vue 在内部尝试对异步队列使用原生的 Promise.then 和 MessageChannel 方法，如果执行环境不支持，会采用 setTimeout(fn, 0) 代替。

第三个 tick（图例中第三个步骤，即'下次 DOM 更新循环结束之后'，异步宏）：

此时通过 Vue.nextTick 获取到改变后的 DOM 。通过 setTimeout(fn, 0) 也可以同样获取到。

官方文档说明：

注意 mounted 不会承诺所有的子组件也都一起被挂载。如果你希望等到整个视图都渲染完毕，可以用 vm.$nextTick 替换掉 mounted

```js
mounted: function () {
  this.$nextTick(function () {
    // Code that will run only after the
    // entire view has been rendered
  })
}
```

![img](https://cdn.nlark.com/yuque/0/2022/png/1614731/1652758862608-68f45339-b5ff-460d-82f9-b56fd88d341d.png)

![img](https://cdn.nlark.com/yuque/0/2022/png/1614731/1652758862656-76be613f-3659-4119-b410-16d93a890849.png)

当点击按钮一次时，打印出是 3 ，而不是 6，

这就说明了页面渲染完后需要 nextTick 来重新获取 DOM 节点才正确，才能获得正确的 childNodes.length

正确代码：

![img](https://cdn.nlark.com/yuque/0/2022/png/1614731/1652758870828-b268707a-193c-42fc-a3a5-b02d830b62d7.png)

这样保证了:

1. 异步渲染，$nextTick 待 DOM 渲染完再回调

2. 页面渲染时会将 data 的修改做整合，多次 data 修改只会渲染一次(就是如上图所示，你无论点击多少次按钮，增加了多少个 item ,然而 nextTick 就只执行一次)

注意：

不要在选项属性或回调上使用箭头函数，比如 created: () => console.log(this.a) 或 vm.$watch('a', newValue => this.myMethod())。

因为箭头函数并没有 this，this 会作为变量一直向上级词法作用域查找，直至找到为止。

Vue 的 8 个生命周期函数如下：

创建：beforeCreate，created

挂载：beforeMount，mounted

更新：beforeUpdate，updated

销毁：beforeDestroy，destroyed

除这 8 个核心钩子，另外还有 3 个新增功能型钩子，目前总共是 11 个钩子，顺带提一下这 3 个钩子的功能

1. 组件缓存，activated 与 deactivated，这两个钩子也是一对的，分别表示被 keep-alive 缓存的组件激活和停用时调用。

2. 组件错误捕获，errorCaptured，对组件中出现对异常错误进行处理，使用较少。

```vue
<div id="app"></div>

var app = new Vue({
    el: '#app',
    template: '<h1>{{text}}</h1>',
    data: {
        text: 'hello world'
    },
    beforeCreate: function () {
        console.log('beforeCreate');
    },
    created: function () {
        console.log('created');
    },
    beforeMount: function () {
        console.log(this.$el);
        console.log('beforeMount');
    },
    mounted: function () {
        console.log(this.$el);
        console.log('mounted');
    },
    beforeDestroy: function () {
        console.log('beforeDestroy');
    },
    destroyed: function () {
        console.log('destroyed');
    },
    beforeUpdate: function () {
        console.log('beforeUpdate');
    },
    updated: function () {
        console.log('updated');
    }
})
```

![img](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1652758977024-7669ca05-2ee1-4ec2-b9de-fd5e32600a2a.jpeg)

<img src="https://cdn.nlark.com/yuque/0/2022/png/1614731/1652758977044-881b4fe5-c015-4433-afb9-538340e36b18.png" alt="img" style="zoom: 33%;" />

![img](https://cdn.nlark.com/yuque/0/2022/png/1614731/1652758977356-a0cfffec-d32c-49d8-8084-6dcefa5e03dc.png)

## 组件小总结

都是操作数据，直接面向数据编程

**data：数据对象 相关的语法**

{{}}、v-text、v-html  

v-bind、class、style、v-model   

v-if、v-show

v-for、key

computed、watch

**methods ：事件处理方法，方法中的 this 自动绑定为 Vue 实例  相关的语法**

v-on 

$emit、$set

**Vue 实例的常用 option API 配置项**

1、el：挂载元素，确定接管的元素区域

2、template：字符串模板，会替换挂载的元素，如果不定义 template 就会把挂载元素下所有的内容作为模板

3、props：接收父组件传的值

4、data：数据绑定

5、computed：计算属性，一个属性根据data里依赖的数据重新计算，内置缓存，不会被其他无关数据的修改干扰而重新渲染

6、watch：侦听器，监测某个数据或计算属性发生的变化，一旦某个数据或计算属性发生变化就执行函数的内容

而 methods、computed和 watch也是对象，但是里面的属性都是实例的方法。

7、生命周期函数：某一个时间点会自动执行的函数

8、methods：事件绑定

9、Vue 实例的局部组件 components，属性和属性值同名，属性值是一个变量（引用一个对象），表示注册局部组件

```js
components: {
  counter
}
```

10、filters ：文本处理

## Vue2 通用模型

**template 里不需要写 this，script 里才需要**

data 里数据相互隔离，无法在 data 中通过一个数据直接改变其它数据，都是 undefined

**除了 el 、template， data 和 生命周期函数 可以是函数（方法）形式，其它一般都以对象形式来表示，**

**methods、computed、watch 里面都是嵌套一个方法**

```vue
<template>
  <div>
  </div>
</template>

<script>
import NavBar from 'common/navbar/NavBar';
import {itemListenerMixin, backUpMixin } from 'commonjs/mixin';
import {formatDate} from "commonjs/utils.js";

export default {
  name: "Detail",
  components: {
    NavBar
  },
  mixins: [xxxMixin, xxxMixin],
  data() {
    return {
      xxx: 0,
      xxx: ''
    }
  },
  props: {
    xxx: String,
	xxx: {
	  type: Object,
	  default() {
         return {}
      }
    },
    xxx: {
	  type: Number,
      default: 3000
    }  
  },
  computed: {
    xxxActive() {
      return this.$route.path.indexOf(this.path) !== -1
    },
    xxxStyle() {
      return this.isActive ? {color: this.activeColor} : {}
    }
  },  
  filters: {
    showDate(value) {
      let date = new Date(value*1000);
      return formatDate(date, 'yyyy-MM-dd')
    }
  },
  watch: {
    xxxInfo() {
      this.imgLength = this.detailInfo.detailImage[0].list.length;
    }
  },  
  methods: {
    xxx() {
      this.$emit('addToCart')
    },
    xxx() {
      this.$toast('对不起,此功能尚未开发',1500)
     }
  },
  created() {
    getDetailData(this.iid).then(res => {
      const data =  res.result;
      this.topImages = data.itemInfo.topImages;
    })
  },
  mounted() {
  },
  updated() {
  },
  destroyed() { 
  },  
  activated() {
  },
  deactivated () {
  } 
}  
</script>

<style>
</style>    
```

## Vue 中的插槽（slot）

<img src="https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1652770868563-c6c0c5d3-2864-4b1f-931d-818f78bd0a57.jpeg" alt="img" style="zoom: 80%;" />

<img src="https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1652770895308-528a2ea5-16c1-491f-b454-71a078f72507.jpeg" alt="img" style="zoom: 67%;" />

**插槽出现的原因：**

例如，要让组件第一次渲染时有按钮，第二次渲染时没有按钮。

如果父组件在使用到一个复用组件的时候，获取这个组件在不同的地方有少量的更改，如果去重写组件是一件不明智的事情

**插槽就是组件模板的容器，为了给组件的模板预留空间（组件抽取相同的地方，插槽抽取不同的地方），**

**通过插槽可以使组件具备扩展性，让用户可以扩展组件，更好地复用组件，并且对其做定制化处理。**

**通过 slot 插槽向组件内部指定位置传递内容，完成这个复用组件在不同场景的应用，**

插槽是 Vue 为组件的封装者提供的能力，允许开发者在封装组件时，把不确定的、希望由用户自定义的部分作为插槽。

**比如布局组件、表格列、下拉选择、弹框显示内容等。**

<img src="https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1652771049323-e01a776d-43f5-428c-b833-7a3c21c3c8cc.jpeg" alt="img" style="zoom:50%;" />

**例如，navbar 组件结构一样，内容不同，所以要把一样的东西写在组件里，不同的、可以替代的东西写在`<slot>`里**

下面代码中，使用自定义组件的子元素替代插槽中的 `<button>按钮</button>`

`<span>哈哈哈</span>` 替代 `<button>按钮</button>`

`<i>呵呵呵</i>` 替代 `<button>按钮</button>`

![img](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1652771141124-b31bab55-4873-438d-a979-53c5b21fbb5e.jpeg)

![img](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1652771177890-4cb0cedb-67e7-4aab-8475-ca52494e702c.jpeg)

**插槽就是组件内部插入的内容，由组件标签包裹的元素或者`<slot>`的默认内容决定**

**（即组件标签包裹的所有内容 替换掉 template 里的 `<slot>`）**

**插槽内容可以是任意合法的模板内容，不局限于文本，可以传入多个元素，甚至是组件。**

插槽内可以包含任何模板代码，包括 HTML：

```vue
<navigation-link url="/profile">
   <!-- 添加一个 Font Awesome 图标 -->
  <span class="fa fa-user"></span>
  Your Profile
</navigation-link>
```

甚至其它的组件：

```vue
<navigation-link url="/profile">
  <!-- 添加一个图标的组件 -->
  <font-awesome-icon name="user"></font-awesome-icon>
  Your Profile
</navigation-link>
```

举例来说，这里有一个 `<FancyButton>` 组件，可以像这样使用：

template

```html
<FancyButton>
  Click me! <!-- 插槽内容 -->
</FancyButton>
```

而 `<FancyButton>` 的模板是这样的：

template

```html
<button class="fancy-btn">
  <slot></slot> <!-- 插槽出口 -->
</button>
```

父元素提供的**插槽内容** (slot content) 会在`<slot>` 元素这个**插槽出口** (slot outlet)渲染。

![slots.dbdaf1e8.png](https://cdn.nlark.com/yuque/0/2022/png/1614731/1669988156627-3f5e9b3a-41b9-49ac-9974-6c4f4cbf0605.png)

最终渲染出的 DOM 是这样：

```html
<button class="fancy-btn">Click me!</button>
```

### 插槽使用场景：子组件展示动态的 DOM 结构内容

例如，child 组件要显示为：

```html
<div>
  <p>hello</p>
  <p>world</p>
</div>
```

```vue
<div id="app">
    <child content="<p>world</p>"></child>
</div>

Vue.component('child', {
    props: ['content'],
    template: `<div>
                   <p>hello</p>
                   <div v-html='this.content'></div>
              </div>`
})

var app = new Vue({
    el: '#app'
})
```

如果这样写，会出现

问题一：必须包裹一个 div 来接收传递的 DOM 结构内容

问题二：如果组件传递的 DOM 结构内容很多时，就很难阅读和维护

要使`<child>` 内部的 DOM 结构内容替换掉子组件 child 的 template 里的 `<slot>` ，Vue 可以这样合成组件：

```vue
<div id="app">
    <child>
        <p>world</p>
    </child>
</div>

Vue.component('child', {
    template: `<div>
                   <p>hello</p>
                   <slot></slot>
               </div>`
})

var app = new Vue({
    el: '#app'
})
```

**`<child>`组件标签 只负责传递 DOM 内容 `<p>world</p>`，`<p>world</p>` 会替换掉组件的 template 里的 `<slot>` 元素**

**`<slot>` 元素作为组件模板之中的内容分发插槽，**

**`<slot>`（槽）让插槽内容（父组件的子元素）插入之后，`<slot>` 元素自身会被替换**

![img](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1652771443039-9c78d92e-1f5f-4201-8a8a-0b922827bb6e.jpeg)

![img](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1652771443037-52607968-814c-4150-9966-67a8f772817f.jpeg)

### 默认插槽内容

**如果子组件 `<child>` 里没有 DOM 结构内容，子组件的 `<slot>` 将使用自己的内容作为默认内容**

**（默认内容可以是 HTML 元素，甚至可以是其它的组件）**

**但如果我们显式提供了插槽内容，那么显式提供的内容会取代默认内容**

```vue
<div id="app">
    <child></child>
</div>

Vue.component('child', {
    template: `<div>
                   <p>hello</p>
                   <slot>默认内容</slot>
               </div>`
})

var app = new Vue({
    el: '#app'
})
```

![img](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1652771504161-d368ae8a-d81f-4b77-935a-30ce331c9d00.jpeg)

![img](https://cdn.nlark.com/yuque/0/2022/png/1614731/1652771504218-1b97ffb3-ca59-4b2e-a4e0-83815fb5dbae.png)

**插槽的基本使用：**

**子组件的 template 里添加 `<slot></slot>`，父组件标签里的 DOM 结构内容 替换掉 `<slot></slot>`，完成额外展示 DOM 结构内容**

**父组件标签里没有 DOM 结构内容就使用子组件插槽的默认内容：`<slot>默认内容</slot>`，`<slot>`元素可以任意添加特性**

**即使父组件标签里多个元素放入到父组件，它们都会替换掉`<slot></slot>`**

<img src="https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1652771561030-2dd3c567-d5a2-46d9-b8d5-ce6b6d483a24.jpeg" alt="img" style="zoom: 67%;" />

**注意**

**在 2.6.0 中，Vue为具名插槽和作用域插槽引入了一个新的统一的语法 (即 v-slot 指令)。**

**它取代了 slot 和 slot-scope 这两个目前已被废弃但未被移除且仍在文档中的 attribute**

### Vue 的具名插槽

![img](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1652771632625-a442f469-be5b-4134-9d6d-a1ce6a38fa63.jpeg)

![img](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1652771632626-1b453edf-a48a-4bef-b905-e1dfad1c31f4.jpeg)

**这种带 name 的插槽被称为具名插槽，需要多个插槽时使用具名插槽。**

**具名插槽有默认值（因为一个不带 name 的 `<slot>` 会带有隐含的名字“default”）**

**子组件插槽未命名时使用的 name 是 default，对应的，父组件使用 v-slot:default ，简写为 v-slot （即默认插槽） **

**v-slot 也有缩写，即把参数之前的所有内容 (即 v-slot:) 替换为字符 #**

**例如 v-slot:header 可以被重写为 #header**

**只是简写为`#`时不能不写参数，要写成 #default**

封装 navbar 组件时用的就是具名插槽，掌握具名插槽就可以封装独立的组件了。

使用 JavaScript 函数来类比可能更有助于你来理解具名插槽：

```js
// 传入不同的内容给不同名字的插槽
BaseLayout({
  header: `...`,
  default: `...`,
  footer: `...`
})

// <BaseLayout> 渲染插槽内容到对应位置
function BaseLayout(slots) {
  return `<div class="container">
      <header>${slots.header}</header>
      <main>${slots.default}</main>
      <footer>${slots.footer}</footer>
    </div>`
}
```

#### 具名插槽的使用

**旧写法**

**在 `<child>` 的DOM结构内容中， `<slot>` 使用 `name` 特性定义不同的插槽名，用来判断父组件会使用哪个插槽，**

**父组件在 `<template>`里的子元素上使用 `slot` 特性指定子组件的插槽名，这些子元素会替换子组件中对应名字的插槽。**

```vue
<div id="app">
    <child>
        <div class="header" slot="header">header</div>
        <div class="footer" slot="footer">footer</div>
    </child>
</div>

Vue.component('child', {
    template: `<div>
                   <slot name="header"></slot>
                   <p>content</p>
                   <slot name="footer"></slot>
               </div>`
})

var app = new Vue({
    el: '#app'
})
```

![img](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1652771822045-901638d7-8189-4a5d-8b0c-f6197ef42419.jpeg)

**向具名插槽提供内容的时候，可以在一个 `<template>` 元素上使用 v-slot 指令，并以 v-slot 的参数的形式提供其名称：**

**新写法**

**slot 是废弃的写法，现在这么写**

```vue
<div id="app">
    <child>
        <!-- 新写法 -->
        <template v-slot:header>
            <div class="header">header</div>
        </template>
        <!-- <p v-slot:default>content</p> 可有可无，结果一样 -->
        <!-- 新写法简写 -->
        <template #footer>
            <div class="footer">footer</div>
        </template>
    </child>
</div>

Vue.component('child', {
    template: `<div>
                   <slot name="header"></slot>
                   <p>content</p>
                   <slot name="footer"></slot>
               </div>`
})

var app = new Vue({
    el: '#app'
})
```

**注意 v-slot 只能添加在 `<template>` 上** (只有一种例外情况)，这一点和已经废弃的 slot 特性 不同。

### Vue 的作用域插槽 

**子组件内数据可以被父组件拿到（解决了数据只能从父组件传递给子组件），**

**子组件模板作用域的数据传给父组件模板作用域，**

**父组件模板拿到数据作为插槽内容替换掉子组件的 `<slot></slot>`，内容由子组件提供。**

作用域插槽与具名插槽区别：

v-slot:header                         具名插槽

v-slot:header="childData"     具名插槽变成作用域插槽

v-slot:default="childData"      默认插槽变成作用域插槽

#### 编译作用域

<img src="https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1652771902525-506f3fd7-fd7a-4c35-8363-b5df6dec69b6.jpeg" alt="img"  />

**Vue 指令使用 data 时会看一下它在哪个组件模板里，就去哪个组件查找 data ，**

所以上述中子组件的模板会渲染出来

当你想在一个插槽中使用数据时：

```vue
<navigation-link url="/profile">
  Logged in as {{ user.name }}
  Clicking here will send you to: {{ url }}
  <!--
  这些内容(指插槽内容)是传递给 <navigation-link> 这个子组件的，
  因为 <navigation-link> 子组件没有定义 `url` ，并且 `url` 它是在父组件定义的，
  所以这里的 `url` 会是 undefined 。
  -->
</navigation-link>
```

该插槽跟模板的其它地方一样可以访问相同的实例 property (也就是相同的“作用域”)，就是不能访问 `<navigation-link>` 的作用域。

**例如在 navigation-link 这个子组件里面， navigation-link 是访问不到 url 的（因为 url 它在父组件定义，属于父级模板）**

**父级模板里的所有内容都是在父级作用域中编译的；子级模板里的所有内容都是在子级作用域中编译的。**

#### 作用域插槽的使用

<img src="https://cdn.nlark.com/yuque/0/2022/png/1614731/1657115176755-b3dac510-2193-4583-98e2-59872f39574d.png" alt="slot.png" style="zoom: 50%;" />

﻿**有时让父组件的插槽内容能够访问子组件中才有的数据是很有用的。**

**作用域插槽可以为 slot 标签绑定数据，让其父组件可以获取到子组件的数据。**

**子组件 data  => 父组件 template **

**简而言之：**

**子组件 data （也可以使用父组件的 props 作为子组件 data 的值）在 `<slot>` 绑定的所有 prop （插槽prop）会传给父组件使用，**

**这些 prop 会作为属性挂在父组件`v-slot:xxx`右边接受的 slotProps 对象（包含所有 插槽 prop 的对象）上，**

**并且可以解构 slotProps 对象，**

**父组件 template 通过`v-slot:`（简写：#）获取子组件的 data，在插槽内容中使用替换子组件 `<slot>`**。

子组件`Child.vue`

```vue
<template> 
  <slot name="footer" testProps="子组件 data 的值">
    <h3>没传footer插槽</h3>
  </slot>
</template>
```

父组件

```vue
<child> 
  <!-- 把 v-slot 的值指定为作⽤域上下⽂对象 -->
  <template v-slot:footer="slotProps">
    来⾃⼦组件数据：{{slotProps.testProps}}
  </template>
   <!-- 或者简写形式 -->
  <template #footer="slotProps">
    来⾃⼦组件数据：{{slotProps.testProps}}
  </template>
</child>
```

- **`v-slot`属性只能在`<template>`上使用，只有默认插槽时可以在组件标签上使用**

<img src="https://cdn.nlark.com/yuque/0/2022/png/1614731/1657115065858-da060aeb-e431-4c60-bb67-76e01192117f.png" alt="scoped-slot.png" style="zoom: 33%;" />

作用域插槽示例

如：

```vue
<template>
    <!-- parent.vue -->
    <div class="parent">
        <h1>这是父组件</h1>
        <current-user>
            <template v-slot="slotProps">
                {{ slotProps.user.name }}
            </template>
        </current-user>
    </div>
</template>


<template>
    <!-- child.vue -->
    <div class="child">
        <h1>这是子组件</h1>
        <slot :user="user"></slot>
    </div>
</template>
<script>
export default {
    data() {
        return {
            user: {
                name: '小赵'
            }
        }
    }
}
</script>
```

**作用域插槽目的就是父组件模板的 DOM 结构内容获取子组件 data 的数据后，再替换掉子组件模板的`<slot>`。**

有如下模板的 `<current-user>` 组件：

子组件

```vue
Vue.component('current-user', {
  template: `<span>
               <slot>{{ user.lastName }}</slot>
             </span>`,
   data: { user: ... }
})

var app = new Vue({
  el: '#app'
})
```

想换掉 `<slot>` 内容，用 user.firstName 代替  user.lastName  。

父组件

```vue
<div id="app">
  <current-user>
    {{ user.firstName }}
  </current-user>
</div>
```

上述代码 

```js
{{ user.firstName }}
```

不会正常工作

**因为只有 `<current-user>` 组件（在 current-user 的模板中）有 data 可以访问到 user ，**

```js 
{{ user.firstName }}
```

**无法渲染（在 app 的模板中）。**

**为了让 user 在父级的插槽内容（app 的模板中）**

```js
{{ user.firstName }}
```

**可用，**

**可以将 user 作为 `<slot>` 元素的一个 attribute 绑定上去：**

子组件

```vue
Vue.component('current-user', {
  template: `<span>
               <slot :user="user">
                 {{ user.lastName }}
               </slot>
             </span>`,
  data: { 
    user: ...
  }
})

var app = new Vue({
  el: '#app'
})
```

**绑定在 `<slot>` 元素上的 attribute 被称为 插槽 prop，这个例子中 user 就是 插槽 prop。**

#### 解构插槽 Prop

在父级作用域中，我们可以使用带值的 v-slot 为我们刚才准备的插槽 prop 定义名字：

父组件

```vue
<div id="app">
  <current-user>
    <template v-slot:default="slotProps">
      {{ slotProps.user.firstName }}
    </template>
  </current-user>
</div>
```

在这个例子中，我们选择**将包含所有 插槽 prop 的对象命名为 slotProps**，但你也可以使用任意你喜欢的名字。

可以简写为：

```vue
<div id="app">  
  <current-user > 
    <template v-slot="{ user }">
      {{ user.firstName }}
    </template>
  </current-user>
</div>
```

**可以通过解构获取`v-slot={user}`，还可以重命名`v-slot="{user: newName}"`和定义默认值`v-slot="{user = '默认值'}`**

**过程如下：**

子组件

```vue
Vue.component('current-user', {
  template: `<span>
               <slot :user="user">
                 {{ user.lastName }}
               </slot>
             </span>`,
  data: { 
    user: ...
  }
})

var app = new Vue({
  el: '#app'
})
```

父组件

```vue
<div id="app">
  <current-user>
    <template v-slot:default="slotProps">
      {{ slotProps.user.firstName }}
    </template>
  </current-user>
</div>
```

**1、子组件模板使用 v-bind 将数据绑定到自己的 `<slot>` 的  user 特性上，user 特性 也称作 插槽 prop**

**2、父组件`<template>`使用 v-slot:default="slotProps"（:default 表示插槽名的默认值，可选）**

**子组件 data => 子组件`<slot :user="user">` => 父组件 `<template v-slot:default="slotProps">`**

**3、user 特性 传递到父组件模板的 slotProps 对象里，**

```js 
{{ slotProps.user.firstName }} 
```

**获取子组件 `<current-user>` 的 data** 

**user => slotProps**

**4、父组件 template 的** 

```js
{{ slotProps.user.firstName }} 
```

**替换掉子组件的**

```vue
<slot :user="user">{{ user.lastName }}</slot>
```

<img src="https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1652772538463-7e3c7883-e0c4-454c-88f1-c995fda95a00.jpeg" alt="img" style="zoom:67%;" />

<img src="https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1652772538453-c7a7942d-0897-440c-8321-c4aebffd2a47.jpeg" alt="img" style="zoom:67%;" />

```vue
<div id="app">
  <cpn></cpn>
  <cpn></cpn>
  <cpn>
    <!-- 目的是父组件的插槽获取子组件 pLanguages 的数据 -->
    <!-- Vue2.5x 以下 -->
    <!-- 引用slot对象 -->
    <!-- 旧写法 -->
    <template slot-scope="slot">
      <!-- <span v-for="item in slot.data">{{item}}-</span> -->
      <span>{{slot.data.join(' - ')}}-</span>
    </template>
     <!-- 新写法 -->
     <template v-slot="slot">
      <span>{{slot.data.join(' - ')}}-</span>
    </template>
  </cpn>
  <cpn>
    <!-- 旧写法 -->  
    <template slot-scope="slot">
      <span>{{slot.data.join(' * ')}}-</span>
    </template>
     <!-- 新写法 -->
     <template v-slot="slot">
      <span>{{slot.data.join(' * ')}}-</span>
    </template>
  </cpn>
</div>

<template id="cpn">
  <div>
    <slot :data="pLanguages">
      <ul>
        <li v-for="item in pLanguages">{{item}}</li>
      </ul>
    </slot>   
  </div>
</template>

const app = new Vue({
  el: '#app',
  components: {
    cpn: {
      template: '#cpn',
      data() {
        return {
          pLanguages: ['JavaScript', 'Java', 'PHP', 'C#', 'C++', 'Python']   
        }
      }
    }
  }
})
```

![img](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1652772596454-46ced93b-c3fe-4012-a98f-40cdd5271b9b.jpeg)

**自 2.6.0 起有所更新。已废弃使用 slot-scope 的语法，使用 v-slot 代替 slot-cope**


子组件的 template 里循环显示 data 的 list 数据

```vue
<div id="app">
    <child></child>
</div>

Vue.component('child', {
    data: function () {
        return {
            list: [1, 2, 3, 4]
        }
    },
    template: `<ul>
                   <li v-for="item of list">{{item}}</li>
               </ul>`
})

var app = new Vue({
    el: '#app'
})
```

**如果 Vue.component 里的`<child>`组件在很多地方使用，列表怎么循环才能由组件`<child>`内部的模板`<template>`内容决定呢？**

**当子组件有多个 DOM 结构（做循环或者某一部分的）要由外部传递进来时，可以使用作用域插槽。**

子组件在 `<template>` 的 `<slot>` 中使用 v-bind 向父组件传数据，

:item是绑定在 `<slot>` 元素上的特性，也就是 插槽 prop，

父组件一定要写一个`<template>`和 v-slot，

在`<template>`使用 v-slot="props" 接收所有的 item，props 是一个对象，item 存放在 props 的属性中

这个例子选择将包含所有插槽 prop 的对象命名为 slotprops，但也可以使用任意你喜欢的名字

```vue
<div id="app">
    <child>
        <template v-slot="slotprops">
            <h1>{{ slotprops.item }}</h1>
        </template>
    </child>
</div>

Vue.component('child', {
    data: function () {
        return {
            list: [1, 2, 3, 4]
        }
    },
    template: `<ul>
                   <slot :item="item" v-for="item of list"></slot>
               </ul>`
})

var app = new Vue({
    el: '#app'
})
```

<img src="https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1652772754962-7def0a7d-8b2d-40f5-b05d-1e2b7268b722.jpeg" alt="img" style="zoom: 50%;" />

如果将 

```js
<h1>{{ slotprops.item }}</h1>
```

改为 

```js
<h1>{{ slotprops }}</h1>
```

```vue
<div id="app">
  <child>
    <template v-slot="slotprops">
      <h1>{{ slotprops }}</h1>
    </template>
  </child>
</div>
```

效果图

<img src="https://cdn.nlark.com/yuque/0/2022/png/1614731/1652772798568-4d80568f-9bc6-4375-800a-4e72149f721a.png" alt="img" style="zoom:50%;" />

#### 动态插槽名

动态指令参数在 `v-slot` 上也是有效的，即可以定义下面这样的动态插槽名：

template

```vue
<base-layout>
  <template v-slot:[dynamicSlotName]>
    ...
  </template>

  <!-- 缩写为 -->
  <template #[dynamicSlotName]>
    ...
  </template>
</base-layout>
```

#### 动态插槽（具名插槽与作用域插槽结合使用）

```vue
<html>
  <head>
    <title>插槽 slot</title>
    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
  </head>
  <body>
    <div id="root2">
      <div>案例2：Vue2.6新特性 - 动态slot</div>
      <Test>
        <template v-slot:[section]="{section}">
          <div>this is {{section}}</div>
        </template>
      </Test>
      <button @click="change">switch header and body</button>
    </div>
    <script>
      Vue.component('Test', {
        template:
          '<div>' +
            '<slot name="header" :user="obj" :section="\'header\'">' +
              '<div>默认header</div>' +
            '</slot>' +
            '<slot :user="obj" :section="\'body\'">默认body</slot>' +
          '</div>',
        data() {
          return {
            obj: { a: 1, b: 2 }
          }
        }
      });
      new Vue({
        el: '#root2',
        data() {
          return {
            section: 'header'
          }
        },
        methods: {
          change() {
            this.section === 'header' ? this.section = 'default' : this.section = 'header'
          }
        }
      });
    </script>
  </body>
</html>
```

![img](https://cdn.nlark.com/yuque/0/2022/gif/1614731/1652772858146-74426825-ec73-418f-945e-2fb07a9ea6b7.gif)

```html
'<slot name="header" :user="obj" :section="\'header\'">' +
   '<div>默认header</div>' +
'</slot>'
```

上述代码，默认header 是具名插槽，当具名插槽不传内容时会显示默认内容 `<div>默认header</div>`

```html
'<slot :user="obj" :section="\'body\'">默认body</slot>'
```

上述代码，默认body 是默认插槽

一开始，因为 data 的 section 值为 header ，所以

```js
:section="\'header\'" => { section } => {{ section }}
```

```vue
<Test>
  <template v-slot:header="{ section }">
    <div>this is header</div>
  </template>
</Test>
```

相当于显示为：

```vue
<div>
  <template>
    <div>this is header</div>
  </template>
  默认body    
</div>
```

当点击按钮触发事件后，section 值为 default，所以

```js
:section="\'body'" => { section } => {{ section }}
```

```vue
<Test>
  <template v-slot:default="{ section }">
    <div>this is body</div>
  </template>
</Test>
```

相当于显示为：

```html
<div>
  默认header
  <template>
    <div>this is body</div>
  </template>   
</div>
```

## 动态组件 与 v-once指令

### 动态组件

**以前的写法**

```vue
<div id="app">
    <child-one v-if="show === 'one'"></child-one>
    <child-two v-if="show === 'two'"></child-two>
    <button @click="handleClick">change</button>
</div>

Vue.component('child-one', {
    template: `<p>one</p>`
})

Vue.component('child-two', {
    template: `<p>two</p>`
})

var app = new Vue({
    el: '#app',
    data: {
        show: 'one'
    },
    methods: {
        handleClick: function () {
            this.show = (this.show === 'one' ? this.show = 'two' : this.show = 'one');
        }
    }
})
```

![img](https://cdn.nlark.com/yuque/0/2022/gif/1614731/1652772960018-0ff3b624-5578-42ef-bcf7-b35ae59427af.gif)

**动态组件的写法**

`<component>`为动态组件，:is 表示组件名，

动态组件就是`<component>`通过 :is 里面的值来选择显示哪个组件，当组件名改变就会销毁上一个组件

```vue
<div id="app">
    <component :is="show"></component>
    <button @click="handleClick">change</button>
</div>

Vue.component('child-one', {
    template: `<p>one</p>`
})

Vue.component('child-two', {
    template: `<p>two</p>`
})

var app = new Vue({
    el: '#app',
    data: {
        show: 'child-one'
    },
    methods: {
        handleClick: function () {
            this.show = (this.show === 'child-one' ? this.show = 'child-two' : this.show = 'child-one');
        }
    }
})
```

![img](https://cdn.nlark.com/yuque/0/2022/gif/1614731/1652772973658-8a8c7611-fade-47c7-a18a-3e4487baae31.gif)

### 通过 v-once 创建低开销的静态组件

有的时候你可能有一个组件，这个组件包含了大量静态内容。

在这种情况下，你可以在根元素上添加 v-once 特性以确保这些内容只计算一次然后缓存起来。

每次组件改变，底层都需要销毁一个组件再创建一个组件，这种方式耗费性能高

v-once 指令会把组件放进内存，当下次使用时再从内存中拿出来，而不是再创建一个组件，有效提高性能

使用 v-once 指令，你也能执行一次性地插值，但当数据改变时，插值处的内容不会更新。请留心这会影响到该节点上的其它数据绑定：

```vue
<span v-once>这个将不会改变: {{ msg }}</span>
```

注意：除非你非常留意渲染变慢了，不然它完全是没有必要的——它在后期可能会带来很多困惑，例如组件的数据无法更新。

```vue
Vue.component('child-one', {
    template: `<p v-once>one</p>`
})

Vue.component('child-two', {
    template: `<p v-once>two</p>`
})

var app = new Vue({
    el: '#app',
    data: {
        show: 'one'
    },
    methods: {
        handleClick: function () {
            this.show = (this.show === 'one' ? this.show = 'two' : this.show = 'one');
        }
    }
})
```

![img](https://cdn.nlark.com/yuque/0/2022/gif/1614731/1652773118405-63397e48-e3e7-47b6-9304-a2a564c1783c.gif)

## 过渡

### Vue 的 CSS 过渡效果

Vue 提供了 transition 的封装组件，在下列情形中，可以给任何元素和组件添加进入/离开过渡

● 条件渲染 (使用 v-if)

● 条件展示 (使用 v-show)

● 动态组件

● 组件根节点

`<transition>` 只会把过渡效果应用到它包裹的内容上，不会额外渲染 DOM 元素，也不会出现在 DOM 结构中。

`<transition>` 的 name 属性用于自动生成 CSS 过渡类名

例如：name: 'fade' 将自动拓展为 .fade-enter， .fade-enter-active 等过渡类名。默认类名为 "v"

```vue
.fade-enter, .fade-leave-to {
    opacity: 0;
}
.fade-enter-active, .fade-leave-active {
    transition: opacity 1s;
}

<div id="app">
    <transition name="fade">
        <div v-if="show">hello world</div>
    </transition>
    <button @click="handleClick">切换</button>
</div>

var app = new Vue({
    el: '#app',
    data: {
        show: true
    },
    methods: {
        handleClick: function () {
            this.show = !this.show
        }
    }
})
```

![img](https://cdn.nlark.com/yuque/0/2022/gif/1614731/1652773681166-4b3a28c7-368a-4ce3-8b6e-18f223ce0c14.gif)

**当一个元素被`<transition>`包裹之后，Vue会自动嗅探被包裹元素是否应用了 CSS 过渡或动画，**

**如果是，在恰当的时机添加/删除 CSS 类名，构建动画流程。**

**如果没有检测到 JavaScript 钩子函数 和 CSS 过渡/动画，DOM 操作 (插入/删除) 在下一帧中立即执行。**

**opcity：0为显示（不透明），1为隐藏（透明）**

在隐藏动画的第一帧（即开始运动前）Vue 会往内部 div 添加两个 class ： 

**fade-enter（opcity为0） 和 fade-enter-active**，hello world 处于隐藏状态

在隐藏动画的第二帧，将 **fade-enter 移除**，往内部 div 再添加一个 class ：

**fade-enter-to（opcity从0变为1）**，fade-enter-active 监听到 opcity 的变化（执行过渡效果）

直到动画结束 fade-enter-to 和 fade-enter-active 才被移除，**fade-enter-active 监听整个过程**

<img src="https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1652773712213-6a244042-9d9e-477c-be0b-b396acfbb60e.jpeg" alt="img" style="zoom:50%;" />

在显示动画的第一帧（即开始运动前）Vue 会往内部 div 添加两个 class ：

**fade-leave（opcity为1） 和 fade-leave-active**，hello world 处于显示状态

在显示动画的第二帧，将 fade-leave 移除，往内部div再添加一个 class ：

**fade-leave-to（opcity从1变为0）**，fade-leave-active 监听到 opcity 的变化（执行过渡效果）

直到动画结束 fade-leave-to 和fade-leave-active 才被移除，**fade-leave-active 监听整个过程**

<img src="https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1652773721161-4cf7887e-c8c6-40de-bea7-8953b29d715b.jpeg" alt="img" style="zoom: 50%;" />

<img src="https://cdn.nlark.com/yuque/0/2022/png/1614731/1652773984494-6ce52d19-d710-43fb-93ea-899b6e19b43a.png" alt="img" style="zoom: 50%;" />

**fade-enter（opcity为0）fade-enter-to（opcity从0变为1） fade-leave（opcity为1） fade-leave-to（opcity从1变为0）** 

​          **fade-enter-active 监听整个过程 （隐藏）                                fade-leave-active 监听整个过程（显示）**

### 使用 Animate.css 库

如果不想使用 fade-enter-active 或 fade-leave-active 类名，可以使用自定义过渡的类名：

● enter-class

● enter-active-class

● enter-to-class (2.1.8+)

● leave-class

● leave-active-class

● leave-to-class (2.1.8+)

```vue
.enter, .leave {
    opacity: 0;
}
.enter-a, .leave-a {
    transition: opacity .5s;
}

<div id="app">
    <transition name="fade"
                enter-class="enter"
                leave-to-class="leave"
                enter-active-class="enter-a"
                leave-active-class="leave-a">
        <div v-if="show">hello world</div>
    </transition>
    <button @click="handleClick">切换</button>
</div>

var app = new Vue({
    el: '#app',
    data: {
        show: true
    },
    methods: {
        handleClick: function () {
            this.show = !this.show
        }
    }
})
```

![img](https://cdn.nlark.com/yuque/0/2022/gif/1614731/1652774482952-2f903e09-564a-488e-a966-db7acddfbdcc.gif)

这对于 Vue 的过渡系统和其他第三方 CSS 动画库，如 Animate.css 结合使用十分有用（前提是要在 class 上先加 animated 表示添加 Animate 库）

```vue
<link rel="stylesheet" type="text/css" href="./animate.css">

<div id="app">
    <transition name="fade"  
                enter-active-class="animated slideInLeft"
                leave-active-class="animated slideOutLeft">
        <div v-if="show">hello world</div>
    </transition>
    <button @click="handleClick">切换</button>
</div>
```

![img](https://cdn.nlark.com/yuque/0/2022/gif/1614731/1652774618112-271853f9-7a4a-4f27-bd64-26df8cfeb7cc.gif)

### 初始化动画

但是初始化时没有动画效果，需要添加 appear 和 appear-active-class，appear-active-class 设置与 enter-active-class一样的动画即可：

appear - 是否在初始渲染时使用过渡。默认为 false。添加则为 true

```vue
<transition name="fade"  
                enter-active-class="animated slideInLeft"
                leave-active-class="animated slideOutLeft"
                appear
                appear-active-class="animated slideInLeft">
    <div v-if="show">hello world</div>
</transition>
```

![img](https://cdn.nlark.com/yuque/0/2022/gif/1614731/1652774711140-c809a092-c79f-44c2-b18b-f57a68998555.gif)

### 同时使用过渡和动画

Animate.css库提供的是 @keyframes 关键帧和 animation 配合的 CSS3 动画

但是如果需要同时使用 过渡 和 CSS3 动画，就要在 enter-active-class 和  leave-active-class 中

添加过渡类 fade-enter-active 和 fade-leave-active 来实现

```vue
.fade-enter, .fade-leave-to {
    opacity: 0;
}
.fade-enter-active, .fade-leave-active {
    transition: opacity 3s;
}

<div id="app">
    <transition name="fade"  
                enter-active-class="animated swing fade-enter-active"
                leave-active-class="animated shake fade-leave-active"
                appear
                appear-active-class="animated swing">
        <div v-if="show">hello world</div>
    </transition>
    <button @click="handleClick">切换</button>
</div>

var app = new Vue({
    el: '#app',
    data: {
        show: true
    },
    methods: {
        handleClick: function () {
            this.show = !this.show
        }
    }
})
```

![img](https://cdn.nlark.com/yuque/0/2022/gif/1614731/1652774815872-cdf460a8-bb51-4d78-9a91-f8c405f6a24b.gif)

最后，还有一个问题：Animate.css 库动画的时长是1s，而 transition 动画的时长是3s

Vue 为了知道过渡的完成，必须设置相应的事件监听器，确定哪个动画时长为结束时长

type 指定过渡事件类型，侦听过渡何时结束。有效值为 "transition" 和 "animation"。

默认 Vue.js 将自动检测出持续时间长的为过渡事件类型。

如果设置 type="transition"，那么动画时长以 transition 动画的时长为准，但是 appear 会失效

```vue
<transition name="fade"  
            type="transition"
            enter-active-class="animated swing fade-enter-active"
            leave-active-class="animated shake fade-leave-active"
            appear
            appear-active-class="animated swing">
```

:duration 可以自定义控制动画的总时长 或者 入场和出场动画的总时长，即可以延迟 DOM 节点的移除，

下面注释部分的意思是 transition 动画的时长执行 3 秒，等 7 秒后移除 DOM 节点

```vue
<transition name="fade"  
            :duration = {enter: 3000, leave: 4000} <!--相当于 :duration = "7000" -->
            enter-active-class="animated swing fade-enter-active"
            leave-active-class="animated shake fade-leave-active"
            appear
            appear-active-class="animated swing">
```

### Vue 的 JS 动画与 Velocity.js

Vue 实现 JS 动画

JS 钩子函数

```vue
v-on:before-enter="beforeEnter"
v-on:enter="enter"
v-on:after-enter="afterEnter"
v-on:enter-cancelled="enterCancelled"

v-on:before-leave="beforeLeave"
v-on:leave="leave"
v-on:after-leave="afterLeave"
v-on:leave-cancelled="leaveCancelled"
```

JS钩子函数可以结合 CSS transitions / animations 使用，也可以单独使用。

如果过渡组件提供了 JavaScript 钩子函数，这些钩子函数将在恰当的时机被调用。

每个 JS钩子函数 都可以接收一个 el参数 和 done回调函数，el 表示 `<transition>` 包裹的元素，

done()表示告诉Vue这个动画执行完成的时机

```js
// 当与 CSS 结合使用时
// 回调函数 done 是可选的
leave: function (el, done) {
  // ...
  done()
}
```

当只用 JavaScript 过渡的时候，在 enter 和 leave 中必须使用 done 进行回调。

否则，它们将被同步调用，过渡会立即完成（比如直接跳到 afterEnter 或者 造成 afterEnter 比 enter 先执行）。

```vue
.fade-enter, .fade-leave-to {
    opacity: 0;
}
.fade-enter-active, .fade-leave-active {
    transition: opacity 1s;
}

<div id="app">
    <transition name="fade"  @before-enter="beforeEnter" @enter="enter" @after-enter="afterEnter">
        <div v-if="show">hello world</div>
    </transition>
    <button @click="handleClick">切换</button>
</div>

var app = new Vue({
    el: '#app',
    data: {
        show: true
    },
    methods: {
        handleClick: function () {
            this.show = !this.show
        },
        // 入场动画前
        beforeEnter: function (el) {
            el.style.color = 'red'
        },
        // 进入入场动画
        enter: function (el, done) {
            setTimeout(() => {
                el.style.color = 'green'
            }, 2000);
            setTimeout(() => {
                done()
            }, 4000);
        },
        // 入场动画之后
        afterEnter: function (el) {
            el.style.color = 'black'
        }
    }
})
```

![img](https://cdn.nlark.com/yuque/0/2022/gif/1614731/1652775284445-f84f1869-e1b3-4dd9-b4b0-2d9a859add96.gif)

### 使用 Velocity.js 实现 JS 动画

```vue
<div id="app">
    <transition name="fade"  @before-enter="beforeEnter" @enter="enter" @after-enter="afterEnter">
        <div v-if="show">hello world</div>
    </transition>
    <button @click="handleClick">切换</button>
</div>

<script src="./velocity.js">

var app = new Vue({
    el: '#app',
    data: {
        show: true
    },
    methods: {
        handleClick: function () {
            this.show = !this.show
        },
        beforeEnter: function (el) {
            el.style.opacity = 0;
        },
        enter: function (el, done) {
            Velocity(el, {opacity: 1}, {duration: 1000, complete: done}); // 隐藏到显示渐隐渐现，注意必须加complete: done 回调done
        },
        afterEnter: function (el) {
            console.log('动画结束')
        }
    }
})
```

![img](https://cdn.nlark.com/yuque/0/2022/gif/1614731/1652775345289-64c7014c-8ba3-4963-a17e-6d6188853ee2.gif)

### 多个元素的过渡

当有相同标签名的元素切换时，需要通过 key 特性设置唯一的值来标记以让 Vue 区分它们，

否则 Vue 为了效率只会替换相同标签内部的内容。

即使在技术上没有必要，给在 `<transition>` 组件中的多个元素设置 key 是一个更好的实践。

```vue
.v-enter, .v-leave-to {
    opacity: 0;
}
.v-enter-active, .v-leave-active {
    transition: opacity 1s;
}

<div id="app">
    <transition>
        <div v-if="show" key="hello">hello world</div>
        <div v-else key="bye">bye world</div>
    </transition>
    <button @click="handleClick">切换</button>
</div>

var app = new Vue({
    el: '#app',
    data: {
        show: true
    },
    methods: {
        handleClick: function () {
            this.show = !this.show;
        }
    }
})
```

![img](https://cdn.nlark.com/yuque/0/2022/gif/1614731/1652775431602-1952836c-0c62-4d54-a04d-6391f888e963.gif)

Vue 还为`<transition>`提供了 过渡模式（mode）

● in-out：新元素先进行过渡，完成之后当前元素过渡离开。（要显示的元素显示完成之后，要隐藏的元素再隐藏）

● out-in：当前元素先进行过渡，完成之后新元素过渡进入。（要隐藏的元素隐藏完成之后，要显示的元素再显示）

```vue
<div id="app">
    <transition mode="out-in">
        <div v-if="show" key="hello">hello world</div>
        <div v-else key="bye">bye world</div>
    </transition>
    <button @click="handleClick">切换</button>
</div>
```

![img](https://cdn.nlark.com/yuque/0/2022/gif/1614731/1652775468804-4f5476f7-be9e-4f4b-a80a-86a052c2a674.gif)

### 多个组件的过渡

多个组件的过渡简单很多 - 我们不需要使用 key 特性。相反，我们只需要使用动态组件（component 和 :is）来切换组件：

```vue
.v-enter, .v-leave-to {
    opacity: 0;
}
.v-enter-active, .v-leave-active {
    transition: opacity 1s;
}

<div id="app">
    <transition mode="out-in">
        <component :is="type"></component>
    </transition>
    <button @click="handleClick">切换</button>
</div>

Vue.component('child-one', {
    template: '<div>child-one</div>'
});  
Vue.component('child-two', {
    template: '<div>child-two</div>'
});

var app = new Vue({
    el: '#app',
    data: {
        type: 'child-one'
    },
    methods: {
        handleClick: function () {
            this.type = (this.type === 'child-one' ? 'child-two' : 'child-one');
        }
    }
})
```

![img](https://cdn.nlark.com/yuque/0/2022/gif/1614731/1652775770038-14df045e-f852-4a02-8b48-f705ad4d3d21.gif)

### Vue 的列表过渡

```vue
.v-enter, .v-leave-to {
    opacity: 0;
}
.v-enter-active, .v-leave-active {
    transition: opacity 1s;
}

<div id="app">
    <transition-group>
        <div v-for="item of list" :key="item.id">{{item.content}}</div>
    </transition-group>
    <button @click="handleClick">添加</button>
</div>

var count = 0;
var app = new Vue({
    el: '#app',
    data: {
        list: []
    },
    methods: {
        handleClick: function () {
             this.list.push({
                 id: count++,
                 content: 'hello world'  
             })
        }
    }
})
```

![img](https://cdn.nlark.com/yuque/0/2022/gif/1614731/1652775824277-4cf8359a-4249-4083-af7e-08d86d8adfd6.gif)

`<transition-group> `组件的几个特点：

● 不同于 `<transition>`，它会以一个真实元素呈现：默认为一个 `<span>`。你也可以通过 tag 特性更换为其他元素。

● 过渡模式不可用，因为我们不再相互切换特有的元素。

● 内部元素 总是需要 提供唯一的 key 属性值。

● CSS 过渡的类将会应用在内部的元素中，而不是这个组/容器本身。

```vue
<transition-group>
    <div v-for="item of list" :key="item.id">{{item.content}}</div>
</transition-group>
```

上面三行代码的本质其实是：

```vue
<transition>
    <div>hello world</div>
</transition>

<transition>
    <div>hello world</div>
</transition>

<transition>
    <div>hello world</div>
</transition>
.......
```

所以，列表过渡的原理是：将整个列表分解为单个元素的过渡，然后Vue对每个元素在某个时间点逐一添加过渡类名。

### Vue 的动画封装

使用组件，组件的 props 和 slot 依靠外部 v-bind 和 DOM 结构 传内容

CSS 动画可以使用 JS 钩子函数实现，就不需要再写 style 样式了

```vue
<div id="app">
    <fade :show="show">
        <div>hello world</div>
    </fade>
    <fade :show="show">
        <h1>hello world</h1>
    </fade>
    <button @click="handleClick">切换</button>
</div>

Vue.component('fade', {
    props: ['show'],
    template: `<transition @before-enter="beforeEnter" @enter="enter">
                   <slot v-if="show"></slot>
               </transition>`,
    methods: {
        beforeEnter: function (el) {
            el.style.color = 'red';
        },
        enter: function (el, done) {
            setTimeout(() => {
                el.style.color = 'green';
            }, 2000);
            setTimeout(() => {
                el.style.color = 'black';
            }, 4000);
            done();
        }
    }      
})

var app = new Vue({
    el: '#app',
    data: {
        show: false
    },
    methods: {
        handleClick: function () {
            this.show = !this.show
        }
    }
})
```

![img](https://cdn.nlark.com/yuque/0/2022/gif/1614731/1652776012548-887b0973-d9e3-4645-b67a-125ceb75c6e6.gif)

注意

在 Vue 中使用插槽 slot ，是无法通过 v-show 控制 slot 的显示的

来看代码

```vue
Vue.component('fade', {
      props: ['show'],
      template: `
        <transition @before-enter="handleBeforeEnter" @enter="handleEnter">
            <slot v-show="show"></slot>
        </transition>
      `,
      methods: {
          handleBeforeEnter: function(el) {
              el.style.color = 'red'
          },
          handleEnter: function(el, done) {
              setTimeout(() => {
                  el.style.color = 'green';
                  done()
          }, 2000)
        }
      }
})
```

原因是这样的，slot 实际上是一个抽象元素，有点类似 template，本质上并不是一个元素。

而 v-show 是通过控制元素的 display 来进行显示隐藏的，slot 本质上并不是元素，所以压根也就不会有 display 这个 css 属性。

所以，slot 的显示隐藏，需要使用 v-if 。

## 自定义指令

**自定义指令主要是为了重用涉及普通元素的底层 DOM 访问的逻辑。**

**只有当所需功能只能通过直接的 DOM 操作来实现时，才应该使用自定义指令。**

**可以使用自定义指令代替一些通用的样式和逻辑，比如点击更新按钮显示加载中的遮罩**

每写一个新的组件都要加上这行代码 :visible，这种方式则是侵入式的，而这里只需要在父组件（父元素）上添加自定义指令即可

### 如何实现

注册一个自定义指令有全局注册与局部注册

全局注册主要是通过 Vue.directive 方法进行注册

Vue.directive 第一个参数是指令的名字（不需要写上v-前缀），第二个参数可以是对象数据，也可以是一个指令函数

```js
// 注册一个全局自定义指令 `v-focus`
Vue.directive('focus', {
  // 当被绑定的元素插入到 DOM 中时……
  inserted: function (el) {
    // 聚焦元素
    el.focus()  // 页面加载完成之后自动让输入框获取到焦点的小功能
  }
})
```

局部注册通过在组件options选项中设置directive属性

```js
directives: {
    focus: {
        // 指令的定义
        inserted: function (el) {
            el.focus() // 页面加载完成之后自动让输入框获取到焦点的小功能
        }
    }
}
```

```js
var vm = new Vue({
    el: '#app',
    data: {
        msg: {
            color: 'red'
        }
    },
    // 局部指令，需要定义在  directives 的选项
    directives: {
        color: {
            bind: function(el, binding){
                el.style.backgroundColor = binding.value.color;
            }
        },
        focus: {
            inserted: function(el) {
                el.focus();
            }
        }
    }
});
```

然后你可以在模板中任何元素上使用新的 v-focus property，如下：

```vue
<input v-focus />
```

### 项目中应用（插件方式批量注册）

**公共组件和过滤器也可以插件方式批量注册**

utils/directives.js

```js
let directives = {
  debounce,
  ...
};
    
export default {
  install(Vue) {
    Object.keys(directives).forEach((directive) => {
      Vue.directive(directive, directives[directive]);
    });
  },
};
```

main.js

```js
import directives from "@/utils/directives.js";
Vue.use(directives);

new Vue({
  el: "#app",
  router,
  store,
  render: (h) => h(App),
});
```

### 钩子函数参数

在 Vue 2 中，自定义指令也像组件那样存在钩子函数：

- `bind`：只调用一次，指令第一次绑定到元素时调用。在这里可以进行一次性的初始化设置

- `inserted`：被绑定元素插入父节点时调用 (仅保证父节点存在，但不一定已被插入文档中)

- `update`：所在组件的 `VNode` 更新时调用，但是可能发生在其子 `VNode` 更新之前。

  ​                 指令的值可能发生了改变，也可能没有。

  ​                但是可以通过比较更新前后的值来忽略不必要的模板更新

- `componentUpdated`：指令所在组件的 `VNode` 及其子 `VNode` 全部更新后调用

- `unbind`：只调用一次，指令与元素解绑时调用

所有的钩子函数的参数都有以下：

- `el`：指令所绑定的元素，可以用来直接操作 `DOM`
- `binding`：一个对象，包含以下 `property`：
  - `name`：指令名，不包括 `v-` 前缀。
  - `value`：指令的绑定值，例如：`v-my-directive="1 + 1"` 中，绑定值为 `2`。
  - `oldValue`：指令绑定的前一个值，仅在 `update` 和 `componentUpdated` 钩子中可用。无论值是否改变都可用。
  - `expression`：字符串形式的指令表达式。例如 `v-my-directive="1 + 1"` 中，表达式为 `"1 + 1"`。
  - `arg`：传给指令的参数，可选。例如 `v-my-directive:foo` 中，参数为 `"foo"`。
  - `modifiers`：一个包含修饰符的对象。例如：`v-my-directive.foo.bar` 中，修饰符对象为 `{ foo: true, bar: true }`
- `vnode`：`Vue` 编译生成的虚拟节点
- `oldVnode`：上一个虚拟节点，仅在 `update` 和 `componentUpdated` 钩子中可用

> 除了 `el` 之外，其它参数都应该是只读的，切勿进行修改。如果需要在钩子之间共享数据，建议通过元素的 `dataset` 来进行

### directive 用法

可以使用自定义指令代替一些通用的样式和逻辑，比如点击更新按钮显示加载中的遮罩

如果是下列方式则是侵入式的，每写一个新的组件都要加上这行代码 :visible，而这里只需要在父组件（父元素）上添加自定义指令即可

<img src="https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1652776187672-16e87b62-48f2-4dc6-b1d0-dbe9dfaaac40.jpeg" alt="img" style="zoom: 80%;" />

一个指令定义对象可以提供如下几个钩子函数 (均为可选)：

● bind：只调用一次，指令第一次绑定到元素时调用。在这里可以进行一次性的初始化设置。

● inserted：被绑定元素插入父节点时调用 (仅保证父节点存在，但不一定已被插入文档中)。

● update：所在组件的 VNode 更新时调用，但是可能发生在其子 VNode 更新之前。指令的值可能发生了改变，也可能没有。

   但是你可以通过比较更新前后的值来忽略不必要的模板更新。

```js
<div id="root">
   <div v-loading="isLoading">{{data}}</div>
   <button @click="update">更新</button>
</div>

// v-loading 对应 Vue.directive 的第一个参数 'loading'，isLoading 对应第二个参数 指令自定义对象

Vue.directive('loading', {
  // el 表示 <div id="root">...</div>，binding 表示绑定的 data 中的 isLoading，
  // vnode 是 Vue 根据 <div id="root">...</div> 编译生成的虚拟节点对象
  update(el, binding, vnode) {
    // 当 isLoading 为 true 时执行
    if (binding.value) {
      const div = document.createElement('div')
      div.innerText = '加载中...'
      div.setAttribute('id', 'loading')
      div.style.position = 'absolute'
      div.style.left = 0
      div.style.top = 0
      div.style.width = '100%'
      div.style.height = '100%'
      div.style.display = 'flex'
      div.style.justifyContent = 'center'
      div.style.alignItems = 'center'
      div.style.color = 'white'
      div.style.background = 'rgba(0, 0, 0, .7)'
      document.body.append(div)
    } 
    // 当 isLoading 为 false 时执行
    else {
      document.body.removeChild(document.getElementById('loading'))
    }
  }
})

new Vue({
  el: '#root',
  data() {
    return {
      isLoading: false,
      data: ''
    }
  },
  methods: {
    update() {
      this.isLoading = true
      setTimeout(() => {
        this.data = '用户数据'
        this.isLoading = false
      }, 3000)
    }
  }
})
```

或者

<img src="https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1652776317398-e401e2f2-ed40-41a4-8c77-7028992b150f.jpeg" alt="img" style="zoom: 67%;" />

参数 el、binding、vnode 如下

![img](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1652776351757-93de1154-1e6e-4151-a23e-cc6a276b6e84.jpeg)

效果图

![img](https://cdn.nlark.com/yuque/0/2022/gif/1614731/1652776385452-16a15abe-edbe-42c5-ada8-ca8a0f4b424a.gif)

### 应用场景

使用自定义指令可以满足我们日常一些场景，这里给出几个自定义指令的案例：

- 防抖
- 图片懒加载
- 一键 Copy 的功能

#### 输入框防抖

防抖这种情况设置一个`v-throttle`自定义指令来实现

举个例子：

```js
// 1.设置v-debounce自定义指令
Vue.directive('debounce', {
  bind: (el, binding) => {
    let debounceTime = binding.value; // 防抖时间
    if (!debounceTime) { // 用户若不设置防抖时间，则默认2s
      debounceTime = 2000;
    }
    let timer;
    el.addEventListener('click', event => {
      if (!timer) { // 第一次执行
        timer = setTimeout(() => {
          timer = null;
        }, debounceTime);
      } else {
        event && event.stopImmediatePropagation();
      }
    }, true);
  },
});
// 2.为button标签设置v-debounce自定义指令
<button @click="sayHello" v-debounce>提交</button>
```

#### 图片懒加载

设置一个`v-lazy`自定义指令完成图片懒加载

```js
const LazyLoad = {
    // install方法
    install(Vue,options){
    	// 代替图片的loading图
        let defaultSrc = options.default;
        Vue.directive('lazy',{
            bind(el,binding){
                LazyLoad.init(el,binding.value,defaultSrc);
            },
            inserted(el){
                // 兼容处理
                if('IntersectionObserver' in window){
                    LazyLoad.observe(el);
                }else{
                    LazyLoad.listenerScroll(el);
                }
                
            },
        })
    },
    // 初始化
    init(el,val,def){
        // data-src 储存真实src
        el.setAttribute('data-src',val);
        // 设置src为loading图
        el.setAttribute('src',def);
    },
    // 利用IntersectionObserver监听el
    observe(el){
        let io = new IntersectionObserver(entries => {
            let realSrc = el.dataset.src;
            if(entries[0].isIntersecting){
                if(realSrc){
                    el.src = realSrc;
                    el.removeAttribute('data-src');
                }
            }
        });
        io.observe(el);
    },
    // 监听scroll事件
    listenerScroll(el){
        let handler = LazyLoad.throttle(LazyLoad.load,300);
        LazyLoad.load(el);
        window.addEventListener('scroll',() => {
            handler(el);
        });
    },
    // 加载真实图片
    load(el){
        let windowHeight = document.documentElement.clientHeight
        let elTop = el.getBoundingClientRect().top;
        let elBtm = el.getBoundingClientRect().bottom;
        let realSrc = el.dataset.src;
        if(elTop - windowHeight<0&&elBtm > 0){
            if(realSrc){
                el.src = realSrc;
                el.removeAttribute('data-src');
            }
        }
    },
    // 节流
    throttle(fn,delay){
        let timer; 
        let prevTime;
        return function(...args){
            let currTime = Date.now();
            let context = this;
            if(!prevTime) prevTime = currTime;
            clearTimeout(timer);
            
            if(currTime - prevTime > delay){
                prevTime = currTime;
                fn.apply(context,args);
                clearTimeout(timer);
                return;
            }

            timer = setTimeout(function(){
                prevTime = Date.now();
                timer = null;
                fn.apply(context,args);
            },delay);
        }
    }

}
export default LazyLoad;
```

使用

在`main.js`里添加

```js
import LazyLoad from './components/LazyLoad';

Vue.use(LazyLoad,{
    default:'https://tva1.sinaimg.cn/large/007S8ZIlgy1gfyof9vr4mj3044032dfl.jpg'
});
```

将组件内 标签的 src 换成 v-lazy

```vue
<img v-lazy="xxx.jpg" />
```

#### 一键 copy

```js
import { Message } from 'ant-design-vue';

const vCopy = { //
  /*
    bind 钩子函数，第一次绑定时调用，可以在这里做初始化设置
    el: 作用的 dom 对象
    value: 传给指令的值，也就是我们要 copy 的值
  */
  bind(el, { value }) {
    el.$value = value; // 用一个全局属性来存传进来的值，因为这个值在别的钩子函数里还会用到
    el.handler = () => {
      if (!el.$value) {
      // 值为空的时候，给出提示，我这里的提示是用的 ant-design-vue 的提示，你们随意
        Message.warning('无复制内容');
        return;
      }
      // 动态创建 textarea 标签
      const textarea = document.createElement('textarea');
      // 将该 textarea 设为 readonly 防止 iOS 下自动唤起键盘，同时将 textarea 移出可视区域
      textarea.readOnly = 'readonly';
      textarea.style.position = 'absolute';
      textarea.style.left = '-9999px';
      // 将要 copy 的值赋给 textarea 标签的 value 属性
      textarea.value = el.$value;
      // 将 textarea 插入到 body 中
      document.body.appendChild(textarea);
      // 选中值并复制
      textarea.select();
      // textarea.setSelectionRange(0, textarea.value.length);
      const result = document.execCommand('Copy');
      if (result) {
        Message.success('复制成功');
      }
      document.body.removeChild(textarea);
    };
    // 绑定点击事件，就是所谓的一键 copy 啦
    el.addEventListener('click', el.handler);
  },
  // 当传进来的值更新的时候触发
  componentUpdated(el, { value }) {
    el.$value = value;
  },
  // 指令与元素解绑的时候，移除事件绑定
  unbind(el) {
    el.removeEventListener('click', el.handler);
  },
};

export default vCopy;
```

新建一个 js （ directives.js ）文件来注册所有的全局指令

```javascript
import copy from './v-copy';
// 自定义指令
const directives = {
  copy,
};
// 这种写法可以批量注册指令
export default {
  install(Vue) {
    Object.keys(directives).forEach((key) => {
      Vue.directive(key, directives[key]);
    });
  },
};
```

最后，在 main.js 中引入：

```javascript
import Vue from 'vue';
import Directives from './directives';

Vue.use(Directives);
```

关于自定义指令还有很多应用场景，如：

拖拽指令、页面水印、权限校验、为组件添加`loading`效果、代码埋点,根据操作类型定义指令等等应用场景

## Vue.extend（构造组件）

### Vue.extend 的用法

```vue
<div id="root">
  <Test :msg="message"></Test>
</div>

<script>
const component = Vue.extend({
  template: '<div>{{msg}}</div>',
  props: {
    msg: {
      type: String,
      default: 'default message'
    }
  }
})

Vue.component('Test', component);

new Vue({
  el: '#root',
  data() {
    return {
      message: "Test Extend Component"
    }
  }
})
</script>
```

![img](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1652776543904-83f82eaa-0c39-4246-aa73-56936c0dc3c5.jpeg)

### Vue.extend 的进阶用法

```vue
<div id="root">
  <button @click="showLoading">显示Loading</button>
</div>

<script>
function Loading(msg) {
  const LoadingComponent = Vue.extend({
    template: '<div id="loading-wrapper">{{msg}}</div>',
    props: {
      msg: {
        type: String,
        default: msg
      }
    },
    name: 'LoadingComponent'
  })

  const div = document.createElement('div');
  div.setAttribute('id', 'loading-wrapper');
  document.body.append(div);

  new LoadingComponent().$mount('#loading-wrapper'); // 替换掉上面三行创建的 div

  return () => {
    document.body.removeChild(document.getElementById('loading-wrapper'))
  }
}

Vue.prototype.$loading = Loading;

new Vue({
  el: '#root',
  methods: {
    showLoading() {
      const hide = this.$loading('正在加载，请稍等...')
      setTimeout(() => {
        hide() // 相当于执行一次 removeChild
      }, 2000)
    }
  }
})
</script>

<style>
#loading-wrapper {
  position: fixed;
  top: 0;
  left: 0;
  display: flex;
  justify-content: center;
  align-items: center;
  width: 100%;
  height: 100%;
  background: rgba(0,0,0,.7);
  color: #fff;
}
</style>
```

与自定义指令不同，这是主动触发的

效果图

![img](https://cdn.nlark.com/yuque/0/2022/gif/1614731/1652776668711-ad8c40c0-b762-40f1-bb1e-941fe030225a.gif)

## Vue.use 使用插件（组件、方法、指令、库）

**插件 (Plugins) 是一种能为 Vue 添加全局功能的工具代码。**

**插件的功能范围没有严格的限制——一般有下面几种：**

- **添加全局方法或者属性。如: `vue-custom-element`**
- **添加全局资源：指令/过滤器/过渡等。如 `vue-touch`**
- **通过全局混入来添加一些组件选项。如`vue-router`**
- **添加 `Vue` 实例方法，通过把它们添加到 `Vue.prototype` 上实现。**
- **一个库，提供自己的 `API`，同时提供上面提到的一个或多个功能。如`vue-router｀**

#### 编写插件

`vue`插件的实现应该暴露一个 `install` 方法。

这个方法的第一个参数是 `Vue` 构造器，第二个参数是一个可选的选项对象。

```js
MyPlugin.install = function (Vue, options) {
  // 1. 添加全局方法或 property
  Vue.myGlobalMethod = function () {
    // 逻辑...
  }

  // 2. 添加全局指令
  Vue.directive('my-directive', {
    bind (el, binding, vnode, oldVnode) {
      // 逻辑...
    }
    ...
  })

  // 3. 混入组件选项
  Vue.mixin({
    created: function () {
      // 逻辑...
    }
    ...
  })

  // 4. 添加实例方法
  Vue.prototype.$myMethod = function (methodOptions) {
    // 逻辑...
  }
}
```

1. Vue 的插件是一个对象
2. 插件对象必须有`install`字段
3. `install`字段是一个函数

#### 插件注册

插件的注册通过`Vue.use()`的方式进行注册（安装），第一个参数为插件的名字，第二个参数是可选择的配置项

```js
Vue.use(插件名字,{ /* ... */} )
```

注意的是：

1. 初始化插件对象需要通过`Vue.use()`
2. 注册插件的时候，需要在调用 `new Vue()` 启动应用之前完成
3. `Vue.use`会自动阻止多次注册相同插件，只会注册一次

```vue
<div id="root">
  <button @click="showLoading">显示Loading</button>
</div>

<script>
const loadingPlugin = {
  install: function (vm) {
    const LoadingComponent = vm.extend({
      template: '<div id="loading-wrapper">{{msg}}</div>',
      props: {
        msg: {
          type: String,
          default: 'loading...'
        }
      }
    }, 'LoadingComponent')
    function Loading(msg) {
      const div = document.createElement('div')
      div.setAttribute('id', 'loading-wrapper')
      document.body.append(div)
      new LoadingComponent({
        props: {
          msg: {
            type: String,
            default: msg
          }
        }
      }).$mount('#loading-wrapper')
      return () => {
        document.body.removeChild(document.getElementById('loading-wrapper'))
      }
    }
    vm.prototype.$loading = Loading
  }
}

Vue.use(loadingPlugin)

new Vue({
  el: '#root',
  methods: {
    showLoading() {
      const hide = this.$loading('正在加载，请稍等...')
      setTimeout(() => {
        hide()
      }, 2000)
    }
  }
})
</script>

<style>
#loading-wrapper {
  position: fixed;
  top: 0;
  left: 0;
  display: flex;
  justify-content: center;
  align-items: center;
  width: 100%;
  height: 100%;
  background: rgba(0, 0, 0, .7);
  color: #fff;
}
</style>
```

### Vue  插件用法

src\components\common\toast\Toast.vue

```vue
<template>
  <div class="toast" v-show="isShow">
    <div>{{message}}</div>
  </div>
</template>

<script>
  export default {
    name: '',
    data () {
      return {
        isShow: false,
        message: ''
      }
    },
    methods: {
      show(message='默认文字', duration=1000) {
        this.isShow = true;
        this.message = message;
        setTimeout(() => {
          this.isShow = false;
          this.message = '';
        }, duration)
      }
    }
  }
</script>

<style scoped>
  .toast {
    position: fixed;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    z-index: 2;
    padding: 8px 10px;
    color: #fff;
    background-color: rgba(0, 0, 0, .75); 
  }
</style>
```

src\components\common\toast\index.js

```js
import Toast from './Toast';

const obj = {};

obj.install = function(Vue) {
  const toastConstructor = Vue.extend(Toast);
  const toast = new toastConstructor();
  toast.$mount(document.createElement('body'));
  document.body.appendChild(toast.$el);
  Vue.prototype.$toast = (message, duration = 1250) => {
    toast.message = message;
    toast.isShow = true;
    setTimeout(() => {
      toast.isShow = false;
    }, duration);
  };
}

export default obj;
```

src\main.js

```js
import toast from 'common/toast';

Vue.use(toast)

new Vue({
  router,
  store,
  render: h => h(App)
}).$mount('#app')
```

组件里使用

```js
this.$toast(res, 1500)
```

## Vue.observable

**用于小型状态管理，是对源码 observe 方法的封装**

```html
<html>
  <head>
    <title>Vue.observable</title>
    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
  </head>
  <body>
    <div id="root">
      {{message}}
      <button @click="change">Change</button>
    </div>
    <script>
      const state = Vue.observable({ message: 'Vue 2.6' })
      const mutation = {
        setMessage(value) {
          state.message = value
        }
      }
      new Vue({
        el: '#root',
        // 要使用计算属性来跟踪 state.message 变化
        computed: {
          message() {
            return state.message
          }
        },
        methods: {
          change() {
            mutation.setMessage('Vue 3.0')
          }
        }
      })
    </script>
  </body>
</html>
```

## 混入

混入 (mixin) 提供了一种非常灵活的方式，来分发 Vue 组件中的可复用功能。

一个混入对象可以包含任意组件选项。

当组件使用混入对象时，所有混入对象的选项将被“混合”进入该组件本身的选项。

https://v2.cn.vuejs.org/v2/guide/mixins.html

src\commonjs\mixin.js

```js
import { debounce } from "commonjs/utils.js";

import BackUp from "content/backUp/BackUp";

export const itemListenerMixin = {
  data() {
    return {
      itemImgListener: null
    }
  },
  mounted() {
    const refresh = debounce(this.$refs.scroll.refresh, 50);
    this.itemImgListener = () => refresh();
    this.$bus.$on("itemImgLoad", this.itemImgListener);
  }
}

export const backUpMixin = {
  components: {
    BackUp
  },
  data() {
    return {
      isShowBackUp: false
    }
  },
  methods: {
    backClick() {
      this.$refs.scroll.scrollTo(0, 0, 300);
    },
    listenShowBackUp(position) {
      this.isShowBackUp = -position.y > 1000;
    }
  }
}

export const tabControlMixin = {
  data() {
    return {
      currentType: 'pop'
    };
  },
  methods: {
    tabClick(index) {
      switch (index) {
        case 0:
          this.currentType = 'pop';
          break;
        case 1:
          this.currentType = 'new';
          break;
        case 2:
          this.currentType = 'sell';
          break;
      }
      // console.log(this.currentType);
    }
  }
};
```

src\views\detail\Detail.vue

```js
import {itemListenerMixin, backUpMixin } from 'commonjs/mixin';


export default {
  name: 'Detail',
  mixins: [itemListenerMixin, backUpMixin],
  data () {
    return {
      iid: null,
      topImages: [],
      goods: {},
      shop: {},
      detailInfo: {},
      paramInfo: {},
      commentInfo: {},
      recommends: [],
      titleTopY: [],
      getTitleTopY: null,
      currentIndex: 0,
    }
  },
  ...
}  
```

## 其它

当 `<style>` 标签有 scoped 属性时，它的 CSS 只作用于当前组件中的元素。

这类似于 Shadow DOM 中的样式封装。它有一些注意事项，但不需要任何 polyfill。

它通过使用 PostCSS 来实现以下转换：

```vue
<template>
  <div class="example">hi</div>
</template>

<style scoped>
.example {
  color: red;
}
</style>
```

转换结果：

```vue
<template>
  <div class="example" data-v-f3f3eg9>hi</div>
</template>

<style>
.example[data-v-f3f3eg9] {
  color: red;
}
</style>
```

[Vue 风格指南](https://v2.cn.vuejs.org/v2/style-guide/)

![img](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1652776890647-ef790e55-9875-49e5-803c-715e9e960328.jpeg)

class 名即便你不使用类似 BEM 的策略，添加一个 app 专属或组件专属的前缀 (比如 ButtonClose-icon) 也可以提供很多保护。

组件名应该始终是多个单词的，根组件 App 以及 `<transition>`、`<component>` 之类的 Vue 内置组件除外。

单文件组件的文件名应该要么始终是单词大写开头 (PascalCase， 大驼峰)，要么始终是横线连接 (kebab-case)。

在单文件组件和字符串模板中组件名应该总是 PascalCase 的——但是在 DOM 模板中总是 kebab-case 的。

注册组件始终为 PascalCase， 大驼峰

```js
Vue.component('MyComponent', {
  // ...
})
```

```js
import MyComponent from './MyComponent.vue'
export default {
  name: 'MyComponent',
  // ...
}
```

html 特性 是不区分大小写的，v-bind 的 html特性 应改为 kebab-case（短横线）命名，不能使用 camelCased（驼峰式，小驼峰）命名：

```vue
<div id="app">
    <input type="text" v-model="message">
    <child v-bind:my-message="message"></child>
    <!--此处的 my-message 只能是短横线命名（若为驼峰式 myMessage 则全部转换为小写 mymessage）-->
</div>

Vue.component('child',{
    //  props:['my-message'],也可以
    props:['myMessage'], // props中传递的数据可以为驼峰式也可以为短横线式，他们在此处是相互转换的
    template:'<p>{{myMessage}}</p>'
    // 此处有限制，是字符串模板，{{ }}语法中不能是短横线连接方式。此处只能是驼峰命名方式。若为短横线的命名方式，则会报错。
});
```

v-on 的事件名则需要完全匹配监听这个事件所用的名称：

```vue
this.$emit('myEvent')
<!-- 没有效果 -->
<my-component v-on:my-event="doSomething"></my-component>
```

真正的项目会将 `<template>` 和 `<script>`、`<style>`会编译成一个组件对象，导出为一个组件注册到主页上

组件对象里不会有 `<template>`，

因为组件对象会渲染成一个 render 函数，所以真正的项目里事件名可以采用 camelCased（驼峰式）命名，

因为如果使用字符串模板，那么这个限制就不存在了

在 Demo 里事件名必须是 kebab-case（短横线）命名，推荐使用 kebab-case 形式来事件名

> 总结：
>
> 组件名单词大写开头 (PascalCase， 大驼峰)、
>
> HTML 中的 attribute 名 、v-bind 和 v-on 使用 kebab-case（短横线）命名 （`<template>` 里的标签上）
>
> {{}}  使用 camelCased（小驼峰）命名 （`<template>` 里的元素内容）
>
> props 、函数和、变量使用 camelCased（小驼峰）命名 （`<script>` 标签内）
>
> props 在模板和 JSX 中应该始终使用 kebab-case 风格（短横线），例如在模板使用 `user-name` 的 HTML attribute 代替 `userName`的 props  传递。
>







**使用 Vue 来实现 RBAC 权限管理**

```js
// 定义角色
const roles = {
  guest: ['read'],
  user: ['read', 'write'],
  admin: ['read', 'write', 'admin']
};

// 定义用户
const users = {
  alice: ['user'],
  bob: ['user', 'admin'],
  carol: ['admin']
};

// 定义路由规则
const routes = [
  {
    path: '/',
    roles: ['guest', 'user', 'admin'],
    component: Home
  },
  {
    path: '/posts',
    roles: ['user', 'admin'],
    component: Posts
  },
  {
    path: '/admin',
    roles: ['admin'],
    component: Admin
  }
];

// 定义路由守卫
const router = new VueRouter({
  routes
});
router.beforeEach((to, from, next) => {
  const user = users[getCurrentUser()]; // 获取当前用户
  const routeRoles = to.meta.roles; // 获取当前路由的角色限制
  const intersection = routeRoles.filter(role => user.includes(role)); // 取出用户与路由角色的交集

  if (intersection.length > 0) {
    // 如果有交集，说明用户具有访问该路由的权限
    next();
  } else {
    // 否则，跳转到指定页面
    next('/');
  }
});

const app = new Vue({
  router
}).$mount('#app');

```

在这个示例中，我们定义了三种角色：guest、user 和 admin。每种角色都有一些特定的权限，例如 guest 只能进行读操作，而 admin 能进行读、写和管理操作。

然后，我们定义了三个用户：alice、bob 和 carol，并为每个用户分配了一些角色。

例如，alice 具有 user 角色，因此可以进行读和写操作；bob 具有 user 和 admin 角色，因此可以进行读、写和管理操作。

接着，我们定义了三个路由：/、/posts 和 /admin。每个路由都只能被指定的角色访问。

例如，/ 路由可以被任何角色访问，/posts 路由只能被 user 和 admin 角色访问，/admin 路由只能被 admin 角色访问。

最后，我们定义了路由守卫，在用户进入某个路由时，会检查用户是否具有访问该路由的权限。如果用户具有权限，就会让用户进入该路由。

对于用户没有权限访问的路由，可以在路由守卫中通过 next() 方法跳转到指定页面。例如，可以跳转到一个“没有权限”的提示页面，或者直接跳转到首页。

路由守卫的实现方式不唯一，可以根据实际需求和开发习惯进行调整。

例如，可以在 Vue 实例中通过 beforeEach() 方法直接定义路由守卫，也可以在单独的路由守卫模块中定义。

**使用 Node.js 来实现 RBAC 权限管理**

```js
const express = require('express');
const app = express();

// 定义角色
const roles = {
  guest: ['read'],
  user: ['read', 'write'],
  admin: ['read', 'write', 'admin']
};

// 定义用户
const users = {
  alice: ['user'],
  bob: ['user', 'admin'],
  carol: ['admin']
};

// 定义接口规则
const apiRoutes = [
  {
    path: '/posts',
    roles: ['user', 'admin'],
    methods: ['GET', 'POST']
  },
  {
    path: '/posts/:id',
    roles: ['user', 'admin'],
    methods: ['GET', 'PUT', 'DELETE']
  },
  {
    path: '/admin',
    roles: ['admin'],
    methods: ['GET', 'POST', 'PUT', 'DELETE']
  }
];

// 定义接口守卫
apiRoutes.forEach(route => {
  app[route.method](route.path, (req, res, next) => {
    const user = users[getCurrentUser()]; // 获取当前用户
    const routeRoles = route.roles; // 获取当前接口的角色限制
    const intersection = routeRoles.filter(role => user.includes(role)); // 取出用户与路由角色的交集

    if (intersection.length > 0) {
      // 如果有交集，说明用户具有访问该接口的权限
      next();
    } else {
      // 否则，返回错误信息
      res.status(401).json({
        error: 'Unauthorized'
      });
    }
  });
});

app.listen(3000);

```

在这个示例中，我们定义了三种角色：guest、user 和 admin。

每种角色都有一些特定的权限，例如 guest 只能进行读操作，而 admin 能进行读、写和管理操作。

然后，我们定义了三个用户：alice、bob 和 carol，并为每个用户分配了一些角色。

例如，alice 具有 user 角色，因此可以进行读和写操作；bob 具有 user 和 admin 角色，因此可以进行读、写和管理操作。

接着，我们定义了三个接口：/posts、/posts/:id 和 /admin。

每个接口都只能被指定的角色访问，并且每个接口只能接受指定的 HTTP 方法。

例如，/posts 接口只能被 user 和 admin 角色访问，并且只能接受 GET 和 POST 方法。

最后，我们定义了接口守卫，在用户进入某个接口时，会检查用户是否具有访问该接口的权限。

如果用户具有权限，就会执行后续操作；否则，会返回 401 错误。

接口守卫的实现方式不唯一，可以根据实际需求和开发习惯进行调整。

通过这个示例，我们可以知道如何使用 Node.js 实现 RBAC 权限管理的后端部分。

例如，我们定义了三种角色，为每个用户分配了一些角色，定义了接口规则，并通过接口守卫来控制用户访问权限。

然而，这只是一个简单的示例代码，实际开发中可能会涉及到更多复杂的情况。

例如：

接口规则可能会更为复杂，需要更多的角色和权限；

用户信息可能会来自数据库或其他存储系统；

接口守卫也可能会涉及到更多的业务逻辑。



**Vue 递归组件实现无限层级的菜单**

例如，可以定义一个名为 Menu 的组件，该组件会渲染一个菜单项。然后，在菜单项中，我们可以再次调用 Menu 组件来渲染子菜单。

首先，定义一个名为 Menu 的递归组件，该组件接收一个 items 属性，表示当前菜单项的子菜单列表。

首先，创建一个名为 Menu.vue 的文件，并在该文件中定义一个名为 Menu 的递归组件。

```vue
<template>
  <ul>
    <li v-for="item in items">
      {{ item.label }}
      <Menu v-if="item.children" :items="item.children" />
    </li>
  </ul>
</template>

<script>
export default {
  name: 'Menu',
  props: ['items']
};
</script>
```

然后，我们可以在父组件模板中调用 Menu 组件，定义一个包含菜单数据的数组，并传递给 Menu 组件。

```vue
<template>
  <div>
    <Menu :items="menus" />
  </div>
</template>

<script>
import Menu from './Menu.vue';

export default {
  name: 'App',
  components: {
    Menu
  },
  data() {
    return {
      menus: [
        { label: 'Home', children: [
          { label: 'About' }
        ]},
        { label: 'Blog', children: [
          { label: 'Category 1', children: [
            { label: 'Article 1' },
            { label: 'Article 2' }
          ]},
          { label: 'Category 2' }
        ]},
        { label: 'Contact' }
      ]
    };
  }
};
</script>
```

最后，我们可以在 index.html 中引用这个组件，并将它渲染到页面上。

```html
<!DOCTYPE html>
<html>
<head>
  <title>Vue Menu</title>
</head>
<body>
  <div id="app"></div>
  <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
  <script src="./App.vue"></script>
  <script>
    new Vue({
      el: '#app',
      render: h => h(App)
    });
  </script>
</body>
</html>
```

这样，我们就可以使用 Vue 的单文件组件的形式来实现可以有无限层级的的菜单

**Vue 不用递归组件实现无限层级的菜单**

可以定义一个名为 Menu 的组件，该组件接收一个 items 属性，表示当前菜单项的子菜单列表。

```jsx
const Menu = {
  props: {
    items: {
      type: Array,
      default: () => [],
    },
  },
  methods: {
    renderMenu(items) {
      return items.map(item => (
        <li>
          {item.label}
          {item.children && (
            <ul>
              {this.renderMenu(item.children)}
            </ul>
          )}
        </li>
      ));
    },
  },
  render() {
    return (
      <ul>
        {this.renderMenu(this.items)}
      </ul>
    );
  },
};

const App = {
  data() {
    return {
      menus: [
        { label: 'Home', children: [
          { label: 'About' }
        ]},
        { label: 'Blog', children: [
          { label: 'Category 1', children: [
            { label: 'Article 1' },
            { label: 'Article 2' }
          ]},
          { label: 'Category 2' }
        ]},
        { label: 'Contact' }
      ],
    };
  },
  components: {
    Menu,
  },
};
```

然后，在父组件中，我们可以定义一个包含菜单数据的数组，并传递给 Menu 组件。

```html
<div>
  <Menu :items="menus" />
</div>
```

最后，我们可以看到，无论菜单有多少层级，都可以用这种方法实现。

**使用 React 的递归组件来实现可以有无限层级的菜单**

例如，可以定义一个名为 Menu 的组件，该组件会渲染一个菜单项。然后，在菜单项中，我们可以再次调用 Menu 组件来渲染子菜单。

首先，定义一个名为 Menu 的递归组件，该组件接收一个 items 属性，表示当前菜单项的子菜单列表。

```jsx
const Menu = ({ items }) => {
  return (
    <ul>
      {items.map(item => (
        <li>
          {item.label}
          {item.children && <Menu items={item.children} />}
        </li>
      ))}
    </ul>
  );
};
```

然后，在父组件中，我们可以定义一个包含菜单数据的数组，并传递给 Menu 组件。

```jsx
const App = () => {
  const menus = [    { label: 'Home', children: [      { label: 'About' }    ]},
    { label: 'Blog', children: [
      { label: 'Category 1', children: [
        { label: 'Article 1' },
        { label: 'Article 2' }
      ]},
      { label: 'Category 2' }
    ]},
    { label: 'Contact' }
  ];

  return (
    <div>
      <Menu items={menus} />
    </div>
  );
};
```

最后，我们可以在 HTML 文件中引用 React 库，并将组件渲染到页面上。

```html
<!DOCTYPE html>
<html>
<head>
  <title>React Menu</title>
</head>
<body>
  <div id="app"></div>
  <script src="https://unpkg.com/react@17/umd/react.development.js"></script>
  <script src="https://unpkg.com/react-dom@17/umd/react-dom.development.js"></script>
  <script src="./App.js"></script>
  <script>
    const { render } = ReactDOM;
    const { createElement: h } = React;

    render(h(App), document.getElementById('app'));
  </script>
</body>
</html>
```

**React 除了使用递归组件外，我们还可以使用 map() 方法和递归函数来实现可以有无限层级的菜单**

例如，可以定义一个名为 Menu 的组件，该组件接收一个 items 属性，表示当前菜单项的子菜单列表。

```jsx
const Menu = ({ items }) => {
  const renderMenu = items => {
    return items.map(item => (
      <li>
        {item.label}
        {item.children && (
          <ul>
            {renderMenu(item.children)}
          </ul>
        )}
      </li>
    ));
  };

  return <ul>{renderMenu(items)}</ul>;
};
```

然后，在父组件中，我们可以定义一个包含菜单数据的数组，并传递给 Menu 组件。

```jsx
const App = () => {
  const menus = [    { label: 'Home', children: [      { label: 'About' }    ]},
    { label: 'Blog', children: [
      { label: 'Category 1', children: [
        { label: 'Article 1' },
        { label: 'Article 2' }
      ]},
      { label: 'Category 2' }
    ]},
    { label: 'Contact' }
  ];

  return (
    <div>
      <Menu items={menus} />
    </div>
  );
};
```

最后，我们可以在页面中渲染 App 组件，即可看到无限层级的菜单。

```js
ReactDOM.render(<App />, document.getElementById('root'));
```

这样，我们就可以不使用递归组件来实现无限层级的菜单了。































































