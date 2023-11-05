data 数据可以怎么使用？

在模板中 {{}} 作为文本使用，可以使用表达式

在 v-if 中作为布尔值变量

在 v-for 中作为数据源

v-bind 属性绑定，将数据与特性绑定在一起

v-model 表单绑定作为初始值

class 样式绑定时的对象或存储布尔值的变量、style 样式绑定时的对象

vue 中 methods 、computed、 watch 的函数里可以直接使用 this. 访问



props 数据如何使用？

与 v-bind 结合，在子组件接收后使用方式与 data 类似，但是不可以在子组件中直接修改





v-for 循环出来的变量都可以在模板中任意使用，例如： v-for="(item, index) of title"

```vue
<div v-for="(item, index) of title" :key="index" 
  @click="tabClick(index)" :class="{active: currentIndex === index}"
  class="tab-control-item">
  <span>{{item}}</span> 
</div>  
```

computed 相当于根据原 data 或 props 的数据计算并构造出一个新的 data 数据

watch 观察 data、props 或 computed 数据的变化，数据一变就执行

 {{}} 可以直接放 data 的数据、props 的数据、methods 方法的返回值

