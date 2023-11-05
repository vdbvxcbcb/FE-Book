# React 基础

## 初识 React

### 安装 yarn

```bash
npm install -g yarn
```

npm 从 5.2 版开始，增加了 npx 命令，使用 npx 命令可以调用项目内 node_modules/.bin 下的模块，而不用再需要全局安装这个模块。

该命令执行的时候会自动安装 npx 。

npx 执行时的查找顺序如下：

默认情况下，首先检查路径中是否存在要执行的包（即在项目中）；

如果存在，它将执行；

若不存在，意味着尚未安装该软件包，npx 将安装其最新版本，然后执行它。

上述行为是 npx 的默认行为之一，但它具有可用来阻止的标志。

例如，如果运行 npx package-name --no-install，意味着告诉 npx ，它应该仅执行 package-name，如果之前未安装，则不安装。

### yarn 和 npm 的命令对比

在运行脚本的时候，可以直接用 yarn 来代替 npm run ，例如 yarn dev 相当于 npm run dev 。

yarn 默认绑定的是 https://registry.yarnpkg.com 的下载源，如果包的下载速度太慢，也可以配置镜像源

```bash
# 查看 npm 当前镜像源
npm config get registry

# 设置 npm 镜像源为官方镜像
npm config set registry https://registry.npmjs.org

# 设置 npm 镜像源为淘宝镜像
npm config set registry https://registry.npmmirror.com

# 删除下载源
npm config rm registry

# 查看 yarn 当前镜像源
yarn config get registry  // 

# 设置 yarn 镜像源为淘宝镜像
yarn config set registry https://registry.npmmirror.com

# 删除镜像源（注意这里是 delete ）
yarn config delete registry
```

| Npm                                                          | Yarn                          |
| :----------------------------------------------------------- | ----------------------------- |
| npm init [-y]                                                | yarn init [-y]                |
| npm install [package] -g /  npm i [package] -g               | yarn global add [package]     |
| npm install / npm i                                          | yarn  install                 |
| npm install --save [package] / npm i -S [package]          生产阶段的依赖 dependencies | yarn add [package]            |
| npm install --save-dev [package] / npm i -D [package]   开发阶段的依赖 devDependencies | yarn add [package] [--dev/-D] |
| npm rebuild                                                  | yarn install --force          |
| npm uninstall  -g [package] -g / npm un -g [package]         | yarn global remove  [package] |
| npm uninstall [package] / npm un  [package]                  | yarn remove [package]         |
| npm uninstall --save [package]                               | yarn remove [package]         |
| npm uninstall --save-dev [package]                           | yarn remove [package]         |
| npm uninstall --save-optional [package]                      | yarn remove [package]         |
| npm cache clean                                              | yarn cache clean              |
| npm update  [package] --save                                 | yarn upgrade  [package]       |
| npm run/test                                                 | yarn run/test                 |
| npm publish/login/logout                                     | yarn publish/login/logout     |
| rm -rf node_modules && npm install                           | yarn upgrade                  |

--save 或者 -S 选项可以将依赖安装到本地，并列为生产依赖。

在实际使用的时候可以省略 --save 或者 -S ，因为它是默认选项，即默认 install 的都是生产以来。

开发依赖包也是会被安装到项目根目录下的 `node_modules` 目录里。

和生产依赖包不同的点在于，只在开发环境生效，构建部署到生产环境时可能会被抛弃，

一些只在开发环境下使用的包，就可以安装到开发依赖里，比如检查代码是否正确的 `ESLint` 就可以用这个方式安装。

```sh
npm i --save-dev   # 安装单个包
npm i --production # 安装 package.json 所有包，只用于生产环境
```

**yarn 升级依赖包**

0. 查看当前已安装库版本: yarn list xxx yyy

1. package.json 文件中如果版本号"^16.0.3"代表始终保持最新版本 

2. yarn upgrade 进行版本升级 

3. 在运行 yarn install 确保更新即可 

4. yarn list xxx 查看库目前版本

**npm 升级依赖包**

0.	npm list xxx yyy 查看当前库版本 

1.	同样的方法修改 package.json 

2.	npm update 即可升级

3.	npm install 确保更新即可

4.	npm audit fix **修复涉嫌有安全风险的库**

**依赖包版本号 ~ 和 ^ 的区别**

~ 会匹配最近的小版本依赖包，比如 ~1.2.3 会匹配所有 1.2.x 版本，但是不包括 1.3.0 。

^ 会匹配最新的大版本依赖包，比如 ^1.2.3 会匹配所有 1.x.x 的包，包括 1.3.0，但是不包括 2.0.0 。

~ x.y.z  x 与 y 保持不变，z 永远安装最新的版本

^ x.y.z  x 保持不变，y 与 z 永远安装最新的版本

^ 版本更新比较大，可能会造成项目代码错误。

使用 ~ 来标记版本号，这样可以保证项目不会出现大的问题，也能保证包中的小 bug 可以得到修复。

### 模块导入导出简介

以下方式需要 import {..., ...} 或 import * as ... 的形式才能引入， export 导出变量都是放到一个对象里

```js
// profile.js
export var firstName = 'Michael';
export var lastName = 'Jackson';
export var year = 1958;
// 或
var firstName = 'Michael';
var lastName = 'Jackson';
var year = 1958;

export { firstName, lastName, year };

// circle.js
export function area(radius) {
  return Math.PI * radius * radius;
}
export function circumference(radius) {
  return 2 * Math.PI * radius;
}

// main.js
import { area, circumference } from './circle';
console.log('圆面积：' + area(4));
console.log('圆周长：' + circumference(14));
// 或
import * as circle from './circle';
console.log('圆面积：' + circle.area(4));
console.log('圆周长：' + circle.circumference(14));
```

一个模块只能有一个默认输出，因此 export default 命令只能使用一次。

所以，import 命令后面不用加大括号，因为只能唯一对应 export default 命令。

本质上，export default 就是输出一个叫做 default 的变量或方法，然后系统允许你为它取任意名字。

不推荐使用 export default，使用 export default 会导致在混合使用 cjs+esm 的时候加载模块极易出现错误。

详细分析文章参考

[球球你们，别再用export default了。](https://zhuanlan.zhihu.com/p/97737035)

[深入解析ES Module（二）：彻底禁用default export](https://zhuanlan.zhihu.com/p/97335917)

如果想在一条 import 语句中，同时输入默认方法和其他接口，可以写成

```js
import _, { each, forEach } from 'lodash';
```

对应上面代码的 export 语句如下

```js
export default function (obj) {
  // ···
}

export function each(obj, iterator, context) {
  // ···
}

export { each as forEach };
```

最后一行的意思是，暴露出 forEach 接口，默认指向 each 接口，即 forEach 和 each 指向同一个方法。

所以 export 与 export default 的区别是：

1、输出多个 vs 输出一个，

2、输入名必须对应输出名 vs 任意输入名对应输出名 

另外，import 语句会执行所加载的模块，因此可以有下面的写法。

```js
import 'lodash';
```

上面代码仅仅执行 lodash 模块

### 安装 create-react-app

<https://github.com/facebook/create-react-app>

<https://www.cnblogs.com/zqxi/p/12498437.html>

<https://blog.csdn.net/muzi190/article/details/84108714>

<https://segmentfault.com/q/1010000015200625>

```bash
npm install -g create-react-app

npx create-react-app my-app 
或 npm init react-app my-app 
或 yarn create react-app my-app

cd my-app
npm start 或 yarn start

打开 http://localhost:3000 
```

### 文件的删减

我们先将不需要的文件统统删掉：

1.将 src 下的所有文件都删除

2.将 public 文件下除了 favicon.ico 和 index.html 之外的文件都删除掉

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1626684668117-533d86f6-65c1-4f4c-ac94-821ad31c9b1b.jpeg)

修改 index.html 文件：

删除选中的内容；这两行内容是我们之前引入的一个图标和 manifest 文件

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1626684771628-ff279f1a-16d4-4aa4-8ae0-49e9376243d4.jpeg)

### 开始编写代码

 src 目录下，创建一个 index.js 文件，因为这是 webpack 打包的入口。

在 index.js 中开始编写 React 代码：

在模块化开发中，我们需要手动导入 React、ReactDOM

```jsx
import React from "react";
import ReactDOM from 'react-dom';

ReactDOM.render(<h2>Hello React</h2>, document.getElementById("root"));
```

如果我们不希望直接在 ReactDOM.render 中编写过多的代码，就可以单独抽取一个组件 App.js：

```jsx
import React, { Component } from 'react';

export default class App extends Component {
  render() {
    return <h2>Hello App</h2>
  }
}
```

在 index.js 中引入 App，并且使用它：

```jsx
import React from "react";
import ReactDOM from 'react-dom';

import App from './App';

ReactDOM.render(<App/>, document.getElementById("root"));
```

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1626685012761-b7c270c9-2169-4419-8b22-83eba1d2b13a.jpeg)

## JSX 基础（JavaScript Syntax Extension ）

