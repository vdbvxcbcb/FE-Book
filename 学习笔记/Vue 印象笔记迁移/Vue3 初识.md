## Vue 3 官方中文文档

https://cn.vuejs.org/

## 工具

Vue3 单文件组件 (SFC) 的 TS IDE 支持请用 `<script setup lang="ts">` + VSCode + Volar。

VSCode：前端最好用的 IDE。

[snippet generator](https://snippet-generator.app/)：生成 VSCode 代码片段设置，首选项 => 用户代码片段 => 输入 vue

Volar：为 Vue3 的 *.vue 单文件组件提供代码高亮、语法提示等功能支持的 VSCode 插件；

Vue2 你可能是使用的 Vetur 插件，需要禁用 Vetur，下载 Volar，并启用它。

vue-tsc：类型检查和 dts 构建命令行工具。类型检查使用 vue-tsc 命令。

vue.config.js: webpack 配置，例如路径别名

jsconfig.json: vscode 代码提示、打包等相关

## 其它

.vue 单文件组件的 template 由 vue-loader 编译为虚拟 DOM 再转换为真实 DOM

普通对象里的 template 选项由 vue.esm-bundler 来编译

style 的样式作用域原理是在元素上添加 data-v 的属性选择器

## Fragment

在 template 中不再需要一个根元素包裹

```vue
<template>
  <img alt="Vue logo" src="./assets/logo.png" />
  <HelloWorld msg="Welcome to Your Vue.js + TypeScript App" />
</template>
```

实际上内部会将多个标签包含在一个 Fragment 虚拟元素中

好处: 减少标签层级, 减小内存占用

## setup 与 组合 API

script 要使用 ts ，只需设置 lang 即可

### setup

为了开始使用 Composition API，我们需要有一个可以实际使用它 （编写代码）的地方；

在 Vue 组件中，这个位置入口就是 setup 函数；

`<script setup>` 是在单文件组件 (SFC) 中使用 composition api 的编译时语法糖。

在使用 Vue 3 生命周期的情况下，整个组件相关的业务代码，都可以放在 `setup` 里执行。

因为在 `setup` 之后，其他的生命周期才会被启用

#### 特点一：setup 不可以使用 this 

setup 执行顺序：**它比 beforeCreate 和 created 这两个生命周期还要快**，beforeCreate 这个钩子的任务就是初始化，

vue3 取消了这两个钩子，统一用 setup 代替, setup 相当于一个生命周期函数

**setup  在它们之前执行，那么 this 就没有被初始化，所以 this = undefined ，不能通过 this 来调用方法 和 获取属性**

#### 特点二：组件以文件名为准,直接引入即可使用，无需注册

引入的组件可以直接使用，无需再通过 components 进行注册，并且不用指定当前组件的名字，

**它会自动以文件名为准，也就是不用再写 name 属性了**

**从 3.3 开始可以直接通过 `<script setup>` 中的 defineOptions 来设置组件名或 inheritAttrs 属性。**

之前写法

```vue
<template>
  <div>
    <h2>我是父组件!</h2>
    <Child />
  </div>
</template>

<script>
import { defineComponent } from 'vue';
import Child from './Child.vue'

export default defineComponent({
  components: {
    Child
  },
  setup() {

    return {
      
    }
  }
});
</script>
```

setup 写法

**不需要 import { defineComponent } from 'vue'; 和 components: { xxx }**

```vue
<template>
  <div>
    <h2>我是父组件!-setup script</h2>
    <Child />
  </div>
</template>

<script setup>
import Child from './Child.vue'
</script>
```

#### 特点三：属性和方法无需挂载到对象上后返回才能在模板使用

之前写法

```vue
<template>
  <div>
    <h2 @click="ageInc">{{ name }} is {{ age }}</h2>
  </div>
</template>

<script>
import { defineComponent, ref } from 'vue';

export default defineComponent({
  setup() {
    const name = ref('CoCoyY1')
    const age = ref(18)

    const ageInc = () => {
      age.value++
    }

    return {
      name,
      age,
      ageInc
    }
  }
})
</script>
```

setup 写法

```vue
<template>
  <div>
    <h2 @click="ageInc">{{ name }} is {{ age }}</h2>
  </div>
</template>

<script setup>
import { ref } from 'vue';

const name = ref('CoCoyY1')
const age = ref(18)

const ageInc = () => {
  age.value++
}

</script>
```

不使用 `<script setup>` 要在 `<script>` 中返回一个对象

```vue
<template>
  <div>
    <h1 @click="add">{{count}}</h1>
  </div>
</template>

<script>
import { ref } from "vue";
export default {
  setup() {
    let count = ref(1)
    function add() {
      count.value++
    }
    return {
      count,
      add
    }
  }
}
</script>

<style scoped>
h1 {
  color: red;
}
</style>
```

**使用 `<script setup>` 不需要返回对象**

```vue
<template>
  <div>
    <h1 @click="add">{{count}}</h1>
  </div>
</template>

<script setup>
import { ref } from "vue";
let count = ref(1)
function add(){
  count.value++
}
</script>

<style scoped>
h1 {
  color: red;
}
</style>
```

template 的编译

```vue
<template>
  <div>
    <Foo />
    <h2 @click="increment">{{ count }}</h2>
  </div>
</template>

<script setup>
  import { ref } from 'vue'
  import Foo from './components/Foo.vue'

  const count = ref(0)
  const increment = () => count.value++
</script>
```

上述代码会被编译为

```js
// 导入的模块会被抽离到模块级别
import { ref } from 'vue'
import Foo from './components/Foo.vue'

export default {
  setup() {
    const count = ref(0)
    const increment = () => count.value++
    	
    // 这是一个返回 h 函数的 render 函数
    // 因为被编译到了 setup 函数中，所以可以直接访问顶层定义的属性和方法
    return () => ([
      h(Foo, null, ''),
      h('h2', {
        count,
        onClick: increment
      }, count)
    ])
  }
}
```

#### 特点四：props 、 emits (defineProps 和 defineEmits)

**在 `<script setup>` 中必须使用 defineProps 和 defineEmits API 来声明 props 和 emits**

> 和原生 DOM 事件不一样，**组件触发的事件没有冒泡机制**。
>
> 只能监听直接子组件触发的事件。
>
> 平级组件或是跨越多层嵌套的组件间通信，应使用一个外部的事件总线，或是使用一个[全局状态管理方案](https://cn.vuejs.org/guide/scaling-up/state-management.html)。
>
> **如果一个原生事件的名字 (例如 `click`) 被定义在 `emits` 选项中，**
>
> **则监听器只会监听组件触发的 `click` 事件而不会再响应原生的 `click` 事件。**

##### 编译器宏

**编译器宏（compiler macros） 有：defineProps、defineEmits、withDefaults、defineExpose 等。**

**编译器宏只能在 `<script setup>` 块中使用，不需要被导入，**

并且会在处理 `<script setup>` 块时被一同编译掉。

**编译器宏必须在 `<script setup>` 的顶层使用，不可以在`<script setup>` 的局部变量中引用。**

传入的选项不能引用在 setup 范围中声明的局部变量。这样做会引起编译错误。

###### defineProps 和 defineEmits

defineProps 和 defineEmits 都是只在 `<script setup>` 中才能使用的编译器宏。

defineProps 接收与 props 选项相同的值，defineEmits 也接收 emits 选项相同的值。

defineProps 和 defineEmits 在选项传入后，会提供恰当的类型推断。

传入到 defineProps 和 defineEmits 的选项会从 setup 中提升到模块的范围。

在使用 `<script setup>` 的单文件组件中，props 可以使用 `defineProps()` 宏来声明：

```vue
<script setup>
const props = defineProps(['foo'])

console.log(props.foo)
</script>
```

除了使用字符串数组来声明 prop 外，还可以使用对象的形式：

```js
// 使用 <script setup>
defineProps({
  title: String,
  likes: Number
})
```

对于以对象形式声明中的每个属性，key 是 prop 的名称，而值则是该 prop 预期类型的构造函数。

比如，如果要求一个 prop 的值是 `number` 类型，则可使用 `Number` 构造函数作为其声明的值。

之前写法

```js
export default {
  props: {
    foo: String,
  },
  emits: ['change', 'delete'],
  
  // setup 中定义的 props 和 emits 会被抽取到模块作用域中
  setup(props, { emit }) {
    // setup code
  },
}
```

setup 写法

```vue
<script setup lang="ts">
  let props = defineProps({
    // 显示的图标
    icon: {
      type: String,
      default: 'Bell',
    },
    // 通知数量
    value: {
      type: [String, Number],
      default: '',
    },
    // 最大值
    max: {
      type: Number,
    },
    // 是否显示小圆点
    isDot: {
      type: Boolean,
      default: false,
    },
  });
  // 获取 emit
  const emit = defineEmits(['update-name'])
  // 调用 emit
  emit('update-name', 'Tom')
</script>
```

我们在 `<template>` 中使用的 `$emit` 方法不能直接在组件的 `<script setup>` 部分中使用，

但是`defineEmits()` 会返回一个相同作用的函数供我们使用：

```vue
<script setup>
const emit = defineEmits(['inFocus', 'submit'])

function buttonClick() {
  emit('submit')
}
</script>
```

`defineEmits()` 宏**不能**在子函数中使用。

如上所示，它必须直接放置在 `<script setup>` 的顶级作用域下。

###### 事件校验

`emits` 选项还支持对象语法，它允许我们对触发事件的参数进行验证：

```vue
<script setup>
const emit = defineEmits({
  submit(payload) {
    // 通过返回值为 true 还是为 false 来判断
    // 验证是否通过
  }
})
</script>
```

要为事件添加校验，那么事件可以被赋值为一个函数，

接受的参数就是抛出事件时`emit()` 传入 的内容，返回一个布尔值来表明事件是否合法。

```vue
<script setup>
const emit = defineEmits({
  // 没有校验
  click: null,

  // 校验 submit 事件
  submit: ({ email, password }) => {
    if (email && password) {
      return true
    } else {
      console.warn('Invalid submit event payload!')
      return false
    }
  }
})

function submitForm(email, password) {
  emit('submit', { email, password })
}
</script>
```

子组件 Foo

```vue
<template>
  <div>
    <h2>{{ count }}</h2>
    <!-- 直接发送 increment 事件 -->
    <button @click="$emit('increment')">+1</button>
    <!-- 间接发送 decrement 事件 -->
    <button @click="decrement">-1</button>
  </div>
</template>

<script setup>
  // 接收 props 并返回一个对象，可以在 js 中使用 props 来获取传入的数据
  const props = defineProps({
    count: {
      type: Number,
      default: 0
    }
  })
  // 声明需要触发的事件，返回一个 emit 函数，作用和 this.$emit 函数的作用是一致的
  const emit = defineEmits(['increment', 'decrement'])
  
  const decrement = () => emit('decrement')
</script>
```

父组件

```vue
<template>
  <div>
    <!-- 自定义 increment 事件和 decrement 事件 -->  
    <Foo :count="count" @increment="increment" @decrement="decrement" />
  </div>
</template>

<script setup>
  import { ref } from 'vue'
  import Foo from './components/Foo.vue'

  const count = ref(0)
  // increment 事件和 decrement 事件触发的方法
  const increment = () => count.value++
  const decrement = () => count.value--
</script>
```

###### TypeScript  中的 defineProps 和 defineEmits

props 和 emits 也可以使用 TypeScript 语法来声明，

方法是向 defineProps 或 defineEmits 传递一个字面类型参数。

```ts
const props = defineProps<{
  foo: string
  bar?: number
}>()

const emit = defineEmits<{
  (e: 'change', id: number): void
  (e: 'update', value: string): void
}>()
```

###### withDefaults

使用 TypeScript 语法来声明 props 和 emits 时没有办法为 props 提供默认值。

为了解决这个问题，提供了一个 withDefaults 编译器宏

```ts
interface Props {
  msg?: string
  labels?: string[]
}

const props = withDefaults(defineProps<Props>(), {
  msg: 'hello',
  labels: () => ['one', 'two']
})
```

#### 特点五：defineExpose (暴露数据和方法给 ref  )

**传统的写法中，我们可以在父组件，通过 ref 实例的方式去访问子组件的内容，**

**但在 script setup 中，该方法就不能用了。**

setup 相当于是一个闭包，除了内部的 template模板，谁都不能访问内部的数据和方法。

**如果需要对外暴露 setup 中的数据和方法，需要使用 defineExpose API**

```vue
<script setup>
import { defineExpose } from 'vue'
const a = 1
const b = 2
defineExpose({
  a
})
</script>
```

子组件

```vue
<template>
  <div>
    <h2>你好-我是xxx</h2>
    <p>性别:{{ sex }}</p>
    <p>其他信息:{{ info }}</p>
  </div>
</template>

<script setup lang="ts">
import { reactive, ref, defineExpose } from "vue";
    
let sex = ref('男');
let info = reactive({
  like:'喜欢xxx',
  age:27
});
// 将组件中的属性暴露出去，这样父组件可以获取
defineExpose({
  sex,
  info
})
</script>
```

父组件

```vue
<template>
  <div class="home">
    <!-- 标记名为 child 的 ref 访问子组件 -->   
    <Child ref="child"></Child>
    <button @click="getSonHander">获取子组件中的数据</button>
  </div>
</template>
<script lang="ts" setup>
import Child from "../components/Child.vue"
import { ref } from 'vue'
// 给子组件定义一个 ref 变量，该变量必须和模板里的 ref 同名  
const child = ref(null)
const getSonHander= () => {
  console.log('获取子组件中的性别', child.value.sex );
  console.log('获取子组件中的其它信息', child.value.info );
}
</script>
```

#### 特点六：slots 和 attrs

Vue3.2 版本，该版本 useContext() 已废弃，被拆分成了`useAttrs`和`useSlots`。

在 `<script setup>` 中使用 slots 和 attrs 应该是比较少的，

如果你确实需要它们，请分别使用 useSlots 和 useAttrs 工具函数

useContext 用来获取组件上下文 context

之前写法

```vue
// Father.vue
<template>
  <div >
    <h2 >我是父组件！</h2>
    <Child msg="hello" @child-click="childCtx" />
  </div>
</template>

<script>
import { defineComponent, ref } from 'vue';
import Child from './Child.vue';

export default defineComponent({
  components: {
    Child
  },
  setup(props, context) {
    const childCtx = (ctx) => {
      console.log(ctx);
    }

    return {
      childCtx
    }
  }
})
</script>


// Child.vue
<template>
  <span @click="handleClick">我是子组件! -- msg: {{ props.msg }}</span>
</template>

<script>
import { defineComponent, ref } from 'vue'

export default defineComponent({
  emits: [
    'child-click'
  ],
  props: {
    msg: String
  },
  setup(props, context) {
    const handleClick = () => {
      context.emit('child-click', context)
    }

    return {
      props,
      handleClick
    }
  },
})
</script>
```

![img](https://cdn.nlark.com/yuque/0/2022/png/1614731/1645272982039-705fc076-e522-43a5-9d7e-0f1a52fffef2.png)

点击一下子组件看看打印了什么。

![img](https://cdn.nlark.com/yuque/0/2022/png/1614731/1645272982119-e33d5ec4-7206-4a6a-b6c3-be5cb7ba87c9.png)

这里可以看到 context 被打印了出来，其中的 attrs、emit、props、slots、expose 属性和方法依然可以使用。

setup 写法

```vue
// Father.vue
<template>
  <div >
    <h2 >我是父组件！</h2>
    <Child msg="hello" @child-click="childSlots" />
  </div>
</template>

<script setup>
import Child from './Child.vue';

const childSlots = (slots) => {
  console.log(slots);
}
</script>


// Child.vue
<template>
  <span @click="handleClick">我是子组件! -- msg: {{ props.msg }}</span>
</template>

<script setup>
import { useAttrs, useSlots } from 'vue'
    
const props = defineProps({
  msg: String
})
const emit = defineEmits(['child-click'])

// 获取 attrs
const attrs = useAttrs();
const slots = useSlots();
    
const handleClick = () => {
  emit('child-click', slots)
}
</script>
```

**useAttrs 访问一个组件的所有透传 attribute**

useSlots 和 useAttrs 是实际的运行时函数，其返回值等价于 setupContext.slots 和 setupContext.attrs。

它们也可以在 Composition API 函数中使用。

```vue
<script setup>
import { useSlots, useAttrs } from 'vue'

const slots = useSlots()
const attrs = useAttrs()
</script>
```

**useSlots**

同样，通过 API 的命名也能了解它是用来**获取插槽**的。

但这个 API 对大部分人来说应该用的比较少，

因为大部分 Vue 开发者应该都是用的 SFC 模式（单组件），插槽可以直接在 template 里使用 `<slot />` 标签渲染。

所以，我个人觉得这个 API 的目标用户是面向 JSX / TSX 的开发者，简单的用法参考如下：

父组件，可以传入默认插槽和命名插槽：

```vue
<template>
  <!-- 子组件 -->
  <ChildTSX>
    <!-- 默认插槽 -->
    <p>I am a default slot from TSX.</p>
    <!-- 默认插槽 -->

    <!-- 命名插槽 -->
    <template #msg>
      <p>I am a msg slot from TSX.</p>
    </template>
    <!-- 命名插槽 -->
  </ChildTSX>
  <!-- 子组件 -->
</template>

<script setup lang="ts">
import ChildTSX from '@cp/context/Child.tsx'
</script>
```

那么在 JSX / TSX 的子组件，通过 useSlots 来获取父组件传进来的 slots 数据进行渲染：

```jsx
import { defineComponent, useSlots } from 'vue'

const ChildTSX = defineComponent({
  setup() {
    // 获取插槽数据
    const slots = useSlots()

    // 渲染组件
    return () => (
      <div>
        <!-- 渲染默认插槽 -->
        <p>{ slots.default ? slots.default() : '' }</p>

        <!-- 渲染命名插槽 -->
        <p>{ slots.msg ? slots.msg() : '' }</p>
      </div>
    )
  },
})

export default ChildTSX
```

#### 特点七：顶层 await

顶层的 await 可以直接在 `<script setup>` 里面使用。

**不需要写 async  即可直接使用 await， setup 自动添加 async 。**

```vue
<script setup>
const post = await fetch('/api/post/1810166').then(res => res.json())
</script>
```

```vue
<script setup>
import Api from '../api/Api'
const data = await Api.getData()
console.log(data)
</script>
```

#### 特点八：在 CSS 中使用 JavaScript 的变量

我们甚至可以**通过 v-bind 函数，直接在 CSS 中使用 JavaScript 中的变量。**

**在 style 内部，我们使用 v-bind 函数绑定 color 的值，**

**就可以动态地通过 JavaScript 的变量实现 CSS 的样式修改，**

**点击累加器的时候文本颜色会随机切换为红或者蓝。**

```vue
<template>
  <div>
    <h1 @click="add">{{ count }}</h1>
  </div>
</template>

<script setup>
import { ref } from "vue";
let count = ref(1)
let color = ref('red')
function add() {
  count.value++
  color.value = Math.random() > 0.5 ? "blue" : "red"
}
</script>

<style scoped>
h1 {
  color: v-bind(color);
}
</style>
```

### 组合 API（Composition API ）

Vue 3 组件默认支持 Options API ，

而 Vue 2 可以通过 [@vue/composition-api](https://www.npmjs.com/package/@vue/composition-api) 插件获得 Composition API 的功能支持

（其中 Vue 2.7 版本内置了该插件， 2.6 及以下的版本需要单独安装）。

**Vue 3 的生命周期里没有了 Vue 实例的 `this` 指向，需要导入 `ref` 、`reactive` 等响应式 API 才能声明并使用响应式数据。**

**Vue 3 中主要是使用`ref`和`reactive`来定义响应式数据。**

**由于  Vue 3 使用的是`proxy`进行响应式监听，所以新增、删除属性也都是响应式的，也就不需要使用`$set $delete`了。**

#### 什么是响应性

这个术语在今天的各种编程讨论中经常出现，但人们说它的时候究竟是想表达什么意思呢？

**本质上，响应性是一种可以使我们声明式地处理变化的编程范式。**

一个经常被拿来当作典型例子的用例即是 Excel 表格：

|      | A    | B    | C    |
| ---- | ---- | ---- | ---- |
| 0    | 1    |      |      |
| 1    | 2    |      |      |
| 2    | 3    |      |      |

这里单元格 A2 中的值是通过公式 `= A0 + A1` 来定义的 (你可以在 A2 上点击来查看或编辑该公式)，因此最终得到的值为 3 。

但如果你试着更改 A0 或 A1，你会注意到 A2 也随即自动更新了。

而 JavaScript 默认并不是这样的。如果我们用 JavaScript 写类似的逻辑：

```js
let A0 = 1
let A1 = 2
let A2 = A0 + A1

console.log(A2) // 3

A0 = 2
console.log(A2) // 仍然是 3
```

当我们更改 `A0` 后，`A2` 不会自动更新。

那么我们如何在 JavaScript 中做到这一点呢？首先，为了能重新运行计算的代码来更新 `A2`，我们需要将其包装为一个函数：

```js
let A2

function update() {
  A2 = A0 + A1
}
```

然后，我们需要定义几个术语：

- 这个 **`update()` 函数会产生一个副作用**，或者就简称为**作用** (effect)，因为它会更改程序里的状态。

- **`A0` 和 `A1` 被视为这个作用的依赖** (dependency)，因为它们的值被用来执行这个作用。

  因此**这次作用（update 函数）可以说是一个它依赖的订阅者** (subscriber)。

我们需要一个魔法函数，能够在 `A0` 或 `A1` (这两个**依赖**) 变化时调用 `update()` (产生**作用**)。

```js
whenDepsChange(update)
```

这个 `whenDepsChange()` 函数有如下的任务：

1. 当一个变量被读取时进行追踪。

   例如我们执行了表达式 `A0 + A1` 的计算，则 `A0` 和 `A1` 都被读取到了。

2. 如果一个变量在当前运行的副作用中被读取了，就将该副作用设为此变量的一个订阅者。

   例如由于 `A0` 和 `A1` 在 `update()` 执行时被访问到了，则 `update()` 需要在第一次调用之后成为 `A0` 和 `A1` 的订阅者。

3. 探测一个变量的变化。

   例如当我们给 `A0` 赋了一个新的值后，应该通知其所有订阅了的副作用重新执行。

**为什么 Vue 3 要舍弃 Object.defineProperty ，换成 Proxy 呢？**

**主要原因在于 `Object.defineProperty` 有以下的不足：**

1. **无法监听数组下标的变化，通过 `arr[i] = newValue` 这样的操作无法实时响应**
2. **无法监听数组长度的变化，例如通过 `arr.length = 10` 去修改数组长度，无法响应**
3. **只能监听对象的属性，对于整个对象需要遍历，特别是多级对象更是要通过嵌套来深度监听**
4. **使用 `Object.assign()` 等方法给对象添加新属性时，也不会触发更新**
5. **更多细节上的问题 …**

**这也是为什么 Vue 2 要提供一个 [Vue.set API](https://v2.cn.vuejs.org/v2/api/#Vue-set) 的原因，可以在官网 [Vue 2 中检测变化的注意事项](https://v2.cn.vuejs.org/v2/guide/reactivity.html#检测变化的注意事项) 一章了解更多说明。**

**而这些问题在 `Proxy` 都可以得到解决，可以在官网 [深入 Vue 3 的响应式原理](https://cn.vuejs.org/guide/extras/reactivity-in-depth.html) 一章了解更多这部分的内容。**

#### 模板语法（v-bind 使用注意）

##### 动态绑定多个值

如果有像这样的一个包含多个 attribute 的 JavaScript 对象：

```js
const objectOfAttrs = {
  id: 'container',
  class: 'wrapper'
}
```

通过不带参数的 `v-bind`，可以将它们绑定到一个元素上：

template

```vue
<div v-bind="objectOfAttrs"></div>
```

##### 调用函数

可以在绑定的表达式中使用一个组件暴露的方法：

template

```vue
<span :title="toTitleDate(date)">
  {{ formatDate(date) }}
</span>
```

##### 动态参数

同样在指令参数上也可以使用一个 JavaScript 表达式，需要包含在一对方括号内：

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

这里的 `attributeName` 会作为一个 JavaScript 表达式被动态执行，计算得到的值会被用作最终的参数。

举例来说，如果你的组件实例有一个数据属性 `attributeName`，其值为 `"href"`，那么这个绑定就等价于 `v-bind:href`。

相似地，你还可以将一个函数绑定到动态的事件名称上：

template

```vue
<a v-on:[eventName]="doSomething"> ... </a>

<!-- 简写 -->
<a @[eventName]="doSomething">
```

在此示例中，当 `eventName` 的值是 `"focus"` 时，`v-on:[eventName]` 就等价于 `v-on:focus`。

##### 动态参数值的限制

动态参数中表达式的值应当是一个字符串，或者是 `null`。

特殊值 `null` 意为显式移除该绑定。

其他非字符串的值会触发警告。

##### 动态参数语法的限制

动态参数表达式因为某些字符的缘故有一些语法限制，比如空格和引号，在 HTML attribute 名称中都是不合法的。

例如下面的示例：

template

```vue
<!-- 这会触发一个编译器警告 -->
<a :['foo' + bar]="value"> ... </a>
```

如果你需要传入一个复杂的动态参数，我们推荐使用[计算属性](https://cn.vuejs.org/guide/essentials/computed.html)替换复杂的表达式

#### 列表渲染

##### 数组变化侦测

###### 变更方法

Vue３ 能够监听响应式数组的变更方法，并在它们被调用时触发相关的更新。

这些变更方法包括：

- `push()`
- `pop()`
- `shift()`
- `unshift()`
- `splice()`
- `sort()`
- `reverse()`

###### 替换一个数组

变更方法，顾名思义，就是会对调用它们的原数组进行变更。

相对地，也有一些不可变 (immutable) 方法，

例如 `filter()`，`concat()` 和 `slice()`，这些都不会更改原数组，而总是**返回一个新数组**。

当遇到的是非变更方法时，我们需要将旧的数组替换为新的：

```js
// `items` 是一个数组的 ref
items.value = items.value.filter((item) => item.message.match(/Foo/))
```

你可能认为这将导致 Vue 丢弃现有的 DOM 并重新渲染整个列表——幸运的是，情况并非如此。

Vue 实现了一些巧妙的方法来最大化对 DOM 元素的重用，

因此用另一个包含部分重叠对象的数组来做替换，仍会是一种非常高效的操作。

###### 展示过滤或排序后的结果

有时，我们希望显示数组经过过滤或排序后的内容，而不实际变更或重置原始数据。

在这种情况下，你可以创建返回已过滤或已排序数组的计算属性。

举例来说：

```js
const numbers = ref([1, 2, 3, 4, 5])

const evenNumbers = computed(() => {
  return numbers.value.filter((n) => n % 2 === 0)
})
```

```vue
<li v-for="n in evenNumbers">{{ n }}</li>
```

在计算属性不可行的情况下 (例如在多层嵌套的 `v-for` 循环中)，你可以使用以下方法：

```js
const sets = ref([
  [1, 2, 3, 4, 5],
  [6, 7, 8, 9, 10]
])

function even(numbers) {
  return numbers.filter((number) => number % 2 === 0)
}
```

template

```vue
<ul v-for="numbers in sets">
  <li v-for="n in even(numbers)">{{ n }}</li>
</ul>
```

**在计算属性中使用 `reverse()` 和 `sort()` 的时候务必小心！**

**这两个方法将变更原始数组，计算函数中不应该这么做。**

请在调用这些方法之前创建一个原数组的副本：

```diff
- return numbers.reverse()
+ return [...numbers].reverse()
```

#### 单向数据流

所有的 props 都遵循着**单向绑定**原则，props 因父组件的更新而变化，自然地将新的状态向下流往子组件，而不会逆向传递。

这避免了子组件意外修改父组件的状态的情况，不然应用的数据流将很容易变得混乱而难以理解。

另外，每次父组件更新后，所有的子组件中的 props 都会被更新到最新值，

这意味着你**不应该**在子组件中去更改一个 prop。

若你这么做了，Vue 会在控制台上向你抛出警告：

```js
const props = defineProps(['foo'])

// ❌ 警告！prop 是只读的！
props.foo = 'bar'
```

导致你想要更改一个 prop 的需求通常来源于以下两种场景：

1. **prop 被用于传入初始值；而子组件想在之后将其作为一个局部数据属性**。

   在这种情况下，最好是新定义一个局部数据属性，从 props 上获取初始值即可：

   ```js
   const props = defineProps(['initialCounter'])
   
   // 计数器只是将 props.initialCounter 作为初始值
   // 像下面这样做就使 prop 和后续更新无关了
   const counter = ref(props.initialCounter)
   ```

2. **需要对传入的 prop 值做进一步的转换**。

   在这种情况中，最好是基于该 prop 值定义一个计算属性：

   

   ```js
   const props = defineProps(['size'])
   
   // 该 prop 变更时计算属性也会自动更新
   const normalizedSize = computed(() => props.size.trim().toLowerCase())
   ```

#### 更改对象 / 数组类型的 props

当对象或数组作为 props 被传入时，虽然子组件无法更改 props 绑定，但仍然可以更改对象或数组内部的值。

这是因为 JavaScript 的对象和数组是按引用传递，而对 Vue 来说，禁止这样的改动虽然可能，但有很大的性能损耗，比较得不偿失。

这种更改的主要缺陷是它允许了子组件以某种不明显的方式影响父组件的状态，可能会使数据流在将来变得更难以理解。

**在大多数场景下，子组件应该[抛出一个事件](https://cn.vuejs.org/guide/components/events.html)来通知父组件做出改变。**

在最佳实践中，你应该尽可能避免这样的更改，除非父子组件在设计上本来就需要紧密耦合。

#### 透传 attribute（非 props 特性， 剩余 props）

**“透传 attribute”指的是传递给一个组件，却没有被该组件声明为 props 或 emits 的 attribute 或者 v-on 事件监听器。**

**最常见的例子就是 class、style 和 id。**

**当一个组件以单个元素为根作渲染时，透传的 attribute 会自动被添加到根元素上。**

举例来说，

**假如我们有一个 `<MyButton>` 组件，它的模板长这样：**

template

```html
<!-- <MyButton> 的模板 -->
<button>click me</button>
```

**一个父组件使用了这个组件，并且传入了 `class`：**

template

```html
<MyButton class="large" />
```

**最后渲染出的 DOM 结果是：**

html

```html
<button class="large">click me</button>
```

**这里，`<MyButton>` 并没有将 `class` 声明为一个它所接受的 prop，**

**所以 `class` 被视作透传 attribute，自动透传到了 `<MyButton>` 的根元素上。**

##### 对 `class` 和 `style` 的合并

**如果一个子组件的根元素已经有了 `class` 或 `style` attribute，它会和从父组件上继承的值合并。**

如果我们将之前的 `<MyButton>` 组件的模板改成这样：

template

```html
<!-- <MyButton> 的模板 -->
<button class="btn">click me</button>
```

则最后渲染出的 DOM 结果会变成：

```html
<button class="btn large">click me</button>
```

##### `v-on` 监听器继承

同样的规则也适用于 `v-on` 事件监听器：

```html
<MyButton @click="onClick" />
```

**组件的 `click` 监听器会被添加到 `<MyButton>` 的根元素，即那个原生的 `<button>` 元素之上。**

**当原生的 `<button>` 被点击，会触发父组件的 `onClick` 方法。**

同样的，**如果原生 `button` 元素自身也通过 `v-on` 绑定了一个事件监听器，则这个监听器和从父组件继承的监听器都会被触发。**

##### 深层组件继承

**有些情况下。一个组件会在根节点上渲染另一个组件。**

**例如，我们重构一下 `<MyButton>`，让它在根节点上渲染 `<BaseButton>`：**

template

```html
<!-- <MyButton/> 的模板，只是渲染另一个组件 -->
<BaseButton />
```

**此时 `<MyButton>` 接收的透传 attribute 会直接继续传给 `<BaseButton>`。**

请注意：

1. **透传的 attribute 不会包含 `<MyButton>` 上声明过的 props 或是针对 `emits` 声明事件的 `v-on` 监听函数，**

   **换句话说，声明过的 props 和  `v-on` 监听函数被 `<MyButton>`“消费”了。**

2. **透传的 attribute 若符合声明，也可以作为 props 传入 `<BaseButton>`。**

##### 禁用 Attributes 继承

**如果你不想要一个组件自动地继承 attribute，你可以在组件选项中设置 `inheritAttrs: false`。**

如果你使用了 `<script setup>`，你需要一个额外的 `<script>` 块来书写这个选项声明：

```vue
<script>
// 使用普通的 <script> 来声明选项
export default {
  inheritAttrs: false
}
</script>

<script setup>
// ...setup 部分逻辑
</script>
```

**最常见的需要禁用 attribute 继承的场景就是 attribute 需要应用在根节点以外的其他元素上。**

**通过设置 `inheritAttrs` 选项为 `false`，你可以完全控制透传进来的 attribute 被如何使用。**

**这些透传进来的 attribute 可以在模板的表达式中直接用 `$attrs` 访问到。**

template

```html
<span>Fallthrough attribute: {{ $attrs }}</span>
```

**这个 `$attrs` 对象包含了除组件所声明的 `props` 和 `emits` 之外的所有其他 attribute，例如 `class`，`style`，`v-on` 监听器等等。**

有几点需要注意：

- **和 props 有所不同，透传 attributes 在 JavaScript 中保留了它们原始的大小写，**

  **所以像 `foo-bar` 这样的一个 attribute 需要通过 `$attrs['foo-bar']` 来访问。**

- **像 `@click` 这样的一个 `v-on` 事件监听器将在此对象下被暴露为一个函数 `$attrs.onClick`。**

现在我们要再次使用一下之前小节中的 `<MyButton>` 组件例子。

有时候我们可能为了样式，需要在 `<button>` 元素外包装一层 `<div>`：

template

```html
<div class="btn-wrapper">
  <button class="btn">click me</button>
</div>
```

**我们想要所有像 `class` 和 `v-on` 监听器这样的透传 attribute 都应用在内部的 `<button>` 上而不是外层的 `<div>` 上。**

**我们可以通过设定 `inheritAttrs: false` 和使用 `v-bind="$attrs"` 来实现：**

template

```html
<div class="btn-wrapper">
  <button class="btn" v-bind="$attrs">click me</button>
</div>
```

小提示：[没有参数的 `v-bind`](https://cn.vuejs.org/guide/essentials/template-syntax.html#dynamically-binding-multiple-attributes) 会将一个对象的所有属性都作为 attribute 应用到目标元素上。

##### 多根节点的 Attributes 继承

**和单根节点组件有所不同，有着多个根节点的组件没有自动 attribute 透传行为。**

**如果 `$attrs` 没有被显式绑定，将会抛出一个运行时警告。**

template

```html
<CustomLayout id="custom-layout" @click="changeValue" />
```

如果 `<CustomLayout>` 有下面这样的多根节点模板，由于 Vue 不知道要将 attribute 透传到哪里，所以会抛出一个警告。

template

```html
<header>...</header>
<main>...</main>
<footer>...</footer>
```

如果 `$attrs` 被显式绑定，则不会有警告：

template

```html
<header>...</header>
<main v-bind="$attrs">...</main>
<footer>...</footer>
```

![img](https://cdn.nlark.com/yuque/0/2022/png/1614731/1669382586986-01d48bc3-9f1e-48fa-bd7b-45fe3cdfa593.png)

##### 在 JavaScript 中访问透传 Attributes

如果需要，你可以在 `<script setup>` 中使用 `useAttrs()` API 来访问一个组件的所有透传 attribute：

```vue
<script setup>
import { useAttrs } from 'vue'

const attrs = useAttrs()
</script>
```

如果没有使用 `<script setup>`，`attrs` 会作为 `setup()` 上下文对象的一个属性暴露：

```js
export default {
  setup(props, ctx) {
    // 透传 attribute 被暴露为 ctx.attrs
    console.log(ctx.attrs)
  }
}
```

需要注意的是，

**虽然这里的 `attrs` 对象总是反映为最新的透传 attribute，但它并不是响应式的 (考虑到性能因素)。**

**你不能通过监听器去监听它的变化。如果你需要响应性，可以使用 prop。**

**或者你也可以使用 `onUpdated()` 使得在每次更新时结合最新的 `attrs` 执行副作用。**

#### v-model 的变化

**在 3.x 中，自定义组件上的 v-model 相当于传递了 modelValue prop 并接收抛出的 update:modelValue 事件：**

```vue
<ChildComponent v-model="pageTitle" />

<!-- 是以下的简写: -->

<ChildComponent
  :modelValue="pageTitle"
  @update:modelValue="pageTitle = $event"
/>
```

若需要更改 model 的名称，现在我们可以为 v-model 传递一个参数，以作为组件内 model 选项的替代：

```vue
<ChildComponent v-model:title="pageTitle" />

<!-- 是以下的简写: -->

<ChildComponent :title="pageTitle" @update:title="pageTitle = $event" />

<!-- 等价于 Vue2 的 .sync -->
<ChildComponent :title.sync="pageTitle" />
```

##### 多个 `v-model` 绑定

我们可以在一个组件上创建多个 `v-model` 双向绑定，每一个 `v-model` 都会同步不同的 prop：

UserName 组件

```html
<UserName
  v-model:first-name="first"
  v-model:last-name="last"
/>
```

UserName 组件内部

```vue
<template>
  <input
    type="text"
    :value="firstName"
    @input="$emit('update:firstName', $event.target.value)"
  />
  <input
    type="text"
    :value="lastName"
    @input="$emit('update:lastName', $event.target.value)"
  />
</template>

<script setup>
  defineProps({
    firstName: String,
    lastName: String
  })

  defineEmits(['update:firstName', 'update:lastName'])
</script>
```

##### v-model 与子传父组件通信 

**方式一  点击事件**

```vue
// 父组件
<template>
  <!-- 2、则父组件需要添加 v-model:name -->
  <ChildComponent v-model:name="title"></ChildComponent>
</template>

<script setup>
  import { ref, watch } from "vue"
  import ChildComponent from './components/ChildComponent.vue'
  let title = ref("旧标题")
  // 4、监听父组件 title 的变化
  watch(title, (newValue, oldValue) => {
    console.log("title 变了", newValue, oldValue); // 打印结果：title 变了 新标题 旧标题
  })
</script>

// 子组件
<template>
  <!-- 方式一 1、点击事件 -->
  <div @click="changeTitle('新标题')">点击</div>
</template>

<script setup>
// 引入需要的方法
import { defineProps, defineEmits, computed } from "vue"
// 定义 props
const props = defineProps({
  name: String,
})
// 2、v-model 规定写法 update:name
const emit = defineEmits(["update:name"])

// 3、点击发射自定义事件，改变父组件传给子组件的 title props
const changeTitle = name => emit("update:name", "点击" + name)
</script>
```

**方式二  computed 里 emit 传给 v-model**

```vue
// 父组件
<template>
  <ChildComponent v-model:name="title"></ChildComponent>
</template>

<script setup>
  import { ref, watch } from "vue"
  import ChildComponent from './components/ChildComponent.vue'
  let title = ref("旧标题")
  watch(title, (newValue, oldValue) => {
    console.log("title 变了", newValue, oldValue); // title 变了 新标题 旧标题
  })
</script>

// 子组件
<template>
  <!-- 方式二 v-model 与 computed 结合 -->
  <input type="text" v-model="myName" />
</template>

<script setup>
  // 引入需要的方法
  import { defineProps, defineEmits, computed } from "vue"
  // 定义 props
  const props = defineProps({
    name: String,
  })
  // v-model 规定写法 update:name，组件需要添加 v-model:name
  const emit = defineEmits(["update:name"])
  // 定义计算属性
  const myName = computed({
    get() {
      return props.name
    },
    set(val) {
      emit("update:name", val)
    },
  })
</script>
```

等价于

```vue
// 父组件
<template>
  <ChildComponent :name="title" @update:name="title = $event" />
</template>

<script setup>
  import { ref, watch } from "vue"
  import ChildComponent from './components/ChildComponent.vue'
  let title = ref("标题")
  watch(title, (newValue, oldValue) => {
    console.log("title 变了", newValue, oldValue);
  })
</script>

// 子组件
<template>
  <input
    type="text"
    :value="name"
    @input="emit('update:name', $event.target.value)"
  > 
</template>

<script setup>
  // 引入需要的方法
  import { defineProps, defineEmits, computed } from "vue"
  // 定义 props
  const props = defineProps({
    name: String,
  })
  // v-model 规定写法 update:name，组件需要添加 v-model:name
  const emit = defineEmits(["update:name"])
</script>
```

**方式三 emit 直接传给 v-model**

**最重要的是 v-model 可以支持多个数据双向绑定，实现多个数据子传父**

**Vue3 的 v-model 可以作为 Vue2 .sync 修饰符的替代，而且允许我们在自定义组件上使用多个 v-model。**

```vue
// Parent.vue
<template>
  <child v-model:key="key" v-model:value="value"></child>
</template>

<script setup>
  import child from "./child.vue"
  import { ref, watch } from "vue"
  const key = ref("1111")
  const value = ref("2222")
  watch([key, value], (newValue, oldValue) => {
    console.log('当前改变了', newValue, oldValue) // 当前改变了 ['新的key', '新的value'] ['1111', '2222']
  })
</script>

// Child.vue
<template>
  <button @click="handlerClick">按钮</button>
</template>

<script setup>
  // 方法一  不适用于 Vue3.2 版本，该版本 useContext() 已废弃
  import { useContext } from "vue"
  const { emit } = useContext()
    
  // 方法二 适用于 Vue3.2版本，不需要引入
  // import { defineEmits } from "vue"
  const emit = defineEmits(["key", "value"])
    
  // 用法
  const handlerClick = () => {
    emit("update:key", "新的key")
    emit("update:value", "新的value")
  }
</script>
```

#### ref

ref：可传入任意类型的值并返回一个响应式且可改变的 ref 对象，

`ref()` 将传入参数的值包装为一个带 `.value` 属性的 ref 对象：

```js
const count = ref(0)

console.log(count) // { value: 0 }
console.log(count.value) // 0

count.value++
console.log(count.value) // 1
```

ref 的 .value 属性也是响应式的。

同时，当值为对象类型时，会用 reactive() 自动转换它的 .value。

返回的 ref 对象拥有一个指向内部值的单一属性 .value ，改变值的时候必须使用其 value 属性。

**Ref 对象的值的读取，切记必须通过 `.value` 读取！**

```ts
// 读取一个字符串
const msg = ref<string>('Hello World!')
console.log(msg.value)

// 读取一个数组
const uids = ref<number[]>([1, 2, 3])
console.log(uids.value[1])
```

**一个包含对象类型值的 ref 可以响应式地替换整个对象：**

```js
const objectRef = ref({ count: 0 })

// 这是响应式的替换
objectRef.value = { count: 1 }
```

**ref 传递给函数或是从一般对象里解构，不会丢失响应性：**

```js
const obj = {
  foo: ref(1),
  bar: ref(2)
}

// 该函数接收一个 ref
// 需要通过 .value 取值
// 但它会保持响应性
callSomeFunction(obj.foo)

// 仍然是响应式的
const { foo, bar } = obj
```

ref 可以持有任何类型的值，包括深层嵌套的对象、数组或者 JavaScript 内置的数据结构，比如 `Map`。

非原始值将通过 [`reactive()`](https://cn.vuejs.org/guide/essentials/reactivity-fundamentals.html#reactive) 转换为响应式代理

ref 会使它的值具有深层响应性。这意味着即使改变嵌套对象或数组时，变化也会被检测到：

```js
import { ref } from 'vue'

const obj = ref({
  nested: { count: 0 },
  arr: ['foo', 'bar']
})

function mutateDeeply() {
  // 以下都会按照期望工作
  obj.value.nested.count++
  obj.value.arr.push('baz')
}
```

**`isRef`用于检查值是否为一个 `ref` 对象。**

ref 包装后的对象（例如下面代码中的 count ）

在 `<script> `中操作数据需要使用 xxx.value 的形式，而 `<template> `模板中不需要添加 .value

一般用来定义基本类型的响应式数据。注意这里说的是一般，并不是说 ref 就不能定义引用类型的响应式数据。

使用 `<script setup>`

**使用 `<script setup>` 所有的 API 需要单独引入，除了 Vue3.2 自动引入的几个 API （编译器宏）外。**

**unplugin-auto-import 是一个按需自动导入Vue/Vue Router 等官方 Api 的插件；作者是 Vite 生态圈大名鼎鼎的 Anthony Fu**

**使用此插件后，不需要手动编写 import { xxx } from vue 这样的代码了，提升开发效率**

```vue
<template>
  <div>
    <h1 @click="add">{{count}}</h1>
  </div>
</template>

<script setup>
import { ref } from "vue";

let count = ref(1)

function add(){
  count.value++
}
</script>

<style>
h1 {
  color: red;
}
</style>
```

todo demo

```vue
<template>
  <form @submit.prevent="addTodo">
    <input v-model="newTodo">
    <button>Add Todo</button>    
  </form>
  <ul>
    <li v-for="todo in todos" :key="todo.id">
      {{ todo.text }}
      <button @click="removeTodo(todo)">X</button>
    </li>
  </ul>
</template>

<script setup>
import { ref } from 'vue'

// 给每个 todo 对象一个唯一的 id
let id = 0

const newTodo = ref('')
const todos = ref([
  { id: id++, text: 'Learn HTML' },
  { id: id++, text: 'Learn JavaScript' },
  { id: id++, text: 'Learn Vue' }
])

function addTodo() {
  todos.value.push({ id: id++, text: newTodo.value })
  newTodo.value = ''
}

function removeTodo(todo) {
  todos.value = todos.value.filter(item => item.id !== todo.id)
}
</script>
```

#### ref 与 ts

在开始使用 API 之前，需要先了解在 TypeScript 中如何声明 Ref 变量的类型。

##### API 本身的类型

先看 API 本身， `ref` API 是一个函数，通过接受一个泛型入参，返回一个响应式对象，

所有的值都通过 `.value` 属性获取，这是 API 本身的 TS 类型：

```ts
// ref API 的 TS 类型
function ref<T>(value: T): Ref<UnwrapRef<T>>

// ref API 的返回值的 TS 类型
interface Ref<T> {
  value: T
}
```

因此在声明变量时，是使用尖括号 `<>` 包裹其 TS 类型，紧跟在 `ref` API 之后：

```ts
// 显式指定 msg.value 是 string 类型
const msg = ref<string>('Hello World!')
```

再回看该 API 本身的类型，其中使用了 `T` 泛型，这表示在传入函数的入参时，

**可以不手动指定其 TS 类型， TypeScript 会根据这个 API 所返回的响应式对象的 `.value` 属性的类型，确定当前变量的类型。**

因此也可以省略显式的类型指定，像下面这样声明变量，其类型交给 TypeScript 去自动推导：

```ts
// TypeScript 会推导 msg.value 是 string 类型
const msg = ref('Hello World')
```

对于声明时会赋予初始值，并且在使用过程中不会改变其类型的变量，是可以省略类型的显式指定的。

**而如果有显式的指定的类型，那么在一些特殊情况下，初始化时可以不必赋值，这样 TypeScript 会自动添加 `undefined` 类型：**

```ts
const msg = ref<string>()
console.log(msg.value) // undefined

msg.value = 'Hello World!'
console.log(msg.value) // Hello World!
```

**因为入参留空时，虽然指定了 `string` 类型，但实际上此时的值是 `undefined` ，**

**因此实际上这个时候的 `msg.value` 是一个 `string | undefined` 的联合类型。**

对于声明时不知道是什么值，在某种条件下才进行初始化的情况，就可以省略其初始值，

**但是切记在调用该变量的时候对 `.value` 值进行有效性判断。**

而如果**既不显式指定类型，也不赋予初始值，那么会被默认为 `any` 类型**，除非真的无法确认类型，否则不建议这么做。

###### 基本类型

对字符串、布尔值等基本类型的定义方式，比较简单：

```ts
// 字符串
const msg = ref<string>('Hello World!')

// 数值
const count = ref<number>(1)

// 布尔值
const isVip = ref<boolean>(false)
```

###### 引用类型

对于对象、数组等引用类型也适用，比如要定义一个对象：

```ts
// 先声明对象的格式
interface Member {
  id: number
  name: string
}

// 在定义对象时指定该类型
const userInfo = ref<Member>({
  id: 1,
  name: 'Tom',
})
```

定义一个普通数组：

```ts
// 数值数组
const uids = ref<number[]>([1, 2, 3])

// 字符串数组
const names = ref<string[]>(['Tom', 'Petter', 'Andy'])
```

定义一个对象数组：

```ts
// 声明对象的格式
interface Member {
  id: number
  name: string
}

// 定义一个对象数组
const memberList = ref<Member[]>([
  {
    id: 1,
    name: 'Tom',
  },
  {
    id: 2,
    name: 'Petter',
  },
])
```

###### DOM 元素与子组件

除了可以定义数据，`ref` 也有熟悉的用途，就是用来挂载节点，也可以挂在子组件上，

也就是对应在 Vue 2 时常用的 `this.$refs.xxx` 获取 DOM 元素信息的作用。

模板部分依然是熟悉的用法，在要引用的 DOM 上添加一个 `ref` 属性：

```vue
<template>
  <!-- 给 DOM 元素添加 ref 属性 -->
  <p ref="msg">请留意该节点，有一个 ref 属性</p>

  <!-- 子组件也是同样的方式添加 -->
  <Child ref="child" />
</template>
```

在 `<script />` 部分有三个最基本的注意事项：

1. **在 `<template />` 代码里添加的 `ref` 属性的值，是对应 `<script />` 里使用 `ref` API 声明的变量的名称；**

2. 请保证**视图渲染完毕后再执行 DOM 或组件的相关操作需要放到生命周期的 `onMounted` 或者 `nextTick` 函数里**，

   这一点在 Vue 2 也是一样；

3. 该 Ref 变量必须 `return` 出去才可以给到 `<template />` 使用，这一点是 Vue 3 生命周期的硬性要求，

   子组件的数据和方法如果要给父组件操作，也要 `return` 出来才可以。（注意：这是之前的写法，非 script setup 写法）

配合上面的 `<template />` ，来看看 `<script />` 部分的具体例子：

```ts
import { defineComponent, onMounted, ref } from 'vue'
import Child from '@cp/Child.vue'

export default defineComponent({
  components: {
    Child,
  },
  setup() {
    // 定义挂载节点，声明的类型详见下方附表
    const msg = ref<HTMLElement>()
    const child = ref<typeof Child>()

    // 请保证视图渲染完毕后再执行节点操作 e.g. `onMounted` / `nextTick`
    onMounted(() => {
      // 比如获取 DOM 的文本
      console.log(msg.value.innerText)

      // 或者操作子组件里的数据
      child.value.isShowDialog = true
    })

    // 必须 `return` 出去才可以给到 `<template />` 使用
    return {
      msg,
      child,
    }
  },
})
```

关于 DOM 和子组件的 TS 类型声明，可参考以下规则：

| 节点类型 | 声明类型                     | 参考文档                                                     |
| :------- | :--------------------------- | :----------------------------------------------------------- |
| DOM 元素 | 使用 HTML 元素接口           | [HTML 元素接口](https://developer.mozilla.org/zh-CN/docs/Web/API/Document_Object_Model#html_元素接口) |
| 子组件   | 使用 typeof 获取子组件的类型 | [typeof 操作符](https://zhuanlan.zhihu.com/p/311150643)      |

###### ref 获取元素

```vue
<template>
  <div>
    <div ref='wrap'>hello vue3.0</div>
    <Article ref='articleComp'></Article>
  </div>
</template>

<script>
import { ref, onMounted } from "vue";
import Article from "./components/Article";

const wrap = ref(null);
const articleComp = ref(null);

onMounted(() => {
  console.log(wrap.value);        // 获取 div 元素
  console.log(articleComp.value); // 获取的 article 组件实例对象    
});
</script>

<style scoped>
</style>
```

在 Vue2.X 中是怎么获取的呢，先在 标签上定义 :ref='XXX' 然后 this.$refs.XXX 来获取

在 Vue3 上使用组合式 API 时，ref 的概念已经是统一的了，

所以还可以用于获取对模板内元素或者组件实例的引用。

**首先需要在标签上添加 ref='xxx'，然后在 setup 中定义一个初始值为 null 的 ref 类型,名字要和标签的 ref 属性一致**

**为了通过组合式 API 获得该模板引用，我们需要声明一个同名的 ref：**

```vue
<template>
  <input ref="input" />
</template>

<script setup>
import { ref, onMounted } from 'vue'

// 声明一个 ref 来存放该元素的引用
// 必须和模板里的 ref 同名
const input = ref(null)

onMounted(() => {
  input.value.focus()
})
</script>
```

**注意，你只可以在组件挂载后才能访问模板引用。**

**如果你想在模板中的表达式上访问 input，在初次渲染时会是 null。这是因为在初次渲染前这个元素还不存在。**

如果你需要监听一个模板引用 ref 的变化，确保考虑到其值为 `null` 的情况：

```js
watchEffect(() => {
  if (input.value) {
    input.value.focus()
  } else {
    // 此时还未挂载，或此元素已经被卸载（例如通过 v-if 控制）
  }
})
```

**使用了 `<script setup>` 的组件是默认私有的：**

**一个父组件无法访问到一个使用了 `<script setup>` 的子组件中的任何东西，除非子组件在其中通过 `defineExpose` 宏显式暴露：**

```vue
<script setup>
import { ref } from 'vue'

const a = 1
const b = ref(2)

defineExpose({
  a,
  b
})
</script>
```

当父组件通过模板引用获取到了该组件的实例时，

得到的实例类型为 `{ a: number, b: number }` ( ref 都会自动解包，和一般的实例一样)。

##### API 返回值的类型

细心的开发者还会留意到 `ref` API 类型里面还标注了一个返回值的 TS 类型：

```ts
interface Ref<T> {
  value: T
}
```

它是代表整个 Ref 变量的完整类型：

- 上文声明 Ref 变量时，提到的 `string` 类型都是指 `msg.value` 这个 `.value` 属性的类型
- 而 `msg` 这个响应式变量，其本身是 `Ref<string>` 类型

如果在开发过程中需要在函数里返回一个 Ref 变量，

那么其 TypeScript 类型就可以这样写（请留意 `Calculator` 里的 `num` 变量的类型）：

```ts
// 导入 ref API
import { ref } from 'vue'
// 导入 ref API 的返回值类型
import type { Ref } from 'vue'

// 声明 useCalculator 函数的返回值类型
interface Calculator {
  // 这里包含了一个 Ref 变量
  num: Ref<number>
  add: () => void
}

// 声明一个 “使用计算器” 的函数
function useCalculator(): Calculator {
  const num = ref<number>(0)

  function add() {
    num.value++
  }

  return {
    num,
    add,
  }
}

// 在执行使用计算器函数时，可以获取到一个 Ref 变量和其他方法
const { num, add } = useCalculator()
add()
console.log(num.value) // 1
```

上面这个简单的例子演示了如何手动指定 Ref 变量的类型，对于逻辑复用时的函数代码抽离、插件开发等场景非常有用！

当然大部分情况下可以交给 TypeScript 自动推导，但掌握其用法，在必要的时候就派得上用场了！

#### ref 解包

解包：无需使用 .value 就可以读写

##### ref 在模板中的解包

**ref 在模板中作为顶层属性被访问时，它们会被自动“解包”，所以不需要使用 .value （包括样式绑定）**

```vue
<template>
  <button @click="increment">
    {{ count }} <!-- 无需 .value -->
  </button>
</template>

<script setup>
import { ref } from 'vue'

const count = ref(0)

function increment() {
  count.value++
}
</script>
```

##### ref 在响应式对象中的解包

当一个 `ref` 被嵌套在一个响应式对象中，作为属性被访问或更改时不需要使用 .value，它会自动解包，因此会表现得和一般的属性一样：

```js
const count = ref(0)
const state = reactive({
  count
})

console.log(state.count) // 0

state.count = 1
console.log(count.value) // 1
```

如果将一个新的 ref 赋值给一个关联了已有 ref 的属性，那么它会替换掉旧的 ref：

```js
// 接着上面的示例
const otherCount = ref(2)

state.count = otherCount
console.log(state.count) // 2
// count.value 读取的是原始（旧的） ref ，原始（旧的） ref 现在已经和 state.count 失去联系，所以还是 1 没有变成 2
console.log(count.value) // 1
```

**只有当嵌套在一个深层响应式对象内时，才会发生 ref 解包。**

##### 数组和集合类型的 ref 不会进行解包

跟响应式对象不同，当 ref 作为响应式数组或像 `Map` 这种原生集合类型的元素被访问时，不会进行解包。

```js
const books = reactive([ref('Vue 3 Guide')])
// 这里需要 .value
console.log(books[0].value)

const map = reactive(new Map([['count', ref(0)]]))
// 这里需要 .value
console.log(map.get('count').value)
```

#### reactive

**`reactive()` 只适用于对象 (包括数组和内置类型，如 `Map` 和 `Set`)**

**reactive 对传入的类型是有限制的，它要求我们必须传入的是一个对象或者数组类型。**

reactive：接受一个普通对象然后返回该普通对象的响应式代理。

实际上该方法的内部就是基于 ES6 的 Proxy 实现的，使得 Vue 能够跟踪对响应式对象属性的访问与更改操作。

总之：reactive 负责把复杂的数据结构包装成响应式，ref  负责把基本的数据结构包装成响应式。

示例一

```vue
<template>
  <div>
    <h2>{{state.count}}</h2>
    <h3>{{num}}</h3>
    <button @click="add">计算</button>
  </div>
</template>

<script>
import { reactive, ref } from "vue";
export default {
  setup() {
    const state = reactive({
      count: 1
    });
    const num = ref(0);
    function add() {
      state.count++;
      num.value+=2
    }
    return { state, add, num };
  }
};
</script>
```

示例二

```vue
<template>
  <h3>信息展示组件</h3>
  <div style="margin: 24px 0">
    <span>姓名：</span>
    <span>{{ data.name }}</span>
    <br />
    <span>年龄：</span>
    <span>{{ data.age }}</span>
  </div>
  <button @click="data.name = 'tom'">修改姓名</button>
  <br />
  <button @click="data.age = '20'">修改年龄</button>
</template>

<script setup>
import { reactive } from 'vue'
// 创建响应式对象
const data = reactive({
  name: 'jack',
  age: '18',
})
</script>
```

#### reactive 与 ts

`reactive` 变量的声明方式没有 `ref` 的变化那么大，基本上和普通变量一样，它的 TS 类型如下：

```ts
function reactive<T extends object>(target: T): UnwrapNestedRefs<T>
```

可以看到其用法还是比较简单的，下面是一个 Reactive 对象的声明方式：

```ts
// 声明对象的类型
interface Member {
  id: number
  name: string
}

// 定义一个对象
const userInfo: Member = reactive({
  id: 1,
  name: 'Tom',
})
```

下面是 Reactive 数组的声明方式：

```ts
const uids: number[] = reactive([1, 2, 3])
```

还可以声明一个 Reactive 对象数组：

```ts
// 对象数组也是先声明其中的对象类型
interface Member {
  id: number
  name: string
}

// 再定义一个为对象数组
const userList: Member[] = reactive([
  {
    id: 1,
    name: 'Tom',
  },
  {
    id: 2,
    name: 'Petter',
  },
  {
    id: 3,
    name: 'Andy',
  },
])
```

#### 响应式代理 vs 原始对象

值得注意的是，`reactive()` 返回的是一个原始对象的 [Proxy](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Proxy)，它和原始对象是不相等的：

```js
const raw = {}
const proxy = reactive(raw)

// 代理对象和原始对象不是全等的
console.log(proxy === raw) // false
```

只有代理对象是响应式的，更改原始对象不会触发更新。因此，使用 Vue 的响应式系统的最佳实践是 **仅使用你声明对象的代理版本**。

为保证访问代理的一致性，

对同一个原始对象调用 `reactive()` 会总是返回同样的代理对象，而对一个已存在的代理对象调用 `reactive()` 会返回其本身：

```js
// 在同一个对象上调用 reactive() 会返回相同的代理
console.log(reactive(raw) === proxy) // true

// 在一个代理上调用 reactive() 会返回它自己
console.log(reactive(proxy) === proxy) // true
```

这个规则对嵌套对象也适用。依靠深层响应性，响应式对象内的嵌套对象依然是代理：

```js
const proxy = reactive({})

const raw = {}
proxy.nested = raw

console.log(proxy.nested === raw) // false
```

#### ref 与 reactive 的区别

**ref 用来定义：基本类型数据。**

ref 通过 Object.defineProperty() 的 get 与 set 来实现响应式（数据劫持）。

**ref 定义的数据：操作数据需要.value，读取数据时模板中直接读取不需要 .value 。**

**reactive 用来定义：对象或数组类型数据。**

reactive 通过使用 Proxy 来实现响应式（数据劫持）, 并通过 Reflect 操作源代码内部的数据。

**reactive 定义的数据：操作数据与读取数据：均不需要 .value 。**

**声明响应式，首先推荐 ref，并且用 const 声明，基本类型修改 value ，**

**引用类型重新赋值也是修改 value ，这样引用类型重新赋值还是响应式的，**

**只有简单的对象和数组，只修改里面内容但不重新赋值的，才使用 reactive。**

**element-plus源码，里面大部分都是用的 ref 声明响应式，只有部分比较确定的用的 reactive。**

#### reactive() 的局限性

`reactive()` API 有两条限制：

1. **仅对对象类型有效（对象、数组和 `Map`、`Set` 这样的[集合类型](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects#使用键的集合对象)），而对 `string`、`number` 和 `boolean` 这样的 [原始类型](https://developer.mozilla.org/zh-CN/docs/Glossary/Primitive) 无效。**

2. 因为 Vue 的响应式系统是通过属性访问进行追踪的，因此我们必须始终保持对该响应式对象的相同引用。

   这意味着我们不可以**随意地“替换”一个响应式对象，这将导致对初始引用的响应性连接丢失：**


   ```js
   let state = reactive({ count: 0 })
   
   // 上面的引用 ({ count: 0 }) 将不再被追踪（响应性连接已丢失！）
   state = reactive({ count: 1 })
   ```

同时这也意味着**当我们将响应式对象的属性重新赋值或解构至本地变量时，或是将该属性传入一个函数时，我们会失去响应性：**

```js
const state = reactive({ count: 0 })

// n 是一个局部变量，同 state.count
// 失去响应性连接
let n = state.count
// 不影响原始的 state
n++

// count 也和 state.count 失去了响应性连接
let { count } = state
// 不会影响原始的 state
count++

// 该函数接收一个普通数字，并且
// 将无法跟踪 state.count 的变化
callSomeFunction(state.count)
```

举个例子，比如要从服务端 API 接口获取翻页数据时，通常要先重置数组，再异步添加数据，如果使用常规的重置，会导致这个变量失去响应性：

```ts
let uids: number[] = reactive([1, 2, 3])

/**
 * 不推荐使用这种方式，会丢失响应性
 * 异步添加数据后，模板不会响应更新
 */
uids = []

// 异步获取数据后，模板依然是空数组
setTimeout(() => {
  uids.push(1)
}, 1000)
```

要让数据依然保持响应性，则必须在关键操作时，不破坏响应性 API ，以下是推荐的操作方式，通过重置数组的 `length` 长度来实现数据的重置：

```ts
const uids: number[] = reactive([1, 2, 3])

/**
 * 推荐使用这种方式，不会破坏响应性
 */
uids.length = 0

// 异步获取数据后，模板可以正确的展示
setTimeout(() => {
  uids.push(1)
}, 1000)
```

#### unref()

如果参数是 ref，则返回内部值，否则返回参数本身。这是 `val = isRef(val) ? val.value : val` 计算的一个语法糖。

**shallowRef()**

[`ref()`](https://cn.vuejs.org/api/reactivity-core.html#ref) 的浅层作用形式。

和 `ref()` 不同，浅层 ref 的内部值将会原样存储和暴露，并且不会被深层递归地转为响应式。只有对 `.value` 的访问是响应式的。

`shallowRef()` 常常用于对大型数据结构的性能优化或是与外部的状态管理系统集成。

示例

```js
const state = shallowRef({ count: 1 })

// 不会触发更改
state.value.count = 2

// 会触发更改
state.value = { count: 2 }
```

这使得对深层级属性的访问变得更快，但代价是，我们现在必须将所有深层级对象视为不可变的，并且只能通过替换整个根状态来触发更新：

```js
const shallowArray = shallowRef([
  /* 巨大的列表，里面包含深层的对象 */
])

// 这不会触发更新...
shallowArray.value.push(newObject)
// 这才会触发更新
shallowArray.value = [...shallowArray.value, newObject]

// 这不会触发更新...
shallowArray.value[0].foo = 1
// 这才会触发更新
shallowArray.value = [
  {
    ...shallowArray.value[0],
    foo: 1
  },
  ...shallowArray.value.slice(1)
]
```

#### toRefs（解构 reactive 不失去响应式）

**`toRef` 和 `toRefs` ，都是用于 `reactive` 向 `ref` 转换。**

关于 `ref` 和 `reactive` 这两个 API 的好处就不重复了，但是在使用的过程中，各自都有不方便的地方：

**`ref` API 虽然在 `<template />` 里使用起来方便，**

**但是在 `<script />` 里进行读取 / 赋值的时候，要一直记得加上 `.value` ，否则 BUG 就来了。**

**`reactive` API 虽然在使用的时候，因为知道它本身是一个对象，所以不会忘记通过 `foo.bar` 这样的格式去操作，**

**但是在 `<template />` 渲染的时候，又因此不得不每次都使用 `foo.bar` 的格式去渲染。**

**那么有没有办法，既可以在编写 `<script />` 的时候不容易出错，在写 `<template />` 的时候又比较简单呢？**

于是， `toRef` 和 `toRefs` 因此诞生。

**使用`reactive`创建的对象，如果想在模板中使用，就必须得使用`xxx.xxx`的形式，**

**如果大量用到的话还是很麻烦的，使用`es6`解构以后，会失去响应式，那么`toRefs`的作用就体现在这。**

**因为`ref` 不会失去响应式。**

如果我们使用 ES6 的解构语法，对 reactive 返回的对象进行解构，

之后无论是修改解构后的变量，还是修改 reactive 返回的 state 对象，数据都不再是响应式的：

```js
const user = reactive({
  name: "Tom",
  age: 18
})

const { name, age } = user
```

toRefs 将响应式对象转换为普通对象，这个普通对象的每个属性都是指向源对象相应属性的 ref。

每个单独的 ref 都是使用 [`toRef()`](https://cn.vuejs.org/api/reactivity-utilities.html#toref) 创建的。

也就是说，将 reactive 返回的对象中的属性都转成了 ref 对象。

这种做法相当于在 user.name 和 ref.value 之间建立了链接，任何一个修改都会引起另外一个变化；

```js
const user = reactive({
  name: "Tom",
  age: 18
})

const { name, age } = toRefs(user)
```

示例

```vue
<template>
  <h2>{{name}}</h2>
</template>

<script setup>
import { reactive, toRefs } from "vue";    
    
const state = reactive({
  name: "Mike",
});
let { name } = toRefs(state);

setInterval(() => {
  name.value += "===";
}, 1000);
</script>
```

##### 类型和基本用法

先看看它的 TS 类型：

```ts
function toRefs<T extends object>(
  object: T
): {
  [K in keyof T]: ToRef<T[K]>
}

type ToRef = T extends Ref ? T : Ref<T>
```

与 `toRef` 不同， `toRefs` 只接收了一个参数，是一个 `reactive` 变量。

```ts
interface Member {
  id: number
  name: string
}

// 声明一个 Reactive 变量
const userInfo: Member = reactive({
  id: 1,
  name: 'Petter',
})

// 传给 `toRefs` 作为入参
const userInfoRefs = toRefs(userInfo)
```

此时这个新的 `userInfoRefs` 变量，它的 TS 类型就不再是 `Member` 了，而应该是：

```ts
// 导入 `toRefs` API 的类型
import type { ToRefs } from 'vue'

// 上下文代码省略...

// 将原来的类型传给 API 的类型
const userInfoRefs: ToRefs<Member> = toRefs(userInfo)
```

也可以重新编写一个新的类型来指定它，因为每个字段都是与原来关联的 Ref 变量，所以也可以这样声明：

```ts
// 导入 `ref` API 的类型
import type { Ref } from 'vue'

// 上下文代码省略...

// 新声明的类型每个字段都是一个 Ref 变量的类型
interface MemberRefs {
  id: Ref<number>
  name: Ref<string>
}

// 使用新的类型进行声明
const userInfoRefs: MemberRefs = toRefs(userInfo)
```

当然实际上日常使用时并不需要手动指定其类型， TypeScript 会自动推导，可以节约非常多的开发工作量。

和 `toRef` API 一样，这个 API 也是可以对数组进行转换：

```ts
const words = reactive(['a', 'b', 'c'])
const wordsRefs = toRefs(words)
```

此时新数组的类型是 `Ref<string>[]` ，不再是原来的 `string[]` 类型。

##### 解构与赋值

转换后的 Reactive 对象或数组支持 ES6 的解构，并且不会失去响应性，因为解构后的每一个变量都具备响应性。

```ts
// 为了提高开发效率，可以直接将 Ref 变量直接解构出来使用
const { name } = toRefs(userInfo)
console.log(name.value) // Petter

// 此时对解构出来的变量重新赋值，原来的变量也可以同步更新
name.value = 'Tom'
console.log(name.value) // Tom
console.log(userInfo.name) // Tom
```

这一点和直接解构 Reactive 变量有非常大的不同，直接解构 Reactive 变量，得到的是一个普通的变量，不再具备响应性。

这个功能在使用 Hooks 函数非常好用（在 Vue 3 里也叫可组合函数， Composable Functions ），还是以一个计算器函数为例，

这一次将其修改为内部有一个 Reactive 的数据状态中心，在函数返回时解构为多个 Ref 变量：

```ts
import { reactive, toRefs } from 'vue'

// 声明 `useCalculator` 数据状态类型
interface CalculatorState {
  // 这是要用来计算操作的数据
  num: number
  // 这是每次计算时要增加的幅度
  step: number
}

// 声明一个 “使用计算器” 的函数
function useCalculator() {
  // 通过数据状态中心的形式，集中管理内部变量
  const state: CalculatorState = reactive({
    num: 0,
    step: 10,
  })

  // 功能函数也是通过数据中心变量去调用
  function add() {
    state.num += state.step
  }

  return {
    // 注意这里  
    ...toRefs(state),
    add,
  }
}
```

这样在调用 `useCalculator` 函数时，可以通过解构直接获取到 Ref 变量，不需要再进行额外的转换工作。

```ts
// 解构出来的 `num` 和 `step` 都是 Ref 变量
const { num, step, add } = useCalculator()
console.log(num.value) // 0
console.log(step.value) // 10

// 调用计算器的方法，数据也是会得到响应式更新
add()
console.log(num.value) // 10
```

#### toRef（只转换普通对象/ reactive 对象中的某一个属性）

##### 基本用法

如果只希望转换 普通对象/ reactive 对象中的某一个属性，可以使用 toRef，示例代码如下：

```js
const name = toRef(user, 'name')
const age = toRef(user, 'age')
```

在读取和赋值时，需要使用 `name.value` 来操作，在重新赋值时会同时更新 `name` 和 `userInfo.name` 的值

```ts
interface Member {
  id: number
  name: string
}

const userInfo: Member = reactive({
  id: 1,
  name: 'Petter',
})

// 修改前先查看初始值
const name = toRef(userInfo, 'name')
console.log(name.value) // Petter
console.log(userInfo.name) // Petter

// 修改 Ref 变量的值，两者同步更新
name.value = 'Tom'
console.log(name.value) // Tom
console.log(userInfo.name) // Tom

// 修改 Reactive 对象上该属性的值，两者也是同步更新
userInfo.name = 'Jerry'
console.log(name.value) // Jerry
console.log(userInfo.name) // Jerry
```

这个 API 也可以接收一个 Reactive 数组，此时第二个参数应该传入数组的下标：

```ts
// 这一次声明的是数组
const words = reactive(['a', 'b', 'c'])

// 通过下标 `0` 转换第一个 item
const a = toRef(words, 0)
console.log(a.value) // a
console.log(words[0]) // a

// 通过下标 `2` 转换第三个 item
const c = toRef(words, 2)
console.log(c.value) // c
console.log(words[2]) // c
```

##### 设置默认值

如果 Reactive 对象上有一个属性本身没有初始值，也可以传递第三个参数进行设置（默认值仅对 Ref 变量有效）：

```ts
interface Member {
  id: number
  name: string
  // 类型上新增一个属性，因为是可选的，因此默认值会是 `undefined`
  age?: number
}

// 声明变量时省略 `age` 属性
const userInfo: Member = reactive({
  id: 1,
  name: 'Petter',
})

// 此时为了避免程序运行错误，可以指定一个初始值
// 但初始值仅对 Ref 变量有效，不会影响 Reactive 字段的值
const age = toRef(userInfo, 'age', 18)
console.log(age.value)  // 18
console.log(userInfo.age) // undefined

// 除非重新赋值，才会使两者同时更新
age.value = 25
console.log(age.value)  // 25
console.log(userInfo.age) // 25
```

数组也是同理，对于可能不存在的下标，可以传入默认值避免项目的逻辑代码出现问题：

```ts
const words = reactive(['a', 'b', 'c'])

// 当下标对应的值不存在时，也是返回 `undefined`
const d = toRef(words, 3)
console.log(d.value) // undefined
console.log(words[3]) // undefined

// 设置了默认值之后，就会对 Ref 变量使用默认值， Reactive 数组此时不影响
const e = toRef(words, 4, 'e')
console.log(e.value) // e
console.log(words[4]) // undefined
```

#### readonly 

readonly 方法通常传入以下类型的参数：

类型一：普通对象；

类型二：reactive 返回的对象；

类型三：ref 返回的对象；

#### isReactive

Vue 中还提供了一个 isReactive，该 API 用于检测是否为 reactive 创建的响应式代理。

#### isRef

检查某个值是否为 ref。

#### 函数的声明和使用

在了解了响应式数据如何使用之后，接下来就要开始了解函数了。

在 Vue 2 ，函数通常是作为当前组件实例上的方法在 `methods` 里声明，

然后再在 `mounted` 等生命周期里调用，或者是在模板里通过 Click 等行为触发，

由于组件内部经常需要使用 `this` 获取组件实例，因此不能使用箭头函数。

```js
export default {
  data: () => {
    return {
      num: 0,
    }
  },
  mounted: function () {
    this.add()
  },
  methods: {
    // 不可以使用 `add: () => this.num++`
    add: function () {
      this.num++
    },
  },
}
```

**在 Vue 3 则灵活了很多，**

**可以使用普通函数、 Class 类、箭头函数、匿名函数等等进行声明，**

**可以将其写在 `setup` 里直接使用，也可以抽离在独立的 `.js` / `.ts` 文件里再导入使用。**

#### computed

在 Vue3 中使用 computed 和 Vue2.X 有些不同，

Vue2 的 computed  是一个对象，里面每个方法定义一个计算属性

Vue3 的 computed 是一个函数或对象

##### 数据的计算

和 Vue 2.0 一样，数据的计算也是使用 `computed` API ，它可以通过现有的响应式数据，去通过计算得到新的响应式变量，

用过 Vue 2.0 的开发者应该不会太陌生，但是在 Vue 3.0 ，在使用方式上也是变化非常大！

> TIP
>
> 这里的响应式数据，可以简单理解为通过 refAPI 、 reactive API 定义出来的数据，
>
> 当然 Vuex 、Vue Router 等 Vue 数据也都具备响应式

##### computed 使用方式

**方式一：接收一个 getter 函数，并根据 getter 函数的返回值返回一个不变的 ref （不可手动修改）对象；**

**方式二：接收一个具有 get 和 set 的对象，返回一个可变（可读写）的 ref 对象；**

和 ref 类似，你可以通过 .`value` 访问计算结果。

> 需要注意的是：
>
> 1. 定义出来的 `computed` 变量，和 Ref 变量的用法一样，也是需要通过 `.value` 才能拿到它的值
> 2. 但是区别在于，默认情况下 `computed` 的 `value` 是只读的

计算属性 ref 也会在模板中自动解包，因此在模板表达式中引用时无需添加 `.value`。

在 Vue 3.0 ，跟其他 API 的用法一样，需要先导入 `computed` 才能使用：

可以把这个用法简单的理解为，传入一个回调函数，并 `return` 一个值，对，它需要有明确的返回值。

```js
import { reactive, ref, computed } from "vue";

// 1.响应式变量声明 reactive 负责复杂数据结构，
const state = reactive({
  count: 1
});
// 2. ref 可以把基本的数据结构包装成响应式
const num = ref(0);
// 3.创建只读的计算属性
const computedEven1 = computed(() => state.count % 2);
// 4.创建可读可写的计算属性
const computedEven2 = computed({
  get: () => {
    return state.count % 2;
  },
  set: newVal => {
    state.count = newVal;
  }
})

// 事件的声明
const add = () => {
  state.count++;
  num.value += 2;
}

const handleClick = () => {
  // 直接修改 computedEven1 会报警告
  computedEven2.value = 10;
}
```

**计算属性值会基于其响应式依赖被缓存**。**一个计算属性仅会在其响应式依赖（即 ref 或 reactive 定义的数据）更新时才重新计算。**

```vue
<script setup>
import { reactive, computed } from 'vue'

const author = reactive({
  name: 'John Doe',
  books: [
    'Vue 2 - Advanced Guide',
    'Vue 3 - Basic Guide',
    'Vue 4 - The Mystery'
  ]
})

// 一个计算属性 ref
const publishedBooksMessage = computed(() => {
  return author.books.length > 0 ? 'Yes' : 'No'
})
</script>

<template>
  <p>Has published books:</p>
  <span>{{ publishedBooksMessage }}</span>
</template>
```

**这意味着只要 `author.books` 不改变，**

**无论多少次访问 `publishedBooksMessage` 都会立即返回先前的计算结果，而不用重复执行 getter 函数。**

这也解释了为什么下面的计算属性永远不会更新，因为 `Date.now()` 并不是一个响应式依赖：

```js
const now = computed(() => Date.now())
```

**相比之下，方法调用（函数调用， 相当于 Vue2 的 methods）总是会在重渲染发生时再次执行函数。**

##### 计算函数不应有副作用

**计算属性的计算函数应只做计算而没有任何其他的副作用，这一点非常重要，请务必牢记。**

举例来说，**不要在计算函数中做异步请求或者更改 DOM **！

**应该使用 [watch ](https://cn.vuejs.org/guide/essentials/watchers.html)根据其他响应式状态的变更来创建副作用**

##### 避免直接修改计算属性值

计算属性的返回值应该被视为只读的，并且永远不应该被更改——应该更新它所依赖的状态来触发新的计算。

##### 应用场景

计算 API 的作用，官网文档只举了一个非常简单的例子，那么在实际项目中，什么情况下用它会让更方便呢？

简单举几个比较常见的例子吧，加深一下对 `computed` 的理解。

###### 数据的拼接和计算

如上面的案例，与其每个用到的地方都要用到 `firstName + ' ' + lastName` 这样的多变量拼接，不如用一个 `fullName` 来的简单。

当然，不止是字符串拼接，数据的求和等操作更是合适，

比如说做一个购物车，购物车里有商品列表，同时还要显示购物车内的商品总金额，这种情况就非常适合用计算数据。

复用组件的动态数据

在一个项目里，很多时候组件会涉及到复用，

比如说：“首页的文章列表 vs 列表页的文章列表 vs 作者详情页的文章列表” ，特别常见于新闻网站等内容资讯站点，

这种情况下，往往并不需要每次都重新写 UI 、数据渲染等代码，仅仅是接口 URL 的区别。

这种情况就可以通过路由名称来动态获取要调用哪个列表接口：

```ts
const route = useRoute()

// 定义一个根据路由名称来获取接口URL的计算数据
const apiUrl = computed(() => {
  switch (route.name) {
    // 首页
    case 'home':
      return '/api/list1'
    // 列表页
    case 'list':
      return '/api/list2'
    // 作者页
    case 'author':
      return '/api/list3'
    // 默认是随机列表
    default:
      return '/api/random'
  }
})

// 请求列表
const getArticleList = async (): Promise<void> => {
  // ...
  articleList.value = await axios({
    method: 'get',
    url: apiUrl.value,
    // ...
  })
  // ...
}
```

当然，这种情况也可以在父组件通过 `props` 传递接口 URL 。

###### 获取多级对象的值

应该很经常的遇到要在 template 显示一些多级对象的字段，

但是有时候又可能存在某些字段不一定有，需要做一些判断的情况，虽然有 `v-if` ，但是嵌套层级一多，模板会难以维护。

如果把这些工作量转移给计算数据，结合 `try / catch` ，这样就无需在 template 里处理很多判断了。

```ts
// 例子比较极端，但在 Vuex 这种大型数据树上，也不是完全不可能存在
const foo = computed(() => {
  // 正常情况下返回需要的数据
  try {
    return store.state.foo3.foo2.foo1.foo
  } catch (e) {
    // 处理失败则返回一个默认值
    return ''
  }
})
```

这样在 template 里要拿到 foo 的值，完全不需要关心中间一级又一级的字段是否存在，只需要区分是不是默认值。

###### 不同类型的数据转换

有时候会遇到一些需求类似于，

让用户在输入框里，按一定的格式填写文本，比如用英文逗号 `,` 隔开每个词，然后保存的时候，是用数组的格式提交给接口。

这个时候 `computed` 的 setter 就可以妙用了，

只需要一个简单的 `computed` ，就可以代替 `input` 的 `change` 事件或者 `watch` 侦听，可以减少很多业务代码的编写。

```vue
<template>
  <input
    type="text"
    v-model="tagsStr"
    placeholder="请输入标签，多个标签用英文逗号隔开"
  />
</template>

<script lang="ts">
import { defineComponent, computed, ref } from 'vue'

export default defineComponent({
  setup() {
    // 这个是最终要用到的数组
    const tags = ref<string[]>([])

    // 因为input必须绑定一个字符串
    const tagsStr = computed({
      // 所以通过getter来转成字符串
      get() {
        return tags.value.join(',')
      },
      // 然后在用户输入的时候，切割字符串转换回数组
      set(newValue: string) {
        tags.value = newValue.split(',')
      },
    })

    return {
      tagsStr,
    }
  },
})
</script>
```

##### 注意事项

在使用上也需要注意一些问题：

1. 只会更新响应式数据的计算

假设要获取当前的时间信息，因为不是响应式数据，所以这种情况下就需要用普通的函数去获取返回值，才能拿到最新的时间。

```ts
const nowTime = computed(() => new Date())
console.log(nowTime.value)
// 输出 Sun Nov 14 2021 21:07:00 GMT+0800 (GMT+08:00)

// 2s 后依然是跟上面一样的结果
setTimeout(() => {
  console.log(nowTime.value)
  // 还是输出 Sun Nov 14 2021 21:07:00 GMT+0800 (GMT+08:00)
}, 2000)
```

2. 数据是只读的

通过 computed 定义的数据，它是只读的，这一点在 [类型声明](https://vue3.chengpeiquan.com/component.html#类型声明) 已经有所了解。

如果直接赋值，不仅无法变更数据，而且会收获一个报错。

```bash
TS2540: Cannot assign to 'value' because it is a read-only property.
```

虽然无法直接赋值，但是在必要的情况下，依然可以通过 `computed` 的 `setter` 来更新数据。

##### setter 的使用

通过 computed 定义的变量默认都是只读的形式（只有一个 getter ），但是在必要的情况下，也可以使用其 setter 属性来更新数据。

###### 基本格式

当需要用到 setter 的时候， `computed` 就不再是一个传入 callback 的形式了，而是传入一个带有 2 个方法的对象。

```ts
// 注意这里computed接收的入参已经不再是函数
const foo = computed({
  // 这里需要明确的返回一个值
  get() {
    // ...
  },
  // 这里接收一个参数，代表修改 foo 时，赋值下来的新值
  set(newValue) {
    // ...
  },
})
```

这里的 `get` 就是 `computed` 的 getter ，跟原来传入 callback 的形式一样，是用于 `foo.value` 的读取，所以这里必须有明确的返回值。

这里的 `set` 就是 `computed` 的 setter ，它会接收一个参数，代表新的值，

当通过 `foo.value = xxx` 赋值的时候，赋入的这个值，就会通过这个入参来传递进来，

可以根据的业务需要，把这个值，赋给相关的数据源。

> TIP
>
> 请注意，必须使用 `get` 和 `set` 这 2 个方法名，也只接受这 2 个方法。

在了解了基本格式后，可以查看下面的例子来了解具体的用法。

###### 使用示范

官网的 [例子](https://cn.vuejs.org/guide/essentials/computed.html#writable-computed) 是一个 Options API 的案例，这里改成 Composition API 的写法来演示：

```ts
// 还是这2个数据源
const firstName = ref<string>('Bill')
const lastName = ref<string>('Gates')

// 这里配合setter的需要，改成了另外一种写法
const fullName = computed({
  // getter还是返回一个拼接起来的全名
  get() {
    return `${firstName.value} ${lastName.value}`
  },
  // setter这里改成只更新firstName，注意参数也定义TS类型
  set(newFirstName: string) {
    firstName.value = newFirstName
  },
})
console.log(fullName.value) // 输出 Bill Gates

// 2s后更新一下数据
setTimeout(() => {
  // 对fullName的赋值，其实更新的是firstName
  fullName.value = 'Petter'

  // 此时firstName已经得到了更新
  console.log(firstName.value) // 会输出 Petter

  // 当然，由于firstName变化了，所以fullName的getter也会得到更新
  console.log(fullName.value) // 会输出 Petter Gates
}, 2000)
```

#### watch

在组合式 API 中，需要在状态变化时执行一些“副作用”：例如更改 DOM，或是根据异步操作的结果去修改另一处的状态，

可以使用 [`watch` 函数](https://cn.vuejs.org/api/reactivity-core.html#watch)在每次响应式状态发生变化时触发回调函数。

- 参数1：要监听的数据
- 参数2：回调函数
- 参数3：配置

这个 API 都是接受三个入参：

|   参数   | 是否可选 | 含义                         |
| :------: | :------: | :--------------------------- |
|  source  |   必传   | 数据源                       |
| callback |   必传   | 监听到变化后要执行的回调函数 |
| options  |   可选   | 一些监听选项                 |

并返回一个可以用来停止监听的函数。

**watch 的第一个参数可以是不同形式的“数据源”：**

**它可以是一个 ref (包括计算属性)、一个响应式对象、一个 getter 函数、或多个数据源组成的数组**

所以要想定义的 watch 能够做出预期的行为，数据源必须具备响应性或者是一个 getter ，

如果只是通过 `let` 定义一个普通变量，然后去改变这个变量的值，这样是无法监听的。

**watch 的第二个参数`callback` 是监听到数据变化时要做出的行为，`callback` 函数有三个参数：**

| 参数      | 作用                               |
| :-------- | :--------------------------------- |
| value     | 变化后的新值，类型和数据源保持一致 |
| oldValue  | 变化前的旧值，类型和数据源保持一致 |
| onCleanup | 注册一个清理函数                   |

```js
const x = ref(0)
const y = ref(0)

// 单个 ref
watch(x, (newX) => {
  console.log(`x is ${newX}`)
})

// getter 函数
watch(
  () => x.value + y.value,
  (sum) => {
    console.log(`sum of x + y is: ${sum}`)
  }
)

// 多个来源组成的数组
watch([x, () => y.value], ([newX, newY]) => {
  console.log(`x is ${newX} and y is ${newY}`)
})
```

##### 监听 ref 定义出来的单个响应式数据

```vue
<template>
  <div class="home">
    <h1>当前数字为:{{num}}</h1>
    <button @click="num++">点击数字加一</button>
  </div>
</template>

<script setup>
import {ref, watch} from 'vue'
let num = ref('0')
watch(num, (newValue, oldValue)=>{
  console.log(`当前数字增加了,${newValue},${oldValue}`)
})
</script>
```

watch 监听多个数据,使用数组

##### 监听 ref 的多个数据，监听数组

```js
watch([num, msg], (newValue, oldValue) => {
  console.log('当前改变了', newValue, oldValue)
})
```

vue2 中 watch 有的其他参数，vue3 中也有，是写在最后的

```js
watch(
  // 数据源改成了数组
  [num, msg], 
  // 回调的入参也变成了数组，每个数组里面的顺序和数据源数组排序一致
  ([newNum, newMsg], [oldNum, oldMsg]) => {
    console.log('当前改变了', newNum, oldMsg)
  }, 
  // options ，可选的一些监听选项
  { immediate: true, deep: true }
)
```

**watch 的第三个参数`options` 是一个对象，它有以下几个选项：**

|   选项    |    类型     | 默认值 |          可选值           | 作用                                 |
| :-------: | :---------: | :----: | :-----------------------: | :----------------------------------- |
|   deep    |   boolean   | false  |       true \| false       | 是否进行深度监听                     |
| immediate |   boolean   | false  |       true \| false       | 是否数据初始化的时候立即执行监听回调 |
|   flush   |   string    | 'pre'  | 'pre' \| 'post' \| 'sync' | 控制监听回调的调用时机               |
|  onTrack  | (e) => void |        |                           | 在数据源被追踪时调用                 |
| onTrigger | (e) => void |        |                           | 在监听回调被触发时调用               |

其中 `onTrack` 和 `onTrigger` 的 `e` 是 debugger 事件，

建议在回调内放置一个 [debugger 语句](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Statements/debugger) 以调试依赖，这两个选项仅在开发模式下生效。

**deep 设置为 `false` 的情况下，如果直接监听一个响应式的 [引用类型](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Data_structures#对象) 数据（e.g. `Object` 、 `Array` … ），**

**虽然它的属性的值有变化，但对其本身来说是不变的，所以不会触发 watch 的 callback 。**

>**deep 默认是 `false` ，但是在监听 reactive 对象或数组时，会默认为 `true`**

**flush**

| 可选值 | 回调的调用时机       | 使用场景                                                     |
| :----: | :------------------- | :----------------------------------------------------------- |
| 'pre'  | 将在渲染前被调用     | 允许回调在模板运行前更新其他值                               |
| 'sync' | 在渲染时被同步调用   | 目前来说没什么好处，可以了解但不建议用…                      |
| 'post' | 被推迟到渲染之后调用 | 如果要通过 ref 操作 [DOM 元素与子组件](https://vue3.chengpeiquan.com/component.html#dom-元素与子组件) ，需要使用这个值来启用该选项，以达到预期的执行效果 |

##### 监听 reactive 定义的响应式数据

**监听 reactive 的响应式数据时，oldValue 无法正确获取，newValue 与 oldValue 一样，都是新的数据。**

**并且 watch 会默认开启深度监视（deep:true），无法关闭。**

```vue
<template>
  <div class="home">
    <h1>当前姓名:{{names.familyName}}</h1>
    <h1>当前年龄:{{names.age}}</h1>
    <h1>当前薪水:{{names.job.salary}}K</h1>
    <button @click="names.familyName+='!'">点击加!</button>
    <button @click="names.age++">点击加一</button>
    <button @click="names.job.salary++">点击薪水加一</button>
  </div>
</template>

<script setup>
import {reactive, watch} from 'vue'

let names = reactive({
  familyName: '郝',
  age: 20,
  job: {
    salary:10
  }
})
watch(names, (newValue, oldValue)=>{
  console.log(`names改变了`, newValue, oldValue)
}, { deep: false })
</script>
```

##### 注意，不能直接监听响应式对象的属性值

这种情况下对象本身是响应式，但它的 property 不是

例如:

```js
const obj = reactive({ count: 0 })

// 错误，因为 watch() 得到的参数是一个 number
watch(obj.count, (count) => {
  console.log(`count is: ${count}`)
})
```

这里**需要用一个返回该属性的 getter 函数**：

```js
// 提供一个 getter 函数
watch(
  () => obj.count,
  (count) => {
    console.log(`count is: ${count}`)
  }
)
```

##### watch 监听对象里的非响应式数据需要使用回调函数的形式

**监听对象的单个属性**

```js
watch(() => names.age, (newValue, oldValue)=>{
  console.log('names改变了', newValue,oldValue)
})
```

**监听对象的多个属性**

```js
watch([() => names.age, () => names.familyName], (newValue, oldValue)=>{
  console.log('names改变了', newValue, oldValue)
})
```

**监听对象的深层属性**

**一个返回响应式对象的 getter 函数，只有在返回不同的对象时，才会触发回调**

**如果  getter 函数监听的是 reactive 定义的响应式数据的属性，并且这个属性是对象，那么我们可以开启深度监听**

```js
// 第一种
watch(() => names.job.salary, (newValue, oldValue)=>{
  console.log('names改变了', newValue, oldValue)
})

// 第二种
watch(() => names.job, (newValue, oldValue)=>{
  console.log('names改变了', newValue, oldValue)
}, {deep: true})
```

直接给 `watch()` 传入一个响应式对象，会隐式地创建一个深层监听器——该回调函数在所有嵌套的变更时都会被触发：

```js
const obj = reactive({ count: 0 })

watch(obj, (newValue, oldValue) => {
  // 在嵌套的属性变更时触发
  // 注意：`newValue` 此处和 `oldValue` 是相等的
  // 因为它们是同一个对象！
})

obj.count++
```

#### 停止监听

**如果在 setup或者 script-setup 里用同步语句使用 watch 的话，**

**组件被卸载的时候也会一起被停止，一般情况下不太需要关心如何停止监听。**

**不过有时候可能想要手动取消， Vue 3 也提供了方法。**

**一个关键点是，监听器必须用同步语句创建：**

**如果用异步回调创建一个监听器，那么它不会绑定到当前组件上，你必须手动停止它，以防内存泄漏。**

**手动停止一个监听器，需要调用 watch / watchEffect 返回的函数：**

```js
const unwatch = watchEffect(() => {})

// ...当该监听器不再需要时
unwatch()
```

如下方这个例子：

```vue
<script setup>
import { watchEffect } from 'vue'

// 它会自动停止
watchEffect(() => {})

// ...这个则不会！
setTimeout(() => {
  watchEffect(() => {})
}, 100)
</script>
```

**注意，需要异步创建监听器的情况很少，请尽可能选择同步创建。**

如果需要等待一些异步数据，你可以使用条件式的监听逻辑：

```js
// 需要异步请求得到的数据
const data = ref(null)

watchEffect(() => {
  if (data.value) {
    // 数据加载后执行某些操作...
  }
})
```

**如果启用了 immediate 选项 ，不能在第一次触发监听回调时执行停止监听。**

```ts
// 注意：这是一段错误的代码，运行会报错
const unwatch = watch(
  message,
  // 监听的回调
  () => {
    // ...
    // 在这里调用会有问题 ❌
    unwatch()
  },
  // 启用 immediate 选项
  {
    immediate: true,
  }
)
```

目前有两种方案可以让实现这个操作：

方案一：使用 `var` 并判断变量类型，利用 [var 的变量提升](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Statements/var#描述) 来实现目的。

```ts
// 这里改成 var ，不要用 const 或 let
var unwatch = watch(
  message,
  // 侦听回调
  () => {
    // 这里加一个判断，是函数才执行它
    if (typeof unwatch === 'function') {
      unwatch()
    }
  },
  // 侦听选项
  {
    immediate: true,
  }
)
```

不过 `var` 已经属于过时的语句了，建议用方案二的 `let` 。

方案二：使用 `let` 并判断变量类型。

```ts
// 如果不想用 any ，可以导入 TS 类型
import type { WatchStopHandle } from 'vue'

// 这里改成 let ，但是要另起一行，先定义，再赋值
let unwatch: WatchStopHandle
unwatch = watch(
  message,
  // 侦听回调
  () => {
    // 这里加一个判断，是函数才执行它
    if (typeof unwatch === 'function') {
      unwatch()
    }
  },
  // 侦听选项
  {
    immediate: true,
  }
)
```

##### 隐式停止监听

watch / watchEffect 在组件的 setup() 函数或生命周期钩子被调用时， 

watch / watchEffect 会被链接到该组件的生命周期，在组件卸载时会自动停止

##### 显式停止监听

我们可以获取 watchEffect 的返回值（一个函数），调用该函数即可

```js
const stop = watchEffect(() => {
  console.log("watchEffect 执行~", name.value, age.value)
})

const changeAge = () => {
  age.value++;
  if (age.value > 20) {
    // 停止监听
    stop()
  }
}
```

##### watch 的第三个参数

 `onCleanup` ，它可以帮注册一个清理函数。

有时 watch 的回调会执行异步操作，当 watch 到数据变更的时候，需要取消这些操作，这个函数的作用就用于此，会在以下情况调用这个清理函数：

- watcher 即将重新运行的时候
- watcher 被停止（组件被卸载或者被手动停止监听 ）

```ts
let unwatch: WatchStopHandle
unwatch = watch(
  message,
  (newValue, oldValue, onCleanup) => {
    // 需要在停止侦听之前注册好清理行为
    onCleanup(() => {
      console.log('侦听清理ing')
      // 根据实际的业务情况定义一些清理操作 ...
    })
    // 然后再停止侦听
    if (typeof unwatch === 'function') {
      unwatch()
    }
  },
  {
    immediate: true,
  }
)
```

#### watchEffect 

`watch()` 是懒执行的：仅当数据源变化时，才会执行回调。但在某些场景中，我们希望在创建监听器时，立即执行一遍回调。

**watchEffect 相当于默认自动开启 immediate:true 的 watch**

**用到了谁就监听谁，监听的值发生了改变就重新调用一次传入的函数（副作用函数）。**

**回调函数中使用的哪些响应式数据就监听哪些响应式数据，不用直接指定要监视的数据，监听数据发生变化时就会执行回调。**

**理论上 `watchEffect` 是 `watch` 的一个简化操作，可以用来代替批量监听 ，但它们也有一定的区别：**

1. **`watch` 可以访问监听状态变化前后的值，而 `watchEffect` 没有。**
2. **`watch` 是在属性改变的时候才执行，而 `watchEffect` 则默认会执行一次，然后在属性改变的时候也会执行。**

watchEffect 有点像 computed （参数为匿名函数时），但是 computed 要写返回值，而 watchEffect 不用写返回值。

```js
watchEffect(() => {
  const one = num.value
  const tow = person.age
  console.log('watchEffect执行了')
})
```

举例来说，我们想请求一些初始数据，然后在相关状态更改时重新请求数据。我们可以这样写：

```js
const url = ref('https://...')
const data = ref(null)

async function fetchData() {
  const response = await fetch(url.value)
  data.value = await response.json()
}

// 立即获取
fetchData()
// ...再监听 url 变化
watch(url, fetchData)
```

而`watchEffect()` 可以立即执行一遍回调函数，如果这时函数产生了副作用，Vue 会自动追踪副作用的依赖关系，自动分析出响应源。

所以上面的例子可以重写为：

```js
watchEffect(async () => {
  const response = await fetch(url.value)
  data.value = await response.json()
})
```

这个例子中，回调会立即执行。

在执行期间，它会自动追踪 `url.value` 作为依赖（和计算属性的行为类似）。

每当 `url.value` 变化时，回调会再次执行。

```vue
<!--
这个示例会通过 GitHub 的 API 获取最新的 Vue.js 提交信息并将其展示为列表。
你可以在两个分支之间切换。
-->
<template>
  <h1>Latest Vue Core Commits</h1>
  <template v-for="branch in branches">
    <input type="radio"
      :id="branch"
      :value="branch"
      name="branch"
      v-model="currentBranch">
    <label :for="branch">{{ branch }}</label>
  </template>
  <p>vuejs/vue@{{ currentBranch }}</p>
  <ul>
    <li v-for="{ html_url, sha, author, commit } in commits">
      <a :href="html_url" target="_blank" class="commit">{{ sha.slice(0, 7) }}</a>
      - <span class="message">{{ truncate(commit.message) }}</span><br>
      by <span class="author">
        <a :href="author.html_url" target="_blank">{{ commit.author.name }}</a>
      </span>
      at <span class="date">{{ formatDate(commit.author.date) }}</span>
    </li>
  </ul>
</template>

<script setup>
import { ref, watchEffect } from 'vue'

const API_URL = `https://api.github.com/repos/vuejs/core/commits?per_page=3&sha=`
const branches = ['main', 'v2-compat']

const currentBranch = ref(branches[0])
const commits = ref(null)

watchEffect(async () => {
  // 该 effect 会立即运行，
  // 并且在 currentBranch.value 改变时重新运行
  const url = `${API_URL}${currentBranch.value}`
  commits.value = await (await fetch(url)).json()
})

function truncate(v) {
  const newline = v.indexOf('\n')
  return newline > 0 ? v.slice(0, newline) : v
}

function formatDate(v) {
  return v.replace(/T|Z/g, ' ')
}
</script>

<style>
a {
  text-decoration: none;
  color: #42b883;
}
li {
  line-height: 1.5em;
  margin-bottom: 20px;
}
.author,
.date {
  font-weight: bold;
}
</style>
```

<img src="https://cdn.nlark.com/yuque/0/2023/gif/1614731/1675329871134-c7f7f4bb-4f66-4133-8986-3469bb4bfa20.gif" alt="img" style="zoom: 67%;" />

##### 清除副作用

**副作用函数也会传入一个清理回调作为参数，和 watch 的一样用法**

开发中我们需要在监听函数中执行网络请求，

但是在网络请求还没有达到的时候，我们停止了监听器，或者监听函数被再次执行了。

那么上一次的网络请求应该被取消掉，这个时候我们就可以清除上一次的副作用。

watchEffect 传入的函数被回调时，其实可以获取到一个参数：onInvalidate

**当副作用即将重新执行或者监听器被停止时会执行 onInvalidate 传入的回调函数（清理函数）**

```js
const stopWatch = watchEffect((onInvalidate) =>{
  console.log("watchEffect 执行", name.value, age.value);
  const timer = setTimeout(() => {
    console.log("2s 后执行的操作")
  }, 2000);
  onInvalidate(() => {
    clearTimeout(timer)
  });
})
```

执行数据请求时，副作用函数往往是一个异步函数：

清理函数必须要在 Promise 被 resolve 之前被注册。

```js
const data = ref(null)
watchEffect(async onInvalidate => {
  onInvalidate(() => {
    /* ... */
  }) // 我们在Promise解析之前注册清除函数
  data.value = await fetchData(props.id)
})
```

##### watchEffect 的执行时机

**默认情况下，组件的更新会在 watchEffect 执行之前**

```js
const titleRef = ref(null)
const counter = ref(0)

watchEffect(() => {
  cosnole.log(titleRef.value)
})
```

结果会打印：

null 和 `<h2></h2>`

这是因为 setup 函数在执行时就会立即执行传入的副作用函数，这个时候 DOM 并没有挂载，所以打印为 null；

而当 DOM 挂载时，会给 title 的 ref 对象赋值新的值，副作用函数会再次执行，打印出来对应的元素；

当你更改了响应式状态，它可能会同时触发 Vue 组件更新和监听器回调。

默认情况下，用户创建的监听器回调，都会在 Vue 组件更新之前被调用。

这意味着你在监听器回调中访问的 DOM 将是被 Vue 更新之前的状态。

**挂载之前立即执行传入的副作用函数 => 挂载之后副作用函数再次执行 => 数据变化，组件更新之前副作用函数再次执行**

##### 调整 watchEffect 的执行时机

**第二个参数传入带有 flush 属性的对象**

**默认值为 pre 表示在组件挂载或者更新之前执行**

**值为 post 表示在组件更新后触发，这样你就可以访问更新的 DOM。**

注意：这也将推迟副作用的初始运行，直到组件的首次渲染完成。

```js
let h2ElContent = null

watchEffect(() => {
  h2ElContent = titleRef.value && titleRef.value.textContent
  console.log(h2ElContent , counter.value)
}, {
  flush: "post"
})
```

后置刷新的 `watchEffect()` 有个更方便的别名 `watchPostEffect()`：

```js
import { watchPostEffect } from 'vue'

watchPostEffect(() => {
  /* 在 Vue 更新后执行 */
})
```

**watchEffect 与 watch 两者的区别**

watchEffect 可以自动收集响应式数据的依赖；

watch 需要手动指定监听的数据源；

`watch` 和 `watchEffect` 都能响应式地执行有副作用的回调。它们之间的主要区别是追踪响应式依赖的方式：

- `watch` 只追踪明确监听的数据源。它不会追踪任何在回调中访问到的东西。

  另外，仅在数据源确实改变时才会触发回调。

  `watch` 会避免在发生副作用时追踪依赖，因此，我们能更加精确地控制回调函数的触发时机。

- `watchEffect`，则会在副作用发生期间追踪依赖。

  它会在同步执行过程中，自动追踪所有能访问到的响应式属性。

  这更方便，而且代码往往更简洁，但有时其响应性依赖关系会不那么明确。

**对比 watch API ，watchEffect 不支持 deep 和 immediate ，请记住这一点，其他的用法是一样的。**

**watchEffect 也有 flush 选项，onTrack 和 onTrigger 选项**

#### 生命周期

当调用 `onMounted` 时，Vue 会自动将回调函数注册到当前正被初始化的组件实例上。

这意味着这些钩子应当在组件初始化时被**同步**注册。例如，请不要这样做：

```js
setTimeout(() => {
  onMounted(() => {
    // 异步注册时当前组件实例已丢失
    // 这将不会正常工作
  })
}, 100)
```

注意这并不意味着对 `onMounted` 的调用必须放在 `setup()` 或 `<script setup>` 内的词法上下文中。

**`onMounted()` 也可以在一个外部函数中调用，只要调用栈是同步的，且最终起源自 `setup()` 就可以。**

<img src="https://cn.vuejs.org/assets/lifecycle.16e4c08e.png" alt="a8f719bf10544f109cd10e5ba6adbcda.jpg" style="zoom: 50%;" />

简单理解：

<img src="https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1655900795012-630f582e-5c2c-4083-ba62-2f6770330710.jpeg" alt="a8f719bf10544f109cd10e5ba6adbcda.jpg"  />

﻿可以使用直接导入的 onX 函数注册生命周期钩子；

| 选项式 API    | setup 钩子函数  | 触发时机         |
| ------------- | --------------- | ---------------- |
| beforeMount   | onBeforeMount   | 组件挂载之前触发 |
| mounted       | onMounted       | 组件挂载后触发   |
| beforeUpdate  | onBeforeUpdate  | 组件更新之前触发 |
| updated       | onUpdated       | 组件更新后触发   |
| beforeUnmount | onBeforeUnmount | 组件卸载之前触发 |
| unmounted     | onUnmounted     | 组件卸载后触发   |

setup 是围绕 beforeCreate 和 created 生命周期钩子运行的，所以不需要显式地定义它们。

Vue 有一个全局组件 `<KeepAlive />` ，用于在多个组件间动态切换时缓存被移除的组件实例，

当组件被包含在 `<KeepAlive />` 组件里时，会多出两个生命周期钩子函数：

| 选项式 API  | setup 钩子函数 | 触发时机                     |
| :---------- | :------------- | :--------------------------- |
| activated   | onActivated    | 被激活时执行                 |
| deactivated | onDeactivated  | 切换组件后，原组件消失前执行 |

以下是几个生命周期的执行顺序对比：

```ts
import { defineComponent, onBeforeMount, onMounted } from 'vue'

export default defineComponent({
  setup() {
    console.log(1)

    onBeforeMount(() => {
      console.log(2)
    })

    onMounted(() => {
      console.log(3)
    })

    console.log(4)
  },
})
```

最终将按照生命周期的顺序输出：

```js
// 1
// 4
// 2
// 3
```

#### DOM 更新时机

当你更改响应式状态后，DOM 会自动更新。然而，你得注意 DOM 的更新并不是同步的。

相反，Vue 将缓冲它们直到更新周期的 “下个时机” 以确保无论你进行了多少次状态更改，每个组件都只需要更新一次。

若要等待一个状态改变后的 DOM 更新完成，你可以使用 [nextTick()](https://cn.vuejs.org/api/general.html#nexttick) 这个全局 API：

```js
import { nextTick } from 'vue'

function increment() {
  state.count++
  nextTick(() => {
    // 访问更新后的 DOM
  })
}
```

#### Provide / Inject 

**提供数据与注入数据实现跨层级组件通信**

**某个深层的子组件需要一个较远的祖先组件中的部分数据。**

**在这种情况下，如果仅使用 props 则必须将其沿着组件链逐级传递下去，这会非常麻烦，这一问题被称为“prop 逐级透传”：**

<img src="https://cdn.nlark.com/yuque/0/2022/png/1614731/1670158426321-0303894c-5c9d-4e7a-8d18-97348f8c1e44.png" alt="prop-drilling.11201220.png" style="zoom: 50%;" />

**provide 和 inject 可以帮助我们解决这一问题**

**一个父组件相对于其所有的后代组件，会作为依赖提供者。**

**任何后代的组件树，无论层级有多深，都可以注入由父组件提供给整条链路的依赖。**

<img src="https://cdn.nlark.com/yuque/0/2022/png/1614731/1670158426347-0acff196-5484-40d6-ad02-c4569e8deb89.png" alt="provide-inject.3e0505e4.png" style="zoom:50%;" />

两者都只能在当前组件的 setup() 中调用

provide 函数定义每个 property。

**provide 函数通过两个参数定义 property：**

**name 提供数据的属性名，可以是一个字符串或是一个 `Symbol`；**

**value 提供数据的属性值，值可以是任意类型，包括响应式的状态，比如一个 ref 。**

**inject 函数可以传入两个参数：**

**要 inject 的 property 的 name；**

**默认值 (可选，没有 provide 情况下的默认值)。**

```js
// 祖先组件
import { ref, reactive, provide } from 'vue'

let counter = ref(100)
const info = reactive({
  title: 'Vue3'
  date: 'provide'
})
// 提供数据 
provide('counter', counter)
provide('info', info)
```

```vue
// 后代组件
<template>
  <h2>孙子组件: {{counter}} - {{info}}</h2>
</template>

<script setup>
  import { inject } from 'vue'
  const counter = inject('counter')
  const info = inject('counter')
</script>
```

**当提供 / 注入响应式的数据时，建议尽可能将任何对响应式状态的变更都保持在供给方（使用 provide 的）组件中。**

```vue
<!-- 在供给方组件内 -->
<script setup>
import { provide, ref } from 'vue'

const location = ref('North Pole')

function updateLocation() {
  location.value = 'South Pole'
}

provide('location', {
  location,
  updateLocation
})
</script>
```

```vue
<!-- 在注入方组件 -->
<script setup>
import { inject } from 'vue'

const { location, updateLocation } = inject('location')
</script>

<template>
  <button @click="updateLocation">{{ location }}</button>
</template>
```

**如果你想确保提供的数据不能被注入方的组件更改，你可以使用 [`readonly()`](https://cn.vuejs.org/api/reactivity-core.html#readonly) 来包装提供的值。**

```vue
<script setup>
import { ref, provide, readonly } from 'vue'

const count = ref(0)
provide('read-only-count', readonly(count))
</script>
```

**使用 Symbol 作注入名**

如果你正在构建大型的应用，包含非常多的依赖提供，

或者你正在编写提供给其他开发者使用的组件库，建议最好使用 Symbol 来作为注入名以避免潜在的冲突。

通常推荐在一个单独的文件中导出这些注入名 Symbol：

```js
// keys.js
export const myInjectionKey = Symbol()
```

```js
// 在供给方组件中
import { provide } from 'vue'
import { myInjectionKey } from './keys.js'

provide(myInjectionKey, { /*
  要提供的数据
*/ });
```

```js
// 注入方组件
import { inject } from 'vue'
import { myInjectionKey } from './keys.js'

const injected = inject(myInjectionKey)
```

#### 异步组件

**大型项目中，我们可能需要把应用拆分为更小的块，仅在需要时再从服务器加载相关组件。**

**Vue 提供了 [`defineAsyncComponent`](https://cn.vuejs.org/api/general.html#defineasynccomponent) 方法来实现此功能：**

```js
import { defineAsyncComponent } from 'vue'

const AsyncComp = defineAsyncComponent(() => {
  return new Promise((resolve, reject) => {
    // ...从服务器获取组件
    resolve(/* 获取到的组件 */)
  })
})
// ... 像使用其他一般组件一样使用 `AsyncComp`
```

`defineAsyncComponent` 方法接收一个返回 Promise 的加载函数。

这个 Promise 的 `resolve` 回调方法应该在从服务器获得组件定义时调用。

也可以调用 `reject(reason)` 表明加载失败。

**[ES 模块动态导入](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/import)也会返回一个 Promise，所以多数情况下会将它和 `defineAsyncComponent` 搭配使用。**

类似 Vite 和 Webpack 这样的构建工具也支持此语法 (并且会将它们作为打包时的代码分割点)，

因此也可以用它来导入 Vue 单文件组件：

```js
import { defineAsyncComponent } from 'vue'

const AsyncComp = defineAsyncComponent(() =>
  import('./components/MyComponent.vue')
)
```

**最后得到的 `AsyncComp` 是一个外层包装过的组件，仅在页面需要它渲染时才会调用加载内部实际组件的函数。**

**它会将接收到的 props 和插槽传给内部组件，所以你可以使用这个异步的包装组件无缝地替换原始组件，同时实现延迟加载。**

与普通组件一样，异步组件可以使用 `app.component()` [全局注册](https://cn.vuejs.org/guide/components/registration.html#global-registration)：

```js
app.component('MyComponent', defineAsyncComponent(() =>
  import('./components/MyComponent.vue')
))
```

也可以直接在父组件中直接定义它们：

```vue
<script setup>
import { defineAsyncComponent } from 'vue'

const AdminPage = defineAsyncComponent(() =>
  import('./components/AdminPageComponent.vue')
)
</script>

<template>
  <AdminPage />
</template>
```

##### 加载与错误状态

异步操作不可避免地会涉及到加载和错误状态，因此 `defineAsyncComponent()` 也支持在高级选项中处理这些状态：

```js
const AsyncComp = defineAsyncComponent({
  // 加载函数
  loader: () => import('./Foo.vue'),

  // 加载异步组件时使用的组件
  loadingComponent: LoadingComponent,
  // 展示加载组件前的延迟时间，默认为 200ms
  delay: 200,

  // 加载失败后展示的组件
  errorComponent: ErrorComponent,
  // 如果提供了一个 timeout 时间限制，并超时了
  // 也会显示这里配置的报错组件，默认值是：Infinity
  timeout: 3000
})
```

如果提供了一个加载组件，它将在内部组件加载时先行显示。

在加载组件显示之前有一个默认的 200ms 延迟——

这是因为在网络状况较好时，加载完成得很快，加载组件和最终组件之间的替换太快可能产生闪烁，反而影响用户感受。

如果提供了一个报错组件，则它会在加载器函数返回的 Promise 抛错时被渲染。

你还可以指定一个超时时间，在请求耗时超过指定时间时也会渲染报错组件。

#### 深度操作符

使用 scoped 后，父组件的样式将不会渗透到子组件中，但也不能直接修改子组件的样式。

如果确实需要进行修改子组件的样式，必须通过 `::v-deep`（完整写法） 或者 `:deep`（快捷写法） 操作符来实现。

> TIP
>
> 1. 旧版的深度操作符是 `>>>` 、 `/deep/` 和 `::v-deep`，现在 `>>>` 和 `/deep/` 已进入弃用阶段（虽然暂时还没完全移除）
> 2. 同时需要注意的是，旧版 `::v-deep` 的写法是作为组合器的方式，写在样式或者元素前面，如：`::v-deep .class-name { /* ... */ }`，现在这种写法也废弃了。

现在不论是 `::v-deep` 还是 `:deep` ，使用方法非常统一，来假设 .b 是子组件的样式名：

```vue
<style scoped>
.a :deep(.b) {
  /* ... */
}
</style>
```

编译后：

```css
.a[data-v-f3f3eg9] .b {
  /* ... */
}
```

> TIP
>
> 可以看到，新的 deep 写法是作为一个类似 JS “函数” 那样去使用，需要深度操作的样式或者元素名，作为 “入参” 去传入。

同理，如果使用 Less 或者 Stylus 这种支持嵌套写法的预处理器，也是可以这样去深度操作的：

```less
.a {
  :deep(.b) {
    /* ... */
  }
}
```

另外，除了操作子组件的样式，那些通过 `v-html` 创建的 DOM 内容，也不受作用域内的样式影响，也可以通过深度操作符来实现样式修改。

## 更多 API 参考

https://cn.vuejs.org/api/

## 与 Vue2 option API 对比

data：data 变成了 ref() 和 reactive()

methods ：从 option api 需要在 methods 对象中进行方法声明 ，变成可以一个个的 function 函数声明

computed：从对象变成可以接收一个 getter 回调函数或一个有 getter 和 setter 对象为参数的函数

watch: 从对象变成可以接受 3 个参数的函数 watch(source,  callback， options )

v-model 变成了 .sync ，可以配合 emit 进行子传父通信

生命周期函数前面都要加 on

统一使用 :deep 操作符

## hook 函数

Composition API 可以让我们更好地组织代码结构，

`<script setup>`本质上是以一种更精简的方式来书写 Composition API 。

随着功能越来越复杂，script 内部的代码也会越来越多。

因此，我们可以进一步对代码进行拆分，把功能独立的模块封装成一个独立的函数，真正做到按需拆分。

在 Composition API 的语法中，所有的功能都是通过全局引入的方式使用的，

并且通过 `<script setup>` 的功能，

我们定义的变量、函数和引入的组件，都不需要额外的生命周期，就可以直接在模板中使用。

### 什么是“组合式函数”？

**“组合式函数”(Composables) 是一个利用 Vue 的组合式 API 来封装和复用有状态逻辑的函数。**

构建前端应用时，我们常常需要复用公共任务的逻辑。

**例如为了在不同地方格式化时间，我们可能会抽取一个可复用的日期格式化函数。**

**这个函数封装了无状态的逻辑：它在接收一些输入后立刻返回所期望的输出。**

**复用无状态逻辑的库有很多，比如你可能已经用过的 [lodash](https://lodash.com/) 或是 [date-fns](https://date-fns.org/)。**

相比之下，**有状态逻辑负责管理会随时间而变化的状态，组合式函数侧重于有状态的逻辑。**

一个简单的例子是跟踪当前鼠标在页面中的位置。

**“组合式函数” 与在组件中的核心逻辑完全一致，做的只是把它移到一个外部函数中去，并返回需要暴露的状态。**

#### 鼠标跟踪器

```js
// mouse.js
import { ref, onMounted, onUnmounted } from 'vue'

// 按照惯例，组合式函数名以“use”开头
export function useMouse() {
  // 被组合式函数封装和管理的状态
  const x = ref(0)
  const y = ref(0)

  // 组合式函数可以随时更改其状态。
  function update(event) {
    x.value = event.pageX
    y.value = event.pageY
  }

  // 一个组合式函数也可以挂靠在所属组件的生命周期上
  // 启动和卸载副作用
  onMounted(() => window.addEventListener('mousemove', update))
  onUnmounted(() => window.removeEventListener('mousemove', update))

  // 通过返回值暴露所管理的状态
  return { x, y }
}
```

下面是它在组件中使用的方式：

```vue
<script setup>
import { useMouse } from './mouse.js'

const { x, y } = useMouse()
</script>

<template>Mouse position is at: {{ x }}, {{ y }}</template>
```

**还可以嵌套多个组合式函数：一个组合式函数可以调用一个或多个其他的组合式函数。**

**这也正是为什么这一设计模式的 API 集合命名为组合式 API。**

举例来说，我们可以将添加和清除 DOM 事件监听器的逻辑也封装进一个组合式函数中：

```js
// event.js
import { onMounted, onUnmounted } from 'vue'

export function useEventListener(target, event, callback) {
  // 如果你想的话，
  // 也可以用字符串形式的 CSS 选择器来寻找目标 DOM 元素
  onMounted(() => target.addEventListener(event, callback))
  onUnmounted(() => target.removeEventListener(event, callback))
}
```

有了它，之前的 `useMouse()` 组合式函数可以被简化为：

```js
// mouse.js
import { ref } from 'vue'
import { useEventListener } from './event'

export function useMouse() {
  const x = ref(0)
  const y = ref(0)

  useEventListener(window, 'mousemove', (event) => {
    x.value = event.pageX
    y.value = event.pageY
  })

  return { x, y }
}
```

> 每一个调用 `useMouse()` 的组件实例会创建其独有的 `x`、`y` 状态拷贝，因此他们不会互相影响。

#### 修改浏览器的小图标 favicon

我们可以在 loading 状态下，去修改浏览器的小图标 favicon。

和本地存储类似，修改 favicon 时，我们需要找到 head 中有 icon 属性的标签。

在下面的代码中，我们把对图标的对应修改的操作封装成了 useFavicon 函数，

并且通过 ref 和 watch 的包裹，把小图标变成了响应式数据。

```js
import { ref, watch } from 'vue'

export default function useFavicon(newIcon) {
    const favicon = ref(newIcon)
    const updateIcon = (icon) => {
      document.head
        .querySelectorAll(`link[rel*="icon"]`)
        .forEach(el => el.href = `${icon}`)
    }
    const reset = () => favicon.value = '/favicon.ico'
    watch(favicon,
      (i) => {
        updateIcon(i)
      }
    )
    return {favicon, reset}
  } 
```

```vue
<template>
  <button @click="loading">123</button>
</template>  

 <script setup>
 import useFavicon from './utils/favicon'
 let { favicon }  = useFavicon()
 function loading() {
   favicon.value = '/geek.png'
 }
</script>
```

#### 异步状态示例

`useMouse()` 组合式函数没有接收任何参数，看一个需要接收一个参数的组合式函数示例。

在做异步数据请求时，我们常常需要处理不同的状态：加载中、加载成功和加载失败。

```vue
<script setup>
import { ref } from 'vue'

const data = ref(null)
const error = ref(null)

fetch('...')
  .then((res) => res.json())
  .then((json) => (data.value = json))
  .catch((err) => (error.value = err))
</script>

<template>
  <div v-if="error">Oops! Error encountered: {{ error.message }}</div>
  <div v-else-if="data">
    Data loaded:
    <pre>{{ data }}</pre>
  </div>
  <div v-else>Loading...</div>
</template>
```

如果在每个需要获取数据的组件中都要重复这种模式，那就太繁琐了。

把它抽取成一个组合式函数：

```js
// fetch.js
import { ref } from 'vue'

export function useFetch(url) {
  const data = ref(null)
  const error = ref(null)

  fetch(url)
    .then((res) => res.json())
    .then((json) => (data.value = json))
    .catch((err) => (error.value = err))

  return { data, error }
}
```

现在在组件里只需要：

```vue
<script setup>
import { useFetch } from './fetch.js'

const { data, error } = useFetch('...')
</script>
```

`useFetch()` 接收一个静态的 URL 字符串作为输入，所以它只执行一次请求，然后就完成了。

但如果想让它在每次 URL 变化时都重新请求呢？

那我们可以让它同时允许接收 ref 作为参数：

```js
// fetch.js
import { ref, isRef, unref, watchEffect } from 'vue'

export function useFetch(url) {
  const data = ref(null)
  const error = ref(null)

  function doFetch() {
    // 在请求之前重设状态...
    data.value = null
    error.value = null
    // unref() 解包可能为 ref 的值
    fetch(unref(url))
      .then((res) => res.json())
      .then((json) => (data.value = json))
      .catch((err) => (error.value = err))
  }

  if (isRef(url)) {
    // 若输入的 URL 是一个 ref，那么启动一个响应式的请求
    watchEffect(doFetch)
  } else {
    // 否则只请求一次
    // 避免监听器的额外开销
    doFetch()
  }

  return { data, error }
}
```

这个版本的 `useFetch()` 现在同时可以接收静态的 URL 字符串和 URL 字符串的 ref。

当通过 [`isRef()`](https://cn.vuejs.org/api/reactivity-utilities.html#isref) 检测到 URL 是一个动态 ref 时，它会使用 [`watchEffect()`](https://cn.vuejs.org/api/reactivity-core.html#watcheffect) 启动一个响应式的 effect。

该 effect 会立刻执行一次，并在此过程中将 URL 的 ref 作为依赖进行跟踪。

当 URL 的 ref 发生改变时，数据就会被重置，并重新请求。

#### resize 获取窗口的宽度和高度

```js
import { ref, onMounted, onUnmounted } from 'vue'

// 定义 useWindowSize hook
function useWindowSize() {
  // 使用 ref 声明窗口的宽度和高度
  const width = ref(window.innerWidth)
  const height = ref(window.innerHeight)

  // 定义一个监听窗口变化的函数
  function handleResize() {
    width.value = window.innerWidth
    height.value = window.innerHeight
  }

  // 在组件创建时，绑定监听事件
  onMounted(() => {
    window.addEventListener('resize', handleResize)
  })

  // 在组件销毁时，移除监听事件
  onUnmounted(() => {
    window.removeEventListener('resize', handleResize)
  })

  // 返回窗口的宽度和高度
  return {
    width,
    height
  }
}
```



#### 案例：ToDo

```js
import { ref, computed } from "vue";

export function useTodos() {
  let title = ref("");

  let todos = ref([{ title: "学习Vue", done: false }]);

  function addTodo() {
    todos.value.push({
      title: title.value,
      done: false,
    });
    title.value = "";
  }

  function clear() {
    todos.value = todos.value.filter((v) => !v.done);
  }

  let active = computed(() => {
    return todos.value.filter((v) => !v.done).length;
  });

  let all = computed(() => todos.value.length);

  let allDone = computed({
    get: function () {
      return active.value === 0;
    },
    set: function (value) {
      todos.value.forEach((todo) => {
        todo.done = value;
      });
    },
  });

  return { title, todos, addTodo, clear, active, all, allDone };
}
```

```vue
<template>
  <div>
    <input type="text" v-model="title" @keydown.enter="addTodo" />
    <button v-if="active < all" @click="clear">清理</button>
    <ul v-if="todos.length">
      <li v-for="todo in todos">
        <input type="checkbox" v-model="todo.done" />
        <span :class="{ done: todo.done }"> {{ todo.title }}</span>
      </li>
    </ul>
    <div v-else>暂无数据</div>
    <div>
      全选<input type="checkbox" v-model="allDone" />
      <span> {{ active }} / {{ all }} </span>
    </div>
  </div>
</template>

<script setup>
import { ref } from "vue";
import { useTodos } from '../utils/todos';

let { title, todos, addTodo, clear, active, all, allDone } = useTodos();
</script>
```

但是所有的操作状态在刷新后就没了，解决这个问题所需要的，就是让 todolist 和本地存储能够同步。

watchEffect 这个函数让我们在数据变化之后可以执行指定的函数。

这段代码使用 watchEffect，数据变化之后会把数据同步到 localStorage 之上，

这样我们就实现了 todolist 和本地存储的同步。

```js
import { ref, computed } from "vue";

export function useTodos() {
  let title = ref("");
  
  function useStorage(name, value=[]){
    let data = ref(JSON.parse(localStorage.getItem(name)||'[]'))
    watchEffect(()=>{
        localStorage.setItem(name,JSON.stringify(data.value))
    })
    return data
  }
  
  let todos = useStorage('todos', []);
  
  function addTodo() {
    todos.value.push({
      title: title.value,
      done: false,
    });
    title.value = "";
  }
  
  function clear() {
    todos.value = todos.value.filter((v) => !v.done);
  }
  
  let active = computed(() => {
    return todos.value.filter((v) => !v.done).length;
  });
  
  let all = computed(() => todos.value.length);
  
  let allDone = computed({
    get: function () {
      return active.value === 0;
    },
    set: function (value) {
      todos.value.forEach((todo) => {
        todo.done = value;
      });
    },
  });
  
  return { title, todos, addTodo, clear, active, all, allDone };
}
```

```vue
<template>
  <div>
    <input type="text" v-model="title" @keydown.enter="addTodo" />
    <button v-if="active < all" @click="clear">清理</button>
    <ul v-if="todos.length">
      <li v-for="todo in todos">
        <input type="checkbox" v-model="todo.done" />
        <span :class="{ done: todo.done }"> {{ todo.title }}</span>
      </li>
    </ul>
    <div v-else>暂无数据</div>
    <div>
      全选<input type="checkbox" v-model="allDone" />
      <span> {{ active }} / {{ all }} </span>
    </div>
  </div>
</template>

<script setup>
import { ref } from "vue";
import { useTodos } from '../utils/todos';

let { title, todos, addTodo, clear, active, all, allDone } = useTodos();
</script>
```

#### 约定和最佳实践

##### 命名

**组合式函数约定用驼峰命名法命名，并以“use”作为开头。**

##### 输入参数

尽管其响应性不依赖 ref，组合式函数仍可接收 ref 参数。

**如果编写的组合式函数会被其他开发者使用，最好在处理输入参数时兼容 ref 而不只是原始的值。**

**[`unref()`](https://cn.vuejs.org/api/reactivity-utilities.html#unref) 工具函数是 val = isRef(val) ? val.value : val 计算的一个语法糖，会对此非常有帮助：**

```js
import { unref } from 'vue'

function useFeature(maybeRef) {
  // 若 maybeRef 确实是一个 ref，它的 .value 会被返回
  // 否则，maybeRef 会被原样返回
  const value = unref(maybeRef)
}
```

**如果组合式函数在接收 ref 为参数时会产生响应式 effect，**

**确保使用 `watch()` 显式地监听此 ref，或者在 `watchEffect()` 中调用 `unref()` 来进行正确的追踪。**

##### 返回值

**一直在组合式函数中使用 `ref()` 而不是 `reactive()`。**

**推荐的约定是组合式函数始终返回一个包含多个 ref 的普通的非响应式对象，**

**这样该对象在组件中被解构为 ref 之后仍可以保持响应性：**

```js
// x 和 y 是两个 ref
const { x, y } = useMouse()
```

从组合式函数返回一个响应式对象会导致在对象解构过程中丢失与组合式函数内状态的响应性连接。

与之相反，ref 则可以维持这一响应性连接。

如果更希望以对象属性的形式来使用组合式函数中返回的状态，

可以将返回的对象用 `reactive()` 包装一次，这样其中的 ref 会被自动解包，

例如：

```js
const mouse = reactive(useMouse())
// mouse.x 链接到了原来的 x ref
console.log(mouse.x)
```

template

```html
Mouse position is at: {{ mouse.x }}, {{ mouse.y }}
```

##### 副作用

**在组合式函数中可以执行副作用 (例如：添加 DOM 事件监听器或者请求数据)，请注意以下规则：**

- 如果应用用到了[服务端渲染](https://cn.vuejs.org/guide/scaling-up/ssr.html) (SSR)，确保在组件挂载后才调用的生命周期钩子中执行 DOM 相关的副作用，

  例如：`onMounted()`。这些钩子仅会在浏览器中被调用，因此可以确保能访问到 DOM。

- **确保在 `onUnmounted()` 时清理副作用。**

  **举例来说，如果一个组合式函数设置了一个事件监听器，**

  **它就应该在 `onUnmounted()` 中被移除 (就像我们在 `useMouse()` 示例中看到的一样)。**

  **当然也可以像之前的 `useEventListener()` 示例那样，使用一个组合式函数来自动帮你做这些事。**

### 使用限制

**组合式函数在 `<script setup>` 或 `setup()` 钩子中，应始终被同步地调用。**

**在某些场景下，也可以在像 `onMounted()` 这样的生命周期钩子中使用组合式函数。**

这个限制是为了让 Vue 能够确定当前正在被执行的到底是哪个组件实例，只有能确认当前组件实例，才能够：

1. 将生命周期钩子注册到该组件实例上
2. 将计算属性和监听器注册到该组件实例上，以便在该组件被卸载时停止监听，避免内存泄漏。

## 响应式

我们使用 JavaScript 的某种机制，把 count 包裹一层，

每当对 count 进行修改时，就去同步更新 double 的值，

那么就有一种 double 自动跟着 count 的变化而变化的感觉，这就是响应式的雏形了。

defineProperty 实现 Vue 2 响应式

```js
let getDouble = n => n * 2
let obj = {}
let count = 1
let double = getDouble(count)

Object.defineProperty(obj, 'count', {
    get() {
        return count
    },
    set(val) {
        count = val
        double = getDouble(val)
    }
})

console.log(double)  // 打印2
obj.count = 2
console.log(double) // 打印4  有种自动变化的感觉
```

Proxy 实现 Vue3 响应式

```js
let getDouble = n => n * 2
let obj = {}
let count = 1
let double = getDouble(count)

let proxy = new Proxy(obj, {
    get: function(target, prop) {
        return target[prop]
    },
    set: function(target, prop, value) {
        target[prop] = value;
        if (prop === 'count') {
            double = getDouble(value)
        }
    },
    deleteProperty(target, prop){
        delete target[prop]
        if (prop === 'count') {
            double = NaN
        }
    }
})

console.log(obj.count, double) // undefined 2
proxy.count = 2
console.log(obj.count, double) // 2 4
delete proxy.count
// 删除属性后，我们打印时，输出的结果就会是 undefined NaN
console.log(obj.count, double) // undefined NaN 
```

在 Vue 3 中还有另一个响应式实现的逻辑，就是利用对象的 get 和 set 函数来进行监听，

这种响应式的实现方式，只能拦截某一个属性的修改，这也是 Vue 3 中 ref 这个 API 的实现。

```js
let getDouble = n => n * 2
let _value = 1
double = getDouble(_value)

let count = {
  get value() {
    return _value
  },
  set value(val) {
    _value = val
    double = getDouble(_value)
  }
}

console.log(count.value, double)
count.value = 2
console.log(count.value, double)
```

## 自定义指令

**一个自定义指令由一个包含类似组件生命周期钩子的对象来定义。**

**钩子函数会接收到指令所绑定元素作为其参数。**

下面是一个自定义指令的例子，当一个 input 元素被 Vue 插入到 DOM 中后，它会被自动聚焦：

**在 `<script setup>` 中，任何以 `v` 开头的驼峰式命名的变量都可以被用作一个自定义指令**

**例子中，`vFocus` 即可以在模板中以 `v-focus` 的形式使用**

```vue
<script setup>
// 在模板中启用 v-focus
const vFocus = {
  mounted: (el) => el.focus()
}
</script>

<template>
  <input v-focus />
</template>
```

假设你还未点击页面中的其他地方，那么上面这个 input 元素应该会被自动聚焦。

该指令比 `autofocus` attribute 更有用，因为它不仅仅可以在页面加载完成后生效，还可以在 Vue 动态插入元素后生效。

### 相关的 TS 类型

在开始编写代码之前，先了解一下自定义指令相关的 TypeScript 类型。

自定义指令有两种实现形式，一种是作为一个对象，其中的写法比较接近于 Vue 组件，除了 [getSSRProps](https://cn.vuejs.org/guide/scaling-up/ssr.html#custom-directives) 和 [deep 选项](https://vue3.chengpeiquan.com/component.html#deep-选项) 外，其他的每一个属性都是一个钩子函数 。

```ts
// 对象式写法的 TS 类型
// ...
export declare interface ObjectDirective<T = any, V = any> {
  created?: DirectiveHook<T, null, V>
  beforeMount?: DirectiveHook<T, null, V>
  mounted?: DirectiveHook<T, null, V>
  beforeUpdate?: DirectiveHook<T, VNode<any, T>, V>
  updated?: DirectiveHook<T, VNode<any, T>, V>
  beforeUnmount?: DirectiveHook<T, null, V>
  unmounted?: DirectiveHook<T, null, V>
  getSSRProps?: SSRDirectiveHook
  deep?: boolean
}
// ...
```

另外一种是函数式写法，只需要定义成一个函数，但这种写法只在 `mounted` 和 `updated` 这两个钩子生效，并且触发一样的行为。

```ts
// 函数式写法的 TS 类型
// ...
export declare type FunctionDirective<T = any, V = any> = DirectiveHook<
  T,
  any,
  V
>
// ...
```

这是每个钩子函数对应的类型，它有 4 个入参：

```ts
// 钩子函数的 TS 类型
// ...
export declare type DirectiveHook<
  T = any,
  Prev = VNode<any, T> | null,
  V = any
> = (
  el: T,
  binding: DirectiveBinding<V>,
  vnode: VNode<any, T>,
  prevVNode: Prev
) => void
// ...
```

钩子函数第二个参数的类型：

```ts
// 钩子函数第二个参数的 TS 类型
// ...
export declare interface DirectiveBinding<V = any> {
  instance: ComponentPublicInstance | null
  value: V
  oldValue: V | null
  arg?: string
  modifiers: DirectiveModifiers
  dir: ObjectDirective<any, V>
}
// ...
```

可以看到自定义指令最核心的就是 “钩子函数” 了，接下来来了解这部分的知识点。

### 指令钩子（钩子函数）

和 组件的生命周期 类似，自定义指令里的逻辑代码也有一些特殊的调用时机，在这里称之为钩子函数。

一个指令的定义对象可以提供几种钩子函数 (都是可选的)：

```js
const myDirective = {
  // 在绑定元素的 attribute 前
  // 或事件监听器应用前调用
  created(el, binding, vnode, prevVnode) {
    // 下面会介绍各个参数的细节
  },
  // 在元素被插入到 DOM 前调用
  beforeMount(el, binding, vnode, prevVnode) {},
  // 在绑定元素的父组件
  // 及它自己的所有子节点都挂载完成后调用
  mounted(el, binding, vnode, prevVnode) {},
  // 绑定元素的父组件更新前调用
  beforeUpdate(el, binding, vnode, prevVnode) {},
  // 在绑定元素的父组件
  // 及它自己的所有子节点都更新后调用
  updated(el, binding, vnode, prevVnode) {},
  // 绑定元素的父组件卸载前调用
  beforeUnmount(el, binding, vnode, prevVnode) {},
  // 绑定元素的父组件卸载后调用
  unmounted(el, binding, vnode, prevVnode) {}
}
```

### 钩子参数

指令的钩子会传递以下几种参数：

- `el`：指令绑定到的 DOM 元素。这可以用于直接操作 DOM。`el` 的值就是通过 `document.querySelector` 拿到的那个 DOM 元素

- `binding`：一个对象，包含以下属性。
  
  - `value`：传递给指令的值。
  
    例如`v-foo="bar"` 里的 `bar`；在 `v-my-directive="1 + 1"` 中，值是 `2`， 支持任意有效的 JS 表达式
  
  - `oldValue`：之前的值，仅在 `beforeUpdate` 和 `updated` 中可用。无论值是否更改，它都可用。
  
  - `arg`：传递给指令的参数 (如果有的话)。
  
    例如`v-foo:bar` 里的 `bar` ，在 `v-my-directive:foo` 中，参数是 `"foo"`；。
  
  - `modifiers`：一个包含修饰符的对象 (如果有的话)。
  
    例如 `v-foo.bar`  里的 `bar`；在 `v-my-directive.foo.bar` 中，修饰符对象是 `{ foo: true, bar: true }`。
  
  - `instance`：使用该指令的组件实例。
  
  - `dir`：指令的定义对象。（就是上面的 `const myDirective = { /* ... */ }` 这个对象）
  
- `vnode`：代表绑定元素的底层 VNode。`el` 对应在 `Vue` 里的虚拟节点信息。

- `prevNode`：之前的渲染中代表指令所绑定元素的 VNode。

  update 时的上一个虚拟节点信息，仅在 `beforeUpdate` 和 `updated` 钩子中可用。

举例来说，像下面这样使用指令：

template

```vue
<div v-example:foo.bar="baz">
```

`binding` 参数会是一个这样的对象：

```js
{
  arg: 'foo',
  modifiers: { bar: true },
  value: /* baz 的值 */,
  oldValue: /* 上一次更新时 baz 的值 */
}
```

**和内置指令类似，自定义指令的参数也可以是动态的。**

举例来说：

template

```vue
<div v-example:[arg]="value"></div>
```

这里指令的参数会基于组件的 `arg` 数据属性响应式地更新。

### 局部注册

自定义指令可以在单个组件内定义并使用，通过和 setup 函数同级别的 `directives` 选项进行定义，可以参考下面的例子和注释：

```vue
<template>
  <!-- 这个使用默认值 `unset` -->
  <div v-highlight>{{ msg }}</div>

  <!-- 这个使用传进去的黄色 -->
  <div v-highlight="`yellow`">{{ msg }}</div>
</template>

<script lang="ts">
import { defineComponent, ref } from 'vue'

export default defineComponent({
  // 自定义指令在这里编写，和 `setup` 同级别
  directives: {
    // `directives` 下的每个字段名就是指令名称
    highlight: {
      // 钩子函数
      mounted(el, binding) {
        el.style.backgroundColor =
          typeof binding.value === 'string' ? binding.value : 'unset'
      },
    },
  },
  setup() {
    const msg = ref<string>('Hello World!')

    return {
      msg,
    }
  },
})
</script>
```

上面是对象式的写法，也可以写成函数式：

```ts
export default defineComponent({
  directives: {
    highlight(el, binding) {
      el.style.backgroundColor =
        typeof binding.value === 'string' ? binding.value : 'unset'
    },
  },
})
```

> TIP
>
> **局部注册的自定义指令，默认在子组件内生效，子组件内无需重新注册即可使用父组件的自定义指令。**

### 全局注册

自定义指令也可以注册成全局，这样就无需在每个组件里定义了，只要在入口文件 `main.ts` 里启用它，

任意组件里都可以使用自定义指令。

#### deep 选项

除了钩子函数，在 相关的 TS 类型里还可以看到有一个 deep 选项，它是一个布尔值，作用是：

如果自定义指令用于一个有嵌套属性的对象，并且需要在嵌套属性更新的时候触发 `beforeUpdate` 和 `updated` 钩子，

那么需要将这个选项设置为 `true` 才能够生效。

```vue
<template>
  <div v-foo="foo"></div>
</template>

<script lang="ts">
import { defineComponent, reactive } from 'vue'

export default defineComponent({
  directives: {
    foo: {
      beforeUpdate(el, binding) {
        console.log('beforeUpdate', binding)
      },
      updated(el, binding) {
        console.log('updated', binding)
      },
      mounted(el, binding) {
        console.log('mounted', binding)
      },
      // 需要设置为 `true` ，如果是 `false` 则不会触发
      deep: true,
    },
  },
  setup() {
    // 定义一个有嵌套属性的对象
    const foo = reactive({
      bar: {
        baz: 1,
      },
    })

    // 2s 后修改其中一个值，会触发 `beforeUpdate` 和 `updated`
    setTimeout(() => {
      foo.bar.baz = 2
      console.log(foo)
    }, 2000)

    return {
      foo,
    }
  },
})
</script>
```

### 在组件上使用

当在组件上使用自定义指令时，它会始终应用于组件的根节点，和[透传 attributes](https://cn.vuejs.org/guide/components/attrs.html) 类似。

template

```vue
<MyComponent v-demo="test" />
```

template

```vue
<!-- MyComponent 的模板 -->

<div> <!-- v-demo 指令会被应用在此处 -->
  <span>My component content</span>
</div>
```

需要注意的是组件可能含有多个根节点。

**当应用到一个多根组件时，指令将会被忽略且抛出一个警告。**

**和 attribute 不同，指令不能通过 v-bind="$attrs" 来传递给一个不同的元素。**

**总的来说，不推荐在组件上使用自定义指令。**

### 简化形式

对于自定义指令来说，一个很常见的情况是仅仅需要在 `mounted` 和 `updated` 上实现相同的行为，除此之外并不需要其他钩子。

这种情况下可以直接用一个函数来定义指令，如下所示：

template

```vue
<div v-color="color"></div>
```

```js
app.directive('color', (el, binding) => {
  // 这会在 `mounted` 和 `updated` 时都调用
  el.style.color = binding.value
})
```

### 对象字面量

**如果指令需要多个值，可以向它传递一个 JavaScript 对象字面量。**

**指令也可以接收任何合法的 JavaScript 表达式。**

template

```
<div v-demo="{ color: 'white', text: 'hello!' }"></div>
```

```js
app.directive('demo', (el, binding) => {
  console.log(binding.value.color) // => "white"
  console.log(binding.value.text) // => "hello!"
})
```

## 插件

插件 (Plugins) 是一种能为 Vue 添加全局功能的工具代码。下面是如何安装一个插件的示例：

```js
import { createApp } from 'vue'

const app = createApp({})

app.use(myPlugin, {
  /* 可选的选项 */
})
```

一个插件可以是一个拥有 `install()` 方法的对象，也可以直接是一个安装函数本身。

安装函数会接收到安装它的[应用实例](https://cn.vuejs.org/api/application.html)和传递给 `app.use()` 的额外选项作为参数：

```js
const myPlugin = {
  install(app, options) {
    // 配置此应用
  }
}
```

插件没有严格定义的使用范围，但是插件发挥作用的常见场景主要包括以下几种：

1. 通过 [`app.component()`](https://cn.vuejs.org/api/application.html#app-component) 和 [`app.directive()`](https://cn.vuejs.org/api/application.html#app-directive) 注册一到多个全局组件或自定义指令。
2. 通过 [`app.provide()`](https://cn.vuejs.org/api/application.html#app-provide) 使一个资源[可被注入](https://cn.vuejs.org/guide/components/provide-inject.html)进整个应用。
3. 向 [`app.config.globalProperties`](https://cn.vuejs.org/api/application.html#app-config-globalproperties) 中添加一些全局实例属性或方法
4. 一个可能上述三种都包含了的功能库 (例如 [vue-router](https://github.com/vuejs/vue-router-next))。

### Vue 专属插件

专属插件通常分为 全局插件 和 单组件插件，

区别在于，全局版本是在 main.ts 引入后 use，而单组件版本则通常是作为一个组件在 .vue 文件里引入使用。

#### 全局插件的使用

**全局插件的使用，就是在 main.ts 通过 import 引入，然后通过 use 来启动初始化。**

**在 Vue 2 ，全局插件是通过 Vue.use(xxx) 来启动，**

**而现在，则需要通过 createApp 的 use，既可以单独一行一个 use ，也可以直接链式 use 下去。**

**参数**

`use` 方法支持两个参数：

| 参数    | 类型               | 作用                                             |
| :------ | :----------------- | :----------------------------------------------- |
| plugin  | object \| function | 插件，一般是在 import 时使用的名称               |
| options | object             | 插件的参数，有些插件在初始化时可以配置一定的选项 |

基本的写法就是像下面这样：

```ts
// main.ts
import plugin1 from 'plugin1'
import plugin2 from 'plugin2'
import plugin3 from 'plugin3'
import plugin4 from 'plugin4'

createApp(App)
  .use(plugin1)
  .use(plugin2)
  .use(plugin3, {
    // plugin3's options
  })
  .use(plugin4)
  .mount('#app')
```

#### 单组件插件的使用

单组件的插件，通常自己本身也是一个 Vue 组件（大部分情况下都会打包为 JS 文件，但本质上是一个 Vue 的 Component ）。

单组件的引入，一般都是在需要用到的 `.vue` 文件里单独 `import` ，然后挂到 `<template />` 里去渲染，

下面是一段模拟代码，理解起来会比较直观：

```vue
<template>
  <!-- 放置组件的渲染标签，用于显示组件 -->
  <ComponentExample />
</template>

<script lang="ts">
import { defineComponent, onMounted, ref } from 'vue'
import logo from '@/assets/logo.png'

// 引入单组件插件
import ComponentExample from 'a-component-example'

export default defineComponent({
  // 挂载组件模板
  components: {
    ComponentExample,
  },
})
</script>
```

### 通用 JS / TS 插件

也叫普通插件，这个 “普通” 不是指功能平平无奇，

而是指它们无需任何框架依赖，可以应用在任意项目中，属于独立的 Library ，比如 [axios](https://github.com/axios/axios) 、 [qrcode](https://github.com/soldair/node-qrcode) 、[md5](https://github.com/pvorb/node-md5) 等等，

在任何技术栈都可以单独引入使用，非 Vue 专属。

通用插件的使用非常灵活，既可以全局挂载，也可以在需要用到的组件里单独引入。

组件里单独引入方式：

```ts
import { defineComponent } from 'vue'
import md5 from '@withtypes/md5'

export default defineComponent({
  setup() {
    const md5Msg = md5('message')
  },
})
```

全局挂载方法比较特殊，因为插件本身不是专属 Vue，没有 `install` 接口，无法通过 `use` 方法直接启动，需要全局 API 挂载。

本地插件

常用的本地封装方式有两种：一种是以 通用 JS / TS 插件 的形式，一种是以 Vue 专属插件 的形式。

#### 封装的目的

举个例子，比如在做一个具备用户系统的网站时，会涉及到手机短信验证码模块，在开始写代码之前，需要先要考虑到这些问题：

1. 很多操作都涉及到下发验证码的请求，比如 “登录” 、 “注册” 、 “修改手机绑定” 、 “支付验证” 等等，

   代码雷同，只是接口 URL 或者参数不太一样

2. 都是需要对手机号是否有传入、手机号的格式正确性验证等一些判断

3. 需要对接口请求成功和失败的情况做一些不同的数据返回，但要处理的数据很相似，都是用于告知调用方当前是什么情况

4. 返回一些 Toast 告知用户当前的交互结果

> TIP
>
> 如果不把这一块的业务代码抽离出来，需要在每个用到的地方都写一次，不仅繁琐，而且以后一旦产品需求有改动，维护起来就惨了。

### 开发本地通用 JS / TS 插件

一般情况下会以通用类型比较常见，因为大部分都是一些比较小的功能，而且可以很方便的在不同项目之间进行复用。

TIP

注：接下来会统一称之为 “通用插件” ，不论是基于 JavaScript 编写的还是 TypeScript 编写的。

#### 项目结构

通常会把这一类文件都归类在 `src` 目录下的 `libs` 文件夹里，

代表存放的是 Library 文件（ JS 项目以 `.js` 文件存放， TS 项目以 `.ts` 文件存放）。

```bash
vue-demo
│ # 源码文件夹
├─src
│ │ # 本地通用插件
│ └─libs
│   ├─foo.ts
│   └─bar.ts
│
│ # 其他结构这里省略…
│
└─package.json
```

这样在调用的时候，可以通过 `@/libs/foo` 来引入，或者配置了 alias 别名（ts），也可以使用别名导入，例如 `@libs/foo` 。

#### 设计规范与开发案例

在设计本地通用插件的时候，需要遵循 ES Module 模块设计规范，并且做好必要的代码注释（用途、入参、返回值等）。

一般来说，会有以下三种情况需要考虑。

##### 只有一个默认功能

如果只有一个默认的功能，那么可以使用 `export default` 来默认导出一个函数。

例如需要封装一个打招呼的功能：

```ts
// src/libs/greet.ts

/**
 * 向对方打招呼
 * @param name - 打招呼的目标人名
 * @returns 向传进来的人名返回一句欢迎语
 */
export default function greet(name: string): string {
  return `Welcome, ${name}!`
}
```

在 Vue 组件里就可以这样使用：

```vue
<script lang="ts">
import { defineComponent } from 'vue'
// 导入本地插件
import greet from '@libs/greet'

export default defineComponent({
  setup() {
    // 导入的名称就是这个工具的方法名，可以直接调用
    const message = greet('Petter')
    console.log(message) // Welcome, Petter!
  },
})
</script>
```

##### 是一个小工具合集

如果有很多个作用相似的函数，那么建议放在一个文件里作为一个工具合集统一管理，使用 `export` 来导出里面的每个函数。

例如需要封装几个通过 [正则表达式](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Guide/Regular_Expressions) 判断表单的输入内容是否符合要求的函数：

```ts
// src/libs/regexp.ts

/**
 * 手机号校验
 * @param phoneNumber - 手机号
 * @returns true=是手机号，false=不是手机号
 */
export function isMob(phoneNumber: number | string): boolean {
  return /^1[3456789]\d{9}$/.test(String(phoneNumber))
}

/**
 * 邮箱校验
 * @param email - 邮箱地址
 * @returns true=是邮箱地址，false=不是邮箱地址
 */
export function isEmail(email: string): boolean {
  return /^[A-Za-z\d]+([-_.][A-Za-z\d]+)*@([A-Za-z\d]+[-.])+[A-Za-z\d]{2,4}$/.test(
    email
  )
}
```

在 Vue 组件里就可以这样使用：

```vue
<script lang="ts">
import { defineComponent } from 'vue'
// 需要用花括号 {} 来按照命名导出时的名称导入
import { isMob, isEmail } from '@libs/regexp'

export default defineComponent({
  setup() {
    // 判断是否是手机号
    console.log(isMob('13800138000')) // true
    console.log(isMob('123456')) // false

    // 判断是否是邮箱地址
    console.log(isEmail('example@example.com')) // true
    console.log(isEmail('example')) // false
  },
})
</script>
```

> TIP
>
> 类似这种情况，就没有必要为 `isMob` 、 `isEmail` 每个方法都单独保存一个文件了，
>
> 只需要统一放在 `regexp.ts` 正则文件里维护。

##### 包含工具及辅助函数

如果主要提供一个独立功能，但还需要提供一些额外的变量或者辅助函数用于特殊的业务场景，

那么可以用 `export default` 导出主功能，用 `export` 导出其他变量或者辅助函数。

在只有一个默认功能这个打招呼例子的基础上修改一下，默认提供的是 “打招呼” 的功能，

偶尔需要更热情的赞美一下，那么这个 “赞美” 行为就可以用这个方式来放到这个文件里一起维护。

```ts
// src/libs/greet.ts

/**
 * 称赞对方
 * @param name - 要称赞的目标人名
 * @returns 向传进来的人名发出一句赞美的话
 */
export function praise(name: string): string {
  return `Oh! ${name}! It's so kind of you!`
}

/**
 * 向对方打招呼
 * @param name - 打招呼的目标人名
 * @returns 向传进来的人名发出一句欢迎语
 */
export default function greet(name: string): string {
  return `Welcome, ${name}!`
}
```

在 Vue 组件里就可以这样使用：

```vue
<script lang="ts">
import { defineComponent } from 'vue'
// 两者可以同时导入使用
import greet, { praise } from '@libs/greet'

export default defineComponent({
  setup() {
    // 使用默认的打招呼
    const message = greet('Petter')
    console.log(message) // Welcome, Petter!

    // 使用命名导出的赞美
    const praiseMessage = praise('Petter')
    console.log(praiseMessage) // Oh! Petter! It's so kind of you!
  },
})
</script>
```

### 开发本地 Vue 专属全局插件

这一类的插件只能给 Vue 使用，有时候自己的业务比较特殊，无法找到完全适用的 npm 包，那么就可以自己写一个！

#### 项目结构

通常会把这一类文件都归类在 `src` 目录下的 `plugins` 文件夹里，

代表存放的是 Plugin 文件（ JS 项目以 `.js` 文件存放， TS 项目以 `.ts` 文件存放）。

```bash
vue-demo
│ # 源码文件夹
├─src
│ │ # 本地 Vue 插件
│ └─plugins
│   ├─foo.ts
│   └─bar.ts
│
│ # 其他结构这里省略…
│
└─package.json
```

这样在调用的时候，可以通过 `@/plugins/foo` 来引入，或者配置了 alias 别名，也可以使用别名导入，例如 `@plugins/foo` 。

#### 设计规范

在设计本地 Vue 插件的时候，需要遵循 Vue 官方撰写的 [Vue Plugins 设计规范](https://cn.vuejs.org/guide/reusability/plugins.html) ，并且做好必要的代码注释，

除了标明插件 API 的 “用途、入参、返回值” 之外，最好在注释内补充一个 Example 或者 Tips 说明，

功能丰富的插件最好直接写个 README 文档。

#### 开发案例

全局插件开发并启用后，只需要在 `main.ts` 里导入并 `use` 一次，即可在所有的组件内使用插件的功能。

下面对全局插件进行一个开发示范，希望能给大家以后需要的时候提供思路参考。

> TIP
>
> 单组件插件一般作为 npm 包发布，会借助 Webpack 、 Vite 或者 Rollup 单独构建，
>
> 本地直接放到 components 文件夹下作为组件管理即可。

##### 基本结构

插件支持导出两种格式的：一种是函数，一种是对象。

1. 当导出为一个函数时， Vue 会直接调用这个函数，此时插件内部是这样子：

```ts
export default function (app, options) {
  // 逻辑代码...
}
```

1. 当导出为一个对象时，对象上面需要有一个 `install` 方法给 Vue ， Vue 通过调用这个方法来启用插件，此时插件内部是这样子：

```ts
export default {
  install: (app, options) => {
    // 逻辑代码...
  },
}
```

不论哪种方式，入口函数都会接受两个入参：

|  参数   |          作用          | 类型                                                   |
| :-----: | :--------------------: | :----------------------------------------------------- |
|   app   | `createApp` 生成的实例 | `App` （从 'vue' 里导入该类型），见下方的案例演示      |
| options |   插件初始化时的选项   | `undefined` 或一个对象，对象的 TS 类型由插件的选项决定 |

如果需要在插件初始化时传入一些必要的选项，可以定义一个对象作为 options ，

这样只要在 `main.ts` 里 `use` 插件时传入第二个参数，插件就可以拿到它们：

```ts
// src/main.ts
createApp(App)
  // 注意这里的第二个参数就是插件选项
  .use(customPlugin, {
    foo: 1,
    bar: 2,
  })
  .mount('#app')
```

##### 编写插件

这里以一个 自定义指令为例，写一个用于管理自定义指令的插件，

其中包含两个自定义指令：一个是判断是否有权限，一个是给文本高亮，文本高亮还支持一个插件选项。

```ts
// src/plugins/directive.ts
import type { App } from 'vue'

// 插件选项的类型
interface Options {
  // 文本高亮选项
  highlight?: {
    // 默认背景色
    backgroundColor: string
  }
}

/**
 * 自定义指令
 * @description 保证插件单一职责，当前插件只用于添加自定义指令
 */
export default {
  install: (app: App, options?: Options) => {
    /**
     * 权限控制
     * @description 用于在功能按钮上绑定权限，没权限时会销毁或隐藏对应 DOM 节点
     * @tips 指令传入的值是管理员的组别 id
     * @example <div v-permission="1" />
     */
    app.directive('permission', (el, binding) => {
      // 假设 1 是管理员组别的 id ，则无需处理
      if (binding.value === 1) return

      // 其他情况认为没有权限，需要隐藏掉界面上的 DOM 元素
      if (el.parentNode) {
        el.parentNode.removeChild(el)
      } else {
        el.style.display = 'none'
      }
    })

    /**
     * 文本高亮
     * @description 用于给指定的 DOM 节点添加背景色，搭配文本内容形成高亮效果
     * @tips 指令传入的值需要是合法的 CSS 颜色名称或者 Hex 值
     * @example <div v-highlight="`cyan`" />
     */
    app.directive('highlight', (el, binding) => {
      // 获取默认颜色
      let defaultColor = 'unset'
      if (
        Object.prototype.toString.call(options) === '[object Object]' &&
        options?.highlight?.backgroundColor
      ) {
        defaultColor = options.highlight.backgroundColor
      }

      // 设置背景色
      el.style.backgroundColor =
        typeof binding.value === 'string' ? binding.value : defaultColor
    })
  },
}
```

##### 启用插件

在 `main.ts` 全局启用插件，在启用的时候传入了第二个参数 “插件的选项” ，这里配置了个高亮指令的默认背景颜色：

```ts
// src/main.ts
import { createApp } from 'vue'
import App from '@/App.vue'
import directive from '@/plugins/directive' // 导入插件

createApp(App)
   // 自定义插件
  .use(directive, {
    highlight: {
      backgroundColor: '#ddd',
    },
  })
  .mount('#app')
```

##### 使用插件

在 Vue 组件里使用：

```vue
<template>
  <!-- 测试 permission 指令 -->
  <div>根据 permission 指令的判断规则：</div>
  <div v-permission="1">这个可以显示</div>
  <div v-permission="2">这个没有权限，会被隐藏</div>

  <!-- 测试 highlight 指令 -->
  <div>根据 highlight 指令的判断规则：</div>
  <div v-highlight="`cyan`">这个是青色高亮</div>
  <div v-highlight="`yellow`">这个是黄色高亮</div>
  <div v-highlight="`red`">这个是红色高亮</div>
  <div v-highlight>这个是使用插件初始化时设置的灰色</div>
</template>
```

### 全局 API 挂载

对于一些使用频率比较高的插件方法，如果觉得在每个组件里单独导入再用很麻烦，也可以考虑将其挂载到 Vue 上，使其成为 Vue 的全局变量。

**注：接下来的全局变量，都是指 Vue 环境里的全局变量，非 Window 下的全局变量。**

#### 回顾 Vue 2

在 Vue 2 ，可以通过 `prototype` 的方式来挂载全局变量，然后通过 `this` 关键字来从 Vue 原型上调用该方法。

以 `md5` 插件为例，在 `main.ts` 里进行全局 `import`，然后通过 `prototype` 去挂到 Vue 上。

```ts
import Vue from 'vue'
import md5 from 'md5'

Vue.prototype.$md5 = md5
```

之后在 `.vue` 文件里，就可以这样去使用 `md5`。

```ts
const md5Msg = this.$md5('message')
```

#### 了解 Vue 3

在 Vue 3 ，已经不再支持 `prototype` 这样使用了，在 `main.ts` 里没有了 `Vue`，在组件的生命周期里也没有了 `this`。

如果依然想要挂载全局变量，需要通过全新的 [globalProperties](https://cn.vuejs.org/api/application.html#app-config-globalproperties) 来实现，

在使用该方式之前，可以把 `createApp` 定义为一个变量再执行挂载。

#### 定义全局 API

如上，在配置全局变量之前，可以把初始化时的 `createApp` 定义为一个变量（假设为 `app` ），

然后把需要设置为全局可用的变量或方法，挂载到 `app` 的 `config.globalProperties` 上面。

```ts
import md5 from 'md5'

// 创建 Vue 实例
const app = createApp(App)

// 把插件的 API 挂载全局变量到实例上
app.config.globalProperties.$md5 = md5

// 也可以自己写一些全局函数去挂载
app.config.globalProperties.$log = (text: string): void => {
  console.log(text)
}

app.mount('#app')
```

main.ts

```ts
import { createApp } from 'vue'
import App from './App.vue'

const app = createApp(App)

// 全局的属性
app.config.globalProperties.$hello = 'hello world'
app.config.globalProperties.$sayHello = () => console.log('你好呀')

app.mount('#app')
```

Demo.vue

```vue
<script lang="ts">
import { defineComponent, getCurrentInstance } from 'vue'

// 为了告诉 TypeScript 这些新 property，我们需要使用[模块扩充]
declare module '@vue/runtime-core' {
  interface ComponentCustomProperties {
    $hello: string
    $sayHello: () => void
  }
}

export default defineComponent({
  name: 'Demo-Component',
  setup() {
    const currentInstance = getCurrentInstance()
    console.log(currentInstance?.proxy?.$hello)
    currentInstance?.proxy?.$sayHello()
    // 或者
    // console.log(currentInstance?.appContext.config.globalProperties.$hello)
    // currentInstance?.appContext.config.globalProperties.$sayHello()
  },
})
</script>
```

#### 全局 API 的替代方案

在 Vue 3 实际上并不是特别推荐使用全局变量，Vue 3 比较推荐按需引入使用，这也是在构建过程中可以更好的做到代码优化。

特别是针对 TypeScript ， Vue 作者尤雨溪先生对于全局 API 的相关 PR 说明： [Global API updates](https://github.com/vuejs/rfcs/pull/117) ，也是不建议在 TS 里使用。

那么确实是需要用到一些全局 API 怎么办？

对于一般的数据和方法，建议采用 [Provide / Inject](https://vue3.chengpeiquan.com/communication.html#provide-inject) 方案，

在根组件（通常是 App.vue ）把需要作为全局使用的数据或方法 Provide 下去，

在需要用到的组件里通过 Inject 即可获取到，或者使用 [EventBus](https://vue3.chengpeiquan.com/communication.html#eventbus-new) / [Vuex](https://vue3.chengpeiquan.com/communication.html#vuex-new) / [Pinia](https://vue3.chengpeiquan.com/pinia.html) 等全局通信方案来处理。

### 编写一个插件

为了更好地理解如何构建 Vue.js 插件，我们可以试着写一个简单的 `i18n` ([国际化 (Internationalization)](https://en.wikipedia.org/wiki/Internationalization_and_localization) 的缩写) 插件。

让我们从设置插件对象开始，建议在一个单独的文件中创建并导出它，以保证更好地管理逻辑，如下所示：

```js
// plugins/i18n.js
export default {
  install: (app, options) => {
    // 在这里编写插件代码
  }
}
```

我们希望有一个翻译函数，这个函数接收一个以 `.` 作为分隔符的 `key` 字符串，用来在用户提供的翻译字典中查找对应语言的文本。

期望的使用方式如下：

template

```html
<h1>{{ $translate('greetings.hello') }}</h1>
```

这个函数应当能够在任意模板中被全局调用。

这一点可以通过在插件中将它添加到 `app.config.globalProperties` 上来实现：

```js
// plugins/i18n.js
export default {
  install: (app, options) => {
    // 注入一个全局可用的 $translate() 方法
    app.config.globalProperties.$translate = (key) => {
      // 传入的 options 就是 { greetings: { hello: 'Bonjour!' } }
      // 获取 `options` 对象的深层属性
      // 使用 `key` 作为索引，key.split('.') 结果为 ['greetings', 'hello']
      return key.split('.').reduce((o, i) => {
        if (o) return o[i]
      }, options)
    }
  }
}
```

我们的 `$translate` 函数会接收一个例如 `greetings.hello` 的字符串，在用户提供的翻译字典中查找，并返回翻译得到的值。

用于查找的翻译字典对象则应当在插件被安装时作为 `app.use()` 的额外参数被传入：

```js
import i18nPlugin from './plugins/i18n'

app.use(i18nPlugin, {
  greetings: {
    hello: 'Bonjour!'
  }
})
```

这样，我们一开始的表达式 `$translate('greetings.hello')` 就会在运行时被替换为 `Bonjour!` 了。

代码解释：

```js
['greetings', 'hello'].reduce(
    (obj, key) => {
      if (obj) {
        return obj[key];
      }
    },
    {
      greetings: {
        hello: 'Bonjour!',
      },
    }
  );
```

| `callback`  | `previousValue`                        | `currentValue` | `currentIndex` | 返回值                  |
| :---------- | :------------------------------------- | :------------- | :------------- | :---------------------- |
| first call  | `{ greetings: { hello: 'Bonjour!' } }` | `'greetings'`  | `0`            | `{ hello: 'Bonjour!' }` |
| second call | `{ hello: 'Bonjour!' }`                | `hello`        | `1`            | `'Bonjour!'`            |

reduce() 方法会将初始值作为上一次回调函数的返回值，并以第一个元素 'greetings' 作为当前遍历的元素。

在回调函数中，我们取出上一次回调函数的返回值中的 greetings 属性值，并将其作为本次回调函数的返回值。

因此，第一次回调函数的返回值为 { hello: 'Bonjour!' }。

接下来，

reduce() 方法会继续遍历数组中的元素，并以上一次回调函数的返回值作为上一次回调函数的返回值，

以当前遍历的元素 'hello' 作为当前遍历的元素。

在回调函数中，我们取出上一次回调函数的返回值中的 hello 属性值，即 'Bonjour!'，

并将其作为本次回调函数的返回值。因此，第二次回调函数的返回值为 'Bonjour!'。

由于数组中没有更多的元素，reduce() 方法就会停止遍历，并将最后一次回调函数的返回值作为 reduce() 方法的返回值。

因此，上面的代码会返回 'Bonjour!'。

### 插件中的 Provide / Inject

在插件中，我们可以通过 `provide` 来为插件用户供给一些内容。

举例来说，我们可以将插件接收到的 `options` 参数提供给整个应用，让任何组件都能使用这个翻译字典对象。

```js
// plugins/i18n.js
export default {
  install: (app, options) => {
    app.config.globalProperties.$translate = (key) => {
      return key.split('.').reduce((o, i) => {
        if (o) return o[i]
      }, options)
    }

    app.provide('i18n', options)
  }
}
```

现在，插件用户就可以在他们的组件中以 `i18n` 为 key 注入并访问插件的选项对象了。

```vue
<script setup>
import { inject } from 'vue'

const i18n = inject('i18n')

console.log(i18n.greetings.hello)
</script>
```

## Teleport


可定制插槽和 CSS 过渡效果的模态框组件

**App.vue**

```vue
<script setup>
import Modal from './Modal.vue'
import { ref } from 'vue'

const showModal = ref(false)
</script>

<template>
  <button id="show-modal" @click="showModal = true">Show Modal</button>
  <!--  
    to 的值可以是一个 CSS 选择器字符串，也可以是一个 DOM 元素对象。
    这段代码的作用就是告诉 Vue“把以下模板片段传送到 body 标签下”。
   -->
  <Teleport to="body">
    <!-- 使用这个 modal 组件，传入 prop -->
    <modal :show="showModal" @close="showModal = false">
      <template #header>
        <h3>custom header</h3>
      </template>
    </modal>
  </Teleport>
</template>
```

**Modal.vue**

```vue
<template>
  <Transition name="modal">
    <div v-if="show" class="modal-mask">
      <div class="modal-wrapper">
        <div class="modal-container">
          <div class="modal-header">
            <slot name="header">default header</slot>
          </div>

          <div class="modal-body">
            <slot name="body">default body</slot>
          </div>

          <div class="modal-footer">
            <slot name="footer">
              default footer
              <button class="modal-default-button" @click="$emit('close')">OK</button>
            </slot>
          </div>
        </div>
      </div>
    </div>
  </Transition>
</template>

<script setup>
const props = defineProps({
  show: Boolean
})
</script>

<style>
.modal-mask {
  position: fixed;
  z-index: 9998;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.5);
  display: table;
  transition: opacity 0.3s ease;
}

.modal-wrapper {
  display: table-cell;
  vertical-align: middle;
}

.modal-container {
  width: 300px;
  margin: 0px auto;
  padding: 20px 30px;
  background-color: #fff;
  border-radius: 2px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.33);
  transition: all 0.3s ease;
}

.modal-header h3 {
  margin-top: 0;
  color: #42b983;
}

.modal-body {
  margin: 20px 0;
}

.modal-default-button {
  float: right;
}

/*
 * 对于 transition="modal" 的元素来说
 * 当通过 Vue.js 切换它们的可见性时
 * 以下样式会被自动应用。
 *
 * 你可以简单地通过编辑这些样式
 * 来体验该模态框的过渡效果。
 */

.modal-enter-from {
  opacity: 0;
}

.modal-leave-to {
  opacity: 0;
}

.modal-enter-from .modal-container,
.modal-leave-to .modal-container {
  -webkit-transform: scale(1.1);
  transform: scale(1.1);
}
</style>
```

### 多个 Teleport 共享目标

一个可重用的模态框组件可能同时存在多个实例。

对于此类场景，

**多个 `<Teleport>` 组件可以将其内容挂载在同一个目标元素上，**

**而顺序就是简单的顺次追加，后挂载的将排在目标元素下更后面的位置上。**

比如下面这样的用例：

template

```vue
<Teleport to="#modals">
  <div>A</div>
</Teleport>
<Teleport to="#modals">
  <div>B</div>
</Teleport>
```

渲染的结果为：

```html
<div id="modals">
  <div>A</div>
  <div>B</div>
</div>
```

## 动态绑定 CSS

动态绑定 CSS ，在 Vue 2 就已经存在了，在此之前常用的是 `:class` 和 `:style` ，现在在 Vue 3 ，还可以通过 `v-bind` 来动态修改了。

### 使用 :class 动态修改样式名

它是绑定在 DOM 元素上面的一个属性，跟 `class="class-name"` 这样的属性同级别，它非常灵活！

> TIP
>
> 使用 `:class` 是用来动态修改样式名，也就意味着必须提前把样式名对应的样式表先写好！

假设已经提前定义好了这几个变量：

```vue
<script lang="ts">
import { defineComponent } from 'vue'

export default defineComponent({
  setup() {
    const activeClass = 'active-class'
    const activeClass1 = 'active-class1'
    const activeClass2 = 'active-class2'
    const isActive = true

    return {
      activeClass,
      activeClass1,
      activeClass2,
      isActive,
    }
  },
})
</script>
```

如果只想绑定一个单独的动态样式，可以传入一个字符串：

```vue
<template>
  <p :class="activeClass">Hello World!</p>
</template>
```

如果有多个动态样式，也可以传入一个数组：

```vue
<template>
  <p :class="[activeClass1, activeClass2]">Hello World!</p>
</template>
```

还可以对动态样式做一些判断，这个时候传入一个对象：

```vue
<template>
  <p :class="{ 'active-class': isActive }">Hello World!</p>
</template>
```

多个判断的情况下，记得也用数组套起来：

```vue
<template>
  <p :class="[{ activeClass1: isActive }, { activeClass2: !isActive }]">
    Hello World!
  </p>
</template>
```

那么什么情况下会用到 `:class` 呢？

最常见的场景，应该就是导航、选项卡了，比如要给一个当前选中的选项卡做一个突出高亮的状态，

那么就可以使用 `:class` 来动态绑定一个样式。

```vue
<template>
  <ul class="list">
    <li
      class="item"
      :class="{ cur: index === curIndex }"
      v-for="(item, index) in 5"
      :key="index"
      @click="curIndex = index"
    >
      {{ item }}
    </li>
  </ul>
</template>

<script lang="ts">
import { defineComponent, ref } from 'vue'

export default defineComponent({
  setup() {
    const curIndex = ref<number>(0)

    return {
      curIndex,
    }
  },
})
</script>

<style scoped>
.cur {
  color: red;
}
</style>
```

这样就简单实现了一个点击切换选项卡高亮的功能。

### 使用 :style 动态修改内联样式

如果觉得使用 `:class` 需要提前先写样式，再去绑定样式名有点繁琐，

有时候只想简简单单的修改几个样式，那么可以通过 `:style` 来处理。

默认的情况下，都是传入一个对象去绑定：

- `key` 是符合 CSS 属性名的 “小驼峰式” 写法，或者套上引号的短横线分隔写法（原写法），

  例如在 CSS 里，定义字号是 `font-size` ，那么需要写成 `fontSize` 或者 `'font-size'` 作为它的键。

- `value` 是 CSS 属性对应的 “合法值”，

  比如要修改字号大小，可以传入 `13px` 、`0.4rem` 这种带合法单位字符串值，

  但不可以是 `13` 这样的缺少单位的值，无效的 CSS 值会被过滤不渲染。

```vue
<template>
  <p
    :style="{
      fontSize: '13px',
      'line-height': 2,
      color: '#ff0000',
      textAlign: 'center',
    }"
  >
    Hello World!
  </p>
</template>
```

如果有些特殊场景需要绑定多套 `style`，

需要在 `script` 先定义好各自的样式变量（也是符合上面说到的那几个要求的对象），然后通过数组来传入：

```vue
<template>
  <p :style="[style1, style2]">Hello World!</p>
</template>

<script lang="ts">
import { defineComponent } from 'vue'

export default defineComponent({
  setup() {
    const style1 = {
      fontSize: '13px',
      'line-height': 2,
    }
    const style2 = {
      color: '#ff0000',
      textAlign: 'center',
    }

    return {
      style1,
      style2,
    }
  },
})
</script>
```

### 使用 v-bind 动态修改 style

当然，以上两种形式都是关于 `<script />` 和 `<template />` 部分的操作，

如果觉得会给模板带来一定的维护成本的话，不妨考虑这个新方案，将变量绑定到 `<style />` 部分去。

> TIP
>
> 请注意这是一个在 `3.2.0` 版本之后才被归入正式队列的新功能！
>
> 如果需要使用它，请确保的 `vue` 的版本号在 `3.2.0` 以上，最好是保持最新版本。

先来看看基本的用法：

```vue
<template>
  <p class="msg">Hello World!</p>
</template>

<script lang="ts">
import { defineComponent, ref } from 'vue'

export default defineComponent({
  setup() {
    const fontColor = ref<string>('#ff0000')

    return {
      fontColor,
    }
  },
})
</script>

<style scoped>
.msg {
  color: v-bind(fontColor);
}
</style>
```

如上面的代码，将渲染出一句红色文本的 `Hello World!`

这其实是利用了现代浏览器支持的 CSS 变量来实现的一个功能

（所以如果打算用它的话，需要提前注意一下兼容性噢，点击查看：[CSS Variables 兼容情况](https://caniuse.com/css-variables) ）。

它渲染到 DOM 上，其实也是通过绑定 `style` 来实现，可以看到渲染出来的样式是：

```html
<p class="msg" data-v-7eb2bc79="" style="--7eb2bc79-fontColor:#ff0000;">
  Hello World!
</p>
```

对应的 CSS 变成了：

```css
.msg[data-v-7eb2bc79] {
  color: var(--7eb2bc79-fontColor);
}
```

理论上 `v-bind` 函数可以在 Vue 内部支持任意的 JavaScript 表达式，

但由于可能包含在 CSS 标识符中无效的字符，因此官方是建议在大多数情况下，用引号括起来，如：

```css
.text {
  font-size: v-bind('theme.font.size');
}
```

由于 CSS 变量的特性，因此对 CSS 响应式属性的更改不会触发模板的重新渲染（这也是和 `:class` 与 `:style` 的最大不同）。

> TIP
>
> 不管有没有开启  `<style scoped>`，使用 `v-bind` 渲染出来的 CSS 变量，
>
> 都会带上 `scoped` 的随机 hash 前缀，避免样式污染（永远不会意外泄漏到子组件中），所以请放心使用！

如果对 CSS 变量的使用还不是很了解的话，可以先阅读一下相关的基础知识点。

相关阅读：[使用 CSS 自定义属性（变量） - MDN](https://developer.mozilla.org/zh-CN/docs/Web/CSS/Using_CSS_custom_properties)

## 样式表的组件作用域

CSS 不像 JS ，是没有作用域的概念的，一旦写了某个样式，直接就是全局污染。

所以 [BEM 命名法](https://www.bemcss.com/) 等规范才应运而生。

但在 Vue 组件里，有两种方案可以避免出现这种污染问题：

一个是 Vue 2 就有的 `<style scoped>` ，一个是 Vue 3 新推出的 `<style module>` 。

### Style Scoped

Vue 组件在设计的时候，就想到了一个很优秀的解决方案，

通过 `scoped` 来支持创建一个 CSS 作用域，使这部分代码只运行在这个组件渲染出来的虚拟 DOM 上。

使用方式很简单，只需要在 `<style />` 上添加 `scoped` 属性：

```vue
<!-- 注意这里多了一个 `scoped` -->
<style scoped>
.msg {
  width: 100%;
}
.msg p {
  color: #333;
  font-size: 14px;
}
</style>
```

编译后，虚拟 DOM 都会带有一个 `data-v-xxxxx` 这样的属性，

其中 `xxxxx` 是一个随机生成的 Hash ，同一个组件的 Hash 是相同并且唯一的：

```html
<div class="msg" data-v-7eb2bc79>
  <p data-v-7eb2bc79>Hello World!</p>
</div>
```

而 CSS 则也会带上与 HTML 相同的属性，从而达到样式作用域的目的。

```css
.msg[data-v-7eb2bc79] {
  width: 100%;
}
.msg p[data-v-7eb2bc79] {
  color: #333;
  font-size: 14px;
}
```

使用 `scoped` 可以有效的避免全局样式污染，

可以在不同的组件里面都使用相同的 className，而不必担心会相互覆盖，不必再定义很长很长的样式名来防止冲突了。

### 子组件的根元素

使用 scoped 后，父组件的样式将不会渗透到子组件中。

不过，子组件的根节点会同时被父组件的作用域样式和子组件的作用域样式影响。

这样设计是为了让父组件可以从布局的角度出发，调整其子组件根元素的样式。

## 深度选择器

使用 scoped 后，父组件的样式将不会渗透到子组件中，但也不能直接修改子组件的样式。

如果确实需要进行修改子组件的样式，必须通过 `::v-deep`（完整写法） 或者 `:deep`（快捷写法） 操作符来实现。

> TIP
>
> 1. 旧版的深度操作符是 `>>>` 、 `/deep/` 和 `::v-deep`，现在 `>>>` 和 `/deep/` 已进入弃用阶段（虽然暂时还没完全移除）
>
> 2. 同时需要注意的是，旧版 `::v-deep` 的写法是作为组合器的方式，写在样式或者元素前面，
>
>    如：`::v-deep .class-name { /* ... */ }`，现在这种写法也废弃了。

现在不论是 `::v-deep` 还是 `:deep` ，使用方法非常统一，来假设 .b 是子组件的样式名：

```vue
<style scoped>
.a :deep(.b) {
  /* ... */
}
</style>
```

编译后：

```css
.a[data-v-f3f3eg9] .b {
  /* ... */
}
```

> TIP
>
> 可以看到，新的 deep 写法是作为一个类似 JS “函数” 那样去使用，需要深度操作的样式或者元素名，作为 “入参” 去传入。
>
> 同理，如果使用 Less 或者 Stylus 这种支持嵌套写法的预处理器，也是可以这样去深度操作的：

```less
.a {
  :deep(.b) {
    /* ... */
  }
}
```

另外，除了操作子组件的样式，

那些通过 `v-html` 创建的 DOM 内容，也不受作用域内的样式影响，也可以通过深度操作符来实现样式修改。

## 插槽选择器

默认情况下，作用域样式不会影响到 `<slot/>` 渲染出来的内容，因为它们被认为是父组件所持有并传递进来的。

使用 `:slotted` 伪类以明确地将插槽内容作为选择器的目标：

```vue
<style scoped>
:slotted(div) {
  color: red;
}
</style>
```

## 全局选择器

如果想让其中一个样式规则应用到全局，比起另外创建一个 `<style>`，可以使用 `:global` 伪类来实现 (看下面的代码)：

```vue
<style scoped>
:global(.red) {
  color: red;
}
</style>
```

## 混合使用局部与全局样式

你也可以在同一个组件中同时包含作用域样式和非作用域样式：

```vue
<style>
/* 全局样式 */
</style>

<style scoped>
/* 局部样式 */
</style>
```

## 组件

Rate组件：根据 rate 的值，来渲染出不同数量的星星

使用 defineProps 来规范传递数据的格式，

规定组件会接收外部传来的 value 属性，并且只能是数字，

然后根据 value 的值计算出评分的星星。

**定义组件**

```vue
<template>
  <div>
    {{rate}}
  </div>
</template>

<script setup>
import { defineProps,computed } from 'vue';
let props = defineProps({
  value: Number
})
let rate = computed(()=>"★★★★★☆☆☆☆☆".slice(5 - props.value, 10 - props.value))
</script>
```

**使用组件**

```vue
<template>
  <Rate :value="score"></Rate>
</template>

<script setup>
import {ref} from 'vue'
import Rate from './components/Rate.vue'
let score = ref(3)
</script>
```

更进一步，在组件中内置一些主题颜色，加入 CSS 的内容

如下面代码，Rate 组件新接收一个属性 theme，默认值是 orange。

在 Rate 组件中内置几个主题颜色，根据传递的 theme 计算出颜色，并且使用 :style 渲染。

```vue
<template>
  <div :style="fontstyle">
    {{rate}}
  </div>
</template>

<script setup>
import { defineProps,computed, } from 'vue';
let props = defineProps({
  value: Number,
  theme:{ type:String, default:'orange' }
})
console.log(props)
let rate = computed(()=>"★★★★★☆☆☆☆☆".slice(5 - props.value, 10 - props.value))
const themeObj = {
  'black': '#00',
  'white': '#fff',
  'red': '#f5222d',
  'orange': '#fa541c',
  'yellow': '#fadb14',
  'green': '#73d13d',
  'blue': '#40a9ff',
}
const fontstyle = computed(()=> {
  return `color:${themeObj[props.theme]};`
})
</script>
```

```vue
<template>
  <Rate :value="3" ></Rate>
  <Rate :value="4" theme="red"></Rate>
  <Rate :value="1" theme="green"></Rate>
</template>

<script setup>
import Rate from './components/Rate.vue'
</script>
```

添加修改评分的功能

滑动修改评分

chrome 支持小数版星星评分，火狐不支持小数版星星评分，支持1~5整数评分（最大值为5）

```vue
<template>
  <div :style="fontstyle">
    <div class="rate" @mouseout="mouseOut">
      <span class="star" @mouseover="mouseOver(num)" v-for="num in 5" :key="num">☆</span>
      <span class="real" :style="fontwidth">
        <span @mouseover="mouseOver(num)" v-for='num in 5' :key="num">★</span>
      </span>
    </div> 
  </div>
</template>

<script setup>
import { ref, defineProps, computed } from "vue";

let props = defineProps({
  value: Number,
  theme:{ type:String, default:'orange' }
})

const themeObj = {
  'black': '#00',
  'white': '#fff',
  'red': '#f5222d',
  'orange': '#fa541c',
  'yellow': '#fadb14',
  'green': '#73d13d',
  'blue': '#40a9ff',
}

const fontstyle = computed(()=> {
  return `color:${themeObj[props.theme]};`
})

let width = ref(props.value)

function mouseOver(i){
  width.value = i 
}

function mouseOut(){
  width.value = props.value
}

const fontwidth = computed(()=>`width:${width.value*13}px;`)
</script>

<style scoped>
.rate {
  position:relative;
}

.star {
  display: inline-block;
  width: 13px;
}

.rate > span.real {
  position:absolute;
  top:0;
  left:0;
  overflow: hidden;
}
</style>
```

```vue
<template>
  <Rate :value="3.5" />
</template>

<script setup>
import Rate from './components/Rate.vue'
</script>

<style>
#app {
  font-size: 16px;
}
</style>
```

滑动修改评分

支持小数版星星评分，支持1~5的小数评分（最大值为5，chrome无问题，火狐小数为8或9显示错误）

```vue
<template>
  <div :style="fontstyle">
    <div class="rate" @mouseout="mouseOut">
      <span class="star" @mouseover="mouseOver(num)" v-for="num in 5" :key="num">☆</span>
      <span class="real" :style="fontwidth">
        <template v-for="num in 5" :key="num">
          <span v-if="num <= width" class="real-star" @mouseover="mouseOver(num)">★</span>
          <span v-else class="inline-star" @mouseover="mouseOver(num)">★</span>
        </template>
      </span>
    </div> 
  </div>
</template>

<script setup>
import { ref, defineProps, computed } from "vue";

let props = defineProps({
  value: Number,
  theme:{ type:String, default:'orange' }
})

const themeObj = {
  'black': '#00',
  'white': '#fff',
  'red': '#f5222d',
  'orange': '#fa541c',
  'yellow': '#fadb14',
  'green': '#73d13d',
  'blue': '#40a9ff',
}

const fontstyle = computed(()=> {
  return `color:${themeObj[props.theme]};`
})

let width = ref(props.value)

function mouseOver(i){
  width.value = i 
}

function mouseOut(){
  width.value = props.value
}

const fontwidth = computed(()=>`width:${width.value*13}px;`)
</script>

<style scoped>
.rate {
  position:relative;
}

.star {
  display: inline-block;
  width: 13px;
}

.rate > span.real {
  position:absolute;
  top:0;
  left:0;
  overflow: hidden;
}

.real-star {
  display: inline-block;
  width: 13px;
}

.inline-star {
  /* 无依赖绝对定位 */
  position: absolute;
  width: 13px;
}
</style>
```

```vue
<template>
  <Rate :value="3.5" />
</template>

<script setup>
import Rate from './components/Rate.vue'
</script>

<style>
#app {
  font-size: 16px;
}
</style>
```

点击选择评分，仅支持整数

```vue
<template>
  <div :style="fontstyle" @mouseout="mouseOut">
    <div class="rate">
      <span class="star" @mouseover="mouseOver(num)" v-for="num in 5" :key="num">☆</span>
      <span class="real" :style="fontwidth">
        <template v-for="num in 5" :key="num">
          <span v-if="num <= width" class="real-star" @click="onRate(num)" @mouseover="mouseOver(num)">★</span>
          <span v-else class="inline-star" @click="onRate(num)" @mouseover="mouseOver(num)">★</span>
        </template>
      </span>
    </div> 
  </div>
</template>

<script setup>
import { ref, computed } from "vue";

let props = defineProps({
  value: Number,
  theme:{ type:String, default:'orange' }
})

let emits = defineEmits('update-rate') // 定义emits

function onRate(num){
  emits('update-rate', num) // 发射emits
}

const themeObj = {
  'black': '#00',
  'white': '#fff',
  'red': '#f5222d',
  'orange': '#fa541c',
  'yellow': '#fadb14',
  'green': '#73d13d',
  'blue': '#40a9ff',
}

const fontstyle = computed(()=> {
  return `color:${themeObj[props.theme]};`
})

let width = ref(props.value)

function mouseOver(i){
  width.value = i 
}

const fontwidth = computed(()=>`width:${width.value*13}px;`)
</script>

<style scoped>
.rate {
  position:relative;
}

.star {
  display: inline-block;
  width: 13px;
}

.rate > span.real {
  position:absolute;
  top:0;
  left:0;
  overflow: hidden;
}

.real-star {
  display: inline-block;
  width: 13px;
}

.inline-star {
  position: absolute;
  width: 13px;
}
</style>
```

```vue
<template>
  <h1>你的评分是 {{score}}</h1>
  <!-- 父组件接收事件 -->
  <Rate :value="score" @update-rate="update"></Rate>
</template>

<script setup>
  import {ref} from 'vue'
  import Rate from './components/Rate.vue'
  let score = ref(3.5)
  function update(num) {
    score.value = num
  }
</script>
```

## 组件通信

常用的方案有：

| 方案             | 父组件向子组件 | 子组件向父组件 |
| :--------------- | :------------- | :------------- |
| props / emits    | props          | emits          |
| v-model / emits  | v-model        | emits          |
| ref / emits      | ref            | emits          |
| provide / inject | provide        | inject         |
| EventBus         | emit / on      | emit / on      |
| Reative State    | -              | -              |
| Vuex             | -              | -              |
| Pinia            | -              | -              |

### 父子组件通信

#### props / emits

这是 Vue 跨组件通信最常用，也是基础的一个方案，它的通信过程是：

1. 父组件 Father.vue 通过 props 向子组件 Child.vue 传值
2. 子组件 Child.vue 则可以通过 emits 向父组件 Father.vue 发起事件通知

##### props

```vue
<!-- Father.vue -->
<template>
  <Child
    id="child-component"
    class="class-name-from-father"
    :keys="['foo', 'bar']"
    :obj="{ foo: 'bar' }"
    data-hash="b10a8db164e0754105b7a99be72e3fe5"
    title="用户信息"
    :index="1"
    :uid="userInfo.id"
    :user-name="userInfo.name"
  />
</template>

<script>
// Father.vue
import { defineComponent } from 'vue'
import Child from '@cp/Child.vue'

interface Member {
  id: number
  name: string
}

export default defineComponent({
  // 需要启用子组件作为模板
  components: {
    Child,
  },

  // 定义一些数据并 `return` 给 `<template />` 用
  setup() {
    const userInfo: Member = {
      id: 1,
      name: 'Petter',
    }

    // 不要忘记 `return` ，否则 `<template />` 拿不到数据
    return {
      userInfo,
    }
  },
})
</script>
```

```vue
<!-- Child.vue -->
<template>
  <p>标题：{{ title }}</p>
  <p>索引：{{ index }}</p>
  <p>用户id：{{ uid }}</p>
  <p>用户名：{{ userName }}</p>
</template>

<script>
// Child.vue
export default defineComponent({
  props: {
    // 可选，并提供默认值
    title: {
      type: String,
      required: false,
      default: '默认标题',
    },

    // 默认可选，单类型
    index: Number,

    // 添加一些自定义校验
    userName: {
      type: String,

      // 在这里校验用户名必须至少 3 个字
      validator: (v) => v.length >= 3,
    },

    // 默认可选，但允许多种类型
    uid: [Number, String],
  },
  setup(props, { attrs }) {
    // 该入参包含了当前组件定义的所有 props
    console.log(props)
    // `attrs` 是个对象，每个 Attribute 都是它的 `key`
    console.log(attrs.id) // child-component
    console.log(attrs.class) // class-name-from-father

    // 传递数组会被保留类型，不会被转换为 `key1,key2` 这样的字符串
    // 这一点与 `Element.getAttribute()` 完全不同
    console.log(attrs.keys) // ['foo', 'bar']

    // 传递对象也可以正常获取
    console.log(attrs.obj) // {foo: 'bar'}

    // 如果传下来的 Attribute 带有短横线，需要通过这种方式获取
    console.log(attrs['data-hash']) // b10a8db164e0754105b7a99be72e3fe5
  },  
})
</script>
```

#####  emits

```vue
<!-- Father.vue -->
<template>
  <Child @update-info="updateInfo" />
</template>

<script>
// Father.vue
import { defineComponent, reactive } from 'vue'
import Child from '@cp/Child.vue'

interface Member {
  id: number
  name: string
  age: number
}

export default defineComponent({
  components: {
    Child,
  },
  setup() {
    const userInfo: Member = reactive({
      id: 1,
      name: 'Petter',
      age: 0,
    })
    
    function updateInfo({ name, age }: Member) {
      // 当 name 变化时更新 name 的值
      if (name && name !== userInfo.name) {
        userInfo.name = name
      }
    
      // 当 age 变化并且新值在正确的范围内时，更新 age 的值
      if (age > 0 && age !== userInfo.age) {
        userInfo.age = age
      }
    }

    return {
      userInfo,
      updateInfo,
    }
  },
})
</script>
```

```js
// Child.vue
export default defineComponent({
  emits: ['update-age'],
  setup(props, { emit }) {
    // 通知父组件
    emit('update-info', {
      name: 'Tom',
      age: 18,
    })
  },
})
```

#### v-model / emits

使用 props / emits ，如果要更新父组件的数据，还需要在父组件声明一个更新函数并绑定事件给子组件，才能够更新。

而使用 v-model / emits ，无需在父组件声明更新函数，

只需要在子组件 Child.vue 里通过 `update:` 前缀加上 v-model 的属性名这样的格式，即可直接定义一个更新事件。

子组件通过调用 `emit('update:xxx')` 即可让父组件更新对应的数据。

```vue
<!-- Father.vue -->
<template>
  <Child
    v-model:uid="userInfo.id"
    v-model:username="userInfo.name"
    v-model:age="userInfo.age"
  />
</template>
```

```js
// Child.vue
export default defineComponent({
  props: {
    uid: Number,
    username: String,
    age: Number,
  },
  // 注意这里的 `update:` 前缀
  emits: ['update:uid', 'update:username', 'update:age'],
  setup(props, { emit }) {
    // 2s 后更新用户名
    setTimeout(() => {
      emit('update:username', 'Tom')
    }, 2000)
  },
})
```

#### ref

父组件操作子组件

父组件可以给子组件绑定 ref 属性，然后通过 Ref 变量操作子组件的数据或者调用子组件里面的方法。

先在 `<template />` 处给子组件标签绑定 `ref` 属性：

```vue
<!-- Father.vue -->
<template>
  <Child ref="child" />
</template>
```

然后在 `<script />` 部分定义好对应的变量名称 `child` （记得要 return 出来哦），即可通过该变量操作子组件上的变量或方法：

```ts
// Father.vue
import { defineComponent, onMounted, ref } from 'vue'
import Child from '@cp/Child.vue'

export default defineComponent({
  components: {
    Child,
  },
  setup() {
    // 给子组件定义一个 `ref` 变量
    const child = ref<typeof Child>()

    // 请保证视图渲染完毕后再执行操作
    onMounted(async () => {
      // 执行子组件里面的 AJAX 请求函数
      await child.value!.queryList()

      // 显示子组件里面的弹窗
      child.value!.isShowDialog = true
    })

    // 必须 `return` 出去才可以给到 `<template />` 使用
    return {
      child,
    }
  },
})
```

### 爷孙组件通信

常用的方法有：

| 方案             | 爷组件向孙组件 | 孙组件向爷组件 |
| :--------------- | :------------- | :------------- |
| provide / inject | provide        | inject         |
| EventBus         | emit / on      | emit / on      |
| Reactive State   | -              | -              |
| Vuex             | -              | -              |
| Pinia            | -              | -              |

#### provide / inject

**这个通信方式也是有两部分：**

**Grandfather.vue 通过 provide 向孙组件 Grandson.vue 提供数据和方法**

**Grandson.vue 通过 inject 注入爷爷组件 Grandfather.vue 的数据和方法**

**无论组件层次结构有多深，发起 provide 的组件都可以作为其所有下级组件的依赖提供者。**

Vue 3 的这一部分内容对比 Vue 2 来说变化很大，但使用起来其实也很简单，开发者学到这里不用慌，它们之间也有相同的地方：

1. 爷组件不需要知道哪些子组件使用它 provide 的数据
2. 子组件不需要知道 inject 的数据来自哪里

另外要切记一点就是： provide 和 inject 绑定并不是可响应的，这是刻意为之的，除非传入了一个可侦听的对象。

**Grandfather.vue**

每次调用 provide 的时候都需要传入两个参数：

| 参数  | 说明       |
| :---- | :--------- |
| key   | 数据的名称 |
| value | 数据的值   |

```js
// Grandfather.vue
import { defineComponent, provide, ref } from 'vue'

export default defineComponent({
  setup() {
    // 声明一个响应性变量并 provide 其自身
    // 孙组件获取后可以保持响应性
    const msg = ref('Hello World!')
    provide('msg', msg)

    // 只 provide 响应式变量的值
    // 孙组件获取后只会得到当前的值
    provide('msgValue', msg.value)

    // 声明一个方法并 provide
    function printMsg() {
      console.log(msg.value)
    }
    provide('printMsg', printMsg)
  },
})
```

**Grandson.vue**

每次调用 inject 时只需要传入一个参数：

| 参数 | 类型   | 说明                        |
| :--- | :----- | :-------------------------- |
| key  | string | 与 provide 相对应的数据名称 |

另外还有一个特殊情况需要注意，

当 Grandson.vue 的父级、爷级组件都 provide 了相同名字的数据下来，那么在 inject 的时候，会优先选择离它更近的组件的数据。

```ts
// Grandson.vue
import { defineComponent, inject } from 'vue'
import type { Ref } from 'vue'

export default defineComponent({
  setup() {
    // 获取响应式变量
    const msg = inject<Ref<string>>('msg')
    console.log(msg!.value)

    // 获取普通的字符串
    const msgValue = inject<string>('msgValue')
    console.log(msgValue)

    // 获取函数
    const printMsg = inject<() => void>('printMsg')
    if (typeof printMsg === 'function') {
      printMsg()
    }
  },
})
```

可以看到在每个 inject 都使用尖括号 `<>` 添加了相应的 TS 类型，并且在调用变量的时候都进行了判断，这是因为默认的情况下， inject 除了返回指定类型的数据之外，还默认带上 `undefined` 作为可能的值。

如果明确数据不会是 `undefined` ，也可以在后面添加 `as` 关键字指定其 TS 类型，这样 TypeScript 就不再因为可能出现 `undefined` 而提示代码有问题。

```ts
// Grandson.vue
import { defineComponent, inject } from 'vue'
import type { Ref } from 'vue'

export default defineComponent({
  setup() {
    // 获取响应式变量
    const msg = inject('msg') as Ref<string>
    console.log(msg.value)

    // 获取普通的字符串
    const msgValue = inject('msgValue') as string
    console.log(msgValue)

    // 获取函数
    const printMsg = inject('printMsg') as () => void
    printMsg()
  },
})
```

##### 设置默认值

inject API 还支持设置默认值，可以接受更多的参数。

默认情况下，只需要传入第二个参数指定默认值即可，

对于不可控的情况，建议在 inject 时添加一个兜底的默认值，防止程序报错：

```ts
// Grandson.vue
import { defineComponent, inject, ref } from 'vue'
import type { Ref } from 'vue'

export default defineComponent({
  setup() {
    // 获取响应式变量
    const msg = inject<Ref<string>>('msg', ref('Hello'))
    console.log(msg.value)

    // 获取普通的字符串
    const msgValue = inject<string>('msgValue', 'Hello')
    console.log(msgValue)

    // 获取函数
    const printMsg = inject<() => void>('printMsg', () => {
      console.log('Hello')
    })
    printMsg()
  },
})
```

需要注意的是， inject 的什么类型的数据，其默认值也需要保持相同的类型。

##### 工厂函数选项

inject API 在第二个 TS 类型的基础上，还有第三个 TS 类型，可以传入第三个参数：

```ts
function inject<T>(
  key: InjectionKey<T> | string,
  defaultValue: () => T,
  treatDefaultAsFactory?: false
): T
```

当第二个参数是一个工厂函数，那么可以添加第三个值，将其设置为 `true` ，此时默认值一定会是其 return 的值。

在 Grandson.vue 里新增一个 inject ，接收一个不存在的函数名，并提供一个工厂函数作为默认值：

```ts
// Grandson.vue
import { defineComponent, inject } from 'vue'

interface Food {
  name: string
  count: number
}

export default defineComponent({
  setup() {
    // 获取工厂函数
    const getFood = inject<() => Food>('nonexistentFunction', () => {
      return {
        name: 'Pizza',
        count: 1,
      }
    })
    console.log(typeof getFood) // function

    const food = getFood()
    console.log(food) // {name: 'Pizza', count: 1}
  },
})
```

此时因为第三个参数默认为 Falsy 值，所以可以得到一个函数作为默认值，并可以调用该函数获得一个 Food 对象。

如果将第三个参数传入为 `true` ，再运行程序则会在 `const food = getFood()` 这一行报错：

```ts
// Grandson.vue
import { defineComponent, inject } from 'vue'

interface Food {
  name: string
  count: number
}

export default defineComponent({
  setup() {
    // 获取工厂函数
    const getFood = inject<() => Food>(
      'nonexistentFunction',
      () => {
        return {
          name: 'Pizza',
          count: 1,
        }
      },
      true
    )
    console.log(typeof getFood) // object

    // 此时下面的代码无法运行
    // 报错 Uncaught (in promise) TypeError: getMsg is not a function
    const food = getFood()
    console.log(food)
  },
})
```

因为此时第三个入参告知 inject ，默认值是一个工厂函数，因此默认值不再是函数本身，而是函数的返回值，

所以 `typeof getFood` 得到的不再是一个 `function` 而是一个 `object` 。

这个参数对于需要通过工厂函数返回数据的情况非常有用！

### 兄弟组件通信

兄弟组件是指两个组件都挂载在同一个 Father.vue 下，但两个组件之间并没有什么直接的关联，先看看它们的关系：

```bash
Father.vue
├─Brother.vue
└─LittleBrother.vue
```

这种层级关系下，如果组件之间要进行通信，目前通常有这两类选择：

1. 【不推荐】先把数据传给 Father.vue ，再使用父子组件通信方案处理
2. 【推荐】借助全局组件通信的方案达到目的

### 全局组件通信

全局组件通信是指项目下两个任意组件，不管是否有直接关联（例如父子关系、爷孙关系）都可以直接进行交流的通信方案。

举个例子，像下面这种项目结构， B2.vue 可以采用全局通信方案直接向 D2.vue 发起交流，而无需经过它们各自的父组件。

```bash
A.vue
├─B1.vue
├───C1.vue
├─────D1.vue
├─────D2.vue
├───C2.vue
├─────D3.vue
└─B2.vue
```

常用的方法有：

| 方案           | 发起方 | 接收方 |
| :------------- | :----- | :----- |
| EventBus       | emit   | on     |
| Reactive State | -      | -      |
| Vuex           | -      | -      |
| Pinia          | -      | -      |

#### Reactive State

在 Vue 3 里，使用响应式的 reactive API 也可以实现一个小型的状态共享库，如果运用在一个简单的 H5 活动页面这样小需求里，完全可以满足使用。

##### 创建状态中心

首先在 src 目录下创建一个 state 文件夹，并添加一个 index.ts 文件，写入以下代码：

```ts
// src/state/index.ts
import { reactive } from 'vue'

// 如果有多个不同业务的内部状态共享
// 使用具名导出更容易维护
export const state = reactive({
  // 设置一个属性并赋予初始值
  message: 'Hello World',

  // 添加一个更新数据的方法
  setMessage(msg: string) {
    this.message = msg
  },
})
```

这就完成了一个简单的 Reactive State 响应式状态中心的创建。

##### 设定状态更新逻辑

接下来在一个组件 Child.vue 的 `<script />` 里添加以下代码，分别进行了以下操作：

1. 打印初始值
2. 对 state 里的数据启用侦听器
3. 使用 state 里的方法更新数据
4. 直接更新 state 的数据

```ts
// Child.vue
import { defineComponent, watch } from 'vue'
import { state } from '@/state'

export default defineComponent({
  setup() {
    // 打印初始值
    console.log(state.message)
    // Hello World

    // 对 state 里的数据启用侦听器 因为是响应式数据，所以可以侦听数据变化
    watch(
      () => state.message,
      (val) => {
        console.log('Message 发生变化：', val)
      }
    )

    setTimeout(() => {
      // 使用 state 里的方法更新数据
      state.setMessage('Hello Hello')
      // Message 发生变化： Hello Hello
    }, 1000)

    setTimeout(() => {
      // 直接更新 state 的数据
      state.message = 'Hi Hi'
      // Message 发生变化： Hi Hi
    }, 2000)
  },
})
```

##### 观察全局状态变化

继续在另外一个组件 Father.vue 里写入以下代码，导入 state 并在 `<template />` 渲染其中的数据：

```vue
<!-- Father.vue -->
<template>
  <div>{{ state.message }}</div>
  <Child />
</template>

<script lang="ts">
import { defineComponent } from 'vue'
import Child from '@cp/Child.vue'
import { state } from '@/state'

export default defineComponent({
  components: {
    Child,
  },
  setup() {
    return {
      state,
    }
  },
})
</script>
```

可以观察到当 Child.vue 里的定时器执行时， Father.vue 的视图也会同步得到更新。

一个无需额外插件即可实现的状态中心就完成了！

#### EventBus

EventBus 通常被称之为 “全局事件总线” ，是用在全局范围内通信的一个常用方案，在 Vue 2 时期该方案非常流行，其特点就是 “简单” 、 “灵活” 、 “轻量级” 。

##### 回顾 Vue 2

在 Vue 2 ，使用 EventBus 无需导入第三方插件，可以在项目下的 libs 文件夹里，创建一个名为 eventBus.ts 的文件，导出一个新的 Vue 实例即可。

```ts
// src/libs/eventBus.ts
import Vue from 'vue'
export default new Vue()
```

上面短短两句代码已完成了一个 EventBus 的创建，接下来就可以开始进行通信。

先在负责接收事件的组件里，利用 Vue 的生命周期，通过 `eventBus.$on` 添加事件侦听，通过 `eventBus.$off` 移除事件侦听。

```ts
import eventBus from '@libs/eventBus'

export default {
  mounted() {
    // 在组件创建时，添加一个名为 `hello` 的事件侦听
    eventBus.$on('hello', () => {
      console.log('Hello World')
    })
  },
  beforeDestroy() {
    // 在组件销毁前，通过 `hello` 这个名称移除该事件侦听
    eventBus.$off('hello')
  },
}
```

然后在另外一个组件里通过 `eventBus.$emit` 触发事件侦听。

```ts
import eventBus from './eventBus'

export default {
  methods: {
    sayHello() {
      // 触发名为 `hello` 的事件
      eventBus.$emit('hello')
    },
  },
}
```

这样一个简单的全局方案就完成了。

##### 了解 Vue 3

Vue 3 应用实例不再实现事件触发接口，因此移除了 `$on` 、 `$off` 和 `$once` 这几个事件 API ，无法像 Vue 2 一样利用 Vue 实例创建 EventBus 。

根据官方文档在 [事件 API 迁移策略](https://v3-migration.vuejs.org/breaking-changes/events-api.html#migration-strategy) 的推荐，可以使用 [mitt](https://github.com/developit/mitt) 或者 [tiny-emitter](https://github.com/scottcorgan/tiny-emitter) 等第三方插件实现 EventBus 。

##### 创建 Vue 3 的 EventBus

这里以 mitt 为例，示范如何创建一个 Vue 3 的 EventBus ，首先需要安装它。

```shell
npm i mitt
```

然后在 src/libs 文件夹下，创建一个名为 eventBus.ts 的文件，文件内容和 Vue 2 的写法其实是一样的，只不过是把 Vue 实例换成了 mitt 实例。

```ts
// src/libs/eventBus.ts
import mitt from 'mitt'
export default mitt()
```

接下来就可以定义通信的相关事件了，常用的 API 和参数如下：

| 方法名称 | 作用                           |
| :------- | :----------------------------- |
| on       | 注册一个侦听事件，用于接收数据 |
| emit     | 调用方法发起数据传递           |
| off      | 用来移除侦听事件               |

`on` 的参数：

| 参数    | 类型             | 作用                                 |
| :------ | :--------------- | :----------------------------------- |
| type    | string \| symbol | 方法名                               |
| handler | function         | 接收到数据之后要做什么处理的回调函数 |

这里的 `handler` 建议使用具名函数，因为匿名函数无法销毁。

`emit` 的参数：

| 参数 | 类型             | 作用                         |
| :--- | :--------------- | :--------------------------- |
| type | string \| symbol | 与 on 对应的方法名           |
| data | any              | 与 on 对应的，允许接收的数据 |

`off` 的参数：

| 参数    | 类型             | 作用                                    |
| :------ | :--------------- | :-------------------------------------- |
| type    | string \| symbol | 与 on 对应的方法名                      |
| handler | function         | 要被删除的，与 on 对应的 handler 函数名 |

更多的 API 可以查阅 [插件的官方文档](https://github.com/developit/mitt) ，在了解了最基本的用法之后，来开始配置一对组件通信。

##### 创建和移除侦听事件

在需要暴露交流事件的组件里，通过 `on` 配置好接收方法，同时为了避免路由切换过程中造成事件多次被绑定，从而引起多次触发，需要在适当的时机 `off` 掉：

```ts
import { defineComponent, onBeforeUnmount } from 'vue'
import eventBus from '@libs/eventBus'

export default defineComponent({
  setup() {
    // 声明一个打招呼的方法
    function sayHi(msg = 'Hello World!') {
      console.log(msg)
    }

    // 启用侦听
    eventBus.on('sayHi', sayHi)

    // 在组件卸载之前移除侦听
    onBeforeUnmount(() => {
      eventBus.off('sayHi', sayHi)
    })
  },
})
```

关于销毁的时机，可以参考组件的生命周期。

##### 调用监听事件

在需要调用交流事件的组件里，通过 `emit` 进行调用：

```ts
import { defineComponent } from 'vue'
import eventBus from '@libs/eventBus'

export default defineComponent({
  setup() {
    // 调用打招呼事件，传入消息内容
    eventBus.emit('sayHi', 'Hello')
  },
})
```

##### 旧项目升级 EventBus

在 Vue 3 的 EventBus 里，可以看到它的 API 和旧版是非常接近的，只是去掉了 `$` 符号。

如果要对旧的项目进行升级改造，由于原来都是使用了 `$on` 、 `$emit` 等旧的 API ，一个一个组件去修改成新的 API 容易遗漏或者全局替换出错。

因此可以在创建 eventBus.ts 的时候，通过自定义一个 eventBus 对象来挂载 mitt 的 API 。

在 eventBus.ts 里，改成以下代码：

```ts
// src/libs/eventBus.ts
import mitt from 'mitt'

// 初始化一个 mitt 实例
const emitter = mitt()

// 在导出时使用旧的 API 名称去调用 mitt 的 API
export default {
  $on: (...args) => emitter.on(...args),
  $emit: (...args) => emitter.emit(...args),
  $off: (...args) => emitter.off(...args),
}
```

这样在组件里就可以继续使用 `eventBus.$on` 、`eventBus.$emit` 等旧 API ，不会影响旧项目的升级使用。

## 全局状态管理

使用 Pinia，即使在小型单页应用中，也可以获得如下功能：

- Devtools 支持
  - 追踪 actions、mutations 的时间线
  - 在组件中展示它们所用到的 Store
  - 让调试更容易的 Time travel
- 热更新
  - 不必重载页面即可修改 Store
  - 开发时可保持当前的 State
- 插件：可通过插件扩展 Pinia 功能
- 为 JS 开发者提供适当的 TypeScript 支持以及**自动补全**功能。
- 支持服务端渲染

```sh
# 需要 cd 到的项目目录下
npm install pinia
```

```ts
import { createApp } from 'vue'
import { createPinia } from 'pinia' // 导入 Pinia
import App from '@/App.vue'

createApp(App)
  .use(createPinia()) // 启用 Pinia
  .mount('#app')
```

|   作用   | Vue Component |        Vuex         |  Pinia  |
| :------: | :-----------: | :-----------------: | :-----: |
| 数据管理 |     data      |        state        |  state  |
| 数据计算 |   computed    |       getters       | getters |
| 行为方法 |    methods    | mutations / actions | actions |

### 创建 Store

Pinia 的核心也是为 Store

先在 src 文件夹下创建一个 stores 文件夹，并在里面添加一个 index.ts 文件，然后就可以来添加一个最基础的 Store 。

Store 是通过 defineStore 方法来创建的，它有两种入参形式：

#### 形式 1 ：接收两个参数

接收两个参数，第一个参数是 Store 的唯一 ID ，第二个参数是 Store 的选项：

```ts
// src/stores/index.ts
import { defineStore } from 'pinia'

export const useStore = defineStore('main', {
  // Store 选项...
})
```

#### 形式 2 ：接收一个参数

接收一个参数，直接传入 Store 的选项，但是需要把唯一 ID 作为选项的一部分一起传入：

```ts
// src/stores/index.ts
import { defineStore } from 'pinia'

export const useStore = defineStore({
  id: 'main',
  // Store 选项...
})
```

> TIP
>
> 不论是哪种创建形式，都必须为 Store 指定一个唯一 ID 。

这里把导出的函数名命名为 `useStore` ，以 `use` 开头是 Vue 3 对可组合函数的一个命名约定。

并且使用的是 `export const` 而不是 `export default` 。

如果有多个 Store ，可以分模块管理，并根据实际的功能用途进行命名（ e.g. `useMessageStore` 、 `useUserStore` 、 `useGameStore` … ）。

### 管理 state

#### 给 Store 添加 state

它是通过一个箭头函数的形式来返回数据，并且能够正确的帮推导 TypeScript 类型：

```ts
// src/stores/index.ts
import { defineStore } from 'pinia'

export const useStore = defineStore('main', {
  // 先定义一个最基本的 message 数据
  state: () => ({
    message: 'Hello World',
  }),
  // ...
})
```

需要注意一点的是，如果不显式 return ，箭头函数的返回值需要用圆括号 `()` 套起来，这个是箭头函数的要求

> TIP
>
> 可能有开发者会问： Vuex 可以用一个对象来定义 state 的数据， Pinia 可以吗？
>
> 答案是：不可以！ state 的类型必须是 `state?: (() => {}) | undefined` ，要么不配置（就是 undefined ），要么只能是个箭头函数。

#### 手动指定数据类型

虽然 Pinia 会帮推导 TypeScript 的数据类型，但有时候可能不太够用，比如下面这段代码，请留意代码注释的说明：

```ts
// ...
export const useStore = defineStore('main', {
  state: () => {
    return {
      message: 'Hello World',
      // 添加了一个随机消息数组
      randomMessages: [],
    }
  },
  // ...
})
```

预期应该是一个字符串数组 `string[]` ，但是这个时候 Pinia 会帮推导成 `never[]` ，那么类型就对不上了。

这种情况下就需要手动指定 randomMessages 的类型，可以通过 `as` 来指定：

```ts
// ...
export const useStore = defineStore('main', {
  state: () => {
    return {
      message: 'Hello World',
      // 通过 as 关键字指定 TS 类型
      randomMessages: [] as string[],
    }
  },
  // ...
})
```

或者使用尖括号 `<>` 来指定：

```ts
// ...
export const useStore = defineStore('main', {
  state: () => {
    return {
      message: 'Hello World',
      // 通过尖括号指定 TS 类型
      randomMessages: <string[]>[],
    }
  },
  // ...
})
```

这两种方式是等价的。

#### 获取和更新 state

获取 state 有多种方法，略微有区别，但相同的是，都是响应性的。

> WARNING
>
> **不能直接通过 ES6 解构的方式（ e.g. const { message } = store ），那样会破坏数据的响应性。**

##### 使用 store 实例

用法上和 Vuex 很相似，但有一点区别是，数据直接是挂在 `store` 上的，而不是 `store.state` 上面！

> TIP
>
> e.g. Vuex 是 `store.state.message` ， Pinia 是 `store.message` 。

所以，可以直接通过 `store.message` 直接调用 state 里的数据。

```ts
import { defineComponent } from 'vue'
import { useStore } from '@/stores'

export default defineComponent({
  setup() {
    // 像 useRouter 那样定义一个变量拿到实例
    const store = useStore()

    // 直接通过实例来获取数据
    console.log(store.message)

    // 这种方式需要把整个 store 给到 template 去渲染数据
    return {
      store,
    }
  },
})
```

一些比较复杂的数据这样写会很长，所以有时候更推荐用下面介绍的 computed API 和 storeToRefs API 等方式来获取。

在数据更新方面，在 Pinia 可以直接通过 Store 实例更新 state （这一点与 Vuex 有明显的不同，[更改 Vuex 的 store 中的状态的唯一方法是提交 mutation](https://vuex.vuejs.org/zh/guide/mutations.html)），

所以如果要更新 `message` ，只需要像下面这样，就可以更新 `message` 的值了！

```ts
store.message = 'New Message.'
```

##### 使用 computed API

在 Vue 组件里导入的 Store ，并通过计算数据 computed 拿到里面的 message 数据传给 template 使用。

```vue
<script lang="ts">
import { computed, defineComponent } from 'vue'
import { useStore } from '@/stores'

export default defineComponent({
  setup() {
    // 像 useRouter 那样定义一个变量拿到实例
    const store = useStore()

    // 通过计算拿到里面的数据
    const message = computed(() => store.message)
    console.log('message', message.value)

    // 传给 template 使用
    return {
      message,
    }
  },
})
</script>
```

和 使用 store 实例 以及 使用 storeToRefs API 不同，这个方式默认情况下无法直接更新 state 的值。

因为这里的定义的 `message` 变量是一个只有 getter ，没有 setter 的 ComputedRef 数据，所以它是只读的。

如果要更新数据怎么办？

1. 可以通过提前定义好的 Store Actions 方法进行更新。
2. 在定义 computed 变量的时候，配置好 setter 的行为：

```ts
// 其他代码和上一个例子一样，这里省略...

// 修改：定义 computed 变量的时候配置 getter 和 setter
const message = computed({
  // getter 还是返回数据的值
  get: () => store.message,
  // 配置 setter 来定义赋值后的行为
  set(newVal) {
    store.message = newVal
  },
})

// 此时不再抛出 Write operation failed: computed value is readonly 的警告
message.value = 'New Message.'

// store 上的数据已成功变成了 New Message.
console.log(store.message)
```

##### 使用 storeToRefs API

Pinia 还提供了一个 storeToRefs API 用于把 state 的数据转换为 ref 变量。

这是一个专门为 Pinia Stores 设计的 API ，类似于 toRefs ，

区别在于，它会忽略掉 Store 上面的方法和非响应性的数据，只返回 state 上的响应性数据。

```ts
import { defineComponent } from 'vue'
import { useStore } from '@/stores'

// 记得导入这个 API
import { storeToRefs } from 'pinia'

export default defineComponent({
  setup() {
    const store = useStore()

    // 通过 storeToRefs 来拿到响应性的 message
    const { message } = storeToRefs(store)
    console.log('message', message.value)

    return {
      message,
    }
  },
})
```

通过这个方式拿到的 `message` 变量是一个 [Ref](https://vue3.chengpeiquan.com/component.html#响应式-api-之-ref-new) 类型的数据，所以可以像普通的 ref 变量一样进行读取和赋值。

```ts
// 直接赋值即可
message.value = 'New Message.'

// store 上的数据已成功变成了 New Message.
console.log(store.message)
```

##### 使用 toRefs API

如 使用 storeToRefs API 部分所说，该 API 本身的设计就是类似于 toRefs ，所以也可以直接用 toRefs 把 state 上的数据转成 ref 变量。

```ts
// 注意 toRefs 是 vue 的 API ，不是 Pinia
import { defineComponent, toRefs } from 'vue'
import { useStore } from '@/stores'

export default defineComponent({
  setup() {
    const store = useStore()

    // 跟 storeToRefs 操作都一样，只不过用 Vue 的这个 API 来处理
    const { message } = toRefs(store)
    console.log('message', message.value)

    return {
      message,
    }
  },
})
```

可以像普通的 ref 变量一样进行读取和赋值。

注意，像上面这样，对 store 执行 toRefs 会把 store 上面的 getters 、 actions 也一起提取，如果只需要提取 state 上的数据，可以这样做：

```ts
// 只传入 store.$state
const { message } = toRefs(store.$state)
```

##### 使用 toRef API

**toRef 是 toRefs 的兄弟 API ，一个是只转换一个字段，一个是转换所有字段，所以它也可以用来转换 state 数据变成 ref 变量。**

```ts
// 注意 toRef 是 vue 的 API ，不是 Pinia
import { defineComponent, toRef } from 'vue'
import { useStore } from '@/stores'

export default defineComponent({
  setup() {
    const store = useStore()

    // 遵循 toRef 的用法即可
    const message = toRef(store, 'message')
    console.log('message', message.value)

    return {
      message,
    }
  },
})
```

##### 使用 actions 方法

在 Vuex ，如果想通过方法来操作 state 的更新，必须通过 mutation 来提交；而异步操作需要更多一个步骤，必须先通过 action 来触发 mutation ，非常繁琐！

Pinia 所有操作都集合为 action ，无需区分同步和异步，按照平时的函数定义即可更新 state 。

详见 管理 actions 一节。

##### 批量更新 state

在获取和更新 state 部分说的都是如何修改单个 state 数据，那么有时候要同时修改很多个，会显得比较繁琐。

如果写过 React 或者微信小程序，应该非常熟悉这些用法：

```ts
// 下面不是 Vue 的代码，不要在项目里使用

// React
this.setState({
  foo: 'New Foo Value',
  bar: 'New bar Value',
})

// 微信小程序
this.setData({
  foo: 'New Foo Value',
  bar: 'New bar Value',
})
```

Pinia 也提供了一个 `$patch` API 用于同时修改多个数据，它接收一个参数：

|     参数     |    类型     |            语法            |
| :----------: | :---------: | :------------------------: |
| partialState | 对象 / 函数 | store.$patch(partialState) |

###### 传入一个对象

当参数类型为对象时，key 是要修改的 state 数据名称， value 是新的值（支持嵌套传值），用法如下：

```ts
// 继续用前面的数据，这里会打印出修改前的值
console.log(JSON.stringify(store.$state))
// 输出 {"message":"Hello World","randomMessages":[]}

/**
 * 注意这里，传入了一个对象
 */
store.$patch({
  message: 'New Message',
  randomMessages: ['msg1', 'msg2', 'msg3'],
})

// 这里会打印出修改后的值
console.log(JSON.stringify(store.$state))
// 输出 {"message":"New Message","randomMessages":["msg1","msg2","msg3"]}
```

对于简单的数据，直接修改成新值是非常好用的。

但有时候并不单单只是修改，而是要对数据进行拼接、补充、合并等操作，相对而言开销就会很大，

这种情况下，更适合 传入一个函数 来处理。

> TIP
>
> 使用这个方式时， key 只允许是实例上已有的数据，不可以提交未定义的数据进去。
>
> 强制提交的话，在 TypeScript 会抛出错误， JavaScript 虽然不会报错，但实际上， Store 实例上面依然不会有这个新增的非法数据。

###### 传入一个函数

当参数类型为函数时，该函数会有一个入参 state ，是当前实例的 state ，等价于 store.$state ，用法如下：

```ts
// 这里会打印出修改前的值
console.log(JSON.stringify(store.$state))
// 输出 {"message":"Hello World","randomMessages":[]}

/**
 * 注意这里，这次是传入了一个函数
 */
store.$patch((state) => {
  state.message = 'New Message'

  // 数组改成用追加的方式，而不是重新赋值
  for (let i = 0; i < 3; i++) {
    state.randomMessages.push(`msg${i + 1}`)
  }
})

// 这里会打印出修改后的值
console.log(JSON.stringify(store.$state))
// 输出 {"message":"New Message","randomMessages":["msg1","msg2","msg3"]}
```

> TIP
>
> 使用这个方式时，和 传入一个对象 一样只能修改已定义的数据，并且另外需要注意，传进去的函数只能是同步函数，不可以是异步函数！

##### 全量更新 state

用 store.$patch 方法对数据进行批量更新操作，不过如其命名，这种方式本质上是一种 “补丁更新” 。

虽然可以对所有数据都执行一次 “补丁更新” 来达到 “全量更新” 的目的，但 Pinia 也提供了一个更好的办法。

从前面多次提到 state 数据可以通过 store.$state 来拿到，而这个属性本身是可以直接赋值的。

假设 state 上现在有 message 和 randomMessages 这两个数据，那么要全量更新为新的值，就这么操作：

```ts
store.$state = {
  message: 'New Message',
  randomMessages: ['msg1', 'msg2', 'msg3'],
}
```

但是，必须遵循 state 原有的数据和对应的类型。该操作不会使 state 失去响应性。

##### 重置 state

Pinia 提供了一个 $reset API 挂在每个实例上面，用于重置整颗 state 树为初始数据：

```ts
// 这个 store 是上面定义好的实例
store.$reset()
```

具体例子：

```ts
// 修改数据
store.message = 'New Message'
console.log(store.message) // 输出 New Message

// 3s 后重置状态
setTimeout(() => {
  store.$reset()
  console.log(store.message) // 输出最开始的 Hello World
}, 3000)
```

##### 订阅 state

和 Vuex 一样， Pinia 也提供了一个用于订阅 state 的 $subscribe API 。

订阅 API 的 TS 类型

```ts
// $subscribe 部分的 TS 类型
// ...
$subscribe(
  callback: SubscriptionCallback<S>,
  options?: { detached?: boolean } & WatchOptions
): () => void
// ...
```

可以看到， `$subscribe` 可以接受两个参数：

1. 第一个入参是 callback 函数，必传
2. 第二个入参是一些选项，可选

同时还会返回一个函数，执行后可以用于移除当前订阅，下面来看看具体用法。

###### 添加订阅

$subscribe API 的功能类似于 watch ，但它只会在 state 被更新的时候才触发一次，并且在组件被卸载时删除（参考：组件的生命周期）。

从 订阅 API 的 TS 类型 可以看到，它可以接受两个参数，第一个参数是必传的 callback 函数，一般情况下默认用这个方式即可，使用例子：

```ts
// 可以在 state 出现变化时，更新本地持久化存储的数据
store.$subscribe((mutation, state) => {
  localStorage.setItem('store', JSON.stringify(state))
})
```

callback 里面有 2 个入参：

|   入参   |        作用        |
| :------: | :----------------: |
| mutation | 本次事件的一些信息 |
|  state   |  当前实例的 state  |

其中 mutation 包含了以下数据：

|  字段   | 值                                                           |
| :-----: | :----------------------------------------------------------- |
| storeId | 发布本次订阅通知的 Pinia 实例的唯一 ID（由 创建 Store 时指定） |
|  type   | 有 3 个值：|
|         | 返回 `direct` 代表 直接更改 数据；|
|         | 返回 `patch object` 代表是通过 传入一个对象 更改； |
|         | 返回 `patch function` 则代表是通过 传入一个函数 更改 |
| events  | 触发本次订阅通知的事件列表                                   |
| payload | 通过 传入一个函数 更改时，传递进来的荷载信息，只有 `type` 为 `patch object` 时才有 |

如果不希望组件被卸载时删除订阅，可以传递第二个参数 options 用以保留订阅状态，传入一个对象。

可以简单指定为 { detached: true } ：

```ts
store.$subscribe(
  (mutation, state) => {
    // ...
  },
  { detached: true }
)
```

也可以搭配 watch API 的选项一起用。

###### 移除订阅

默认情况下，组件被卸载时订阅也会被一并移除，但如果之前启用了 `detached` 选项，就需要手动取消。

前面在 订阅 API 的 TS 类型里提到，在启用 `$subscribe` API 之后，会有一个函数作为返回值，这个函数可以用来取消该订阅。

用法非常简单，做一下简单了解即可：

```ts
// 定义一个退订变量，它是一个函数
const unsubscribe = store.$subscribe(
  (mutation, state) => {
    // ...
  },
  { detached: true }
)

// 在合适的时期调用它，可以取消这个订阅
unsubscribe()
```

跟 watch API 的机制非常相似， 它也是返回一个取消侦听的函数 用于移除指定的 watch 。

### 管理 getters

 Pinia 的 `getters` 是用来计算数据的。

#### 给 Store 添加 getter

##### 添加普通的 getter

```ts
// src/stores/index.ts
import { defineStore } from 'pinia'

export const useStore = defineStore('main', {
  state: () => ({
    message: 'Hello World',
  }),
  // 定义一个 fullMessage 的计算数据
  getters: {
    fullMessage: (state) => `The message is "${state.message}".`,
  },
  // ...
})
```

和 Options API 的 Computed 写法一样，也是通过函数来返回计算后的值，

Getter 可以通过入参的 state 来拿到当前实例的数据（在 Pinia 里，官方更推荐使用箭头函数）。

##### 添加引用 getter 的 getter

有时候可能要引用另外一个 getter 的值来返回数据，

这个时候不能用箭头函数了，需要定义成普通函数而不是箭头函数，并在函数内部通过 this 来调用当前 Store 上的数据和方法。

继续在上面的例子里，添加多一个 emojiMessage 的 getter ，在返回 fullMessage 的结果的同时，拼接多一串 emoji 。

```ts
export const useStore = defineStore('main', {
  state: () => ({
    message: 'Hello World',
  }),
  getters: {
    fullMessage: (state) => `The message is "${state.message}".`,
    // 这个 getter 返回了另外一个 getter 的结果
    emojiMessage(): string {
      return `🎉🎉🎉 ${this.fullMessage}`
    },
  },
})
```

如果只写 JavaScript ，可能对这一条所说的限制觉得很奇怪，事实上用 JS 写箭头函数来引用确实不会报错，

但如果用的是 TypeScript ，不按照这个写法，在 VSCode 提示和执行 TSC 检查的时候都会给抛出一条错误

给 getter 传递参数

getter 本身是不支持参数的，但和 Vuex 一样，支持返回一个具备入参的函数，用来满足需求。

```ts
import { defineStore } from 'pinia'

export const useStore = defineStore('main', {
  state: () => ({
    message: 'Hello World',
  }),
  getters: {
    // 定义一个接收入参的函数作为返回值
    signedMessage: (state) => {
      return (name: string) => `${name} say: "The message is ${state.message}".`
    },
  },
})
```

```ts
const signedMessage = store.signedMessage('Petter')
console.log('signedMessage', signedMessage)
// Petter say: "The message is Hello World".
```

这种情况下，这个 getter 只是调用的函数的作用，不再有缓存，

如果通过变量定义了这个数据，那么这个变量也只是普通变量，不具备响应性。

```ts
// 通过变量定义一个值
const signedMessage = store.signedMessage('Petter')
console.log('signedMessage', signedMessage)
// Petter say: "The message is Hello World".

// 2s 后改变 message
setTimeout(() => {
  store.message = 'New Message'

  // signedMessage 不会变
  console.log('signedMessage', signedMessage)
  // Petter say: "The message is Hello World".

  // 必须这样再次执行才能拿到更新后的值
  console.log('signedMessage', store.signedMessage('Petter'))
  // Petter say: "The message is New Message".
}, 2000)
```

#### 获取和更新 getter

getter 和 state 都属于数据管理，读取和赋值的方法是和获取和更新 state 一样的

### 管理 actions

 Pinia 只需要用 actions 就可以解决各种数据操作，无需像 Vuex 一样区分为 mutations / actions 两大类。

#### 给 Store 添加 action

可以为当前 Store 封装一些可以开箱即用的方法，支持同步和异步。

```ts
// src/stores/index.ts
import { defineStore } from 'pinia'

export const useStore = defineStore('main', {
  state: () => ({
    message: 'Hello World',
  }),
  actions: {
    // 异步更新 message
    async updateMessage(newMessage: string): Promise<string> {
      return new Promise((resolve) => {
        setTimeout(() => {
          // 这里的 this 是当前的 Store 实例
          this.message = newMessage
          resolve('Async done.')
        }, 3000)
      })
    },
    // 同步更新 message
    updateMessageSync(newMessage: string): string {
      // 这里的 this 是当前的 Store 实例
      this.message = newMessage
      return 'Sync done.'
    },
  },
})
```

#### 调用 action

像普通的函数一样使用即可，不需要和 Vuex 一样执行 commit 或者 dispatch 。

```ts
export default defineComponent({
  setup() {
    const store = useStore()
    const { message } = storeToRefs(store)

    // 立即执行
    console.log(store.updateMessageSync('New message by sync.'))

    // 3s 后执行
    store.updateMessage('New message by async.').then((res) => console.log(res))

    return {
      message,
    }
  },
})
```

### 添加多个 Store

实际项目中会涉及到很多数据操作，还可以用多个 Store 来维护不同需求模块的数据状态。

这一点和 Vuex 的 Module 比较相似，目的都是为了避免状态树过于臃肿，但用起来会更为简单。

#### 目录结构建议

建议统一存放在 `src/stores` 下面管理，根据业务需要进行命名，比如 `user` 就用来管理登录用户相关的状态数据。

```bash
src
└─stores
  │ # 入口文件
  ├─index.ts
  │ # 多个 store
  ├─user.ts
  ├─game.ts
  └─news.ts
```

里面暴露的方法就统一以 `use` 开头加上文件名，并以 `Store` 结尾，作为小驼峰写法，

比如 `user` 这个 Store 文件里面导出的函数名就是：

```ts
// src/stores/user.ts
export const useUserStore = defineStore('user', {
  // ...
})
```

然后以 `index.ts` 里作为统一的入口文件， `index.ts` 里的代码写为：

```ts
export * from './user'
export * from './game'
export * from './news'
```

这样在使用的时候，只需要从 `@/stores` 里导入即可，无需写完整的路径，例如，只需要这样：

```ts
import { useUserStore } from '@/stores'
```

而无需这样：

```ts
import { useUserStore } from '@/stores/user'
```

#### 在 Vue 组件 / TS 文件里使用

这里以一个比较简单的业务场景举例，希望能够方便的理解如何同时使用多个 Store 。

假设目前有一个 `userStore` 是管理当前登录用户信息， `gameStore` 是管理游戏的信息，

而 “个人中心” 这个页面需要展示 “用户信息” ，以及 “该用户绑定的游戏信息”，那么就可以这样：

```ts
import { defineComponent, onMounted, ref } from 'vue'
import { storeToRefs } from 'pinia'
// 这里导入要用到的 Store
import { useUserStore, useGameStore } from '@/stores'
import type { GameItem } from '@/types'

export default defineComponent({
  setup() {
    // 先从 userStore 获取用户信息（已经登录过，所以可以直接拿到）
    const userStore = useUserStore()
    const { userId, userName } = storeToRefs(userStore)

    // 使用 gameStore 里的方法，传入用户 ID 去查询用户的游戏列表
    const gameStore = useGameStore()
    const gameList = ref<GameItem[]>([])
    onMounted(async () => {
      gameList.value = await gameStore.queryGameList(userId.value)
    })

    return {
      userId,
      userName,
      gameList,
    }
  },
})
```

再次提醒，切记每个 Store 的 ID 必须不同，

如果 ID 重复，在同一个 Vue 组件 / TS 文件里定义 Store 实例变量的时候，会以先定义的为有效值，后续定义的会和前面一样。

如果先定义了 userStore :

```ts
// 假设两个 Store 的 ID 一样
const userStore = useUserStore() // 是想要的 Store
const gameStore = useGameStore() // 得到的依然是 userStore 的那个 Store
```

如果先定义了 gameStore :

```ts
// 假设两个 Store 的 ID 一样
const gameStore = useGameStore() // 是想要的 Store
const userStore = useUserStore() // 得到的依然是 gameStore 的那个 Store
```

#### Store 之间互相引用

如果在定义一个 Store 的时候，要引用另外一个 Store 的数据，也是很简单，

回到那个 message 的例子，添加一个 getter ，它会返回一句问候语欢迎用户：

```ts
// src/stores/message.ts
import { defineStore } from 'pinia'

// 导入用户信息的 Store 并启用它
import { useUserStore } from './user'
const userStore = useUserStore()

export const useMessageStore = defineStore('message', {
  state: () => ({
    message: 'Hello World',
  }),
  getters: {
    // 这里就可以直接引用 userStore 上面的数据了
    greeting: () => `Welcome, ${userStore.userName}!`,
  },
})
```

假设现在 `userName` 是 Petter ，那么会得到一句对 Petter 的问候：

```ts
const messageStore = useMessageStore()
console.log(messageStore.greeting) // Welcome, Petter!
```

#### 专属插件的使用

Pinia 拥有非常灵活的可扩展性，有专属插件可以开箱即用满足更多的需求场景。

##### 如何查找插件

插件有统一的命名格式 `pinia-plugin-*` ，所以可以在 npmjs 上搜索这个关键词来查询目前有哪些插件已发布。

点击查询： [pinia-plugin - npmjs](https://www.npmjs.com/search?q=pinia-plugin)

##### 如何使用插件

这里以 [pinia-plugin-persistedstate](https://www.npmjs.com/package/pinia-plugin-persistedstate) 为例，这是一个让数据持久化存储的 Pinia 插件。

> TIP
>
> 数据持久化存储，指页面关闭后再打开，浏览器依然可以记录之前保存的本地数据，例如：浏览器原生的 [localStorage](https://developer.mozilla.org/zh-CN/docs/Web/API/Window/localStorage) 和 [IndexedDB](https://developer.mozilla.org/zh-CN/docs/Web/API/IndexedDB_API) ，
>
> 或者是一些兼容多种原生方案并统一用法的第三方方案，例如： [localForage](https://github.com/localForage/localForage) 。

插件也是独立的 npm 包，需要先安装，再激活，然后才能使用。

激活方法会涉及到 Pinia 的初始化过程调整，这里不局限于某一个插件，通用的插件用法如下（请留意代码注释）：

```ts
// src/main.ts
import { createApp } from 'vue'
import App from '@/App.vue'
import { createPinia } from 'pinia' // 导入 Pinia
import piniaPluginPersistedstate from 'pinia-plugin-persistedstate' // 导入 Pinia 插件

const pinia = createPinia() // 初始化 Pinia
pinia.use(piniaPluginPersistedstate) // 激活 Pinia 插件

createApp(App)
  .use(pinia) // 启用 Pinia ，这一次是包含了插件的 Pinia 实例
  .mount('#app')
```

##### 使用前

Pinia 默认在页面刷新时会丢失当前变更的数据，没有在本地做持久化记录：

```ts
// 其他代码省略
const store = useMessageStore()

// 假设初始值是 Hello World
setTimeout(() => {
  // 2s 后变成 Hello World!
  store.message = store.message + '!'
}, 2000)

// 页面刷新后又变回了 Hello World
```

##### 使用后

按照 persistedstate 插件的文档说明，在其中一个 Store 启用它，只需要添加一个 `persist: true` 的选项即可开启：

```ts
// src/stores/message.ts
import { defineStore } from 'pinia'
import { useUserStore } from './user'

const userStore = useUserStore()

export const useMessageStore = defineStore('message', {
  state: () => ({
    message: 'Hello World',
  }),
  getters: {
    greeting: () => `Welcome, ${userStore.userName}`,
  },
  // 这是按照插件的文档，在实例上启用了该插件，这个选项是插件特有的
  persist: true,
})
```

回到的页面，现在这个 Store 具备了持久化记忆的功能了，

它会从 localStorage 读取原来的数据作为初始值，每一次变化后也会将其写入 localStorage 进行记忆存储。

```ts
// 其他代码省略
const store = useMessageStore()

// 假设初始值是 Hello World
setTimeout(() => {
  // 2s 后变成 Hello World!
  store.message = store.message + '!'
}, 2000)

// 页面刷新后变成了 Hello World!!
// 再次刷新后变成了 Hello World!!!
// 再次刷新后变成了 Hello World!!!!
```

可以在浏览器查看到 localStorage 的存储变化，以 Chrome 浏览器为例，按 F12 ，打开 Application 面板，选择 Local Storage ，

可以看到以当前 Store ID 为 Key 的存储数据。



































































