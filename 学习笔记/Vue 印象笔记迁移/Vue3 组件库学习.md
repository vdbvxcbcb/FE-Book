```sh
npm create vite@latest imooc-vue3-components -- --template vue-ts

cd imooc-vue3-components
npm install
npm run dev
```

## 配置路由和 element-plus，全局注册图标

src\router\index.ts

```ts
import { createRouter, createWebHistory, RouteRecordRaw } from 'vue-router';
import Home from '../views/home.vue';
import Container from '../components/container/src/index.vue';

const routes: RouteRecordRaw[] = [
  {
    path: '/',
    component: Container,
    children: [
      {
        path: '/',
        component: Home,
      },
      {
        path: '/chooseIcon',
        component: () => import('../views/chooseIcon/index.vue'),
      },
      {
        path: '/chooseArea',
        component: () => import('../views/chooseArea/index.vue'),
      },
      {
        path: '/trend',
        component: () => import('../views/trend/index.vue'),
      },
      {
        path: '/notification',
        component: () => import('../views/notification/index.vue'),
      },
      {
        path: '/menu',
        component: () => import('../views/menu/index.vue'),
      },
      {
        path: '/progress',
        component: () => import('../views/progress/index.vue'),
      },
      {
        path: '/chooseTime',
        component: () => import('../views/chooseTime/index.vue'),
      },
      {
        path: '/chooseCity',
        component: () => import('../views/chooseCity/index.vue'),
      },
      {
        path: '/form',
        component: () => import('../views/form/index.vue'),
      },
      {
        path: '/table',
        component: () => import('../views/table/index.vue'),
      },
    ],
  },
];

const router = createRouter({
  routes,
  history: createWebHistory(),
});

export default router;
```

src\utils\index.ts

```ts
// 为了将组件驼峰形式转横杠形式连接，将大写字母转小写 
export const toLine = (value: string) => {
  return value.replace(/A-Z/g, '$1').toLowerCase()
}
```

src\main.ts

```ts
import { createApp } from 'vue';
import App from './App.vue';
import router from './router/index';
import ElementPlus from 'element-plus';
import 'element-plus/dist/index.css';
import * as ElementPlusIconsVue from '@element-plus/icons-vue';
import { toLine } from './utils';

// 引入所有二次封装好的组件作为自己的组件库
import myUI from './components';

const app = createApp(App);
// 全局注册图标组件，ElementPlus 所有图标都是一个组件，牺牲一点性能
for (const [key, component] of Object.entries(ElementPlusIconsVue)) {
  app.component(`el-icon-${toLine(key)}`, component);
}
// 全局注册自己的组件库、路由
app.use(ElementPlus).use(myUI).use(router);
app.mount('#app');
```

src\App.vue

```vue
<template>
  <div>
    <router-view></router-view>
  </div>
</template>

<script setup lang="ts"></script>

<style>
  * {
    margin: 0;
    padding: 0;
  }
  svg {
    width: 1em;
    height: 1em;
  }
  html,
  body,
  #app,
  .el-container,
  .el-menu {
    height: 100vh;
  }
</style>
```

## 整体布局与菜单伸缩

src\components\container\src\index.vue

```vue
<template>
  <el-container>
    <el-aside width="auto">
       <!-- 左侧边栏 -->  
      <nav-side v-model:collapse="isCollapse"></nav-side>
    </el-aside>
    <el-container>
      <!-- 右侧头部 -->    
      <el-header>
        <nav-header v-model:collapse="isCollapse"></nav-header>
      </el-header>
      <el-main>
        <!-- 右侧内容区域显示 views 文件夹的视图，即 routes.children 嵌套子路由对应的组件 -->  
        <router-view></router-view>
      </el-main>
    </el-container>
  </el-container>
</template>

<script setup lang="ts">
  import { ref } from 'vue';
  import NavSide from './navSide/index.vue';
  import NavHeader from './navHeader/index.vue';
  const isCollapse = ref(false);
</script>

<style scoped>
  .el-header {
    padding: 0;
  }
</style>
```

src\components\container\src\navSide\index.vue

```vue
<template>
  <!-- 导航菜单组件，启用 vue-router 模式激活导航时以 index 作为 path ， $route.path 用于对应 data 里的 index -->
  <my-menu :collapse="collapse" :data="data" router :defaultActive="$route.path"></my-menu>
</template>

<script setup lang="ts">
  const props = defineProps<{
    // 控制侧边栏折叠
    collapse: boolean;
  }>();

  const data = [
    {
      icon: 'HomeFilled',
      name: '首页',
      index: '/',
    },
    {
      icon: 'Check',
      name: '图标选择器',
      index: '/chooseIcon',
    },
    {
      icon: 'Location',
      name: '省市区选择',
      index: '/chooseArea',
    },
    {
      icon: 'Sort',
      name: '趋势标记',
      index: '/trend',
    },
    {
      icon: 'Timer',
      name: '时间选择',
      index: '/chooseTime',
    },
    {
      icon: 'Bell',
      name: '通知菜单',
      index: '/notification',
    },
    {
      icon: 'Menu',
      name: '导航菜单',
      index: '/menu',
    },
    {
      icon: 'TurnOff',
      name: '城市选择',
      index: '/chooseCity',
    },
    {
      icon: 'DArrowRight',
      name: '进度条',
      index: '/progress',
    },
    {
      icon: 'ScaleToOriginal',
      name: '日历',
      index: '/calendar',
    },
    {
      icon: 'Setting',
      name: '表单',
      index: '/form',
    },
    {
      icon: 'Setting',
      name: '弹出框表单',
      index: '/modalForm',
    },
    {
      icon: 'ShoppingBag',
      name: '表格',
      index: '/table',
    },
  ];
</script>

<style scoped></style>
```

src\components\container\src\navHeader\index.vue

```vue
<template>
  <div class="header" @click="toggle">
    <el-icon-expand v-if="collapse"></el-icon-expand>
    <el-icon-fold v-else></el-icon-fold>
  </div>
</template>

<script setup lang="ts">
  const props = defineProps<{
    // 控制侧边栏折叠
    collapse: boolean;
  }>();
  const emits = defineEmits(['update:collapse']);
  const toggle = () => emits('update:collapse', !props.collapse);
</script>

<style scoped>
  .header {
    display: flex;
    align-items: center;
    height: 60px;
    padding: 0 20px;
    border-bottom: 1px solid #eee;
  }
</style>
```

## 全局注册组件库的组件

src\components\index.ts

```js
import { App } from 'vue';
import chooseArea from './chooseArea';
import chooseIcon from './chooseIcon';
import trend from './trend';
import notification from './notification';
import list from './list';
import menu from './menu';
import progress from './progress';
import chooseTime from './chooseTime';
import chooseDate from './chooseDate';
import chooseCity from './chooseCity';
import form from './form';
import modalForm from './modalForm';
import table from './table';
import calendar from './calendar';

const components = [chooseArea, chooseIcon, trend, notification, list, menu, progress, chooseTime, chooseDate, chooseCity, form, modalForm, table, calendar];

// 全局注册组件库里的组件
export default {
  install(app: App) {
    components.map((item) => {
      app.use(item);
    });
  },
};
```

## 导航菜单组件

### 全局注册导航菜单组件

src\components\menu\index.ts

```ts
import { App } from 'vue';
import menu from './src/index.vue';
import infiniteMenu from './src/menu';

// 让这个组件可以通过use的形式使用
export default {
  install(app: App) {
    app.component('MyMenu', menu);
    app.component('MyInfiniteMenu', infiniteMenu);
  },
};
```

### 实现两级导航菜单组件

src\components\container\src\navSide\index.vue

```vue
<!-- 渲染只有两级的菜单 -->
<template>
  <!-- v-bind="$attrs" 透传 attribute 绑定剩余 props -->
  <el-menu class="el-menu-vertical-demo" :default-active="defaultActive" :router="router" v-bind="$attrs">
    <template v-for="(item, i) in data" :key="i">
      <!-- 一级菜单 -->
      <el-menu-item v-if="!item[children] || !item[children].length" :index="item[index]">
        <!-- 图标 -->
        <component v-if="item[icon]" :is="`el-icon-${toLine(item[icon])}`"></component>
        <!-- 菜单标题 -->
        <span>{{ item[name] }}</span>
      </el-menu-item>
      <!-- 两级菜单 -->
      <el-sub-menu v-if="item[children] && item[children].length" :index="item[index]">
        <template #title>
          <component v-if="item[icon]" :is="`el-icon-${toLine(item[icon])}`"></component>
          <span>{{ item[name] }}</span>
        </template>
        <el-menu-item v-for="(item1, index1) in item[children]" :key="index1" :index="item1.index">
          <component v-if="item1[icon]" :is="`el-icon-${toLine(item1[icon])}`"></component>
          <span>{{ item1[name] }}</span>
        </el-menu-item>
      </el-sub-menu>
    </template>
  </el-menu>
</template>

<script lang="ts" setup>
  import { PropType } from 'vue';
  import { toLine } from '../../../utils';

  let props = defineProps({
    // 导航菜单的数据
    data: {
      // 因为可以自定义不同的 key 代替 name index icon children，所以定义为 any[]
      type: Array as PropType<any[]>,
      required: true,
    },
    // 默认选中的菜单
    defaultActive: {
      type: String,
      default: '',
    },
    // 是否是路由模式
    router: {
      type: Boolean,
      default: false,
    },
    // 键名
    // 菜单标题的键名
    name: {
      type: String,
      default: 'name',
    },
    // 菜单标识的键名
    index: {
      type: String,
      default: 'index',
    },
    // 菜单图标的键名
    icon: {
      type: String,
      default: 'icon',
    },
    // 菜单子菜单的键名
    children: {
      type: String,
      default: 'children',
    },
  });
</script>

<style lang="less" scoped>
  svg {
    margin-right: 4px;
    width: 1em;
    height: 1em;
  }
  .el-menu-vertical-demo:not(.el-menu--collapse) {
    width: 200px;
    min-height: 400px;
  }
</style>

```

### 实现无限级导航菜单组件

src\components\menu\src\types.ts

```ts
export interface MenuItem {
  // 导航的图标
  icon?: string;
  // 处理之后的图标
  i?: any;
  // 导航的名字
  name: string;
  // 导航的标识
  index: string;
  // 导航的子菜单
  children?: MenuItem[];
}
```

src\components\menu\src\styles\index.less

```less
.menu-icon-svg {
  svg {
    margin-right: 4px;
    width: 1em;
    height: 1em;
  }
}
```

src\components\menu\src\menu.tsx

```tsx
// 渲染无限层级的菜单
import { defineComponent, PropType, useAttrs } from 'vue'
import { MenuItem } from './types'
import * as Icons from '@element-plus/icons-vue'
import './styles/index.less'

export default defineComponent({
  props: {
    // 导航菜单的数据
    data: {
      type: Array as PropType<MenuItem[]>,
      required: true
    },
    // 默认选中的菜单
    defaultActive: {
      type: String,
      default: ''
    },
    // 是否是路由模式
    router: {
      type: Boolean,
      default: false
    },
    // 菜单标题的键名
    name: {
      type: String,
      default: 'name'
    },
    // 菜单标识的键名
    index: {
      type: String,
      default: 'index'
    },
    // 菜单图标的键名
    icon: {
      type: String,
      default: 'icon'
    },
    // 菜单子菜单的键名
    children: {
      type: String,
      default: 'children'
    },
  },
  setup(props, ctx) {
    // 封装一个渲染无限层级菜单的方法
    // 函数会返回一段 jsx 的代码
    const renderMenu = (data: any[]) => {
      return data.map((item: any) => {
        // 每个菜单的图标
        item.i = (Icons as any)[item[props.icon!]]
        // 处理 sub-menu 的默认插槽，二级以上菜单的图标和标题
        const slots = {
          title: () => {
            return <>
              <item.i />
              <span>{item[props.name]}</span>
            </>
          }
        }
        // 递归渲染 children
        if (item[props.children!] && item[props.children!].length) {
          return (
            <el-sub-menu index={item[props.index]} v-slots={slots}>
              {renderMenu(item[props.children!])}
            </el-sub-menu>
          )
        }
        // 正常渲染普通的菜单，最底层的菜单，即 
        // <el-sub-menu>
        //   <el-sub-menu>
        //     <el-sub-menu>
        //       <el-sub-menu>
        //         <el-menu-item></el-menu-item>
        //     </el-sub-menu>
        //   </el-sub-menu>
        // </el-sub-menu>
        return (
          <el-menu-item index={item[props.index]}>
            <item.i />
            <span>{item[props.name]}</span>
          </el-menu-item>
        )
      })
    }
    let attrs = useAttrs()
    return () => {
      return (
        <el-menu
          class="menu-icon-svg"
          default-active={props.defaultActive}
          router={props.router}
          {...attrs}
        >
          {renderMenu(props.data)}
        </el-menu>
      )
    }
  }
})
```

### 使用导航菜单组件

路由菜单显示内容

src\views\menu\index.vue