[https://react.docschina.org/docs/jsx-in-depth.html](https://react.docschina.org/docs/jsx-in-depth.html)

```jsx
<script type="text/babel">
  const element = <h2>Hello World</h2>
  ReactDOM.render(element, document.getElementById("app"));
</script>
```

- 这段 element 变量右侧赋值的标签语法是什么呢？

- 它不是一段字符串（因为没有使用引号包裹），它看起来是一段 HTML 元素，但是我们能在 js 中直接给一个变量赋值 html 吗？

- 其实是不可以的，如果我们将 type="text/babel" 去掉，那么就会出现语法错误；

- 但这就是一段 jsx 的语法，允许 HTML 中写 JS ；

### JSX 是什么？( JS  的进化版)

- JSX 是快速生成 React 元素的一种语法，

  实际是 React.createElement(component, props, ...children) 的语法糖，

  同时 JSX 是一种 JavaScript 的语法扩展（eXtension）。

- 它用于描述我们的 UI 界面，并且可以和 JavaScript 融合在一起使用。

- 它不同于 Vue 中的模板语法，

  不需要专门学习模板语法中的一些指令（比如 v-for、v-if、v-else、v-bind）

- **JSX 是 JS 里的 HTML 元素（无论是 DOM 类型还是组件类型），**

  **是标签 + 属性（JS 里叫属性， HTML 里叫特性） + 子级内容的语法糖。**

- **遇到 < ，JSX 就当 HTML 解析，遇到 { ，JSX 就当 JavaScript 解析**

- **JSX 可以被编译成“合法”的 JavaScript 代码**

- **也就是：在 JS 里写 HTML，利用 HTML 语法来创建虚拟 DOM , JSX = HTML in JS**

- **JSX 语法上更接近 JavaScript 而不是 HTML ，就像后端模板代码的写法，但是可以全面使用 JS**

```jsx
// 类组件示例
class Xxx extends Component { 
  render( return( JSX ) ); 
}

// 函数组件示例
const Xxx = () => ( JSX ) // 小括号具有欺骗编译器的效果，使编译器直接执行/返回小括号内容

const Xxx = () => { return (JSX) }

function Xxx() { 
  return (JSX) 
}
```

### 为什么 React  选择了 JSX ？

- React 认为渲染逻辑本质上与其他 UI 逻辑存在内在耦合

- 比如 UI 需要绑定事件（button、a 元素等等）；

- 比如 UI 中需要展示数据状态，在某些状态发生改变时，又需要改变 UI ；

- 它们之间是密不可分的，所以 React 没有将标记分离到不同的文件中，而是将它们组合到了一起，这就是组件（Component）

jquery 还有 vm 模板好用，那么为什么要迁移到 React + jsx 中来呢？

因为用 jsx + React 我们可以使用 js 的所有语法和能力，

而使用模板引擎通常只能使用其提供的有限的模板语法。

举个栗子，循环列表，在 vm 中我们只能用这样的语法写:

```html
<ul> 
  #foreach ( $product in $allProducts ) 
    <li> $product </li> 
  #end 
</ul>
```

而在 jsx 中, 我们可以:

```jsx
// 用 map 写
let list = items => items.map( p => <li> {p} </li> );
                              
// 用循环写
let list = [];
for (let i = 0 ; i < items.length: i++) {
  list.push(<li>{items[i]}</li>);
}

// 用 forEach 写
let list = [];
items.forEach(item => list.push(<li>{item}</li>))

// 用 while 写
                                
// 用 for...of 写
                                
// ...
```

总之你爱怎么写就怎么写。这极大地拓展了前端写界面的能力，前端同学心里美滋滋。

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1615551200471-da8e7a61-01ba-41e0-9e2d-bc81fb7688a1.jpeg)

**总结**

1. JSX 是一种用于描述 UI 的 JavaScript 扩展语法，可以在 HTML 中写 JS ，React 使用这种语法描述组件的 UI。

2. React 认为组件是具备 UI 描述和 UI 数据的完整体，不应该将它们分开处理，于是使用 JSX ，作为 UI 描述和 UI 数据之间的桥梁。

### JSX 使用注意

**必须引入 React ！**

 从 create-react-app 4.0  开始，会自动引入

```jsx
import React from 'react';

function A() {
  // ...other code
  return <h1>A</h1>
}
```

**从本质上讲，JSX 只是为 React.createElement(component, props, ...children) 函数提供的语法糖。**

**JSX 存在的意义就是为了创建虚拟 DOM**

### JSX  的书写规范

- **JSX 的顶层只能有一个根元素，所以我们会在外层包裹一个 div 元素（或者使用 Fragment ）；**

  > **组件外层包裹原则：React 要求必须在一个组件的最外层用一个  div 进行包裹，否则报错**。

- 为了方便阅读，我们通常在 JSX 的外层包裹一个小括号 () ，并且 JSX 可以进行换行书写；

  >  **建议将 JSX 内容包裹在括号中，虽然这样做不是强制要求的，但是可以避免自动插入分号**

- JSX 中的标签可以是单标签，也可以是双标签；

  > **注意：如果是单标签，必须以 /> 结尾，比如不能写 `<img>` ，要写成 `<img/>`**

```jsx
import React, { Component, Fragment } from 'react';

class SomeThing extends Component{
  render(){
    return  (
      <Fragment>
        <div><input /> <button> 事件 </button></div>
        <ul>
          <li>上班</li>
          <li>下班</li>
        </ul> 
      </Fragment>
    )
  }
}

export default SomeThing;
```

### JSX 的基本语法

JSX 的基本语法和 XML 语法相同，都是使用成对的标签构成一个树状结构的数据，例如：

![img](https://cdn.nlark.com/yuque/0/2020/png/1614731/1608121327625-2ab5a45c-d3c1-45f4-b968-190abda2f524.png)

```jsx
// 下面两个是声明式的组件，并且没有 state 状态
const head = <header>头部样式</header>
  
function Section(){
  return <section>主体样式</section>
}

let element = (
  <div>
    <h2>{head}</h2>
    {
      // 单标签和双标签都可以，推荐单标签
    }
    <Section />   
    <Section></Section>
  </div>
)
```

**可以在 if 语句和 for 循环的代码块中使用 JSX**

```jsx
function getGreeting(user) {
  if (user) {
    return <h1>Hello, {formatName(user)}!</h1>;
  }
  return <h1>Hello, Stranger.</h1>;
}
```

**可以将 JSX 赋值给变量，因此可以把 JSX 当作函数的参数传入，也可以把 JSX 当作函数的返回值**

### JSX 的类型

在 JSX 语法中，使用的标签类型有两种：DOM 类型的标签（div、span 等）和 React 组件类型的标签。

**当使用 DOM 类型的标签时，标签的首字母必须小写；**

**当使用 React 组件类型的标签时，组件名称的首字母必须大写。**

**DOM 类型的标签组成 React 组件。**

**React 通过首字母的大小写判断渲染的是一个 DOM 类型的标签还是一个 React 组件类型的标签。**

**自定义的组件必须首写字母大写，而普通 JSX 是小写字母开头的。**

![img](https://cdn.nlark.com/yuque/0/2020/png/1614731/1608086443289-38e712aa-beff-45ec-875b-ae684239b7a4.png)

### 插值符号

**JSX 语法中，可以在大括号 {} 内放置任何有效的 JavaScript 表达式。**

**JSX 只能在插值符号 {} 中使用 JS ，插值符号外写的是 HTML 标签。**

**插值符号不仅可以写在 HTML 标签内，还可以写在 HTML 特性上作为 HTML 特性的值。**

**插值符号 {} 内能写的内容：**

- 字符串、模板字符串

- 数值

- 布尔值

- 数组（注意：数组元素不能是 json 对象），

  通常将数组中的每一个元素依次解析出来放在 jsx 的 html 中，数组常常通过 map 方法渲染

- 函数

- 函数调用

- 对象的属性/方法，只要返回的不是普通对象

- state 数据、props 数据

- 运算符表达式、三元表达式

   jsx 代码可通过 && 或三元表达式进行条件渲染

- HTML 元素 ，即 DOM 类型标签

- 存储以上值的变量

- 存储组件（React 类型标签）的变量，该变量可以是多个组件形成的数组

- js 的注释（注意：是 js 的注释）

> **注意：要有返回值才能在网页中渲染出来，不然只是进行了一堆逻辑操作**

### JSX 的  JS  表达式

**JSX 中的内容是动态的，我们可以通过表达式来获取：**

- **书写规则：{表达式}**

- **大括号内可以是变量、字符串、数组、函数调用等任意 js 表达式（即上文提到的内容）；**

**如何判断是不是表达式?**

**看它可不可以放在等于号右边。**

**像 if, for 这样的就不是表达式了，所以我们也不能在 {} 中写  for 循环和 if 判断，**

**但我们可以把结果暂存到变量中，然后插入到 {} 中，或者用 ? 表达式。**

**表达式会产生一个值，可以放在任何需要该值的地方。**

常见的表达式有：

- `num`
- `num++`
- `fn(num)`（这是函数表达式）
- `arr.map()`

另外，表达式不仅可以用在标签内部，还能用在标签的属性上。

JSX 可以使用 JavaScript 表达式，因为 JSX 本质上仍然是 JavaScript。

**在 JSX 中使用 JavaScript 表达式需要将表达式用大括号 {} 包起来。**

**通过 {} 语法可以引入外部的变量或者表达式。**

**表达式在 JSX 中的使用场景主要有两个：**

**1. 通过表达式给标签属性（特性）赋值**

**2. 通过表达式定义子组件**。

![img](https://cdn.nlark.com/yuque/0/2020/png/1614731/1608120898225-12a0fd9e-b937-46f6-8529-934877ace9ca.png)

>  **注意：JSX 中只能使用 JavaScript 表达式（一行生成值的代码片段，比如字面量、运算、函数调用），而不能使用多行 JavaScript 语句。**

JSX 中可以使用三目运算符（ ?  : ）或逻辑与（&&）运算符代替 if 语句的作用。

![img](https://cdn.nlark.com/yuque/0/2020/png/1614731/1608121113276-63cb3659-d859-4008-853a-7636acdff4de.png)

下面我们演示三个，其它的在开发中灵活运用：

- 运算表达式

- 三元运算符

- 执行一个函数

```jsx
class App extends React.Component {
  constructor(props) {
    super(props);

    this.state = {
      firstName: "kobe",
      lastName: "bryant",
      age: 20
    }
  }

  render() {
    return (
      <div>
        {/* 运算表达式 */}
        <h2>{this.state.firstName + " " + this.state.lastName}</h2>
        {/* 三元运算符 */}
        <h2>{this.state.age >= 18 ? "成年人": "未成年人"}</h2>
        {/* 执行一个函数 */}
        <h2>{this.sayHello("kobe")}</h2>
      </div>
    )
  }

  sayHello(name) {
    return "Hello " + name;
  }
}
```

### JSX 的注释

**html 标签包裹时用 {/\**/} 或 {//}，html 标签外用** **/\**/** **或 // 进行注释。**

**函数体外用 {/\**/} 或 {//}，函数体内用** **/\**/** **或 // 进行注释。**

注意下方 `<div className="tab-control">` 前后注释的不同

```jsx
render() {
  const {titles} = this.props;
  const {currIndex} = this.state;
  // 我是一段注释 
  /* 我是一段注释 */
  return (
    // 我是一段注释 
    /* 我是一段注释 */
    <div className="tab-control"> 
     {
      // 我是一段注释
     }
     {/* 我是一段注释 */} 
     {
       // 我是一段注释
       /* 我是一段注释 */
     }   
       titles.map((item, index) => {
         // 我是一段注释 
         /* 我是一段注释 */
         return (
           <div className="tab-item" onClick={e => this.itemClick(index)} key={index}> 
             <span className={"title " + (index === currIndex ? "active" : "")}>{item}</span>
           </div>
         )
       })
    </div>
  )
}
```

### JSX 显示变量内容

**与 Vue 的 template 不同，需要 this** 

- **情况一：当变量是 Number、String、Array 类型时，可以直接显示**

- **情况二：当变量是 null、undefined、Boolean 类型时，内容为空；**

  如果希望可以显示 null、undefined、Boolean，那么需要转成字符串；

  转换的方式有很多，比如 toString 方法、空字符串拼接，String(变量) 等方式；

- **情况三：对象类型不能作为子元素（not valid as a React child）**

```jsx
class App extends React.Component {
  constructor(props) {
    super(props);

    this.state = {
      name: "why",
      age: 18,
      hobbies: ["篮球", "唱跳", "rap"],
      
      test1: null,
      test2: undefined,
      flag: false,

      friend: {
        name: "kobe",
        age: 40
      }
    }
  }

  render() {
    return (
      <div>
        {/* 我是一段注释 */}
        <h2>Hello World</h2>
      </div>

      <div>
        {/* 1.可以直接显示 */}
        <h2>{this.state.name}</h2>
        <h2>{this.state.age}</h2>
        <h2>{this.state.hobbies}</h2>

        
        {/* 2.不显示 */}
        <h2>{this.state.test1}</h2>
        <h2>{this.state.test1 + ""}</h2>
        <h2>{this.state.test2}</h2>
        <h2>{this.state.test2 + ""}</h2>
        <h2>{this.state.flag}</h2>
        <h2>{this.state.flag + ""}</h2>
        
        {/* 3.不显示 */}
        <h2>123{this.state.friend}</h2>
      </div>
    )
  }
}

ReactDOM.render(<App/>, document.getElementById("app"));
```

**为什么 null、undefined、Boolean 在 JSX 中要显示为空内容呢？**

原因是在开发中，我们会进行很多的判断，比如：

- 在判断结果为 false 时，不显示内容；

- 在判断结果为 true 时，显示内容；

```jsx
class App extends React.Component {
  constructor(props) {
    super(props);

    this.state = {
      flag: false
    }
  }

  render() {
    return (
      <div>
        {this.state.flag ? <h2>我是标题</h2>: null}
        {this.state.flag && <h2>我是标题</h2>}
      </div>
    )
  }
}
```

## JSX 样式绑定与事件绑定

### JSX 的标签特性绑定（样式绑定）

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1611057753424-b2732fc3-1343-4403-9649-d2645659a3e9.jpeg)

当 JSX 标签是 DOM 类型的标签时，对应 DOM 标签支持的属性 JSX 也支持，例如 id、class、style、onclick 。

**标签属性值：对于字符串值使用引号 "" ，对于表达式使用大括号 {}，**

**对于同一标签属性不能同时使用这两种符号。**

**部分属性的名称会有所改变，如 label 的 for 变成 htmlFor ，再如 onclick 要写成 onClick** 

**主要的变化有：class 要写成 className，事件属性名采用驼峰格式，**

原因：class 是 JavaScript 的关键字，所以改成 className 。

**元素使用使用 className 时，className 后面跟的是字符串。**

**元素使用行内样式 style 时，style 后面跟的是一个对象类型，对象中是样式的属性名和属性值**

``` jsx
style={{fontSize: "30px", color: "red", backgroundColor: "blue"}}
```

#### 使用类名

```jsx
const element1 = (
    <div className="red">
        HelloWorld
    </div>
)
```

#### 使用行内样式

```jsx
const style = {
    color: red;
}

const style2 = {
    bg: {
        background: blue;
    }
}

const element1 = (
    <div style={style}>
        <h1 style={style2.bg}>HelloWorld</h1>
    </div>
)

class App extends React.Component {
  constructor(props) {
    super(props);

    this.state = {
      title: "你好啊",
      imgUrl: "https://upload.jianshu.io/users/upload_avatars/1102036/c3628b478f06.jpeg?imageMogr2/auto-orient/strip|imageView2/1/w/240/h/240",
      link: "https://www.baidu.com",
      active: false
    }
  }

  render() {
    return (
      <div>
        <h2 title={this.state.title}>Hello World</h2>
        <img src={this.state.imgUrl} alt=""/>
        <a href={this.state.link} target="_blank">百度一下</a>
        <div className={"message " + (this.state.active ? "active": "")}>你好啊</div>
        <div className={["message", (this.state.active ? "active": "")].join(" ")}>你好啊</div>
        <div style={{fontSize: "30px", color: "red", backgroundColor: "blue"}}>我是文本</div>
      </div>
    )
  }
}
```

### JSX 的事件绑定

#### 原生 JS 监听事件

- 方式一：获取 DOM 元素，添加监听事件；

- 方式二：在 HTML 元素中，直接绑定 onclick；

演示原生 JS 监听事件方式二

- btnClick() 这样写的原因是 onclick 绑定后面需要跟函数调用而不是声明；

```html
<button onclick="btnClick()">点我一下</button>

<script>
  function btnClick() {
    console.log("按钮发生了点击");
  }
</script>
```

#### React 监听事件

**主要有三点不同**

- **React 事件的命名采用小驼峰式（camelCase），而不是纯小写；**

- **我们需要通过 {} 传入一个事件处理函数，这个函数会在事件发生时被执行；**

- **事件处理函数必须是函数声明，不能是函数调用（这点与 Vue 不一样，所以不能直接在 {} 里的事件处理函数中传入参数），**

  **因为插值表达式会直接解析函数的调用 ，造成一进页面就会自动触发事件处理函数**

```jsx
class App extends React.Component {
  btnClick() {
    console.log("React按钮点击了一下")
  }
  
  render() {
    return (
      <div>
        <button onClick={this.btnClick}>点我一下(React)</button>
      </div>
    )
  }
}

function handleKeyUp() {
  console.log(123)    
}

function handleClick(a , b, e) {
  console.log(a + b)
  // 如果传入了 n 个参数,第 n + 1 个就是 event 对象
  console.log(e) // 最后一个值为 event 对象
}

const element1 = (
  <div>
    <input type="text" onKeyDown={alert(123)}/>
    {
      // 不能是上面那种写法调用事件，因为插值表达式会直接解析函数的调用 
    }
    <br/>
    <input type="text" onKeyUp={() => alert(1)}/>
    {
      // 使用上面的声明式写法才能正常触发 alert 事件
    }
    <br/>
    <input type="text" onKeyUp={handleKeyUp}/>
    {
      // 1、不调用而是将函数本身给事件，事件也能正常触发
    }
    <br/>
    <button
      type="text"
      onClick={(e) => {
        handleClick(10, 11)
      }}
    >
      函数传参1
    </button>
    {
      // 2、可以通过这种二次调用函数传参，这也是推荐的事件传参方式，这会默认将 event 传入
    }
    <button type="text" onClick={handleClick.bind(null, 10, 11)}>函数传参2</button>
    {
     // 3、也可通过 bind 传参
    }    
  </div>
)
```

> **注意：React 中的事件是合成事件，并不是 DOM 的原生事件，在 DOM 中可以通过返回 false 来阻止事件的默认行为，但在 React 中必须调用 e.preventDefault 才能阻止事件的默认行为，除了这点，和原生 DOM 事件并无差别**

例如，通常我们在 HTML 中阻止链接默认打开一个新页面会这样做：

```jsx
function ActionLink() {
  function handleClick(e) {
    e.preventDefault();
    console.log('链接被点击');
  }
 
  return (
    <a href="#" onClick={handleClick}>
      点我
    </a>
  );
}
```

### 事件绑定中的 this

> **注意：定义事件处理函数时一定要先绑定 this ，与 super(props) 一样，在 super(props) 下方绑定即可**

如果事件绑定的 this 是错的，可能会出现以下报错：

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1611216829308-c375f45b-2fb6-44f5-aad0-586d27cac2f1.jpeg)

```jsx
class App extends React.Component {
  constructor(props) {
    super(props);

    this.state = {
      message: "你好啊,React"
    }
  }
  
  btnClick() {
    console.log(this);
    console.log(this.state.message);
  }

  render() {
    return (
      <div>
        <button onClick={this.btnClick}>点我一下(React)</button>
      </div>
    )
  }
}
```

- 在类中直接定义一个事件处理（响应）函数，并且将这个函数绑定到 html 元素的 onClick 事件上，当前这个函数的 this 指向的是谁呢？

- 默认情况下是 undefined ，为什么是 undefined？

- 在正常的 DOM 操作中，监听点击，监听函数中的 this 其实是节点对象（比如 button 对象）

- 这是因为 React 并不是直接渲染成真实的 DOM，我们所编写的 button 只是一个语法糖，它的本质是 React 的 Element 对象

- 在这里发生监听的时候，因为函数赋值时会丢失 this，所以是  undefined

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1615551098201-b21413ef-477c-45bd-8a7b-e288c0d39898.jpeg)

比如我们这里打印：this.state.message，但是这里会报错：Cannot read property 'state' of undefined

原因： this 在这里是 undefined

如果我们这里直接打印 this，会发现它是一个 undefined

我们在绑定的函数中，可能想要使用当前对象，比如执行 this.setState 函数，就必须拿到当前对象的 this

我们就需要在传入函数时，给这个函数直接绑定 this

类似于下面的写法： 

```jsx
<button onClick={this.changeText.bind(this)}>改变文本</button>
```

#### 方案一（推荐，不常用）

我们看一下代码是如何实现的（点击按钮 Hello World 变成 Hello React）：

##### 使用 bind 绑定 this， 创建新函数绑定到监听事件中

> 注意第 13 行 onClick={this.changeText.bind(this)}

```jsx
class App extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      message: "Hello World"
    };
  }
  
  changeText() {
    this.setState({
      message: "Hello React"
    })
  }

  render() {
    return (
      <div>
        <h2>{this.state.message}</h2>
        <button onClick={this.changeText.bind(this)}>改变文本</button>
    	</div>
    )
  }
}

ReactDOM.render(<App/>, document.getElementById("app"));
```

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1615636255214-e92efa4b-65cb-4522-b96a-583e1d769e68.jpeg)

缺点：性能不太好，这种方式跟 react 内部帮你 bind 一样的，

每次 render 都会进行 bind，而且如果有两个元素的事件处理函数是同一个，也会进行 bind，

这样会多写点代码，而且进行两次 bind，性能不是太好。

其实这点性能往往不会是性能瓶颈的地方，如果你觉得顺手，这样写完全没问题。

##### constructor 构造方法中改变 this 指向（推荐，除了丑没有缺点）

> 注意看 constructor 中的操作： this.btnClick = this.btnClick.bind(this);

```jsx
class App extends React.Component {
  constructor(props) {
    super(props);

    this.state = {
      message: "你好啊,React"
    }

    this.btnClick = this.btnClick.bind(this);
  }
  
  btnClick() {
    console.log(this);
    console.log(this.state.message);
  }

  render() {
    return (
      <div>
        <button onClick={this.btnClick}>点我一下(React)</button>
        <button onClick={this.btnClick}>也点我一下(React)</button>
      </div>
    )
  }
}
```

相比于上一种绑定 this 的方式性能更好，

因为构造函数只执行一次，那么只会 bind 一次，

而且如果有多个元素都需要调用这个函数，也不需要重复 bind，基本上解决了第一种的两个缺点。

**为什么 React 没有自动地把 bind 集成到 render 方法中呢?**

因为 render 多次调用每次都要 bind 会影响性能，所以官方建议自己在 constructor 中手动 bind 达到性能优化。

#### 方案二（最推荐，常用）

##### 使用 ES6 class fields 语法

ES6 中给类定义属性的方法，称之为 class fields 语法；

> **注意第 10 行的 btnClick**

下面例子中赋值时，使用了箭头函数，所以在当前函数中的 this 会去上一个**作用域**中查找；

而上一个作用域中的 this 就是当前的对象；

```jsx
class App extends React.Component {
  constructor(props) {
    super(props);

    this.state = {
      message: "你好啊,React"
    }
  }
  
  btnClick = () => {
    console.log(this);
    console.log(this.state.message);
  }
  
  render() {
    return (
      <div>
        <button onClick={this.btnClick}>点我一下(React)</button>
        <button onClick={this.btnClick}>也点我一下(React)</button>
      </div>
    )
  }
}
```

优点：好看，性能好。

缺点：没有明显缺点，如果硬要说可能就是要多装一个 babel 插件来支持这种语法。

#### 方案三

##### 事件监听时传入箭头函数

```jsx
class App extends React.Component {
  constructor(props) {
    super(props);

    this.state = {
      message: "你好啊,张三"
    }
  }
  
  btnClick() {
    console.log(this);
    console.log(this.state.message);
  }

  render() {
    return (
      <div>
        <button onClick={() => this.btnClick()}>点我一下(React)</button>
        <button onClick={() => this.btnClick()}>也点我一下(React)</button>
      </div>
    )
  }
}
```

优点：顺手，好看。

缺点：每次 render 都会重复创建函数，性能会差一点。

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1611140875316-7ac1b72e-5566-4716-ada4-776ab9681308.jpeg)

### 事件参数传递

**有更多参数传递时, 事件对象 e 默认隐式地作为最后一个参数传入**

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1611140786668-2245391a-1675-4507-9876-ca479fc2aed1.jpeg)

![img](https://cdn.nlark.com/yuque/0/2021/png/1614731/1616205530188-08137094-e97e-469d-9b18-13f1411e8aba.png)

#### 获取 event 对象

- 很多时候我们需要拿到 event 对象来做一些事情（比如阻止默认行为）

- 假如我们用不到 this ，那么直接传入函数就可以获取到 event 对象

```jsx
class App extends React.Component {
  btnClick(e) {
    e.preventDefault();
    console.log(e);
  }
  
  render() {
    return (
      <div>
        <a href="http://www.baidu.com" onClick={this.btnClick}>点我一下</a>
      </div>
    )
  }
}
```

#### 获取更多参数

有更多参数传递时，我们最好的方式就是传入一个箭头函数（性能有影响），

箭头函数里面放主动执行的事件函数，并且传入相关的其他参数；

```jsx
class App extends React.Component {
  constructor(props) {
    super(props);

    this.state = {
      names: ["衣服", "鞋子", "裤子"]
    }
  }
  
  aClick(e, item, index) {
    e.preventDefault();
    console.log(item, index);
    console.log(e);
  }

  render() {
    return (
      <div>
        <a href="http://www.baidu.com" onClick={this.aClick}>点我一下</a>

        {
          this.state.names.map((item, index) => {
            return (
              <a href="#" onClick={e => this.aClick(e, item, index)}>{item}</a>
            )
          })
        }
      </div>
    )
  }
}
```

#### 事件参数传递与列表渲染

![img](https://cdn.nlark.com/yuque/0/2021/png/1614731/1616206498986-880994a1-cdff-4447-ae8a-53008a35c5eb.png)

## 关于 JSX 

### JSX 技巧

#### 属性使用字符串字面量

属性可以用字符串字面量, 会进行解码

```jsx
<MyComponent message="&lt;3" />
===
<MyComponent message={'<3'} />
```

#### Boolean 属性可省略

一些常用的属性例如 disabled checked 等，我们可以省略值来表示为 true。

```jsx
<input disabled /> 
===
<input disabled={true} />
```

#### 使用 ES6 rest 传递所有的 props

我们可以直接将一个 JavaScript 对象里的属性作为元素的属性合并，使用 ES6 的 rest 特性（展开）。

```jsx
const App = () => {
  const props = {
    className: 'app',
    id: 'app',
    'data-root': 'root',
  };
  return (
    <div {...props}>hello</div>
  );
};
```

渲染的 DOM ：

![img](https://cdn.nlark.com/yuque/0/2021/png/1614731/1611295777346-ba070f14-6e64-4ab3-8340-022920848319.png)

 下面两种写法是等价的：

```jsx
function App1() {
  return <Greeting firstName="Ben" lastName="Hector" />;
}

function App2() {
  const props = {firstName: 'Ben', lastName: 'Hector'};
  return <Greeting {...props} />;
}
```

### JSX 安全

可以安全地在 JSX 当中插入用户输入内容，

React DOM 在渲染所有输入内容之前，默认会进行转义，

这样可以确保在你的应用中，永远不会注入那些并非自己明确编写的内容，

所有的内容在渲染之前都被转换成了字符串，可以有效地防止 XSS 跨站脚本攻击。

### JSX 的本质

JSX 语法对使用 React 来说并不是必需的，

**实际上，JSX 语法只是 React.createElement(component, props, ...children) 的语法糖，**

**所以 JSX 本质就是 JS 。**

**Babel 会把所有的 JSX 语法转换成对 React.createElement() 的调用。**

因此 .js 文件里写 JSX 或 .jsx 文件里写 JSX 都可以运行相同的结果。

![img](https://cdn.nlark.com/yuque/0/2021/png/1614731/1615891618745-96dcd712-139d-456a-95ae-5026a0e7741e.png)

![img](https://cdn.nlark.com/yuque/0/2021/png/1614731/1615891646196-ab9d18ad-21cf-4b2d-8852-8e325a17268d.png)

<img src="https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1615893079070-838e69ca-7789-41d5-9c43-04bb3693f37d.jpeg" alt="img" style="zoom:;" />

<img src="https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1615893113949-ad00f148-ae4a-459d-ac5d-3a2dd66dde5a.jpeg" alt="img"  />

<img src="https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1615893181600-1f518cae-b0c0-492a-9bc7-501f37987d12.jpeg" alt="img"  />

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1615893213364-af435466-3a4f-4eff-b50e-b565decca8bb.jpeg)

**React.createElement() 在源码上的位置**

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1611057939019-03546df4-8770-468f-a554-66b5e2c1b3a1.jpeg)

### React.createElement() 

React.createElement() 最终创建出来一个 ReactElement 对象（React Node）。

```js
React.createElement(type, props, children)
```

参数类型如下：

- type (string | React.createClass())

- props (null | object)

- children (null | string | React.createClass() | React.createElement())

示例

```js
// create React element <li>'s
var rElmLi1 = React.createElement('li', {id:'li1'}, 'one');
var rElmLi2 = React.createElement('li', {id:'li2'}, 'two');
var rElmLi3 = React.createElement('li', {id:'li3'}, 'three');

//create <ul> React element and add child React <li> elements to it
var reactElementUl = React.createElement('ul', {className:'myList'}, rElmLi1, rElmLi2, rElmLi3);

ReactDOM.render(reactElementUl, document.getElementById('app'));
```

或

```js
var reactElementUl = React.createElement(
  'ul', {
    className: 'myList'
  },
  React.createElement('li', {id: 'li1'},'one'),
  React.createElement('li', {id: 'li2'},'two'),
  React.createElement('li', {id: 'li3'},'three')
);

ReactDOM.render(reactElementUl, document.getElementById('app'));
```

上述代码被渲染到 DOM 时，产生的 HTML 会是这样的：

```html
<ul class="myList" data-reactid=".0">
  <li id="li1" data-reactid=".0.0">one</li>
  <li id="li2" data-reactid=".0.1">two</li>
  <li id="li3" data-reactid=".0.2">three</li>
</ul>
```

React Node 只是一个树中的 JavaScript 对象，代表了虚拟 DOM 树内的真实 DOM 节点。

虚拟 DOM 在 HTML 页面中构建一个实际的 DOM 树，Javascript 创建带有 id 的元素。

以下两种示例代码完全等效：

```jsx
const element = (
  <h1 className="greeting">
    Hello, world!
  </h1>
);

const element = React.createElement(
  'h1',
  {className: 'greeting'},
  'Hello, world!'
);
```

React.createElement() 会预先执行一些检查，帮助你编写无错代码，

**但实际上它创建了一个这样的对象，JSX 的本质就是这样：**

```jsx
// 注意：这是简化过的结构
const element = {
  type: 'h1',
  props: {
    className: 'greeting',
    children: 'Hello, world!'
  }
};
```

**这些对象被称为 “React 元素”（React Node）。**

**React 元素就是一个 javascript 对象，描述了你希望在屏幕上看到的内容。**

**组件是由 React 元素构成的。**

React 通过读取这些对象，然后使用它们来构建 DOM 以及保持随时更新。

**这个 ReactElement 对象是什么作用呢？React 为什么要创建它呢？**

- 原因是 React 利用 ReactElement 对象组成了一个 JavaScript 的对象树；

- JavaScript 的对象树就是大名鼎鼎的虚拟 DOM（Virtual DOM）；

```jsx
// 注意：这是简化过的结构
const element = {
  type: 'h1',
  props: {
    className: 'greeting',
    children: 'Hello, world!'
  }
};
```

实际上，这就是虚拟 DOM 的一个节点，Virtual DOM是一种编程概念，

在这个概念里，UI 以一种理想化的，或者说虚拟的表现形式被保存于内存中，

并通过如 ReactDOM 等类库使之与真实的 DOM 同步，这一过程叫做协调。

这种方式赋予了 React 声明式的 API，告诉 React 希望 UI 是什么状态，React 就确保 DOM 匹配该状态，

这样就可以从属性操作、事件处理和手动 DOM 更新这些在构建应用程序时必要的操作中解放出来。

与其将 Virtual DOM 视为一种技术，不如说它是一种模式，

人们提到它时经常是要表达不同的东西。

**在 React 的世界里，术语 Virtual DOM 通常与 React 元素关联在一起，**

**因为它们都是代表了用户界面的对象，**

**而 React 也使用一个名为 fibers 的内部对象来存放组件树的附加信息，**

 **React 元素与 fibers 也被认为是 React 中 Virtual DOM 实现的一部分，**

**Virtual DOM也为使用 diff 算法奠定了基础。**

**JSX => React.createElement() => React 元素（虚拟DOM，即 JS 对象） => 真实DOM**

![img](https://cdn.nlark.com/yuque/0/2020/jpeg/1614731/1608208770610-c951eb62-65be-4b89-ba37-bcbe558f4878.jpeg)

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1611058196319-2dd39199-bd38-4c93-914e-c42676c200a9.jpeg)

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1611058298847-07877f97-f501-40da-a7b9-47df8cad139a.jpeg)

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1611058481416-8b800d1e-f070-43dd-9b57-90eb382c6c70.jpeg)

编译前：

```jsx
import React, { Component } from 'react';
import ReactDOM from 'react-dom';

class App extends Component {
  render() {
    return (
      <div className="App">
        <span>Hello, World.</span>
      </div>
    );
  }
}

ReactDOM.render(<App />, document.getElementById('root'));
```

编译后：

createElement 这个函数会将入参进行处理，

返回一个以 JavaScript 对象为基础的结构描述我们最终想要得到的 DOM 对象。

最终，通过 ReactDOM.render 方法，将所得到的 JavaScript 对象转换成真正的 DOM。

```jsx
import React, { Component } from 'react';
import ReactDOM from 'react-dom';

class App extends Component {
  render() {
    return (
      React.createElement(
            'div',
            { className: 'App' },
            React.createElement(
                'span',
                null,
                'Hello, World.'
            )
      )
    );
  }
}

ReactDOM.render(React.createElement(App), document.getElementById('root'))
```

JS 对象（虚拟 DOM）—— JSX 的本质

```javascript
{
    tag: 'div',
    attrs: {
        className: 'App',
    },
    children: [
        {
            tag: 'span',
            text: 'Hello, World.'
        }
    ]
}
```

简要过程如下：

```jsx
// JSX -> VDOM: 将 jsx 编译成 vdom
let vdom = <div id="foo">Hello!</div>;

// VDOM -> DOM: 将 vdom 渲染成 dom
let dom = render(vdom);

// add the tree to <body>: 将 dom 插入到挂载点
document.body.appendChild(dom);
```

### React 只更新它需要更新的部分

React DOM 会将元素和它的子元素与它们之前的状态进行比较，

只会进行必要的更新来使 DOM 达到预期的状态。

React DOM 只会更新实际改变了的内容，也就是下图中的文本节点。

![img](https://cdn.nlark.com/yuque/0/2020/gif/1614731/1608271508033-05c083e5-e3b5-4369-a7eb-75b0fb78f65f.gif)

### 虚拟 DOM 的作用

**虚拟 DOM 帮助我们从命令式编程转到了声明式编程的模式**

React 官方的说法：Virtual DOM 是一种编程理念。

在这个理念中，UI 以一种理想化或者说虚拟化的方式保存在内存中，并且它是一个相对简单的 JavaScript 对象。

我们可以通过 ReactDOM.render 让 虚拟 DOM 和 真实 DOM 同步起来，这个过程中叫做协调（Reconciliation）。

这种编程的方式赋予了 React 声明式的 API ：

你只需要告诉 React 希望让 UI 是什么状态，React 来确保 DOM 和这些状态是匹配的。

**不需要直接进行 DOM 操作，从手动更改 DOM 、属性操作、事件处理中解放出来。**

**正是因为这层的抽象，所以使跨平台成为了可能，对于所有拥有 JavaScript 运行环境的平台，我们都可以执行它。**

此外，因为将它抽象成了 JavaScript 对象，

所以我们也可以更方便地进行 diff/patch（React中的比对算法，用于比对后更新DOM结构）。

而不是当**数据产生变化的时候，我们直接去比对处理 DOM，这很大程度上优化了它的性能。**

### 书籍购物车示例

```jsx
function formatPrice(price) {
  if (typeof price !== "number") {
    price = Number("aaa") || 0;
  }
  return "¥" + price.toFixed(2);
}

class App extends React.Component {
  constructor(props) {
    super(props);

    this.state = {
      books: [
        {
          id: 1,
          name: '《算法导论》',
          date: '2006-9',
          price: 85.00,
          count: 2
        },
        {
          id: 2,
          name: '《UNIX编程艺术》',
          date: '2006-2',
          price: 59.00,
          count: 1
        },
        {
          id: 3,
          name: '《编程珠玑》',
          date: '2008-10',
          price: 39.00,
          count: 1
        },
        {
          id: 4,
          name: '《代码大全》',
          date: '2006-3',
          price: 128.00,
          count: 1
        },
      ]
    }
  }

  renderEmptyTip() {
    return <h2>购物车为空~</h2>
  }

  changeBookCount(index, count) {
    const newBooks = [...this.state.books];
    newBooks[index].count += count;
    this.setState({
      books: newBooks
    })
  }

  removeBook(index) {
    // React 设计原则: state 中数据不可变;
    this.setState({
      books: this.state.books.filter((item, indey) => index != indey)
    })
  }

  getTotalPrice() {
    // 1. for 循环的方式
    // let totalPrice = 0;
    // for (let item of this.state.books) {
    //   totalPrice += item.price * item.count;
    // }
    // return formatPrice(totalPrice);

    // 2.filter/map/reduce(归纳为)
    // 回调函数的参数:
    // 参数一: 上一次回调函数的结果(第一次没有上一次函数的回调函数的结果, 使用初始化值)
    const totalPrice = this.state.books.reduce((preValue, item) => {
      return preValue + item.count * item.price;
    }, 0);

    return formatPrice(totalPrice);
  }

  renderBooks() {
    return  (
      <div>
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
            {
              this.state.books.map((item, index) => {
                return (
                  <tr>
                    <td>{index+1}</td>
                    <td>{item.name}</td>
                    <td>{item.date}</td>
                    <td>{formatPrice(item.price)}</td>
                    <td>
                      <button disabled={item.count <= 1} onClick={e => this.changeBookCount(index, -1)}>-</button>
                      <span className="count">{item.count}</span>
                      <button onClick={e => this.changeBookCount(index, 1)}>+</button>
                    </td>
                    <td><button onClick={e => this.removeBook(index)}>移除</button></td>
                  </tr>
                )
              })
            }
          </tbody>
        </table>
        <h2>总价格: {this.getTotalPrice()}</h2>
      </div>
    )
  }

  render() {
    return this.state.books.length ? this.renderBooks(): this.renderEmptyTip();
  }
}

ReactDOM.render(<App/>, document.getElementById("app"));

table {
  border: 1px solid #eee;
  border-collapse: collapse;
}

th, td {
  border: 1px solid #eee;
  padding: 10px 16px;
  text-align: center;
}

th {
  background-color: #ccc;
}

.count {
  margin: 0 5px;
}
```

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1626854699375-4e935362-fdc5-4ae0-a968-5f22c6445f72.jpeg)

## 条件渲染

### 使用 state 数据判断

当逻辑较多时，通过条件判断：

```jsx
class App extends React.Component {
  constructor(props) {
    super(props);

    this.state = {
      isLogin: true
    }
  }

  render() {
    let titleJsx = null;
    if (this.state.isLogin) {
      titleJsx = <h2>欢迎回来~</h2>
    } else {
      titleJsx = <h2>请先登录~</h2>
    }

    return (
      <div>
        {titleJsx}
      </div>
    )
  }
}
```

### 使用函数判断

我们也可以将其封装到一个独立的函数中：

```jsx
class App extends React.Component {
  constructor(props) {
    super(props);

    this.state = {
      isLogin: true
    }
  }
  
  getTitleJsx() {
    let titleJsx = null;
    if (this.state.isLogin) {
      titleJsx = <h2>欢迎回来~</h2>
    } 
    else {
      titleJsx = <h2>请先登录~</h2>
    }
    return titleJsx;
  }

  render() {
    return (
      <div>
        {this.getTitleJsx()}
      </div>
    )
  }
}
```

### 三元运算符判断

三元运算符适用于没有太多逻辑的代码：只是根据不同的条件直接返回不同的结果

```jsx
class App extends React.Component {
  constructor(props) {
    super(props);

    this.state = {
      isLogin: true
    }
  }
  
  loginBtnClick() {
    this.setState({
      isLogin: !this.state.isLogin
    })
  }

  render() {
    return (
      <div>
        <h2>{this.state.isLogin ? "欢迎回来~": "请先登录~"}</h2>
        <button onClick={e => this.loginBtnClick()}>{this.state.isLogin ? "退出": "登录"}</button>
      </div>
    )
  }
}
```

### 与运算符 && 判断

某些情况下，我们会遇到这样的场景：

- 如果条件成立，渲染某一个组件；

- 如果条件不成立，什么内容都不渲染；

使用三元运算符的做法：

```jsx
{this.state.isLogin ? <h2>{this.state.username}</h2> : null}
```

逻辑与 && 可以简化操作：

```jsx
{this.state.isLogin && <h2>{this.state.username}</h2>}
```

### 阻止整个组件的渲染

有时，我们希望整个组件都不渲染，而不仅仅是局部不渲染，

我们可以在 render() 函数里返回一个 null

```jsx
function LogBtn (props) {
  const isLogined = props.isLogined;
  const isShow = props.isShow;
  if (isShow) {
    return (
      <div>You can 
        <button>{isLogined ? '退出' : '登陆'}</button>
      </div>
    )
  }
  return null;
}
```

### v-show 效果

一个 HTML 元素，渲染和不渲染之间，如果切换的非常频繁，那么会相对比较损耗性能：

- 在开发中，我们可以通过 display 的属性来控制它的显示和隐藏；

- 在控制方式在 vue 中有一个专门的指令：v-show；

- React 没有指令，但是 React 会更加灵活（灵活带来的代价就是需要自己去实现）；

```jsx
  render() {
    const { isLogin, username } = this.state;
    const nameDisplay = isLogin ? "block": "none";

    return (
      <div>
        <h2 style={{display: nameDisplay}}>{username}</h2>
        <button onClick={e => this.loginBtnClick()}>{isLogin ? "退出": "登录"}</button>
      </div>
    )
  }
```

> **注意：在 render() 里变量放 return() 上方**

[React 控制元素显示隐藏的三种方法](https://www.jianshu.com/p/9eb803e523a2)

第一种是通过 state 变量来控制是否渲染元素，类似 vue 中的 v-if 。

直接不渲染用户信息的 DOM 元素，保证了安全性。

不适合频繁控制显示隐藏的情况，因为会重新渲染元素，比较耗费性能。

第二种是通过 style 控制 display 属性，类似 vue 中的 v-show 。

第三种是通过动态切换 className 。

[React className 加 js 判断](https://blog.csdn.net/sinat_38783046/article/details/93194959)

```jsx
className={`title ${index === this.state.active ? 'active' : ''}`}
```

```jsx
className={index === this.state.active ? "active title" : "title"}
```

### 条件渲染官网示例

#### 登录/退出

使用变量存储组件，子组件触发事件改变父组件状态切换按钮文本，父组件传递状态给子组件改变标题文本

```jsx
import React, {Component} from 'react';

function UserGreeting() {
  return <h1>欢迎回来!</h1>
}

function GuestGreeting() {
  return <h1>请登录</h1>
}

function Greeting(props) {
  const isLogin = props.isLogin;
  if (isLogin) {
    return <UserGreeting/>
  }
  else {
    return <GuestGreeting/>
  }
}

function LoginButton(props) {
  return (
    <button onClick={props.onClick}>
      登录
    </button>
  )
}

function LogoutButton(props) {
  return (
    <button onClick={props.onClick}>
      退出
    </button>
  )
}

class LoginControl extends Component {
  constructor(props) {
    super(props);
    this.state = {
      isLogin: false
    };
    this.handleLoginClick = this.handleLoginClick.bind(this);
    this.handleLogoutClick = this.handleLogoutClick.bind(this);
  }
  
  handleLoginClick() {
    this.setState({isLogin: true})
  }

  handleLogoutClick() {
    this.setState({isLogin: false}) 
  }
  
  render() {
    const {isLogin} = this.state;
    let btn;
    if (isLogin) {
      btn = <LogoutButton onClick={this.handleLogoutClick}/> 
    }
    else {
      btn = <LoginButton onClick={this.handleLoginClick}/>
    }
    return (
      <div>
        <Greeting isLogin={isLogin}/>
        {btn}
      </div>
    )
  }
} 

export {Greeting, LoginControl};

import React from 'react';
import ReactDOM from 'react-dom';
import {Greeting, LoginControl} from './LoginControl/LoginControl';

ReactDOM.render(
  <LoginControl/>,
  document.getElementById('root')
);
```

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1611147582960-0e8ad438-f019-4c9d-aa94-19682edf7dc8.jpeg)

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1611147617085-ce65ac34-f9f0-44aa-a3c3-9cd60875a278.jpeg)

#### 显示/隐藏

传父组件的状态决定子组件是否显示，然后改变父组件之前的状态进行 显示/隐藏 的切换

> **注意 this.setState(() => ({}))** **箭头函数需要返回一个对象，必须在对象外面加上括号**

```jsx
import React, {Component} from 'react';
import './WarningBanner.css';

function WarningBanner(props) {
  if (!props.show) {
    return null;
  }
  return(
   <div className="warning">
     警告!
   </div>
  );
}

export default class Page extends Component {
  constructor (props) {
    super(props);
    this.state = {
      isShow: true
    };
    this.handleClick  = this.handleClick.bind(this);
  }

  handleClick() {
    this.setState(prevState => ({
      isShow: !prevState.isShow
    }));
  }

  render() {
    const {isShow} = this.state;
    return (
      <div>
       <WarningBanner show={isShow}/>
       <button onClick={this.handleClick}>{isShow ? '隐藏' : '显示'}</button>
      </div>
    );
  }
}


import React from 'react';
import ReactDOM from 'react-dom';
import Page from './WarningBanner/WarningBanner'

ReactDOM.render(
  <Page/>,
  document.getElementById('root')
);
```

```css
* {
  margin: 0;
}

button {
  height: 40px;
  width: 200px;
}

.warning {
  background-color: red;
  text-align: center;
  width: 100%;
  padding: 10px;

  font-size: 14pt;
  color: white;
}
```

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1611150052399-4467d7a9-5cfe-4268-bfa3-fac865be7620.jpeg)

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1611150111048-1c693d63-c6c4-4d0f-bd0c-37806c2406e6.jpeg)

## 列表渲染

真实开发中我们会从服务器请求到大量的数据，数据会以列表的形式存储：

- 比如歌曲、歌手、排行榜列表的数据；

- 比如商品、购物车、评论列表的数据；

- 比如好友消息、动态、联系人列表的数据；

在 React 中并没有像 Vue 模块语法中的 v-for 指令，

而且需要我们通过 JavaScript 代码的方式组织数据，转成 JSX：

- 很多从 Vue 转型到 React 的同学非常不习惯，认为 Vue 的方式更加的简洁明了；

- 但是 React 中的 JSX 正是因为和 JavaScript 无缝的衔接，让它可以更加的灵活；

- React 是真正可以提高我们编写代码能力的一种方式；

### JSX 列表渲染

**第一种方式 {数组}**

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1615550066802-80a27e90-a40f-4fb8-aba8-ff4d9dfcc1bd.jpeg)

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1615550095480-085ee0c5-b008-45d6-ad37-2af7fea90e94.jpeg)

**第二种方式 map() 方法，第一种方式的进化版，推荐**

map() 简介

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1615550531571-b6967c35-7ff7-4a61-a895-3ae9733a581e.jpeg)

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1615550505195-7f9c6ac4-0316-4c18-8a42-3114b7fce51b.jpeg)

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1615550587659-dc0f7327-3906-4025-9ff0-f64ab8a542ae.jpeg)

```jsx
class App extends React.Component {
  constructor(props) {
    super(props);

    this.state = {
      movies: ["盗梦空间", "大话西游", "流浪地球", "少年派", "食神", "美人鱼", "海王"]
    }
  }

  render() {
    return (
      <div>
        <h2>电影列表</h2>
        <ul>
          {
            this.state.movies.map(item => {
              return <li>{item}</li>
            })
          }
        </ul>
      </div>
    )
  }
}

ReactDOM.render(<App/>, document.getElementById("app"));
```

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1611057625342-0eb07425-dfa2-4bc4-98ec-c17045930b72.jpeg)

### 列表渲染官网示例

```jsx
function NumberList(props) {
  const numbers = props.numbers;
  const ListItem = numbers.map(num => {
    return (
      <li key={num.toString()}>{num}</li>
    )
  })
  return (
    <ul>
      {ListItem}
    </ul>
  )
}

export default function List() {
  const numbers = [1, 2, 3, 4, 5];
  return (
    <NumberList numbers={numbers}/>
  )
}
import React from 'react';
import ReactDOM from 'react-dom';

import List from './NumberList/NumberList';

ReactDOM.render(
  <List/>,
  document.getElementById('root')
);
```

上面代码里的组件还可以细分，多一个 ListItem 组件：

```jsx
function ListItem(props) {
  return (
    <li>{props.num}</li>
  )
}

function NumberList(props) {
  const numbers = props.numbers;
  const listItems = numbers.map(num => {
    return (
      <ListItem key={num.toString()} num={num}/>
    )
  })
  return (
    <ul>
      {listItems}
    </ul>
  )
}

export default function List() {
  const numbers = [1, 2, 3, 4, 5];
  return (
    <NumberList numbers={numbers}/>
  )
}
import React from 'react';
import ReactDOM from 'react-dom';
import List from './NumberList/NumberList';

ReactDOM.render(
  <List/>,
  document.getElementById('root')
);
```

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1611197216180-a01d8ebd-706a-47ce-9bbd-020d3e99302a.jpeg)

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1611195180728-e155a602-b17a-4905-9ad2-a4119e44d9c7.jpeg)

> **注意：可以使用变量存储列表，列表渲染时箭头函数必须要 return **

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1611197431783-d2cc6921-cf80-4fd1-a980-04914af5e941.jpeg)

### 数组处理

很多时候我们在展示一个数组中的数据之前，需要先对它进行一些处理：

- 比如过滤掉一些内容：filter 函数

- 比如截取数组中的一部分内容：slice 函数

比如我当前有一个数组中存放了一系列的数字：[10, 30, 120, 453, 55, 78, 111, 222]

