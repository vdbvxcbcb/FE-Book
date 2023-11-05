

## 命令式和声明式

从范式的角度来看，我们的框架应该设计成命令式的还是声明式的呢？这两种范式有何优缺点？我们能否汲取两者的优点？

 jQuery 就是典型的命令式框架。命令式框架的一大特点就是关注过程。

```
01 - 获取 id 为 app 的 div 标签
02 - 它的文本内容为 hello world
03 - 为其绑定点击事件
04 - 当点击时弹出提示：ok
```

```js
$('#app') // 获取 div
  .text('hello world') // 设置文本内容
  .on('click', () => { alert('ok') }) // 绑定点击事件
```

原生 JavaScript 来实现：

```js
const div = document.querySelector('#app') // 获取 div
div.innerText = 'hello world' // 设置文本内容
div.addEventListener('click', () => { alert('ok') }) // 绑定点击事件
```

自然语言描述能够与代码产生一一对应的关系，代码本身描述的是“做事的过程”。

声明式框架更加关注结果。

结合 Vue.js，如何实现上面自然语言描述的功能

```VUE
<div @click="() => alert('ok')">hello world</div>
```

我们提供的是一个“结果”，至于如何实现这个“结果”，我们并不关心，

这就像我们在告诉 Vue.js：“嘿，Vue.js，看到没，我要的就是一个div，文本内容是 hello world，它有个事件绑定，你帮我搞定吧。

”至于实现该“结果”的过程，则是由 Vue.js 帮我们完成的。

换句话说，Vue.js 帮我们封装了过程。

因此，我们能够猜到 Vue.js 的内部实现一定是命令式的，而暴露给用户的却更加声明式

**声明式代码的性能不优于命令式代码的性能。**

要将 div 标签的文本内容修改为 hello vue3，那么如何用命令式代码实现呢？

```
div.textContent = 'hello vue3' // 直接修改
```

理论上命令式代码可以做到极致的性能优化，因为我们明确知道哪些发生了变更，只做必要的修改就行了。

但是声明式代码不一定能做到这一点。

```vue
<!-- 之前： -->
<div @click="() => alert('ok')">hello world</div>
<!-- 之后： -->
<div @click="() => alert('ok')">hello vue3</div>
```

对于框架来说，为了实现最优的更新性能，它需要找到前后的差异并只更新变化的地方，但是最终完成这次更新的代码仍然是：

```js
div.textContent = 'hello vue3' // 直接修改
```

如果我们把直接修改的性能消耗定义为 A，把找出差异的性能消耗定义为 B，那么：

命令式代码的更新性能消耗 = A

声明式代码的更新性能消耗 = B + A

声明式代码会比命令式代码多出找出差异的性能消耗，

因此最理想的情况是，当找出差异的性能消耗为 0 时，声明式代码与命令式代码的性能相同，但是无法做到超越，

毕竟框架本身就是封装了命令式代码才实现了面向用户的声明式。

这符合前文中给出的性能结论：**声明式代码的性能不优于命令式代码的性能。**

**那么为什么 Vue.js 要选择声明式的设计方案呢？**

**原因就在于声明式代码的可维护性更强。**

在采用命令式代码开发的时候，我们需要维护实现目标的整个过程，包括要手动完成 DOM 元素的创建、更新、删除等工作。

而声明式代码展示的就是我们要的结果，看上去更加直观，至于做事儿的过程，并不需要我们关心，Vue.js 都为我们封装好了。

这就体现了我们在框架设计上要做出的关于可维护性与性能之间的权衡。

在采用声明式提升可维护性的同时，性能就会有一定的损失，而框架设计者要做的就是：**在保持可维护性的同时让性能损失最小化**。

## 虚拟 DOM 的性能

声明式代码的更新性能消耗 = 找出差异的性能消耗+ 直接修改的性能消耗，

因此，如果我们能够最小化找出差异的性能消耗，就可以让声明式代码的性能无限接近命令式代码的性能。

而所谓的虚拟 DOM，就是为了最小化找出差异这一步的性能消耗而出现的。

采用虚拟 DOM 的更新技术的性能理论上不可能比原生 JavaScript 操作 DOM 更高。

这里我们强调了理论上三个字，因为这很关键，为什么呢？

因为在大部分情况下，我们很难写出绝对优化的命令式代码，尤其是当应用程序的规模很大的时候，

即使你写出了极致优化的代码，也一定耗费了巨大的精力，这时的投入产出比其实并不高。

虚拟 DOM要解决的问题就是让我们不用付出太多的努力（写声明式代码），

还能够保证应用程序的性能下限，让应用程序的性能不至于太差。

虚拟 DOM 创建页面的过程分为两步：

第一步是创建 JavaScript 对象，这个对象可以理解为真实 DOM 的描述；

第二步是递归地遍历虚拟 DOM 树并创建真实 DOM。

我们同样可以用一个公式来表达：创建 JavaScript 对象的计算量 + 创建真实 DOM 的计算量。

