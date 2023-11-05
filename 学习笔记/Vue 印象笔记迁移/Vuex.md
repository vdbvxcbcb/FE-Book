## Vuex

全局状态管理模式：把多个组件共用的数据抽离出来，通过一个单例模式进行管理。

状态是变量，也是数据。

Vuex 是具备【全局状态管理模式】的库，是对象，也是状态的管家。

![3.jpg](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1653726872217-f0bfc3f3-5131-4e08-b09f-f7820a365325.jpeg)



![3 [1].jpg](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1653726846123-58efde13-1d4f-4aed-9fa2-c4ccc95873ee.jpeg)

以上代码虽然可以做到数据共享，但是无法实现响应式

![img](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1653726846272-488d4048-afb6-4043-8aac-2b1017f12b8a.jpeg)



![img](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1653726846276-761345f4-024b-4af0-aa25-b2105c6a0b49.jpeg)



![img](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1653726846271-dd353081-0562-41e3-bbf6-d1568e59c7c0.jpeg)



![img](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1653726846877-a41bfa64-db6c-47a5-8ce1-301bb2ac7a5c.jpeg)



![img](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1653726847580-9fa972df-9d71-42b2-a4c0-67ed2649b9a9.jpeg)

### 安装 Vuex

![img](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1653726847752-53b7cbbe-e1e1-4c53-8a22-b0114cd04c62.jpeg)

![img](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1653726847765-5bb4b6a1-224a-4d29-b77d-f48f358407f3.jpeg)

![img](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1653726847805-c0a7723f-e7bc-4385-8878-de9b42075e02.jpeg)

| State            状态      | 存储状态，供全局使用                                   |
| :------------------------- | ------------------------------------------------------ |
| Mutations    改变          | 负责跟踪哪个页面组件修改了 state                       |
| Actions   操作（可有可无） | 负责异步操作，向后端 API 发送请求                      |
| Devtools                   | Vue 开发的浏览器插件，跟踪多个页面的修改，防止修改错误 |



![img](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1653726848199-50bca4de-4872-4bd1-b8b2-db4f3569d6eb.jpeg)

![img](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1653726848555-2b636da2-1f98-464a-bc0a-55bf231ef0b3.jpeg)

store文件夹 index.js

```js
import Vue from 'vue'
import Vuex from 'vuex'

Vue.use(Vuex);

const store = new Vuex.Store(
  {
    state: {
      count: 0
    },
    getters: {},
    mutations: {
      
    },
    actions: {
      
    }
  }
)
 
export default store
```

main.js

```js
import Vue from 'vue'
import App from './App'
import router from './router'
import store from "./store"

Vue.config.productionTip = false

new Vue({
  el: '#app',
  router,
  store, // 把 store 对象添加到 vue 实例上 
  render: h => h(App)
})
```

APP.vue

官方建议 this.$store.state.XXX 最好放在计算属性中

```vue
<template>
  <div id="app">
    <h2>app组件的内容</h2>
    <p>{{$store.state.count}}</p>
    <button @click="increase">+1</button>
    <button @click="decrease">-1</button>
  </div>
</template>

<script>
  export default {
    name: 'App',
    methods: {
      increase() {
        return this.$store.state.count++;
      },
      decrease() {
        return this.$store.state.count--;
      }
    }
  }
</script>

<style>
</style>
```