需求：获取所有大于 50 的数字，并且展示前 3 个数

```jsx
class App extends React.Component {
  constructor(props) {
    super(props);

    this.state = {
      numbers: [10, 30, 120, 453, 55, 78, 111, 222]
    }
  }

  render() {
    return (
      <div>
        <h2>数字列表</h2>
        <ul>
          {
            this.state.numbers.filter(item => item >= 50).slice(0, 3).map(item => {
              return <li>{item}</li>
            })
          }
        </ul>
      </div>
    )
  }
}

ReactDOM.render(<App/>, document.getElementById("app"));
```

### 列表的 key

- 一个元素的 key 最好是这个元素在列表中拥有的一个独一无二的字符串，

     通常用数据 id ，万不得已才用数组索引。

- **key 最好是放在组件（组件标签，如果有的话）上，而不是 html 标签上。**

     一个好的经验是：在 map() 方法中的元素需要设置 key 属性。

- key 只要求在兄弟节点之间必须唯一

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1611196301703-78c56570-bad4-41e8-8a26-8348f529e410.jpeg)

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1611196373493-40bc1243-4115-4c22-8bfc-92aca1675d75.jpeg)

## 表单绑定(受控组件与非受控组件)

### 什么是受控组件与非受控组件？

**受控和非控是什么意思呢？**

受控和非控的“控”指的是：对某个组件值的掌控。

**受控组件与非受控组件的区别**

原生组件还有一些公用组件库都有一些通用的实践，即用于表单的组件一般都暴露出两个数据接口： value，defaultValue 。

如果指定了value，那么这个组件就被控制了，时刻响应父组件中数据的变化。

如果只是指定了 defaultValue ，那么这个组件就是非受控的，只是在初始化的时候指定一下初始值，随后就交出了控制权。

> 受控组件 —— 
>
> 组件的值只能由用户控制，使用 onChange 通过用户控制表单元素的上层组件
>
> 非受控组件  —— 
>
> 组件的值通过代码控制，使用 ref 找到它的当前值，通过 DOM 改变组件
>

在大多数情况下，推荐使用受控组件来处理表单数据

![v2-14660153f1a0bd577f36a75010b546c1_720w.jpg](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1651131134989-3aec31cf-2359-43dd-8524-b8e80be5e09f.jpeg)

﻿仅仅需要一次性收集数据，提交时才需要验证，用两种方式都可以。

但是，当业务中需要对数据进行即时校验，格式化输入数据等需求，就只能使用受控组件了。

受控组件能力更强。

### 受控组件（类似 v-model ，表单的值受 state 控制）

![img](https://cdn.nlark.com/yuque/0/2021/png/1614731/1616232444479-f441ed23-b530-498c-9762-4b2d3605bbb8.png)

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1616394396801-f3b65e23-7913-4bf0-9bf8-e37296f2e345.jpeg)

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1616394435071-2953f5e4-90a1-4d07-979c-fe8c255a8900.jpeg)

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1616394253191-dd1537cf-8523-4672-aa83-e11d57e710c6.jpeg)

**在 HTML 中， input 、 textarea 和 select 之类的表单元素通常会维护 state，**

**在 React 中，表单元素不会维护 state，**

**而是将 React 里的 state 和表单元素的值（value、checked） 建立依赖关系，**

**通过 onChange 事件将用户输入与 setState() 结合，更新 state 属性，最终更新表单元素的值** 

**表单元素的值受上层组件的某个 state 控制，就叫“受控组件”。**

在受控组件中，组件的状态与它的 value 或 checked prop 相对应。

**举个例子，受控组件不维护自己内部的值，它只会依据 props（this.state.username） 进行渲染**

```jsx
class TestComponent extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      username: "Tom"
    }
  }
  
  onChange(e) {
    console.log(e.target.value);
    this.setState({
      username: e.target.value
    })
  }
  
  render() {
    return <input name="username" value={this.state.username} onChange={(e) => this.onChange(e)} />
  }
}
```

**如果表单元素在某个子组件里，**

**子组件的 value 就会被父组件的 state 控制，时刻响应父组件中数据的变化。**

**这种情况的子组件也叫受控组件。**

大体的实现就是：

**给子组件提供一个 value ， 一个 onChange ，**

**value 与父组件 state 绑定，用于渲染当前的 UI 视图，onChange 用于用户触发操作。**

**需要更新子组件 value 的时候，父组件调用 onChange，由父组件负责更新自己的 state 来改变子组件的 value 。**

总结下 React 受控组件更新的流程：

- (1) 在初始 state 中设置表单的默认值。
- (2) 每当表单的值发生变化时，调用 onChange 事件处理函数。
- (3) 事件处理函数通过合成事件对象 e 拿到改变后的状态。
- (4) setState 更新应用的 state，触发视图的重新渲染，完成表单组件 prop 的更新

在 React 中，数据是单向流动的，一般通过 props 传递给组件的 state。

现在通过 onChange 事件处理函数将新的表单数据写回到组件的 state，完成**表单数据的双向绑定**。

整体设计的一个关键点是：

**受控组件内部不维护任何相关的状态，状态都由外部传入，对于状态的更新，提供 onChange 回调**

以下面的 DatePicker 作子组件，Form 作上层组件为例

```jsx
<DatePicker value={this.state.time} onChange={this.onChange} />
```

```jsx
import React, { Component } from 'react';
import { DatePicker } from 'antd';
import 'antd/dist/antd.css';

class Form extends Component{
  constructor(props) {
    super(props)
    this.state = {
      time: ""
    }
    this.onChange.bind(this);
  }

  onChange(value) {
    this.setState({
      time: value
    });
  }
  
  render() {
    return(
      <div>
        <DatePicker value={this.state.time} onChange={this.onChange} />
      </div>
    )
  }
}
```

### 表单绑定官网示例

#### 输入提交

```jsx
import React, {Component} from 'react';

export default class NameForm extends Component {
  constructor(props) {
    super(props);
    this.state = {
      value: ''
    };
    this.handleChange = this.handleChange.bind(this);
    this.handleSubmit = this.handleSubmit.bind(this);
  }
  
  handleChange(e) {
    this.setState({
      value: e.target.value
    })
  }

  handleSubmit(e) {
    alert('你的提交名字是:' + this.state.value);
    e.preventDefault();
  }

  render() {
    const {value} = this.state;
    return (
      <form onSubmit={this.handleSubmit}>
        <label>
          名字:
          <input type="text" value={value} onChange={this.handleChange}></input>
        </label>
        <input type="submit" value="提交"/>
      </form>
    )
  }
}
import React from 'react';
import ReactDOM from 'react-dom';

import NameForm from './NameForm/NameForm'

ReactDOM.render(
  <NameForm/>,
  document.getElementById('root')
);
```

主要代码执行过程：

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1611208999681-889f0a83-a9ac-4a2a-9ac4-17ae73d817bc.jpeg)

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1611208233892-333091af-4cd9-457d-9d51-e04c5e754057.jpeg)

#### 处理多个输入（两个）

当需要处理多个 input 元素时，

我们可以给每个元素添加 name 属性，并让处理函数根据 event.target.name 的值选择要执行的操作。

```jsx
import React, {Component} from 'react';

export default class Reservation extends Component {
  constructor(props) {
    super(props);
    this.state = {
      isGoing: true,
      numberOfGuests: 0
    }
    this.handleChange = this.handleChange.bind(this);
  }
  
  handleChange(e) {
    const target = e.target;
    const name = target.name;
    const value = target.name === "isGoing" ? target.checked : target.value;
    this.setState({
      [name]: value
    }) 
  }

  render() {
    const {isGoing, numberOfGuests} = this.state;
    return (
      <form>
        <label>
          是否离开
          <input name="isGoing" type="checkbox" checked={isGoing} onChange={this.handleChange}/>
        </label>
        <br/>
        <label>
          访客数
          <input name="numberOfGuests" type="number" value={numberOfGuests} onChange={this.handleChange}/>
        </label>
      </form>
    )
  }
}
import React from 'react';
import ReactDOM from 'react-dom';
import Reservation from './Reservation/Reservation'

ReactDOM.render(
  <Reservation/>,
  document.getElementById('root')
);
```

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1611211975041-45f7cd77-222c-4835-a9a1-6b69af9cd918.jpeg)

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1611213609251-a4260630-15e4-4b10-ba63-25fe6e7aff4b.jpeg)

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1616221412223-572780b6-4c67-4600-8986-499f752588c3.jpeg)

#### 示例

##### 单选 select

在原生中，我们默认一个 select 选项选中使用的是 selected ，比如下面这样：

```html
<select>
  <option value="sunshine">阳光</option>
  <option value="handsome">帅气</option>
  <option selected value="cute">可爱</option>
  <option value="reserved">高冷</option>
</select>
```

如果是使用 React 受控组件来写的话就不用那么麻烦了，

因为它允许在根 select 标签上使用 value 属性，去控制选中了哪个，

用户每次重选之后我们只需要在根标签中更新它

```jsx
class SelectComponent extends React.Component {
  constructor(props) {
    super(props);
    this.state = { value: 'cute' };
  }
  
  handleChange(event) {
    this.setState({value: event.target.value});
  }
  
  handleSubmit(event) {
    alert('你今日相亲对象的类型是: ' + this.state.value);
    event.preventDefault();
  }
  
  render() {
    return (
      <form onSubmit={(e) => this.handleSubmit(e)}>
        <label>
          你今日相亲对象的类型是:
          <select value={this.state.value} onChange={(e) => this.handleChange(e)}>
            <option value="sunshine">阳光</option>
            <option value="handsome">帅气</option>
            <option value="cute">可爱</option>
            <option value="reserved">高冷</option>
          </select>
        </label>
        <input type="submit" value="提交" />
      </form>
    );
  }
}

export default SelectComponent;
```

##### 多选 select

多选 select 对比单选来说，只有这两处改动：

- 给 select 标签设置 multiple 属性为 true

- select 标签 value 绑定的值为一个数组

```jsx
class SelectComponent extends React.Component {
  constructor(props) {
    super(props);
    // this.state = { value: 'cute' };
    this.state = { value: ['cute'] };
  }

  handleChange(event) {
    console.log(event.target.value)
    const val = event.target.value;
    const oldValue = this.state.value;
    const i = this.state.value.indexOf(val);
    const newValue = i > -1 ? [...oldValue].splice(i, 1) : [...oldValue, val];
    this.setState({value: newValue});
  }

  handleSubmit(event) {
    alert('你今日相亲对象的类型是: ' + this.state.value);
    event.preventDefault();
  }

  render() {
    return (
      <form onSubmit={(e) => this.handleSubmit(e)}>
        <label>
          你今日相亲对象的类型是:
          <select multiple={true} value={this.state.value} onChange={(e) => this.handleChange(e)}>
            <option value="sunshine">阳光</option>
            <option value="handsome">帅气</option>
            <option value="cute">可爱</option>
            <option value="reserved">高冷</option>
          </select>
        </label>
        <input type="submit" value="提交" />
      </form>
    );
  }
}
export default SelectComponent;
```

> 参考 https://react.docschina.org/docs/forms.html

## ref  获取 DOM 与非受控组件

### 创建  ref  的方式

如何创建 ref 来获取对应的 DOM 呢？

#### 三种方式

##### 方式一：传入字符串

使用时通过 `this.refs.传入的字符串`格式获取对应的元素；

##### 方式二：传入一个对象（推荐）

**对象是通过 React.createRef() 方式创建出来的；**

**使用时获取到创建的对象，其中有一个 current 属性就是对应的元素；**

##### 方式三：传入一个函数

该函数会在 DOM 被挂载时进行回调，这个函数会传入一个 元素对象，我们可以自己保存；

使用时，直接拿到之前保存的元素对象即可；

#### 三种方式的示例

```jsx
import React, { PureComponent, createRef } from 'react'

export default class App extends PureComponent {
  constructor(props) {
    super(props);
    
    // 1
    this.titleRef = createRef();
    this.titleEl = null;
  }
  
  changeText() {
    this.refs.title.innerHTML = "你好啊,李银河";
    // 3
    this.titleRef.current.innerHTML = "你好啊,李银河";
    this.titleEl.innerHTML = "你好啊,李银河";
  }

  render() {
    return (
      <div>
        <h2 ref="title">String Ref</h2>
        {/* 2 */}
        <h2 ref={this.titleRef}>Hello Create Ref</h2>
        <h2 ref={element => this.titleEl = element}>Callback Ref</h2>

        <button onClick={e => this.changeText()}>改变文本</button>
      </div>
    )
  }
}
```

### ref  节点的类型

ref 的值根据节点的类型而有所不同：

**当 ref 属性用于 HTML 元素时，**

**构造函数中使用 React.createRef() 创建的 ref 接收底层 DOM 元素作为其 current 属性；**

**当 ref 属性用于自定义 class 组件时，**

**ref 对象接收组件的挂载实例作为其 current 属性；**

你不能在函数组件上使用 ref 属性，因为它们没有实例；

但是某些时候，我们可能想要获取函数式组件中的某个 DOM 元素；

这个时候我们可以通过 React.forwardRef ，也可以在 hooks 中使用 ref；

### ref  引用 class 组件示例

```jsx
import React, { PureComponent, createRef } from 'react';

class Counter extends PureComponent {
  constructor(props) {
    super(props);

    this.state = {
      counter: 0
    }
  }
  
  increment() {
    this.setState({
      counter: this.state.counter + 1
    })
  }

  render() {
    return (
      <div>
        <h2>当前计数: {this.state.counter}</h2>
        <button onClick={e => this.increment()}>+1</button>
      </div>
    )
  }
}

export default class App extends PureComponent {
  constructor(props) {
    super(props);
    
    // 1
    this.counterRef = createRef();
  }
  
  increment() {
    // 3
    this.counterRef.current.increment();
  }

  render() {
    return (
      <div>
        {/* 2 */}
        <Counter ref={this.counterRef}/>
        <button onClick={e => this.increment()}>app +1</button>
      </div>
    )
  }
}
```

APP 组件可以控制 Counter 组件的 increment() 方法

![img](https://cdn.nlark.com/yuque/0/2021/png/1614731/1616227046172-82d09fef-ded5-410e-90f3-27d4307d616c.png)

### refs 转发（函数组件使用 ref）

**Ref 转发是一个可选特性，**

**允许某些组件接收 ref，并将其向下传递（换句话说，“转发”它）给子组件。**

在下面的示例中，FancyButton 使用 React.forwardRef 来获取传递给它的 ref，

然后转发到它渲染的 DOM button：

```jsx
// 3、 React 传递 ref 给 forwardRef 的回调函数 (props, ref) => ...，作为其第二个参数。
// 向下转发该 ref 参数到 <button ref={ref}>，将其指定为 JSX 属性。
// 当 ref 挂载完成，ref.current 指向 <button> DOM 节点。
const FancyButton = React.forwardRef((props, ref) => (
  <button ref={ref} className="FancyButton">
    {props.children}
  </button>
));

// 你可以直接获取 DOM button 的 ref：
// 1、 通过调用 React.createRef 创建了一个 React ref 并将其赋值给 ref 变量。
const ref = React.createRef();
// 2、 通过指定 ref 为 JSX 属性，将其向下传递给 <FancyButton ref={ref}>。
<FancyButton ref={ref}>Click me!</FancyButton>;
```

这样，使用 FancyButton 的组件可以获取底层 DOM 节点 button 的 ref ，并在必要时访问，

就像其直接使用 DOM 的 button 一样。

#### 示例

```jsx
import React, { PureComponent, createRef } from 'react';

function Home(props) {
  return (
    <div>
      {
        // 3
      }
      <h2 ref={props.ref}>Home</h2>
      <button>按钮</button>
    </div>
  )
}

export default class App extends PureComponent {
  constructor(props) {
    super(props);

    this.homeTitleRef = createRef(); // 1
  }
  
  printInfo() {
    console.log(this.homeTitleRef);
  }

  render() {
    return (
      <div>
        {
          // 2
        }
        <Home ref={this.homeTitleRef}/> 
        <button onClick={e => this.printInfo()}>打印ref</button>
      </div>
    )
  }
}
```

**函数组件使用 forwardRef 转发**

```jsx
import React, { PureComponent, createRef, forwardRef } from 'react';

const Home = forwardRef(function(props, ref) {
  return (
    <div>
      {
        // 3
      }
      <h2 ref={ref}>Home</h2>
      <button>按钮</button>
    </div>
  )
})

export default class App extends PureComponent {
  constructor(props) {
    super(props);

    this.homeTitleRef = createRef(); // 1
  }
  
  printInfo() {
    console.log(this.homeTitleRef.current);
  }

  render() {
    return (
      <div>
        {
          // 2
        }
        <Home ref={this.homeTitleRef}/>
        <button onClick={e => this.printInfo()}>打印ref</button>
      </div>
    )
  }
}
```

### 非受控组件（ref 访问 DOM）

如果一个表单组件没有 value / checked props（单选按钮和复选框对应的是 checked prop）时，就可以称为非受控组件。

通常，需要通过为其添加 ref prop 来访问渲染后的底层 DOM 元素。

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1616394575547-9f6ac490-9b43-49ca-a29f-65e148b60a4a.jpeg)

```jsx
import React, { PureComponent, createRef } from 'react'

export default class App extends PureComponent {
  constructor(props) {
    super(props);

    // 1 
    this.usernameRef = createRef();
  }
  
  handleSubmit(event) {
    event.preventDefault();
    // 3
    console.log(this.usernameRef.current.value);
  }

  render() {
    return (
      <div>
        <form onSubmit={e => this.handleSubmit(e)}>
          <label htmlFor="">
            {/* 2 */}
            用户:<input defaultValue="username" type="text" name="username" ref={this.usernameRef}/>
          </label>
          <input type="submit" value="提交"/>
        </form>
      </div>
    )
  }
}
```

在输入框输入内容后，点击提交按钮，我们可以通过 this.usernameRef 成功拿到 input 的 DOM 属性信息，

包括用户输入的值，这样我们就不需要像受控组件一样，单独的为每个表单元素维护一个状态。

同时我们也可以用 defaultValue 属性来指定表单元素的默认值。

**默认值（Default Value）**

如果想给非受控组件一个初始值，可以使用 defaultValue 属性，该属性只会在初次渲染起作用。

```jsx
export default class App extends React.Component {
  render() {
    return <input type="text" defaultValue="hello!" />;
  }
}
```

同样，

`<input type="checkbox">` 和 `<input type="radio">` 支持 defaultChecked，

`<select>` 和 `<textarea>` 支持 defaultValue。

下面对比以下两个示例。

在受控组件中，可以将用户输入的英文字母转化为大写后输出展示，而在非受控组件中则不会。

```jsx
// 将输入的字母转化为大写展示：
<input
  value={this.state.value}
  onChange={e => {
  this.setState({ value: e.target.value.toUpperCase() })
}}
/>

// 直接展示输入的字母：
<input
  defaultValue={this.state.value}
  onChange={e => {
  this.setState({ value: e.target.value.toUpperCase() })
}}
/>
```

通过上面的示例可以看出，

受控组件和非受控组件的最大区别是：

非受控组件的状态并不会受应用状态的控制，而受控组件的值来自于组件的 state。

### 为什么推荐使用受控组件？

```jsx
export default class App extends React.Component {
  inputRef = React.createRef();

  handleChange() {
    console.log(
      "attribute - value: ",
      this.inputRef.current.getAttribute("value")
    );
    console.log("target - value: ", this.inputRef.current.value);
  }

  render() {
    return (
      <div className="App">
        <input
          ref={this.inputRef}
          type="text"
          defaultValue="hello!"
          onChange={() => this.handleChange()}
        />
      </div>
    );
  }
}
```

<img src="https://cdn.nlark.com/yuque/0/2021/png/1614731/1633155075867-901e6cdb-512e-466d-bfc5-ec391bd0b11a.png" alt="img" style="zoom: 33%;" />

React 中的非受控组件，input 的值由自身维护，

当改变输入框的值时，虽然 e.target.value 会及时更新，但 getAttribute('value') 始终为 “hello!”。

为什么 React 始终推荐使用受控组件，因为受控组件完全可以避免这个问题（无法及时更新）。

当改用受控组件的写法后，

input 的值由 props（`this.state.val`）决定，

val 会随着 `setState()` 更新，

所以 `e.target.getAttribute('value')` 也会随之更新。

```jsx
export default class App extends React.Component {
  state = { val: "hello!" };

  handleChange(e) {
    this.setState({ val: e.target.value }, () => {
      console.log("attribute - value: ", e.target.getAttribute("value"));
      console.log("target - value: ", e.target.value);
    });
  }

  render() {
    return (
      <div className="App">
        <input
          type="text"
          value={this.state.val}
          onChange={e => this.handleChange(e)}
        />
      </div>
    );
  }
}
```

<img src="https://cdn.nlark.com/yuque/0/2021/png/1614731/1633155185394-d4707ef6-d0a8-4bec-923a-0934c42384e5.png" alt="img" style="zoom:33%;" />

## 组件

一个 React 应用是由许多个组件像搭积木或搭房子一样搭建而成的

### 组件化思想

- 如果我们将一个页面中所有的处理逻辑全部放在一起，

  处理起来就会变得非常复杂，而且不利于后续的管理以及扩展。

- 但如果，我们讲一个页面拆分成一个个小的功能块，

  每个功能块完成属于自己这部分独立的功能，那么之后整个页面的管理和维护就变得非常容易了。
  
  ![img](https://cdn.nlark.com/yuque/0/2020/png/1614731/1608210634780-9427ba7d-a815-4a7d-aaf7-7b8cb42c29a5.png)

我们需要通过组件化的思想来思考整个应用程序：

- 我们将一个完整的页面分成很多个组件；

- 每个组件都用于实现页面的一个功能块；

- 每一个组件又可以进行细分；

- 而组件本身又可以在多个地方进行复用；

### 组件分类

React 的组件相对于 Vue 更加的灵活和多样，按照不同的方式可以分成很多类组件：

- 根据组件的定义方式，可以分成：函数组件(Functional Component )和类组件(Class Component)；

- 根据组件内部是否有状态需要维护，可以分成：无状态组件(Stateless Component )和有状态组件(Stateful Component)；

- 根据组件的不同职责，可以分成：展示型组件(Presentational Component)和容器型组件(Container Component)；

这些概念有很多重叠，但是它们最主要是关注数据逻辑和 UI 展示的分离：

- 函数组件、无状态组件、展示型组件主要关注 UI 的展示；

- 类组件、有状态组件、容器型组件主要关注数据逻辑；

当然还有很多组件的其他概念：比如异步组件、高阶组件等。

### 定义组件的方式

**定义一个组件有两种方式，使用 ES6 class（类组件）和使用函数（函数组件）。**

目前 React 开发模式中更加流行 hooks，但是依然有很多的项目是使用类组件（包括 AntDesign 库）。

#### 类组件

**使用 class 定义组件需要满足两个条件：**

**（1）class 继承自 React.Component。**

**（2）class 内部必须定义 render() 方法，render() 返回代表该组件 UI 的 React 元素。**

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1611058717930-9586c493-5eb1-47e9-8271-98cb2750b428.jpeg)

```jsx
import React, { Component } from 'react';

export default class App extends Component {
  constructor() {
    super();

    this.state = {
      
    }
  }

  render() {
    return <h2>Hello App</h2>
  }
}
```

**注意： render() 里要有 return()，render() 里 return() 上方通常写接收数据、判断数据的处理**

**每次组件更新时 render() 方法都会被调用**

#### ReactDOM.render() 函数

- **参数一：传递要渲染的内容，这个内容可以是 HTML 元素，也可以是 React 的组件**

- **参数二：将渲染的内容，挂载到哪一个 HTML 元素上**

默认情况下 ReactDOM.render 会覆盖挂载到的元素中的所有内容，

所以不能在这之后对 DOM 元素进行操作,

也就是说：执行完 ReactDOM.render 之后，就不存在原来的 DOM 了

**render() 当中返回的 jsx 内容，就是之后 React 会帮助我们渲染的内容。**

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1611058785876-cb7b18e1-c01a-4852-b470-c351ee7ba6ec.jpeg)

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1611059131656-2c20131a-92a3-455a-8472-0649cf26d17d.jpeg)

**注意：使用 ReactDOM.render() 需要先导入 react-dom 库。**

**这个库会完成组件所代表的虚拟 DOM 节点到浏览器的 DOM 节点的转换，也就是** **JSX –> 虚拟DOM –> 真实 DOM 。**

如果按下面代码使用两次 ReactDOM.render() ,只会渲染 Hello 组件

```jsx
import NavBar from './NavBar/NavBar';
import Hello from './Welcome/Welcome';

ReactDOM.render(
  <NavBar/>,
  document.getElementById('root')
);

ReactDOM.render(
  <Hello />,
  document.getElementById('root')
);
```

ReactDOM.render 写在函数组件里，如同 render() 写在类组件里一样

```jsx
function tick() {
  const element = (
    <div>
      <h1>Hello, world!</h1>
      <h2>It is {new Date().toLocaleTimeString()}.</h2>
    </div>
  );
  ReactDOM.render(
    element,
    document.getElementById('root')
  );
}

setInterval(tick, 1000);
```

render 方法支持两种新的返回类型：数组（由 React 元素组成）和字符串。

定义一个 ListComponent 组件，它的 render 方法返回数组：

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1612237555073-cc6ec64e-8523-41f8-8261-87e3c65c18b8.jpeg)

再定义一个 StringComponent 组件，它的 render 方法返回字符串：

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1612237609112-6986953a-75c5-43f8-87ca-ebca6afe0d6e.jpeg)

App 组件的 render 方法渲染 ListComponent 组件和 StringComponent  组件：

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1612237632765-65e8219b-6a07-455f-860e-11cb9b2f1bc6.jpeg)

#### 函数组件

函数组件是使用 function 来进行定义的函数，只是这个函数会返回和类组件中的 render 函数返回一样的内容。

**函数组件有自己的特点（当然， hooks 就不一样了）：**

- **没有生命周期，也会被更新并挂载，但是没有生命周期函数；**

- **没有 this （组件实例）；**

- **没有 state （组件状态）；**

**函数组件**

**1、只有 return()**

**2、使用参数接收 props ，而不是 this.props**

**如果组件有状态或生命周期方法，使用类组件。否则，使用函数组件。**

定义一个函数组件：

```jsx
export default function App() {
  return (
    <div>Hello World</div>
  )
}
```

#### 使用类组件

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1611129624912-5a5124ba-fbaa-4d41-90f2-50fbf2ff9a93.jpeg)

> **Error: Objects are not valid as a React child (found: Wed Jan 20 2021 16:16:04 GMT+0800 (中国标准时间)). If you meant to render a collection of children, use an array instead.**
>
> **以上错误表示需要将 Date 对象转换为字符串**

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1611131862926-b743e048-c9e0-4fac-9ea7-cb2c8387d94d.jpeg)

**index.js**

```jsx
import React from 'react';
import ReactDOM from 'react-dom';
import PostList from './PostList';

ReactDOM.render(
  <PostList/>,
  document.getElementById('root')
);
```

**PostList.js**

```jsx
import React, { Component } from "react";

class PostList extends Component {
  render() {
    return (
      <div>
        话题列表：
        <ul>
          <li>番茄炒蛋</li>
          <li>香菇滑鸡</li>
          <li>萝卜玉米猪骨汤</li>
        </ul>
      </div>
    );
  }
}

export default PostList;
```