![Snipaste_2023-06-07_12-14-54](https://cdn.nlark.com/yuque/0/2023/png/1614731/1686111321506-f8090c87-9f8f-40ce-a281-2c6a733607b1.png)

虚拟DOM 相比 innerHTML ，无论是纯 JavaScript 层面的计算，还是 DOM 层面的计算，其实两者差距不大。这里我们从宏观的角度只看数量级上的差异。

使用 innerHTML 更新页面的过程是重新构建 HTML 字符串，再重新设置 DOM 元素的 innerHTML 属性，

这其实是在说，哪怕我们只更改了一个文字，也要重新设置 innerHTML 属性。

而重新设置 innerHTML 属性就等价于销毁所有旧的 DOM 元素，再全量创建新的 DOM 元素。

虚拟 DOM 是如何更新页面的。它需要重新创建 JavaScript 对象（虚拟 DOM 树），然后比较新旧虚拟 DOM，找到变化的元素并更新它。

![Snipaste_2023-06-07_12-19-22](https://cdn.nlark.com/yuque/0/2023/png/1614731/1686111568903-4a2a5659-ebb6-42ca-8466-de2bce4d8dab.png)

在更新页面时，虚拟 DOM 在 JavaScript 层面的运算要比创建页面时多出一个 Diff 的性能消耗，

然而它毕竟也是 JavaScript 层面的运算，所以不会产生数量级的差异。

再观察 DOM 层面的运算，可以发现虚拟 DOM 在更新页面时只会更新必要的元素，但 innerHTML需要全量更新。这时虚拟 DOM 的优势就体现出来了。  

对于虚拟 DOM 来说，无论页面多大，都只会更新变化的内容，而对于 innerHTML 来说，页面越大，就意味着更新时的性能消耗越大。

如果加上性能因素，那么最终它们在更新页面时的性能如图 

![Snipaste_2023-06-07_12-20-50](https://cdn.nlark.com/yuque/0/2023/png/1614731/1686111657164-637c13be-b4e5-4df9-91ab-25cba588e31e.png)

innerHTML、虚拟 DOM 以及原生 JavaScript 在更新页面时的性能

![Snipaste_2023-06-07_12-21-21](https://cdn.nlark.com/yuque/0/2023/png/1614731/1686111689158-5fbddb0b-5c76-40b0-ba36-25ca9817ec81.png)

原生 DOM操作方法的心智负担最大，因为你要手动创建、删除、修改大量的DOM 元素。

但它的性能是最高的，不过为了使其性能最佳，我们同样要承受巨大的心智负担。另外，以这种方式编写的代码，可维护性也极差。

而对于 innerHTML 来说，由于我们编写页面的过程有一部分是通过拼接 HTML 字符串来实现的，

这有点儿接近声明式的意思，但是拼接字符串总归也是有一定心智负担的，而且对于事件绑定之类的事情，我们还是要使用原生 JavaScript 来处理。

如果 innerHTML 模板很大，则其更新页面的性能最差，尤其是在只有少量更新时。

虚拟 DOM，它是声明式的，因此心智负担小，可维护性强，性能虽然比不上极致优化的原生 JavaScript，但是在保证心智负担和可维护性的前提下相当不错

## 运行时和编译时

框架要设计成纯运行时的还是纯编译时的，甚至是运行时+编译时的呢？它们之间又有何差异？优缺点分别是什么？  

假设我们设计了一个框架，它提供一个 Render 函数，

用户可以为该函数提供一个树型结构的数据对象，然后 Render 函数会根据该对象递归地将数据渲染成 DOM 元素。

```js
const obj = {
  tag: 'div',
  children: [
    { tag: 'span', children: 'hello world' }
  ]
}
```

每个对象都有两个属性：tag 代表标签名称，children 既可以是一个数组（代表子节点），也可以直接是一段文本（代表文本子节点）。

```html
<body></body>

<script>
function Render(obj, root) {
  // 1、 第一次创建 div
  const el = document.createElement(obj.tag)
  if (typeof obj.children === 'string') {
    const text = document.createTextNode(obj.children)
    el.appendChild(text)
  } else if (obj.children) {
    // 数组，递归调用 Render，使用 el 作为 root 参数，2、 div 作为 el ，child 为 { tag: 'span', children: 'hello world' }
    obj.children.forEach((child) => Render(child, el))
  }

  // 将元素添加到 root，3、 div 插入 span ,
  // 4、递归完成退出，div 插入了 span ，最后 body 插入 div ，递归从下到上从里到外
  root.appendChild(el)
}

const obj = {
  tag: 'div',
  children: [
    { tag: 'span', children: 'hello world' }
  ]
}
// 渲染到 body 下
Render(obj, document.body)
</script>
```

用户在使用它渲染内容时，直接为 Render 函数提供了一个树型结构的数据对象。

实际上，我们刚刚编写的框架就是一个纯运行时的框架。

但是有一天，你的用户抱怨说：“手写树型结构的数据对象太麻烦了，而且不直观，能不能支持用类似于 HTML 标签的方式描述树型结构的数据对象呢？”

你看了看现在的 Render 函数，然后回答：“抱歉，暂不支持。”

为了满足用户的需求，你开始思考，能不能引入编译的手段，把 HTML 标签编译成树型结构的数据对象，这样不就可以继续使用 Render 函数了吗？

![Snipaste_2023-06-07_12-56-55](https://cdn.nlark.com/yuque/0/2023/png/1614731/1686113820690-9da7ae2d-7e19-4a24-bacc-dd91b0d19025.png)

你编写了一个叫作 Compiler 的程序，它的作用就是把 HTML 字符串编译成树型结构的数据对象，于是交付给用户去用了。

那么用户该怎么用呢？最简单的方式就是让用户分别调用 Compiler 函数和 Render 函数：

```js
const html = `
  <div>
    <span>hello world</span>
  </div>
 `
// 调用 Compiler 编译得到树型结构的数据对象
const obj = Compiler(html)
// 再调用 Render 进行渲染
Render(obj, document.body)
```

这时我们的框架就变成了一个运行时 + 编译时的框架。

它既支持运行时，用户可以直接提供数据对象从而无须编译；又支持编译时，用户可以提供 HTML 字符串，我们将其编译为数据对象后再交给运行时处理。

准确地说，上面的代码其实是运行时编译，意思是代码运行的时候才开始编译，而这会产生一定的性能开销，

因此我们也可以在构建的时候就执行 Compiler 程序将用户提供的内容编译好，等到运行时就无须编译了，这对性能是非常友好的。

既然编译器可以把HTML 字符串编译成数据对象，那么能不能直接编译成命令式代码？

![Snipaste_2023-06-07_13-00-49](https://cdn.nlark.com/yuque/0/2023/png/1614731/1686114056217-85de1157-609d-4ec4-bf4c-a1398b5152a7.png)

这样我们只需要一个 Compiler 函数就可以了，连 Render 都不需要了。

其实这就变成了一个纯编译时的框架，因为我们不支持任何运行时内容，用户的代码通过编译器编译后才能运行。

一个框架既可以是纯运行时的，也可以是纯编译时的，还可以是既支持运行时又支持编译时的。

纯运行时的框架，由于它没有编译的过程，因此我们没办法分析用户提供的内容，

但是如果加入编译步骤，可能就大不一样了，我们可以分析用户提供的内容，看看哪些内容未来可能会改变，哪些内容永远不会改变，

这样我们就可以在编译的时候提取这些信息，然后将其传递给 Render 函数，Render 函数得到这些信息之后，就可以做进一步的优化了。

纯编译时的框架，那么它也可以分析用户提供的内容。

由于不需要任何运行时，而是直接编译成可执行的 JavaScript 代码，因此性能可能会更好，但是这种做法有损灵活性，即用户提供的内容必须编译后才能用。

Svelte 就是纯编译时的框架，但是它的真实性能可能达不到理论高度。

Vue.js 3 仍然保持了运行时 + 编译时的架构，在保持灵活性的基础上能够尽可能地去优化。

## 描述 UI

除了使用模板来声明式地描述 UI 之外，我们还可以用JavaScript 对象来描述

无论是手写虚拟 DOM（渲染函数）还是使用模板，都属于声明式地描述 UI

```js
const title = {
  // 标签名称
  tag: 'h1',
  // 标签属性
  props: {
    onClick: handler
  },
  // 子节点
  children: [
    { tag: 'span' }
  ]
}
```

对应到 Vue.js 模板，其实就是：

```html
 <h1 @click="handler"><span></span></h1>
```

使用模板和 JavaScript 对象描述 UI 有何不同呢？答案是：使用 JavaScript 对象描述 UI 更加灵活。

举个例子，假如我们要表示一个标题，根据标题级别的不同，会分别采用 h1~h6 这几个标签，

如果用 JavaScript 对象来描述，我们只需要使用一个变量来代表 h 标签即可：

```js
// h 标签的级别
let level = 3
const title = { 
  tag: `h${level}`, // h3 标签 
}
```

可以看到，当变量 level 值改变，对应的标签名字也会在 h1 和h6 之间变化。但是如果使用模板来描述，就不得不穷举

```html
<h1 v-if="level === 1"></h1>
<h2 v-else-if="level === 2"></h2>
<h3 v-else-if="level === 3"></h3>
<h4 v-else-if="level === 4"></h4>
<h5 v-else-if="level === 5"></h5>
<h6 v-else-if="level === 6"></h6>
```

这远没有 JavaScript 对象灵活。而使用 JavaScript 对象来描述 UI的方式，其实就是所谓的虚拟 DOM。

Vue.js 3除了支持使用模板描述 UI 外，还支持使用虚拟 DOM 描述 UI。

```js
import { h } from 'vue'

export default {
  render() {
    return h('h1', { onClick: handler }) // 虚拟 DOM
  }
}
```

 h 函数的返回值就是一个对象，其作用是让我们编写虚拟DOM 变得更加轻松。

如果把上面 h 函数调用的代码改成 JavaScript 对象，就需要写更多内容

```js
export default {
  render() {
    return {
      tag: 'h1',
      props: { onClick: handler }
    }
  }
}
```

如果还有子节点，那么需要编写的内容就更多了，所以 h 函数就是一个辅助创建虚拟 DOM 的工具函数，仅此而已。

什么是组件的渲染函数？

一个组件要渲染的内容是通过渲染函数来描述的，也就是上面代码中的 render 函数，

Vue.js 会根据组件的 render 函数的返回值拿到虚拟 DOM，然后就可以把组件的内容渲染出来了

## 初识渲染器

虚拟 DOM，它其实就是用 JavaScript对象来描述真实的 DOM 结构。

**虚拟 DOM 需要使用渲染器变成真实 DOM 并渲染到浏览器页面**

![Snipaste_2023-06-07_16-03-39](https://cdn.nlark.com/yuque/0/2023/png/1614731/1686125060012-b11b50c9-1e4e-4c59-a93f-c81804ff9d0b.png)

```js
const vnode = {
  tag: 'div',
  props: {
    onClick: () => alert('hello')
  },
  children: 'click me'
}
```

tag 用来描述标签名称，所以 tag: 'div' 描述的就是一个 `<div>` 标签。

props 是一个对象，用来描述 `<div>` 标签的属性、事件等内容。

可以看到，我们希望给 div 绑定一个点击事件。children 用来描述标签的子节点。

在上面的代码中，children是一个字符串值，意思是 div 标签有一个文本子节点： `<div>`click me`</div>`

你完全可以自己设计虚拟 DOM 的结构，例如可以使用tagName 代替 tag，因为它本身就是一个 JavaScript 对象，并没有特殊含义

编写一个渲染器：

```html
<body></body>

<script>
function renderer(vnode, container) {
  // 使用 vnode.tag 作为标签名称创建 DOM 元素
  const el = document.createElement(vnode.tag)
  // 遍历 vnode.props 将属性、事件添加到 DOM 元素
  for (const key in vnode.props) {
    if (/^on/.test(key)) {
      // 如果 key 以 on 开头，那么说明它是事件
      el.addEventListener(
        key.substr(2).toLowerCase(), // 事件名称 onClick ---> click ，得到合法的事件名称
        vnode.props[key] // 事件处理函数
      )
    }
  }

  // 处理 children
  if (typeof vnode.children === 'string') {
    // 如果 children 是字符串，说明是元素的文本子节点
    // 使用 createTextNode 函数创建一个文本节点，并将其添加到新创建的元素内
    el.appendChild(document.createTextNode(vnode.children))
  } else if (Array.isArray(vnode.children)) {
    // 如果 children 是一个数组，递归地调用 renderer 函数渲染子节点，使用当前元素 el 作为挂载点
    vnode.children.forEach(child => renderer(child, el))
  }

  // 将元素添加到挂载点下
  container.appendChild(el)
}

const vnode = {
  tag: 'div',
  props: {
    onClick: () => alert('hello')
  },
  children: 'click me'
}

renderer(vnode, document.body)
</script>
```

组件的本质是什么？一句话总结：组件就是一组 DOM 元素的封装，这组 DOM 元素就是组件要渲染的内容

可以定义一个函数来代表组件，而函数的返回值就代表组件要渲染的内容：

```js
const MyComponent = function () {
  return {
    tag: 'div',
    props: {
      onClick: () => alert('hello')
    },
    children: 'click me'
  }
}
```

可以定义用虚拟 DOM 来描述组件。

我们可以让虚拟 DOM 对象中的 tag 属性来存储组件函数

```html
<body></body>

<script>
function renderer(vnode, container) {
  // 如果 vnode.tag 的类型是字符串
  if (typeof vnode.tag === 'string') {
    // 说明 vnode 描述的是标签元素
    mountElement(vnode, container)
  } else if (typeof vnode.tag === 'function') {
    // 如果 vnode.tag 的类型是函数，则说明它描述的是组件  
    // 说明 vnode 描述的是组件
    mountComponent(vnode, container)
  }
}

function mountElement(vnode, container) {
  // 使用 vnode.tag 作为标签名称创建 DOM 元素
	const el = document.createElement(vnode.tag)
  // 遍历 vnode.props 将属性、事件添加到 DOM 元素
  for (const key in vnode.props) {
    if (/^on/.test(key)) {
      // 如果 key 以 on 开头，那么说明它是事件
      el.addEventListener(
        key.substr(2).toLowerCase(), // 事件名称 onClick ---> click
        vnode.props[key] // 事件处理函数
      )
    }
  }

  // 处理 children
  if (typeof vnode.children === 'string') {
    // 如果 children 是字符串，说明是元素的文本子节点
    el.appendChild(document.createTextNode(vnode.children))
  } else if (Array.isArray(vnode.children)) {
    // 递归地调用 renderer 函数渲染子节点，使用当前元素 el 作为挂载点
    vnode.children.forEach(child => renderer(child, el))
  }

  // 将元素添加到挂载点下
  container.appendChild(el)
}

function mountComponent(vnode, container) {
  // 调用组件函数，获取组件要渲染的内容（虚拟 DOM）
  const subtree = vnode.tag()
  // 递归调用 renderer 渲染 subtree
  renderer(subtree, container)
}

const MyComponent = function () {
  return {
    tag: 'div',
    props: {
      onClick: () => alert('hello')
    },
    children: 'click me'
  }
}

const vnode = {
  tag: MyComponent
}

renderer(vnode, document.body)
</script>
```

组件一定得是函数吗？当然不是，我们完全可以使用一个 JavaScript 对象来表达组件，

```html
<body></body>

<script>
function renderer(vnode, container) {
  if (typeof vnode.tag === 'string') {
    // 说明 vnode 描述的是标签元素
    mountElement(vnode, container)
  } else if (typeof vnode.tag === 'object') {
    // 将 typeof vnode.tag === 'function' 修改为 typeof vnode.tag === 'object'
    // 说明 vnode 描述的是组件
    mountComponent(vnode, container)
  }
}

function mountElement(vnode, container) {
  // 使用 vnode.tag 作为标签名称创建 DOM 元素
	const el = document.createElement(vnode.tag)
  // 遍历 vnode.props 将属性、事件添加到 DOM 元素
  for (const key in vnode.props) {
    if (/^on/.test(key)) {
      // 如果 key 以 on 开头，那么说明它是事件
      el.addEventListener(
        key.substr(2).toLowerCase(), // 事件名称 onClick ---> click
        vnode.props[key] // 事件处理函数
      )
    }
  }

  // 处理 children
  if (typeof vnode.children === 'string') {
    // 如果 children 是字符串，说明是元素的文本子节点
    el.appendChild(document.createTextNode(vnode.children))
  } else if (Array.isArray(vnode.children)) {
    // 递归地调用 renderer 函数渲染子节点，使用当前元素 el 作为挂载点
    vnode.children.forEach(child => renderer(child, el))
  }

  // 将元素添加到挂载点下
  container.appendChild(el)
}

function mountComponent(vnode, container) {
  // vnode.tag 是组件对象，调用它的 render 函数得到组件要渲染的内容（虚拟 DOM）
  const subtree = vnode.tag.render()
  // 递归调用 renderer 渲染 subtree
  renderer(subtree, container)
}

// 这里我们使用一个对象来代表组件，该对象有一个函数，叫作 render ，其返回值代表组件要渲染的内容。
const MyComponent = {
  render() {
    return {
      tag: 'div',
      props: {
        onClick: () => alert('hello')
      },
      children: 'click me'
    }
  }
}

const vnode = {
  tag: MyComponent
}

renderer(vnode, document.body)
</script>
```

其实 Vue.js 中的有状态组件就是使用对象结构来表达的

## 模板的工作原理

渲染器讲解了虚拟 DOM 是如何渲染成真实 DOM 的，那么模板是如何工作的呢？

这就要提到 Vue.js 框架中的另外一个重要组成部分：编译器。

**编译器的作用其实就是将模板编译为渲染函数**

```html
<div @click="handler">
  click me
</div>
```

对于编译器来说，模板就是一个普通的字符串，它会分析该字符串并生成一个功能与之相同的渲染函数：

```js
render() {
  return h('div', { onClick: handler }, 'click me')
}
```

一个 .vue 文件就是一个组件，

```vue
<template>
  <div @click="handler">
    click me
 </div>
</template>

<script>
export default {
  data() { 
    /* ... */
  },
  methods: {
    handler: () => {/* ... */}
  }
}
</script>
```

**其中 `<template>` 标签里的内容就是模板内容，编译器会把模板内容编译成渲染函数并添加到 `<script>` 标签块的组件对象上**

```js
export default {
  data() {/* ... */},
  methods: {
    handler: () => {/* ... */}
  },
  render() {
    return h('div', { onClick: handler }, 'click me')
  }
}
```

**总结：模板 => 编译器 => 渲染函数 虚拟 DOM  => 渲染器 Diff => 真实 DOM**

无论是使用模板还是直接手写渲染函数，

**对于一个组件来说，它要渲染的内容最终都是通过渲染函数产生的，然后渲染器再把渲染函数返回的虚拟 DOM 渲染为真实 DOM，**

这就是模板的工作原理，也是 Vue.js 渲染页面的流程

**组件其实就是一组虚拟 DOM 元素的封装，它可以是一个返回虚拟 DOM 的函数，也可以是一个对象，**

**但这个对象下必须要有一个函数用来产出组件要渲染的虚拟 DOM。**

**渲染器在渲染组件时，会先获取组件要渲染的内容，即执行组件的渲染函数并得到其返回值，我们称之为 subtree，**

**最后再递归地调用渲染器将 subtree 渲染出来即可。**

**模板会被一个叫作编译器的程序编译为渲染函数。**

**渲染器的作用是，把虚拟 DOM 对象渲染为真实 DOM 元素。**

**它的工作原理是，递归地遍历虚拟 DOM 对象，并调用原生 DOM API 来完成真实 DOM 的创建。**

**渲染器的精髓在于后续的更新，它会通过 Diff 算法找出变更点，并且只会更新需要更新的内容。**

## 响应式数据的基本实现

修改了字段 obj.text 的值，当值变化后，副作用函数 effect 自动重新执行，如果能实现这个目标，那么对象 obj 就是响应式数据。

当读取操作发生时，将副作用函数收集到“桶”中；

当设置操作发生时，从“桶”中取出副作用函数并执行。

![Snipaste_2023-06-12_11-33-35](https://cdn.nlark.com/yuque/0/2023/png/1614731/1686540842699-fa8b9209-7483-4ee9-8c35-d9b1b754b069.png)

![Snipaste_2023-06-12_11-33-45](https://cdn.nlark.com/yuque/0/2023/png/1614731/1686540847911-0974aefb-f4b8-486c-96d8-64f80f08f599.png)

```html
<body></body>
<script>
// 存储副作用函数的桶
const bucket = new Set()

// 原始数据
const data = { text: 'hello world' }
// 对原始数据的代理
const obj = new Proxy(data, {
  // 拦截读取操作
  get(target, key) {
    // 将副作用函数 activeEffect 添加到存储副作用函数的桶中
    if (activeEffect) {
       bucket.add(activeEffect)     
    }
    // 返回属性值
    return target[key]
  },
  // 拦截设置操作
  set(target, key, newVal) {
    // 设置属性值
    target[key] = newVal
    // 把副作用函数从桶里取出并执行
    bucket.forEach(fn => fn())
  }
})


// 用一个全局变量存储当前激活的 effect 函数
let activeEffect
function effect(fn) {
  // 当调用 effect 注册副作用函数时，将副作用函数复制给 activeEffect
  activeEffect = fn
  // 执行副作用函数
  fn()
}

effect(
  // 匿名副作用函数
  () => {
    console.log('effect run') // 会打印 2 次
    document.body.innerText = obj.text
  }
)

setTimeout(() => {
  // 副作用函数中并没有读取 notExist 属性的值，但是会执行 2 次 匿名副作用函数
  // 为了解决这个问题，我们需要重新设计“桶”的数据结构。
  obj.notExist = 'hello vue3'
}, 1000)

</script>
```

我们使用一个 Set 数据结构作为存储副作用函数的“桶”。

导致该问题的根本原因是，我们没有在副作用函数与被操作的目标字段之间建立明确的联系。

例如当读取属性时，无论读取的是哪一个属性，其实都一样，都会把副作用函数收集到“桶”里；

当设置属性时，无论设置的是哪一个属性，也都会把“桶”里的副作用函数取出并执行。

副作用函数与被操作的字段之间没有明确的联系。

只需要在副作用函数与被操作的字段之间建立联系即可，这就需要我们重新设计“桶”的数据结构，而不能简单地使用一个Set 类型的数据作为“桶”了。

```html
<body></body>
<script>
// 存储副作用函数的桶
const bucket = new WeakMap()

// 原始数据
const data = { text: 'hello world' }
// 对原始数据的代理
const obj = new Proxy(data, {
  // 拦截读取操作
  get(target, key) {
    // 根据 target 从“桶”中取得 depsMap，它也是一个 Map 类型：key --> effects ，将副作用函数 activeEffect 添加到存储副作用函数的桶中
    let depsMap = bucket.get(target)
    // 如果不存在 depsMap，那么新建一个 Map 并与 target 关联
    if (!depsMap) {
      bucket.set(target, (depsMap = new Map()))
    }
    // 再根据 key 从 depsMap 中取得 deps，它是一个 Set 类型，里面存储着所有与当前 key 相关联的副作用函数：effects
    let deps = depsMap.get(key)
    // 如果 deps 不存在，同样新建一个 Set 并与 key 关联
    if (!deps) {
      depsMap.set(key, (deps = new Set()))
    }
    // 最后将当前激活的副作用函数添加到“桶”里
    deps.add(activeEffect)

    // 返回属性值
    return target[key]
  },
  // 拦截设置操作
  set(target, key, newVal) {
    // 设置属性值
    target[key] = newVal
    // 根据 target 从桶中取得 depsMap，它是 key --> effects ，把副作用函数从桶里取出并执行
    const depsMap = bucket.get(target)
    if (!depsMap) return
    // 根据 key 取得所有副作用函数 effects
    const effects = depsMap.get(key)
    // 执行副作用函数
    effects && effects.forEach(fn => fn())
  }
})


// 用一个全局变量存储当前激活的 effect 函数
let activeEffect
function effect(fn) {
  // 当调用 effect 注册副作用函数时，将副作用函数复制给 activeEffect
  activeEffect = fn
  // 执行副作用函数
  fn()
}

effect(() => {
  console.log('effect run')
  document.body.innerText = obj.text
})

setTimeout(() => {
  obj.text2 = 'hello vue3'
}, 1000)

</script>
```

我们分别使用了 WeakMap、Map 和 Set：

WeakMap 由 target --> Map 构成；

Map 由 key --> Set 构成。

WeakMap 的键是原始对象 target，WeakMap 的值是一个 Map 实例，

Map 的键是原始对象 target 的 key，Map 的值是一个由副作用函数组成的 Set。

WeakMap、Map 和 Set 之间的关系

![Snipaste_2023-06-12_11-57-20](https://cdn.nlark.com/yuque/0/2023/png/1614731/1686542246063-9db578f9-0ba6-4fd9-8397-72c8ed859514.png)

Set 数据结构所存储的副作用函数集合称为 key 的依赖集合。

```js
const map = new Map();
const weakmap = new WeakMap();

(function(){
  const foo = {foo: 1};
  const bar = {bar: 2};

  map.set(foo, 1);
  weakmap.set(bar, 2);
})()
```

定义了一个立即执行的函数表达式（IIFE），

在函数表达式内部定义了两个对象：foo 和 bar，这两个对象分别作为 map 和 weakmap 的 key。

当该函数表达式执行完毕后，对于对象 foo 来说，它仍然作为 map 的 key 被引用着，因此垃圾回收器（grabage collector）不会把它从内存中移除，

我们仍然可以通过 map.keys 打印出对象 foo。

然而对于对象 bar 来说，由于 WeakMap 的 key 是弱引用，它不影响垃圾回收器的工作，

所以一旦表达式执行完毕，垃圾回收器就会把对象 bar 从内存中移除，并且我们无法获取 weakmap 的 key 值，也就无法通过 weakmap 取得对象 bar。

WeakMap 对 key 是弱引用，不影响垃圾回收器的工作。据这个特性可知，一旦 key 被垃圾回收器回收，那么对应的键和值就访问不到了。

所以 WeakMap 经常用于存储那些只有当 key 所引用的对象存在时（没有被回收）才有价值的信息，

例如上面的场景中，如果 target 对象没有任何引用了，说明用户侧不再需要它了，这时垃圾回收器会完成回收任务。

但如果使用 Map 来代替 WeakMap，那么即使用户侧的代码对 target 没有任何引用，这个 target 也不会被回收，最终可能导致内存溢出

如下代码，同理

```js
const obj1 = {}
const obj2 = {}

const map1 = new WeakMap()
map1.set(obj1, 1)

const map2 = new Map()
map2.set(obj2, 2)

obj1 = null
obj2 = null

map1.get()
```

最后，我们对上文中的代码做一些封装处理。

在目前的实现中，

当读取属性值时，我们直接在 get 拦截函数里编写把副作用函数收集到“桶”里的这部分逻辑，但更好的做法是将这部分逻辑单独封装到一个 track 函数中，

函数的名字叫 track 是为了表达追踪的含义。

同样，我们也可以把触发副作用函数重新执行的逻辑封装到 trigger 函数中：

```html
<body></body>
<script>
// 存储副作用函数的桶
const bucket = new WeakMap()

// 原始数据
const data = { text: 'hello world' }
// 对原始数据的代理
const obj = new Proxy(data, {
  // 拦截读取操作
  get(target, key) {
    // 将副作用函数 activeEffect 添加到存储副作用函数的桶中
    track(target, key)
    // 返回属性值
    return target[key]
  },
  // 拦截设置操作
  set(target, key, newVal) {
    // 设置属性值
    target[key] = newVal
    // 把副作用函数从桶里取出并执行
    trigger(target, key)
  }
})

// 追踪和收集依赖
function track(target, key) {
  // 根据 target 从“桶”中取得 depsMap，它也是一个 Map 类型：key --> effects ，将副作用函数 activeEffect 添加到存储副作用函数的桶中
  let depsMap = bucket.get(target)
  // 如果不存在 depsMap，那么新建一个 Map 并与 target 关联
  if (!depsMap) {
    bucket.set(target, (depsMap = new Map()))
  }
  // 再根据 key 从 depsMap 中取得 deps，它是一个 Set 类型，里面存储着所有与当前 key 相关联的副作用函数：effects
  let deps = depsMap.get(key)
  // 如果 deps 不存在，同样新建一个 Set 并与 key 关联
  if (!deps) {
    depsMap.set(key, (deps = new Set()))
  }
  // 最后将当前激活的副作用函数添加到“桶”里    
  deps.add(activeEffect)
}

// 触发副作用函数重新执行
function trigger(target, key) {
  // 根据 target 从桶中取得 depsMap，它是 key --> effects ，把副作用函数从桶里取出并执行
  const depsMap = bucket.get(target)
  if (!depsMap) return
  // 根据 key 取得所有副作用函数 effects    
  const effects = depsMap.get(key)
  effects && effects.forEach(fn => fn())
}

// 用一个全局变量存储当前激活的 effect 函数
let activeEffect
function effect(fn) {
  // 当调用 effect 注册副作用函数时，将副作用函数复制给 activeEffect
  activeEffect = fn
  // 执行副作用函数
  fn()
}

effect(() => {
  console.log('effect run')
  document.body.innerText = obj.text
})

setTimeout(() => {
  trigger(data, 'text')
}, 1000)

</script>
```

## 分支切换与 cleanup

```js
const data = { ok: true, text: 'hello world' }
const obj = new Proxy(data, { /* ... */ })

effect(function effectFn() {
  document.body.innerText = obj.ok ? obj.text : 'not'
})
```

在 effectFn 函数内部存在一个三元表达式，根据字段 obj.ok值的不同会执行不同的代码分支。

当字段 obj.ok 的值发生变化时，代码执行的分支会跟着变化，这就是所谓的分支切换。

分支切换可能会产生遗留的副作用函数。

拿上面这段代码来说，字段 obj.ok 的初始值为 true，这时会读取字段 obj.text 的值，

所以当 effectFn 函数执行时会触发字段 obj.ok 和字段 obj.text这两个属性的读取操作，此时副作用函数 effectFn 与响应式数据之间建立的联系如下：  

```
data
  └── ok
    └── effectFn
  └── text
    └── effectFn
```

![Snipaste_2023-06-12_12-14-51](https://cdn.nlark.com/yuque/0/2023/png/1614731/1686543298833-ea1e9af5-f50d-4e72-9436-35abd70e8d77.png)

副作用函数 effectFn 分别被字段 data.ok 和字段 data.text 所对应的依赖集合收集。

当字段 obj.ok 的值修改为 false，并触发副作用函数重新执行后，由于此时字段 obj.text 不会被读取，只会触发字段 obj.ok 的读取操作，

**所以理想情况下副作用函数 effectFn 不应该被字段 obj.text 所对应的依赖集合收集。**

**按照前文的实现，我们还做不到这一点。也就是说，当我们把字段 obj.ok 的值修改为 false，并触发副作用函数重新执行之后，**

**整个依赖关系仍然保持如上图描述的那样，这时就产生了遗留的副作用函数。**

理想情况：

![Snipaste_2023-06-12_12-16-16](https://cdn.nlark.com/yuque/0/2023/png/1614731/1686543381274-d4cf8f97-39da-4ba5-9121-8457212b03bc.png)

**解决这个问题的思路很简单，每次副作用函数执行时，我们可以先把它从所有与之关联的依赖集合中删除**

**当副作用函数执行完毕后，会重新建立联系，但在新的联系中不会包含遗留的副作用函数，如上图所描述的那样**  

![Snipaste_2023-06-12_12-19-23](https://cdn.nlark.com/yuque/0/2023/png/1614731/1686543568684-dc62cee4-3a21-47c9-ab78-5c02b9152265.png)

要将一个副作用函数从所有与之关联的依赖集合中移除，就需要明确知道哪些依赖集合中包含它，因此我们需要重新设计副作用函数

```html
<body></body>
<script>
// 存储副作用函数的桶
const bucket = new WeakMap()

// 原始数据
const data = { ok: true, text: 'hello world' }
// 对原始数据的代理
const obj = new Proxy(data, {
  // 拦截读取操作
  get(target, key) {
    // 将副作用函数 activeEffect 添加到存储副作用函数的桶中
    track(target, key)
    // 返回属性值
    return target[key]
  },
  // 拦截设置操作
  set(target, key, newVal) {
    // 设置属性值
    target[key] = newVal
    // 把副作用函数从桶里取出并执行
    trigger(target, key)
  }
})

// 追踪和收集依赖
function track(target, key) {
  // 根据 target 从“桶”中取得 depsMap，它也是一个 Map 类型：key --> effects ，将副作用函数 activeEffect 添加到存储副作用函数的桶中
  let depsMap = bucket.get(target)
  // 如果不存在 depsMap，那么新建一个 Map 并与 target 关联
  if (!depsMap) {
    bucket.set(target, (depsMap = new Map()))
  }
  // 再根据 key 从 depsMap 中取得 deps，它是一个 Set 类型，里面存储着所有与当前 key 相关联的副作用函数：effects
  let deps = depsMap.get(key)
  // 如果 deps 不存在，同样新建一个 Set 并与 key 关联
  if (!deps) {
    depsMap.set(key, (deps = new Set()))
  }
  // 最后将当前激活的副作用函数添加到“桶”里  
  deps.add(activeEffect)
  // deps 就是一个与当前副作用函数存在联系的依赖集合
  // 将其添加到 activeEffect.deps 数组中
  activeEffect.deps.push(deps) // 新增
}
    
// 触发副作用函数重新执行
function trigger(target, key) {
  // 根据 target 从桶中取得 depsMap，它是 key --> effects ，把副作用函数从桶里取出并执行
  const depsMap = bucket.get(target)
  if (!depsMap) return
  // 根据 key 取得所有副作用函数 effects  
  const effects = depsMap.get(key)
  // 避免无限循环
  const effectsToRun = new Set()
  effects && effects.forEach(effectFn => effectsToRun.add(effectFn))
  // 执行副作用函数  
  effectsToRun.forEach(effectFn => effectFn())
  // effects && effects.forEach(effectFn => effectFn())
}

// 用一个全局变量存储当前激活的 effect 函数
let activeEffect
function effect(fn) {
  const effectFn = () => {
    // 调用 cleanup 函数完成清除工作  
    cleanup(effectFn) // 新增
    // 当调用 effect 注册副作用函数时，将副作用函数复制给 activeEffect
    activeEffect = effectFn
    fn()
  }
  // activeEffect.deps 用来存储所有与该副作用函数相关联的依赖集合
  // effectFn.deps 数组中的依赖集合在 track 函数中收集
  effectFn.deps = []
  // 执行副作用函数
  effectFn()
}

function cleanup(effectFn) {
  // 遍历 effectFn.deps 数组
  for (let i = 0; i < effectFn.deps.length; i++) {
    // deps 是依赖集合
    const deps = effectFn.deps[i]
    // 将 effectFn 从依赖集合中移除
    deps.delete(effectFn)
  }
  // 最后需要重置 effectFn.deps 数组
  effectFn.deps.length = 0
}

effect(() => {
  console.log('effect run')
  document.body.innerText = obj.ok ? obj.text : 'not'
})

setTimeout(() => {
  obj.ok = false
  setTimeout(() => {
    obj.text = 'hello vue3'
  }, 1000)
}, 1000)
</script>
```

在 track 函数中我们将当前执行的副作用函数 activeEffect 添加到依赖集合 deps 中，这说明 deps 就是一个与当前副作用函数存在联系的依赖集合，

于是我们也把它添加到 activeEffect.deps 数组中，这样就完成了对依赖集合的收集。下图描述了这一步所建立的关系。

![Snipaste_2023-06-12_12-27-33](https://cdn.nlark.com/yuque/0/2023/png/1614731/1686544083044-89cc60a2-2386-44d8-a470-0b89247d460c.png)

有了这个联系后，我们就可以在每次副作用函数执行时，根据 effectFn.deps 获取所有相关联的依赖集合，进而将副作用函数从依赖集合中移除

cleanup 函数接收副作用函数作为参数，遍历副作用函数的 effectFn.deps 数组，

该数组的每一项都是一个依赖集合，然后将该副作用函数从依赖集合中移除，最后重置 effectFn.deps 数组。

**至此，我们的响应系统已经可以避免副作用函数产生遗留了。**

**但如果你尝试运行代码，会发现目前的实现会导致无限循环执行，问题出在 trigger 函数中：**

```js
function trigger(target, key) {
  const depsMap = bucket.get(target)
  if (!depsMap) return
  const effects = depsMap.get(key)
  effects && effects.forEach(fn => fn()) // 问题出在这句代码
}
```

**trigger 函数内部，我们遍历 effects 集合，它是一个 Set 集合，里面存储着副作用函数。当副作用函数执行时，会调用 cleanup 进行清除，**

**实际上就是从 effects 集合中将当前执行的副作用函数剔除，但是副作用函数的执行会导致其重新被收集到集合中，**

**而此时对于 effects 集合的遍历仍在进行。**

这个行为可以用如下简短的代码来表达：

```js
const set = new Set([1])

set.forEach(item => {
  set.delete(1)
  set.add(1)
  console.log('遍历中')
})
```

在上面这段代码中，我们创建了一个集合 set，它里面有一个元素数字 1，接着我们调用 forEach 遍历该集合。

在遍历过程中，首先调用 delete(1) 删除数字 1，紧接着调用 add(1) 将数字 1 加回，最后打印 '遍历中'。

如果我们在浏览器中执行这段代码，就会发现它会无限执行下去。

解决办法很简单，我们可以构造另外一个 Set 集合并遍历它：

```js
const set = new Set([1])

const newSet = new Set(set)
newSet.forEach(item => {
  set.delete(1)
  set.add(1)
  console.log('遍历中')
})
```

回到 trigger 函数，我们需要同样的手段来避免无限执行：  

```js
function trigger(target, key) {
  const depsMap = bucket.get(target)
  if (!depsMap) return
  const effects = depsMap.get(key)

  const effectsToRun = new Set(effects) // 新增
  effectsToRun.forEach(effectFn => effectFn()) // 新增
  // effects && effects.forEach(effectFn => effectFn()) // 删除
}
```

## 嵌套的 effect 与 effect 栈  

当组件发生嵌套时，例如 Foo 组件渲染了 Bar 组件：

```js
// Bar 组件
const Bar = {
  render() { /* ... */ },
}
// Foo 组件渲染了 Bar 组件
const Foo = {
  render() {
    return <Bar /> // jsx 语法
  }
}
```

此时就发生了 effect 嵌套，它相当于：

```js
effect(() => {
  Foo.render()
  // 嵌套
  effect(() => {
    Bar.render()
  })
})
```

这个例子说明了为什么 effect 要设计成可嵌套的。

我们所实现的响应系统并不支持 effect 嵌套，可以用下面的代码来测试一下：

```js
// 原始数据
const data = { foo: true, bar: true }
// 代理对象
const obj = new Proxy(data, { /* ... */ })

// 全局变量
let temp1, temp2

// effectFn1 嵌套了 effectFn2
effect(function effectFn1() {
  console.log('effectFn1 执行')

  effect(function effectFn2() {
    console.log('effectFn2 执行')
    // 在 effectFn2 中读取 obj.bar 属性
    temp2 = obj.bar
  })
  // 在 effectFn1 中读取 obj.foo 属性
  temp1 = obj.foo
})
```

上面这段代码中，effectFn1 内部嵌套了 effectFn2，很明显，effectFn1 的执行会导致 effectFn2 的执行。

需要注意的是，我们在 effectFn2 中读取了字段 obj.bar，在 effectFn1 中读取了字段 obj.foo，并且 effectFn2 的执行先于对字段 obj.foo 的读取操作。

在理想情况下，我们希望副作用函数与对象属性之间的联系如下：

```
data
  └── foo
    └── effectFn1
  └── bar
    └── effectFn2
```

在这种情况下，我们希望当修改 obj.foo 时会触发 effectFn1执行。由于 effectFn2 嵌套在 effectFn1 里，所以会间接触发effectFn2 执行，

而当修改 obj.bar 时，只会触发 effectFn2 执行。

但结果不是这样的，我们尝试修改 obj.foo 的值，会发现输出为

```
'effectFn1 执行'
'effectFn2 执行'
'effectFn2 执行
```

一共打印三次，前两次分别是副作用函数 effectFn1 与effectFn2 初始执行的打印结果，到这一步是正常的，问题出在第三行打印。

我们修改了字段 obj.foo 的值，发现 effectFn1 并没有重新执行，反而使得 effectFn2 重新执行了，这显然不符合预期。

问题出在哪里呢？其实就出在我们实现的 effect 函数与activeEffect 上。观察下面这段代码：

```js
// 用一个全局变量存储当前激活的 effect 函数
let activeEffect
function effect(fn) {
  const effectFn = () => {
    cleanup(effectFn)
    // 当调用 effect 注册副作用函数时，将副作用函数赋值给 activeEffect
    activeEffect = effectFn
    fn()
  }
  // activeEffect.deps 用来存储所有与该副作用函数相关的依赖集合
  effectFn.deps = []
  // 执行副作用函数
  effectFn()
}
```

我们用全局变量 activeEffect 来存储通过 effect 函数注册的副作用函数，这意味着同一时刻 activeEffect 所存储的副作用函数只能有一个。

当副作用函数发生嵌套时，内层副作用函数的执行会覆盖 activeEffect 的值，并且永远不会恢复到原来的值。

这时如果再有响应式数据进行依赖收集，即使这个响应式数据是在外层副作用函数中读取的，它们收集到的副作用函数也都会是内层副作用函数，

这就是问题所在。

为了解决这个问题，

我们需要一个副作用函数栈 effectStack，在副作用函数执行时，将当前副作用函数压入栈中，待副作用函数执行完毕后将其从栈中弹出，

并始终让 activeEffect 指向栈顶的副作用函数。

这样就能做到一个响应式数据只会收集直接读取其值的副作用函数，而不会出现互相影响的情况

```js
// 用一个全局变量存储当前激活的 effect 函数
let activeEffect
// effect 栈
const effectStack = []

function effect(fn) {
  const effectFn = () => {
    cleanup(effectFn)
    // 当调用 effect 注册副作用函数时，将副作用函数复制给 activeEffect
    activeEffect = effectFn
    // 在调用副作用函数之前将当前副作用函数压栈
    effectStack.push(effectFn)
    fn()
    // 在当前副作用函数执行完毕后，将当前副作用函数弹出栈，并还原 activeEffect 为之前的值
    effectStack.pop()
    activeEffect = effectStack[effectStack.length - 1]
  }
  // activeEffect.deps 用来存储所有与该副作用函数相关的依赖集合
  effectFn.deps = []
  // 执行副作用函数
  effectFn()
}
```

我们定义了 effectStack 数组，用它来模拟栈，activeEffect 没有变化，它仍然指向当前正在执行的副作用函数。

不同的是，当前执行的副作用函数会被压入栈顶，这样当副作用函数发生嵌套时，栈底存储的就是外层副作用函数，而栈顶存储的则是内层副作用函数

![Snipaste_2023-06-12_22-29-25](https://cdn.nlark.com/yuque/0/2023/png/1614731/1686580213597-51f6ceeb-cba3-4abb-ba9e-d68f3ba3130f.png)

当内层副作用函数 effectFn2 执行完毕后，它会被弹出栈，并将副作用函数 effectFn1 设置为 activeEffect

![Snipaste_2023-06-12_22-29-55](https://cdn.nlark.com/yuque/0/2023/png/1614731/1686580213546-b72b0146-a6f0-4521-bd04-14d878ab5c37.png)

如此一来，响应式数据就只会收集直接读取其值的副作用函数作为依赖，从而避免发生错乱  

```html
<body></body>
<script>
// 存储副作用函数的桶
const bucket = new WeakMap()

// 原始数据
const data = { foo: true, bar: true }
// 对原始数据的代理
const obj = new Proxy(data, {
  // 拦截读取操作
  get(target, key) {
    // 将副作用函数 activeEffect 添加到存储副作用函数的桶中
    track(target, key)
    // 返回属性值
    return target[key]
  },
  // 拦截设置操作
  set(target, key, newVal) {
    // 设置属性值
    target[key] = newVal
    // 把副作用函数从桶里取出并执行
    trigger(target, key)
  }
})

function track(target, key) {
  let depsMap = bucket.get(target)
  if (!depsMap) {
    bucket.set(target, (depsMap = new Map()))
  }
  let deps = depsMap.get(key)
  if (!deps) {
    depsMap.set(key, (deps = new Set()))
  }
  deps.add(activeEffect)
  activeEffect.deps.push(deps)
}

function trigger(target, key) {
  const depsMap = bucket.get(target)
  if (!depsMap) return
  const effects = depsMap.get(key)

  const effectsToRun = new Set()
  effects && effects.forEach(effectFn => effectsToRun.add(effectFn))
  effectsToRun.forEach(effectFn => effectFn())
  // effects && effects.forEach(effectFn => effectFn())
}

// 用一个全局变量存储当前激活的 effect 函数
let activeEffect
// effect 栈
const effectStack = []

function effect(fn) {
  const effectFn = () => {
    cleanup(effectFn)
    // 当调用 effect 注册副作用函数时，将副作用函数复制给 activeEffect
    activeEffect = effectFn
    // 在调用副作用函数之前将当前副作用函数压栈
    effectStack.push(effectFn)
    fn()
    // 在当前副作用函数执行完毕后，将当前副作用函数弹出栈，并还原 activeEffect 为之前的值
    effectStack.pop()
    activeEffect = effectStack[effectStack.length - 1]
  }
  // activeEffect.deps 用来存储所有与该副作用函数相关的依赖集合
  effectFn.deps = []
  // 执行副作用函数
  effectFn()
}

function cleanup(effectFn) {
  for (let i = 0; i < effectFn.deps.length; i++) {
    const deps = effectFn.deps[i]
    deps.delete(effectFn)
  }
  effectFn.deps.length = 0
}

// =========================
let temp1, temp2

effect(function effectFn1() {
  console.log('effectFn1 执行')
  effect(function effectFn2() {
    console.log('effectFn2 执行')
    temp2 = obj.bar
  })
  temp1 = obj.foo
})

setTimeout(() => {
  obj.foo = false
}, 1000);
</script>
```

## 避免无限递归循环

举个例子：

```js
const data = { foo: 1 }
const obj = new Proxy(data, { /*...*/ })

effect(() => obj.foo++)
```

可以看到，在 effect 注册的副作用函数内有一个自增操作obj.foo++，该操作会引起栈溢出：

```
Uncaught RangeError: Maximum call stack size exceeded
```

为什么会这样呢？

我们可以把 obj.foo++ 这个自增操作分开来看，它相当于：

```js
effect(() => {
  // 语句
  obj.foo = obj.foo + 1
})
```

在这个语句中，既会读取 obj.foo 的值，又会设置 obj.foo 的值，而这就是导致问题的根本原因。

我们可以尝试推理一下代码的执行流程：

首先读取 obj.foo 的值，这会触发 track 操作，将当前副作用函数收集到“桶”中，接着将其加 1 后再赋值给 obj.foo，

此时会触发 trigger 操作，即把“桶”中的副作用函数取出并执行。但问题是该副作用函数正在执行中，还没有执行完毕，就要开始下一次的执行。

这样会导致无限递归地调用自己，于是就产生了栈溢出。

解决办法并不难。

通过分析这个问题我们能够发现，读取和设置操作是在同一个副作用函数内进行的。

此时无论是 track 时收集的副作用函数，还是 trigger 时要触发执行的副作用函数，都是activeEffect。

基于此，我们可以在 trigger 动作发生时增加守卫条件：如果 trigger 触发执行的副作用函数与当前正在执行的副作用函数相同，则不触发执行，如以下代码所示：

```js
function trigger(target, key) {
  const depsMap = bucket.get(target)
  if (!depsMap) return
  const effects = depsMap.get(key)

  const effectsToRun = new Set()
  effects && effects.forEach(effectFn => {
    // 如果 trigger 触发执行的副作用函数与当前正在执行的副作用函数相同，则不触发执行
    if (effectFn !== activeEffect) { // 新增
      effectsToRun.add(effectFn)
    }
  })
  effectsToRun.forEach(effectFn => effectFn())
  // effects && effects.forEach(effectFn => effectFn())
}
```

这样就能够避免无限递归调用，从而避免栈溢出。

```html
<body></body>
<script>
// 存储副作用函数的桶
const bucket = new WeakMap()

// 原始数据
const data = { foo: 1 }
// 对原始数据的代理
const obj = new Proxy(data, {
  // 拦截读取操作
  get(target, key) {
    // 将副作用函数 activeEffect 添加到存储副作用函数的桶中
    track(target, key)
    // 返回属性值
    return target[key]
  },
  // 拦截设置操作
  set(target, key, newVal) {
    // 设置属性值
    target[key] = newVal
    // 把副作用函数从桶里取出并执行
    trigger(target, key)
  }
})

function track(target, key) {
  let depsMap = bucket.get(target)
  if (!depsMap) {
    bucket.set(target, (depsMap = new Map()))
  }
  let deps = depsMap.get(key)
  if (!deps) {
    depsMap.set(key, (deps = new Set()))
  }
  deps.add(activeEffect)
  activeEffect.deps.push(deps)
}

function trigger(target, key) {
  const depsMap = bucket.get(target)
  if (!depsMap) return
  const effects = depsMap.get(key)

  const effectsToRun = new Set()
  effects && effects.forEach(effectFn => {
    // 如果 trigger 触发执行的副作用函数与当前正在执行的副作用函数相同，则不触发执行
    if (effectFn !== activeEffect) { // 新增
      effectsToRun.add(effectFn)
    }
  })
  effectsToRun.forEach(effectFn => effectFn())
  // effects && effects.forEach(effectFn => effectFn())
}

// 用一个全局变量存储当前激活的 effect 函数
let activeEffect
// effect 栈
const effectStack = []

function effect(fn) {
  const effectFn = () => {
    cleanup(effectFn)
    // 当调用 effect 注册副作用函数时，将副作用函数复制给 activeEffect
    activeEffect = effectFn
    // 在调用副作用函数之前将当前副作用函数压栈
    effectStack.push(effectFn)
    fn()
    // 在当前副作用函数执行完毕后，将当前副作用函数弹出栈，并还原 activeEffect 为之前的值
    effectStack.pop()
    activeEffect = effectStack[effectStack.length - 1]
  }
  // activeEffect.deps 用来存储所有与该副作用函数相关的依赖集合
  effectFn.deps = []
  // 执行副作用函数
  effectFn()
}

function cleanup(effectFn) {
  for (let i = 0; i < effectFn.deps.length; i++) {
    const deps = effectFn.deps[i]
    deps.delete(effectFn)
  }
  effectFn.deps.length = 0
}

// =========================
effect(() => {
  console.log(99)
  obj.foo++
})
</script>
```

## 调度执行

可调度，指的是当 trigger 动作触发副作用函数重新执行时，有能力决定副作用函数执行的时机、次数以及方式。

以下面的代码为例：

```js
const data = { foo: 1 }
const obj = new Proxy(data, { /* ... */ })0304 effect(() => {05 console.log(obj.foo)06 })0708 obj.foo++09
console.log('结束了')
```

在副作用函数中，我们首先使用 console.log 语句打印obj.foo 的值，接着对 obj.foo 执行自增操作，最后使用console.log 语句打印 '结束了'。

这段代码的输出结果如下：

```
1
2
'结束了'
```

现在假设需求有变，输出顺序需要调整为：

```
1
'结束了'
2
```

根据打印结果我们很容易想到对策，即把语句 obj.foo++ 和语句 console.log('结束了') 位置互换即可。

那么有没有什么办法能够在不调整代码的情况下实现需求呢？这时就需要响应系统支持调度。

我们可以为 effect 函数设计一个选项参数 options，允许用户指定调度器：

```js
effect(
  () => {
    console.log(obj.foo)
  },
  // options
  {
    // 调度器 scheduler 是一个函数
    scheduler(fn) {
      // ...
    }
  }
)
```

用户在调用 effect 函数注册副作用函数时，可以传递第二个参数 options。

它是一个对象，其中允许指定scheduler 调度函数，同时在 effect 函数内部我们需要把options 选项挂载到对应的副作用函数上：

```js
function effect(fn, options = {}) {
  const effectFn = () => {
    cleanup(effectFn)
    // 当调用 effect 注册副作用函数时，将副作用函数复制给 activeEffect
    activeEffect = effectFn
    // 在调用副作用函数之前将当前副作用函数压栈
    effectStack.push(effectFn)
    fn()
    // 在当前副作用函数执行完毕后，将当前副作用函数弹出栈，并还原 activeEffect 为之前的值
    effectStack.pop()
    activeEffect = effectStack[effectStack.length - 1]
  }
  // 将 options 挂在到 effectFn 上
  effectFn.options = options      // 新增       
  // activeEffect.deps 用来存储所有与该副作用函数相关的依赖集合
  effectFn.deps = []
  // 执行副作用函数
  effectFn()
}
```

有了调度函数，我们在 trigger 函数中触发副作用函数重新执行时，就可以直接调用用户传递的调度器函数，从而把控制权交给用户：  

```js
function trigger(target, key) {
  const depsMap = bucket.get(target)
  if (!depsMap) return
  const effects = depsMap.get(key)

  const effectsToRun = new Set()
  effects && effects.forEach(effectFn => {
    if (effectFn !== activeEffect) {
      effectsToRun.add(effectFn)
    }
  })
  effectsToRun.forEach(effectFn => {
    // 如果一个副作用函数存在调度器，则调用该调度器，并将副作用函数作为参数传递
    if (effectFn.options.scheduler) {      // 新增
      effectFn.options.scheduler(effectFn) // 新增
    } else {
      // 否则直接执行副作用函数（之前的默认行为）  
      effectFn() // 新增
    }
  })
  // effects && effects.forEach(effectFn => effectFn())
}
```

在 trigger 动作触发副作用函数执行时，我们优先判断该副作用函数是否存在调度器，

如果存在，则直接调用调度器函数，并把当前副作用函数作为参数传递过去，由用户自己控制如何执行；

否则保留之前的行为，即直接执行副作用函数。  

有了这些之后，我们就可以实现需求了，如以下代码所示：

```js
const data = { foo: 1 }
const obj = new Proxy(data, { /* ... */ })

effect(
  () => {
    console.log(obj.foo)
  },
  // options
  {
    // 调度器 scheduler 是一个函数
    scheduler(fn) {
      // 将副作用函数放到宏任务队列中执行
      setTimeout(fn)
     }
   }
)

obj.foo++

console.log('结束了')
```

我们使用 setTimeout 开启一个宏任务来执行副作用函数 fn，这样就能实现期望的打印顺序了：

```
1
'结束了'
2
```

除了控制副作用函数的执行顺序，通过调度器还可以做到控制它的执行次数，这一点也尤为重要。我们思考如下例子：

```js
const data = { foo: 1 }
const obj = new Proxy(data, { /* ... */ })

effect(() => {
  console.log(obj.foo)
})

obj.foo++
obj.foo++
```

首先在副作用函数中打印 obj.foo 的值，接着连续对其执行两次自增操作，在没有指定调度器的情况下，它的输出如下：

```
1
2
3
```

由输出可知，字段 obj.foo 的值一定会从 1 自增到 3，2 只是它的过渡状态。

如果我们只关心最终结果而不关心过程，那么执行三次打印操作是多余的，我们期望的打印结果是：

```
1
3
```

其中不包含过渡状态

```html
<body></body>
<script>
// 存储副作用函数的桶
const bucket = new WeakMap()

// 原始数据
const data = { foo: 1 }
// 对原始数据的代理
const obj = new Proxy(data, {
  // 拦截读取操作
  get(target, key) {
    // 将副作用函数 activeEffect 添加到存储副作用函数的桶中
    track(target, key)
    // 返回属性值
    return target[key]
  },
  // 拦截设置操作
  set(target, key, newVal) {
    // 设置属性值
    target[key] = newVal
    // 把副作用函数从桶里取出并执行
    trigger(target, key)
  }
})

function track(target, key) {
  if (!activeEffect) return
  let depsMap = bucket.get(target)
  if (!depsMap) {
    bucket.set(target, (depsMap = new Map()))
  }
  let deps = depsMap.get(key)
  if (!deps) {
    depsMap.set(key, (deps = new Set()))
  }
  deps.add(activeEffect)
  activeEffect.deps.push(deps)
}

function trigger(target, key) {
  const depsMap = bucket.get(target)
  if (!depsMap) return
  const effects = depsMap.get(key)

  const effectsToRun = new Set()
  effects && effects.forEach(effectFn => {
    if (effectFn !== activeEffect) {
      effectsToRun.add(effectFn)
    }
  })
  effectsToRun.forEach(effectFn => {
    // 如果一个副作用函数存在调度器，则调用该调度器，并将副作用函数作为参数传递
    if (effectFn.options.scheduler) {      // 新增
      effectFn.options.scheduler(effectFn) // 新增
    } else {
      // 否则直接执行副作用函数（之前的默认行为）  
      effectFn() // 新增
    }
  })
  // effects && effects.forEach(effectFn => effectFn())
}

// 用一个全局变量存储当前激活的 effect 函数
let activeEffect
// effect 栈
const effectStack = []

function effect(fn, options = {}) {
  const effectFn = () => {
    cleanup(effectFn)
    // 当调用 effect 注册副作用函数时，将副作用函数复制给 activeEffect
    activeEffect = effectFn
    // 在调用副作用函数之前将当前副作用函数压栈
    effectStack.push(effectFn)
    fn()
    // 在当前副作用函数执行完毕后，将当前副作用函数弹出栈，并还原 activeEffect 为之前的值
    effectStack.pop()
    activeEffect = effectStack[effectStack.length - 1]
  }
  // 将 options 挂在到 effectFn 上
  effectFn.options = options      // 新增       
  // activeEffect.deps 用来存储所有与该副作用函数相关的依赖集合
  effectFn.deps = []
  // 执行副作用函数
  effectFn()
}

function cleanup(effectFn) {
  for (let i = 0; i < effectFn.deps.length; i++) {
    const deps = effectFn.deps[i]
    deps.delete(effectFn)
  }
  effectFn.deps.length = 0
}

// =========================
// 定义一个任务队列
const jobQueue = new Set()
// 使用 Promise.resolve() 创建一个 promise 实例，我们用它将一个任务添加到微任务队列
const p = Promise.resolve()
// 一个标志代表是否正在刷新队列
let isFlushing = false
function flushJob() {
  // 如果队列正在刷新，则什么都不做
  if (isFlushing) return
  // 设置为 true，代表正在刷新  
  isFlushing = true
  // 在微任务队列中刷新 jobQueue 队列  
  p.then(() => {
    jobQueue.forEach(job => job())
  }).finally(() => {
    // 结束后重置 isFlushing  
    isFlushing = false
  })
}

effect(() => {
  console.log(obj.foo)
}, {
  scheduler(fn) {
    // 每次调度时，将副作用函数添加到 jobQueue 队列中  
    jobQueue.add(fn)
    // 调用 flushJob 刷新队列  
    flushJob()
  }
})

obj.foo++
obj.foo++
</script>
```

首先，我们定义了一个任务队列 jobQueue，它是一个 Set 数据结构，目的是利用 Set 数据结构的自动去重能力。

接着我们看调度器 scheduler 的实现，在每次调度执行时，先将当前副作用函数添加到 jobQueue 队列中，再调用 flushJob 函数刷新队列。

然后我们把目光转向 flushJob 函数，该函数通过 isFlushing 标志判断是否需要执行，只有当其为 false 时才需要执行，

而一旦 flushJob 函数开始执行，isFlushing 标志就会设置为true，意思是无论调用多少次 flushJob 函数，在一个周期内都只会执行一次。

需要注意的是，在 flushJob 内通过 p.then 将一个函数添加到微任务队列，在微任务队列内完成对 jobQueue 的遍历执行。

整段代码的效果是，连续对 obj.foo 执行两次自增操作，会同步且连续地执行两次 scheduler 调度函数，

这意味着同一个副作用函数会被 jobQueue.add(fn) 语句添加两次，但由于 Set 数据结构的去重能力，最终 jobQueue 中只会有一项，即当前副作用函数。

类似地，flushJob 也会同步且连续地执行两次，

但由于 isFlushing 标志的存在，实际上 flushJob 函数在一个事件循环内只会执行一次，即在微任务队列内执行一次。

当微任务队列开始执行时，就会遍历jobQueue 并执行里面存储的副作用函数。

由于此时 jobQueue 队列内只有一个副作用函数，所以只会执行一次，并且当它执行时，字段obj.foo 的值已经是 3 了，

这样我们就实现了期望的输出：

```
1
3
```

这个功能有点类似于在 Vue.js 中连续多次修改响应式数据但只会触发一次更新，实际上 Vue.js 内部实现了一个更加完善的调度器，思路与上文介绍的相同 。

## 计算属性 computed 与 lazy

懒执行的effect，即 lazy 的 effect。

现在我们所实现的 effect 函数会立即执行传递给它的副作用函数，例如：

```js
effect(
   // 这个函数会立即执行
   () => {
     console.log(obj.foo)
   }
)
```

但在有些场景下，我们并不希望它立即执行，而是希望它在需要的时候才执行，例如计算属性。

我们可以通过在 options 中添加 lazy 属性来达到目的，如下面的代码所示：

```js
effect(
  // 指定了 lazy 选项，这个函数不会立即执行
  () => { 
    console.log(obj.foo)
  },
  // options
  {
    lazy: true
  }
)
```

lazy 选项和之前介绍的 scheduler 一样，它通过 options 选项对象指定。

有了它，我们就可以修改 effect 函数的实现逻辑，当 options.lazy 为 true 时，则不立即执行副作用函数：

```js
function effect(fn, options = {}) {
  const effectFn = () => {
    cleanup(effectFn)
    activeEffect = effectFn
    effectStack.push(effectFn)
    fn()
    effectStack.pop()
    activeEffect = effectStack[effectStack.length - 1]
  }
  effectFn.options = options
  effectFn.deps = []
  // 只有非 lazy 的时候，才执行副作用函数
  if (!options.lazy) { // 新增
    // 执行副作用函数
    effectFn()
  }
  // 将副作用函数作为返回值返回
  return effectFn // 新增
}
```

这个判断实现了让副作用函数不立即执行的功能。但问题是，副作用函数应该什么时候执行呢？  

上面的代码可以看到，将副作用函数 effectFn 作为 effect 函数的返回值，这就意味着当调用 effect 函数时，

通过其返回值能够拿到对应的副作用函数，这样就能手动执行该副作用函数了：

```js
const effectFn = effect(() => { 
  console.log(obj.foo) 
}, { lazy: true })
// 手动执行副作用函数
effectFn()
```

如果仅仅能够手动执行副作用函数，其意义并不大。

但如果把传递给 effect 的函数看作一个 getter，那么这个 getter 函数可以返回任何值，例如

```js
const effectFn = effect(
   // getter 返回 obj.foo 与 obj.bar 的和
   () => obj.foo + obj.bar,
   { lazy: true }
)
```

这样在手动执行副作用函数时，就能够拿到其返回值：

```js
const effectFn = effect(
   // getter 返回 obj.foo 与 obj.bar 的和
   () => obj.foo + obj.bar,
   { lazy: true }
)
// value 是 getter 的返回值
 const value = effectFn()
```

为了实现这个目标，需要再对 effect 函数做一些修改，如以下代码所示：  

```js
function effect(fn, options = {}) {
  const effectFn = () => {
    cleanup(effectFn)
    activeEffect = effectFn
    effectStack.push(effectFn)
    // 将 fn 的执行结果存储到 res 中  
    const res = fn() // 新增
    effectStack.pop()
    activeEffect = effectStack[effectStack.length - 1]
    // 将 res 作为 effectFn 的返回值
    return res // 新增
  }
  effectFn.options = options
  effectFn.deps = []
  if (!options.lazy) {
    effectFn()
  }
  return effectFn
}
```

通过新增的代码可以看到，传递给 effect 函数的参数 fn 才是真正的副作用函数，而 effectFn 是我们包装后的副作用函数。

为了通过 effectFn 得到真正的副作用函数 fn 的执行结果，我们需要将其保存到 res 变量中，然后将其作为 effectFn 函数的返回值。

所以现在已经能够实现懒执行的副作用函数，并且能够拿到副作用函数的执行结果了  

接下来就可以实现计算属性

```js
function computed(getter) {
  // 把 getter 作为副作用函数，创建一个 lazy 的 effect
  const effectFn = effect(getter, {
    lazy: true
  })

  const obj = {
    // 当读取 value 时才执行 effectFn
    get value() {
      return effectFn()
    }
  }

  return obj
}
```

首先我们定义一个 computed 函数，它接收一个 getter 函数作为参数，我们把 getter 函数作为副作用函数，用它创建一个 lazy的 effect。

computed 函数的执行会返回一个对象，该对象的value 属性是一个访问器属性，只有当读取 value 的值时，才会执行 effectFn 并将其结果作为返回值返回

可以使用 computed 函数来创建一个计算属性：

```js
const data = { foo: 1, bar: 2 }
const obj = new Proxy(data, { /* ... */ })

const sumRes = computed(() => obj.foo + obj.bar)
console.log(sumRes.value) // 3
```

现在我们实现的计算属性只做到了懒计算，也就是说，只有当你真正读取 sumRes.value 的值时，它才会进行计算并得到值。

但是还做不到对值进行缓存，即假如我们多次访问 sumRes.value 的值，会导致 effectFn 进行多次计算，即使 obj.foo 和 obj.bar 的值本身并没有变化：

```js
console.log(sumRes.value) // 3
console.log(sumRes.value) // 3
console.log(sumRes.value) // 3
```

上面的代码多次访问 sumRes.value 的值，每次访问都会调用 effectFn 重新计算

为了解决这个问题，就需要我们在实现 computed 函数时，添加对值进行缓存的功能，如以下代码所示：

```js
function computed(getter) {
  // value 用来缓存上一次计算的值
  let value
  // dirty 标志，用来标识是否需要重新计算值，为 true 则意味着“脏”，需要计算
  let dirty = true

  const effectFn = effect(getter, {
    lazy: true
  })

  const obj = {
    get value() {
      // 只有“脏”时才计算值，并将得到的值缓存到 value 中
      if (dirty) {
        value = effectFn()
        // 将 dirty 设置为 false，下一次访问直接使用缓存到 value 中的值
        dirty = false
       }
       return value
     }
  }
  return obj
}
```

我们新增了两个变量 value 和 dirty，其中 value 用来缓存上一次计算的值，

而 dirty 是一个标识，代表是否需要重新计算。

当我们通过 sumRes.value 访问值时，只有当 dirty 为 true 时才会调用 effectFn 重新计算值，否则直接使用上一次缓存在 value 中的值。

这样无论我们访问多少次 sumRes.value，都只会在第一次访问时进行真正的计算，后续访问都会直接读取缓存的 value 值

如果此时我们修改 obj.foo或 obj.bar 的值，再访问 sumRes.value 会发现访问到的值没有发生变化：

```js
const data = { foo: 1, bar: 2 }
const obj = new Proxy(data, { /* ... */ })

const sumRes = computed(() => obj.foo + obj.bar)

console.log(sumRes.value) // 3
console.log(sumRes.value) // 3

// 修改 obj.foo
obj.foo++

// 再次访问，得到的仍然是 3，但预期结果应该是 4
console.log(sumRes.value) // 3
```

这是因为，当第一次访问 sumRes.value 的值后，变量 dirty 会设置为 false，代表不需要计算。

即使我们修改了 obj.foo 的值，但只要 dirty 的值为 false，就不会重新计算，所以导致我们得到了错误的值。

解决办法很简单，当 obj.foo 或 obj.bar 的值发生变化时，只要 dirty 的值重置为 true 就可以了。

这时就用到了 scheduler 选项，如以下代码所示：

```js
function computed(getter) {
  let value
  let dirty = true04
  const effectFn = effect(getter, {
    lazy: true,
    // 添加调度器，在调度器中将 dirty 重置为 true
    scheduler() {
      dirty = true
    }
  })

  const obj = {
    get value() {
      if (dirty) {
        value = effectFn()
        dirty = false
      }
      return value
    }
  }

  return obj
}
```

为 effect 添加了 scheduler 调度器函数，它会在 getter函数中所依赖的响应式数据变化时执行，这样我们在 scheduler 函数内将 dirty 重置为 true，

当下一次访问 sumRes.value 时，就会重新调用 effectFn 计算值，这样就能够得到预期的结果了。  

但还有一个缺陷，它体现在当我们在另外一个 effect 中读取计算属性的值时：

```js
const sumRes = computed(() => obj.foo + obj.bar)

effect(() => {
  // 在该副作用函数中读取 sumRes.value
  console.log(sumRes.value)
})

// 修改 obj.foo 的值
obj.foo++
```

以上代码所示，sumRes 是一个计算属性，并且在另一个effect 的副作用函数中读取了 sumRes.value 的值。

如果此时修改 obj.foo 的值，我们期望副作用函数重新执行，

就像我们在 Vue.js 的模板中读取计算属性值的时候，一旦计算属性发生变化就会触发重新渲染一样。

但是如果尝试运行上面这段代码，会发现修改 obj.foo 的值并不会触发副作用函数的渲染，因此我们说这是一个缺陷。

本质上看这就是一个典型的 effect 嵌套。

一个计算属性内部拥有自己的 effect，并且它是懒执行的，只有当真正读取计算属性的值时才会执行。

对于计算属性的 getter 函数来说，它里面访问的响应式数据只会把 computed 内部的 effect 收集为依赖。

而当把计算属性用于另外一个 effect 时，就会发生 effect 嵌套，外层的 effect 不会被内层 effect 中的响应式数据收集。

解决办法很简单。当读取计算属性的值时，我们可以手动调用 track 函数进行追踪；

当计算属性依赖的响应式数据发生变化时，我们可以手动调用 trigger 函数触发响应：

```js
function computed(getter) {
  let value
  let dirty = true

  const effectFn = effect(getter, {
    lazy: true,
    scheduler() {
      if (!dirty) {
        dirty = true
        // 当计算属性依赖的响应式数据变化时，手动调用 trigger 函数触发响应
        trigger(obj, 'value')
      }
    }
  })

  const obj = {
    get value() {
      if (dirty) {
        value = effectFn()
        dirty = false
      }
      // 当读取 value 时，手动调用 track 函数进行追踪
      track(obj, 'value')
      return value
    }
  }
  return obj
}
```

如以上代码所示，当读取一个计算属性的 value 值时，我们手动调用 track 函数，把计算属性返回的对象 obj 作为 target，同时作为第一个参数传递给 track 函数。

当计算属性所依赖的响应式数据变化时，会执行调度器函数，在调度器函数内手动调用 trigger 函数触发响应即可。

这时，对于如下代码来说：

```js
effect(function effectFn() {
  console.log(sumRes.value)
})
```

它会建立这样的联系：

```
computed(obj)
  └── value
    └── effectFn
```

计算属性的响应联系

![Snipaste_2023-06-14_16-01-53](https://cdn.nlark.com/yuque/0/2023/png/1614731/1686729736460-14847514-8c82-46c5-b348-1af51c62c6a8.png)

完整代码

```html
<body></body>
<script>
// 存储副作用函数的桶
const bucket = new WeakMap()

// 原始数据
const data = { foo: 1, bar: 2 }
// 对原始数据的代理
const obj = new Proxy(data, {
  // 拦截读取操作
  get(target, key) {
    // 将副作用函数 activeEffect 添加到存储副作用函数的桶中
    track(target, key)
    // 返回属性值
    return target[key]
  },
  // 拦截设置操作
  set(target, key, newVal) {
    // 设置属性值
    target[key] = newVal
    // 把副作用函数从桶里取出并执行
    trigger(target, key)
  }
})

function track(target, key) {
  if (!activeEffect) return
  let depsMap = bucket.get(target)
  if (!depsMap) {
    bucket.set(target, (depsMap = new Map()))
  }
  let deps = depsMap.get(key)
  if (!deps) {
    depsMap.set(key, (deps = new Set()))
  }
  deps.add(activeEffect)
  activeEffect.deps.push(deps)
}

function trigger(target, key) {
  const depsMap = bucket.get(target)
  if (!depsMap) return
  const effects = depsMap.get(key)

  const effectsToRun = new Set()
  effects && effects.forEach(effectFn => {
    if (effectFn !== activeEffect) {
      effectsToRun.add(effectFn)
    }
  })
  effectsToRun.forEach(effectFn => {
    if (effectFn.options.scheduler) {
      effectFn.options.scheduler(effectFn)
    } else {
      effectFn()
    }
  })
  // effects && effects.forEach(effectFn => effectFn())
}

// 用一个全局变量存储当前激活的 effect 函数
let activeEffect
// effect 栈
const effectStack = []

function effect(fn, options = {}) {
  const effectFn = () => {
    cleanup(effectFn)
    // 当调用 effect 注册副作用函数时，将副作用函数复制给 activeEffect
    activeEffect = effectFn
    // 在调用副作用函数之前将当前副作用函数压栈
    effectStack.push(effectFn)
    // 将 fn 的执行结果存储到 res 中  
    const res = fn() // 新增
    // 在当前副作用函数执行完毕后，将当前副作用函数弹出栈，并还原 activeEffect 为之前的值
    effectStack.pop()
    activeEffect = effectStack[effectStack.length - 1]
    // 将 res 作为 effectFn 的返回值
    return res // 新增
  }
  // 将 options 挂在到 effectFn 上
  effectFn.options = options
  // activeEffect.deps 用来存储所有与该副作用函数相关的依赖集合
  effectFn.deps = []
  // 只有非 lazy 的时候，才执行副作用函数
  if (!options.lazy) { // 新增
    // 执行副作用函数
    effectFn()
  }
  // 将副作用函数作为返回值返回
  return effectFn // 新增
}

function cleanup(effectFn) {
  for (let i = 0; i < effectFn.deps.length; i++) {
    const deps = effectFn.deps[i]
    deps.delete(effectFn)
  }
  effectFn.deps.length = 0
}

    
// =========================
function computed(getter) {
  // value 用来缓存上一次计算的值  
  let value
  // dirty 标志，用来标识是否需要重新计算值，为 true 则意味着“脏”，需要计算
  let dirty = true

  const effectFn = effect(getter, {
    lazy: true,
    // 添加调度器，在调度器中将 dirty 重置为 true  
    scheduler() {
      if (!dirty) {
        dirty = true
        // 当计算属性依赖的响应式数据变化时，手动调用 trigger 函数触发响应  
        trigger(obj, 'value')
      }
    }
  })
  
  const obj = {
    get value() {
       // 只有“脏”时才计算值，并将得到的值缓存到 value 中  
      if (dirty) {
        value = effectFn()
        // 将 dirty 设置为 false，下一次访问直接使用缓存到 value 中的值  
        dirty = false
      }
      // 当读取 value 时，手动调用 track 函数进行追踪  
      track(obj, 'value')
      return value
    }
  }

  return obj
}

const sumRes = computed(() => obj.foo + obj.bar)

console.log(sumRes.value)
console.log(sumRes.value)

obj.foo++

console.log(sumRes.value)

effect(() => {
  console.log(sumRes.value)
})

obj.foo++
</script>
```

## watch 的实现原理





























































































