```vue
<template>
  <div style="display: flex">
    <div style="flex: 1">
      <my-menu :data="data1" defaultActive="1" name="a" index="b" icon="c" children="d"></my-menu>
    </div>
    <div style="flex: 1">
      <my-infinite-menu :data="data2" defaultActive="2" name="a" index="b" icon="c" children="d"></my-infinite-menu>
    </div>
  </div>
</template>

<script lang="ts" setup>
  // 可以使用不同的 key 分别代替 name index icon children ，这里用的是 a b c d
  const data1 = [
    {
      a: '导航1',
      b: '1',
      c: 'Document',
    },
    {
      a: '导航2',
      b: '2',
      c: 'Document',
    },
    {
      a: '导航3',
      b: '3',
      c: 'Document',
      d: [
        {
          a: '导航3-1',
          b: '3-1',
          c: 'Document',
        },
      ],
    },
  ];

  const data2 = [
    {
      a: '导航1',
      b: '1',
      c: 'Document',
    },
    {
      a: '导航2',
      b: '2',
      c: 'Document',
    },
    {
      a: '导航3',
      b: '3',
      c: 'Document',
      d: [
        {
          a: '导航3-1',
          b: '3-1',
          c: 'Document',
          d: [
            {
              a: '导航3-1-1',
              b: '3-1-1',
              c: 'Document',
              d: [
                {
                  a: '导航3-1-1-1',
                  b: '3-1-1-1',
                  c: 'Document',
                  d: [
                    {
                      a: '导航3-1-1-1-1',
                      b: '3-1-1-1-1',
                      c: 'Document',
                    },
                  ],
                },
              ],
            },
          ],
        },
      ],
    },
  ];
</script>

<style lang="less" scoped></style>
```

### 效果图