![img](https://cdn.nlark.com/yuque/0/2020/png/1614731/1608169290527-146bfc18-522e-44d3-9fc1-3d9be7c903fa.png)

PostList 中的每一个帖子都使用一个标签直接包裹，但一个帖子不仅包含帖子的标题，

还会包含帖子的创建人、帖子创建时间等信息，这时候标签下的结构就会变得复杂，

而且每一个帖子都需要重写一次这个复杂的结构，PostList 的结构将会变成类似这样的形式：

![img](https://cdn.nlark.com/yuque/0/2020/png/1614731/1608169473703-b08543a4-05a1-4599-be78-032dd1e05819.png)

这样的结构很冗余，

我们完全可以封装一个 PostItem 组件负责每一个帖子的展示，

然后在 PostList 中直接使用 PostItem 组件，

这样在 PostList 中就不需要为每一个帖子重复写一堆 JSX 标签。（详见 props ）

#### 使用函数组件（组合组件）

示例一

```jsx
function Welcome(props) {
  return <h1>{props.name}</h1>
}

export default function Hello(props) {
  return (
    <div>
      <Welcome name="Mike"/>
      <Welcome name="Sara"/>
      <Welcome name="John"/>
    </div>
  )
}
```

```jsx
import React from 'react';
import ReactDOM from 'react-dom';
import Hello from './Hello/Hello';

ReactDOM.render(
  <Hello />,
  document.getElementById('root')
);
```

示例二

```jsx
import React from 'react';
import PropTypes from 'prop-types';
import { Flex } from 'antd-mobile';
import "./index.less";

// 定义函数组件
function FilterFooter({
  onCancel,
  onOk
}) {
  return (
    <Flex className="root footer">
      {/* 重置按钮 */}
      <span
        className="btn cancel"
        onClick={onCancel}
      >
        重置
      </span>
      {/* 确定按钮 */}
      <span className="btn ok" onClick={onOk}>
        确定
      </span>
    </Flex>
  )
}

// props校验
FilterFooter.propTypes = {
  onCancel: PropTypes.func,
  onOk: PropTypes.func
}

export default FilterFooter

// 使用函数组件
<FilterFooter onCancel={this.handleReset} onOk={this.handleSure}/>
```

### 函数组件与类组件另一大区别

```jsx
import React from "react";
import ReactDOM from "react-dom";

import ProfilePageFunction from './ProfilePageFunction';
import ProfilePageClass from './ProfilePageClass';

class App extends React.Component {
  state = {
    user: 'Dan',
  };
  render() {
    return (
      <>
        <label>
          <b>Choose profile to view: </b>
          <select
            value={this.state.user}
            onChange={e => this.setState({ user: e.target.value })}
          >
            <option value="Dan">Dan</option>
            <option value="Sophie">Sophie</option>
            <option value="Sunil">Sunil</option>
          </select>
        </label>
        <h1>Welcome to {this.state.user}’s profile!</h1>
        <p>
          <ProfilePageFunction user={this.state.user} />
          <b> (function)</b>
        </p>
        <p>
          <ProfilePageClass user={this.state.user} />
          <b> (class)</b>
        </p>
        <p>
          Can you spot the difference in the behavior?
        </p>
      </>
    )
  }
}

const rootElement = document.getElementById("root");
ReactDOM.render(<App />, rootElement);
```

**操作如下：**

1、点击其中某一个 Follow 按钮。

函数组件的 props 作为该函数的参数传入，它是函数作用域下的变量。

函数的每一次调用中，都产生了新的 props 变量，它在声明时被赋予了对应的属性。

对于其中任意一个 props ，其值在声明时便已经决定，不会随着时间产生变化。

handleClick 函数亦是如此。

例如定时器的回调函数是在未来发生的，但 props.user 的值是在声明 handleClick 函数时就已经决定好的。

在第一次 render() 就获取当时的 props.user ，第二次 render() 创建新的 props.user ，互不影响，

两次 render() 都是不同的 props

2、在 3 秒内 切换 选中的账号。

触发 setState() 后，第二次 render() ，类组件的 this 更新，指向的是新实例，

所以 this.props 更新，类组件获取最新的 props

3、查看弹出的文本。

#### 函数组件

```jsx
import React from 'react';

function ProfilePageFunction(props) {
  const showMessage = () => {
    alert('Followed ' + props.user);
  };

  const handleClick = () => {
    setTimeout(showMessage, 3000);
  };

  return (
    <button onClick={handleClick}>Follow</button>
  );
}

export default ProfilePageFunction;
```

![img](https://cdn.nlark.com/yuque/0/2021/gif/1614731/1617616659676-f4f19e30-559b-426f-a8e2-773fcee62201.gif)

#### 类组件

```jsx
import React from 'react';

class ProfilePageClass extends React.Component {
  showMessage = () => {
    alert('Followed ' + this.props.user);
  };

  handleClick = () => {
    setTimeout(this.showMessage, 3000);
  };

  render() {
    return <button onClick={this.handleClick}>Follow</button>;
  }
}

export default ProfilePageClass;
```

![img](https://cdn.nlark.com/yuque/0/2021/gif/1614731/1617616633379-20eeef79-cbfe-4c03-9001-f5346d97a739.gif)

#### 区别

**函数组件的实现是正确的，类组件的实现明显有 bug**

如果我先关注了一个人，然后切换到了另一个人的页面，关注按钮不应该混淆我实际关注的是哪个人

![img](https://cdn.nlark.com/yuque/0/2021/gif/1614731/1617616711957-f44fbc91-e29e-4749-a6f0-e59c0cc7c350.gif)

上面两种实现，存在着微妙的差异

```jsx
class ProfilePage extends React.Component {
  showMessage = () => {
    alert('Followed ' + this.props.user);
  };
```

这个方法（第 3 行）会读取 this.props.user 。

**在 React 里，props 是不可变数据，永远不会改变。但是，this 却始终是可变的。**

**事实上，这就是类组件 this 存在的意义。**

如果 SayHi 是一个函数，React 需要调用它：

```jsx
// 你的代码 
function SayHi() { 
    return <p>Hello, React</p> 
} 

// React内部 
const result = SayHi(props) // » <p>Hello, React</p>
```

如果 SayHi 是一个类，React 需要先用 new 操作符将其实例化，然后调用新实例的 render 方法：

```jsx
// 你的代码 
class SayHi extends React.Component { 
    render() { 
        return <p>Hello, React</p> 
    } 
}

// React内部 
const instance = new SayHi(props) // » SayHi {} 
const result = instance.render() // » <p>Hello, React</p>
```

函数组件重新渲染将重新调用组件方法返回新的 react 元素，

类组件重新渲染将 new 一个新的组件实例，然后调用 render 类方法返回 react 元素，

这就说明了为什么类组件中 this 是可变的。

React 本身会随着时间的推移而改变，让你可以在 render 方法以及生命周期方法中得到最新的实例。

**所以如果在请求已经发出的情况下，我们的组件已经进行了重新渲染，this.props 会改变，**

**showMessage 方法从一个“新”的 props 中得到了 user。**

**但是，我们的事件处理函数应该“属于”一个拥有特定 props 和 state 的特定渲染。**

**假设不用函数组件，如何解决这个问题？**

一种方法是在调用点击事件之前读取当前的 this.props

```jsx
class ProfilePage extends React.Component {
  showMessage = (user) => {
    alert('Followed ' + user);
  };

  handleClick = () => {
    const {user} = this.props;
    setTimeout(() => this.showMessage(user), 3000);
  };

  render() {
    return <button onClick={this.handleClick}>Follow</button>;
  }
}
```

这种方法明显使代码变得更加冗长，并且会随着时间推移容易出错。

如果 showMessage 调用了另一个方法，该方法读取了 this.props.something 或者 this.state.something，

我们又将遇到同样的问题。

不得不将 this.props 和 this.state 以函数参数的形式在被 showMessage 调用的每个方法中一路传递下去。

**如果在 render 方法里，通过闭包来访问 props 和 state ，**

**我们就能确保在 showMessage 执行时，访问到的 props 和 state 就是 render 执行时的那份数据**

```jsx
class ProfilePage extends React.Component {
  render() {
    // 捕获到 props!
    const props = this.props;

    // 注意我们在 render 里面
    // 它们不是类方法
    const showMessage = () => {
      alert('Followed ' + props.user);
    };

    const handleClick = () => {
      setTimeout(showMessage, 3000);
    };

    return <button onClick={handleClick}>Follow</button>;
  }
}
```

如果组件逻辑都作为函数定义在 render 内部，而不是作为类的实例方法，那为什么还要用类呢？

确实，我们剥离掉类的外衣，剩下的就是一个函数组件：

```jsx
function ProfilePage(props) {
  const showMessage = () => {
    alert('Followed ' + props.user);
  };

  const handleClick = () => {
    setTimeout(showMessage, 3000);
  };

  return (
    <button onClick={handleClick}>Follow</button>
  );
}
```

**函数组件只会捕获当前 render() 执行时，使用的 state 和 props。**

### 关于组件的一些问题

#### 为什么要调用 super ？

**这是 JavaScript 的限制，在构造函数里如果要调用 this，那么提前就要调用 super，**

因为在 React 里，我们常常会在构造函数里初始化 state，this.state = xxx ，所以需要调用 super。

#### 为什么要传递 props ？

**无论有没有 constructor，在 render 中 this.props 都是可以使用的，这是 React 自动附带的。**

你可能以为必须给 super 传入 props，否则 React.Component 就没法初始化 this.props：

```jsx
class Component {
  constructor(props) {
    this.props = props;
    // ...
  }
}
```

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1612259508708-1c63c2a6-e83e-4a47-9583-a23fd3c7f81e.jpeg)

**constructor 中不是必须传入 props 也能使用,**

**如果不小心漏传了 props，但是调用了 super()，仍然可以在 render 和其他方法中访问 this.props**

为什么这样也行？

因为 React 会在 constructor() 构造函数被调用之后，把 props 赋值给刚刚创建的实例对象，

相当于自动执行：

```jsx
const instance = new YourComponent(props);
instance.props = props;
```

**虽然 React 会在** **constructor()** **构造函数运行之后，为 this.props 赋值，**

**但在 super() 调用之后与** **constructor()** **构造函数结束之前， this.props 其实仍然是没法用的**。

```jsx
// Inside React
class Component {
  constructor(props) {
    this.props = props;
    // ...
  }
}

// Inside your code
class Button extends React.Component {
  constructor(props) {
    super(); // 忘了传入 props
    console.log(props); // {}
    console.log(this.props); // undefined
  }
  // ...
}
```

要是 constructor() 构造函数中调用了某个访问 props 的方法，那这个 bug 就更难定位了。

**因此强烈建议始终使用 super(props)，即使这不是必须：**

```jsx
class Button extends React.Component {
  constructor(props) {
    super(props); // ✅ We passed props
    console.log(props); // ✅ {}
    console.log(this.props); // ✅ {}
  }
  // ...
}
```

上面的代码可以确保 this.props 始终是有值的。

**所以，如果在 constructor 中要使用 this.props ，就必须给 super 加参数：super(props)**

#### 为什么组件用大写开头？

JSX 是 React.createElement(component, props, …children) 提供的语法糖，

component 的类型是：string/ReactClass type，

我们具体看一下在什么情况下会用到 string 类型，什么情况下用到 ReactClass type 类型

**string 类型，React 会觉得它是一个原生 dom 节点**

**ReactClass type 类型则是自定义组件**

例如（string）：在 jsx 中我们写一个

```jsx
<div></div>
```

转换为 js 的时候就变成了

```javascript
React.createElement("div", null)
```

例如（ReactClass type）：在 jsx 中我们写一个

```jsx
function MyDiv() {
    return (<div><div>)
}

<MyDiv></MyDiv>
```

转换为 js 的时候就变成了

```javascript
function MyDiv() {
  return React.createElement("div", null);
}

React.createElement(MyDiv, null);
```

**上边的例子中如果将 MyDiv 中的首字母小写，如下**

```jsx
function myDiv() {
  return (<div><div>)
}

<myDiv></myDiv>
```

转换为 js 的时候就变成了

```javascript
function MyDiv() {
  return React.createElement("div", null);
}

React.createElement("myDiv", null);
```

**由于找不到 myDiv 这个 dom，所以就会报错。**

### props

#### 什么是 props ？

**props 可以看作是组件所有属性值的集合。在组件创建之前，作为组件设计的一部分。**

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1611123583759-db0be966-fcd5-420d-afc9-6154f8b575e8.jpeg)

**组件可以接受任意 props，包括基本数据类型，React 元素以及函数。**

**props 可以放在 constructor() 或 render() 中，**

**在 constructor() 中 super(props) 后可以直接使用 props 操作**

**在 render() 中则需要使用 this.props**

如何将数据传递给每一个 PostItem 组件呢？这时候就需要用到组件的 props 。

组件的 props 用于把父组件中的数据或方法传递给子组件，供子组件使用。

**props 是一个简单结构的对象，它包含的属性由组件作为 JSX 标签使用时的属性（特性）组成。**

```jsx
<ZS name="张三" age=18 height=1.8/>

ZS 组件对应的 props 为 {name: "张三", age: 18, height: 1.8} 
```

**index.js**

```jsx
import React from 'react';
import ReactDOM from 'react-dom';
import PostList from './PostList';

ReactDOM.render(
  <PostList/>,
  document.getElementById('root')
);
```

**PostList.js**

```jsx
import React, {Component} from 'react';
import PostItem from './PostItem';

const data = [
  {title: '番茄炒蛋', author: '张三', date: '2020-12-16'},
  {title: '香菇滑鸡', author: '李四', date: '2020-12-16'},
  {title: '萝卜玉米猪骨汤', author: '王五', date: '2020-12-16'}
]

class PostList extends Component {
  render() {
    return (
      <div>
        话题列表：
        <ul>
          {/*将数组中的每一个元素都渲染成一个组件*/}
          {data.map(item => 
            <PostItem 
              title={item.title}
              author={item.author} 
              date={item.date}/>
          )}
        </ul>
      </div>
    )
  }
}

export default PostList;
```

**PostItem.js**

```jsx
import React, {Component} from 'react';

class PostItem extends Component {
  render() {
    const {title, author, date} = this.props;
    return(
      <li>
        <div>
          <span>{title}</span>
        </div>
        <div>
          创建人：<span>{author}</span>
        </div>
        <div>
          创建时间：<span>{date}</span>
        </div>
      </li>
    )
  }
}

export default PostItem;
```

![img](https://cdn.nlark.com/yuque/0/2020/png/1614731/1608167876811-460959f4-9582-48a3-9963-5af98321bd8e.png)

#### props 的只读性（不可更改）

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1611127591369-ec42edfd-2a34-4b56-8504-7837a57a376b.jpeg)

#### 默认的 props

```jsx
class HellWorld extends React.Component {
  render() {
    return (
      <h1>Hello, {this.props.name}</h1>
    );
  }
}

// 默认的 props
HelloMessage.defaultProps = {
  name: 'World'
};
 
const element = <HelloWorld/>;
 
ReactDOM.render(
  element,
  document.getElementById('example')
);
```

#### 参数验证  propTypes

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1611059382149-91c9ee0b-85a7-4ed6-8985-88a3d423301b.jpeg)

```jsx
ChildCpn1.propTypes = {
  name: PropTypes.string,
  age: PropTypes.number,
  height: PropTypes.number
}

<ChildCpn1 name="why" age={18} height={1.88}/>
```

#### 函数组件的 props

```jsx
function ChildCpn(props) {
  const {name, age, height} = props;

  return (
    <div>
      <h2>我是function的子组件</h2>
      <p>展示父组件传递过来的数据: {name + " " + age + " " + height}</p>
    </div>
  )
}

export default class App extends Component {
  render() {
    return (
      <div>
        <ChildCpn name="kobe" age="30" height="1.98"/>
      </div>
    )
  }
}
```

### state

**state 是一种可以在组件创建后更改的数据，当组件创建后才可以使用它。**

React 把组件看成是一个状态机（State Machines）。

通过与用户的交互，实现不同状态，然后渲染 UI，让用户界面和数据保持一致。

React 里，只需更新组件的 state ，然后根据新的 state 重新渲染用户界面（不要操作 DOM）

**组件的 state 是组件内部的状态，state 的变化最终是为了反映到组件 UI 的变化上。**

我们在组件的构造方法 constructor 中通过 this.state 定义组件的初始状态，

并通过调用 this.setState 方法改变组件状态，进而组件 UI 也会随之重新渲染。

**state 中的数据初始化之后需要通过 setState() 方法才能重新在页面上渲染出来。**

**this.setState 方法可以改变组件状态（也是改变组件状态的唯一方式）。**

下面是演示 this.setState 的时钟示例：

```jsx
class Clock extends React.Component {
  constructor(props) {
    super(props);
    this.state = {date: new Date()};
  }
 // 下面两个都是生命周期钩子
  componentDidMount() {
    this.timerID = setInterval(
      () => this.tick(),
      1000
    );
  }
 
  componentWillUnmount() {
    clearInterval(this.timerID);
  }
 
  tick() {
    this.setState({
      date: new Date()
    });
  }
 
  render() {
    return (
      <div>
        <h1>Hello, world!</h1>
        <h2>现在是 {this.state.date.toLocaleTimeString()}.</h2>
      </div>
    );
  }
}
 
ReactDOM.render(
  <Clock />,
  document.getElementById('example')
);
```

**index.js**

```jsx
import React from 'react';
import ReactDOM from 'react-dom';
import PostList from './PostList';

ReactDOM.render(
  <PostList/>,
  document.getElementById('root')
);
```

**PostList.js**

```jsx
import React, { Component } from "react";
import PostItem from "./PostItem";

const data = [
  {id: 1, title: '番茄炒蛋', author: '张三', date: '2020-12-16', vote: 0},
  {id: 2, title: '香菇滑鸡', author: '李四', date: '2020-12-16', vote: 0},
  {id: 3, title: '萝卜玉米猪骨汤', author: '王五', date: '2020-12-16', vote: 0}
];

class PostList extends Component {
  render() {
    return (
      <div>
        话题列表：
        <ul>
          {data.map(item =>
            <PostItem
              title={item.title}
              author={item.author}
              date={item.date}
            />
          )}
        </ul>
      </div>
    );
  }
}

export default PostList;
```

**PostItem.js**

```jsx
import React, { Component } from "react";

class PostItem extends Component {
  constructor(props) {
    super(props);
    this.state = {
      vote: 0
    };
  }

  // 处理点赞逻辑
  handleClick() {
    let vote = this.state.vote;
    vote++;
    this.setState({
      vote: vote
    });
  }

  render() {
    const { title, author, date } = this.props;
    return (
      <li>
        <div>{title}</div>
        <div>
          创建人：<span>{author}</span>
        </div>
        <div>
          创建时间：<span>{date}</span>
        </div>
        <div>
          <button onClick={() => {this.handleClick()}}> 👍</button>
          <span>{this.state.vote}</span>
        </div>
      </li>
    );
  }
}

export default PostItem;
```

![img](https://cdn.nlark.com/yuque/0/2020/png/1614731/1608168053541-d60e16da-6901-4d33-8004-5deee7fd9bde.png)

### props 和 state

组件的 props 和 state 都会直接影响组件的 UI 。

事实上，React 组件可以看作一个函数，函数的输入是 props 和 state，函数的输出是组件的 UI。

![img](https://cdn.nlark.com/yuque/0/2020/png/1614731/1608121595177-d0382541-6ffc-448d-8e33-3858e23d17d7.png)

React 组件是由 props 和 state 两种类型的数据驱动渲染出组件 UI。

**props 是组件对外的接口，组件通过 props 接收外部传入的数据（包括方法）；**

**state 是组件对内的接口，组件内部状态的变化通过 state 来反映。**

**state 和 props 主要的区别在于：**

**props 是不可变的（只读的），你不能在组件内部修改 props；**

**state 是可变的，组件状态的变化通过修改 state 来实现，可以根据用户交互来改。**

举个例子，门既可以开，又可以关。

我们可以说门的状态就是 state ，因为它的值是可以在门创建后更改的。

门的材料就像 prop， 是不会改变的。

**state 是私有的，只能在当前组件的内部才能访问，其他组件都无法访问。**

门无论是开还是关，这都只是门的逻辑。与房子或其他组件没有任何关系。

事实上，我们完全可以将门从房子中移出去，它仍然可以自己打开或关闭。

 **在组件中的数据，我们可以分成两类：**

- **参与界面更新的数据：当数据变化时，需要更新组件渲染的内容**

- **不参与界面更新的数据：当数据变化时，不需要更新组件渲染的内容**

**与界面更新的数据我们可以称之为是参与数据流，这个数据是定义在当前对象的 state 中：**

- 我们可以在 constructor() 构造方法中写 this.state = {定义的数据}

**当数据发生变化时，我们可以调用 this.setState() 来更新数据，并且通知 React 进行 update 操作:**

- 在进行 update 操作时，会重新调用 render 函数，并且使用最新的数据，来渲染界面

<https://react.docschina.org/docs/faq-state.html#what-is-the-difference-between-state-and-props>

### setState() 使用注意

#### setState() 接收两个参数

第一个参数接收一个对象或返回一个对象的函数作为入参,

第二个参数是一个回调函数，这个回调函数会在更新后会执行

#### 不可变数据的原则

不可变数据是函数式编程的重要概念，

就是我们对已经初始化的数据不进行更改，每次更改都是创建新的对象来承载新的数据状态

**状态类型**

- 值类型：string、number、boolean、null、undefined

- 数组类型

- 对象

**简单使用**

```jsx
// 1.状态的类型是值类型
this.setState({
    count: 1,
    name: 'zs',
    show: true
})

// 2.状态的类型是数组
const _books = this.state.books.slice()
// 或
const _books = this.state.books.concat('new book')
// 或
const _books = [...this.state.books, 'new book')]

this.setState({
    books:_books
})

// 3.状态的类型是对象
const _item = Object.assign({}, this.state.item, {id: 1, name: 'zs'})
// 或
const _item = {...this.state.item, id: 1, name:'zs'}

this.setState({
    item:_item
})
```

**示例**

```jsx
import React, { PureComponent } from 'react'

export default class App extends PureComponent {
  constructor(props) {
    super(props);

    this.state = {
      friends: [
        { name: "lilei", age: 20, height: 1.76 },
        { name: "lucy", age: 18, height: 1.65 },
        { name: "tom", age: 30, height: 1.78 }
      ]
    }
  }
    
  insertFriend() {
     
  }

  incrementAge(index) {
    
  }  

  render() {
    return (
      <div>
        <h2>朋友列表</h2>
        <ul>
          {
            this.state.friends.map((item, index) => {
              return (
                <li key={item.name}>
                  <span>{`姓名:${item.name} 年龄: ${item.age}`}</span>
                  <button onClick={e => this.incrementAge(index)}>年龄+1</button>
                </li>
              )
            })
          }
        </ul>
        <button onClick={e => this.insertFriend()}>添加新数据</button>
      </div>
    )
  }
}
```

**insertFriend 应该如何实现？**

**实现方式一（错误）：**

这种方式会造成界面不会发生刷新，添加新的数据；

原因是继承自 PureComponent，会进行浅层比较，浅层比较过程中两个 friends 是相同的对象；

```jsx
insertFriend() {
  this.state.friends.push({name: "why", age: 18, height: 1.88});
  this.setState({
    friends: this.state.friends
  })
}
```

**实现方式二（正确）：**

[...this.state.friends, {name: "why", age: 18, height: 1.88} ] 会生成一个新的数组引用；

在进行浅层比较时，两个引用的是不同的数组，所以它们是不相同的；

```jsx
insertFriend() {
  this.setState({
    friends: [...this.state.friends, {name: "why", age: 18, height: 1.88}]
  })
}
```

**incrementAge 应该如何实现？**

**实现方式一（错误）：**

和上面方式一类似

```jsx
incrementAge(index) {
  this.state.friends[index].age += 1;
  this.setState({
    friends: this.state.friends
  })
}
```

**实现方式二（正确）：**

和上面方式二类似

```jsx
incrementAge(index) {
  const newFriends = [...this.state.friends];
  newFriends[index].age += 1;
  this.setState({
    friends: newFriends
  })
}
```

不可变数据：对最外层的引用类型浅拷贝一般就足够了，不需要深拷贝（里外都拷贝）

**如果要深拷贝可以用 immutable.js 类库 来处理不可变数据。**

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1616393687199-29e1795f-750b-4898-a111-0b5aca094cbc.jpeg)

#### setState() 可以只更新部分状态，不会影响其它状态

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1611138471812-ff20af5f-706c-4048-8f9e-fe1f0dc3c092.jpeg)

假如我们有这样的数据：

```jsx
this.state = {
  name: "Tom",
  message: "Hello World"
}
```

我们需要更新 message ,

通过 setState 去修改 message ，是不会对 name 产生影响的；

```jsx
changeText() {
  this.setState({
    message: "你好啊,李银河"
  });
}
```

#### setState() 使用函数获取上一个状态，才能更新下一个状态

在之前的 有状态的组件 与 条件渲染 显示/隐藏 中都有使用到这个特性

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1611138417944-df33fb87-6013-433a-a3f5-d863ededada9.jpeg)

如果进行如下操作，那么 counter 会变成几呢？答案是 1 

为什么呢？因为它会对多个 state 进行合并

```jsx
increment() {
  this.setState({
    counter: this.state.counter + 1
  });

  this.setState({
    counter: this.state.counter + 1
  });

  this.setState({
    counter: this.state.counter + 1
  });
}
```

如何可以做到，让 counter 最终变成 3 呢？

一次加 3：

```jsx
increment() {
  this.setState((state, props) => {
    return {
      counter: state.counter + 1
    }
  })

  this.setState((state, props) => {
    return {
      counter: state.counter + 1
    }
  })

  this.setState((state, props) => {
    return {
      counter: state.counter + 1
    }
  })
}
```

为什么传入一个函数就可以变出 3 呢？

原因就是多个 state 进行合并时，每次遍历都会执行一次函数

### setState 的一些问题

#### 为什么要 setState()，而不是直接 this.state.xx = oo ？

**setState 做的事情不仅仅只是修改了 this.state 的值，**

**最重要的是它会触发 React 的更新机制，会进行 diff ，然后将 patch 部分更新到真实 dom 里。**

**如果你直接 this.state.xx == oo 的话，state 的值确实会改，**

**但是改了不会触发 UI 的更新，那就不是数据驱动了。**

那为什么 Vue 直接修改 data 可以触发 UI 的更新呢？

因为 Vue 在创建 UI 的时候会把这些 data 给收集起来，

并且在这些 data 的访问器属性 setter 进行了重写，在这个重写的方法里会去触发 UI 的更新。

#### setState 是同步还是异步相关问题

1. **setState 是同步还是异步？**

执行过程中代码是同步的，合成事件和钩子函数的调用顺序在更新之前，

导致在合成事件和钩子函数中没法立马拿到更新后的值，形成了所谓的“异步”，

所以表现出来有时是同步，有时是“异步”。

2. **何时是同步，何时是异步呢？**

**只在合成事件和钩子函数中是“异步”的，**

**在原生事件和 setTimeout/setInterval 等原生 API 中都是同步的。**

**简单地可以理解为被 React 控制的函数里面就会表现出“异步”，反之表现为同步。**

**验证一：原生DOM事件**

```jsx
componentDidMount() {
  const btnEl = document.getElementById("btn");
  btnEl.addEventListener('click', () => {
    this.setState({
      message: "你好啊,李银河"
    });
    console.log(this.state.message); // 你好啊,李银河
  })
}
```

**验证二：在 setTimeout 中的更新**

```jsx
changeText() {
  setTimeout(() => {
    this.setState({
      message: "你好啊,李银河"
    });
    console.log(this.state.message); // 你好啊,李银河
  }, 0);
}
```

3. **那为什么会出现异步的情况呢？**

**为了做性能优化，将 state 的更新延缓到最后批量合并再去渲染，**

**这对于应用的性能优化是有极大好处的，**

如果每次的状态改变都去重新渲染真实 dom，那么它将带来巨大的性能消耗。

4. **那如何在表现出异步的函数里可以准确拿到更新后的 state 呢？**

通过第二个参数 setState(partialState, callback) 中的 callback 拿到更新后的结果。

setState() 的第二个参数为可选的回调函数，

它将在 setState 完成合并并重新渲染组件后执行。

通常，我们建议使用 componentDidUpdate() 来代替此方式。

或者可以通过给 setState 传递函数来表现出同步的情况：

```jsx
this.setState((state) => {
    return { val: newVal }
})
```

5. **那表现出异步的原理是怎样的呢？**

在 React 的 setState 函数实现中，

会根据 isBatchingUpdates(默认是 false) 变量判断是否直接更新 this.state 还是放到队列中稍后更新。

然后有一个 batchedUpdate 函数，可以修改 isBatchingUpdates 为 true，

当 React 调用事件处理函数之前，或者生命周期函数之前就会调用 batchedUpdate 函数，

这样的话，setState 就不会同步更新 this.state，而是放到更新队列里面后续更新。

**这样就可以理解为什么原生事件和 setTimeout/setInterval 里面调用 this.state 会同步更新了，**

**因为通过这些函数调用的 React 没办法去调用 batchedUpdate 函数将 isBatchingUpdates 设置为 true，**

**这个时候 setState 的时候默认就是 false，就会同步更新。**

### 有状态组件和无状态组件

类组件（有状态组件）、函数组件（无状态组件）

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1611138772493-99a71917-5c9f-4e94-904a-c98694ed20e1.jpeg)

**index.js**

```jsx
import React from 'react';
import ReactDOM from 'react-dom';
import PostList from './PostList';

ReactDOM.render(
  <PostList/>,
  document.getElementById('root')
);
```

**PostList.js**

```jsx
import React, {Component} from 'react';
import PostItem from './PostItem';

class PostList extends Component {
  constructor(props) {
    super(props);
    this.state = {
      posts: []
    };
    this.timer = null;
    this.handleVote = this.handleVote.bind(this);
  }

  componentDidMount() {
    this.timer = setTimeout(() => {
      this.setState({
        posts: [
          {id: 1, title: '番茄炒蛋', author: '张三', date: '2020-12-16', vote: 0},
          {id: 2, title: '香菇滑鸡', author: '李四', date: '2020-12-16', vote: 0},
          {id: 3, title: '萝卜玉米猪骨汤', author: '王五', date: '2020-12-16', vote: 0}
        ]
      })
    }, 1000)
  }

  componentWillUnmount() {
    if (this.timer) {
      clearTimeout(this.timer)
    }
  }

  handleVote(id) {
    const posts = this.state.posts.map(item => {
      const newItem = item.id === id ? {...item, vote: ++item.vote} : item;
      return newItem;
    })
    this.setState({
      posts
    })
  }

  render() {
    return (
      <div>
        话题列表：
        <ul>
          {this.state.posts.map(item => 
            <PostItem 
              post = {item}
              onVote = {this.handleVote}
            />
          )}
        </ul>
      </div>
    )
  }
}

export default PostList;
```

**PostItem.js**

```jsx
import React from 'react';

function PostItem(props) {
  const handleClick = () => {
    props.onVote(props.post.id)
  }
  const {post} = props;
  return(
    <li>
      <div>
        <span>{post.title}</span>
      </div>
      <div>
        创建人：<span>{post.author}</span>
      </div>
      <div>
        创建时间：<span>{post.date}</span>
      </div>
      <div>
        <button onClick={handleClick}>👍</button>
        <span>{post.vote}</span>
      </div>      
    </li>
  )
}

export default PostItem;
```

![img](https://cdn.nlark.com/yuque/0/2020/png/1614731/1608168138488-c052cb76-0b1c-47c3-97c5-fe7e2deaf71b.png)

### 类组件的通用模型

```jsx
import React, {Component} from 'react';

class ··· extends Component {
  constructor(props) {
    super(props);
    this.state = {···};
    this.··· = ···;
    this.handle··· = this.handle···.bind(this);
  }
    
  handle···(···) {
    ···
    this.setState({
      ...
    })
  }   

  handle··· = (···) => {
    ···
    this.setState({
      ...
    })
  }
                  
  componentDidMount() {
    ...
  }

  componentWillUnmount() {
    ...
  }

  render (
    const ··· = ···;
    ... if for 函数等等一系列对数据（state 、 props ）或 JSX 处理都可以，或者封装到函数（方法）里
    return(
      <div>
        ···
      </div>
    ) 
  )
}  

export default ···; // 默认导出父级类组件
      
      
import React from 'react';
import ReactDOM from 'react-dom';  
import ... from '...';

ReactDOM.render(
  <.../>,
  document.getElementById('root')
);  
```

## 组件通信

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1617765016671-988c6e74-65c9-4016-b40e-b4bf3946c7ee.jpeg)