![3.gif](https://cdn.nlark.com/yuque/0/2022/gif/1614731/1653726872095-9cdd057e-4283-44bf-bf45-37c3658c91b2.gif)



![img](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1653726848809-4a4268ab-ec00-4b58-924e-d28a7603cb46.jpeg)

![img](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1653726848842-5b17068c-5ab2-4204-bdba-825a98c1627e.jpeg)

<img src="https://cdn.nlark.com/yuque/0/2022/gif/1614731/1653726846564-e0267cab-aec6-4e5b-b0b5-59fa1fe728a3.gif" alt="3 [1].gif" style="zoom:67%;" />

###  state 单一状态树的理解

![img](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1653726849122-efe56f5e-8c27-45d0-a512-841952fd4ba2.jpeg)

![3 [15].jpg](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1653726849507-abf1f96d-aa20-4ca0-ae45-b95ced64fba3.jpeg)

只建一个 Store，不然不方便维护

### getter 的使用详解

Vuex 允许我们在 store 中定义“getter”（可以认为是 store 的计算属性）。

就像计算属性一样，getter 的返回值会根据它的依赖被缓存起来，且只有当它的依赖值发生了改变才会被重新计算。

![img](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1653726849670-9516f225-a45f-4912-a0df-4401d0339844.jpeg)

![img](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1653726849990-89125eed-a541-473b-bf28-91145625313c.jpeg)

![3 [18].jpg](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1653726850156-ac6820e4-0f8a-405a-8933-a7e90a23877e.jpeg)

![3 [19].jpg](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1653726850183-cf8b3926-c28a-4dec-bc82-468e347fac87.jpeg)

![3 [20].jpg](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1653726850703-d9db2d75-899d-496a-a6e1-ebd549dd7554.jpeg)

![3 [21].jpg](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1653726851056-1feffe3e-b2a2-4117-b260-eecf3c399fb2.jpeg)

### mutations 携带参数

Vuex 中的 mutation 非常类似于事件：每个 mutation 都有一个字符串的 事件类型 (type) 和 一个 回调函数 (handler)。

这个回调函数就是我们实际进行状态更改的地方，并且它会接受 state 作为第一个参数：

```js
const store = new Vuex.Store({
  state: {
    count: 1
  },
  mutations: {
    increment (state) {
      // 变更状态
      state.count++
    }
  }
})
```

你不能直接调用一个 mutation 的 handler。这个参数更像是事件注册：“当一个类型为 increment 的 mutation 被唤醒时，调用此函数。”

要唤醒一个 mutation handler，你需要以相应的 type 调用 store.commit 方法：

```js
store.commit('increment')
```

![img](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1653726851052-0900df9f-4dbc-4480-8ec8-dc7571554d84.jpeg)

<img src="https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1653726851573-d101cb65-5f53-4402-a814-cc84eaad5337.jpeg" alt="img" style="zoom:67%;" />

![img](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1653726851855-fc8026d9-2941-4a5e-b675-2853ca85e537.jpeg)

![img](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1653726851995-5f90bc03-a2b3-4772-bdc3-67f2018452d3.jpeg)

![img](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1653726852332-8008d681-3928-49df-a9a8-5981c38288f6.jpeg)

### Vuex 数据的响应规则

![img](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1653726852441-3f8eea19-658e-4539-812e-cbbea1134f83.jpeg)



![img](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1653726852741-705572c4-fdb3-4b24-a3da-416ccd43a669.jpeg)



![img](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1653726853895-fe7dac71-899b-4630-a906-d23a42e89eda.jpeg)



![img](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1653726853849-b1c82102-36a6-4e9e-a54c-cd18ccd9de57.jpeg)



![img](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1653726853844-16284244-23de-4261-a96d-0fe30a094029.jpeg)

删除的响应式

![img](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1653726853888-7d535ff8-2c8b-4bc5-a693-f1773bbb64de.jpeg)

### mutations  常量

![img](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1653726854413-227636b8-12b9-4fbf-b726-bb23655c7dab.jpeg)

例如下列代码定义了很多方法

![3 [34].jpg](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1653726854635-7781fc16-0f1a-4d30-926c-35ded56b4884.jpeg)

在 commit 时写方法名往往容易出错

![3 [35].jpg](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1653726854928-66179f52-0267-4ba0-bea1-e62b66bbfc4b.jpeg)

如果 mutations-type.js 里常量 INCREMENT 的值 'increment '是错的，也不会影响 mutations 

![3 [36].jpg](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1653726855117-1bebfe85-6331-4668-b919-3404160fee58.jpeg)

![img](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1653726855238-543b2e0d-bfdb-4b1f-b779-333e27dffe32.jpeg)

![img](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1653726855642-2a18084a-9949-4a73-8c88-cafa9883c27e.jpeg)



![img](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1653726856119-5cb1cad8-36df-4a26-82bd-cd4227b272c9.jpeg)

![img](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1653726856277-6ba40885-f732-4f5f-b287-ae2b98f02fa9.jpeg)

### mutations 同步函数

![img](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1653726856522-70ddc206-9152-4656-8302-8fc562f284f3.jpeg)

![img](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1653726856555-c2b8ce7f-bf7d-4eda-a51d-6ed092b36c96.jpeg)

![img](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1653726856903-5bbaae8e-0e0e-48b3-ab92-dd9f95d7324b.jpeg)

![img](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1653726857540-0a564f39-33e8-4b1d-927b-794d3d929f5e.jpeg)

如果要进行异步操作，必须：

![img](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1653726857672-9e81d680-1315-4876-ae1b-e58cae2b6a64.jpeg)

### action 的使用详解

actions 的 函数接受一个与 store 实例具有相同方法和属性的 context 对象，

因此你可以调用 context.commit 提交一个 mutation，

或者通过 context.state 和 context.getters 来获取 state 和 getters。

context 对象其实不是 store 实例本身，当介绍到 Modules 时，你就知道了 。

实践中，我们会经常用到 ES2015 的 参数解构 来简化代码，（特别是我们需要调用 commit 很多次的时候，可以少写点代码）：

因为 context 是对象，所以可以解构成 {commit}

```js
actions: {
  increment ({ commit }) {
    commit('increment')
  }
}
```

![img](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1653726857829-92fa1eb3-0483-4571-85ed-f9e5605d3d44.jpeg)

![img](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1653726858033-1d006e00-bbfd-4eb0-9b8f-b5a87d3e2f9d.jpeg)

![img](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1653726857964-29c9a797-d786-4466-990d-6daf58dd5c77.jpeg)

![img](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1653726858906-c971f8fd-4887-43b0-aea9-3d12b78768a0.jpeg)

![img](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1653726859288-0ea46144-7ab8-4087-8c53-251e04863906.jpeg)

#### 异步操作完成时发送通知信息

第一种写法

![img](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1653726859395-9c0f747b-0b88-40e0-9a1e-786df6ca7d0b.jpeg)

![img](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1653726860060-8ebf8d68-4b87-422a-821c-cd8337641276.jpeg)

第二种写法

![img](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1653726860133-4cbb8c6d-a926-4b29-a650-f6efd1334fcc.jpeg)



![img](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1653726860182-1e11d80a-5331-40b4-a952-b37a7e737120.jpeg)



![img](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1653726860349-7f588b3f-e91a-4242-8156-12c395859fa9.jpeg)

### 实际应用

1、提取一个 store 对象，保存多个组件共享的状态

src\store\index.js

```js
import Vue from 'vue'
import Vuex from 'vuex'
import mutations from './mutations';
import actions from './actions';
import getters from './getters';

Vue.use(Vuex)

const state = {
  cartList: []
};

const store = new Vuex.Store({
  state,
  mutations,
  actions,
  getters,
  modules: {}
});

export default store
```

src\store\mutations-type.js

```js
export const ADD_COUNTER = 'addCounter';
export const ADD_TO_CART = 'addToCart';
```

src\store\mutations.js

```js
import {ADD_COUNTER, ADD_TO_CART} from './mutations-type'

export default {
  [ADD_COUNTER](state, payload) {
    payload.count++;
  },
  [ADD_TO_CART](state, payload) {
    payload.checked = true; 
    state.cartList.push(payload);
  }
};
```

src\store\actions.js

```js
import { ADD_COUNTER, ADD_TO_CART } from "./mutations-type";

export default {
  addCart({ commit, state }, payload) {
    // 查找之前是否有该商品，cartList 中有该商品，就赋值给 oldProduct
    // 之前有该商品就将该商品的数量加 1
    return new Promise((resolve, reject) => {
      let oldProduct = state.cartList.find(item => item.iid === payload.iid);
      if (oldProduct) {
        // oldProduct.count += 1;
        commit(ADD_COUNTER, oldProduct);
        resolve('当前商品数量+1')
      }
      // 之前没有该商品就该商品的数量设置为 1 并添加到购物车
      else {
        payload.count = 1;
        // state.cartList.push(payload);
        commit(ADD_TO_CART, payload);
        resolve('添加了新的商品')
      }
    })
  }
}
```

2、引入并挂载到全局的 Vue 实例下

src\main.js

```js
import Vue from 'vue'
import App from './App.vue'
import router from './router'
import store from './store'

import toast from 'common/toast';

Vue.config.devtools = true;
Vue.config.productionTip = false;

new Vue({
  router,
  store, // 把 store 对象添加到 vue 实例上 
  render: h => h(App)
}).$mount('#app')
```

src\views\detail\Detail.vue

```vue
<template>
  <div id="detail">
    <detail-bottom-bar @addToCart="addToCart"></detail-bottom-bar>
  </div>
</template>

<script>
  import DetailBottomBar from './childCpns/DetailBottomBar';
  import{mapActions} from 'vuex';

  export default {
    name: 'Detail',
    data () {
      return {
        iid: null,
        topImages: [],
        goods: {},
      }
    },
    methods: {
      ...mapActions(['addCart']),
      addToCart() {
        // 获取购物车要展示的商品信息
        const product = {};
        product.image = this.topImages[0];
        product.title = this.goods.title;
        product.desc = this.goods.desc;
        product.price = this.goods.lowNowPrice;
        product.iid = this.iid;
        // 将商品添加到购物车
        // this.$store.dispatch('addCart', product);
        this.addCart(product).then(res => {
          this.$toast(res, 1500)
        })
      }
    },
    components: {
      DetailBottomBar,
    }
  }
</script>
```

3、

this.$store.dispatch("action 方法", 参数)  actions 异步操作（涉及异步时才用） =>

组件 methods 里直接 this.$store.commit(''mutation方法'', 参数) 

=> mutation 对应的 方法(state, payload) 修改 state （也可修改 paylaod）

### 总结流程

（$store 表示 this.$store，此处省略）

1、state 存储数据 => 

2、getters 的方法(state) 变异数据 =>

3、模板 

```js
{{$store.state.xxx}} 
```

展示 getters 后的数据 =>

4、组件 methods 的 $store.diapatch('异步方法', payload) => actions 的异步方法(context, payload) => （这步可省略，需要异步处理时使用）

5、actions 的异步方法 context.commit('同步方法'或常量类型, 参数)  （这步也可省略，需要异步处理时使用）

或 组件 methods  的 $store.commit('同步方法'或常量类型, 参数) => 

6、mutations 的同步方法 `[常量类型对象的方法名](state, 参数)`  => 修改 state

7、getters 变异数据 => 模板 

```js
{{$store.state.xxx}} 
```

展示 getters 后数据 => ...

### module 的使用详解

由于只能使用单一状态树的 store，不能定义多个 state、mutations、actions、getters，所以还提供了另一个方案 —— modules

第一种方式

![img](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1653726860889-34cc7b53-ed79-443f-b93c-e01da367d94a.jpeg)

第二种方式

模块的 state

![img](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1653726861080-a7bddbd4-9ea1-42cb-86ab-8cb3fd457d66.jpeg)

![img](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1653726861239-c7255a07-3816-4ecd-a323-ba2bda2f5cfe.jpeg)

![img](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1653726861742-6a522515-cbe7-4f10-ad48-f97e1890a96f.jpeg)

![3 [60].jpg](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1653726862143-b8dec7cf-aa58-40bc-b14c-a1b7f288bd70.jpeg?x-oss-process=image%2Fresize%2Cw_1022%2Climit_0)

#### 模块的 mutations

模块的 mutations 不能有与 new Vue.Store 的 mutaions 同名的情况，

this.store.commit('updateName', 'lisi') 时，

会优先在 new Vuex.Store 的 mutaions 找有没有 updateName 函数，没有的话，就会去模块的 mutations 中找

![img](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1653726862450-8133e54e-f24a-44d8-9deb-17cf34e9c6aa.jpeg)

![img](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1653726862504-ed658a36-9dba-4da4-afb8-d008323aaf8e.jpeg)

![img](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1653726862870-9ad71251-d7ed-49bd-8e58-507ee7dbec89.jpeg)

![img](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1653726863409-589638e9-8563-48d3-b4bd-1f34baf0f60c.jpeg)

![img](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1653726863472-de70f16d-55be-4d1b-923a-96489b2ec4df.jpeg)

![img](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1653726864205-eb29f282-7f25-44be-8cfd-961a14613b8c.jpeg)

#### 模块的 getters

![img](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1653726864521-e362dd92-ab53-459f-958b-baf1f96b19fe.jpeg)

![img](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1653726864606-12ae6c3f-e4f6-4a3a-b7a0-efafd6f9c857.jpeg)

![img](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1653726864595-0bcba2e4-e5c3-4aef-9865-897af4bd0f6e.jpeg)

![img](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1653726864887-1ffe5fed-85cf-4993-9abe-4762be37c73a.jpeg)

#### 拼接 store 的 counter

![img](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1653726865736-f8fb22d9-979d-4f64-bf7a-e5a13daee7cb.jpeg)

![img](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1653726866087-ffabd8b5-50d5-407f-b294-8b23c1956898.jpeg)

![img](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1653726866088-1e30b658-2774-4220-9f36-9105763f2923.jpeg)

#### 模块 actions 

模块 actions 的 context 表示该模块 （module），commit 针对的是该模块的 mutations

![img](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1653726866367-31eae814-fe3c-4257-ac91-1eaf16062dda.jpeg)



![img](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1653726866673-2e3be8a7-4281-4e43-bd2f-7bcac8567a6b.jpeg)



![img](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1653726866994-b867f24d-24fe-4d62-a805-5ab5033aa364.jpeg)



![img](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1653726867311-d948defd-8ad3-4f98-a42e-37c9e85c6b97.jpeg)



![img](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1653726867679-b7e4f95d-e692-426d-9667-e07e62f252c9.jpeg)



![img](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1653726867807-e61dc985-2dfb-4a34-8f7d-af4fdd8e3aed.jpeg)

![img](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1653726868554-f5eac307-92df-445b-95f3-0a00339a01a4.jpeg)

对象的解构

![img](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1653726868726-3f22b904-041b-4bc5-a129-903c676b3473.jpeg)

### store 文件夹的目录组织

![img](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1653726868770-ac4ef997-ed3a-4f7b-bcfd-80d779459f0c.jpeg)

#### 抽离 state

![img](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1653726868822-37d436bd-188b-4616-b666-431ea51814c9.jpeg)

#### 抽离 mutations（需要再创建一个文件）

![img](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1653726868954-991289b9-f7b1-49da-aaed-ce01a122f623.jpeg)

![img](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1653726869315-15c60c24-1a3b-4556-8561-1c888bcf38cd.jpeg)



![3 [86].jpg](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1653726870202-efc14d7f-6865-48a2-a778-88c82f7592b4.jpeg)

#### 抽离 actions 和 getters 

![img](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1653726870309-fe04f7ac-7986-4e27-a89b-1c81ad87c12c.jpeg)

![img](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1653726870479-e786ef44-bdd9-4156-acf2-bafacc0e4e0c.jpeg)

![img](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1653726870639-56bf86cd-67be-4d2d-9a04-dabe988b5cc7.jpeg)

#### 抽离 moduleA

![img](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1653726870880-07fd6ca3-de43-416d-bfaa-fd0e8e253182.jpeg)

![img](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1653726871610-f658edbf-3819-44bb-952c-f4854a3657f3.jpeg)

![img](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1653726871753-c1e6aa6b-b709-4bc4-9395-d7ec471fd07e.jpeg)

全部抽离之后：

![img](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1653726871888-c5e8e75b-d6a3-448c-8f7a-54dcaa5ce49e.jpeg)

### mapXxx 简化操作 

1、每次都写 this.$store.state.XXX 和 this.$store.getters.XXX 很麻烦，

官方建议我们可以使用 mapState 和 mapGetters 解构到计算属性中，就可以直接使用 this 调用

```html
<script>
import { mapState, mapGetters } from 'vuex';
export default {
    mounted() {
        console.log(this.name);
        console.log(this.getMessage);
    },
    computed: {
        ...mapState(['name']),
        ...mapGetters(['getMessage']),
    },
}
</script>
```

解构的时候可以赋别名

```js
...mapState({ aliasName: 'name' }),  // 赋别名的话，这里接收对象，而不是数组
...mapGetters({ aliasName: 'getMessage' }),  // 赋别名的话，这里接收对象，而不是数组
```

2、可以使用 mapActions 代替 this.$store.dispatch('XXX') 调用 action，把相关的 actions 解构到 methods 中，用 this 直接调用

```html
<script>
import { mapActions } from 'vuex';
export default {
    methods: {
        ...mapActions(['setNum']),   // 就像这样，解构到methods中
    },
    async mounted() {
        await this.setNum({ number: 123 });  // 直接这样调用即可
    },
}
</script>
```

解构的时候可以赋别名

```js
methods: {
  ...mapActions({ setNumAlias: 'setNum' }),   // 赋别名的话，这里接收对象，而不是数组
}
```

3、像 mapState 和 mapGetters 一样，我们在组件中可以使用 mapMutations 代替 this.$store.commit('XXX')

```html
<script>
import { mapMutations } from 'vuex';
export default {
    mounted() {
        this.setNumberIsWhat({ number: 999 });
    },
    methods: {   // 注意，mapMutations是解构到 methods 里面的，而不是计算属性
        ...mapMutations(['setNumberIsWhat']),
    },
}
</script>
```

解构的时候可以赋别名

```js
methods: {
    ...mapMutations({ setNumberIsAlias: 'setNumberIsWhat' }),   // 赋别名的话，这里接收对象，而不是数组
}
```













































































































































































































































































