![Snipaste_2023-02-13_18-06-11.png](https://cdn.nlark.com/yuque/0/2023/png/1614731/1676282793770-284041d2-3a74-4a38-8b6a-af8de83cb624.png)

﻿

## 选择图标组件

### 全局注册选择图标组件

src\components\chooseIcon\index.ts

```ts
import { App } from 'vue';
import chooseIcon from './src/index.vue';

// 让这个组件可以通过 use 的形式使用
export default {
  install(app: App) {
    app.component('MyChooseIcon', chooseIcon);
  },
};
```

### 实现选择图标组件

src\hooks\useCopy\index.ts

```ts
import { ElMessage } from 'element-plus';

export const useCopy = (text: string) => {
  navigator.clipboard.writeText(text);
  ElMessage({
    message: '复制成功!',
    type: 'success',
  });
};
```

src\components\chooseIcon\src\index.vue

```vue
<template>
  <div class="choose-icon-content">
    <el-button @click="handleClick">
      <slot></slot>
    </el-button>
    <el-dialog :title="title" v-model="dialogVisible">
      <div class="container">
        <div class="item" v-for="(item, index) in Object.keys(ElIcons)" :key="index" @click="clickItem(item)">
          <div class="icon">
            <component :is="`el-icon-${toLine(item)}`"></component>
          </div>
          <div class="text">
            {{ item }}
          </div>
        </div>
      </div>
    </el-dialog>
  </div>
</template>

<script setup lang="ts">
  import { ref, watch } from 'vue';
  import * as ElIcons from '@element-plus/icons-vue';
  import { toLine } from '../../../utils';
  import { useCopy } from '../../../hooks/useCopy';

  const props = defineProps<{
    // 弹出框的标题
    title: string;
    // 控制弹出框的显示与隐藏
    visible: boolean;
  }>();
  const emits = defineEmits(['update:visible']);

  // 拷贝父组件传进来的 props
  const dialogVisible = ref<boolean>(props.visible);

  // 将值传给父组件，通知父组件更新 props
  const handleClick = () => {
    emits('update:visible', !props.visible);
  };

  // 点击图标复制组件字符串
  const clickItem = (item: string) => {
    const text = `<el-icon-${toLine(item)}/>`;
    useCopy(text);
    dialogVisible.value = false;
  };

  watch(
    // 父组件传进来的 props 更新，改变自己的状态 dialogVisible，用于传给子组件 el-dialog
    () => props.visible,
    (val) => {
      dialogVisible.value = val;
    },
  );

  // el-dialog 改变自己的状态 dialogVisible，自己再将值传给父组件
  watch(
    () => dialogVisible.value,
    (val) => {
      emits('update:visible', val);
    },
  );
</script>

<style scoped>
  .container {
    display: flex;
    align-items: center;
    flex-wrap: wrap;
  }

  .item {
    width: 25%;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    margin-bottom: 20px;
    cursor: pointer;
    height: 70px;
  }

  .icon {
    flex: 1;
  }

  .text {
    font-size: 14px;
  }

  svg {
    width: 2em;
    height: 2em;
  }

  .choose-icon-content :deep(.el-dialog__body) {
    height: 500px;
    overflow: scroll;
  }
</style>
```

src\components\chooseIcon\index.ts

```ts
import { App } from 'vue';
import chooseIcon from './src/index.vue';

// 让这个组件可以通过 use 的形式使用
export default {
  install(app: App) {
    app.component('MyChooseIcon', chooseIcon);
  },
};
```

### 使用选择图标组件

src\views\chooseIcon\index.vue

```vue
<template>
  <my-choose-icon title="选择图标" v-model:visible="visible"> 选择图标 </my-choose-icon>
</template>

<script setup lang="ts">
  import { ref } from 'vue';

  let visible = ref<boolean>(false);
</script>

<style scoped></style>
```

### 效果图

![Snipaste_2023-02-13_18-14-41.png](https://cdn.nlark.com/yuque/0/2023/png/1614731/1676283293177-eb20906c-c4f7-4ecd-a673-625c375e877e.png)

## 省市区选择组件

﻿pca-code.json

```json
[{"code":"11","name":"北京市","children":[{"code":"1101","name":"市辖区","children":[{"code":"110101","name":"东城区"},{"code":"110102","name":"西城区"},{"code":"110105","name":"朝阳区"},{"code":"110106","name":"丰台区"},{"code":"110107","name":"石景山区"},{"code":"110108","name":"海淀区"},{"code":"110109","name":"门头沟区"},{"code":"110111","name":"房山区"},{"code":"110112","name":"通州区"},{"code":"110113","name":"顺义区"},{"code":"110114","name":"昌平区"},{"code":"110115","name":"大兴区"},{"code":"110116","name":"怀柔区"},{"code":"110117","name":"平谷区"},{"code":"110118","name":"密云区"},{"code":"110119","name":"延庆区"}]}]},
...
]
```

### 全局注册省市区选择组件

src\components\chooseArea\index.ts

```ts
import { App } from 'vue';
import chooseArea from './src/index.vue';

// 让这个组件可以通过 use 的形式使用
export default {
  install(app: App) {
    app.component('MyChooseArea', chooseArea);
  },
};
```

### 实现省市区选择组件

src\components\chooseArea\src\index.vue

```vue
<template>
  <div>
    <el-select clearable placeholder="请选择省份" v-model="province">
      <el-option v-for="item in allAreasData" :key="item.code" :value="item.code" :label="item.name"></el-option>
    </el-select>
    <el-select clearable :disabled="!province" style="margin: 0 10px" placeholder="请选择城市" v-model="city">
      <el-option v-for="item in selectCity" :key="item.code" :value="item.code" :label="item.name"></el-option>
    </el-select>
    <el-select clearable :disabled="!province" placeholder="请选择区域" v-model="area">
      <el-option v-for="item in selectArea" :key="item.code" :value="item.code" :label="item.name"></el-option>
    </el-select>
  </div>
</template>

<script setup lang="ts">
  import { ref, watch } from 'vue';
  import allAreas from '../lib/pca-code.json';

  interface AreaItem {
    code: string;
    name: string;
    children?: AreaItem[];
  }

  interface Data {
    name: string;
    code: string;
  }

  // 所有的省市区数据
  let allAreasData = ref(allAreas);

  // 下拉框选择省份的值
  let province = ref<string>('');
  // 下拉框选择城市的值
  let city = ref<string>('');
  // 下拉框选择区域的值
  let area = ref<string>('');

  // 城市下拉框所有的值
  let selectCity = ref<AreaItem[]>([]);
  // 区域下拉框所有的值
  let selectArea = ref<AreaItem[]>([]);

  // 分发事件给父组件
  const emits = defineEmits(['change']);

  // 监听选择省份
  watch(
    () => province.value,
    (val) => {
      if (val) {
        let cities = allAreasData.value.find((item) => item.code === province.value)!.children!;
        selectCity.value = cities;
      }
      city.value = '';
      area.value = '';
    },
  );

  // 监听选择城市
  watch(
    () => city.value,
    (val) => {
      if (val) {
        let areas = selectCity.value.find((item) => item.code === city.value)!.children!;
        selectArea.value = areas;
      }
      area.value = '';
    },
  );

  // 监听选择区域
  watch(
    () => area.value,
    (val) => {
      if (val) {
        let provinceData: Data = {
          code: province.value,
          name: province.value && allAreasData.value.find((item) => item.code === province.value)!.name,
        };
        let cityData: Data = {
          code: city.value,
          name: city.value && selectCity.value.find((item) => item.code === city.value)!.name,
        };
        let areaData: Data = {
          code: val,
          name: val && selectArea.value.find((item) => item.code === val)!.name,
        };
        emits('change', {
          province: provinceData,
          city: cityData,
          area: areaData,
        });
      }
    },
  );
</script>

<style scoped></style>
```

### 使用省市区选择组件

src\views\chooseArea\index.vue

```vue
<template>
  <my-choose-area @change="handleArea"></my-choose-area>
</template>

<script setup lang="ts">
  const handleArea = (val: any) => {
    console.log(val);
  };
</script>

<style scoped></style>
```

### 效果图

![Snipaste_2023-02-13_19-49-43.png](https://cdn.nlark.com/yuque/0/2023/png/1614731/1676288999633-53dccf5d-07ec-40e2-b896-e372ba98fe08.png)


![Snipaste_2023-02-13_20-07-25.png](https://cdn.nlark.com/yuque/0/2023/png/1614731/1676290057667-3f5d2f69-91b8-4bcf-8093-b290115f8942.png)

## ﻿趋势标记组件

### 全局注册﻿趋势标记组件

src\components\trend\index.ts

```ts
import { App } from 'vue';
import trend from './src/index.vue';

// 让这个组件可以通过 use 的形式使用
export default {
  install(app: App) {
    app.component('MyTrend', trend);
  },
};
```

### ﻿实现趋势标记组件

﻿src\components\trend\src\index.vue

```vue
<template>
  <div class="trend">
    <div class="text" :style="{ color: textColor }">
      <slot v-if="slots.default"></slot>
      <span v-else>{{ text }}</span>
    </div>
    <div class="icon">
      <!-- 因为 v-if ，组件的 type 与 upIcon 必须对应 -->
      <component :is="`el-icon-${toLine(upIcon)}`" v-if="type === 'up'" :style="{ color: !reverseColor ? upIconColor : downIconColor }"></component>
      <component :is="`el-icon-${toLine(downIcon)}`" v-else :style="{ color: !reverseColor ? downIconColor : upIconColor }"></component>
    </div>
  </div>
</template>

<script setup lang="ts">
  import { useSlots, computed } from 'vue';
  import { toLine } from '../../../utils';
  const props = defineProps({
    // 当前趋势图标是上升(up)还是下降(down)
    type: {
      type: String,
      default: 'up',
    },
    // 插槽没有内容时使用 prop 默认的内容
    text: {
      type: String,
      default: '文字',
    },
    // 上升趋势文字颜色
    upTextColor: {
      type: String,
      default: 'rgb(0,0,0)',
    },
    // 下降趋势的文字颜色
    downTextColor: {
      type: String,
      default: 'rgb(0,0,0)',
    },
    // 上升趋势图标颜色
    upIconColor: {
      type: String,
      default: '#f5222d',
    },
    // 下降趋势图标颜色
    downIconColor: {
      type: String,
      default: '#52c41a',
    },
    // 图标颜色翻转只在默认的颜色下生效 如果使用了与 type 对应的 IconColor 自定义颜色， 这个属性就不生效了
    reverseColor: {
      type: Boolean,
      default: false,
    },
    // 上升趋势显示的图标
    upIcon: {
      type: String,
      default: 'ArrowUp',
    },
    // 下降趋势显示的图标
    downIcon: {
      type: String,
      default: 'ArrowDown',
    },
  });
  // 用于判断插槽有没有内容，proxy 的 Target 里查看，slots.default 为一个函数
  let slots = useSlots();
  let textColor = computed(() => {
    return props.type === 'up' ? props.upTextColor : props.downIconColor;
  });
</script>

<style lang="less" scoped>
  .trend {
    display: flex;
    align-items: center;
    .text {
      font-size: 12px;
      margin-right: 4px;
    }
    .icon {
      svg {
        width: 0.8em;
        height: 0.8em;
      }
    }
  }
</style>
```

### ﻿使用趋势标记组件

﻿src\views\trend\index.vue

```vue
<template>
  <div class="flex">
    <my-trend type="up" upIcon="CaretTop" upIconColor="#065279" reverseColor>销售量</my-trend>
    <my-trend type="down" downIcon="CaretBottom" downIconColor="#ED5736" reverseColor></my-trend>
  </div>
</template>

<script setup lang="ts"></script>

<style lang="less" scoped>
  .flex {
    display: flex;
    div {
      margin-right: 10px;
    }
  }
</style>
```

### 效果图

![Snipaste_2023-02-13_20-18-52.png](https://cdn.nlark.com/yuque/0/2023/png/1614731/1676290748517-db560bf4-f4eb-44e4-bdc9-b57a7b6af81a.png)

## ﻿通知菜单组件

### 全局注册通知菜单组件

src\components\notification\index.ts

```ts
import { App } from 'vue';
import notification from './src/index.vue';

// 让这个组件可以通过 use 的形式使用
export default {
  install(app: App) {
    app.component('MyNotification', notification);
  },
};
```

### 实现通知菜单组件

src\components\notification\src\index.vue

```vue
<template>
  <!-- 弹出框 -->
  <el-popover popper-class="notification-popper-class" placement="bottom" :width="300" trigger="click">
    <!-- Popover 内嵌 HTML 元素 -->
    <template #default>
      <!-- my-list 组件用的插槽 -->
      <slot></slot>
    </template>
    <!-- 触发 Popover 显示的 HTML 元素 -->
    <template #reference>
      <!-- 徽章红点 -->
      <el-badge style="cursor: pointer" :value="value" :max="max" :is-dot="isDot">
        <!-- 动态 icon ,根据 icon prop 觉得 -->
        <component :is="`el-icon-${toLine(icon)}`"></component>
      </el-badge>
    </template>
  </el-popover>
</template>

<script setup lang="ts">
  import { toLine } from '../../../utils';
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
</script>

<style lang="less" scoped>
  svg {
    width: 1.5em;
    height: 1.5em;
  }
</style>
```

#### 通知列表组件

##### 全局注册通知列表组件

src\components\list\index.ts

```ts
import { App } from 'vue';
import list from './src/index.vue';

// 让这个组件可以通过 use 的形式使用
export default {
  install(app: App) {
    app.component('MyList', list);
  },
};
```

##### 实现通知列表组件

src\components\list\src\types.ts

```ts
// 列表的每一项
export interface ListItem {
  // 头像
  avatar?: string,
  // 标题
  title?: string,
  // 描述
  desc?: string,
  // 时间
  time?: string,
  // 标签内容
  tag?: string,
  tagType?: '' | 'success' | 'info' | 'warning' | 'danger'
}

// 列表
export interface ListOptions {
  title: string,
  content: ListItem[]
}

// 操作选项
export interface ActionOptions {
  text: string,
  icon?: string
}
```

src\components\list\src\index.vue

```vue
<template>
  <el-tabs>
    <el-tab-pane v-for="(item, index) in list" :key="index" :label="item.title">
      <el-scrollbar max-height="300px">
        <div class="container" @click="clickItem(item1, index1)" v-for="(item1, index1) in item.content" :key="index1">
          <div class="avatar" v-if="item1.avatar">
            <el-avatar size="small" :src="item1.avatar"></el-avatar>
          </div>
          <div class="content">
            <div v-if="item1.title" class="title">
              <div>{{ item1.title }}</div>
              <el-tag v-if="item1.tag" size="small" :type="item1.tagType">{{ item1.tag }}</el-tag>
            </div>
            <div class="time" v-if="item1.desc">{{ item1.desc }}</div>
            <div class="time" v-if="item1.time">{{ item1.time }}</div>
          </div>
        </div>
        <div class="actions">
          <div class="a-item" :class="{ borderR: i !== actions.length }" v-for="(action, i) in actions" :key="i" @click="clickAction(action, i)">
            <div class="a-icon" v-if="action.icon">
              <component :is="`el-icon-${toLine(action.icon)}`"></component>
            </div>
            <div class="a-text">{{ action.text }}</div>
          </div>
        </div>
      </el-scrollbar>
    </el-tab-pane>
  </el-tabs>
</template>

<script setup lang="ts">
  import { PropType } from 'vue';
  import { ListOptions, ActionOptions, ListItem } from './types';
  import { toLine } from '../../../utils';

  let props = defineProps({
    // 列表的内容
    list: {
      type: Array as PropType<ListOptions[]>,
      required: true,
    },
    // 操作的内容
    actions: {
      type: Array as PropType<ActionOptions[]>,
      default: () => [],
    },
  });
  let emits = defineEmits(['clickItem', 'clickAction']);

  let clickItem = (item: ListItem, index: number) => {
    emits('clickItem', { item, index });
  };

  let clickAction = (item: ActionOptions, index: number) => {
    emits('clickAction', { item, index });
  };
</script>

<style lang="less" scoped>
  .container {
    display: flex;
    align-items: center;
    padding: 12px 20px;
    cursor: pointer;
    &:hover {
      background: #e6f6ff;
    }
    .avatar {
      flex: 1;
    }
    .content {
      flex: 3;
      .title {
        display: flex;
        align-items: center;
        justify-content: space-between;
      }
      .time {
        font-size: 12px;
        color: #999;
        margin-top: 4px;
      }
    }
  }
  .actions {
    height: 50px;
    display: flex;
    align-items: center;
    border-top: 1px solid #eee;
    .a-item {
      height: 50px;
      flex: 1;
      display: flex;
      align-items: center;
      justify-content: center;
      cursor: pointer;
      .a-icon {
        margin-right: 4px;
        position: relative;
        top: 2px;
      }
    }
  }
  .borderR {
    border-right: 1px solid #eee;
  }
</style>
```

### 使用通知菜单组件

src\views\notification\data.ts

```ts
export const list = [
  {
    title: '通知',
    content: [
      {
        title: '蒂姆·库克回复了你的邮件',
        time: '2019-05-08 14:33:18',
        avatar: 'https://gw.alipayobjects.com/zos/rmsportal/ThXAXghbEsBCCSDihZxY.png'
      },
      {
        title: '乔纳森·伊夫邀请你参加会议',
        time: '2019-05-08 14:33:18',
        avatar: 'https://gw.alipayobjects.com/zos/rmsportal/OKJXDXrmkNshAMvwtvhu.png'
      },
      {
        title: '斯蒂夫·沃兹尼亚克已批准了你的休假申请',
        time: '2019-05-08 14:33:18',
        avatar: 'https://gw.alipayobjects.com/zos/rmsportal/kISTdvpyTAhtGxpovNWd.png'
      }

    ],
  },
  {
    title: '关注',
    content: [
      {
        avatar: 'https://gw.alipayobjects.com/zos/rmsportal/fcHMVNCjPOsbUGdEduuv.jpeg',
        title: '曲丽丽 评论了你',
        desc: '描述信息描述信息描述信息',
        time: '3小时前'
      },
      {
        avatar: 'https://gw.alipayobjects.com/zos/rmsportal/fcHMVNCjPOsbUGdEduuv.jpeg',
        title: '曲丽丽 评论了你',
        desc: '描述信息描述信息描述信息',
        time: '3小时前'
      },
      {
        avatar: 'https://gw.alipayobjects.com/zos/rmsportal/fcHMVNCjPOsbUGdEduuv.jpeg',
        title: '曲丽丽 评论了你',
        desc: '描述信息描述信息描述信息',
        time: '3小时前'
      }
    ]
  },
  {
    title: '代办',
    content: [
      {
        title: '任务名称',
        desc: '任务需要在 2017-01-12 20:00 前启动',
        tag: '未开始',
        tagType: ''
      },
      {
        title: '第三方紧急代码变更',
        desc: '冠霖提交于 2017-01-06，需在 2017-01-07 前完成代码变更任务',
        tag: '马上到期',
        tagType: 'danger'
      },
      {
        title: '信息安全考试',
        desc: '指派竹尔于 2017-01-09 前完成更新并发布',
        tag: '已耗时8天',
        tagType: 'warning'
      }
    ]
  },
]
export const actions = [
  {
    text: '清空代办',
    icon: 'delete'
  },
  {
    text: '查看更多',
    icon: 'edit'
  },
]
```

src\views\notification\index.vue

```vue
<template>
  <my-notification :value="50">
    <template #default>
      <my-list @clickItem="clickItem" @clickAction="clickAction" :list="list" :actions="actions"></my-list>
    </template>
  </my-notification>
</template>

<script setup lang="ts">
  import { list, actions } from './data';

  let clickItem = (val: any) => {
    console.log(val);
  };

  let clickAction = (val: any) => {
    console.log(val);
  };
</script>

<style scoped></style>
```

### 效果图

![screen1.gif](https://cdn.nlark.com/yuque/0/2023/gif/1614731/1676291514372-331c06af-9651-4674-86b5-7393721713c2.gif)

点击对应条目

![Snipaste_2023-02-13_20-47-24.png](https://cdn.nlark.com/yuque/0/2023/png/1614731/1676292454956-d1f6ff26-0e50-4765-9155-424240a56949.png)

## ﻿进度条组件

### 全局注册﻿进度条组件

src\components\progress\index.ts

```ts
import { App } from 'vue';
import progress from './src/index.vue';

// 让这个组件可以通过use的形式使用
export default {
  install(app: App) {
    app.component('my-progress', progress);
  },
};
```

### 实现﻿进度条组件

src\components\progress\src\index.vue

```vue
<template>
  <el-progress :percentage="p" v-bind="$attrs"></el-progress>
</template>

<script lang="ts" setup>
  import { onMounted, ref } from 'vue';

  let props = defineProps({
    // 进度条进度
    percentage: {
      type: Number,
      required: true,
    },
    // 是否有动画效果
    isAnimate: {
      type: Boolean,
      default: false,
    },
    // 动画时长(毫秒)
    time: {
      type: Number,
      default: 3000,
    },
  });

  let p = ref(0);
  let flag = true;

  const requestInterval = function (fn: Function, delay: number) {
    let start = performance.now();
    const handle = { value: 0 };

    function loop() {
      let current = performance.now();
      let difference = current - start;

      if (difference >= delay) {
        fn();
        start = performance.now();
      }

      handle.value = requestAnimationFrame(loop);
    }

    handle.value = requestAnimationFrame(loop);
    return handle;
  };

  const clearRequestInterval = function (handle: { value: number }) {
    if (flag) {
      cancelAnimationFrame(handle.value);
      flag = false;
    }
  };

  onMounted(() => {
    if (props.isAnimate) {
      // 规定时间内加载完成
      let t = props.time / props.percentage;
      let timer = requestInterval(() => {
        p.value += 1;
        if (p.value >= props.percentage) {
          p.value = props.percentage;
          clearRequestInterval(timer);
        }
      }, t);
    }
  });
</script>

<style lang="less" scoped>
  .el-progress--circle :deep(svg) {
    width: 126px;
    height: 126px;
  }
</style>
```

### ﻿使用进度条组件

src\views\progress\index.vue

```vue
<template>
  <div>
    <my-progress isAnimate :percentage="60"></my-progress>
    <br />
    <my-progress isAnimate status="success" :stroke-width="20" :percentage="60"></my-progress>
    <br />
    <my-progress :time="5000" type="circle" isAnimate :percentage="60"></my-progress>
  </div>
</template>

<script lang="ts" setup></script>

<style lang="less" scoped></style>
```

### 效果图

![screen1.gif](https://cdn.nlark.com/yuque/0/2023/gif/1614731/1676376883491-cf5fdac6-407c-4bfd-908c-e9c444f7504c.gif)

## ﻿时间选择组件

### 全局注册日期选择组件

﻿src\components\chooseDate\index.ts

```ts
import { App } from 'vue';
import chooseDate from './src/index.vue';

// 让这个组件可以通过use的形式使用
export default {
  install(app: App) {
    app.component('my-choose-date', chooseDate);
  },
};
```

### ﻿实现日期选择组件

src\components\chooseDate\src\index.vue

```vue
<template>
  <div style="display: flex">
    <div style="margin-right: 20px">
      <el-date-picker v-model="startDate" type="date" :placeholder="startPlaceholder" :disabledDate="startDisabledDate" v-bind="$attrs.startOptions"></el-date-picker>
    </div>
    <div>
      <el-date-picker v-model="endDate" type="date" :placeholder="endPlaceholder" :disabled="endDateDisabled" :disabledDate="endDisabledDate" v-bind="$attrs.endOptions"></el-date-picker>
    </div>
  </div>
</template>

<script lang="ts" setup>
  import { ref, watch } from 'vue';

  let props = defineProps({
    // 开始日期的占位符
    startPlaceholder: {
      type: String,
      default: '请选择开始日期',
    },
    // 结束日期的占位符
    endPlaceholder: {
      type: String,
      default: '请选择结束日期',
    },
    // 是否禁用选择今天之前的日期
    disableToday: {
      type: Boolean,
      default: true,
    },
  });

  let emits = defineEmits(['startChange', 'endChange']);

  // 开始日期
  let startDate = ref<Date | null>(null);
  // 结束日期
  let endDate = ref<Date | null>(null);
  // 控制结束日期的禁用状态
  let endDateDisabled = ref<boolean>(true);

  // 禁用开始日期的函数
  let startDisabledDate = (time: Date) => {
    if (props.disableToday) return time.getTime() < Date.now() - 1000 * 60 * 60 * 24;
  };
  // 禁用结束日期的函数
  let endDisabledDate = (time: Date) => {
    if (startDate.value) {
      return time.getTime() < startDate.value.getTime() + 1000 * 60 * 60 * 24;
    }
  };

  // 监听开始的日期
  watch(
    () => startDate.value,
    (val) => {
      if (!val) {
        endDateDisabled.value = true;
        endDate.value = null;
      } else {
        emits('startChange', val);
        endDateDisabled.value = false;
      }
    },
  );
  // 监听结束的日期
  watch(
    () => endDate.value,
    (val) => {
      if (val) {
        emits('endChange', {
          startDate: startDate.value,
          endDate: val,
        });
      }
    },
  );
</script>

<style lang="scss" scoped></style>
```

### 全局注册时间选择组件

src\components\chooseTime\index.ts

```ts
import { App } from 'vue';
import chooseTime from './src/index.vue';

// 让这个组件可以通过use的形式使用
export default {
  install(app: App) {
    app.component('my-choose-time', chooseTime);
  },
};
```

### ﻿实现时间选择组件

```vue
<template>
  <div style="display: flex">
    <div style="margin-right: 20px">
      <el-time-select v-model="startTime" :placeholder="startPlaceholder" :start="startTimeStart" :step="startStep" :end="startTimeEnd" v-bind="$attrs.startOptions"></el-time-select>
    </div>
    <div>
      <el-time-select
        v-model="endTime"
        :min-time="startTime"
        :placeholder="endPlaceholder"
        :start="endTimeStart"
        :step="endStep"
        :end="endTimeEnd"
        :disabled="endTimeDisabled"
        v-bind="$attrs.endOptions"
      ></el-time-select>
    </div>
  </div>
</template>

<script lang="ts" setup>
  import { ref, watch } from 'vue';

  let props = defineProps({
    // 开始时间的占位符
    startPlaceholder: {
      type: String,
      default: '请选择开始时间',
    },
    // 结束时间的占位符
    endPlaceholder: {
      type: String,
      default: '请选择结束时间',
    },
    // 开始时间的开始选择
    startTimeStart: {
      type: String,
      default: '08:00',
    },
    // 开始时间的步进
    startStep: {
      type: String,
      default: '00:30',
    },
    // 开始时间的结束选择
    startTimeEnd: {
      type: String,
      default: '24:00',
    },
    // 结束时间的开始选择
    endTimeStart: {
      type: String,
      default: '08:00',
    },
    // 结束时间的步进
    endStep: {
      type: String,
      default: '00:30',
    },
    // 结束时间的结束选择
    endTimeEnd: {
      type: String,
      default: '24:00',
    },
  });

  let emits = defineEmits(['startChange', 'endChange']);

  // 开始时间
  let startTime = ref<string>('');
  // 结束时间
  let endTime = ref<string>('');
  // 是否禁用结束时间
  let endTimeDisabled = ref<boolean>(true);

  // 监听开始时间的变化
  watch(
    () => startTime.value,
    (val) => {
      if (val === '') {
        endTime.value = '';
        endTimeDisabled.value = true;
      } else {
        endTimeDisabled.value = false;
        // 给父组件分发事件
        emits('startChange', val);
      }
    },
  );

  // 监听结束时间的变化
  watch(
    () => endTime.value,
    (val) => {
      if (val !== '') {
        emits('endChange', {
          startTime: startTime.value,
          endTime: val,
        });
      }
    },
  );
</script>

<style lang="scss" scoped></style>
```

### 使用时间选择组件

```vue
<template>
  <div>
    <my-choose-time :startOptions="startOptions" @startChange="startChange" @endChange="endChange"></my-choose-time>
    <br />
    <br />
    <my-choose-date :disableToday="false" :startOptions="startOptions" @startChange="dateStartChange" @endChange="dateEndChange"></my-choose-date>
  </div>
</template>

<script lang="ts" setup>
  interface endValue {
    startTime: string;
    endTime: string;
  }

  interface dateEndValue {
    startDate: Date;
    endDate: Date;
  }

  let startChange = (val: string) => {
    console.log('startChange', val);
  };

  let endChange = (val: endValue) => {
    console.log('endChange', val);
  };

  let dateStartChange = (val: Date) => {
    console.log(val);
  };

  let dateEndChange = (val: dateEndValue) => {
    console.log(val);
  };

  let startOptions = {
    // size: 'mini',
    // clearable: false
  };
</script>

<style lang="scss" scoped></style>
```

### 效果图

![Snipaste_2023-02-14_20-19-52.png](https://cdn.nlark.com/yuque/0/2023/png/1614731/1676377211768-dc0c04c4-e9fc-4b0c-9f6c-1bd1510b3d7c.png)

## ﻿城市选择组件

### 全局注册﻿城市选择组件

src\components\chooseCity\index.ts

```ts
import { App } from 'vue';
import chooseCity from './src/index.vue';

// 让这个组件可以通过use的形式使用
export default {
  install(app: App) {
    app.component('my-choose-city', chooseCity);
  },
};
```

### 实现﻿城市选择组件

src\components\chooseCity\src\types.ts

```ts
export interface City {
  id: number;
  // 拼音
  spell: string;
  // 名字
  name: string;
}

export interface Province {
  name: string;
  data: string[];
  id?: string;
}
```

src\components\chooseCity\src\index.vue

```vue
<template>
  <el-popover v-model:visible="visible" placement="bottom-start" :width="430" trigger="click">
    <template #reference>
      <div class="result">
        <div>{{ result }}</div>
        <div>
          <el-icon-arrowdown :class="{ rotate: visible }"></el-icon-arrowdown>
        </div>
      </div>
    </template>
    <div class="container">
      <el-row class="container-header">
        <el-col :span="8">
          <el-radio-group v-model="radioValue" size="small">
            <el-radio-button label="按城市"></el-radio-button>
            <el-radio-button label="按省份"></el-radio-button>
          </el-radio-group>
        </el-col>
        <el-col :offset="1" :span="15">
          <el-select @change="changeSelect" placeholder="请搜索城市" size="small" v-model="selectValue" filterable :filter-method="filterMethod">
            <el-option v-for="item in options" :key="item.id" :label="item.name" :value="item.id"></el-option>
          </el-select>
        </el-col>
      </el-row>
      <template v-if="radioValue === '按城市'">
        <div class="city">
          <!--  <div v-for="(value, key) in cities">{{key}}</div> -->
          <!-- 字母区域 -->
          <div class="city-item" @click="clickChat(item)" v-for="(item, index) in Object.keys(cities)">
            {{ item }}
          </div>
        </div>
        <el-scrollbar max-height="300px">
          <template v-for="(value, key) in cities" :key="key">
            <el-row style="margin-bottom: 10px" :id="key">
              <el-col :span="2">{{ key }}:</el-col>
              <el-col :span="22" class="city-name">
                <div @click="clickItem(item)" class="city-name-item" v-for="(item, index) in value" :key="item.id">
                  <div>{{ item.name }}</div>
                </div>
              </el-col>
            </el-row>
          </template>
        </el-scrollbar>
      </template>
      <template v-else>
        <div class="province">
          <div class="province-item" v-for="(item, index) in Object.keys(provinces)" :key="index" @click="clickChat(item)">
            {{ item }}
          </div>
        </div>
        <el-scrollbar max-height="300px">
          <template v-for="(item, index) in Object.values(provinces)" :key="index">
            <template v-for="(item1, index1) in item" :key="index1">
              <el-row style="margin-bottom: 10px" :id="item1.id">
                <el-col :span="3">{{ item1.name }}:</el-col>
                <el-col :span="21" class="province-name">
                  <div class="province-name-item" v-for="(item2, index2) in item1.data" :key="index2">
                    <div @click="clickProvince(item2)">{{ item2 }}</div>
                  </div>
                </el-col>
              </el-row>
            </template>
          </template>
        </el-scrollbar>
      </template>
    </div>
  </el-popover>
</template>

<script lang="ts" setup>
  import { ref, onMounted } from 'vue';
  import city from '../lib/city';
  import { City } from './types';
  import province from '../lib/province.json';

  // 分发事件
  let emits = defineEmits(['changeCity', 'changeProvince']);

  // 最终选择的结果
  let result = ref<string>('请选择');
  // 控制弹出层的显示
  let visible = ref<boolean>(false);
  // 单选框的值 按城市还是按省份选择
  let radioValue = ref<string>('按城市');
  // 下拉框的值 搜索下拉框
  let selectValue = ref<string>('');
  // 下拉框显示城市的数据
  let options = ref<City[]>([]);
  // 所有的城市数据
  let cities = ref(city.cities);
  // 所有省份的数据
  let provinces = ref(province);
  // 所有的城市数据
  let allCity = ref<City[]>([]);

  // 点击每个城市
  let clickItem = (item: City) => {
    // 给结果赋值
    result.value = item.name;
    // 关闭弹出层
    visible.value = false;
    emits('changeCity', item);
  };
  let clickProvince = (item: string) => {
    // 给结果赋值
    result.value = item;
    // 关闭弹出层
    visible.value = false;
    emits('changeProvince', item);
  };

  // 点击字母区域
  let clickChat = (item: string) => {
    let el = document.getElementById(item);
    if (el) el.scrollIntoView();
  };
  // 自定义搜索过滤
  let filterMethod = (val: string) => {
    let values = Object.values(cities.value).flat(2);
    if (val === '') {
      options.value = values;
    } else {
      if (radioValue.value === '按城市') {
        // 中文和拼音一起过滤
        options.value = values.filter((item) => {
          return item.name.includes(val) || item.spell.includes(val);
        });
      } else {
        // 中文过滤
        options.value = values.filter((item) => {
          return item.name.includes(val);
        });
      }
    }
  };
  // 下拉框选择
  let changeSelect = (val: number) => {
    let city = allCity.value.find((item) => item.id === val)!;
    result.value = city.name;
    if (radioValue.value === '按城市') {
      emits('changeCity', city);
    } else {
      emits('changeProvince', city.name);
    }
  };
  onMounted(() => {
    // 获取下拉框的城市数据
    let values = Object.values(cities.value).flat(2);
    allCity.value = values;
    options.value = values;
  });
</script>

<style lang="less" scoped>
  .result {
    display: flex;
    align-items: center;
    width: fit-content;
    cursor: pointer;
  }

  .rotate {
    transform: rotate(180deg);
  }

  svg {
    position: relative;
    top: 2px;
    margin-left: 4px;
    width: 1em;
    height: 1em;
    transition: all 0.25s linear;
  }

  .container {
    padding: 6px;
  }

  .container-header {
    align-items: flex-end;
  }

  .city,
  .province {
    display: flex;
    align-items: center;
    flex-wrap: wrap;
    margin-top: 10px;
    margin-bottom: 10px;

    &-item {
      padding: 3px 6px;
      margin-right: 8px;
      margin-bottom: 8px;
      border: 1px solid #eee;
      cursor: pointer;
    }
  }

  .city-name,
  .province-name {
    display: flex;
    align-items: center;
    flex-wrap: wrap;

    .city-name-item,
    .province-name-item {
      margin-right: 6px;
      margin-bottom: 6px;
      cursor: pointer;
    }
  }
</style>
```

### 使用城市选择组件

src\views\chooseCity\index.vue

```vue
<template>
  <my-choose-city></my-choose-city>
</template>

<script lang="ts" setup></script>

<style lang="scss" scoped></style>
```

### 效果图

![screen1.gif](https://cdn.nlark.com/yuque/0/2023/gif/1614731/1676449185356-58b11580-3b10-4a18-9f4f-876e32bd3399.gif)

## 表单组件

### 全局注册﻿表单组件

src\components\form\index.ts

```ts
import { App } from 'vue';
import form from './src/index.vue';

// 让这个组件可以通过use的形式使用
export default {
  install(app: App) {
    app.component('my-form', form);
  },
};
```

### 实现表单组件

src\components\form\src\types\rule.ts

```ts
export type RuleType =
  | 'string'
  | 'number'
  | 'boolean'
  | 'method'
  | 'regexp'
  | 'integer'
  | 'float'
  | 'array'
  | 'object'
  | 'enum'
  | 'date'
  | 'url'
  | 'hex'
  | 'email'
  | 'pattern'
  | 'any';

export interface ValidateOption {
  // whether to suppress internal warning
  suppressWarning?: boolean;

  // when the first validation rule generates an error stop processed
  first?: boolean;

  // when the first validation rule of the specified field generates an error stop the field processed, 'true' means all fields.
  firstFields?: boolean | string[];

  messages?: Partial<ValidateMessages>;

  /** The name of rules need to be trigger. Will validate all rules if leave empty */
  keys?: string[];

  error?: (rule: InternalRuleItem, message: string) => ValidateError;
}

export type SyncErrorType = Error | string;
export type SyncValidateResult = boolean | SyncErrorType | SyncErrorType[];
export type ValidateResult = void | Promise<void> | SyncValidateResult;

export interface RuleItem {
  type?: RuleType; // default type is 'string'
  required?: boolean;
  pattern?: RegExp | string;
  min?: number; // Range of type 'string' and 'array'
  max?: number; // Range of type 'string' and 'array'
  len?: number; // Length of type 'string' and 'array'
  enum?: Array<string | number | boolean | null | undefined>; // possible values of type 'enum'
  whitespace?: boolean;
  trigger?: string | string[];
  fields?: Record<string, Rule>; // ignore when without required
  options?: ValidateOption;
  defaultField?: Rule; // 'object' or 'array' containing validation rules
  transform?: (value: Value) => Value;
  message?: string | ((a?: string) => string);
  asyncValidator?: (
    rule: InternalRuleItem,
    value: Value,
    callback: (error?: string | Error) => void,
    source: Values,
    options: ValidateOption,
  ) => void | Promise<void>;
  validator?: (
    rule: InternalRuleItem,
    value: Value,
    callback: (error?: string | Error) => void,
    source: Values,
    options: ValidateOption,
  ) => SyncValidateResult | void;
}

export type Rule = RuleItem | RuleItem[];

export type Rules = Record<string, Rule>;

/**
 *  Rule for validating a value exists in an enumerable list.
 *
 *  @param rule The validation rule.
 *  @param value The value of the field on the source object.
 *  @param source The source object being validated.
 *  @param errors An array of errors that this rule may add
 *  validation errors to.
 *  @param options The validation options.
 *  @param options.messages The validation messages.
 *  @param type Rule type
 */
export type ExecuteRule = (
  rule: InternalRuleItem,
  value: Value,
  source: Values,
  errors: string[],
  options: ValidateOption,
  type?: string,
) => void;

/**
 *  Performs validation for any type.
 *
 *  @param rule The validation rule.
 *  @param value The value of the field on the source object.
 *  @param callback The callback function.
 *  @param source The source object being validated.
 *  @param options The validation options.
 *  @param options.messages The validation messages.
 */
export type ExecuteValidator = (
  rule: InternalRuleItem,
  value: Value,
  callback: (error?: string[]) => void,
  source: Values,
  options: ValidateOption,
) => void;

// >>>>> Message
type ValidateMessage<T extends any[] = unknown[]> =
  | string
  | ((...args: T) => string);
type FullField = string | undefined;
type EnumString = string | undefined;
type Pattern = string | RegExp | undefined;
type Range = number | undefined;
type Type = string | undefined;

export interface ValidateMessages {
  default?: ValidateMessage;
  required?: ValidateMessage<[FullField]>;
  enum?: ValidateMessage<[FullField, EnumString]>;
  whitespace?: ValidateMessage<[FullField]>;
  date?: {
    format?: ValidateMessage;
    parse?: ValidateMessage;
    invalid?: ValidateMessage;
  };
  types?: {
    string?: ValidateMessage<[FullField, Type]>;
    method?: ValidateMessage<[FullField, Type]>;
    array?: ValidateMessage<[FullField, Type]>;
    object?: ValidateMessage<[FullField, Type]>;
    number?: ValidateMessage<[FullField, Type]>;
    date?: ValidateMessage<[FullField, Type]>;
    boolean?: ValidateMessage<[FullField, Type]>;
    integer?: ValidateMessage<[FullField, Type]>;
    float?: ValidateMessage<[FullField, Type]>;
    regexp?: ValidateMessage<[FullField, Type]>;
    email?: ValidateMessage<[FullField, Type]>;
    url?: ValidateMessage<[FullField, Type]>;
    hex?: ValidateMessage<[FullField, Type]>;
  };
  string?: {
    len?: ValidateMessage<[FullField, Range]>;
    min?: ValidateMessage<[FullField, Range]>;
    max?: ValidateMessage<[FullField, Range]>;
    range?: ValidateMessage<[FullField, Range, Range]>;
  };
  number?: {
    len?: ValidateMessage<[FullField, Range]>;
    min?: ValidateMessage<[FullField, Range]>;
    max?: ValidateMessage<[FullField, Range]>;
    range?: ValidateMessage<[FullField, Range, Range]>;
  };
  array?: {
    len?: ValidateMessage<[FullField, Range]>;
    min?: ValidateMessage<[FullField, Range]>;
    max?: ValidateMessage<[FullField, Range]>;
    range?: ValidateMessage<[FullField, Range, Range]>;
  };
  pattern?: {
    mismatch?: ValidateMessage<[FullField, Value, Pattern]>;
  };
}

export interface InternalValidateMessages extends ValidateMessages {
  clone: () => InternalValidateMessages;
}

// >>>>> Values
export type Value = any;
export type Values = Record<string, Value>;

// >>>>> Validate
export interface ValidateError {
  message?: string;
  fieldValue?: Value;
  field?: string;
}

export type ValidateFieldsError = Record<string, ValidateError[]>;

export type ValidateCallback = (
  errors: ValidateError[] | null,
  fields: ValidateFieldsError | Values,
) => void;

export interface RuleValuePackage {
  rule: InternalRuleItem;
  value: Value;
  source: Values;
  field: string;
}

export interface InternalRuleItem extends Omit<RuleItem, 'validator'> {
  field?: string;
  fullField?: string;
  fullFields?: string[];
  validator?: RuleItem['validator'] | ExecuteValidator;
}
```

src\components\form\src\types\types.ts

```ts
// 可配置的表单
import { CSSProperties } from 'vue';
import { RuleItem } from './rule';
import { ValidateFieldsError } from 'async-validator';
interface Callback {
  (isValid?: boolean, invalidFields?: ValidateFieldsError): void;
}

// 表单每一项的配置选项
export interface FormOptions {
  // 表单项显示的元素
  type:
    | 'cascader'
    | 'checkbox'
    | 'checkbox-group'
    | 'checkbox-button'
    | 'color-picker'
    | 'date-picker'
    | 'input'
    | 'input-number'
    | 'radio'
    | 'radio-group'
    | 'radio-button'
    | 'rate'
    | 'select'
    | 'option'
    | 'slider'
    | 'switch'
    | 'time-picker'
    | 'time-select'
    | 'transfer'
    | 'upload'
    | 'editor';
  // 表单项的值
  value?: any;
  // 表单项label
  label?: string;
  // 表单项的标识
  prop?: string;
  // 表单项的验证规则
  rules?: RuleItem[];
  // 表单项的占位符
  placeholder?: string;
  // 表单元素特有的属性
  attrs?: {
    // css样式
    style?: CSSProperties;
    clearable?: boolean;
    showPassword?: boolean;
    disabled?: boolean;
  };
  // 表单项的子元素
  children?: FormOptions[];
  // 处理上传组件的属性和方法
  uploadAttrs?: {
    action: string;
    headers?: object;
    method?: 'post' | 'put' | 'patch';
    multiple?: boolean;
    data?: any;
    name?: string;
    withCredentials?: boolean;
    showFileList?: boolean;
    drag?: boolean;
    accept?: string;
    thumbnailMode?: boolean;
    fileList?: any[];
    listType?: 'text' | 'picture' | 'picture-card';
    autoUpload?: boolean;
    disabled?: boolean;
    limit?: number;
  };
}

export interface ValidateFieldCallback {
  (message?: string, invalidFields?: ValidateFieldsError): void;
}

export interface FormInstance {
  registerLabelWidth(width: number, oldWidth: number): void;
  deregisterLabelWidth(width: number): void;
  autoLabelWidth: string | undefined;
  emit: (evt: string, ...args: any[]) => void;
  labelSuffix: string;
  inline?: boolean;
  model?: Record<string, unknown>;
  size?: string;
  showMessage?: boolean;
  labelPosition?: string;
  labelWidth?: string;
  rules?: Record<string, unknown>;
  statusIcon?: boolean;
  hideRequiredAsterisk?: boolean;
  disabled?: boolean;
  validate: (callback?: Callback) => Promise<boolean>;
  resetFields: () => void;
  clearValidate: (props?: string | string[]) => void;
  validateField: (props: string | string[], cb: ValidateFieldCallback) => void;
}
```

src\components\form\src\index.vue

```vue
<template>
  <el-form ref="form" v-if="model" :validate-on-rule-change="false" :model="model" :rules="rules" v-bind="$attrs">
    <template v-for="(item, index) in options" :key="index">
      <el-form-item v-if="!item.children || !item.children!.length" :prop="item.prop" :label="item.label">
        <component
          v-if="item.type !== 'upload' && item.type !== 'editor'"
          :placeholder="item.placeholder"
          v-bind="item.attrs"
          :is="`el-${item.type}`"
          v-model="model[item.prop!]"
        ></component>
        <el-upload
          v-if="item.type === 'upload'"
          v-bind="item.uploadAttrs"
          :on-preview="onPreview"
          :on-remove="onRemove"
          :on-success="onSuccess"
          :on-error="onError"
          :on-progress="onProgress"
          :on-change="onChange"
          :before-upload="beforeUpload"
          :before-remove="beforeRemove"
          :http-request="httpRequest"
          :on-exceed="onExceed"
        >
          <slot name="uploadArea"></slot>
          <slot name="uploadTip"></slot>
        </el-upload>
        <div id="editor" v-if="item.type === 'editor'"></div>
      </el-form-item>
      <el-form-item v-if="item.children && item.children.length" :prop="item.prop" :label="item.label">
        <component :placeholder="item.placeholder" v-bind="item.attrs" :is="`el-${item.type}`" v-model="model[item.prop!]">
          <component v-for="(child, i) in item.children" :key="i" :is="`el-${child.type}`" :label="child.label" :value="child.value"></component>
        </component>
      </el-form-item>
    </template>
    <el-form-item>
      <slot name="action" :form="form" :model="model"></slot>
    </el-form-item>
  </el-form>
</template>

<script lang="ts" setup>
  import { PropType, ref, onMounted, watch, nextTick } from 'vue';
  import { FormInstance, FormOptions } from './types/types';
  import cloneDeep from 'lodash/cloneDeep';
  import E from 'wangeditor';

  let emits = defineEmits(['on-preview', 'on-remove', 'on-success', 'on-error', 'on-progress', 'on-change', 'before-upload', 'before-remove', 'on-exceed']);

  let props = defineProps({
    // 表单的配置项
    options: {
      type: Array as PropType<FormOptions[]>,
      required: true,
    },
    // 用户自定义上传方法
    httpRequest: {
      type: Function,
    },
  });

  let model = ref<any>(null);
  let rules = ref<any>(null);
  let form = ref<FormInstance | null>();
  let edit = ref();

  // 初始化表单
  let initForm = () => {
    if (props.options && props.options.length) {
      let m: any = {};
      let r: any = {};
      props.options.map((item: FormOptions) => {
        m[item.prop!] = item.value;
        r[item.prop!] = item.rules;
        if (item.type === 'editor') {
          // 初始化富文本
          nextTick(() => {
            if (document.getElementById('editor')) {
              const editor = new E('#editor');
              editor.config.placeholder = item.placeholder!;
              editor.create();
              // 初始化富文本的内容
              editor.txt.html(item.value);
              editor.config.onchange = (newHtml: string) => {
                model.value[item.prop!] = newHtml;
              };
              edit.value = editor;
            }
          });
        }
      });
      model.value = cloneDeep(m);
      rules.value = cloneDeep(r);
    }
  };

  // 重置表单
  let resetFields = () => {
    // 重置element-plus的表单
    form.value!.resetFields();
    // 重置富文本编辑器的内容
    // 获取到富文本的配置项
    if (props.options && props.options.length) {
      let editorItem = props.options.find((item) => item.type === 'editor')!;
      edit.value.txt.html(editorItem.value);
    }
  };

  // 表单验证方法
  let validate = () => {
    return form.value!.validate;
  };

  // 获取表单数据
  let getFormData = () => {
    return model.value;
  };

  // 分发方法
  defineExpose({
    resetFields,
    validate,
    getFormData,
  });

  onMounted(() => {
    initForm();
  });

  // 监听父组件传递进来的options
  watch(
    () => props.options,
    () => {
      initForm();
    },
    { deep: true }
  );

  // 上传组件的所有方法
  let onPreview = (file: File) => {
    emits('on-preview', file);
  };

  let onRemove = (file: File, fileList: FileList) => {
    emits('on-remove', { file, fileList });
  };

  let onSuccess = (response: any, file: File, fileList: FileList) => {
    // 上传图片成功 给表单上传项赋值
    let uploadItem = props.options.find((item) => item.type === 'upload')!;
    model.value[uploadItem.prop!] = { response, file, fileList };
    emits('on-success', { response, file, fileList });
  };

  let onError = (err: any, file: File, fileList: FileList) => {
    emits('on-error', { err, file, fileList });
  };

  let onProgress = (event: any, file: File, fileList: FileList) => {
    emits('on-progress', { event, file, fileList });
  };

  let onChange = (file: File, fileList: FileList) => {
    emits('on-change', { file, fileList });
  };

  let beforeUpload = (file: File) => {
    emits('before-upload', file);
  };

  let beforeRemove = (file: File, fileList: FileList) => {
    emits('before-remove', { file, fileList });
  };

  let onExceed = (files: File, fileList: FileList) => {
    emits('on-exceed', { files, fileList });
  };
</script>

<style lang="scss" scoped></style>
```

### 使用表单组件

src\views\form\index.vue

```vue
<template>
  <div>
    <my-form
      ref="form"
      label-width="100px"
      :options="options"
      @on-change="handleChange"
      @before-upload="handleBeforeUpload"
      @on-preview="handlePreview"
      @on-remove="handleRemove"
      @before-remove="beforeRemove"
      @on-success="handleSuccess"
      @on-exceed="handleExceed"
    >
      <template #uploadArea>
        <el-button size="small" type="primary">Click to upload</el-button>
      </template>
      <template #uploadTip>
        <div style="font-size: 12px; color: #ccc">jpg/png files with a size less than 500kb</div>
      </template>
      <template #action="scope">
        <el-button type="primary" @click="submitForm(scope)">提交</el-button>
        <el-button @click="resetForm">重置</el-button>
      </template>
    </my-form>
  </div>
</template>

<script lang="ts" setup>
  import { FormInstance, FormOptions } from '../../components/form/src/types/types';
  import { ElMessage, ElMessageBox } from 'element-plus';
  import { ref } from 'vue';

  interface Scope {
    form: FormInstance;
    model: any;
  }

  let options: FormOptions[] = [
    {
      type: 'input',
      value: '',
      label: '用户名',
      prop: 'username',
      placeholder: '请输入用户名',
      rules: [
        {
          required: true,
          message: '用户名不能为空',
          trigger: 'blur',
        },
        {
          min: 2,
          max: 6,
          message: '用户名在2-6位之间',
          trigger: 'blur',
        },
      ],
      attrs: {
        clearable: true,
      },
    },
    {
      type: 'input',
      value: '',
      label: '密码',
      prop: 'password',
      placeholder: '请输入密码',
      rules: [
        {
          required: true,
          message: '密码不能为空',
          trigger: 'blur',
        },
        {
          min: 6,
          max: 15,
          message: '密码在6-15位之间',
          trigger: 'blur',
        },
      ],
      attrs: {
        showPassword: true,
        clearable: true,
      },
    },
    {
      type: 'select',
      value: '',
      placeholder: '请选择职位',
      prop: 'role',
      label: '职位',
      attrs: {
        style: {
          width: '100%',
        },
      },
      rules: [
        {
          required: true,
          message: '职位不能为空',
          trigger: 'change',
        },
      ],
      children: [
        {
          type: 'option',
          label: '经理',
          value: '1',
        },
        {
          type: 'option',
          label: '主管',
          value: '2',
        },
        {
          type: 'option',
          label: '员工',
          value: '3',
        },
      ],
    },
    {
      type: 'checkbox-group',
      value: [],
      prop: 'like',
      label: '爱好',
      rules: [
        {
          required: true,
          message: '爱好不能为空',
          trigger: 'change',
        },
      ],
      children: [
        {
          type: 'checkbox',
          label: '足球',
          value: '1',
        },
        {
          type: 'checkbox',
          label: '篮球',
          value: '2',
        },
        {
          type: 'checkbox',
          label: '排球',
          value: '3',
        },
      ],
    },
    {
      type: 'radio-group',
      value: '',
      prop: 'gender',
      label: '性别',
      rules: [
        {
          required: true,
          message: '性别不能为空',
          trigger: 'change',
        },
      ],
      children: [
        {
          type: 'radio',
          label: '男',
          value: 'male',
        },
        {
          type: 'radio',
          label: '女',
          value: 'female',
        },
        {
          type: 'radio',
          label: '保密',
          value: 'not',
        },
      ],
    },
    {
      type: 'upload',
      label: '上传',
      prop: 'pic',
      uploadAttrs: {
        action: 'https://jsonplaceholder.typicode.com/posts/',
        multiple: true,
        limit: 3,
      },
      rules: [
        {
          required: true,
          message: '图片不能为空',
          trigger: 'blur',
        },
      ],
    },
    {
      type: 'editor',
      value: '123',
      prop: 'desc',
      label: '描述',
      placeholder: '请输入描述',
      rules: [
        {
          required: true,
          message: '描述不能为空',
          trigger: 'blur',
        },
      ],
    },
  ];
  let form = ref();

  let submitForm = (scope: Scope) => {
    scope.form.validate((valid) => {
      if (valid) {
        console.log(scope.model);
        ElMessage.success('提交成功');
      } else {
        ElMessage.error('表单填写有误,请检查');
      }
    });
  };

  // 重置表单
  let resetForm = () => {
    form.value.resetFields();
  };

  let handleRemove = (file: any, fileList: any) => {
    console.log('handleRemove');
    console.log(file, fileList);
  };

  let handlePreview = (file: any) => {
    console.log('handlePreview');
    console.log(file);
  };

  let beforeRemove = (val: any) => {
    console.log('beforeRemove');
    return ElMessageBox.confirm(`Cancel the transfert of ${val.file.name} ?`);
  };

  let handleExceed = (val: any) => {
    console.log('handleExceed', val);
    ElMessage.warning(`The limit is 3, you selected ${val.files.length} files this time, add up to ${val.files.length + val.fileList.length} totally`);
  };

  let handleSuccess = (val: any) => {
    console.log('success');
    console.log(val);
  };

  let handleChange = (val: any) => {
    console.log('change');
    console.log(val);
  };

  let handleBeforeUpload = (val: any) => {
    console.log('handleBeforeUpload');
    console.log(val);
  };
</script>

<style lang="scss" scoped></style>
```

### 效果图

![img](https://cdn.nlark.com/yuque/0/2023/png/1614731/1681040956181-9a6cf6ea-82a9-4d76-aed1-4e55835c1763.png)

## 弹出框表单组件

### 全局注册弹出框表单组件

src\components\modalForm\index.ts

```ts
import { App } from 'vue';
import modalForm from './src/index.vue';

// 让这个组件可以通过use的形式使用
export default {
  install(app: App) {
    app.component('my-modal-form', modalForm);
  },
};
```

### 实现弹出框表单组件

src\components\modalForm\src\index.vue

```vue
<template>
  <div>
    <el-dialog v-model="dialogVisible" v-bind="$attrs" top="2vh" size="40%">
      <template #default>
        <my-form
          ref="form"
          :options="options"
          label-width="100px"
          @on-change="onChange"
          @before-upload="beforeUpload"
          @on-preview="onPreview"
          @on-remove="onRemove"
          @before-remove="beforeRemove"
          @on-success="onSuccess"
          @on-exceed="onExceed"
        >
          <template #uploadArea>
            <slot name="uploadArea"></slot>
          </template>
          <template #uploadTip>
            <slot name="uploadTip"></slot>
          </template>
        </my-form>
      </template>
      <template #footer>
        <slot name="footer" :form="form"></slot>
      </template>
    </el-dialog>
  </div>
</template>

<script lang="ts" setup>
  import { PropType, ref, watch } from 'vue';
  import { FormOptions } from '../../form/src/types/types';
  let props = defineProps({
    visible: {
      type: Boolean,
      default: false,
    },
    options: {
      type: Array as PropType<FormOptions[]>,
      required: true,
    },
    onChange: {
      type: Function,
    },
    beforeUpload: {
      type: Function,
    },
    onPreview: {
      type: Function,
    },
    onRemove: {
      type: Function,
    },
    beforeRemove: {
      type: Function,
    },
    onSuccess: {
      type: Function,
    },
    onExceed: {
      type: Function,
    },
  });
  let emits = defineEmits(['update:visible']);

  // 表单实例
  let form = ref();

  // 弹出框的显示与隐藏
  let dialogVisible = ref<boolean>(props.visible);

  watch(
    () => props.visible,
    (val) => {
      dialogVisible.value = val;
    }
  );
  watch(
    () => dialogVisible.value,
    (val) => {
      emits('update:visible', val);
    }
  );
</script>
```

### 使用弹出框表单组件

```vue
<template>
  <my-calendar :events="events" :eventContent="eventContent" @date-click="dateClick"></my-calendar>
</template>

<script lang="ts" setup>
  import { DateClickArg } from '@fullcalendar/interaction';
  import { EventContentArg } from '@fullcalendar/core';
  import { EventItem } from '../../components/calendar/src/types';
  import { ref } from 'vue';

  let events = ref<EventItem[]>([
    {
      title: '购物',
      start: '2021-11-11 10:00:00',
      end: '2021-11-11 12:00:00',
      editable: true,
    },
    {
      title: '学习',
      start: '2021-11-15 08:00:00',
      end: '2021-11-15 16:00:00',
    },
  ]);
  let dateClick = (info: DateClickArg) => {
    let event = {
      start: `${info.dateStr} 12:00:00`,
      end: `${info.dateStr} 13:00:00`,
      title: '吃饭',
    };
    events.value.push(event);
    console.log(info);
  };

  let eventContent = (arg: EventContentArg) => {
    let el = document.createElement('div');
    let timeTextArr = arg.timeText.split(' - ');
    let start = timeTextArr[0].replace('上午', '').replace('下午', '').replace('时', '');
    let end = timeTextArr[1].replace('上午', '').replace('下午', '').replace('时', '');
    el.innerHTML = `
        <img src="src/assets/logo.png" style="width:20px;height:20px;">
         <div>开始时间: ${start}</div>
         <div>结束时间: ${end}</div>
         <div>标题: ${arg.event._def.title}</div>
        `;
    return {
      domNodes: [el],
    };
  };
</script>
```

### 效果图

![Snipaste_2023-04-29_21-28-59](https://cdn.nlark.com/yuque/0/2023/png/1614731/1682779418591-6b6a0e09-b7f9-40a8-a873-a008cda891df.png)


## 表格组件

```sh
 npm i axios 
 npm i mockjs -D
```

### 全局注册﻿表单组件

src\components\table\index.ts

```js
import { App } from 'vue';
import table from './src/index.vue';

// 让这个组件可以通过use的形式使用
export default {
  install(app: App) {
    app.component('my-table', table);
  },
};
```

### 实现表格组件

src\components\table\src\index.vue

```vue
<template>
  <el-table
    :data="tableData"
    v-loading="isLoading"
    :element-loading-text="elementLoadingText"
    :element-loading-spinner="elementLoadingSpinner"
    :element-loading-background="elementLoadingBackground"
    :element-loading-svg="elementLoadingSvg"
    :element-loading-svg-view-box="elementLoadingSvgViewBox"
    @row-click="rowClick"
    v-bind="$attrs"
  >
    <!-- 非操作 -->
    <template v-for="(item, index) in tableOption" :key="index">
      <el-table-column v-if="item.prop && !item.action" :label="item.label" :prop="item.prop" :width="item.width" :align="item.align">
        <!-- 自定义列模板 通过 slot 可以获取到 row, column, $index 和 store（table 内部的状态管理）的数据 -->
        <!-- 作用域插槽 props 对象 scope，由 element-plus 处理 scope -->
        <template #default="scope">
          <template v-if="scope.row.rowEdit">
            <!-- 点击编辑按钮全部变成编辑输入框 -->
            <el-input size="small" v-model="scope.row[item.prop!]"></el-input>
          </template>
          <template v-else>
            <!-- 点击编辑 icon 编辑 -->
            <template v-if="scope.$index + scope.column.id === currentEdit">
              <div style="display: flex">
                <el-input size="small" v-model="scope.row[item.prop!]"></el-input>
                <div>
                  <!-- 用户可自定义编辑按钮 -->
                  <slot name="cellEdit" v-if="$slots.cellEdit" :scope="scope"></slot>
                  <!-- 例子里使用下面默认的 -->
                  <div class="action-icon" v-else>
                    <el-icon-check class="check" @click.stop="check(scope)"></el-icon-check>
                    <el-icon-close class="close" @click.stop="close(scope)"></el-icon-close>
                  </div>
                </div>
              </div>
            </template>
            <!-- 未编辑状态 -->
            <template v-else>
              <!-- 具名插槽 name="date" name="name" 例子里是自定义日期列和姓名列，将 element-plus 处理的 scope 以作用域插槽 props 对象传递出去 -->
              <slot v-if="item.slot" :name="item.slot" :scope="scope"></slot>
              <!-- 例子里是地址列 -->
              <span v-else>{{ scope.row[item.prop!] }}</span>
              <!-- item.editable 控制显示编辑 icon ，例子里是日期列和地址列 -->
              <component :is="`el-icon-${toLine(editIcon)}`" class="edit" v-if="item.editable" @click.stop="clickEditIcon(scope)"></component>
            </template>
          </template>
        </template>
      </el-table-column>
    </template>
    <el-table-column :label="actionOption!.label" :width="actionOption!.width" :align="actionOption!.align">
      <!-- 操作 -->
      <template #default="scope">
        <!-- 编辑删除 -->
        <slot name="editRow" :scope="scope" v-if="scope.row.rowEdit"></slot>
        <!-- 确认取消 -->
        <slot name="action" :scope="scope" v-else></slot>
      </template>
    </el-table-column>
  </el-table>

  <div v-if="pagination && !isLoading" class="pagination" :style="{ justifyContent }">
    <el-pagination
      v-model:currentPage="currentPage"
      :page-sizes="pageSizes"
      :page-size="pageSize"
      layout="total, sizes, prev, pager, next, jumper"
      :total="total"
      @size-change="handleSizeChange"
      @current-change="handleCurrentChange"
    ></el-pagination>
  </div>
</template>

<script lang="ts" setup>
  import { PropType, computed, ref, watch, onMounted } from 'vue';
  import { TableOptions } from './types';
  import { toLine } from '../../../utils';
  import cloneDeep from 'lodash/cloneDeep';

  let props = defineProps({
    // 表格配置选项
    options: {
      type: Array as PropType<TableOptions[]>,
      required: true,
    },
    // 表格数据
    data: {
      type: Array,
      required: true,
    },
    // 加载文案
    elementLoadingText: {
      type: String,
    },
    // 加载图标名
    elementLoadingSpinner: {
      type: String,
    },
    // 加载背景颜色
    elementLoadingBackground: {
      type: String,
    },
    // 加载图标是svg
    elementLoadingSvg: {
      type: String,
    },
    // 加载团是svg的配置
    elementLoadingSvgViewBox: {
      type: String,
    },
    // 编辑显示的图标
    editIcon: {
      type: String,
      default: 'Edit',
    },
    // 是否可以编辑行
    isEditRow: {
      type: Boolean,
      default: false,
    },
    // 编辑行按钮的标识
    editRowIndex: {
      type: String,
      default: '',
    },
    // 是否显示分页
    pagination: {
      type: Boolean,
      default: false,
    },
    // 显示分页的对齐方式
    paginationAlign: {
      type: String as PropType<'left' | 'center' | 'right'>,
      default: 'left',
    },
    // 当前是第几页
    currentPage: {
      type: Number,
      default: 1,
    },
    // 当前一页多少条数据
    pageSize: {
      type: Number,
      default: 10,
    },
    // 显示分页数据多少条的选项
    pageSizes: {
      type: Array,
      default: () => [10, 20, 30, 40],
    },
    // 数据总条数
    total: {
      type: Number,
      default: 0,
    },
  });

  let emits = defineEmits(['confirm', 'cancel', 'update:editRowIndex', 'size-change', 'current-change']);

  // 分页的每一页数据变化
  let handleSizeChange = (val: number) => {
    emits('size-change', val);
  };
  // 分页页数改变
  let handleCurrentChange = (val: number) => {
    emits('current-change', val);
  };

  // 当前被点击的单元格的标识
  let currentEdit = ref<string>('');

  // 拷贝一份表格的数据
  let tableData = ref<any[]>(cloneDeep(props.data));
  // 拷贝一份按钮的标识
  let cloneEditRowIndex = ref<string>(props.editRowIndex);
  // 拷贝一份当前页数， v-model 不能绑定传进来的 prop
  let currentPage = ref<number>(cloneDeep(props.currentPage));

  // 监听当前页数
  watch(
    () => props.currentPage,
    (val) => {
      if (val) {
        currentPage.value = val;
      }
    }
  );

  // 如果 data 的数据变了 要重新给 tableData 赋值
  watch(
    () => props.data,
    (val) => {
      tableData.value = val;
    },
    { deep: true }
  );

  // 监听编辑按钮标识
  watch(
    () => props.editRowIndex,
    (val) => {
      if (val) cloneEditRowIndex.value = val;
    }
  );

  onMounted(() => {
    // 为每一条数据添加自定义属性 rowEdit
    tableData.value.map((item) => {
      item.rowEdit = false;
    });
  });

  // 过滤 除了操作项的配置
  let tableOption = computed(() => props.options.filter((item) => !item.action));
  // 操作项
  let actionOption = computed(() => props.options.find((item) => item.action));

  // 是否在加载中
  let isLoading = computed(() => !props.data || !props.data.length);

  // 表格分页的排列方式
  let justifyContent = computed(() => {
    if (props.paginationAlign === 'left') return 'flex-start';
    else if (props.paginationAlign === 'right') return 'flex-end';
    else return 'center';
  });

  // 点击编辑图标
  let clickEditIcon = (scope: any) => {
    console.log('scope: ', scope);
    // 做一个判断 判断是否当前单元格被点击了
    // 拼接 $index 和 column 的 id ，scope.$index 为行标识，scope.column.id 为列标识
    currentEdit.value = scope.$index + scope.column.id;
  };

  // 点击确认
  let check = (scope: any) => {
    emits('confirm', scope);
    currentEdit.value = '';
  };

  // 点击取消
  let close = (scope: any) => {
    emits('cancel', scope);
    currentEdit.value = '';
  };

  // 点击行的事件
  let rowClick = (row: any, column: any) => {
    // 判断是否是点击的操作项
    if (column.label === actionOption.value!.label) {
      if (props.isEditRow && cloneEditRowIndex.value === props.editRowIndex) {
        // 编辑行的操作
        row.rowEdit = !row.rowEdit;
        // 重置其他数据的rowEdit
        tableData.value.map((item) => {
          if (item !== row) item.rowEdit = false;
        });
        // 重置按钮的标识
        if (!row.rowEdit) emits('update:editRowIndex', '');
      }
    }
  };
</script>

<style lang="less" scoped>
  .edit {
    position: relative;
    top: 2px;
    left: 12px;
    width: 1em;
    height: 1em;
    cursor: pointer;
  }

  .action-icon {
    display: flex;

    svg {
      position: relative;
      top: 8px;
      margin-left: 8px;
      width: 1em;
      height: 1em;
      cursor: pointer;
    }

    .check {
      color: red;
    }

    .close {
      color: green;
    }
  }

  .pagination {
    display: flex;
    margin-top: 16px;
  }
</style>
```

src\components\table\src\types.ts

```ts
export interface TableOptions {
  // 字段名称
  prop?: string,
  // 表头
  label: string,
  // 对应列的宽度
  width?: string | number,
  // 对齐方式
  align?: 'left' | 'center' | 'right',
  // 自定义列模板的插槽名
  slot?: string,
  // 是否是操作项
  action?: boolean,
  // 是否可以编辑
  editable?: boolean
}
```

### 使用表格组件

src\views\table\index.vue

```vue
<template>
  <my-table
    :options="options"
    :data="tableData"
    elementLoadingText="加载中..."
    elementLoadingBackground="rgba(0,0,0,.8)"
    :element-loading-svg="svg"
    element-loading-svg-view-box="-10, -10, 50, 50"
    isEditRow
    pagination
    stripe
    border
    :total="total"
    :currentPage="current"
    :pageSize="pageSize"
    v-model:editRowIndex="editRowIndex"
    @confirm="confirm"
    @size-change="handleSizeChange"
    @current-change="handleCurrentChange"
  >
    <!-- 获取 element-plus 处理的 scope -->
    <template #date="{ scope }">
      <el-icon-timer></el-icon-timer>
      <span style="margin-left: 10px">{{ scope.row.date }}</span>
    </template>
    <template #name="{ scope }">
      <el-popover effect="light" trigger="hover" placement="top">
        <template #default>
          <p>姓名: {{ scope.row.name }}</p>
          <p>住址: {{ scope.row.address }}</p>
        </template>
        <template #reference>
          <div class="name-wrapper">
            <el-tag size="small">{{ scope.row.name }}</el-tag>
          </div>
        </template>
      </el-popover>
    </template>
    <!-- 按钮 -->
    <template #editRow="scope">
      <el-button size="small" type="primary" @click="sure(scope.scope)">确认</el-button>
      <el-button size="small" type="danger">取消</el-button>
    </template>
    <!-- 按钮 -->
    <!-- scope 为作用域擦插槽 prop 集合，即 element-plus 处理的 scope -->
    <template #action="scope">
      <el-button size="small" type="primary" @click="edit(scope.scope)">编辑</el-button>
      <el-button size="small" type="danger">删除</el-button>
    </template>
  </my-table>
</template>

<script lang="ts" setup>
  import { TableOptions } from '../../components/table/src/types';
  import { ref, onMounted } from 'vue';
  import axios from 'axios';

  let options: TableOptions[] = [
    {
      prop: 'date',
      label: '日期',
      // width: '180',
      align: 'center',
      slot: 'date',
      editable: true,
    },
    {
      prop: 'name',
      label: '姓名',
      // width: '180',
      align: 'center',
      slot: 'name',
    },
    {
      prop: 'address',
      label: '地址',
      align: 'center',
      editable: true,
    },
    {
      label: '操作',
      action: true,
      align: 'center',
    },
  ];
  let tableData = ref<any[]>([]);
  let editRowIndex = ref<string>('');
  let svg = `
        <path class="path" d="
          M 30 15
          L 28 17
          M 25.61 25.61
          A 15 15, 0, 0, 1, 15 30
          A 15 15, 0, 1, 1, 27.99 7.5
          L 15 15
        " style="stroke-width: 4px; fill: rgba(0, 0, 0, 0)"/>
      `;
  let current = ref<number>(1);
  let pageSize = ref<number>(10);
  let total = ref<number>(0);

  let getData = () => {
    axios
      .post('/api/list', {
        current: current.value,
        pageSize: pageSize.value,
      })
      .then((res: any) => {
        if (res.data.code === '200') {
          tableData.value = res.data.data.rows;
          total.value = res.data.data.total;
          console.log(res.data.data);
        }
      });
  };

  let handleSizeChange = (val: number) => {
    pageSize.value = val;
    getData();
  };

  let handleCurrentChange = (val: number) => {
    current.value = val;
    getData();
  };

  onMounted(() => {
    getData();
  });

  // 点击编辑按钮，
  let edit = (scope: any) => {
    // console.log(scope)
    // 改变编辑按钮标识
    editRowIndex.value = 'edit';
  };
  // 点击操作项确认按钮
  let sure = (scope: any) => {
    console.log(scope);
  };
  // 点击编辑 icon 编辑确认完成勾选
  let confirm = (scope: any) => {
    // console.log(scope)
  };
</script>

<style lang="less" scoped>
  svg {
    width: 1em;
    height: 1em;
  }
</style>
```

### Mock 数据

src\mock\index.ts

```ts
import Mock from 'mockjs'; // 引入mockjs
const Random = Mock.Random; // Mock.Random 是一个工具类，用于生成各种随机数据

interface DataList {
  date: string;
  name: string;
  address: string;
}

const dataList: DataList[] = []; // 用于接受生成数据的数组

for (let i = 0; i < 100; i++) {
  // 可自定义生成的个数
  const template = {
    date: Random.date(), // 生成一个随机日期,可加参数定义日期格式
    name: Random.name(), // 生成姓名
    address: Random.province(), // 生成地址
  };
  dataList.push(template);
}
// list 分页接口()

Mock.mock('/api/list', 'post', (params: any) => {
  let info = JSON.parse(params.body);
  let [index, size, total] = [info.current, info.pageSize, dataList.length];
  let len = total / size;
  let totalPages = len - parseInt(String(len)) > 0 ? parseInt(String(len)) + 1 : len;
  let newDataList = dataList.slice((index - 1) * size, index * size);

  return {
    code: '200',
    message: '获取成功',
    data: {
      current: index,
      pageSize: size,
      rows: newDataList,
      total: total,
      totalPages: totalPages,
    },
  };
});
```

src\main.ts

```ts
import { createApp } from 'vue';
import App from './App.vue';
import router from './router/index';
import ElementPlus from 'element-plus';
import 'element-plus/dist/index.css';
import * as ElementPlusIconsVue from '@element-plus/icons-vue';
import { toLine } from './utils';
import './mock';

import myUI from './components';

const app = createApp(App);
// 全局注册图标组件，ElementPlus 所有图标都是一个组件，牺牲一点性能
for (const [key, component] of Object.entries(ElementPlusIconsVue)) {
  app.component(`el-icon-${toLine(key)}`, component);
}
app.use(ElementPlus).use(myUI).use(router);
app.mount('#app');
```

### 效果图

![Snipaste_2023-04-29_15-23-52](https://cdn.nlark.com/yuque/0/2023/png/1614731/1682753040153-47df7014-9659-47a2-9101-16c666a7910e.png)

![Snipaste_2023-04-29_15-22-52](https://cdn.nlark.com/yuque/0/2023/png/1614731/1682753006014-f949c1a8-0723-4261-92b0-281e51b55267.png)

![Snipaste_2023-04-29_15-23-03](https://cdn.nlark.com/yuque/0/2023/png/1614731/1682753010743-c811c75a-3d54-4a10-ba65-84e16ec1baed.png)

## 打包

```sh
npm i fs-extra -D
```

package\chooseIcon\index.ts

修改图标为局部注册，而不是全局注册，方便使用和打包

```ts
import { App } from 'vue';
import chooseIcon from './src/index.vue';
import * as ElementPlusIconsVue from '@element-plus/icons-vue';
import { toLine } from '../utils';

// 让这个组件可以通过 use 的形式使用
export default {
  install(app: App) {
    // 全局注册图标组件，ElementPlus 所有图标都是一个组件，牺牲一点性能
    for (const [key, component] of Object.entries(ElementPlusIconsVue)) {
      app.component(`el-icon-${toLine(key)}`, component);
    }
    app.component('MyChooseIcon', chooseIcon);
  },
};
```

src\main.ts

```ts
import { createApp } from 'vue';
import App from './App.vue';
import router from './router/index';
import ElementPlus from 'element-plus';
import 'element-plus/dist/index.css';
import './mock';

import myUI from './components';

const app = createApp(App);
app.use(ElementPlus).use(myUI).use(router);
app.mount('#app');
```

component 下所有组件文件夹移入新建的文件夹 package

引入全局样式（如果有的话，这个项目没有使用）

package\index.ts

```ts
import { App } from 'vue';
import chooseArea from './chooseArea';
import chooseIcon from './chooseIcon';
import trend from './trend';
import notification from './notification';
import list from './list';
import menu from './menu';
import progress from './progress';
import chooseTime from './chooseTime';
import chooseDate from './chooseDate';
import chooseCity from './chooseCity';
import form from './form';
import modalForm from './modalForm';
import table from './table';
import calendar from './calendar';

const components = [chooseArea, chooseIcon, trend, notification, list, menu, progress, chooseTime, chooseDate, chooseCity, form, modalForm, table, calendar];

// 全局注册组件库里的组件
export default {
  install(app: App) {
    components.map((item) => {
      app.use(item);
    });
  },
};
```

hooks utils style 文件夹移入新建的文件夹 package，并修改路径 ../../../hooks ../../../utils  为 ../../hooks ../../utils

![Snipaste_2023-05-01_21-11-09](https://cdn.nlark.com/yuque/0/2023/png/1614731/1682946677393-94f58009-a966-40de-bf3e-770fda3090de.png)

package.json

```json
  "scripts": {
    "dev": "vite",
    "build": "vue-tsc --noEmit && vite build",
    "serve": "vite preview",
    "build:components": "node  ./command/build.js",
    "lib": "npm run build:components && cp package.json"
  },
```

package\vue.d.ts

```ts
declare module '*.vue' {
  import { DefineComponent } from 'vue'
  // eslint-disable-next-line @typescript-eslint/no-explicit-any, @typescript-eslint/ban-types
  const component: DefineComponent<{}, {}, any>
  export default component
}
```

command\build.js

```js
const path = require('path')
const fsExtra = require('fs-extra')
const fs = require('fs')
const { defineConfig, build } = require('vite')
const vue = require('@vitejs/plugin-vue')
const vueJsx = require('@vitejs/plugin-vue-jsx')


const entryDir = path.resolve(__dirname, '../package')
const outputDir = path.resolve(__dirname, '../lib')


const baseConfig = defineConfig({
  configFile: false,
  publicDir: false,
  plugins: [vue(), vueJsx()]
})

const rollupOptions = {
  external: ['vue', 'vue-router'],
  output: {
    globals: {
      vue: 'Vue'
    }
  }
}

// 全量构建
const buildAll = async () => {
  await build(defineConfig({
    ...baseConfig,
    build: {
      rollupOptions,
      lib: {
        entry: path.resolve(entryDir, 'index.ts'),
        name: 'index',
        fileName: 'index',
        formats: ['es', 'umd']
      },
      outDir: outputDir
    }
  }))
}


const buildSingle = async (name) => {
  await build(defineConfig({
    ...baseConfig,
    build: {
      rollupOptions,
      lib: {
        entry: path.resolve(entryDir, name),
        name: 'index',
        fileName: 'index',
        formats: ['es', 'umd']
      },
      outDir: path.resolve(outputDir, name)
    }
  }))
}

// 生成组件的 package.json 文件
const createPackageJson = (name) => {
  const fileStr = `{
    "name": "${name}",
    "version": "0.0.0",
    "main": "index.umd.js",
    "module": "index.es.js",
    "style": "style.css"
  }`

  fsExtra.outputFile(
    path.resolve(outputDir, `${name}/package.json`),
    fileStr,
    'utf-8'
  )
}


const buildLib = async () => {
  await buildAll()
  // 获取组件名称组成的数组
  const components = fs.readdirSync(entryDir).filter(name => {
    const componentDir = path.resolve(entryDir, name)
    const isDir = fs.lstatSync(componentDir).isDirectory()
    return isDir && fs.readdirSync(componentDir).includes('index.ts')
  })

  // 循环一个一个组件构建
  for (const name of components) {
    // 构建单组件
    await buildSingle(name)

    // 生成组件的 package.json 文件
    createPackageJson(name)
  }
}

buildLib()
```

vite.config.ts

```ts
import { defineConfig } from 'vite';
import vue from '@vitejs/plugin-vue';
import vueJsx from '@vitejs/plugin-vue-jsx';

export default defineConfig({
  plugins: [vue(), vueJsx()],
  css: {
    preprocessorOptions: {
      less: {
        javascriptEnabled: true,
      },
    },
  },
});
```

测试

src\main.ts

```ts
import { createApp } from 'vue';
import App from './App.vue';
import router from './router/index';
import ElementPlus from 'element-plus';
import 'element-plus/dist/index.css';
import './mock';

// import myUI from './components';
// 全量打包
// import myUI from '../lib/index.es.js';
// import '../lib/style.css'
// 按需打包
import chooseIcon from '../lib/chooseIcon/index.es.js'
import '../lib/chooseIcon/style.css'

const app = createApp(App);
app.use(ElementPlus).use(chooseIcon).use(router);
app.mount('#app');
```

```sh
npm run dev
```

```
http://localhost:3000/chooseIcon
```

## 发布组件库到 npm 

在 lib 文件夹下新建 package.json

```json
{
  "name": "element-components-lib",
  "version": "1.0.0",
  "main": "index.umd.js",
  "module": "index.es.js",
  "types": "index.d.ts",
  "author": {
    "name": "owo1z"
  },
  "keywords": [
    "element-plus",
    "ts",
    "封装组件",
    "二次封装",
    "vue-components"
  ]
}
```

lib 文件夹下新建  index.d.ts

```ts
import { App } from "vue"

// 告诉使用组件库的项目，该组件库是一个 Vue 的插件
declare const _default: {
  install: (app: App): void
} 

export default _default
```

cpfile.js

```js
const fs = require('fs');
const path = require('path');

const folder = path.resolve(__dirname, './lib');
const srcFile = path.resolve(folder, 'index.d.ts');

const files = fs.readdirSync(folder, { withFileTypes: true });

files.forEach(file => {
  if (file.isDirectory() && file.name !== 'utils') {
    fs.copyFileSync(srcFile, path.resolve(folder, file.name + '/index.d.ts'))
  }
})

// fs.readdir(folder, { withFileTypes: true }, (err, files) => {
//   if (err) return;
//   files.forEach(file => {
//     if (file.isDirectory() && file.name !== 'utils') {
//       fs.copyFileSync(srcFile, path.resolve(folder, file.name + '/index.d.ts'))
//       console.log('复制成功')
//     }
//   })
// })
```

终端执行，复制文件  index.d.ts 到每一个组件文件夹里

```sh
node cpfile.js
```

注册 并登录 npm 

```sh
# 设置 npm 镜像源为官方镜像
npm config set registry https://registry.npmjs.org
```

```sh
cd lib
npm login
npm publish
```

新建一个测试项目

```sh
# npm 7+, extra double-dash is needed:
npm create vite@latest my-vue-app -- --template vue

npm i element-plus @element-plus/icons-vue
# npm i 自己的组件库名
npm i element-components-lib
```

src\main.js

```js
import { createApp } from 'vue'
import './style.css'
import App from './App.vue'
// 引入 element-plus
import ElementPlus from 'element-plus'
import 'element-plus/dist/index.css'
// 全量引入
import myUI from 'element-components-lib';
import 'element-components-lib/style.css'

createApp(App).use(ElementPlus).use(myUI).mount('#app')
```

```js
import { createApp } from 'vue'
import './style.css'
import App from './App.vue'
import ElementPlus from 'element-plus'
import 'element-plus/dist/index.css'
// 按需引入
import chooseIcon from 'element-components-lib/chooseIcon';
import 'element-components-lib/chooseIcon/style.css';

createApp(App).use(ElementPlus).use(chooseIcon).mount('#app')
```

把 App.vue 代码删掉，复制一份 views 下使用组件库组件的代码

```vue
<template>
  <my-choose-icon title="选择图标" v-model:visible="visible">选择图标</my-choose-icon>
</template>

<script setup lang="ts">
  import { ref } from 'vue';

  let visible = ref<boolean>(false);
</script>
```

```sh
npm run dev
```

## 更新发布组件库

每次修改组件库打包需要把 package.json 和 index.d.ts 复制出来，因为打包后不会有这两个文件

然后执行

```sh
npm run lib
```

把 package.json 和 index.d.ts 重新放在 lib 文件夹下，

终端执行，复制文件  index.d.ts 到每一个组件文件夹里

```sh
node cpfile.js
```

把 package.json 的 version 修改后，

```sh
cd lib
npm publish
```

## 部署到 github/gitee

src\main.ts

```ts
import { createApp } from 'vue';
import App from './App.vue';
import router from './router/index';
import ElementPlus from 'element-plus';
import 'element-plus/dist/index.css';
import './mock';

import myUI from './components';

const app = createApp(App);
app.use(ElementPlus).use(myUI).use(router);
app.mount('#app');
```

src\router\index.ts

```diff
+ import { createRouter, createWebHashHistory, RouteRecordRaw } from 'vue-router';
import Home from '../views/home.vue';
import Container from '../components/container/src/index.vue';

const routes: RouteRecordRaw[] = [
  {
    path: '/',
    component: Container,
    children: [
      {
        path: '/',
        component: Home,
      },
      {
        path: '/chooseIcon',
        component: () => import('../views/chooseIcon/index.vue'),
      },
      {
        path: '/chooseArea',
        component: () => import('../views/chooseArea/index.vue'),
      },
      {
        path: '/trend',
        component: () => import('../views/trend/index.vue'),
      },
      {
        path: '/notification',
        component: () => import('../views/notification/index.vue'),
      },
      {
        path: '/menu',
        component: () => import('../views/menu/index.vue'),
      },
      {
        path: '/progress',
        component: () => import('../views/progress/index.vue'),
      },
      {
        path: '/chooseTime',
        component: () => import('../views/chooseTime/index.vue'),
      },
      {
        path: '/chooseCity',
        component: () => import('../views/chooseCity/index.vue'),
      },
      {
        path: '/form',
        component: () => import('../views/form/index.vue'),
      },
      {
        path: '/modalForm',
        component: () => import('../views/modalForm/index.vue'),
      },
      {
        path: '/table',
        component: () => import('../views/table/index.vue'),
      },
      {
        path: '/calendar',
        component: () => import('../views/calendar/index.vue'),
      },
    ],
  },
];

const router = createRouter({
  routes,
+  history: createWebHashHistory(),
});

export default router;
```

文件报错

提示：`JSX element class does not support attributes because it does not have a 'props' property.ts(2607)`

解决方法:

在项目根目录新建一个`stub/empty.d.ts`

```ts
// stub/empty.d.ts

export {}
```

同时，配置`tsconfig.json`

```json
{
  "compilerOptions": {
     "baseUrl": ".",
     "paths": {
        "preact/*": ["./stub/empty.d.ts"]
     }
  }
}
```

vite.config.ts

```ts
import { defineConfig } from 'vite';
import vue from '@vitejs/plugin-vue';
import vueJsx from '@vitejs/plugin-vue-jsx';

// https://vitejs.dev/config/
export default defineConfig({
  //部署git配置路径，否则报404
  base: './',
  plugins: [vue(), vueJsx()],
  css: {
    preprocessorOptions: {
      less: {
        javascriptEnabled: true,
      },
    },
  },
  build: {
    rollupOptions: {
      output: {
        // 用于从入口点创建的块的打包输出格式[name]表示文件名,[hash]表示该文件内容hash值
        entryFileNames: 'js/[name].[hash].js', // 用于命名代码拆分时创建的共享块的输出命名
        chunkFileNames: 'js/[name].[hash].js', // 用于输出静态资源的命名，[ext]表示文件扩展名
        assetFileNames: '[ext]/[name].[hash].[ext]',
      },
    },
  },
});

```

创建公开的仓库

github

```sh
git init
git add README.md
git commit -m "first commit"
git branch -M master
git remote add origin https://github.com/vdbvxcbcb/element-components-lib.git
git push -u origin master
```

齿轮图标 settings 里配置 Pages 服务

gitee

```sh
cd dist
git init 
git add .
git commit -m "first commit"
git remote add origin https://gitee.com/owo1z/element-components-lib.git
git push -u origin "master"
```

服务 => Gitee Pages 服务 