### 父组件传递子组件（props）

在一个 React 项目中，组件之间的通信是非常重要的环节；

父组件在展示子组件，可能会传递一些数据给子组件：

- 父组件通过 **属性=值** 的形式来传递给子组件数据；

- 子组件通过 **props** 参数获取父组件传递过来的数据；

```jsx
class Child extends React.Component {
  render() {
    const {msg} = this.props;
    return (
      <div>
        <p>{msg}</p>
      </div>
    )
  }
}

class Parent extends React.Component {
  data = {
    msg: "传给子组件的值"
  }
    
  render() {
    return (
      <div>
        {/* 
           通过解构传参给 props , 
           {...this.data} === {msg: "传给子组件的值"}  
           表达式 => 对象 
           this.props 
         */} 
        <Child {...this.data}/>
      </div>
    )
  }
}

ReactDOM.render(<Parent/>, document.getElementById('example'))
```

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1611042608342-75f5e56b-fbe1-4a8b-a82c-3939a0199c66.jpeg)

```jsx
import React, { Component } from 'react';

// 子组件
class ChildCpn1 extends Component {
  render() {
    const { name, age, height } = this.props;

    return (
      <div>
        <h2>我是class的子组件</h2>
        <p>展示父组件传递过来的数据: {name + " " + age + " " + height}</p>
      </div>
    )
  }
}

export default class App extends Component {
  render() {
    return (
      <div>
        <ChildCpn1 name="why" age="18" height="1.88" />
      </div>
    )
  }
}
```

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1612233486348-8f12ef19-efe0-405b-bd1a-14633c6efd43.jpeg)

```jsx
import React, { Component } from 'react';

// class 类型的子组件
class ChildCpn1 extends Component {
  constructor(props) {
    // 一般都是 super(props)
    super();
    this.props = props;
  }

  render() {
    const { name, age, height } = this.props;

    return (
      <div>
        <h2>我是class的子组件</h2>
        <p>展示父组件传递过来的数据: {name + " " + age + " " + height}</p>
      </div>
    )
  }
}

// 函数类型的子组件
function ChildCpn2(props) {
  const {name, age, height} = props;

  return (
    <div>
      <h2>我是function的子组件</h2>
      <p>展示父组件传递过来的数据: {name + " " + age + " " + height}</p>
    </div>
  )
}

export default class App extends Component {
  render() {
    return (
      <div>
        <ChildCpn1 name="why" age="18" height="1.88"/>
        <ChildCpn2 name="kobe" age="30" height="1.98"/>
      </div>
    )
  }
}
```

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1612233652626-eaf1ca31-ac9a-4d4c-be4c-c5de6d16c2df.jpeg)

### 子组件传递父组件（props）

某些情况，我们也需要子组件向父组件传递数据：

- 在 Vue 中是通过自定义事件来完成的；

- 在 React 中则是通过 props 传递消息，

  只是让父组件给子组件传递一个回调函数（方法或变量，只要是函数即可）作为 props，

  在子组件中使用事件监听调用这个函数即可；

**也就是**

**1、父组件先把回调函数作为 props （特指左边的属性）传给子组件**

**2、子组件在需要改变父组件 state 数据时，调用这个回调函数**

**比如子组件接收 props 后再使用事件触发父组件传入的 props —— 即父组件的回调函数，**

**并且可以传值给父组件这个传入的 props  —— 即父组件的回调函数**

**或者把父组件传入的 props 放在子组件的方法里后在子组件使用事件触发子组件的方法**，

详见组件通信综合案例和状态提升后的多个类组件

**4、最后触发父组件 props （即父组件的回调函数）使用子组件传的值进行处理**

比如父组件的回调函数使用子组件传的值改变父组件的 state 。

这就像把电视遥控器传递给别人（用户）一样，

某人（用户）在 Door 组件（子组件）内按下了遥控器按键，

House组件（父组件）里的电视机就会换台或加大音量。

会发生什么取决于传给 Door 组件的遥控器是什么。

它可能控制的是房间里的电视、空调或高保真音响系统。

在 Door 组件内，某人（用户）需要做的只是按下遥控器的按键。

![img](https://cdn.nlark.com/yuque/0/2021/gif/1614731/1612255711463-a82ae8ef-c4d1-44de-ab22-5820aa70527b.gif)

**注意：子组件不能改变父组件的值，只能够由父组件自身调用方法改变自身的值**

```jsx
class Child extends React.Component {
  render() {
    const {msg, handleClick} = this.props;
    return (
      <div>
        {
          // 2、 子组件触发父组件的事件回调，在事件回调里传值给父组件
        }
        <button
          onClick={() => {
            handleClick(msg)
          }}
        >
          点击按钮
        </button>
        <p> Child 组件</p>
      </div>
    )
  }
}

class Parent extends React.Component {
  // 3、父组件接收子组件传的值  
  handleClick = msg => {
    // input 点击打印的是事件对象（合成事件的）, Child 子组件 <button> 点击打印的是传给父组件的值
    console.log(msg);
    // input 点击和 Child 子组件 <button> 点击两次打印的 this 都是指向父组件 
    console.log(this); 
  }
  render() {
    return (
      <div>    
        <input type="button" value="点击" onClick={this.handleClick} />
        {
           // 1、 为子组件传入改变父组件回调函数作为 props
        }
        <Child msg="传给子组件的值" handleClick={this.handleClick} />
     </div>
    )
  }
}

ReactDOM.render(<Parent/>, document.getElementById('example'))
```

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1617687997828-9dc51d21-f883-4fa5-bd57-8d64992fbd8c.jpeg)

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1611042446124-75dfec22-bea1-4cd4-8687-68d0a0271e70.jpeg)

```jsx
import React, { Component } from 'react';

function CounterButton(props) {
  const { operator, btnClick } = props;
  return <button onClick={btnClick}>{operator}</button>
}

export default class App extends Component {
  constructor(props) {
    super(props);

    this.state = {
      counter: 0
    }
  }

  changeCounter(count) {
    this.setState({
      counter: this.state.counter + count
    })
  }

  render() {
    return (
      <div>
        <h2>当前计数: {this.state.counter}</h2>
        <CounterButton operator="+1" btnClick={e => this.changeCounter(1)} />
        <CounterButton operator="-1" btnClick={e => this.changeCounter(-1)} />
      </div>
    )
  }
}
```

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1611048978701-dd608061-6ac6-472d-8d84-8584f4090daf.jpeg)

### 组件通信综合案例

#### 新增用户

在 input 内输入新用户的名称，然后点击新增按钮，调用 handleClick 方法，

在 handleClick 内部，调用通过 props 传递过来的 onAddUser 执行保存用户的逻辑。

子组件 UserList 

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1612234355786-e63503fb-59a3-49f4-bc4c-6300d17bae90.jpeg)

下面来看一下 onAddUser 在 UserListContainer 中的实现：

父组件 UserListContainer 

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1612237932803-f038640c-7330-4fb8-93a1-d5cbc3ece37b.jpeg)

子组件 UserList 通过调用 props.onAddUser 方法成功地将待新增的用户 this.state.newUser 

传递给父组件 UserListContainer 的 handleAddUser 方法执行保存操作，

保存成功后，UserListContainer 会更新状态 users ，从而又将最新的用户列表传递给 UserList 。

这一过程既包含子组件到父组件的通信，又包含父组件到子组件的通信，

而通信的桥梁就是通过 props 传递的数据和回调函数。

#### 选项卡切换

```jsx
import React from 'react';
import ReactDOM from 'react-dom';
import './Tab/style.css';
import Tabs from './Tab/Tabs';

ReactDOM.render(
  <Tabs/>,
  document.getElementById('root')
);


import React, {Component} from 'react';
import Tab from './Tab';

// 父组件 Tabs
export default class Tabs extends Component {
  constructor(props) {
    super(props);
    
    this.titles = ['流行', '新款', '精选'];
    this.state = {
      currTitle: '流行'
    };
  }
  // 8、父组件根据子组件汇报的 index，取 titles 的某一个元素来改变自己的状态 currTitle
  itemClick(index) {
    this.setState({
      currTitle: this.titles[index]
    })
  }

  render() {
    return (
      <div>
        {/* 1、this.titles 直接传给子组件。this.itemClick 在回调函数里 ，作为 props 传给子组件*/}
        <Tab titles={this.titles} itemClick={index => this.itemClick(index)}/>
        {/* 10、显示 currTitle */}
        <h2>{this.state.currTitle}</h2>
      </div>
    )
  }
}


import React, {Component} from 'react';

// 子组件 Tab
export default class Tab extends Component {
  constructor(props) {
    super(props);
    
    this.state = {
      currIndex: 0
    };
  }
  
  itemClick(index) {
    // 6、根据父组件传入的状态 titles 改变子组件的状态，用于判断是否添加类名 active
    this.setState({
      currIndex: index
    });
    // 7、调用父组件的事件，告诉父组件点击的是 titles 里的哪一个元素
    this.props.itemClick(index);
  }
  
  render() {
    // 2、拿到父组件传入的状态 titles
    const {titles} = this.props;
    // 3、拿到子组件的状态 currIndex
    const {currIndex} = this.state;
    return (
      <div className="tab-control"> 
       {
         // 4、根据父组件传入的状态 titles 循环输出一组 html 元素（内容为 titles 里的数据）
         titles.map((item, index) => {
           return (
             // 5、子组件监听点击，并根据 titles 的 index 改变自己的状态 currIndex
             // 9、判断父组件传入的状态 index 是否与子组件的状态 currIndex 相同来决定是否添加类名 active
             <div className="tab-item" onClick={e => this.itemClick(index)} key={index}> 
               <span className={"title " + (index === currIndex ? "active" : "")}>{item}</span>
             </div>
           )
         })
       }
      </div>
    )
  }
}

.tab-control {
  height: 40px;
  line-height: 40px;
  display: flex;
}

.tab-control .tab-item {
  flex: 1;
  text-align: center;
}

.tab-control .title {
  padding: 3px 5px;
}

.tab-control .title.active {
  color: red;
  border-bottom: 3px solid red;
}
```

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1611115651823-4a9c713b-fd59-4391-a89f-b968e664da4d.jpeg )

### 状态提升（兄弟组件通信）

**在 React 中，将多个组件中需要共享的 state 向上移动到它们的最近共同父组件中，实现共享 state。**

**父组件负责管理多个子组件的公共状态，这就是所谓的“状态提升”。**

#### 通俗易懂的解释

> 参考：https://zhuanlan.zhihu.com/p/44784850
>

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1612255310494-26bf00a8-c20c-4dba-a8c2-357d1df54a7e.jpeg)

这是一只嗜睡的猫，门一关它就睡，只有当门开启的时候才会醒来。

需实现：房子 => 门开、唤醒猫

因为门和猫的状态都是私有的，不能在 Door 组件访问 Cat 组件的状态，

同理，Cat 组件也不能访问 Door 组件的状态。

要保持门和猫的状态同步，我们必须要在某处能同时访问两者。

所以门和猫的状态需通过房子的 state 进行连接

![img](https://cdn.nlark.com/yuque/0/2021/gif/1614731/1612255252686-b1c47275-e2b0-4e38-bb14-e17c9f7d01dc.gif)

完整的伪代码如下：

```
House:
  State: doorStatus <!-- 'open' or 'closed' -->
  toggleDoorStatus:
    如果 state.doorStatus 为 'open'
      将 state.doorStatus 修改成 'closed'
    否则
      将 state.doorStatus 修改成 'open'
  <div>
    ...
    <Door status={state.doorStatus} onClickAction={toggleDoorStatus} />
    <Cat status={如果 state.doorStatus 为 'open' 值为 'awake' 否则为 'sleeping'} />
  </div>

Door:
  <div>{props.status} door</div>
  当点击门时
    执行 props.onClickAction

Cat:
  <div>{props.status} cat</div>
```

#### 状态提升官网示例

##### 函数组件（子组件）使用类组件（父组件）的状态

子组件 BoilingVerdict 、父组件  Calculator 

```jsx
import React, {Component} from 'react';

function BoilingVerdict(props) {
  const {celsius} = props;
  if (celsius >= 100) {
    return <p>水开了</p>
  }
  else {
    return <p>水没开</p>
  }
}

export default class Calculator extends Component {
  constructor(props) {
    super(props);
    this.state = {
      temperature: ''
    };
    this.handleChange = this.handleChange.bind(this);
  }
  
  handleChange(e) {
    this.setState({
      temperature: e.target.value
    });
  }
  
  render () {
    const {temperature} = this.state;
    return (
      <fieldset>
        <legend>输入摄氏度:</legend>
        <input type="text" value={temperature} onChange={this.handleChange}/>
        <BoilingVerdict celsius={parseFloat(temperature)}/>
      </fieldset>
    );
  }
}

import React from 'react';
import ReactDOM from 'react-dom';
import Calculator from './TemperatureCalculator/TemperatureCalculator'

ReactDOM.render(
  <Calculator/>,
  document.getElementById('root')
);
```

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1611226863309-d9a84cbf-6419-4968-8e58-1492c9278b91.jpeg)

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1611226919184-79f8786a-477f-437b-b3f6-edc9e9269786.jpeg)

##### 没有状态提升的多个类组件

没有状态提升时，一个类组件多次复用，状态也会有多个，但不会互相影响

子组件 Temperature 、父组件 Calculator

```jsx
import React, {Component} from 'react';

const unit = {
  c: '摄氏度',
  f: '华氏度'
}

class Temperature extends Component {
  constructor(props) {
    super(props);  
    // 状态 temperature 在子组件 Temperature 里  
    this.state = {
      temperature: ''
    };
    this.handleChange = this.handleChange.bind(this);
  }
  
  handleChange(e) {
    this.setState({
      temperature: e.target.value
    });
  }
  
  render () {
    const scale = this.props.scale;
    const {temperature} = this.state.temperature;
    return (
      <fieldset>
        <legend>输入{unit[scale]}:</legend>
        <input type="text" value={temperature} onChange={this.handleChange}/>
      </fieldset>
    );
  }
}

export default class Calculator extends Component {
  render() {
    return (
      <div>
        {
          // 多个相同的子组件，多个状态，不会互相影响
        }    
        <Temperature scale="c"/>
        <Temperature scale="f"/>
      </div>
    )
  }
}

import React from 'react';
import ReactDOM from 'react-dom';

import Calculator from './TemperatureCalculator/TemperatureCalculator'

ReactDOM.render(
  <Calculator/>,
  document.getElementById('root')
);
```

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1611227233832-10f79969-560e-40bc-9ca3-19baf3663bc7.jpeg)

##### 状态提升后的多个类组件

子组件 BoilingVerdict 、 Temperature 、父组件 Calculator

将多个子组件相同的状态提升到父组件后，

由一个父组件共享一个状态给多个子组件，可以使子组件（Temperature）变化影响其他子组件（BoilingVerdict）

**当父组件 setState 更新状态后，父组件 render() 都会重新调用一次，子组件随之一起重新渲染**

另一个输入框的温度值通过父组件 temperature 、scale 的状态变化与转换方法获得

```jsx
import React, {Component} from 'react';

function toCelsius(fahrenheit) {
  return (fahrenheit - 32) * 5 / 9;
}

function toFahrenheit(celsius) {
  return (celsius * 9 / 5) + 32;
}

function tryConvert(temperature, convert) {
  const input = parseFloat(temperature);
  // Number.isNaN(input) 字符串解析成数字是否失败，失败则为 true，返回 ''
  // 即 input 不是数字，则为 true，返回 ''
  if (Number.isNaN(input)) {
    return '';
  }
  const output = convert(input);
  const rounded = Math.round(output * 1000) / 1000;
  return rounded.toString();
}

function BoilingVerdict(props) {
  const {celsius} = props;
  if (celsius >= 100) {
    return <p>水开了</p>
  }
  else {
    return <p>水没开</p>
  }
}

const unit = {
  c: '摄氏度',
  f: '华氏度'
}

class Temperature extends Component {
  constructor(props) {
    super(props);   
    this.handleChange = this.handleChange.bind(this);
  }
  
  handleChange(e) {
    this.props.onTemperatureChange(e.target.value);
  }
  
  render () {
    const {temperature, scale} = this.props;
    return (
      <fieldset>
        <legend>输入{unit[scale]}:</legend>
        <input 
          value={temperature} 
          onChange={this.handleChange}/>
      </fieldset>
    );
  }
}

export default class Calculator extends Component {
  constructor(props) {
    super(props);
    // 状态 temperature 提升到 Calculator 父组件上  
    this.state = {
      temperature: '',
      scale: 'c'
    };
    this.handleCelsiusChange = this.handleCelsiusChange.bind(this);
    this.handleFahrenheitChange = this.handleFahrenheitChange.bind(this);
  }
  
  handleCelsiusChange(temperature) {
    this.setState({
      temperature,
      scale: 'c'
    });
  }
  
  handleFahrenheitChange(temperature) {
    this.setState({
      temperature,
      scale: 'f'
    });
  }
  
  render() {
    const {temperature, scale} = this.state;
    const celsius = scale === 'f' ? tryConvert(temperature, toCelsius) : temperature;
    const fahrenheit = scale === 'c' ? tryConvert(temperature, toFahrenheit) : temperature;
    return (
      <div>
        <Temperature 
          temperature={celsius} 
          scale="c" 
          onTemperatureChange={this.handleCelsiusChange}/>
        <Temperature 
          temperature={fahrenheit} 
          scale="f" 
          onTemperatureChange={this.handleFahrenheitChange}/>
        <BoilingVerdict 
          celsius={parseFloat(celsius)}/>
      </div>
    )
  }
}

import React from 'react';
import ReactDOM from 'react-dom';

import Calculator from './TemperatureCalculator/TemperatureCalculator'

ReactDOM.render(
  <Calculator/>,
  document.getElementById('root')
);
```

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1611233588905-094e7283-2b33-4a61-b544-cb7758cf93f8.jpeg)![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1611233605321-93aa6464-0a8e-446c-9502-f2ef83410818.jpeg)![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1611233620794-ad93a2b2-7aa3-496b-8f47-19639c9a0657.jpeg)

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1611283770925-e1ec7998-f0e4-4f72-aa84-f52c2dffa0d5.jpeg)

![img](https://cdn.nlark.com/yuque/0/2021/gif/1614731/1611285493174-3ea66afc-65e8-4e82-a729-fe17d17f95c2.gif)

### 跨组件通信

直到现在，将数据传递到同级组件的唯一方法是将状态移动到更高的组件，

然后通过 props 将其向下传递给同级组件：

![img](https://cdn.nlark.com/yuque/0/2021/png/1614731/1612247065464-b82bea21-d6af-423a-b77e-c64104ea0b79.png)

但是，如果以后同级的兄弟组件也需要数据，则必须再次提升状态并将其传递过去：

![img](https://cdn.nlark.com/yuque/0/2021/png/1614731/1612247154758-e7cd931d-973d-41e4-b46c-cb8618b8d918.png)

尽管此方案确实有效，但如果不同分支上的组件需要数据，就会出现问题：

![img](https://cdn.nlark.com/yuque/0/2021/png/1614731/1612247213880-49289de5-504b-4316-a618-c41bf4b9c491.png)

在这种情况下，

我们需要将状态从顶层组件通过所有中间层组件传递到需要数据的底层组件，即使中间层组件不需要它。

![img](https://cdn.nlark.com/yuque/0/2021/png/1614731/1612247561984-c8753a11-f71e-4fa5-9a0b-6c769a9244d7.png)

**props 单向数据流动：**

![img](https://cdn.nlark.com/yuque/0/2021/png/1614731/1612248478253-f931a954-cf42-4ab7-b5f5-4265e213f5e9.png)

**context 跨组件数据流动：**

![img](https://cdn.nlark.com/yuque/0/2021/png/1614731/1612248537153-64c3d380-70a1-4075-b878-11416f2ebe44.png)

#### Context 简介

**父子组件通信对于某些类型的属性而言是极其繁琐的（例如：城市地区），**

**这些属性是应用程序中许多组件都需要的。**

Context 提供了一种在组件之间共享此类值的方式，而不必显式地通过组件树的逐层传递 props 。

**Context 设计目的是为了共享那些对于一个组件树而言是“全局”的数据，例如当前用户、主题或使用的语言。**

一般情况下，在没有绝对把握用好和必须的场景下，是不推荐使用的。

但是我们依然间接地使用着它，比如许多官方依赖在使用，

如：react-redux, mobx-react，react-router。

我们需要这个功能的时候，更多是靠第三方依赖库就能实现，而不是自己手动写 context 。

#### 原则

**Context  可以穿透（无视） React.memo 或者 shouldComponentUpdate 的比对，**

**也就是说，一旦 Context 的 Value 变动，所有依赖该 Context 的组件会全部强制重新渲染。**

**Context 中只定义被大多数组件所共用的属性，例如当前用户的信息、主题或者选择的语言。**

#### Context API 

**一个方法、两个组件、一个属性**

##### React.createContext()

```jsx
// 这个方法用来创建 context 对象，并包含 <Provider />、<Consumer /> 两个组件
const {Provider, Consumer} = React.createContext();

// 如果一个组件订阅了 Context ，那么这个组件会从离自身最近的那个匹配的 Provider 中读取当前的 context 值

// 常用
// defaultValue 是组件在顶层查找过程中没有找到对应的 Provider，那么就使用默认值
const MyContext = React.createContext(defaultValue);
```

##### Provider

```jsx
// 数据的生产者，通过 value 属性接收存储的共享数据，传递给子组件或后代组件

<Provider value={/* some value */}>
  
// 常用
<MyContext.Provider value={/* 某个值 */}>
```

每个 Context 对象都会返回一个 Provider 组件，它允许消费者 Consumer 组件订阅 context 的变化：

- Provider 接收一个 `value` 属性，传递给消费者 Consumer 组件；

- 一个 Provider 可以和多个消费者 Consumer 组件有对应关系；

- 多个 Provider 也可以嵌套使用，里层的会覆盖外层的数据；

> **值得一提的是每当 Provider 的** `value` **值发生改变时, 作为 Provider 后代的所有 Consumer 都会重新渲染**

在后代组件的最外层，通过生产者 Provider 组件进行包裹并存储共享数据到 value 中，

数据可以是任何数据类型。

##### Consumer

```jsx
// 数据的消费者，通过订阅 Provider 传入的 context 的值，来实时更新当前组件的状态

<Consumer>
  {value => /* render something based on the context value */}
</Consumer>
```

需要用到共享数据的组件均可通过消费者 Consumer 组件进行数据获取。

消费者组件 Consumer 订阅 context 对象的变化。

```jsx
// 常用
<MyContext.Consumer>
  {value => /* 基于 context 值进行渲染*/}
</MyContext.Consumer>
```

这里需要 函数 作为子元素（function as child）；

这个函数参数接收当前的 context 值，返回一个 React 元素；

##### contextType（可选，推荐）

contextType 属性由一个 React.createContext() 创建的 Context 对象赋值。

contextType 能让你使用 this.context 来消费最近 Context 上的那个值。

**可以在任何生命周期中使用 this.context 访问到最近 Context，包括 render 函数中。**

```jsx
class MyClass extends React.Component {
  componentDidMount() {
    let value = this.context;
    /* 在组件挂载完成后，使用 MyContext 组件的值来执行一些有副作用的操作 */
  }
  componentDidUpdate() {
    let value = this.context;
    /* ... */
  }
  componentWillUnmount() {
    let value = this.context;
    /* ... */
  }
  render() {
    let value = this.context;
    /* 基于 MyContext 组件的值进行渲染 */
  }
}

MyClass.contextType = MyContext;
```

##### 官方基本示例

```jsx
// Context 可以让我们无须明确地传遍每一个组件，就能将值深入传递进组件树。
// 为当前的 theme 创建一个 context（“light”为默认值）。
const ThemeContext = React.createContext('light');

class App extends React.Component {
  render() {
    // 使用一个 Provider 来将当前的 theme 传递给以下的组件树。
    // 无论多深，任何组件都能读取这个值。
    // 在这个例子中，我们将 “dark” 作为当前的值传递下去。
    return (
      <ThemeContext.Provider value="dark">
        <Toolbar />
      </ThemeContext.Provider>
    );
  }
}

// 中间的组件再也不必指明往下传递 theme 了。
function Toolbar() {
  return (
    <div>
      <ThemedButton />
    </div>
  );
}

class ThemedButton extends React.Component {
  // 指定 contextType 来读取当前的 theme context。
  // React 会往上找到最近的 theme Provider，然后使用它的值。
  // 在这个例子中，当前的 theme 值为 “dark”。
  static contextType = ThemeContext;
  // 相当于 ThemedButton.contextType = ThemeContext;
  render() {
    return <Button theme={this.context} />;
  }
}
```

```jsx
// 主要代码
// 过程：this.context => 找到 contextType 指向的 context => 寻找该 context 的 Provider

// 1
<ThemeContext.Provider value="dark">

// 2    
// 相当于 ThemedButton.contextType = ThemeContext;
static contextType = ThemeContext;
    
 // 3
<Button theme={this.context} />; // 寻找最近的 Provider
```

> 参考
>
> https://zh-hans.reactjs.org/docs/context.html#classcontexttype
>
> https://segmentfault.com/a/1190000017758300
>
> https://www.jianshu.com/p/eba2b76b290b
>
> https://www.cnblogs.com/ifon/p/11514307.html
>
> https://blog.csdn.net/weixin_41564885/article/details/108502839

#### 两种使用方式

##### 示例1（不用 contextType，用 Consumer）

**基本流程：**

**createContext  产生 Provider 与  Consumer => Provider value => Consumer 函数**

```jsx
import React from 'react';

// 1
const {Provider, Consumer} = React.createContext('默认值')

class Person extends React.Component {
  constructor(props){
    super(props);
    this.state = {
      color : 'red'
    }
  }
  render() {
    return (
      <div>
        {/* 2 */}
        <Provider value={this.state.color}>
          <h1>我是父组件</h1>
          <Son></Son>
        </Provider>
      </div>
    );
  }
}

class Son extends Person {
  render() {
    return (
      <div>
        <h1>我是子组件</h1>
        <GrandSon></GrandSon>
      </div>
    )
  }
}

class GrandSon extends Son {
  render() {
    return (
      {/* 3 */}
      <Consumer>
        {
          (color) => 
            <div>
              <h1 style={{color}}>我是孙组件,我拿到了父组件 state 的 color 值:{color}</h1>
            </div>
        }
      </Consumer>
    )
  }
}

export default Person;
import React from 'react';
import ReactDOM from 'react-dom';

import Person from './RandomTest/RandomTest';

ReactDOM.render(
  <Person/>,
  document.getElementById('root')
);
```

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1612517629799-d04a7539-a4bd-4c29-b947-e608058b0fbd.jpeg)

##### 示例2（推荐使用 contextType 不用 Consumer）

**基本流程：**

**createContext => 自定义Context => 组件contextType 指向 自定义Context => 自定义Context.Provider value => this.context**

```jsx
import React, { Component } from 'react';

// 1
const UserContext = React.createContext({ nickname: "默认", level: -1 })

export default class App extends Component {
  render() {
    return (
      <div>
        {/* 2 */}
        <UserContext.Provider value={{ nickname: "why", level: 99 }}>
          <Profile />
        </UserContext.Provider>
        <h2>其他内容</h2>
      </div>
    )
  }
}

class Profile extends Component {
  render() {
    return (
      <div>
        <ProfileHeader />
        <ul>
          <li>设置1</li>
          <li>设置2</li>
          <li>设置3</li>
          <li>设置4</li>
          <li>设置5</li>
        </ul>
      </div>
    )
  }
}

class ProfileHeader extends Component {
  render() {
    return (
      <div>
        {/* 4 */}
        <h2>用户昵称: {this.context.nickname}</h2>
        <h2>用户等级: {this.context.level}</h2>
      </div>
    )
  }
}

// 3
ProfileHeader.contextType = UserContext;
```

**如果出现了如下代码：**

```jsx
<Profile />
<UserContext.Provider value={{ nickname: "why", level: 99 }}>
</UserContext.Provider>
```

`<Profile />` **并没有嵌套在 UserContext.Provider 里面作为子组件，**

`<ProfileHeader />` **会使用 React.createContext 的默认值**

#### 什么时候使用  Consumer  呢？

##### 1.当使用 value 的组件是一个函数式组件时

```jsx
function ProfileHeader(props) {
  return (
    <div>
      <UserContext.Consumer>
        {value => {
          return (
            <div>
              <h2>用户昵称: {value.nickname}</h2>
              <h2>用户等级: {value.level}</h2>
            </div>
          )
        }}
      </UserContext.Consumer>
    </div>
  )
}
```

##### 2.当组件中需要使用多个 Context 

**因为使用多个 contextType ，后一个 contextType 会覆盖前一个 contextType** ，

 如图：

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1616393402261-16e20406-4d5f-4d42-8fe3-af6ffa3a19f9.jpeg)

创建一个新的 Context

```jsx
const ThemeContext = React.createContext({ color: "black" });
```

Provider 的嵌套

```jsx
<UserContext.Provider value={{ nickname: "why", level: 99 }}>
  <ThemeContext.Provider value={{color: "red"}}>
    <Profile />
  </ThemeContext.Provider>
</UserContext.Provider>
```

使用 Consumer 的嵌套

```jsx
<UserContext.Consumer>
  {value => {
    return (
      <ThemeContext.Consumer>
        {
          theme => (
            <div>
              <h2 style={theme}>用户昵称: {value.nickname}</h2>
              <h2 style={theme}>用户等级: {value.level}</h2>
            </div>
          )
        }
      </ThemeContext.Consumer>
    )
  }}
</UserContext.Consumer>
```

#### 官方进阶使用示例

##### 组件组合代替 Context

如果有一个 Page 组件，它层层向下传递 user 和 avatarSize 属性，

从而让深度嵌套的 Link 和 Avatar 组件可以读取到这些属性：

在最后只有 Avatar 组件需要 user 和 avatarSize，那么层层传递这两个 props 就显得非常冗余。

而且一旦 Avatar 组件需要更多从来自顶层组件的 props，

你还得在中间层级一个一个加上去，这将会变得非常麻烦。

```jsx
<Page user={user} avatarSize={avatarSize} />
// ... 渲染出 ...
<PageLayout user={user} avatarSize={avatarSize} />
// ... 渲染出 ...
<NavigationBar user={user} avatarSize={avatarSize} />
// ... 渲染出 ...
<Link href={user.permalink}>
  <Avatar user={user} size={avatarSize} />
</Link>
```

如果只是想避免层层传递一些属性，

组件组合（component composition）有时候是一个比 context 更好的解决方案。(使用场景不多)

**无需 context 的组件组合方案是：**

**将 Link 包裹的 Avatar 组件自身作为 props 传递下去**，

因而中间组件无需知道 user 或者 avatarSize 等 props：

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1612169917747-afc81846-f2ef-4ef7-9702-d63d15fa7cf4.jpeg)

##### 动态 Context

**只使用 contextType，没有使用 Consumer**

theme-context.js

```jsx
export const themes = {
  light: {
    foreground: '#000000',
    background: '#eeeeee',
  },
  dark: {
    foreground: '#ffffff',
    background: '#222222',
  },
};

export const ThemeContext = React.createContext(
  themes.dark // 默认值
);
```

themed-button.js

```jsx
import {ThemeContext} from './theme-context';

class ThemedButton extends React.Component {
  render() {
    let props = this.props;
    let theme = this.context;
    return (
      <button
        {...props}
        style={{backgroundColor: theme.background}}
      />
    );
  }
}
// 接受 ThemeContext.Provider 传过来的值给 let theme = this.context;
// 没有 ThemeContext.Provider 包裹则使用 React.createContext() 的默认值给
// let theme = this.context;
ThemedButton.contextType = ThemeContext;

export default ThemedButton;
```

app.js

```jsx
import {ThemeContext, themes} from './theme-context';
import ThemedButton from './themed-button';

// 一个使用 ThemedButton 的中间组件，切换 App 组件 state 中的 theme 值实现动态 Context
function Toolbar(props) {
  return (
    <ThemedButton onClick={props.changeTheme}>
      Change Theme
    </ThemedButton>
  );
}

class App extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      theme: themes.light,
    };

    this.toggleTheme = () => {
      this.setState(state => ({
        theme:
          state.theme === themes.dark
            ? themes.light
            : themes.dark,
      }));
    };
  }

  render() {
    // 有 ThemeContext.Provider 包裹的 <ThemedButton /> 按钮组件使用 state 中的 theme 值，
    // 而没有 ThemeContext.Provider 包裹的 <ThemedButton /> 按钮组件使用默认的 theme 值
    return (
      <Page>
        <ThemeContext.Provider value={this.state.theme}>
          <Toolbar changeTheme={this.toggleTheme} />
        </ThemeContext.Provider>
        <Section>
          <ThemedButton />
        </Section>
      </Page>
    );
  }
}

ReactDOM.render(<App />, document.root);
```

##### 在嵌套组件中更新 Context

theme-context.js

```jsx
export const themes = {
  light: {
    foreground: '#000000',
    background: '#eeeeee',
  },
  dark: {
    foreground: '#ffffff',
    background: '#222222',
  },
};

// 传递给 createContext 的默认值数据结构，所有的 Consumer 都要保持一致！
export const ThemeContext = React.createContext({
  theme: themes.dark,
  toggleTheme: () => {},
});
```

theme-toggler-button.js

```jsx
import {ThemeContext} from './theme-context';

function ThemeTogglerButton() {
  // ThemeTogglerButton 不仅要获取 theme 值，
  // 它也要从 context 中获取 toggleTheme 函数
  return (
    <ThemeContext.Consumer>
      {({theme, toggleTheme}) => (
        <button
          onClick={toggleTheme}
          style={{backgroundColor: theme.background}}>
          Toggle Theme
        </button>
      )}
    </ThemeContext.Consumer>
  );
}

export default ThemeTogglerButton;
```

app.js

```jsx
import {ThemeContext, themes} from './theme-context';
import ThemeTogglerButton from './theme-toggler-button';

function Content() {
  return (
    <div>
      <ThemeTogglerButton />
    </div>
  );
}

class App extends React.Component {
  constructor(props) {
    super(props);

    this.toggleTheme = () => {
      this.setState(state => ({
        theme:
          state.theme === themes.dark
            ? themes.light
            : themes.dark,
      }));
    };

    // state 包含了更新函数，它会传给 provider
    this.state = {
      theme: themes.light,
      toggleTheme: this.toggleTheme,
    };
  }

  render() {
    // 整个 state 都传给 provider，在下面的嵌套组件中更新 Context
    return (
      <ThemeContext.Provider value={this.state}>
        <Content />
      </ThemeContext.Provider>
    );
  }
}

ReactDOM.render(<App />, document.root);
```

##### 消费多个 Context

如果一个组件需要两个或者更多的 context 值一起使用时，那么就需要嵌套 Provider 和 Consumer

```jsx
// Theme context，默认的 theme 是 “light” 值
const ThemeContext = React.createContext('light');

// 用户登录 context
const UserContext = React.createContext({
  name: 'Guest',
});

// 一个组件可能会消费多个 context
function Content() {
  return (
    <ThemeContext.Consumer>
      {theme => (
        <UserContext.Consumer>
          {user => (
            <ProfilePage user={user} theme={theme} />
          )}
        </UserContext.Consumer>
      )}
    </ThemeContext.Consumer>
  );
}

function Layout() {
  return (
    <div>
      <Sidebar />
      <Content />
    </div>
  );
}

class App extends React.Component {
  render() {
    const {signedInUser, theme} = this.props;

    // 提供初始 context 值的 App 组件
    return (
      <ThemeContext.Provider value={theme}>
        <UserContext.Provider value={signedInUser}>
          <Layout />
        </UserContext.Provider>
      </ThemeContext.Provider>
    );
  }
}
```

## React 组件设计

**将 UI 分离为组件，其中每个组件需与数据模型的某部分匹配**

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1611639850114-ceaa2167-10d5-4b1d-ab2f-18459319dd81.jpeg)

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1611639980314-fc455714-e60c-48d4-826c-d7a9dd356c05.jpeg)

### 准则

**1、一个组件原则上只能负责一个功能**

如果它需要负责更多的功能，这时候应该考虑将它拆分成更小的组件。

向用户展示 JSON 数据模型时，

如果数据模型设计恰当，UI（或者说组件结构）便会与数据模型一一对应，

这是因为 UI 和数据模型都会倾向于遵守相同的信息结构。

**2、先渲染后交互、先用已有的数据模型渲染一个不包含交互功能的 UI**

最好将渲染 UI 和添加交互这两个过程分开。

这是因为：编写一个应用的静态版本时，往往要编写大量代码，而不需要考虑太多交互细节；

添加交互功能时则要考虑大量细节，而不需要编写太多代码。

所以，将这两个过程分开进行更为合适。

**个人理解：先写标签再写交互，先写一个大的组件再逐一拆分为小组件**

**3、构建应用的静态版本时，不需要 state。state 代表了随时间会产生变化的数据**

应当仅在实现交互时使用 state 。

**4、自上而下或者自下而上构建应用**

自上而下意味着首先编写层级较高的组件（比如 FilterableProductTable），

自下而上意味着从最基本的组件开始编写（比如 ProductRow）。

当你的应用比较简单时，使用自上而下的方式更方便；

对于较为大型的项目来说，自下而上地构建，并同时为低层组件编写测试是更加简单的方式。

**5、通过问自己以下三个问题，你可以逐个检查相应数据是否属于 state：**

**该数据是否是由父组件通过 props 传递而来的？如果可以通过  props 传递，那它不是 state。**

**该数据是否随时间的推移而保持不变？如果随时间不变，那它也不是 state。**

**能否根据其他 state 或 props 计算出该数据的值？如果需要计算，那它也不是 state。**

### 静态版本

由于构建的是静态版本，所以这些组件目前只需提供 render() 方法用于渲染。

最顶层的组件 FilterableProductTable 通过 props 接收数据模型。

如果你的数据模型发生了改变，再次调用 ReactDOM.render()，UI 就会相应地被更新。

过程：数据模型变化、调用 render() 方法、UI 相应变化，

因此很容易看清楚 UI 是如何被更新的，以及是在哪里被更新的。

React 单向数据流（也叫单向绑定）的思想使得组件模块化，易于快速开发。

```jsx
import React, {Component} from 'react';

class FilterableProductTable extends Component {
  render() {
    const {products} = this.props;
    return (
      <div>
        <SearchBar />
        <ProductTable products={products} />
      </div>
    );
  }
}

class SearchBar extends Component {
  render() {
    return (
      <form>
        <input type="text" placeholder="Search..." />
        <p>
          <input type="checkbox" />
          {' '}
          Only show products in stock
        </p>
      </form>
    );
  }
}

class ProductTable extends Component {
  render() {
    const rows = [];
    let lastCategory = null;
    
    this.props.products.forEach((product) => {
      // 防止产品类别重名重复，重复 push 同一个产品类别
      if (product.category !== lastCategory) {
        // 列表渲染记得加 key，这里使用名字作为组件的 key
        rows.push(
          <ProductCategoryRow
            category={product.category}
            key={product.category} />
        );
      }
      rows.push(
        <ProductRow
          product={product}
          key={product.name} />
      );
      // 防止产品类别重名重复，重复 push 同一个产品类别
      lastCategory = product.category;
    });

    return (
      <table>
        <thead>
          <tr>
            <th>Name</th>
            <th>Price</th>
          </tr>
        </thead>
        {/* 组件统一放到一个数组，统一使用插值符号按顺序该数组，其实是解析一组 jsx ：一个 ProductCategoryRow ,三个 ProductRow ...*/}
        <tbody>{rows}</tbody>
      </table>
    );
  }
}

class ProductCategoryRow extends Component {
  render() {
    const category = this.props.category;
    return (
      <tr>
        <th colSpan="2">
          {category}
        </th>
      </tr>
    );
  }
}

class ProductRow extends Component {
  render() {
    const product = this.props.product;
    // 没货则文字为红色，插值符号可以嵌套 JSX 
    const name = product.stocked ? product.name : <span style={{color: 'red'}}>{product.name}</span>;
    return (
      <tr>
        <td>{name}</td>
        <td>{product.price}</td>
      </tr>
    );
  }
}

export default FilterableProductTable;
import React from 'react';
import ReactDOM from 'react-dom';
import FilterableProductTable from "./FilterableProductTable/FilterableProductTable";

const PRODUCTS = [
  {category: 'Sporting Goods', price: '$49.99', stocked: true, name: 'Football'},
  {category: 'Sporting Goods', price: '$9.99', stocked: true, name: 'Baseball'},
  {category: 'Sporting Goods', price: '$29.99', stocked: false, name: 'Basketball'},
  {category: 'Electronics', price: '$99.99', stocked: true, name: 'iPod Touch'},
  {category: 'Electronics', price: '$399.99', stocked: false, name: 'iPhone 5'},
  {category: 'Electronics', price: '$199.99', stocked: true, name: 'Nexus 7'}
];

ReactDOM.render(
  <FilterableProductTable products={PRODUCTS} />,
  document.getElementById('root')
);
```

### 根据 UI 使用最少的 state

**我们的示例应用拥有如下数据：**

1. **包含所有产品的原始列表**

2. **用户输入的搜索词**

3. **复选框是否勾选的值**

所以，

包含所有产品的原始列表不是 state ，因为它是由 props 传入的；

搜索词和复选框的值是 state ，因为它们随时间会发生改变且无法由其他数据计算而来；

经过搜索筛选的产品列表不是 state，因为它的结果是由产品的原始列表根据搜索词和复选框的选择计算出来。

**综上所述，属于 state 的有：**

1. **用户输入的搜索词**

2. **复选框是否勾选的值**

### 确定 state 放在哪个组件

#### 策略

对于应用中的每一个 state：

- **找到需要根据这个 state 进行渲染的所有组件。**

- **找到它们的共同所有者（common owner）组件（在组件层级上高于所有需要该 state 的组件）。**

- **共同所有者组件或者比它层级更高的组件应该有这个 state。**

- **如果你找不到一个合适的位置来存放这个 state，可以直接创建一个新的组件来存放该 state，**

   **并将这个新组件放到高于共同所有者组件层级的位置。**

**根据以上策略重新思考我们的示例：**

ProductTable 需要根据 state 筛选产品列表。

SearchBar 需要展示搜索词和复选框的状态。

它们的共同所有者是 FilterableProductTable 。

因此，搜索词和复选框的值应该存放在 FilterableProductTable 组件中。

所以，我们已经决定把这些 state 存放在 FilterableProductTable 组件中。

**首先设置应用的初始 state，**

**将实例属性 `this.state = {filterText: '', inStockOnly: false}`**

**添加到 FilterableProductTable 的 constructor 中；**

**接着，**

**将 filterText 和 inStockOnly 作为 props 传入 ProductTable 和 SearchBar；**

**最后，**

**用这些 props 筛选 ProductTable 中的产品信息，并设置 SearchBar 的表单值。**

```jsx
import React, {Component} from 'react';

class FilterableProductTable extends Component {
  constructor(props) {
    super(props);
    this.state = {
      filterText: 'ball',
      inStockOnly: false
    }
  }
  render() {
    const {products} = this.props;
    const {filterText, inStockOnly} = this.state;
    return (
      <div>
        <SearchBar filterText={filterText} inStockOnly={inStockOnly}/>
        <ProductTable products={products} filterText={filterText} inStockOnly={inStockOnly}/>
      </div>
    );
  }
}

class SearchBar extends Component {
  render() {
    const {filterText, inStockOnly} = this.props;
    return (
      <form>
        <input type="text" placeholder="Search..." value={filterText}/>
        <p>
          <input type="checkbox" checked={inStockOnly}/>
          {' '}
          Only show products in stock
        </p>
      </form>
    );
  }
}

class ProductTable extends Component {
  render() {
    const rows = [];
    let lastCategory = null;
    const {filterText, inStockOnly} = this.props;

    this.props.products.forEach((product) => {
      // 如果字符串（搜索内容）模糊匹配 匹配不到，则不往下执行（不显示），并且过滤掉其他不匹配的产品
      if (product.name.indexOf(filterText) === -1) {
        return;
      }
      // 如果勾选了只显示有货的产品，但产品状态为无货，则不往下执行（不显示）
      if (inStockOnly && !product.stocked) {
        return;
      }
      // 防止产品类别重名重复，重复 push 同一个产品类别
      if (product.category !== lastCategory) {
        // 列表渲染记得加 key，这里使用名字作为组件的 key
        rows.push(
          <ProductCategoryRow
            category={product.category}
            key={product.category} />
        );
      }
      rows.push(
        <ProductRow
          product={product}
          key={product.name} />
      );
      // 防止产品类别重名重复，重复 push 同一个产品类别
      lastCategory = product.category;
    });

    return (
      <table>
        <thead>
          <tr>
            <th>Name</th>
            <th>Price</th>
          </tr>
        </thead>
        {/* 组件统一放到一个数组，统一使用插值符号按顺序该数组，其实是解析一组 jsx ：一个 ProductCategoryRow, ,三个 ProductRow ...*/}
        <tbody>{rows}</tbody>
      </table>
    );
  }
}

class ProductCategoryRow extends Component {
  render() {
    const category = this.props.category;
    return (
      <tr>
        <th colSpan="2">
          {category}
        </th>
      </tr>
    );
  }
}

class ProductRow extends Component {
  render() {
    const product = this.props.product;
    // 没货则文字为红色，插值符号可以嵌套 JSX 
    const name = product.stocked ? product.name : <span style={{color: 'red'}}>{product.name}</span>;
    return (
      <tr>
        <td>{name}</td>
        <td>{product.price}</td>
      </tr>
    );
  }
}

export default FilterableProductTable;
import React from 'react';
import ReactDOM from 'react-dom';
import FilterableProductTable from "./FilterableProductTable/FilterableProductTable";

const PRODUCTS = [
  {category: 'Sporting Goods', price: '$49.99', stocked: true, name: 'Football'},
  {category: 'Sporting Goods', price: '$9.99', stocked: true, name: 'Baseball'},
  {category: 'Sporting Goods', price: '$29.99', stocked: false, name: 'Basketball'},
  {category: 'Electronics', price: '$99.99', stocked: true, name: 'iPod Touch'},
  {category: 'Electronics', price: '$399.99', stocked: false, name: 'iPhone 5'},
  {category: 'Electronics', price: '$199.99', stocked: true, name: 'Nexus 7'}
];

ReactDOM.render(
  <FilterableProductTable products={PRODUCTS} />,
  document.getElementById('root')
);
```

将 filterText 设置为 "ball" 并刷新应用，就能发现表格中的数据已经更新了。

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1611647194531-eae09ed5-d8f8-401c-8c92-eb223934614c.jpeg)

### 反向数据流（父子组件通信实现双向绑定）

**子组件通知父组件修改父组件的 state，父组件更新 state 后，根据父组件 state 变化的子组件 props 也会更新。**

需要实现的功能：每当用户改变表单的值，我们需要改变 state 来反映用户的当前输入。

由于 state 只能由拥有它们的组件进行更改，

**父组件 FilterableProductTable 必须将一个能够触发 state 改变的回调函数（callback）**

**handleFilterTextChange 和 handleInStockChange 传递给子组件 SearchBar。**

**我们可以使用输入框的 onChange 事件来监视用户输入的变化，**

**并通知父组件 FilterableProductTable 传递给子组件 SearchBar 的回调函数。**

**然后该回调函数将调用父组件  FilterableProductTable 的 setState()，从而更新应用，**

**更新了父组件 FilterableProductTable 的 state 即更新子组件的 props。**

```jsx
import React, {Component} from 'react';

class FilterableProductTable extends Component {
  constructor(props) {
    super(props);
    this.handleFilterTextChange = this.handleFilterTextChange.bind(this);
    this.handleInStockChange = this.handleInStockChange.bind(this);
    this.state = {
      filterText: '',
      inStockOnly: false
    }
  }
  
  handleFilterTextChange(filterText) {
    this.setState({
      filterText: filterText
    })
  }
  
  handleInStockChange(inStockOnly) {
    this.setState({
      inStockOnly: inStockOnly
    })
  }

  render() {
    const {products} = this.props;
    const {filterText, inStockOnly} = this.state;
    return (
      <div>
        <SearchBar 
          filterText={filterText} 
          inStockOnly={inStockOnly} 
          onFilterTextChange={this.handleFilterTextChange} 
          onInStockChange={this.handleInStockChange}/>
        <ProductTable 
          products={products} 
          filterText={filterText} 
          inStockOnly={inStockOnly}/>
      </div>
    );
  }
}

class SearchBar extends Component {
  constructor(props) {
    super(props);
    this.handleFilterTextChange = this.handleFilterTextChange.bind(this);
    this.handleInStockChange = this.handleInStockChange.bind(this);
  }

  handleFilterTextChange(e) {
    const {onFilterTextChange} = this.props;
    onFilterTextChange(e.target.value);
  }
  
  handleInStockChange(e) {
    const {onInStockChange} = this.props;
    onInStockChange(e.target.checked);
  }

  render() {
    const {filterText, inStockOnly} = this.props;
    return (
      <form>
        <input type="text" placeholder="Search..." value={filterText} onChange={this.handleFilterTextChange}/>
        <p>
          <input type="checkbox" checked={inStockOnly} onChange={this.handleInStockChange}/>
          {' '}
          Only show products in stock
        </p>
      </form>
    );
  }
}

class ProductTable extends Component {
  render() {
    const rows = [];
    let lastCategory = null;
    const {filterText, inStockOnly} = this.props;

    this.props.products.forEach((product) => {
      // 如果字符串（搜索内容）模糊匹配 匹配不到，则不往下执行（不显示），并且过滤掉其他不匹配的产品
      if (product.name.indexOf(filterText) === -1) {
        return;
      }
      // 如果勾选了只显示有货的产品，但产品状态为无货，则不往下执行（不显示）
      if (inStockOnly && !product.stocked) {
        return;
      }
      // 防止产品类别重名，重复 push 同一个产品类别
      if (product.category !== lastCategory) {
        // 列表渲染记得加 key，这里使用名字作为组件的 key
        rows.push(
          <ProductCategoryRow
            category={product.category}
            key={product.category} />
        );
      }
      rows.push(
        <ProductRow
          product={product}
          key={product.name} />
      );
      // 防止产品类别重名，重复 push 同一个产品类别
      lastCategory = product.category;
    });

    return (
      <table>
        <thead>
          <tr>
            <th>Name</th>
            <th>Price</th>
          </tr>
        </thead>
        {/* 组件统一放到一个数组，统一使用插值符号按顺序解析该数组，其实是解析一组 jsx ：一个 ProductCategoryRow, ,三个 ProductRow ...*/}
        <tbody>{rows}</tbody>
      </table>
    );
  }
}

class ProductCategoryRow extends Component {
  render() {
    const category = this.props.category;
    return (
      <tr>
        <th colSpan="2">
          {category}
        </th>
      </tr>
    );
  }
}

class ProductRow extends Component {
  render() {
    const product = this.props.product;
    // 没货则文字为红色，插值符号可以嵌套 JSX 
    const name = product.stocked ? product.name : <span style={{color: 'red'}}>{product.name}</span>;
    return (
      <tr>
        <td>{name}</td>
        <td>{product.price}</td>
      </tr>
    );
  }
}

export default FilterableProductTable;


import React from 'react';
import ReactDOM from 'react-dom';
import FilterableProductTable from "./FilterableProductTable/FilterableProductTable";

const PRODUCTS = [
  {category: 'Sporting Goods', price: '$49.99', stocked: true, name: 'Football'},
  {category: 'Sporting Goods', price: '$9.99', stocked: true, name: 'Baseball'},
  {category: 'Sporting Goods', price: '$29.99', stocked: false, name: 'Basketball'},
  {category: 'Electronics', price: '$99.99', stocked: true, name: 'iPod Touch'},
  {category: 'Electronics', price: '$399.99', stocked: false, name: 'iPhone 5'},
  {category: 'Electronics', price: '$199.99', stocked: true, name: 'Nexus 7'}
];

ReactDOM.render(
  <FilterableProductTable products={PRODUCTS} />,
  document.getElementById('root')
);
```

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1611647628842-832f05a9-49c4-4f0c-bcd0-8e67c339b560.jpeg)

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1611647657213-9df153d6-05ff-46ab-b11b-bf539313a24b.jpeg)

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1611647676612-91048021-a7b8-432e-ac27-db530988fea3.jpeg)

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1611647692194-e1de54a9-cd2f-49c6-ba0f-e847a2011586.jpeg)

## 组件的生命周期

React 组件也有自己的生命周期，了解组件的生命周期可以让我们在最合适的地方完成自己想要的功能；

### 生命周期和生命周期函数的关系

生命周期是一个抽象的概念，在生命周期的整个过程，分成了很多个阶段；

- 比如挂载阶段（Mount），组件第一次在 DOM 树中被渲染的过程；

- 比如更新过程（Update），组件状态发生变化，重新更新渲染的过程；

- 比如卸载过程（Unmount），组件从 DOM 树中被移除的过程；

React 内部为了告诉我们当前处于哪些阶段，

会对我们组件内部实现的某些函数进行回调，这些函数就是生命周期函数：

- 比如实现 componentDidMount 函数：组件已经挂载到 DOM 上时，就会回调；

- 比如实现 componentDidUpdate 函数：组件已经发生了更新时，就会回调；

- 比如实现 componentWillUnmount 函数：组件即将被移除时，就会回调；

我们可以在这些回调函数中编写自己的逻辑代码，来完成自己的需求功能；

我们谈 React 生命周期时，谈的是类组件的生命周期，

因为函数组件是没有生命周期函数的（可以通过 hooks 来模拟一些生命周期的回调）

### 生命周期函数解析

我们先来学习一下最基础、最常用的生命周期函数：

![img](https://cdn.nlark.com/yuque/0/2020/png/1614731/1608213884471-a780c4b7-510f-4623-ae9f-316477d4c458.png)

**上图第一个区域解析：**

- 当我们挂载一个组件时，会先执行 constructor 构造方法来创建组件；

- 紧接着调用 render 函数，获取要渲染的 DOM 结构（ jsx ），并且开始渲染 DOM；

- 当组件挂载成功（ DOM 渲染完成），会执行 componentDidMount 生命周期函数；

**上图第二个区域解析：**

- 当我们通过修改 props，或者调用 setState 修改内部状态，或者直接调用 forceUpdate 时会重新调用 render 函数，进行更新操作；

- 当更新完成时，会回调 componentDidUpdate 生命周期函数；

**上图第三个区域解析：**

- 当我们的组件不再使用，会被从 DOM 中移除掉（卸载）；

- 这个时候会回调 componentWillUnmount 生命周期函数；

### constructor

```jsx
constructor(props);
```

如果不初始化 state 或不进行方法绑定，则不需要为 React 组件实现构造方法。

**constructor 中通常只做两件事情：**

- **通过给 this.state 赋值对象来初始化内部的 state，constructor 是唯一可以给 this.state 赋值的地方；**

- **为事件绑定实例（this）;**

###  getDerivedStateFromProps

**用它来比较 this.props 和 nextProps 来重新 setState ，让组件在 props 变化时更新 state 。**

**getDerivedStateFromProps 会在调用 render 方法之前调用，并且在初始挂载及后续更新时都会被调用。**

它返回一个对象来更新 state，如果返回 null 则不更新任何内容。

### componentDidMount

```jsx
componentDidMount()
```

componentDidMount() 会在组件挂载后（插入 DOM 树中）立即调用。

**componentDidMount 中通常在哪里操作呢？**

- **依赖于 DOM 的操作可以在这里进行；**

- **在此处发送网络请求就最好的地方；（官方建议）**

- **可以在此处添加一些订阅（会在 componentWillUnmount 取消订阅）；**

### componentDidUpdate

```jsx
componentDidUpdate(prevProps, prevState, snapshot)
```

**componentDidUpdate() 会在更新后会被立即调用，首次渲染不会执行此方法。**

- **当组件更新后，可以在此处对 DOM 进行操作；**

- **如果你对更新前后的 props 进行了比较，也可以选择在此处进行网络请求；（例如，当 props 未发生变化时，则不会执行网络请求）。**

```jsx
componentDidUpdate(prevProps) {
  // 典型用法（不要忘记比较 props）：
  if (this.props.userID !== prevProps.userID) {
    this.fetchData(this.props.userID);
  }
}
```

### componentWillUnmount

```jsx
componentWillUnmount()
```

**componentWillUnmount() 会在组件卸载及销毁之前直接调用。**

- **在此方法中执行必要的清理操作；**

- **例如，清除 timer，取消网络请求或清除在 componentDidMount() 中创建的订阅等；**

### 验证所有生命周期函数

```jsx
import React, { Component } from 'react';

class HYTestCpn extends Component {
  componentWillUnmount() {
    console.log("HYTestCpn componentWillUnmount");
  }
  
  render() {
    return <h2>HYTestCpn</h2>
  }
}

export default class App extends Component {
  constructor(props) {
    super(props);

    this.state = {
      counter: 0
    }

    console.log("调用constructor方法");
  }

  increment() {
    this.setState({
      counter: this.state.counter + 1
    })
  }

  componentDidMount() {
    console.log("调用componentDidMount方法");
  }

  componentDidUpdate() {
    console.log("调用componentDidUpdate方法");
  }

  componentWillUnmount() {
    console.log("调用componentWillUnmount方法");
  }
  
  render() {
    console.log("调用render方法")
    return (
      <div>
        <h2>当前计数: {this.state.counter}</h2>
        {this.state.counter <= 5 && <HYTestCpn/>}
        <button onClick={e => this.increment()}>+1</button>
      </div>
    )
  }
}
```

### 不常用的生命周期函数

除了上面介绍的生命周期函数之外，还有一些不常用的生命周期函数：

- getDerivedStateFromProps：state 的值在任何时候都依赖于 props 时使用；该方法返回一个对象来更新 state ；

- getSnapshotBeforeUpdate：在 React 更新 DOM 之前回调的一个函数，可以获取 DOM 更新前的一些信息（比如说滚动位置）；

- shouldComponentUpdate：该生命周期函数很常用，讲性能优化时再来详细讲解；

![img](https://cdn.nlark.com/yuque/0/2021/png/1614731/1612514796176-cfc353e2-38f2-4e29-9aab-309e1f2c069d.png)

![img](https://cdn.nlark.com/yuque/0/2021/png/1614731/1611303985892-61d698e6-0562-4d99-bd16-66d51e0d2b99.png)

![img](https://cdn.nlark.com/yuque/0/2021/png/1614731/1611304009101-5a184cdb-8f14-4647-85ce-a2218bf28b0b.png)

### 生命周期阶段总结

1. **挂载阶段**

   constructor() => getDerivedStateFromProps => render => componentDidMount

3. **更新阶段**

   getDerivedStateFromProps => shoudeComponentUpdate => render => getSnapshotBeforeUpdate => componentDidUpdate

4. **卸载阶段**

    componentWillUnmount()

### shouldComponentUpdate

生命周期方法 shouldComponentUpdate（很多时候，我们简称为 SCU ），

这个方法接受参数，并且需要有返回值：

**该方法有两个参数：**

**参数一：nextProps 修改之后，最新的 props 属性**

**参数二：nextState 修改之后，最新的 state 属性**

**该方法返回值是一个 boolean 类型**

**返回值为 true ，那么就需要调用 render 方法；**

**返回值为 false，那么就不需要调用 render 方法；**

**默认返回的是 true，也就是只要 state 发生改变，就会调用 render 方法；**

**我们可以控制它返回的内容，来决定是否需要重新渲染。**

比如我们在 App 中增加一个 message 属性：

jsx 中并没有依赖这个 message，那么它的改变不应该引起重新渲染；

但是因为 render 监听到 state 的 message 改变，

就会重新 render，所以最后 render 方法还是被重新调用了；

```jsx
export default class App extends Component {
  constructor(props) {
    super(props);

    this.state = {
      counter: 0,
      message: "Hello World"
    }
  }

  increment() {
    this.setState({
      counter: this.state.counter + 1
    })
  }

  changeText() {
    this.setState({
      message: "你好啊,李银河"
    })
  } 
  
  render() {
    console.log("App Render 被调用");

    return (
      <div>
        <h2>当前计数: {this.state.counter}</h2>
        <button onClick={e => this.increment()}>+1</button>
        <button onClick={e => this.changeText()}>改变文本</button>
        <Header/>
        <Main/>
        <Footer/>
      </div>
    )
  }
}
```

这个时候，我们可以通过实现 shouldComponentUpdate 来决定要不要重新调用 render 方法：

```jsx
shouldComponentUpdate(nextProps, nextState) {
  if (nextState.counter !== this.state.counter) {
    return true;
  }

  return false;
}
```

但是我们的代码依然没有优化到最好，因为当 counter 改变时，所有的子组件依然重新渲染了：

所以，事实上，我们应该实现所有的子组件的 shouldComponentUpdate ；

比如 Main 组件，可以进行如下实现：

shouldComponentUpdate 默认返回一个 false；

在特定情况下，需要更新时，我们在上面添加对应的条件即可；

```jsx
class Main extends Component {

  shouldComponentUpdate(nextProps, nextState) {
    return false;
  }

  render() {
    console.log("Main Render 被调用");
    return (
      <div>
        <Banner/>
        <ProductList/>
      </div>
    )
  }
}
```

#### PureComponent 和 memo

##### PureComponent

如果所有的类，我们都需要手动来实现 shouldComponentUpdate，

那么会给我们开发者增加非常多的工作量。

我们想一下 shouldComponentUpdate 中的各种判断的目的是什么？

**判断 props 或者 state 中的数据是否发生了改变，**

**来决定 shouldComponentUpdate 返回 true 或者false；**

**事实上 React 已经考虑到了这一点，所以 React 已经默认帮我们实现好了，如何实现呢？**

**将 class 继承自 PureComponent 。**

比如我们修改 Main 组件的代码：

```jsx
class Main extends PureComponent {
  render() {
    console.log("Main Render 被调用");
    return (
      <div>
        <Banner/>
        <ProductList/>
      </div>
    )
  }
}
```

**PureComponent 的原理是什么呢？**

**默认对新旧的 props 和 state 进行浅层比较**

##### memo

**如果是一个函数式组件呢？**

**我们需要使用一个高阶组件 memo：**

**我们将之前的 Header、Banner、ProductList 都通过 memo 函数进行一层包裹；**

**Footer 没有使用 memo 函数进行包裹；**

**最终的效果是，当 counter 发生改变时，Header、Banner、ProductList 的函数不会重新执行（因为没有 props 判断）,**

**而 Footer 的函数会被重新执行**

```jsx
import React, { Component, PureComponent, memo } from 'react';

const MemoHeader = memo(function() {
  console.log("Header Render 被调用");
  return <h2>Header</h2>
})

class Main extends PureComponent {
  render() {
    console.log("Main Render 被调用");
    return (
      <div>
        <MemoBanner/>
        <MemoProductList/>
      </div>
    )
  }
}

const MemoBanner = memo(function() {
  console.log("Banner Render 被调用");
  return <div>Banner</div>
})

const MemoProductList = memo(function() {
  console.log("ProductList Render 被调用");
  return (
    <ul>
      <li>商品1</li>
      <li>商品2</li>
      <li>商品3</li>
      <li>商品4</li>
      <li>商品5</li>
    </ul>
  )
})

function Footer() {
  console.log("Footer Render 被调用");
  return <h2>Footer</h2>
}

export default class App extends Component {
  constructor(props) {
    super(props);

    this.state = {
      counter: 0,
      message: "Hello World"
    }
  }

  increment() {
    this.setState({
      counter: this.state.counter + 1
    })
  }

  shouldComponentUpdate(nextProps, nextState) {
    if (nextState.counter !== this.state.counter) {
      return true;
    }

    return false;
  }

  changeText() {
    this.setState({
      message: "你好啊,李银河"
    })
  }
  
  render() {
    console.log("App Render 被调用");

    return (
      <div>
        <h2>当前计数: {this.state.counter}</h2>
        <button onClick={e => this.increment()}>+1</button>
        <button onClick={e => this.changeText()}>改变文本</button>
        <MemoHeader/>
        <Main/>
        <Footer/>
      </div>
    )
  }
}
```

**默认情况下只会对 props 的复杂对象做浅层对比，**

**如果想要控制对比过程，需将自定义的比较函数通过第二个参数传入来实现。**

```jsx
function MyComponent(props) {
  /* 使用 props 渲染 */
}
function areEqual(prevProps, nextProps) {
  /*
  如果把 nextProps 传入 render 方法的返回结果与
  将 prevProps 传入 render 方法的返回结果一致则返回 true，
  否则返回 false
  */
}
export default React.memo(MyComponent, areEqual);
```

**如果 props 相等，`areEqual` 会返回 `true`；**

**如果 props 不相等，则返回 `false`。**

**这与 `shouldComponentUpdate` 方法的返回值相反。**

## React 的插槽（组合与继承）

### JSX 组合（默认插槽 props.children）

`**<FancyBorder>` JSX 标签中包含的所有子元素内容都会作为一个 children prop 传递给 FancyBorder 组件。**

**FancyBorder 将 {props.children} 渲染在一个 `<div>` 中，被传递的这些子组件最终都会出现在输出结果中。**

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1611648356457-3066faad-7fb9-485c-96d5-45a7778a175f.jpeg)

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1611648551945-ad6fc210-dabd-44ff-9cb6-71492a614886.jpeg)

### props  组件组合（具名插槽）

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1611649122639-2536dbeb-fd43-4cf0-b523-f67f673c2cda.jpeg)

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1611648709988-e10f9eb4-7d5b-4acb-aaaf-ca2ab92c7acc.jpeg)

#### 特例组合

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1611650035422-dffc2dc9-5eea-4f5e-a9b4-25719023dc35.jpeg)

### 插槽示例

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1611121507459-8c604b76-cf05-482c-b797-df34b74717b2.jpeg)

```css
.nav-bar {
  display: flex;
  height: 44px;
  line-height: 44px;
  text-align: center;
}

.nav-bar .left, .nav-bar .right {
  width: 80px;
  background: #ffd093;
}

.nav-bar .center {
  flex: 1;
  background: #61dafb;
}
```



```jsx
import React, { Component } from 'react';
import './NavBar.css';

class NavTab extends Component {
  render() {
    const {leftSlot, centerSlot, rightSlot} = this.props; 

    return (
      <div className="nav-bar">
        <div className="item left">{leftSlot}</div>
        <div className="item center">{centerSlot}</div>
        <div className="item right">{rightSlot}</div>
      </div>
    )
  }
}

export default class NavBar extends Component {
  render() {
    const navLeft = <div>返回</div>
    const navCenter = <div>中心</div>
    const navRight = <div>更多</div>

    return (
      <div>
        <NavTab leftSlot={navLeft} centerSlot={navCenter} rightSlot={navRight}/>
      </div>
    ) 
  }
}
```



```jsx
// 或者使用 props.children
// 如果只有一个元素，那么 children 指向该元素；
// 如果有多个元素，那么 children 指向的是数组，数组中包含多个元素
import React, { Component } from 'react';
import './NavBar.css';

export default class NavTab extends Component {
  render() {
    return (
      <div>
        <NavBar>
          <div>返回</div>
          <div>购物</div>
          <div>更多</div>
        </NavBar>
      </div>
    )
  }
}

export default class NavBar extends Component {
  render() {
    return (
      <div className="nav-bar">
        <div className="item left">{this.props.children[0]}</div>
        <div className="item center">{this.props.children[1]}</div>
        <div className="item right">{this.props.children[2]}</div>
      </div>
    )
  }
}
```

## Portal （指定节点渲染的插槽）

Portal 可以有一个很形象的翻译 —— “传送门”。

引用 程墨大佬的解释：曾经有一款游戏就叫做 Portal，

玩家手上一杆很厉害很科幻的枪，朝墙上开一枪，就可以开出两个“传送门”，

人钻进这个传送门，可以从另一个传送门里走出来，

也就是说，两个不同位置的传送门之间形成了对接。

![img](https://cdn.nlark.com/yuque/0/2021/png/1614731/1618648996863-d8ed1a77-cef4-4b49-88dc-64e47dac9cf3.png)

Portal 提供了一种将子节点渲染到存在于父组件以外的 DOM 节点的优秀的方案：

第一个参数（child）是任何可渲染的 React 子元素，例如一个元素，字符串或 fragment；

第二个参数（container）是一个 DOM 元素；

```js
ReactDOM.createPortal(child, container)
```

**React 中， Portals 提供了一种很好的将子节点渲染到父组件以外的 DOM 节点的方式。**

**例如：我们在写一个弹出框组件 Modal 时，这个组件其实不属于父组件，**

**但是由于我们要把它显示出来，我们必须要把它挂在到父组件下，**

**这里就可以用到 createPortal，我们将它渲染到父组件以外。**

比如说，我们准备开发一个 Modal 组件，它可以将它的子组件渲染到屏幕的中间位置：

步骤一：修改 index.html 添加新的节点

```html
<div id="root"></div>
<!-- 新节点 -->
<div id="modal"></div>
```

步骤二：编写这个节点的样式：

```css
#modal {
  position: fixed;
  left: 50%;
  top: 50%;
  transform: translate(-50%, -50%);
  background-color: red;
}
```

步骤三：编写组件代码，渲染在新节点

```jsx
import React, { PureComponent } from 'react';
import ReactDOM from 'react-dom';

class Modal extends PureComponent {
  constructor(props) {
    super(props);
  }

  render() {
    // 将 this.props.children 渲染到 <div id="modal"></div> 里面  
    return ReactDOM.createPortal(
      this.props.children,
      document.getElementById("modal")
    )
  }
}

export default class App extends PureComponent {
  render() {
    return (
      <div>
        <Modal>
          <h2>我是标题</h2>
        </Modal>
      </div>
    )
  }
}
```

理解：Portal（传送门）

```html
<div id="root"></div>
<!-- 新节点 -->
<div id="modal">
   <h2>我是标题</h2>  
 </div>
```

## Fragment

在之前的开发中，我们总是在一个组件中返回内容时包裹一个 div 元素：

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1616662066121-a8450e39-4a14-475b-891b-2604c48da5d0.jpeg)

我们会发现多了一个 div 元素：

这个 div 元素对于某些场景是需要的（比如我们希望放到一个 div 元素中，再针对性设置样式）

**某些场景下这个 div 是没有必要的，比如当前这里我可能希望所有的内容直接渲染到 root 中即可；**

**我们希望可以不渲染这样一个 div ，应该如何操作？**

**使用 Fragment**

Fragment 允许你将子列表分组，而无需向 DOM 添加额外节点；

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1616662232500-9036de8c-49e2-4669-b4bd-a4aef1b23cd2.jpeg)

**Fragment 是一个特殊的标签组件，**

**该组件与 Vue 中的 template 类似，在渲染的时候都会将其去除掉，**

**该组件常在最外层包裹 render 内部的元素（当想要外部额外包裹一层多余函数的时候）**

```jsx
import React,{Fragment} from 'react'

function ActionLink() {
  function handleClick(e) {
    e.preventDefault();
    console.log('链接被点击');
  }
 
  return (
    <React.Fragment>
    <a href="#" onClick={handleClick}>
      点我
    </a>
     <a href="#" onClick={handleClick}>
      点我
     </a>
    {
       /*
       Fragment常常会被解构出来使用
        <Fragment>
        <a href="#" onClick={handleClick}>
          点我
        </a>
         <a href="#" onClick={handleClick}>
          点我
         </a>
         </Fragment>
       */   
    }
      </React.Fragment>
  );
}
```

### 短语法

**React 还提供了 Fragment 的短语法：**

**它看起来像空标签 <> </>；**

```jsx
export default class App extends PureComponent {
  render() {
    return (
      <>
        <h2>当前计数: 0</h2>
        <button>+1</button>
        <button>-1</button>
      </>
    )
  }
}
```

但是，如果我们需要在 Fragment 中添加 key ，那么就不能使用短语法：

```jsx
{
  this.state.friends.map((item, index) => {
    return (
      <Fragment key={item.name}>
        <div>{item.name}</div>
        <div>{item.age}</div>
      </Fragment>
    )
  })
}
```

这里是不支持如下写法的：

```jsx
<key={item.name}>
  <div>{item.name}</div>
  <div>{item.age}</div>
</>
```

## AntDesign 简单评论示例

```bash
yarn add antd
```

**Antd 是否会将一些没有用的代码（组件或者逻辑代码）引入，造成包很大呢？**

antd 官网有提到：antd 的 JS 代码默认支持基于 ES modules 的 tree shaking，

对于 js 部分，直接引入 import { Button } from 'antd' 就会有按需加载的效果。

CommentInput.js

```jsx
import React, { PureComponent } from 'react';
import moment from 'moment';
import { Input, Button } from "antd";

export default class CommentInput extends PureComponent {
  constructor(props) {
    super(props);

    this.state = {
      content: ""
    }
  }
  
  handleChange(event) {
    this.setState({
      content: event.target.value
    })
  }

  addComment() {
    const commentInfo = {
      id: moment().valueOf(),
      avatar: "https://upload.jianshu.io/users/upload_avatars/1102036/c3628b478f06.jpeg?imageMogr2/auto-orient/strip|imageView2/1/w/240/h/240",
      nickname: "Tom",
      datetime: moment(),
      content: this.state.content,
      comments: [
        
      ]
    }

    this.props.submitComment(commentInfo);

    this.setState({
      content: ""
    })
  }
  
  render() {
    return (
      <div>
        <Input.TextArea rows={4} 
                        value={this.state.content}
                        onChange={e => this.handleChange(e)}/>
        <Button type="primary" onClick={e => this.addComment()}>添加评论</Button>
      </div>
    )
  }
}
```

CommentItem.js

```jsx
import React, { PureComponent } from 'react';
import { Comment, Avatar, Tooltip } from "antd";
import { DeleteOutlined } from "@ant-design/icons";

export default class CommentItem extends PureComponent {
  removeItem() {
    this.props.removeItem();
  }
  
  render() {
    const { nickname, avatar, content, datetime } = this.props.comment;

    return (
      <Comment
        author={<a href="/#">{nickname}</a>}
        avatar={<Avatar src={avatar} alt={nickname} />}
        content={<p>{content}</p>}
        datetime={
          <Tooltip title={datetime.format("YYYY-MM-DD")}>
            <span>{datetime.fromNow()}</span>
          </Tooltip>
        }
        actions={[
          <span onClick={e => this.removeItem()}><DeleteOutlined />删除</span>
        ]}
      />
    )
  }
}
```

App.js

```jsx
import React, { PureComponent } from 'react';

import CommentInput from './components/CommentInput';
import CommentItem from './components/CommentItem';

export default class App extends PureComponent {
  constructor(props) {
    super(props);

    this.state = {
      commentList: []
    }
  }

  submitComment(info) {
    this.setState({
      commentList: [...this.state.commentList, info]
    })
  }

  removeComment(index) {
    const newCommentList = [...this.state.commentList];
    newCommentList.splice(index, 1);
    this.setState({
      commentList: newCommentList
    })
  }
  
  render() {
    return (
      <div style={{width: "500px", padding: "20px"}}>
        {
          this.state.commentList.map((item, index) => {
            return <CommentItem key={item.id} 
                                comment={item} 
                                removeItem={e => this.removeComment(index)}/>
          })
        }
        <CommentInput submitComment={this.submitComment.bind(this)}/>
      </div>
    )
  }
}
```

发布评论时间改为中文：

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1626936288277-3559f842-ef72-4ae5-8872-37c7cddfa37b.jpeg)

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1626700373138-23eaaeed-0d93-4f69-aeb1-f46f36660d93.jpeg)

## React 过渡动画

> 官网：http://reactcommunity.org/react-transition-group/
>

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1626619494928-68bedd1d-009a-4d75-825e-89abfa59c200.jpeg)

```bash
# npm
npm install react-transition-group --save

# yarn
yarn add react-transition-group
```

### CSSTransition

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1626936684163-6dca4817-9232-46fb-86c9-713dbee78c72.jpeg)

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1626936684033-deb5625e-3b2d-4916-a882-9474551800bd.jpeg)

App.js

```jsx
import React, { PureComponent } from 'react';
import { CSSTransition } from 'react-transition-group';
import { Card, Avatar, Button } from 'antd';
import { EditOutlined, EllipsisOutlined, SettingOutlined } from '@ant-design/icons';
import "antd/dist/antd.css";
import './App.css';

const { Meta } = Card;

export default class App extends PureComponent {
  constructor(props) {
    super(props);

    this.state = {
      isShowCard: true
    }
  }

  render() {
    return (
      <div>
        <Button type="primary" onClick={e => this.setState({isShowCard: !this.state.isShowCard})}>显示/隐藏</Button>
        <CSSTransition in={this.state.isShowCard}
                       classNames="card"
                       timeout={1000}
                       unmountOnExit={true}
                       onEnter={el => console.log("进入动画前")}
                       onEntering={el => console.log("进入动画")}
                       onEntered={el => console.log("进入动画后")}
                       onExit={el => console.log("退出动画前")}
                       onExiting={el => console.log("退出动画")}
                       onExited={el => console.log("退出动画后")}
                      >
          <Card
            style={{ width: 300 }}
            cover={
              <img
                alt="example"
                src="https://gw.alipayobjects.com/zos/rmsportal/JiqGstEfoWAOHiTxclqi.png"
              />
            }
            actions={[
              <SettingOutlined key="setting" />,
              <EditOutlined key="edit" />,
              <EllipsisOutlined key="ellipsis" />,
            ]}
          >
            <Meta
              avatar={<Avatar src="https://zos.alipayobjects.com/rmsportal/ODTLcjxAfvqbxHnVXCYX.png" />}
              title="Card title"
              description="This is the description"
            />
          </Card>
        </CSSTransition>
      </div>
    )
  }
}
```

App.css

```css
.card-enter, .card-appear {
  opacity: 0;
  transform: scale(.8);
}

.card-enter-active, .card-appear-active {
  opacity: 1;
  transform: scale(1);
  transition: opacity 300ms, transform 300ms;
}

.card-exit {
  opacity: 1;
}

.card-exit-active {
  opacity: 0;
  transform: scale(.8);
  transition: opacity 300ms, transform 300ms;
}
```

![img](https://cdn.nlark.com/yuque/0/2021/gif/1614731/1626874335925-d1e7d173-0654-49df-b386-408b03a879b8.gif)

### SwitchTransition

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1626936684174-bab54d15-de70-4b98-bbe3-638b54140750.jpeg)

App.js

```jsx
import React, { PureComponent } from 'react';
import { SwitchTransition, CSSTransition } from "react-transition-group";
import './App.css';

export default class App extends PureComponent {
  constructor(props) {
    super(props);

    this.state = {
      isOn: true
    }
  }
  
  btnClick() {
    this.setState({isOn: !this.state.isOn})
  }

  render() {
    const {isOn} = this.state;

    return (
      <SwitchTransition mode="out-in">
        <CSSTransition classNames="btn"
                       timeout={500}
                       key={isOn ? "on" : "off"}>
          {
          <button onClick={this.btnClick.bind(this)}>
            {isOn ? "on": "off"}
          </button>
        }
        </CSSTransition>
      </SwitchTransition>
    )
  }
}
```

App.css

```css
.btn-enter {
  transform: translate(100%, 0);
  opacity: 0;
}

.btn-enter-active {
  transform: translate(0, 0);
  opacity: 1;
  transition: all 500ms;
}

.btn-exit {
  transform: translate(0, 0);
  opacity: 1;
}

.btn-exit-active {
  transform: translate(-100%, 0);
  opacity: 0;
  transition: all 500ms;
}
```

![img](https://cdn.nlark.com/yuque/0/2021/gif/1614731/1626875095495-e174c2fe-1c9c-4c5c-bcf2-29459a75fa70.gif)

### TransitionGroup

当我们有一组动画时，需要将这些 CSSTransition 放入到一个 TransitionGroup 中来完成动画：

App.js

第 23 行的 CSSTransition 必须加 key，而且 key 必须是 index ，

如果不加 key ， 造成 bug ：删除第一个时， diff 算法在新建所有元素后会认为删除的是最后一个，所以动画 class 会添加到最后一个

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1627108392204-5bb8291f-d269-44b1-8a0c-8700d0ce97d2.jpeg)

如果 key 是 item ，添加时，key 都是 Tom 与第一项的 Tom 造成重复 key，不会有添加效果

包含在 `<TransitionGroup></TransitionGroup>` 的元素都需要包裹在 `<CSSTransition></CSSTransition>` 中

App.js

```jsx
import React, { PureComponent } from 'react';
import { CSSTransition, TransitionGroup } from 'react-transition-group';

import './App.css';

export default class App extends PureComponent {

  constructor(props) {
    super(props);

    this.state = {
      names: ["Tom", "kobe", "lilei"]
    }
  }
  
  addName() {
    const newNames = [...this.state.names, "Tom"]
    this.setState({
      names: newNames
    })
  }

  removeItem(index) {
    // 为了变量名称不冲突，取名：index indey indez
    // indey 与 removeItem 传过来的 index 不相等的数组元素留下，相等的数组元素去除
    this.setState({
      names: this.state.names.filter((item, indey) => index !== indey)
    })
  }

  render() {
    return (
      <div>
        <TransitionGroup>
          {
            this.state.names.map((item, index) => {
              return (
                <CSSTransition key={index}
                  timeout={500}
                  classNames="item">
                  <div>
                    {item}
                    <button onClick={e => this.removeItem(index)}>-</button>
                  </div>
                </CSSTransition>
              )
            })
          }
        </TransitionGroup>
        <button onClick={e => this.addName()}>+name</button>
      </div>
    )
  }
}
```

App.css

```css
.item-enter {
  opacity: 0;
  transform: scale(.6);
}

.item-enter-active {
  opacity: 1;
  transform: scale(1);
  transition: opacity 300ms, transform 300ms;
}

/* 
   在 enter-done 加颜色有 bug，
   如果新加一条列表项前面是一条旧的列表项，删除前一条旧的列表项，新加的列表项 enter-done 样式    会被移除，所以一般不加 enter-done 
*/
.item-enter-done {
  color: red;
}

.item-exit {
  opacity: 1;
  transform: scale(1);
}

.item-exit-active {
  opacity: 0;
  transform: scale(.6);
  transition: opacity 300ms, transform 300ms;
}

.item-exit-done {
  opacity: 0;
}

```

![img](https://cdn.nlark.com/yuque/0/2021/gif/1614731/1626935446629-60ee56dc-20cc-428c-a75c-4d2525914083.gif)



## 高阶组件（HOC mixin 进化，组件复用，装饰器模式）

### 高阶组件介绍

**高阶组件（higher-order component）是参数为组件，返回值为新组件的函数。**

**高阶组件是将组件转换为另一个组件。**

**被包装组件 => 经过容器组件（即 HOC） => 新组件**

**就是函数，类似一个铅笔刨。**

使用高阶组件一般是为了抽离与业务无关的代码逻辑

定义解析：

- 首先， `高阶组件` 本身不是一个组件，而是一个函数；

- 其次，这个函数的参数是一个组件，返回值也是一个组件；

**HOC 不会修改传入的组件，也不会使用继承来复制其行为。**

**HOC 是纯函数，没有副作用。**

高阶函数的编写过程类似于这样：

```jsx
function higherOrderComponent(WrapperComponent) {
  return class NewComponent extends PureComponent {
    render() {
      return <WrapperComponent/>
    }
  }
}
```

在 ES6 中，类表达式中类名是可以省略的，所以也可以写成下面的写法：

```jsx
function higherOrderComponent(WrapperComponent) {
  return class extends PureComponent {
    render() {
      return <WrapperComponent/>
    }
  }
}
```

完整的代码，我们可以这样来编写：

```jsx
import React, { PureComponent } from 'react';

function higherOrderComponent(WrapperComponent) {
  return class NewComponent extends PureComponent {
    render() {
      return <WrapperComponent/>
    }
  }
}

class App extends PureComponent {
  render() {
    return (
      <div>
        App
      </div>
    )
  }
}

export default higherOrderComponent(App);
```

另外，组件的名称可以通过 displayName 来修改：

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1616416560166-1f5999c5-c279-4709-a8bb-354e0ac56b33.jpeg)

高阶组件并不是 React API 的一部分，它是基于 React 的组合特性而形成的设计模式；

**高阶组件在一些 React 第三方库中非常常见：**

**比如 redux 中的 connect ；**

**比如 react-router 中的 withRouter ；**

**官方示例**

渲染评论列表的组件

```jsx
class CommentList extends React.Component {
  constructor(props) {
    super(props);
    this.handleChange = this.handleChange.bind(this);
    this.state = {
      // 假设 "DataSource" 是个全局范围内的数据源变量
      comments: DataSource.getComments()
    };
  }

  componentDidMount() {
    // 订阅更改
    DataSource.addChangeListener(this.handleChange);
  }

  componentWillUnmount() {
    // 清除订阅
    DataSource.removeChangeListener(this.handleChange);
  }

  handleChange() {
    // 当数据源更新时，更新组件状态
    this.setState({
      comments: DataSource.getComments()
    });
  }

  render() {
    return (
      <div>
        {this.state.comments.map((comment) => (
          <Comment comment={comment} key={comment.id} />
        ))}
      </div>
    );
  }
}
```

订阅单个博客帖子的组件

```jsx
class BlogPost extends React.Component {
  constructor(props) {
    super(props);
    this.handleChange = this.handleChange.bind(this);
    this.state = {
      blogPost: DataSource.getBlogPost(props.id)
    };
  }

  componentDidMount() {
    DataSource.addChangeListener(this.handleChange);
  }

  componentWillUnmount() {
    DataSource.removeChangeListener(this.handleChange);
  }

  handleChange() {
    this.setState({
      blogPost: DataSource.getBlogPost(this.props.id)
    });
  }

  render() {
    return <TextBlock text={this.state.blogPost} />;
  }
}
```

CommentList 和 BlogPost 不同 - 它们在 DataSource 上调用不同的方法，且渲染不同的结果。

**但它们的大部分实现都是一样的：**

**在挂载时，向 DataSource 添加一个侦听器（handleChange）。**

**在侦听器（handleChange）内部，当数据源发生变化时，调用 setState。**

**在卸载时，删除侦听器（handleChange）。**

在一个大型应用程序中，这种订阅 `DataSource` 和调用 `setState` 的模式可能会一次又一次地发生。

**所以我们需要一个抽象，允许我们在一个地方定义这个逻辑，并在许多组件之间共享它。**

**我们可以编写一个创建组件的函数。**

该函数将接受一个子组件作为它的其中一个参数，该子组件将订阅数据作为 prop 。

```jsx
// 此函数接收一个组件
function withSubscription(WrappedComponent, selectData) {
  // 并返回另一个组件
  return class extends React.Component {
    constructor(props) {
      super(props);
      this.handleChange = this.handleChange.bind(this);
      // 无论传入的函数声明 selectData 有没有定义参数，selectData(DataSource, props) 都可以正常执行
      // 传入的函数声明没有定义参数，则调用时相当于 selectData(undefined, undefined)
      // 传入的函数声明只定义一个参数，则调用时相当于 selectData(DataSource)
      // 传入的函数声明定义两个参数，则调用时相当于 selectData(DataSource, props)
      this.state = {
        data: selectData(DataSource, props)
      };
    }

    componentDidMount() {
      // 负责订阅相关的操作
      DataSource.addChangeListener(this.handleChange);
    }

    componentWillUnmount() {
      DataSource.removeChangeListener(this.handleChange);
    }

    handleChange() {
      this.setState({
        data: selectData(DataSource, this.props)
      });
    }

    render() {
      // 使用新数据渲染被包装的组件！请注意，我们可能还会传递其他属性
      // 被包装组件 WrappedComponent 接收来自容器组件的所有 prop， 即 {...this.props}
      // 同时也接收一个新的用于 render 的 data prop，即 data={this.state.data}
      return <WrappedComponent data={this.state.data} {...this.props} />;
    }
  };
}
```

`withSubscription` 是一个普通函数，你可以根据需要对参数进行增添或者删除

`withSubscription` 和被包装组件之间的契约完全基于之间传递的 props

```jsx
const CommentListWithSubscription = withSubscription(
  CommentList,
  (DataSource) => DataSource.getComments()
);

const BlogPostWithSubscription = withSubscription(
  BlogPost,
  (DataSource, props) => DataSource.getBlogPost(props.id)
);
```

### 高阶组件的使用

#### props 的增强

假如我们有如下代码：

```jsx
class Header extends PureComponent {
  render() {
    const { name, age } = this.props;
    return <h2>Header {name + age}</h2>
  }
}

export default class App extends PureComponent {
  render() {
    return (
      <div>
        <Header name="aaa" age={18} />
      </div>
    )
  }
}
```

我们可以通过一个高阶组件可以在不破坏原有结构的情况下对某个组件增强 props ：

```jsx
function enhanceProps(WrapperCpn, otherProps) {
  return props => <WrapperCpn {...props} {...otherProps} />
}

const EnhanceHeader = enhanceProps(Header, {height: 1.88})
```

对象展开：

Header 组件 this.props => { name: "aaa", age: 18 } => {...props}

{height: 1.88} => {...otherProps}

#### 利用高阶组件来共享 Context

```jsx
import React, { PureComponent, createContext } from 'react';

const UserContext = createContext({
  nickname: "默认",
  level: -1
})

function Header(props) {
  return (
    <UserContext.Consumer>
      {
        value => {
          const { nickname, level } = value;
          return <h2>Header {"昵称:" + nickname + "等级" + level}</h2>
        }
      }
    </UserContext.Consumer>
  )
}

function Footer(props) {
  return (
    <UserContext.Consumer>
      {
        value => {
          const { nickname, level } = value;
          return <h2>Footer {"昵称:" + nickname + "等级" + level}</h2>
        }
      }
    </UserContext.Consumer>
  )
}

export default class App extends PureComponent {
  render() {
    return (
      <div>
        <UserContext.Provider value={{ nickname: "why", level: 90 }}>
          <Header />
          <Footer />
        </UserContext.Provider>
      </div>
    )
  }
}
```

```jsx
import React, { PureComponent, createContext } from 'react';

const UserContext = createContext({
  nickname: "默认",
  level: -1
})

function withUser(WrapperCpn) {
  return props => {
    return (
      <UserContext.Consumer>
        {
          value => {
            return <WrapperCpn {...props} {...value}/>
          }
        }
      </UserContext.Consumer>
    )
  }
}

function Header(props) {
  const { nickname, level } = props;
  return <h2>Header {"昵称:" + nickname + "等级:" + level}</h2>
}


function Footer(props) {
  const { nickname, level } = props;
  return <h2>Footer {"昵称:" + nickname + "等级:" + level}</h2>
}

// 处理成新组件
const UserHeader = withUser(Header);
const UserFooter = withUser(Footer);

export default class App extends PureComponent {
  render() {
    return (
      <div>
        <UserContext.Provider value={{ nickname: "why", level: 90 }}>
          <UserHeader />
          <UserFooter />
        </UserContext.Provider>
      </div>
    )
  }
}
```

#### 渲染判断鉴权

开发中，我们可能遇到这样的场景：

某些页面是必须用户登录成功才能进行进入；

如果用户没有登录成功，那么直接跳转到登录页面；

这个时候，我们就可以使用高阶组件来完成鉴权操作：

```jsx
import React, { PureComponent } from 'react';

function LoginPage() {
  return <h2>LoginPage</h2>
}

function CartPage() {
  return <h2>CartPage</h2>
}

// HOC
function loginAuth(Page) {
  return props => {
    if (props.isLogin) {
      return <Page/>
    } else {
      return <LoginPage/>
    }
  }
}

const AuthCartPage = loginAuth(CartPage);

export default class App extends PureComponent {
  render() {
    return (
      <div>
        <AuthCartPage isLogin={true}/>
      </div>
    )
  }
}
```

#### 生命周期劫持

```jsx
import React, { PureComponent } from 'react';

class Home extends PureComponent {
  render() {
    return (
      <div>
        <h2>Home</h2>
        <p>我是home的元素,哈哈哈</p>
      </div>
    )
  }
}

class Detail extends PureComponent {
  render() {
    return (
      <div>
        <h2>Detail</h2>
        <p>我是detail的元素,哈哈哈</p>
      </div>
    )
  }
}

// HOC
function logRenderTime(WrapperCpn) {
  return class extends PureComponent {
    UNSAFE_componentWillMount() {
      this.begin = Date.now();
    }

    componentDidMount() {
      this.end = Date.now();
      const interval = this.end - this.begin;
      console.log(`${WrapperCpn.name}渲染使用时间:${interval}`)
    }

    render() {
      return <WrapperCpn {...this.props}/>
    }
  }
}

const LogHome = logRenderTime(Home);
const LogDetail = logRenderTime(Detail);

export default class App extends PureComponent {
  render() {
    return (
      <div>
        <LogHome />
        <LogDetail />
      </div>
    )
  }
}
```

当然，HOC 也有自己的一些缺陷：

HOC 需要在原组件上进行包裹或者嵌套，如果大量使用 HOC ，将会产生非常多的嵌套，这让调试变得非常困难；

HOC 可以劫持 props ，在不遵守约定的情况下也可能造成冲突；

## Render Prop（作用域插槽，动态渲染组件，状态或方法复用，代理模式）

**Render Prop：**

**React 组件间使用值为函数的 prop 共享代码的技术，**

**将一个组件的 state 或 方法作为 prop 分享给其它组件（需要相同的 state 或 方法），**

**是用于告知其它组件需要渲染什么内容的函数 prop 。**

假设我们有一个 `<Cat>` 组件，可以让一张猫的图片追随鼠标位置。

我们需要使用 

```jsx
<Cat mouse={{ x, y }}
```

的 prop 来告诉 Cat 组件鼠标的坐标，

让它知道鼠标在屏幕哪个位置。

使用 Render Prop 前：

```jsx
class Cat extends React.Component {
  render() {
    const mouse = this.props.mouse;
    return (
      <img src="/cat.jpg" style={{ position: 'absolute', left: mouse.x, top: mouse.y }} />
    );
  }
}

class MouseWithCat extends React.Component {
  constructor(props) {
    super(props);
    this.handleMouseMove = this.handleMouseMove.bind(this);
    this.state = { x: 0, y: 0 };
  }

  handleMouseMove(event) {
    this.setState({
      x: event.clientX,
      y: event.clientY
    });
  }

  render() {
    return (
      <div style={{ height: '100vh' }} onMouseMove={this.handleMouseMove}>
        {/*
          其它像 <Cat> 这样的组件需要使用 MouseWithCat 的 state 时，
          都要在类似的 <MouseWithSomethingElse> 组件（本质上是另一个 <MouseWithCat> ）
          里硬编码 <SomethingElse>，
          每次都要创建一个类似的 <MouseWithSomethingElse> 组件，
          所以这种方式无法让 <MouseWithCat> 组件的 state 可复用
        */}
        <Cat mouse={this.state} />
      </div>
    );
  }
}

class MouseTracker extends React.Component {
  render() {
    return (
      <div>
        <h1>移动鼠标!</h1>
        <MouseWithCat />
      </div>
    );
  }
}
```

使用 Render Prop 后：

**我们可以写一个值为函数的 prop 的 `<Mouse>` 组件，**

**它能够动态决定什么需要渲染的，**

**而不是复制多个类似的  `<Mouse>` 组件，将 `<Cat>` 组件硬编码到某个 `<Mouse>` 组件里**

```jsx
class Cat extends React.Component {
  render() {
    const mouse = this.props.mouse;
    return (
      <img src="/cat.jpg" style={{ position: 'absolute', left: mouse.x, top: mouse.y }} />
    );
  }
}

class Mouse extends React.Component {
  constructor(props) {
    super(props);
    this.handleMouseMove = this.handleMouseMove.bind(this);
    this.state = { x: 0, y: 0 };
  }

  handleMouseMove(event) {
    this.setState({
      x: event.clientX,
      y: event.clientY
    });
  }

  render() {
    return (
      <div style={{ height: '100vh' }} onMouseMove={this.handleMouseMove}>
        {/*
          不是写 <Mouse> 需要呈现的静态内容，而是使用 render 这个 prop 属性来动态确定要渲染的内容
        */}
        {this.props.render(this.state)}
      </div>
    );
  }
}

class MouseTracker extends React.Component {
  render() {
    return (
      <div>
        <h1>移动鼠标!</h1>
        {/*
          Mouse 使用 this.props.render(this.state) 将 this.state 传给 mouse，最后传给 Cat 组件
        */}
        <Mouse render={mouse => (
          <Cat mouse={mouse} />
        )}/>
      </div>
    );
  }
}
```

### 使用 Render Prop 实现高阶组件（HOC）

例如，

如果你更喜欢使用 `withMouse` HOC而不是 `<Mouse>` 组件，

可以使用带有 render prop 的常规 `<Mouse>` 轻松创建一个：

```jsx
// 如果出于某种原因，你真的想要 HOC，
// 那么你可以使用 render prop 创建一个！
function withMouse(Component) {
  return class extends React.Component {
    render() {
      return (
        <Mouse render={mouse => (
          <Component {...this.props} mouse={mouse} />
        )}/>
      );
    }
  }
}
```

**不一定要用名为 `render` 的 prop 才叫 render prop，**

**任何用于告知组件需要渲染什么内容的函数 prop 在技术上都可以被称为 “render prop”。**

### Render Prop 使用注意

**将 Render Props 与 React.PureComponent 一起使用时要小心**

**如果你在 render 方法里创建函数，**

**那么使用 render prop 会让 `React.PureComponent`带来的优势荡然无存。**

在下面这个示例中，

继续使用我们之前的 `<Mouse>` 组件，

如果 `Mouse` 继承自 `React.PureComponent` ，而不是 `React.Component`

**每次 `<MouseTracker>` 渲染，它会生成一个新的函数作为 `<Mouse render> ` prop 的值**

**因为新旧 prop 浅比较的时候总是得到 false，**

**在这种情况下每一次 `render` 对于 render prop 都是生成一个新的值。**

```jsx
class Mouse extends React.PureComponent {
  // 与上面相同的代码......
}

class MouseTracker extends React.Component {
  render() {
    return (
      <div>
        <h1>Move the mouse around!</h1>

        {/*
          这是不好的！
          每个渲染的 render prop 的值是不同的。
        */}
        <Mouse render={mouse => (
          <Cat mouse={mouse} />
        )}/>
      </div>
    );
  }
```

为了解决这个问题，可以定义一个 prop 作为实例方法，像这样：

```jsx
class MouseTracker extends React.Component {
  // 定义为实例方法，
  // 当我们在渲染中使用 this.renderTheCat 时，this.renderTheCat 始终指的是相同的函数
  renderTheCat(mouse) {
    return <Cat mouse={mouse} />;
  }

  render() {
    return (
      <div>
        <h1>Move the mouse around!</h1>
        <Mouse render={this.renderTheCat} />
      </div>
    );
  }
}
```

## StrictMode

StrictMode 是一个用来突出显示应用程序中潜在问题的工具。

- 与 Fragment 一样，StrictMode 不会渲染任何可见的 UI；

- 它为其后代元素触发额外的检查和警告；

- 严格模式检查仅在开发模式下运行；它们不会影响生产构建；

可以为应用程序的任何部分启用严格模式，如下代码：

- 不对 Header 和 Footer 组件运行严格模式检查；

- 但是，ComponentOne 和 ComponentTwo 以及它们的所有后代元素都将进行检查；

```jsx
import React from 'react';

function ExampleApplication() {
  return (
    <div>
      <Header />
      <React.StrictMode>
        <div>
          <ComponentOne />
          <ComponentTwo />
        </div>
      </React.StrictMode>
      <Footer />
    </div>
  );
}
```

到底检测什么呢？

1.识别不安全的生命周期：

2.使用过时的 ref API

3.使用废弃的 findDOMNode 方法

4.检查意外的副作用

5.组件的 constructor 会被调用两次；

这是严格模式下故意进行的操作，查看在这里写的一些逻辑代码被调用多次时，是否会产生一些副作用；

在生产环境中，是不会被调用两次的；

6.检测过时的 context API

## 错误边界

**部分 UI 的 JavaScript 错误不应该导致整个应用崩溃，**

为了解决这个问题，React 16 引入了一个新的概念 —— 错误边界。

**错误边界是一种 React 组件，**

**这种组件可以捕获并打印发生在其子组件树任何位置的 JavaScript 错误，**

**并且，它会渲染出备用 UI，而不是渲染那些崩溃了的子组件树。**

**如果一个 class 组件中定义了 static getDerivedStateFromError() 或 componentDidCatch()** 

**这两个生命周期方法中的任意一个（或两个）时，那么它就变成一个错误边界。**

**当抛出错误后，**

**使用 static getDerivedStateFromError() 渲染备用 UI ，使用 componentDidCatch() 打印错误信息。**

如果一个错误边界无法渲染错误信息，则错误会冒泡至最近的上层错误边界，

即使一个错误发生在 componentDidUpdate 方法中，并且由某一个深层组件树的 setState 引起，其仍然能够冒泡到最近的错误边界。

**错误边界可以将其包装在最顶层的路由组件，也可以将单独的组件包装在错误边界以保护应用其他部分不崩溃。**

```jsx
class ErrorBoundary extends React.Component {
  constructor(props) {
    super(props);
    this.state = { hasError: false };
  }

  static getDerivedStateFromError(error) {
    // 1、更新 state 使下一次渲染能够显示降级后的 UI
    return { hasError: true };
  }

  componentDidCatch(error, errorInfo) {
    // 2、可以将错误日志上报给服务器
    logErrorToMyService(error, errorInfo);
  }

  render() {
    if (this.state.hasError) {
      // 3、可以自定义降级后的 UI 并渲染
      return <h1>Something went wrong.</h1>;
    }

    return this.props.children; 
  }
}
```

然后将它作为一个常规组件去使用：

```jsx
<ErrorBoundary>
  <MyWidget />
</ErrorBoundary>
```

错误边界的工作方式类似于 JavaScript 的 catch {}，

不同的地方在于错误边界只针对 React 组件。

**只有 class 组件才可以成为错误边界组件。**

**自 React 16 起，任何未被错误边界捕获的错误将会导致整个 React 组件树被卸载。**

注意

**错误边界无法捕获以下场景中产生的错误：**

- **事件处理，如果需要在事件处理器内部捕获错误，使用普通的 JavaScript try / catch 语句**
- **异步代码（例如 `setTimeout` 或 `requestAnimationFrame` 回调函数）**
- **服务端渲染**
- **它自身抛出来的错误（并非它的子组件）**

### 关于事件处理器

错误边界无法捕获事件处理器内部的错误。

React 不需要错误边界来捕获事件处理器中的错误。

与 render 方法和生命周期方法不同，事件处理器不会在渲染期间触发。

**如果需要在事件处理器内部捕获错误，使用普通的 JavaScript try / catch 语句：**

```jsx
class MyComponent extends React.Component {
  constructor(props) {
    super(props);
    this.state = { error: null };
    this.handleClick = this.handleClick.bind(this);
  }

  handleClick() {
    try {
      // 执行操作，如有错误则会抛出
    } catch (error) {
      this.setState({ error });
    }
  }

  render() {
    if (this.state.error) {
      return <h1>Caught an error.</h1>
    }
    return <button onClick={this.handleClick}>Click Me</button>
  }
}
```

## 代码分割

### 懒加载代码包（模块）

对应用进行代码分割能够“懒加载”当前用户所需要的内容，能够显著地提高应用性能。

**尽管并没有减少应用整体的代码体积，但可以避免加载用户永远不需要的代码，**

**并在初始加载的时候减少所需加载的代码量。**

**引入代码分割的最佳方式是通过动态 import() 语法。**

使用之前：

```js
import { add } from './math';

console.log(add(16, 26));
```

使用之后：

```js
import("./math").then(math => {
  console.log(math.add(16, 26));
});
```

当 Webpack 解析到该语法时，会自动进行代码分割。

如果使用 Create React App，该功能已开箱即用，可以立刻使用该特性。

Next.js 也已支持该特性而无需进行配置。

### 懒加载组件

**React.lazy 函数动态引入（的组件），在某个组件首次渲染时，自动导入包含该组件的包。**

React.lazy 接受一个函数，这个函数需要动态调用 import()。

它必须返回一个 Promise，该 Promise 需要 resolve 一个 defalut export 的 React 组件。

使用之前：

```js
import OtherComponent from './OtherComponent';
```

使用之后：

```jsx
const OtherComponent = React.lazy(() => import('./OtherComponent'));
```

然后应该在 Suspense 组件中渲染 lazy 组件，

使得我们可以使用在等待加载 lazy 组件时做优雅降级（如 loading 指示器等）。

**Suspense 使得组件可以“等待”某些操作结束后，再进行渲染。**

**目前，Suspense 仅支持的使用场景是：通过 React.lazy 动态加载组件。**

**可以在等待加载 lazy 组件时展示 loading 元素，不至于直接空白，提升用户体验。**

```jsx
import React, { Suspense } from 'react';

const OtherComponent = React.lazy(() => import('./OtherComponent'));

function MyComponent() {
  return (
    <div>
      <Suspense fallback={<div>Loading...</div>}>
        <OtherComponent />
      </Suspense>
    </div>
  );
}
```

**fallback 属性接受任何在组件加载过程中想展示的 React 元素（即可以是 HTML 元素，也可以是 React 组件）。**

**可以将 Suspense 组件置于懒加载组件之上的任何位置。**

**甚至可以用一个 Suspense 组件包裹多个懒加载组件。**

```jsx
import React, { Suspense } from 'react';

const OtherComponent = React.lazy(() => import('./OtherComponent'));
const AnotherComponent = React.lazy(() => import('./AnotherComponent'));

function MyComponent() {
  return (
    <div>
      <Suspense fallback={<div>Loading...</div>}>
        <section>
          <OtherComponent />
          <AnotherComponent />
        </section>
      </Suspense>
    </div>
  );
}
```

### 异常捕获边界（Error boundaries）

如果模块加载失败（如网络问题），它会触发一个错误。

可以通过异常捕获边界（Error boundaries）技术来处理这些情况，以显示良好的用户体验并管理恢复事宜。

```jsx
import React, { Suspense } from 'react';
import MyErrorBoundary from './MyErrorBoundary';

const OtherComponent = React.lazy(() => import('./OtherComponent'));
const AnotherComponent = React.lazy(() => import('./AnotherComponent'));

const MyComponent = () => (
  <div>
    <MyErrorBoundary>
      <Suspense fallback={<div>Loading...</div>}>
        <section>
          <OtherComponent />
          <AnotherComponent />
        </section>
      </Suspense>
    </MyErrorBoundary>
  </div>
);
```

### 基于路由的代码分割

在应用中可以使用 React.lazy 和 React Router 这类的第三方库，来配置基于路由的代码分割。

```jsx
import React, { Suspense, lazy } from 'react';
import { BrowserRouter as Router, Route, Switch } from 'react-router-dom';

const Home = lazy(() => import('./routes/Home'));
const About = lazy(() => import('./routes/About'));

const App = () => (
  <Router>
    <Suspense fallback={<div>Loading...</div>}>
      <Switch>
        <Route exact path="/" component={Home}/>
        <Route path="/about" component={About}/>
      </Switch>
    </Suspense>
  </Router>
);
```

### 命名导出（Named Exports）

**React.lazy 目前只支持默认导出（default exports）,也就是默认导出之后根据路径导入。**

如果想被引入的模块使用命名导出（named exports），

可以创建多一个中间模块（下例的 MyComponent.js），重新导出为默认模块。

这能保证 tree shaking 不会出错，并且不必引入不需要的组件。

```js
// ManyComponents.js 中间模块
export const MyComponent = /* ... */;
export const MyUnusedComponent = /* ... */;

// MyComponent.js    命名导出引入的模块
export { MyComponent as default } from "./ManyComponents.js";

// MyApp.js
import React, { lazy } from 'react';
const MyComponent = lazy(() => import("./MyComponent.js"));
```

## React 中的 CSS 方案

React 官方并没有给出在 React 中统一的样式风格：

由此，从普通的 css，到 css modules，再到 css in js，有几十种不同的解决方案，上百个不同的库；

大家一致在寻找最好的或者说最适合自己的 CSS 方案，但是到目前为止也没有统一的方案。

**四种解决方案：**

方案一：内联样式的写法；

方案二：普通的 css 写法；

方案三：css modules；

方案四：css in js（styled-components）；

### 方案一  内联样式

内联样式是官方推荐的一种 css 样式的写法：

style 接受一个采用小驼峰命名属性的 JavaScript 对象，而不是 CSS 字符串；

并且可以引用 state 中的状态来设置相关的样式；

```jsx
export default class App extends PureComponent {
  constructor(props) {
    super(props);

    this.state = {
      titleColor: "red"
    }
  }

  render() {
    return (
      <div>
        <h2 style={{color: this.state.titleColor, fontSize: "20px"}}>我是App标题</h2>
        <p style={{color: "green", textDecoration: "underline"}}>我是一段文字描述</p>
      </div>
    )
  }
}
```

**内联样式的优点：**


1.内联样式, 样式之间不会有冲突

2.可以动态获取当前 state 中的状态

**内联样式的缺点：**


1.写法上都需要使用驼峰标识


2.某些样式没有提示


3.大量的样式, 代码混乱


4.某些样式无法编写(比如伪类/伪元素)


所以官方依然是希望内联样式和普通的css来结合编写；

### 方案二 普通的 css

App.js 中编写 React 逻辑代码：

```jsx
import React, { PureComponent } from 'react';

import Home from './Home';

import './App.css';

export default class App extends PureComponent {
  render() {
    return (
      <div className="app">
        <h2 className="title">我是App的标题</h2>
        <p className="desc">我是App中的一段文字描述</p>
        <Home/>
      </div>
    )
  }
}
```

App.css 中编写 React 样式代码：

```css
.title {
  color: red;
  font-size: 20px;
}

.desc {
  color: green;
  text-decoration: underline;
}
```

这样的编写方式和普通的网页开发中编写方式是一致的：

如果我们按照普通的网页标准去编写，那么也不会有太大的问题；

但是组件化开发中，

我们总是希望组件是一个独立的模块，即便是样式也只是在自己内部生效，不会相互影响；

但是普通的 css 都属于全局的 css，样式之间会相互影响；

比如编写 Home.js 的逻辑代码：

```jsx
import React, { PureComponent } from 'react';

import './Home.css';

export default class Home extends PureComponent {
  render() {
    return (
      <div className="home">
        <h2 className="title">我是Home标题</h2>
        <span className="desc">我是Home中的span段落</span>
      </div>
    )
  }
}
```

又编写了 Home.css 的样式代码：

```css
.title {
  color: orange;
}

.desc {
  color: purple;
}
```

由于是全局 css ，最终样式之间会相互层叠，只有一个样式会生效。

虽然可以添加多一个独特的 class 或者 id，但这样写选择器不仅很麻烦，某些情况还需要考虑权重问题。

### 方案三  css modules

**css modules 并不是 React 特有的解决方案，**

**而是所有使用了类似于 webpack 配置的环境下都可以使用的。**

css modules 利用 webpack 等构建工具使 class 作用域为局部，

但是，如果在其他项目中使用，那么我们需要自己来进行配置，

例如  webpack 配置 options modules: true。

```js
module.exports = {
  module: {
    rules: [
      {
        test: /\.css$/,
        loader: 'css-loader',
        options: {
          modules: true,
        },
      },
    ],
  },
};
```

modules 更具体的配置项参考：https://www.npmjs.com/package/css-loader

loader 会用唯一的标识符 (identifier) 来替换局部选择器。

所选择的唯一标识符以模块形式暴露出去。

**示例：**

webpack css-loader options

```js
options: {
  ...,
  modules: {
    mode: 'local',
    // 样式名规则配置
    localIdentName: '[name]__[local]--[hash:base64:5]',
  },
},
...
```

**React 的脚手架已经内置了 css modules 的配置：**

- **.css/.less/.scss 等样式文件都修改成 .module.css/.module.less/.module.scss 等；**

- **之后就可以引用并且进行使用了；**

使用的方式如下：

![img](https://cdn.nlark.com/yuque/0/2021/png/1614731/1630921050675-cee36c56-2d85-486c-ab1a-4a3c875f3d59.png)

这种 css 使用方式最终生成的 class 名称会全局唯一：

![img](https://cdn.nlark.com/yuque/0/2021/png/1614731/1630921038668-5835e6d0-803c-463d-ae5d-24fbb53d31f7.png)

**css modules 确实解决了局部作用域的问题，也是很多人喜欢在 React 中使用的一种方案。**

**但是这种方案也有自己的缺陷：**

- **引用的类名，不能使用连接符(.home-title)，在 JavaScript 中是不识别的；**

- **所有的 className 都必须使用 {style.className}  的形式来编写；**

- **不方便动态来修改某些样式，依然需要使用内联样式的方式；**

如果你觉得上面的缺陷还算 OK，那么在开发中完全可以选择使用 css modules 来编写，

并且也是在 React 中很受欢迎的一种方式。

### 方案四 CSS in JS

“CSS-in-JS” 是指一种模式， CSS 由 JavaScript 生成而不是在外部文件中定义；

在传统的前端开发中，我们通常会将结构（HTML）、样式（CSS）、逻辑（JavaScript）进行分离。


但是 React 的思想中认为逻辑本身和 UI 是无法分离的，所以才会有了 JSX 的语法。


样式呢？样式也是属于 UI 的一部分；

事实上 CSS-in-JS 的模式就是一种将样式（CSS）也写入到 JavaScript 中的方式，

并且可以方便地使用JavaScript 的状态；


所以 React 又被人称之为 All in JS，当然，这种开发的方式也受到了很多的批评；

批评声音虽然有，但是在我们看来很多优秀的CSS-in-JS的库依然非常强大、方便：

**CSS-in-JS 通过 JavaScript 来为 CSS 赋予一些能力：**

**包括类似 CSS 预处理器的样式嵌套、函数定义、逻辑复用，以及动态修改状态；**

**虽然 CSS 预处理器也具备某些能力，但是获取动态状态依然是一个不好处理的点；**

所以，目前可以说 CSS-in-JS 是 React 编写 CSS 最为受欢迎的一种解决方案；

安装 styled-components：

```shell
yarn add styled-components
```

VSCode 推荐插件： vscode-styled-components

**模板字符串与标签模板字符串**

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1630935190454-aea3c774-13f9-4130-ba6d-12c7157decbe.jpeg)

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1630934763554-cae75354-4a0d-45d0-a4c4-9cee695517d1.jpeg)

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1630935190422-89edc094-93da-40d0-a47a-a8b59ee5cf85.jpeg)

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1630934763475-72d43202-57f3-4710-b91a-1283f2bbbb36.jpeg)

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1630935190445-4bd63f00-d7d8-4cf5-9a21-b661aab58297.jpeg)

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1630934763467-92b2c7b2-6694-4a3b-9492-84bbb63bbfe2.jpeg)

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1630934763591-b7794c2d-f391-4cb4-a826-43d0025b40be.jpeg)

**基本使用**

**styled-components 是一个函数的执行，返回一个 React 组件。**

**里面会使用正则表达式对模板字符串进行解析，从数组取出插值，**

**例如插值为一个函数，它会将函数取出来并执行。**

```
const Button = styled.button({
  color: 'grey',
});
```

相当于

```
const Button = styled.button`
  color: grey;
`;
```

个人习惯：

一般在最外层都会包裹一个 Wrapper  组件，

最外层 Wrapper 里面嵌套选择器来选中子元素，

很少嵌套多个 Wrapper 来选中子元素（除非有冲突）

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1630935431309-ced6b31d-dd87-4273-8925-a7ea2b947bc6.jpeg)

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1630935536734-6ff4c929-f007-4bc8-b1da-8540063d71ef.jpeg)

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1630935857966-eecb1c6b-ddd1-465c-aa75-ba405ce06187.jpeg)

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1630935857936-04263794-65f5-4be0-9c48-2b08c9d80f34.jpeg)

**styled.div 表示在子组件外层默认包裹一个 `<div>`，**

**这个 `<div>` 会添加两个 class ：以 sc 为前缀 的 class + 随机唯一 的class ，再也没有想样式名的烦恼**

**模板字符串里可以写该 div 及子元素（使用选择器在里面嵌套编写即可）的样式，**

**与预处理器一样，使用 & 表示外层嵌套的父元素，并且支持伪类和伪元素**

**动态样式**

```jsx
import React, { PureComponent } from 'react';
import styled from 'styled-components';

/**
 * 特点:
 *  1.props 穿透：特性跟原生一样添加到组件里，比如 type="password" 
 *  2.attrs 添加特性：
 *    attrs() 本身是一个函数，允许我们为一个 StyledComponent 添加默认属性和默认样式值
 *    要求传入一个对象或接收 props 返回一个对象的函数，
 *    返回值为一个新的函数，所以后面可以跟 `` 
 *    在参数（对象）里添加特性，
 *    在 `` 添加 CSS 声明
 *    获取 props 需要通过 ${} 传入一个插值函数，props 作为该函数的参数；
 *  3.传入 state 作为 props 属性
 *    this.state.color 会传入组件作为 props，通过 color: ${props => props.color}; 应用
 */
// 在对象里写 border-color 会报错， 在对象里写 borderColor 则无法应用，所以需要 props
const HYInput = styled.input.attrs({
  placeholder: "Tom",
  bColor: "red"
})`
  background-color: lightblue;
  border-color: ${props => props.bColor};
  color: ${props => props.color};
`

export default class Profile extends PureComponent {
  constructor(props) {
    super(props);

    this.state = {
      color: "purple"
    }
  }

  render() {
    return (
      <div>
        <input type="password"/>
        <HYInput type="password" color={this.state.color}/>
        <h2>我是Profile的标题</h2>
        <ul>
          <li>设置列表1</li>
          <li>设置列表2</li>
          <li>设置列表3</li>
        </ul>
      </div>
    )
  }
}
```

**继承样式**

```js
const HYButton = styled.button`
  padding: 8px 30px;
  border-radius: 5px;
`

const HYWarnButton = styled(HYButton)`
  background-color: red;
  color: #fff;
`

const HYPrimaryButton = styled(HYButton)`
  background-color: green;
  color: #fff;
`
```

使用按钮

```jsx
<HYButton>我是普通按钮</HYButton>
<HYWarnButton>我是警告按钮</HYWarnButton>
<HYPrimaryButton>我是主要按钮</HYPrimaryButton>
```

styled 设置主题

在全局定制自己的主题，通过 Provider 进行共享：

```jsx
import { ThemeProvider } from 'styled-components';

<ThemeProvider theme={{color: "red", fontSize: "30px"}}>
  <Home />
  <Profile />
</ThemeProvider>
```

在 styled 组件中可以获取到主题的内容：

```js
const ProfileWrapper = styled.div`
  color: ${props => props.theme.color};
  font-size: ${props => props.theme.fontSize};
`
```

### 方案五  classnames

很明显，这是一个用于动态添加 classnames 的一个库。

常见的使用案例：

```jsx
classNames('foo', 'bar'); // => 'foo bar'
classNames('foo', { bar: true }); // => 'foo bar'
classNames({ 'foo-bar': true }); // => 'foo-bar'
classNames({ 'foo-bar': false }); // => ''
classNames({ foo: true }, { bar: true }); // => 'foo bar'
classNames({ foo: true, bar: true }); // => 'foo bar'

// lots of arguments of various types
classNames('foo', { bar: true, duck: false }, 'baz', { quux: true }); // => 'foo bar baz quux'

// other falsy values are just ignored
classNames(null, false, 'bar', undefined, 0, 1, { baz: null }, ''); // => 'bar 1'
```







