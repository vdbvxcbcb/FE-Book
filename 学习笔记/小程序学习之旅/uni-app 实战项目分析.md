## 配置路由页面、 tabBar 与分包页

page.json

```json
{
  "pages": [
    {
      "path": "pages/hot/hot",
      "style": {
        "navigationBarTitleText": "热搜",
        "enablePullDownRefresh": false
      }
    },
    {
      "path": "pages/hot-video/hot-video",
      "style": {
        "navigationBarTitleText": "热播",
        "enablePullDownRefresh": false
      }
    },
    {
      "path": "pages/my/my",
      "style": {
        "navigationBarTitleText": "个人中心",
        "enablePullDownRefresh": false
      }
    }
  ],
  "globalStyle": {
    "navigationBarTextStyle": "black",
    "navigationBarTitleText": "uni-app",
    "navigationBarBackgroundColor": "#F8F8F8",
    "backgroundColor": "#F8F8F8",
    "app-plus": {
      "background": "#efeff4"
    }
  },
  "tabBar": {
    "selectedColor": "#f94d2a",
    "list": [
      {
        "pagePath": "pages/hot/hot",
        "text": "热榜",
        "iconPath": "static/tab-icons/hot.png",
        "selectedIconPath": "static/tab-icons/hot-active.png"
      },
      {
        "pagePath": "pages/hot-video/hot-video",
        "text": "热播",
        "iconPath": "static/tab-icons/hot-video.png",
        "selectedIconPath": "static/tab-icons/hot-video-active.png"
      },
      {
        "pagePath": "pages/my/my",
        "text": "我的",
        "iconPath": "static/tab-icons/my.png",
        "selectedIconPath": "static/tab-icons/my-active.png"
      }
    ]
  },
  "subPackages": [
    {
      "root": "subpkg",
      "name": "sub-1",
      "pages": [
        {
          "path": "pages/search-blog/search-blog",
          "style": {
            "navigationBarTitleText": "慕课搜索",
            "enablePullDownRefresh": false
          }
        },
        {
          "path": "pages/blog-detail/blog-detail",
          "style": {
            "navigationBarTitleText": "",
            "enablePullDownRefresh": false
          }
        },
        {
          "path": "pages/login-page/login-page",
          "style": {
            "navigationBarTitleText": "",
            "enablePullDownRefresh": false
          }
        },
        {
          "path": "pages/video-detail/video-detail",
          "style": {
            "navigationBarTitleText": "",
            "enablePullDownRefresh": false
          }
        }
      ]
    }
  ]
}
```

## 封装请求

utils\request.js

```js
import store from '../store';
const BASE_URL = 'https://api.imooc-blog.lgdsunday.club/api';
function request({ url, data, method }) {
  return new Promise((resolve, reject) => {
    uni.request({
      url: BASE_URL + url,
      data,
      method,
      header: {
        Authorization: store.state.user.token
      },
      success: ({ data }) => {
        if (data.success) {
          resolve(data);
        } else {
          uni.showToast({
            title: data.message,
            icon: 'none',
            mask: true,
            duration: 3000
          });
          reject(data.message);
        }
      },
      fail: (error) => {
        reject(error);
      },
      complete: () => {
        // 关闭加载
        uni.hideLoading();
      }
    });
  });
}

export default request;
```

## 热搜文章列表页请求

api\hot.js

```js
import request from '../utils/request';

export function getHotTabs() {
  return request({
    url: '/hot/tabs'
  });
}

/**
 * 热搜文章列表
 */
export function getHotListFromTabType(type) {
  return request({
    url: '/hot/list',
    data: {
      type
    }
  });
}
```

## 热搜文章列表页

<img src="https://cdn.nlark.com/yuque/0/2022/png/1614731/1656511797518-10ae6593-eecb-472a-bea5-d9c1c5d4588a.png?x-oss-process=image%2Fresize%2Cw_394%2Climit_0" alt="2022-06-29_220920.png" style="zoom:67%;" />

pages\hot\hot.vue

```vue
<template>
  <view class="hot-container">
    <!-- logo -->
    <image class="logo" mode="aspectFit" src="@/static/images/logo.png" />
    <view class="search-box" @click="onToSearch">
      <!-- 搜索模块，可直接使用组件，无需注册 -->
      <my-search placeholderText="uni-app 自定义组件" />
    </view>
    <!-- tabs -->
    <view class="tab-sticky">
      <my-tabs
        :tabData="tabData"
        :defaultIndex="currentIndex"
        :config="{ textColor: '#333333' }"
        @tabClick="tabClick"
      ></my-tabs>
    </view>
    <!-- 基于 swiper 的 list 列表 -->
    <swiper
      class="swiper"
      :current="currentIndex"
      :style="{ height: currentSwiperHeight + 'px' }"
      @animationfinish="onSwiperEnd"
      @change="onSwiperChange"
    >
      <swiper-item class="swiper-item" v-for="(tabItem, tabIndex) in tabData" :key="tabIndex">
        <view>
          <!-- 加载动画 -->
          <uni-load-more status="loading" v-if="isLoading"></uni-load-more>
          <!-- 列表 -->
          <block v-else>
            <!-- 列表循环数据更改为 listData[tabIndex] -->
            <hot-list-item
              :class="'hot-list-item-' + tabIndex"
              v-for="(item, index) in listData[tabIndex]"
              :key="index"
              :data="item"
              :ranking="index + 1"
              @click="onItemClick(item)"
            ></hot-list-item>
          </block>
        </view>
      </swiper-item>
    </swiper>
  </view>
</template>

<script>
import { getHotTabs, getHotListFromTabType } from 'api/hot';
export default {
  data() {
    return {
      // tabs 数据源
      tabData: [],
      // 当前的切换 index
      currentIndex: 0,
      // list 列表数据加载过程
      isLoading: true,
      // 以 index 为 key，对应的 list 为 val
      listData: {},
      // 当前 swiper 的高度
      currentSwiperHeight: 0,
      // 以 index 为 key，对应的 swiper 的高度 为 val
      swiperHeightData: {},
      // 当前的滚动距离
      currentPageScrollTop: 0
    };
  },
  // 在实例创建完成后被立即调用
  created() {
    this.getHotTabs();
  },
  /**
   * 监听页面的滚动
   */
  onPageScroll(res) {
    this.currentPageScrollTop = res.scrollTop;
  },
  // 定义方法
  methods: {
    /**
     * 获取热搜标题数据
     */
    async getHotTabs() {
      // uniapp 支持 async await
      const { data: res } = await getHotTabs();
      this.tabData = res.list;
      // 获取列表数据
      this.getHotListFromTab();
    },
    /**
     * list 列表数据
     */
    async getHotListFromTab() {
      // 展示 loading
      this.isLoading = true;
      // 获取列表数据
      const id = this.tabData[this.currentIndex].id;
      const { data: res } = await getHotListFromTabType(id);
      // 放入数据缓存
      this.listData[this.currentIndex] = res.list;
      // 隐藏 loading
      this.isLoading = false;
      // 因为 this.$nextTick 存在一定的兼容性问题，所以更加推荐使用 setTimeout
      setTimeout(async () => {
        // 获取当前 swiper 的高度
        this.currentSwiperHeight = await this.getCurrentSwiperHeight();
        // 放入缓存
        this.swiperHeightData[this.currentIndex] = this.currentSwiperHeight;
      }, 0);
    },
    /**
     * tab item 的点击事件
     */
    tabClick(index) {
      this.currentIndex = index;
      // 获取列表数据
      // this.getHotListFromTab();
    },
    // 监听 swiper 的切换事件
    onSwiperChange(e) {
      if (this.currentPageScrollTop > 130) {
        // 控制列表滚动位置
        uni.pageScrollTo({
          scrollTop: 130
        });
      }
	  // 解决官方 swiper 在 change 里改变 current 值无限左右抖动的 bug
	  if (e.detail.source === 'touch') {
		this.currentIndex = e.detail.current;
	  }
    },
    /**
     * 解决卡顿问题；等待 swiper 动画完成之后，获取数据
     */
    onSwiperEnd() {
      // 判断缓存是否有数据，不存在则重新获取数据
      if (!this.listData[this.currentIndex]) {
        this.getHotListFromTab();
        return;
      }
      // 未 return ，则证明存在缓存数据，即同时存在 height 的缓存数据
      this.currentSwiperHeight = this.swiperHeightData[this.currentIndex];
    },
    /**
     * 计算当前 swiper 的高度
     */
    getCurrentSwiperHeight() {
      return new Promise((resolve, reject) => {
        let sum = 0;
        const query = uni.createSelectorQuery().in(this);
        query
          .selectAll(`.hot-list-item-${this.currentIndex}`)
          .boundingClientRect((res) => {
            res.forEach((item) => {
              sum += item.height;
            });
            resolve(sum);
          })
          .exec();
      });
    },
    /**
      搜索框点击事件
     */
    onToSearch() {
      uni.navigateTo({
        url: '/subpkg/pages/search-blog/search-blog'
      });
    },
    /**
     * item 点击事件
     */
    onItemClick(item) {
      uni.navigateTo({
        url: `/subpkg/pages/blog-detail/blog-detail?author=${item.user_name}&articleId=${item.id}`
      });
    }
  }
};
</script>

<style lang="scss" scoped>
.hot-container {
  background-color: $uni-bg-color;
  .logo {
    width: 100%;
    height: 80px;
  }
  .search-box {
    padding: 0 16px;
    margin-bottom: $uni-spacing-col-base;
  }
  .tab-sticky {
    position: -webkit-sticky;
    position: sticky;
    z-index: 99;
    /* #ifndef H5 */
    top: 0;
    /* #endif */
    /* #ifdef H5 */
    top: 44px;
    /* #endif */
  }
}
</style>
```

components\my-search\my-search.vue

```vue
<template>
  <view class="my-search-container">
    <!-- 搜索输入框 -->
    <uni-search-bar
      v-if="isShowInput"
      class="my-search-bar"
      :radius="100"
      @confirm="onSearch"
      @focus="onFocus"
      @blur="onBlur"
      @clear="onClear"
      @cancel="onCancel"
      @input="onInput"
      :bgColor="config.backgroundColor"
      :placeholder="placeholderText"
      :value="value"
    >
      <uni-icons slot="clearIcon" type="clear" color="#999999" />
    </uni-search-bar>
    <!-- 搜索按钮 -->
    <view
      class="my-search-box"
      v-else
      :style="{
        height: config.height + 'px',
        backgroundColor: config.backgroundColor,
        border: config.border
      }"
    >
      <image class="icon" :src="config.icon" />
      <text
        class="placeholder"
        :style="{
          color: config.textColor
        }"
        >{{ placeholderText }}</text
      >
    </view>
  </view>
</template>

<script>
export default {
  name: 'my-search',
  props: {
    // placeholder
    placeholderText: {
      type: String,
      default: '搜索'
    },
    // 配置对象
    config: {
      type: Object,
      default: () => ({
        height: 36,
        backgroundColor: '#ffffff',
        icon: '/static/images/search.png',
        textColor: '#454545',
        border: '1px solid #c9c9c9'
      })
    },
    // 是否显示输入框
    isShowInput: {
      type: Boolean,
      default: false
    },
    // 输入的内容
    // value 名称不可修改，与 $emit('input') 事件对应
    value: {
      type: String
    }
  },
  data() {
    return {};
  },
  methods: {
    /**
     * 点击搜索按钮触发
     */
    onSearch() {
      this.$emit('search', this.value);
    },
    /**
     * 输入框获取焦点触发
     */
    onFocus() {
      this.$emit('focus', this.value);
    },
    /**
     * 输入框失去焦点触发
     */
    onBlur() {
      this.$emit('blur', this.value);
    },
    /**
     * 点击输入框中的清空按钮时
     */
    onClear() {
      this.$emit('clear', this.value);
    },
    /**
     * 点击取消按钮时
     */
    onCancel() {
      this.$emit('cancel', this.value);
    },
    /**
     *  value 改变时触发事件
     */
    onInput(val) {
      // input 的事件名称不可修改，与 props 中的 value 对应
      // 当同时存在：
      // props -> value
      // $emit('input', val)
      // 时，在组件外可以使用 v-model 完成双向数据绑定。
      // 即：用户输入内容时，父组件传递过来的 value 同步发生变化
      // 详细见 vue 中 v-model 指令：https://cn.vuejs.org/v2/guide/components-custom-events.html#%E8%87%AA%E5%AE%9A%E4%B9%89%E7%BB%84%E4%BB%B6%E7%9A%84-v-model
      this.$emit('input', val);
    }
  }
};
</script>

<style lang="scss" scoped>
.my-search-container {
  display: flex;
  align-items: center;
  .my-search-bar {
    width: 100%;
  }
  .my-search-box {
    height: 36px;
    background-color: #ffffff;
    border-radius: 15px;
    border: 1px solid #c9c9c9;
    width: 100%;
    display: flex;
    align-items: center;
    padding: 0 $uni-spacing-row-base;

    .icon {
      width: $uni-img-size-sm;
      height: $uni-img-size-sm;
    }

    .placeholder {
      font-size: $uni-font-size-sm;
      margin-left: $uni-spacing-row-sm;
      color: #454545;
    }
  }
}
</style>
```

components\my-tabs\my-tabs.vue

```vue
<template>
  <view class="tab-container">
    <view class="tab-box">
      <scroll-view
        id="_scroll"
        scroll-x
        class="scroll-view"
        scroll-with-animation
        :scroll-left="scrollLeft"
      >
        <view class="scroll-content">
          <view class="tab-item-box">
            <block v-for="(item, index) in tabList" :key="index">
              <view
                class="tab-item"
                :id="'_tab_' + index"
                :class="{ 'tab-item-active': activeIndex === index }"
                @click="tabClick(index)"
                :style="{
                  color:
                    activeIndex === index ? defaultConfig.activeTextColor : defaultConfig.textColor
                }"
                >{{ item.label || item }}</view
              >
            </block>
          </view>
          <!-- 滑块 -->
          <view
            class="underLine"
            :style="{
              transform: 'translateX(' + slider.left + 'px)',
              width: defaultConfig.underLineWidth + 'px',
              height: defaultConfig.underLineHeight + 'px',
              backgroundColor: defaultConfig.underLineColor
            }"
          />
        </view>
      </scroll-view>
    </view>
  </view>
</template>

<script>
export default {
  name: 'my-tabs',
  props: {
    // 父组件传入的 tabs 数据
    tabData: {
      type: Array,
      default: () => []
    },
    // 默认激活项
    defaultIndex: {
      type: Number,
      default: 0
    },
    // 配置对象
    config: {
      type: Object,
      default: () => {
        return {};
      }
    }
  },
  data: () => {
    return {
      // 内部维护的数据对象，为每个 item 单独额外维护一个 slider 的滑块对象
      tabList: [],
      // 当前激活项的 index
      activeIndex: -1,
      // 滑块对象
      slider: {
        // 距离左侧的距离
        left: 0
      },
      // scrollView 的横向滚动条位置
      scrollLeft: 0,
      // 默认配置
      defaultConfig: {
        // 默认的字体颜色
        textColor: '#333333',
        // 高亮字体颜色
        activeTextColor: '#f94d2a',
        // 下划线宽度 px
        underLineWidth: 24,
        // 下划线高度 px
        underLineHeight: 2,
        // 下划线颜色
        underLineColor: '#f94d2a'
      }
    };
  },
  // 侦听器
  watch: {
    // 侦听数据的变化
    tabData: {
      handler(val) {
        this.tabList = val;
        setTimeout(() => {
          this.updateTabWidth();
        }, 0);
      },
      // 该回调将会在侦听开始之后被立即调用
      immediate: true
    },
    // 监听激活项目的变化
    defaultIndex: {
      handler(val) {
        this.activeIndex = val;
        // 定义滑块的位置
        this.tabToIndex();
      },
      // 该回调将会在侦听开始之后被立即调用
      immediate: true
    },
    // 监听 config
    config: {
      handler(val) {
        this.defaultConfig = { ...this.defaultConfig, ...val };
      },
      // 该回调将会在侦听开始之后被立即调用
      immediate: true
    }
  },
  methods: {
    /**
     * 更新 tab item 的宽度
     */
    updateTabWidth() {
      /**
       * 为 tabList 的每个 item 单独额外维护一个 slider 的滑块对象
       */
      let data = this.tabList;
      if (data.length == 0) return false;

      // 获取 dom 的固定写法
      const query = uni.createSelectorQuery().in(this);
      // 循环数据源
      data.forEach((item, index) => {
        // 获取 dom 的固定写法
        query
          .select('#_tab_' + index)
          .boundingClientRect((res) => {
            // 为数据对象中每一个 item 都维护一个 _slider（滑动条） 对象
            item._slider = {
              // 当前的 tab 距离左侧的距离
              left: res.left + (res.width - this.defaultConfig.underLineWidth) / 2
            };
            // 运算完成之后，执行一次 【滑块】位置运算
            if (data.length - 1 === index) {
              this.tabToIndex();
            }
          })
          .exec();
      });
    },
    /**
     * tab 的点击事件处理
     */
    tabClick(index) {
      this.activeIndex = index;
      // 定义滑块的位置
      this.tabToIndex();
      // 发送通知
      this.$emit('tabClick', index);
    },
    /**
     * 根据当前的 activeIndex 下标，计算 【滑块】 滚动位置
     */
    tabToIndex() {
      if (this.tabList.length === 0) return;
      // 获取当前的 activeIndex
      const activeIndex = this.activeIndex;
      // 滑块的宽度
      const underLineWidth = this.defaultConfig.underLineWidth;
      // 配置 滚动条 的数据
      this.slider = {
        // TODO：left 如何定义呢？
        // 1. 维护一个单独的数据对象 `tabList`
        // 2. 在 `tabList`  的 `item` 中为一个 `_slider` 属性
        // 3. 该属性保存了 【当前 `item` 下 的滑块位置】
        //    3.1. 计算公式：`滑块左侧位置 = item.left + (item.width - slider.width) / 2`
        left: this.tabList[activeIndex]._slider.left
      };
      // 为 scrollView 设置滚动位置
      this.scrollLeft = this.activeIndex * this.defaultConfig.underLineWidth;
    }
  }
};
</script>

<style lang="scss" scoped>
.tab-container {
  font-size: $uni-font-size-base;
  height: 45px;
  line-height: 45px;
  background-color: $uni-bg-color;
  .tab-box {
    width: 100%;
    height: 45px;
    display: flex;
    position: relative;
    .scroll-view {
      white-space: nowrap;
      width: 100%;
      height: 100%;
      box-sizing: border-box;
      .scroll-content {
        width: 100%;
        height: 100%;
        position: relative;

        .tab-item-box {
          height: 100%;
          .tab-item {
            height: 100%;
            display: inline-block;
            text-align: center;
            padding: 0 15px;
            position: relative;
            text-align: center;
            color: $uni-text-color;

            &-active {
              color: $uni-text-color-hot;
            }
          }
        }
        .underLine {
          height: 2px;
          width: 25px;
          background-color: #f01414;
          border-radius: 3px;
          transition: 0.5s;
          position: absolute;
          bottom: 0;
        }
      }
    }
  }

  /* #ifdef H5 */
  /deep/.uni-scroll-view::-webkit-scrollbar {
    display: none;
  }

  /deep/.uni-scroll-view {
    scrollbar-width: none;
  }
  /* #endif */
}
</style>
```

components\hot-list-item\hot-list-item.vue

```vue
<template>
  <view class="item-container" @click="$emit('click')">
    <view class="item-box">
      <view class="item-box-left">
        <hot-ranking :ranking="ranking" />
      </view>
      <view class="item-box-right">
        <!-- 标题 -->
        <view class="item-title line-clamp-2">{{ data.title }}</view>
        <!-- 简介 -->
        <view class="item-desc line-clamp-2">{{ data.desc }}</view>
        <view class="item-bottom-box">
          <!-- 作者 -->
          <text class="item-author">{{ data.nickname }}</text>
          <!-- 热度 -->
          <view class="hot-box">
            <image class="hot-icon" src="@/static/images/hot-icon.png" />
            <text class="hot-text">{{ data.views | hotNumber }} 热度</text>
          </view>
        </view>
      </view>
    </view>
  </view>
</template>

<script>
export default {
  name: 'hot-list-item',
  props: {
    data: {
      type: Object,
      required: true
    },
    ranking: {
      type: Number,
      required: true
    }
  },
  data() {
    return {};
  }
};
</script>

<style lang="scss" scoped>
.item-container {
  padding-bottom: $uni-spacing-col-lg;
  .item-box {
    display: flex;
    margin: 0 $uni-spacing-col-base;
    padding: $uni-spacing-row-lg $uni-spacing-col-base;
    background-color: $uni-bg-color;
    border-radius: $uni-border-radius-lg;
    box-shadow: 2px 2px 5px 1px rgba(143, 143, 143, 0.1);
    .item-box-left {
      margin-right: $uni-spacing-row-sm;
    }
    .item-box-right {
      width: 100%;
      .item-title {
        font-size: $uni-font-size-lg;
        font-weight: bold;
        color: $uni-text-color-title;
      }
      .item-desc {
        padding-top: $uni-spacing-row-sm;
        font-size: $uni-font-size-base;
        color: $uni-text-color;
      }
      .item-bottom-box {
        margin-top: $uni-spacing-col-sm;
        display: flex;
        justify-content: space-between;
        align-items: center;
        .item-author {
          font-size: $uni-font-size-sm;
          color: $uni-text-color-grey;
        }
        .hot-box {
          .hot-icon {
            width: $uni-img-size-sm;
            height: $uni-img-size-sm;
            vertical-align: top;
          }
          .hot-text {
            margin-left: $uni-spacing-row-sm;
            font-size: $uni-font-size-sm;
            color: $uni-text-color-hot;
          }
        }
      }
    }
  }
}
</style>
```

filters\index.js

```js
// 1. 导入dayjs
import dayjs from 'dayjs';
// 2. dayjs 默认语言是英文，我们这里配置为中文
import 'dayjs/locale/zh-cn';
// 3. 引入 relativeTime
import rTime from 'dayjs/plugin/relativeTime';

// 4. 加载中文语言包
dayjs.locale('zh-cn');
// 5. 加载相对时间插件
dayjs.extend(rTime);

/**
 * 6. 定义过滤器，通过 dayjs().to(dayjs(val)) 方法把【当前时间】处理为【相对时间】
 */
export function relativeTime(val) {
  return dayjs().to(dayjs(val));
}

/**
 * 将字符转化为以千为单位的字符
 * @param {*} val 待转换字符
 * @returns
 */
export function hotNumber(val) {
  const num = parseInt(val);
  if (num < 1000) return val;

  // 将 val 转为字符串
  val = val + '';
  // 获取以 千 为单位的值
  return val.substring(0, val.length - 3) + 'k';
}
```

main.js

```js
import Vue from 'vue';
import App from './App';
// 导入 vuex 实例
import store from './store';
// 通用样式
import './styles/global.scss';
// 文章详情样式
import './styles/article-detail.scss';
// 导入过滤器
import * as filters from './filters';

// 注册过滤器
Object.keys(filters).forEach((key) => {
  Vue.filter(key, filters[key]);
});

Vue.config.productionTip = false;

App.mpType = 'app';

const app = new Vue({
  ...App,
  store // 挂载实例对象
});
app.$mount();
```

components\hot-ranking\hot-ranking.vue

```vue
<template>
  <view class="ranking">
    <image class="ranking-bg" :src="getRankingBg" />
    <text class="ranking-text" :class="{ 'text-white': ranking <= 3 }">{{ ranking }}</text>
  </view>
</template>

<script>
export default {
  name: 'hot-ranking',
  props: {
    ranking: {
      type: Number,
      required: true
    }
  },
  /**
   * 当依赖值发生变化时，会重新计算
   */
  computed: {
    getRankingBg() {
      if (this.ranking <= 3) {
        return require(`@/static/images/ranking-${this.ranking}.png`);
      }
      return require('@/static/images/ranking-other.png');
    }
  }
};
</script>

<style lang="scss" scoped>
.ranking {
  position: relative;
  text-align: center;
  width: 18px;
  height: 20px;
  .ranking-bg {
    width: 100%;
    height: 100%;
  }
  .ranking-text {
    position: absolute;
    left: 50%;
    top: 50%;
    transform: translate(-55%, -55%);
    font-size: $uni-font-size-sm;
    font-weight: bold;
    color: $uni-text-color;
  }
  .text-white {
    color: white;
  }
}
</style>
```

## 文章搜索页请求

api\search.js

```js
import request from '../utils/request';

/**
 * 默认搜索内容
 */
export function getDefaultText() {
  return request({
    url: '/search/default-text'
  });
}

/**
 * 热搜搜索列表
 */
export function getSearchHotList() {
  return request({
    url: '/search/hot-list'
  });
}

/**
 * 搜索结果
 */
export function getSearchResult(data) {
  return request({
    url: '/search',
    data
  });
}
```

## 文章搜索页

<img src="https://cdn.nlark.com/yuque/0/2022/png/1614731/1656511323038-a5a6f69c-7e6b-45ac-b29c-af8a23bf21db.png" alt="2022-06-29_215204.png" style="zoom:67%;" />

<img src="https://cdn.nlark.com/yuque/0/2022/png/1614731/1656511323001-c772f323-01c4-40b0-a8cc-27c07a457dd1.png" alt="2022-06-29_215144.png" style="zoom:67%;" />

<img src="https://cdn.nlark.com/yuque/0/2022/png/1614731/1656511323122-9b58a0c6-03fa-4bf1-8d9b-ffc098212ff4.png?x-oss-process=image%2Fresize%2Cw_426%2Climit_0" alt="2022-06-29_214953.png" style="zoom: 67%;" />

subpkg\pages\search-blog\search-blog.vue

```vue
<template>
  <view class="search-blog-container">
    <!-- search模块 -->
    <view class="search-bar-box">
      <my-search
        :placeholderText="defaultText"
        v-model="searchVal"
        :isShowInput="true"
        :config="{
          backgroundColor: '#f1f0f3'
        }"
        @search="onSearchConfirm"
        @focus="onSearchFocus"
        @blur="onSearchBlur"
        @clear="onSearchClear"
        @cancel="onSearchCancel"
      />
    </view>
    <!-- 热搜列表 -->
    <view class="search-hot-list-box card" v-if="showType === HOT_LIST">
      <!-- 列表 -->
      <search-hot-list @onSearch="onSearchConfirm" />
    </view>
    <!-- 搜索历史 -->
    <view class="search-history-box" v-else-if="showType === SEARCH_HISTORY">
      <search-history @onItemClick="onSearchConfirm" />
    </view>
    <!-- 搜索结果 -->
    <view class="search-result-box" v-else>
      <!-- 1. 给mescroll-body的组件添加: ref="mescrollItem" (固定的,不可改,与mescroll-comp.js对应)-->
      <search-result-list ref="mescrollItem" :queryStr="searchVal" />
    </view>
  </view>
</template>

<script>
// 2. 引入mescroll-comp.js
import MescrollCompMixin from '@/uni_modules/mescroll-uni/components/mescroll-uni/mixins/mescroll-comp.js';
// 导入 mapMutations 函数
import { mapMutations } from 'vuex';

import { getDefaultText } from 'api/search';
// 0: 热搜列表 - 默认
const HOT_LIST = '0';
// 1：搜索历史
const SEARCH_HISTORY = '1';
// 2：搜索结果
const SEARCH_RESULT = '2';
export default {
  // 3. 注册 mixins
  mixins: [MescrollCompMixin],
  data() {
    return {
      HOT_LIST,
      SEARCH_HISTORY,
      SEARCH_RESULT,
      // 默认情况下 || 点击输入框的取消按钮时，显示【热搜列表】
      // 当 searchBar 获取焦点时 || 点击输入框清空按钮时，显示 【搜索历史】
      // 用户点击热搜列表 item || 用户点击搜索历史 || 用户按下搜索键，显示 【搜索结果】
      showType: HOT_LIST,
      // 绑定输入框中的内容
      searchVal: '',
      // 默认的placeholderText
      defaultText: '默认的placeholderText',
      // 搜索历史数据
      searchData: []
    };
  },
  created() {
    this.loadDefaultText();
  },
  methods: {
    ...mapMutations('search', ['addSearchData']),
    /**
     * 获取推荐搜索文本
     */
    async loadDefaultText() {
      const { data: res } = await getDefaultText();
      this.defaultText = res.defaultText;
    },
    /**
     * 搜索内容
     */
    onSearchConfirm(val) {
      // 用户未输入文本，直接搜索时，使用【推荐搜索文本】
      this.searchVal = val ? val : this.defaultText;
      // 保存搜索历史数据
      this.addSearchData(this.searchVal);
      // 切换视图
      if (this.searchVal) {
        this.showType = SEARCH_RESULT;
      }
    },
    /**
     * @deprecated
     * 保存搜索历史数据
     */
    saveSearchData() {
      // 1. 如果数据已存在，则删除
      const index = this.searchData.findIndex((item) => item === this.searchVal);
      if (index !== -1) {
        this.searchData.splice(index, 1);
      }
      // 2. 新的搜索内容需要先于旧的搜索内容展示
      this.searchData.unshift(this.searchVal);
    },
    /**
     * @deprecated
     * 删除数据
     */
    onRemoveSearchData(index) {
      this.searchData.splice(index, 1);
    },
    /**
     * @deprecated
     */
    onRemoveAllSearchData() {
      this.searchData = [];
    },
    // searchbar 获取焦点
    onSearchFocus(val) {
      this.showType = SEARCH_HISTORY;
    },
    /**
     * searchbar 失去焦点
     */
    onSearchBlur(val) {},
    /**
     * searchbar 清空内容
     */
    onSearchClear() {
      // 切换视图
      this.showType = SEARCH_HISTORY;
    },
    /**
     * searchbar 取消按钮
     */
    onSearchCancel(val) {
      this.showType = HOT_LIST;
    }
  }
};
</script>

<style lang="scss" scoped>
.search-blog-container {
  .search-bar-box {
    background-color: $uni-bg-color;
    padding: $uni-spacing-row-sm;
    position: sticky;
    top: 0px;
    z-index: 9;
  }
}
</style>
```

components\search-hot-list\search-hot-list.vue

```vue
<template>
  <view class="search-hot-list-container">
    <!-- 标题 -->
    <view class="search-hot-title">慕课热搜 - 全网技术 一网打尽</view>
    <block v-for="(item, index) in hotList" :key="index">
      <view class="search-hot-item" @click="onItemClick(item, index)">
        <!-- 序号 -->
        <hot-ranking :ranking="index + 1"></hot-ranking>
        <!-- 文本 -->
        <text class="title line-clamp">{{ item.label }}</text>
        <!-- hot-icon -->
        <image v-if="index <= 2" class="search-hot-icon" src="/static/images/hot-icon.png" />
      </view>
    </block>
  </view>
</template>

<script>
import { getSearchHotList } from 'api/search';

export default {
  name: 'search-hot-list',
  data() {
    return {
      hotList: []
    };
  },
  created() {
    this.getSearchHotList();
  },
  methods: {
    /**
     * 获取热搜列表数据
     */
    async getSearchHotList() {
      const { data: res } = await getSearchHotList();
      this.hotList = res.list;
    },
    /**
     * item 点击事件
     */
    onItemClick(item, index) {
      this.$emit('onSearch', item.label);
    }
  }
};
</script>

<style lang="scss" scoped>
.search-hot-list-container {
  .search-hot-title {
    font-weight: bold;
    font-size: $uni-font-size-base;
    color: $uni-text-color-hot;
    padding: 0 12px $uni-spacing-col-lg 12px;
    margin: 0 -12px $uni-spacing-col-lg -12px;
    box-shadow: 2px 2px 5px 1px rgba(143, 143, 143, 0.1);
  }
  .search-hot-item {
    display: flex;
    align-items: center;
    padding: $uni-spacing-col-lg 0;
    .title {
      color: $uni-text-color;
      font-size: $uni-font-size-base;
      margin: 0 $uni-spacing-row-base;
    }
    .search-hot-icon {
      width: 14px;
      height: 14px;
    }
  }
}
</style>
```

components\search-history\search-history.vue

```vue
<template>
  <view class="search-history-container">
    <!-- 3. 使用导入的 vuex 模块中的数据 -->
    <!-- <div>{{ msg }}</div> -->
    <!-- title 区域 -->
    <view class="search-history-title-box">
      <view class="search-history-title">搜索历史</view>
      <view v-if="!isShowClear">
        <uni-icons type="trash" @click="isShowClear = true" />
      </view>
      <view v-else>
        <text class="txt" @click="onClearAll">全部删除</text>
        <text class="txt" @click="isShowClear = false">完成</text>
      </view>
    </view>
    <!-- 内容区域 -->
    <view class="search-history-box">
      <block v-for="(item, index) in searchData" :key="index">
        <view class="search-history-item" @click="onHistoryItemClick(item, index)">
          <text class="history-txt line-clamp nowrap">{{ item }}</text>
          <uni-icons v-show="isShowClear" type="clear" />
        </view>
      </block>
    </view>
  </view>
</template>

<script>
// 1. 导入 mapState 函数
import { mapState, mapMutations } from 'vuex';

export default {
  name: 'search-history',
  props: {
    /**
     * @deprecated
     */
    // searchData: {
    //   type: Array,
    //   required: true
    // }
  },
  data() {
    return {
      isShowClear: false
    };
  },
  methods: {
    ...mapMutations('search', ['removeSearchData', 'removeAllSearchData']),
    onClearAll() {
      uni.showModal({
        title: '提示',
        content: '删除搜索历史记录？',
        showCancel: true,
        success: ({ confirm, cancel }) => {
          if (confirm) {
            // 删除 searchData
            this.removeAllSearchData();
            // 返回状态
            this.isShowClear = false;
          }
        }
      });
    },
    onHistoryItemClick(item, index) {
      if (this.isShowClear) {
        // 删除指定的 searchData
        this.removeSearchData(index);
      } else {
        this.$emit('onItemClick', item);
      }
    }
  },
  computed: {
    // 2. 在 computed 中，通过 mapState 函数，注册 state 中的数据，导入之后的数据可直接使用（就像使用 data 中的数据一样）
    // mapState(模块名, ['字段名','字段名','字段名'])
    ...mapState('search', ['searchData'])
  }
};
</script>

<style lang="scss" scoped>
.search-history-container {
  padding: $uni-spacing-col-lg $uni-spacing-row-lg;
  .search-history-title-box {
    display: flex;
    justify-content: space-between;

    .search-history-title {
      font-size: $uni-font-size-sm;
      color: $uni-text-color;
      padding: $uni-spacing-col-sm $uni-spacing-row-sm;
    }
    .txt {
      color: $uni-text-color-grey;
      font-size: $uni-font-size-sm;
      padding: $uni-spacing-col-sm $uni-spacing-row-sm;
    }
  }
  .search-history-box {
    margin-top: $uni-spacing-col-lg;
    .search-history-item {
      width: 50%;
      box-sizing: border-box;
      display: inline-block;
      padding: $uni-spacing-col-base $uni-spacing-row-base;
      position: relative;
      .history-txt {
        width: 95%;
        display: inline-block;
        color: $uni-text-color;
        font-size: $uni-font-size-base;
      }
    }
    .search-history-item:nth-child(odd):before {
      content: ' ';
      border-left: 1px solid #999;
      display: inline-block;
      height: 10px;
      position: absolute;
      top: 50%;
      transform: translateY(-50%);
      right: 0;
    }
  }
}
</style>
```

components\search-result-list\search-result-list.vue

```vue
<template>
  <view class="search-result-list-container">
    <empty-data v-if="isEmpty"></empty-data>
    <!-- 1. 通过 mescroll-body 包裹列表，指定 ref 为 mescrollRef ，监听@init、@down、@up 事件 -->
    <mescroll-body
      v-else
      ref="mescrollRef"
      @init="mescrollInit"
      @down="downCallback"
      @up="upCallback"
    >
      <!-- 循环渲染列表数据 -->
      <block v-for="(item, index) in resultList" :key="index">
        <view class="search-result-item-box" @click="onItemClick(item)">
          <!-- 内容区 - 样式 1 -->
          <search-result-item-theme-1
            v-if="!item.pic_list || item.pic_list.length === 0"
            :data="item"
          />

          <!-- 内容区 - 样式 2 -->
          <search-result-item-theme-2 v-else-if="item.pic_list.length === 1" :data="item" />

          <!-- 内容区 - 样式 3 -->
          <search-result-item-theme-3 v-else :data="item" />

          <!-- / -->
        </view>
      </block>
    </mescroll-body>
  </view>
</template>

<script>
// 2. 导入对应的 mixins
import MescrollMixin from '@/uni_modules/mescroll-uni/components/mescroll-uni/mescroll-mixins.js';
import { getSearchResult } from 'api/search';
export default {
  name: 'search-result-list',
  // 3. 注册 mixins
  mixins: [MescrollMixin],
  props: {
    // 搜索关键字
    queryStr: {
      type: String,
      required: true
    }
  },
  data() {
    return {
      // 数据源
      resultList: [],
      // 页数
      page: 1,
      // 实例
      mescroll: null,
      // 处理渲染，会回调 down和up 方法，为了避免该问题，定义 init 变量，表示当前是否为首次请求
      isInit: true,
      // 是否无数据
      isEmpty: false
    };
  },
  // created() {
  //   不需要主动调用
  //   this.loadSearchResult();
  // },
  // 页面渲染完成之后回调，想要获取组件实例，需要在该回调中进行
  mounted() {
    // 通过 $refs 获取组件实例
    this.mescroll = this.$refs.mescrollRef.mescroll;
  },
  methods: {
    // 4. 实现三个回调方法
    /**
     * List 组件的首次加载
     */
    async mescrollInit() {
      await this.loadSearchResult();
      this.isInit = false;
      // 结束 上拉加载 && 下拉刷新
      this.mescroll.endSuccess();
    },
    /**
     * 下拉刷新的回调
     */
    async downCallback() {
      if (this.isInit) return;
      this.page = 1;
      await this.loadSearchResult();
      // 结束 上拉加载 && 下拉刷新
      this.mescroll.endSuccess();
    },
    /**
     * 上拉加载的回调
     */
    async upCallback() {
      if (this.isInit) return;
      this.page += 1;
      await this.loadSearchResult();
      // 结束 上拉加载 && 下拉刷新
      this.mescroll.endSuccess();
    },
    /**
     * 获取搜索数据
     */
    async loadSearchResult() {
      const { data: res } = await getSearchResult({
        q: this.queryStr,
        p: this.page
      });
      // 使用下拉刷新上拉加载的赋值策略
      // this.resultList = res.list;
      // 更改返回数据样式（行内样式）
      res.list.forEach((item) => {
        item.title = item.title.replace(/<em>/g, "<em style='color:#f94d2a; margin:0 2px'>");
        item.description = item.description.replace(
          /<em>/g,
          "<em style='color:#f94d2a; margin:0 2px'>"
        );
      });
      // 判断是否为第一页数据
      if (this.page === 1) {
        this.resultList = res.list;
      } else {
        this.resultList = [...this.resultList, ...res.list];
      }

      // this.resultList = [];
      // 无数据，显示空数据组件
      if (this.resultList.length === 0) {
        this.isEmpty = true;
      }
    },
    /**
     * item 点击事件
     */
    onItemClick(item) {
      uni.navigateTo({
        url: `/subpkg/pages/blog-detail/blog-detail?author=${item.author}&articleId=${item.id}`
      });
    }
  }
};
</script>

<style lang="scss" scoped>
.search-result-list-container {
  padding: $uni-spacing-col-lg $uni-spacing-row-lg;
  .search-result-item-box {
    margin-bottom: $uni-spacing-col-big;
  }
}
</style>
```

components\empty-data\empty-data.vue

```vue
<template>
  <view class="empty-data-cocntainer">
    <image src="/static/images/empty-data.png" class="img" />
    <view class="txt">暂无数据</view>
  </view>
</template>

<script>
export default {
  name: 'empty-data',
  data() {
    return {};
  }
};
</script>

<style lang="scss" scoped>
.empty-data-cocntainer {
  display: flex;
  flex-direction: column;
  align-items: center;
  padding-top: 35%;
  .img {
    width: 64px;
    height: 64px;
  }
  .txt {
    margin-top: $uni-spacing-col-sm;
    color: $uni-text-color-grey;
    font-size: $uni-font-size-base;
  }
}
</style>
```

components\search-result-item-theme-1\search-result-item-theme-1.vue

```vue
<template>
  <!-- 样式1 ： 无图片样式 -->
  <view class="search-result-item-box">
    <!-- 标题 -->
    <rich-text :nodes="data.title" class="item-title line-clamp-2"></rich-text>
    <!-- 内容区 - 样式 1 -->
    <rich-text :nodes="data.description" class="item-desc line-clamp-2"></rich-text>
    <!-- 底部 -->
    <view class="item-desc-bottom">
      <view class="item-author">{{ data.author }}</view>
      <view class="item-read-num">
        <uni-icons class="read-num-icon" color="#999999" type="compose" />
        <text>{{ data.updateTime | relativeTime }}</text>
      </view>
    </view>
  </view>
</template>

<script>
export default {
  props: {
    data: {
      type: Object,
      required: true
    }
  },
  data: () => ({})
};
</script>

<style lang="scss" scoped>
.search-result-item-box {
  margin-bottom: $uni-spacing-col-big;
  // 标题
  .item-title {
    font-size: $uni-font-size-base;
    font-weight: bold;
    color: $uni-text-color-title;
  }

  .item-desc {
    margin-top: $uni-spacing-col-base;
    font-size: $uni-font-size-base;
    color: $uni-color-subtitle;
  }

  // 底部作者 + 阅读量
  .item-desc-bottom {
    margin-top: $uni-spacing-col-base;
    display: flex;
    color: $uni-text-color-grey;
    font-size: $uni-font-size-sm;
    .item-author {
      margin-right: $uni-spacing-row-lg;
    }
    .item-read-num {
      .read-num-icon {
        color: $uni-text-color-grey;
        margin-right: $uni-spacing-row-sm;
      }
    }
  }
}
</style>
```

components\search-result-item-theme-2\search-result-item-theme-2.vue

```vue
<template>
  <!-- 样式2 ： 一张图片样式 -->
  <view class="search-result-item-box">
    <!-- 标题 -->
    <rich-text :nodes="data.title" class="item-title line-clamp-2"></rich-text>
    <view class="item-info-box">
      <view class="item-desc-box">
        <rich-text :nodes="data.description" class="item-desc line-clamp-2"></rich-text>
        <view class="item-desc-bottom">
          <view class="item-author">{{ data.nickname }}</view>
          <view class="item-read-num">
            <uni-icons class="read-num-icon" color="#999999" type="compose" />
            <text>{{ data.updateTime | relativeTime }}</text>
          </view>
        </view>
      </view>
      <image class="item-img" :src="data.pic_list[0]" />
    </view>
  </view>
</template>

<script>
export default {
  props: {
    data: {
      type: Object,
      required: true
    }
  },
  data: () => ({})
};
</script>

<style lang="scss" scoped>
.search-result-item-box {
  margin-bottom: $uni-spacing-col-big;
  // 标题
  .item-title {
    font-size: $uni-font-size-base;
    font-weight: bold;
    color: $uni-text-color-title;
  }

  .item-info-box {
    display: flex;
    margin-top: $uni-spacing-col-base;
    .item-desc-box {
      width: 65%;
      font-size: $uni-font-size-base;
      color: $uni-color-subtitle;
      // 底部作者 + 阅读量
      .item-desc-bottom {
        margin-top: $uni-spacing-col-base;
        display: flex;
        color: $uni-text-color-grey;
        font-size: $uni-font-size-sm;
        .item-author {
          margin-right: $uni-spacing-row-lg;
        }
        .item-read-num {
          .read-num-icon {
            color: $uni-text-color-grey;
            margin-right: $uni-spacing-row-sm;
          }
        }
      }
    }

    .item-img {
      width: 33%;
      height: 70px;
    }
  }
}
</style>
```

components\search-result-item-theme-3\search-result-item-theme-3.vue

```vue
<template>
  <!-- 样式3 ： 三张图片样式 -->
  <view class="search-result-item-box">
    <!-- 标题 -->
    <rich-text :nodes="data.title" class="item-title line-clamp-2"></rich-text>
    <!-- 内容区 - 样式 3 -->
    <view class="item-info-img-box">
      <image v-for="item in data.pic_list" :key="item" class="item-img" :src="item" />
    </view>
    <!-- 底部 -->
    <view class="item-desc-bottom">
      <view class="item-author">{{ data.nickname }}</view>
      <view class="item-read-num">
        <uni-icons class="read-num-icon" color="#999999" type="compose" />
        <text>{{ data.updateTime | relativeTime }}</text>
      </view>
    </view>
  </view>
</template>

<script>
export default {
  props: {
    data: {
      type: Object,
      required: true
    }
  },
  data: () => ({}),
  created() {
    if (this.data.pic_list.length > 3) {
      this.data.pic_list = this.data.pic_list.splice(0, 2);
    }
  }
};
</script>

<style lang="scss" scoped>
.search-result-item-box {
  margin-bottom: $uni-spacing-col-big;
  // 标题
  .item-title {
    font-size: $uni-font-size-base;
    font-weight: bold;
    color: $uni-text-color-title;
  }

  .item-info-img-box {
    display: flex;
    // 图片
    .item-img {
      width: 33%;
      height: 70px;
      box-sizing: border-box;
    }
    .item-img:nth-child(1n + 1) {
      margin-right: $uni-spacing-row-sm;
    }
  }

  // 底部作者 + 阅读量
  .item-desc-bottom {
    margin-top: $uni-spacing-col-base;
    display: flex;
    color: $uni-text-color-grey;
    font-size: $uni-font-size-sm;
    .item-author {
      margin-right: $uni-spacing-row-lg;
    }
    .item-read-num {
      .read-num-icon {
        color: $uni-text-color-grey;
        margin-right: $uni-spacing-row-sm;
      }
    }
  }
}
</style>
```

## 全局状态管理

store\modules\search.js

```js
const STORAGE_KEY = 'search-list';
const HISTORY_MAX = 10;

export default {
  // 独立命名空间
  namespaced: true,
  // 通过 state 声明数据
  state: () => ({
    // 优先从 storage 中读取
    searchData: uni.getStorageSync(STORAGE_KEY) || []
  }),
  // 更改 state 数据的唯一方式是：提交 mutations
  mutations: {
    /**
     * 保存数据到 storage
     */
    saveToStorage(state) {
      uni.setStorage({
        key: STORAGE_KEY,
        data: state.searchData
      });
    },
    /**
     * 添加数据
     */
    addSearchData(state, val) {
      if (!val) return;
      const index = state.searchData.findIndex((item) => item === val);
      if (index !== -1) {
        state.searchData.splice(index, 1);
      }
      // 判断是否超过了最大缓存数量
      if (state.searchData.length > HISTORY_MAX) {
        state.searchData.splice(HISTORY_MAX - 1, state.searchData.length - HISTORY_MAX - 1);
      }

      state.searchData.unshift(val);
      // 调用 saveToStorage
      this.commit('search/saveToStorage');
    },
    /**
     * 删除指定数据
     */
    removeSearchData(state, index) {
      state.searchData.splice(index, 1);
      // 调用 saveToStorage
      this.commit('search/saveToStorage');
    },
    /**
     * 删除所有数据
     */
    removeAllSearchData(state) {
      state.searchData = [];
      // 调用 saveToStorage
      this.commit('search/saveToStorage');
    }
  }
};
```

store\index.js

```js
// 1. 导入 Vue 和 Vuex
import Vue from 'vue';
// uniapp 已默认安装，不需要重新下载
import Vuex from 'vuex';
// 导入 search.js 暴露的对象
import search from './modules/search';
import user from './modules/user';
import video from './modules/video';

// 2. 安装 Vuex 插件
Vue.use(Vuex);
// 3. 创建 store 实例
const store = new Vuex.Store({
  modules: {
    search,
    user,
    video
  }
});
export default store;
```

store\modules\user.js

```js
import { login } from 'api/user';
const STORAGE_KEY = 'user-info';
const TOKEN_KEY = 'token';
export default {
  namespaced: true,
  state: () => {
    return {
      // 用户 token
      token: uni.getStorageSync(TOKEN_KEY) || '',
      // 用户信息
      userInfo: uni.getStorageSync(STORAGE_KEY) || {}
    };
  },
  mutations: {
    /**
     * 保存 token 到 vuex
     */
    setToken(state, token) {
      state.token = token;
      this.commit('user/saveToken');
    },
    /**
     * 保存 token 到 本地存储
     */
    saveToken(state) {
      uni.setStorage({
        key: TOKEN_KEY,
        data: state.token
      });
    },
    /**
     * 删除 token
     */
    removeToken(state) {
      state.token = '';
      this.commit('user/saveToken');
    },
    /**
     * 保存 用户信息 到 vuex
     */
    setUserInfo(state, userInfo) {
      state.userInfo = userInfo;
      this.commit('user/saveUserInfo');
    },
    /**
     * 保存 用户信息 到 本地存储
     */
    saveUserInfo(state) {
      uni.setStorage({
        key: STORAGE_KEY,
        data: state.userInfo
      });
    },
    /**
     * 删除用户信息
     */
    removeUserInfo(state) {
      state.userInfo = {};
      this.commit('user/saveUserInfo');
    }
  },
  actions: {
    /**
     * 完成登录
     */
    async login(context, userProfile) {
      // 用户数据
      const rawData = JSON.parse(userProfile.rawData);
      const { data: res } = await login({
        signature: userProfile.signature,
        iv: userProfile.iv,
        nickName: rawData.nickName,
        gender: rawData.gender,
        city: rawData.city,
        province: rawData.province,
        avatarUrl: rawData.avatarUrl
      });
      // 登录逻辑
      this.commit('user/setToken', res.token);
      this.commit('user/setUserInfo', JSON.parse(userProfile.rawData));
    },
    /**
     * 退出登录
     */
    logout(context) {
      this.commit('user/removeToken');
      this.commit('user/removeUserInfo');
    },
    /**
     * 进行登录判定
     */
    async isLogin(context) {
      if (context.state.token) return true;
      // 如果用户未登录，则引导用户进入登录页面
      const [error, res] = await uni.showModal({
        title: '登录之后才可以进行后续操作',
        content: '立即跳转到登录页面？（登录后回自动返回当前页面哦~~~）'
      });
      const { cancel, confirm } = res;
      if (confirm) {
        uni.navigateTo({
          url: '/subpkg/pages/login-page/login-page'
        });
      }
      return false;
    }
  }
};
```

store\modules\video.js

```js
export default {
  // 独立命名空间
  namespaced: true,
  // 通过 state 声明数据
  state: () => ({
    videoData: {}
  }),
  // 更改 state 数据的唯一方式是：提交 mutations
  mutations: {
    /**
     * 保存视频对象到 vuex
     */
    setVideoData(state, videoData) {
      state.videoData = videoData;
    }
  }
};
```

main.js

```js
import Vue from 'vue';
import App from './App';
// 导入 vuex 实例
import store from './store';
// 通用样式
import './styles/global.scss';
// 文章详情样式
import './styles/article-detail.scss';
// 导入过滤器
import * as filters from './filters';

// 注册过滤器
Object.keys(filters).forEach((key) => {
  Vue.filter(key, filters[key]);
});

Vue.config.productionTip = false;

App.mpType = 'app';

const app = new Vue({
  ...App,
  store // 挂载实例对象
});
app.$mount();
```



## 文章详情页请求

api\article.js

```js
import request from '../utils/request';

/**
 * 获取文章详情
 */
export function getArticleDetail(data) {
  return request({
    url: '/article/details',
    data
  });
}

/**
 * 获取文章评论列表
 */
export function getArticleCommentList(data) {
  return request({
    url: '/article/comment/list',
    data
  });
}
```

## 文章详情页

<img src="https://cdn.nlark.com/yuque/0/2022/png/1614731/1656511323156-c2da7060-be1f-466a-9f9b-d2dfab5ebf64.png?x-oss-process=image%2Fresize%2Cw_408%2Climit_0" alt="2022-06-29_215340.png" style="zoom:67%;" />

<img src="https://cdn.nlark.com/yuque/0/2022/png/1614731/1656511323125-769cfb19-3328-4138-aee5-bfb4cc6858fc.png?x-oss-process=image%2Fresize%2Cw_419%2Climit_0" alt="2022-06-29_215447.png" style="zoom:67%;" />

<img src="https://cdn.nlark.com/yuque/0/2022/png/1614731/1656511323842-02dedc49-65b5-4687-ade1-3f0cea2d1dcc.png" alt="2022-06-29_215534.png" style="zoom:67%;" />

<img src="https://cdn.nlark.com/yuque/0/2022/png/1614731/1656511323882-76759562-2a59-4026-b15f-4ed87141d055.png?x-oss-process=image%2Fresize%2Cw_417%2Climit_0" alt="2022-06-29_215716.png" style="zoom:67%;" />

subpkg\pages\blog-detail\blog-detail.vue

```vue
<template>
  <!-- #ifndef H5 -->
  <page-meta root-font-size="52px">
    <!-- #endif -->
    <view class="detail-container">
      <!-- 文章内容区域 -->
      <block v-if="articleData">
        <!-- 标题 -->
        <view class="title">{{ articleData.articleTitle }}</view>
        <view class="detail-info">
          <view class="detail-left">
            <view class="avatar-box">
              <!-- 头像 -->
              <image class="avatar" :src="articleData.avatar" />
            </view>
            <view class="author-box">
              <!-- 作者 -->
              <text class="author">{{ articleData.nickName }}</text>
              <!-- 发布时间 -->
              <text class="release-time">{{ articleData.date }}</text>
            </view>
          </view>
          <view class="detail-right">
            <!-- 关注按钮 -->
            <button
              class="follow"
              size="mini"
              :type="articleData.isFollow ? 'primary' : 'default'"
              :loading="isFollowLoading"
              @click="onFollowClick"
            >
              {{ articleData.isFollow ? '已关注' : '关注' }}
            </button>
          </view>
        </view>
        <!-- 文章内容 -->
        <mp-html
          class="markdown_views"
          :content="addClassFromHTML(articleData.content)"
          scroll-table
        />
        <!-- 评论列表 -->
        <view class="comment-box">
          <article-comment-list
            ref="mescrollItem"
            :articleId="articleId"
            :isShowAllComment="isShowAllComment"
            @moreClick="isShowAllComment = true"
          />
        </view>
        <!-- 底部功能区 -->
        <article-operate
          :articleData="articleData"
          @commitClick="onCommit"
          @changePraise="onChangePraise"
          @changeCollect="onChangeCollect"
        />
        <!-- 输入评论的popup -->
        <uni-popup ref="popup" type="bottom" @change="onCommitPopupChange">
          <article-comment-commit
            v-if="isShowCommit"
            :articleId="articleId"
            @success="onSendSuccess"
          />
        </uni-popup>
      </block>
    </view>
    <!-- #ifndef H5 -->
  </page-meta>
  <!-- #endif -->
</template>

<script>
// 导入组件
import mpHtml from '@/uni_modules/mp-html/components/mp-html/mp-html';
// 当mescroll-body写在子组件时,父页面需引入mescroll-comp.js的mixins
import MescrollCompMixin from '@/uni_modules/mescroll-uni/components/mescroll-uni/mixins/mescroll-comp.js';
import { getArticleDetail } from 'api/article';
import { mapActions } from 'vuex';
import { userFollow } from 'api/user';
export default {
  mixins: [MescrollCompMixin], // 使用mixin
  // 注册组件
  components: {
    mpHtml
  },
  data() {
    return {
      // 作者名
      author: '',
      // 文章 ID
      articleId: '',
      // 文章详情数据
      articleData: null,
      // 是否展示全部评论
      isShowAllComment: false,
      // 关注用户的 loading
      isFollowLoading: false,
      // popup 的显示状态
      isShowCommit: false
    };
  },
  onLoad(options) {
    this.author = options.author;
    this.articleId = options.articleId;
    this.loadArticleDetail();
  },
  methods: {
    ...mapActions('user', ['isLogin']),
    /**
     * 为所有的 DOM 增加类名
     */
    addClassFromHTML(info) {
      return info
        .replace(/<p>/gi, '<p class="p-cls">')
        .replace(/<a>/gi, '<a class="a-cls">')
        .replace(/<h1>/gi, '<h1 class="h1-cls">')
        .replace(/<h2>/gi, '<h2 class="h2-cls">')
        .replace(/<h3>/gi, '<h3 class="h3-cls">')
        .replace(/<h4>/gi, '<h4 class="h4-cls">')
        .replace(/<h5>/gi, '<h5 class="h5-cls">')
        .replace(/<h6>/gi, '<h6 class="h6-cls">')
        .replace(/<ul>/gi, '<ul class="ul-cls">')
        .replace(/<li>/gi, '<li class="li-cls">')
        .replace(/<ol>/gi, '<ol class="ol-cls">')
        .replace(/<td>/gi, '<td class="td-cls">')
        .replace(/<th>/gi, '<th class="th-cls">')
        .replace(/<tr>/gi, '<tr class="tr-cls">')
        .replace(/<dl>/gi, '<dl class="dl-cls">')
        .replace(/<dd>/gi, '<dd class="dd-cls">')
        .replace(/<hr>/gi, '<hr class="hr-cls">')
        .replace(/<pre>/gi, '<pre class="pre-cls">')
        .replace(/<strong>/gi, '<strong class="strong-cls">')
        .replace(/<input>/gi, '<input class="input-cls">')
        .replace(/<table>/gi, '<table class="table-cls">')
        .replace(/<details>/gi, '<details class="details-cls">')
        .replace(/<code>/gi, '<code class="code-cls">')
        .replace(/<kbd>/gi, '<kbd class="kbd-cls">')
        .replace(/<summary>/gi, '<summary class="summary-cls">')
        .replace(/<blockquote>/gi, '<blockquote class="blockquote-cls">')
        .replace(/<img/gi, '<img class="img-cls"');
    },
    /**
     * 获取文章详情数据
     */
    async loadArticleDetail() {
      // 展示加载框
      uni.showLoading({
        title: '加载中'
      });
      const { data: res } = await getArticleDetail({
        author: this.author,
        articleId: this.articleId
      });
      this.articleData = res.data;
    },
    /**
     *  关注按钮点击事件
     */
    async onFollowClick() {
      // 进行登录判定
      const isLogin = await this.isLogin();
      if (!isLogin) {
        return;
      }
      // 关注用户
      // 开启 button 的 loading
      this.isFollowLoading = true;
      const { data: res } = await userFollow({
        author: this.author,
        isFollow: !this.articleData.isFollow
      });
      // 修改用户数据
      this.articleData.isFollow = !this.articleData.isFollow;
      // 关闭 button 的 loading
      this.isFollowLoading = false;
    },
    /**
     * 发布评论点击事件
     */
    onCommit() {
      // 通过组件定义的ref调用uni-popup方法
      this.$refs.popup.open();
    },
    /**
     * 发布评论的 popup 切换事件
     */
    onCommitPopupChange(e) {
      // 修改对应的标记，当 popup 关闭时，为了动画平顺，进行延迟处理
      if (e.show) {
        this.isShowCommit = e.show;
      } else {
        setTimeout(() => {
          this.isShowCommit = e.show;
        }, 200);
      }
    },
    /**
     * 发表评论成功
     */
    onSendSuccess(data) {
      // 关闭弹出层
      this.$refs.popup.close();
      this.isShowCommit = false;
      // 显示评论数据
      this.$refs.mescrollItem.addCommentList(data);
    },
    /**
     * 点赞处理回调
     */
    onChangePraise(isPraise) {
      this.articleData.isPraise = isPraise;
    },
    /**
     * 收藏处理回调
     */
    onChangeCollect(isCollect) {
      this.articleData.isCollect = isCollect;
    }
  }
};
</script>

<style lang="scss">
// @import '~@/styles/article-detail.scss';
.detail-container {
  padding: $uni-spacing-col-base $uni-spacing-row-base;
  padding-bottom: 108px;
  .title {
    font-size: $uni-font-size-title;
    color: $uni-text-color-title;
    font-weight: bold;
  }
  .detail-info {
    padding: $uni-spacing-col-base 0;
    display: flex;
    justify-content: space-between;

    .detail-left {
      display: flex;
      .author-box {
        margin-left: $uni-spacing-row-base;
        display: flex;
        flex-direction: column;
        .author {
          font-size: $uni-font-size-base;
          font-weight: bolder;
          color: $uni-color-title;
        }
        .release-time {
          font-size: $uni-font-size-sm;
          color: $uni-text-color-grey;
        }
      }
    }

    .detail-right {
      display: flex;
      align-items: center;
    }
  }
}
</style>
```

components\article-comment-list\article-comment-list.vue

```vue
<template>
  <!-- 精简评论 -->
  <view class="comment-limt-container" v-if="!isShowAllComment">
    <view class="comment-title">精简评论</view>
    <block v-for="(item, index) in commentList.slice(0, 2)" :key="index">
      <!-- item 项组件 -->
      <article-comment-item :data="item.info" />
    </block>
    <view class="show-more" @click="$emit('moreClick')">查看更多评论</view>
  </view>
  <!-- 全部评论 -->
  <view class="comment-all-container" v-else>
    <!-- 当mescroll-body写在子组件时,父页面需引入mescroll-comp.js的mixins -->
    <mescroll-body
      ref="mescrollRef"
      @init="mescrollInit"
      @up="upCallback"
      :down="{
        use: false
      }"
      :up="{
        textNoMore: '-- 我也是有底线的！ --'
      }"
    >
      <view class="comment-title">全部评论</view>
      <view class="list">
        <block v-for="(item, index) in commentList" :key="index">
          <article-comment-item :data="item.info" />
        </block>
      </view>
    </mescroll-body>
  </view>
</template>

<script>
import { getArticleCommentList } from 'api/article';
import MescrollMixin from '@/uni_modules/mescroll-uni/components/mescroll-uni/mescroll-mixins.js';
export default {
  name: 'article-comment-list',
  mixins: [MescrollMixin], // 使用mixin
  props: {
    // 文章 ID
    articleId: {
      type: String,
      required: true
    },
    // 是否展示全部评论
    isShowAllComment: {
      type: Boolean,
      default: false
    }
  },
  data() {
    return {
      // 当前页数
      page: 1,
      // 每页评论数
      pageSize: 5,
      // 评论列表
      commentList: [],
      // 评论总数
      commentListTotal: 0,
      // 是否为 init
      isInit: true,
      // 组件实例
      mescroll: null
    };
  },
  created() {
    this.loadCommentList();
  },
  methods: {
    /**
     * 获取评论列表
     */
    async loadCommentList() {
      const { data: res } = await getArticleCommentList({
        articleId: this.articleId,
        page: this.page,
        size: this.pageSize
      });
      // 获取总数量
      this.commentListTotal = res.count;
      // 判断是否为第一页数据
      if (this.page === 1) {
        this.commentList = res.list;
      } else {
        this.commentList = [...this.commentList, ...res.list];
      }
    },
    /**
     * 首次加载
     */
    async mescrollInit() {
      await this.loadCommentList();
      this.isInit = false;
      // 结束 上拉加载 && 下拉刷新
      this.getMescroll().endSuccess();
      // 判断数据是否加载完成
      this.mescroll.endBySize(this.commentList.length, this.commentListTotal);
    },
    /**
     * 上拉加载更多
     */
    async upCallback() {
      if (this.isInit) return;
      this.page += 1;
      await this.loadCommentList();
      // 结束 上拉加载 && 下拉刷新
      this.getMescroll().endSuccess();
      // 判断数据是否加载完成
      this.mescroll.endBySize(this.commentList.length, this.commentListTotal);
    },
    /**
     * 返回 mescroll实例对象
     */
    getMescroll() {
      if (!this.mescroll) {
        this.mescroll = this.$refs.mescrollRef.mescroll;
      }
      return this.mescroll;
    },
    /**
     * 为 comment 增加一个评论
     */
    addCommentList(data) {
      this.commentList.unshift(data);
    }
  }
};
</script>

<style lang="scss" scoped>
.comment-title {
  font-weight: bold;
  color: $uni-text-color-title;
  font-size: $uni-font-size-lg;
  margin: $uni-spacing-col-lg 0;
}
.comment-limt-container {
  .show-more {
    margin: $uni-spacing-col-lg;
    text-align: center;
    color: $uni-text-color-more;
    font-size: $uni-font-size-base;
  }
}
</style>
```

components\article-comment-item\article-comment-item.vue

```vue
<template>
  <view class="comment-item-container">
    <!-- 头像 -->
    <view class="avatar-box">
      <image class="avatar" :src="data.avatar" />
    </view>
    <!-- 评论信息 -->
    <view class="info-box">
      <!-- 评论人 -->
      <text class="comment-user">{{ data.nickName || data.uname }}</text>
      <!-- 评论内容 -->
      <text class="comment-info">{{ data.content }}</text>
      <!-- 评论时间 -->
      <text class="comment-time">{{ data.postTime | relativeTime }}</text>
    </view>
  </view>
</template>

<script>
export default {
  name: 'article-comment-item',
  props: {
    data: {
      type: Object,
      required: true
    }
  },
  data() {
    return {};
  }
};
</script>

<style lang="scss" scoped>
.comment-item-container {
  padding: $uni-spacing-col-lg 0;
  display: flex;
  .info-box {
    margin-left: $uni-spacing-row-sm;
    display: flex;
    flex-direction: column;
    .comment-user {
      font-size: $uni-font-size-sm;
      font-weight: bolder;
      color: $uni-text-color;
    }
    .comment-info {
      margin-top: $uni-spacing-col-sm;
      font-size: $uni-font-size-base;
      color: $uni-text-color;
    }
    .comment-time {
      margin-top: $uni-spacing-col-sm;
      font-size: $uni-font-size-sm;
      color: $uni-text-color-grey;
    }
  }
}
</style>
```

components\article-operate\article-operate.vue

```vue
<template>
  <view class="operate-container">
    <!-- 输入框 -->
    <view class="comment-box" @click="onCommitClick">
      <my-search
        :placeholderText="placeholder"
        :config="{
          height: 28,
          backgroundColor: '#eeedf4',
          icon: '/static/images/input-icon.png',
          textColor: '#a6a5ab',
          border: 'none'
        }"
      ></my-search>
    </view>
    <!-- 点赞 -->
    <view class="options-box">
      <article-praise :articleData="articleData" @changePraise="$emit('changePraise', $event)" />
    </view>
    <!-- 收藏 -->
    <view class="options-box">
      <article-collect :articleData="articleData" @changeCollect="$emit('changeCollect', $event)" />
    </view>
  </view>
</template>

<script>
import { mapActions } from 'vuex';

export default {
  name: 'article-operate',
  props: {
    articleData: {
      type: Object,
      required: true
    },
    placeholder: {
      type: String,
      default: '评论一句，前排打call...'
    }
  },
  data() {
    return {};
  },
  methods: {
    ...mapActions('user', ['isLogin']),
    /**
     * my-search 的点击事件
     */
    async onCommitClick() {
      // 进行登录判定，登录之后允许发布评论
      if (!(await this.isLogin())) {
        return;
      }
      this.$emit('commitClick');
    }
  }
};
</script>

<style lang="scss" scoped>
.operate-container {
  position: fixed;
  left: 0;
  right: 0;
  bottom: 0;
  background-color: $uni-bg-color;
  padding: 4px 6px 32px 6px;
  display: flex;
  border-top: 1px solid $uni-bg-color-grey;
  align-items: center;
  .comment-box {
    flex-grow: 1;
  }

  .options-box {
    display: flex;
    flex-direction: column;
    align-items: center;
    flex-grow: 1;
    .img {
      width: $uni-img-size-base;
      height: $uni-img-size-base;
    }
    .txt {
      font-size: $uni-font-size-sm;
      color: $uni-text-color;
    }
  }
}
</style>
```

components\article-comment-commit\article-comment-commit.vue

```vue
<template>
  <view class="comment-container" :style="{ bottom: bottom + 'px' }">
    <uni-easyinput
      v-model="value"
      type="textarea"
      placeholder="说点什么..."
      :maxlength="50"
      :inputBorder="false"
    ></uni-easyinput>
    <button class="commit" type="primary" :disabled="!value" size="mini" @click="onBtnClick">
      发送
    </button>
  </view>
</template>

<script>
import { userArticleComment } from 'api/user';

export default {
  name: 'article-comment-commit',
  props: {
    articleId: {
      type: String,
      required: true
    }
  },
  data() {
    return {
      value: '',
      bottom: 0
    };
  },
  created() {
    // 检测软键盘的变化
    uni.onKeyboardHeightChange(({ height }) => {
      this.bottom = height;
    });
  },
  methods: {
    /**
     * 发送按钮点击事件
     */
    async onBtnClick() {
      // 展示加载框
      uni.showLoading({
        title: '加载中'
      });
      // 异步处理即可
      const { data: res } = await userArticleComment({
        articleId: this.articleId,
        content: this.value
      });
      uni.showToast({
        title: '发表成功',
        icon: 'success',
        mask: true
      });
      // 发表成功之后的回调
      this.$emit('success', res);
    }
  }
};
</script>

<style lang="scss" scoped>
.comment-container {
  background-color: $uni-bg-color;
  text-align: right;
  padding: $uni-spacing-row-base;
  position: relative;
}
</style>
```

components\article-praise\article-praise.vue

```vue
<template>
  <view class="praise-box" @click="onClick">
    <image class="img" :src="getIsPraise" />
    <text class="txt">点赞</text>
  </view>
</template>

<script>
import { mapActions } from 'vuex';
import { userPraise } from 'api/user';
export default {
  name: 'article-praise',
  props: {
    /**
     * 数据源
     */
    articleData: {
      type: Object,
      required: true
    }
  },
  computed: {
    getIsPraise() {
      return this.articleData && this.articleData.isPraise
        ? '/static/images/praise.png'
        : '/static/images/un-praise.png';
    }
  },
  methods: {
    ...mapActions('user', ['isLogin']),
    async onClick() {
      // 进行登录判定，登录之后允许发布评论
      if (!(await this.isLogin())) {
        return;
      }
      // 展示加载框
      uni.showLoading({
        title: '加载中'
      });
      await userPraise({
        articleId: this.articleData.articleId,
        isPraise: !this.articleData.isPraise
      });
      // 关闭加载
      uni.hideLoading();
      // 更新数据
      this.$emit('changePraise', !this.articleData.isPraise);
    }
  }
};
</script>

<style lang="scss" scoped>
.praise-box {
  display: flex;
  flex-direction: column;
  align-items: center;

  .img {
    width: $uni-img-size-base;
    height: $uni-img-size-base;
    color: $uni-text-color;
  }

  .txt {
    font-size: $uni-font-size-sm;
    color: $uni-text-color;
  }
}
</style>
```

components\article-collect\article-collect.vue

```vue
<template>
  <view class="collect-box" @click="onClick">
    <image class="img" :src="getIsCollect" />
    <text class="txt">收藏</text>
  </view>
</template>

<script>
import { mapActions } from 'vuex';
import { userCollect } from 'api/user';
export default {
  name: 'article-collect',
  props: {
    /**
     * 数据源
     */
    articleData: {
      type: Object,
      required: true
    }
  },
  computed: {
    getIsCollect() {
      return this.articleData && this.articleData.isCollect
        ? '/static/images/collect.png'
        : '/static/images/un-collect.png';
    }
  },
  methods: {
    ...mapActions('user', ['isLogin']),
    async onClick() {
      // 进行登录判定，登录之后允许发布评论
      if (!(await this.isLogin())) {
        return;
      }
      // 展示加载框
      uni.showLoading({
        title: '加载中'
      });
      await userCollect({
        articleId: this.articleData.articleId,
        isCollect: !this.articleData.isCollect
      });
      // 关闭加载
      uni.hideLoading();
      // 更新数据
      this.$emit('changeCollect', !this.articleData.isCollect);
    }
  }
};
</script>

<style lang="scss" scoped>
.collect-box {
  display: flex;
  flex-direction: column;
  align-items: center;

  .img {
    width: $uni-img-size-base;
    height: $uni-img-size-base;
  }

  .txt {
    font-size: $uni-font-size-sm;
    color: $uni-text-color;
  }
}
</style>
```

## 用户登录页请求

api\user.js

```js
import request from '../utils/request';

/**
 * 用户登录
 */
export function login(data) {
  return request({
    url: '/sys/login',
    method: 'POST',
    data
  });
}

/**
 * 关注用户
 */
export function userFollow(data) {
  return request({
    url: '/user/follow',
    data
  });
}

/**
 * 发表评论
 */
export function userArticleComment(data) {
  return request({
    url: '/user/article/comment',
    method: 'POST',
    data
  });
}

/**
 * 用户点赞
 */
export function userPraise(data) {
  return request({
    url: '/user/praise',
    data
  });
}

/**
 * 用户收藏
 */
export function userCollect(data) {
  return request({
    url: '/user/collect',
    data
  });
}
```

## 用户登录页

<img src="https://cdn.nlark.com/yuque/0/2022/png/1614731/1656511324288-05539815-056a-4484-b63b-51c0075e00ed.png" alt="2022-06-29_215800.png" style="zoom:67%;" />

<img src="https://cdn.nlark.com/yuque/0/2022/png/1614731/1656511324483-592e4c8c-5477-45a7-b3c3-0f52090089f4.png" alt="2022-06-29_215826.png" style="zoom:67%;" />

pages\my\my.vue

```vue
<template>
  <my-login />
</template>

<script>
export default {
  name: 'login'
};
</script>
```

components\my-login\my-login.vue

```vue
<template>
  <view class="my-container">
    <!-- 用户未登录 -->
    <block v-if="!token">
      <image class="avatar avatar-img" src="/static/images/default-avatar.png" mode="scaleToFill" />
      <view class="login-desc">登录后可同步数据</view>
      <!-- #ifdef MP-WEIXIN -->
      <button class="login-btn" type="primary" @click="getUserInfo">微信用户一键登录</button>
      <!-- #endif -->
      <!-- #ifndef MP-WEIXIN -->
      <button class="login-btn" type="primary" @click="onAutoLogin">一键登录</button>
      <!-- #endif -->
    </block>
    <!-- 已登录 -->
    <block v-else>
      <image class="avatar avatar-img" :src="userInfo.avatarUrl" mode="scaleToFill" />
      <view class="login-desc">{{ userInfo.nickName }}</view>
      <button class="login-btn" type="default" @click="onLogoutClick">退出登录</button>
    </block>
  </view>
</template>

<script>
import { mapState, mapActions } from 'vuex';
export default {
  name: 'my-login',
  data() {
    return {};
  },
  computed: {
    ...mapState('user', ['token', 'userInfo'])
  },
  methods: {
    ...mapActions('user', ['login', 'logout']),
    /**
     * 获取用户信息
     */
    getUserInfo() {
      // 展示加载框
      uni.showLoading({
        title: '加载中'
      });
      uni.getUserProfile({
        desc: '登录后可同步数据',
        success: async (obj) => {
          // 调用 action ，请求登录接口
          await this.login(obj);
          // 登录成功之后，发送事件
          this.$emit('onLoginSuccess');
        },
        fail: () => {
          uni.showToast({
            title: '授权已取消',
            icon: 'error',
            mask: true
          });
        },
        complete: () => {
          // 隐藏loading
          uni.hideLoading();
        }
      });
    },
    /**
     * 一键登录
     */
    async onAutoLogin() {
      // 展示加载框
      uni.showLoading({
        title: '加载中'
      });
      await this.login({
        encryptedData: 'BmGEMqpGI5w',
        errMsg: 'getUserProfile:ok',
        iv: 'c+NbINO4CuEWCBYGG2FxWw==',
        rawData:
          '{"nickName":"小慕同学","gender":1,"language":"zh_CN","city":"","province":"","country":"China","avatarUrl":"https://m.imooc.com/static/wap/static/common/img/logo-small@2x.png"}',
        signature: '449a10f11998daf680fe546a5176e6e2973516ce',
        userInfo: { nickName: '小慕同学', gender: 1, language: 'zh_CN', city: '', province: '' }
      });
      this.$emit('onLoginSuccess');
      // 隐藏loading
      uni.hideLoading();
    },
    /**
     * 退出登录
     */
    onLogoutClick() {
      uni.showModal({
        title: '提示',
        content: '退出登录将无法同步数据哦~',
        success: ({ confirm, cancel }) => {
          if (confirm) {
            this.logout();
          }
        }
      });
    }
  }
};
</script>

<style lang="scss" scoped>
.my-container {
  display: flex;
  flex-direction: column;
  align-items: center;
  padding-top: 25%;
  .avatar-img {
    width: 78px;
    height: 78px;
  }
  .login-desc {
    color: $uni-text-color-grey;
    font-size: $uni-font-size-base;
    margin-top: $uni-spacing-col-big;
  }
  .login-btn {
    margin-top: $uni-spacing-col-big;
    width: 85%;
  }
}
</style>
```

subpkg\pages\login-page\login-page.vue

```vue
<template>
  <my-login @onLoginSuccess="onLoginSuccess" />
</template>

<script>
export default {
  data() {
    return {};
  },
  methods: {
    /**
     * 登录成功的事件回调
     */
    onLoginSuccess() {
      console.log('onLoginSuccess');
      uni.navigateBack({ delta: 1 });
    }
  }
};
</script>

<style lang="scss"></style>
```



## 热播页请求

api\video.js

```js
import request from '../utils/request';

/**
 * 热播视频列表
 */
export function getHotVideoList(data) {
  return request({
    url: '/video/list',
    data
  });
}

/**
 * 获取视频弹幕列表
 */
export function getVideoDanmuList(data) {
  return request({
    url: '/video/danmu',
    data
  });
}
```

## 热播页

<img src="https://cdn.nlark.com/yuque/0/2022/png/1614731/1656511325519-c7aa37b8-d46a-492a-ba67-60284edb4545.png?x-oss-process=image%2Fresize%2Cw_402%2Climit_0" alt="2022-06-29_220051.png" style="zoom:67%;" />

<img src="https://cdn.nlark.com/yuque/0/2022/png/1614731/1656511324868-8feaf611-3448-4093-98a8-1569cf1e5f24.png?x-oss-process=image%2Fresize%2Cw_413%2Climit_0" alt="2022-06-29_215915.png" style="zoom:67%;" />

<img src="https://cdn.nlark.com/yuque/0/2022/png/1614731/1656511325089-6b1a2772-7c41-474f-8cbb-9763f7bb0f5d.png?x-oss-process=image%2Fresize%2Cw_393%2Climit_0" alt="2022-06-29_215952.png" style="zoom:67%;" />

pages\hot-video\hot-video.vue

```vue
<template>
  <view class="hot-video-container">
    <!-- 1. 导入 mescroll-body -->
    <mescroll-body ref="mescrollRef" @init="mescrollInit" @down="downCallback" @up="upCallback">
      <block v-for="(item, index) in videoList" :key="index">
        <hot-video-item :data="item" @click="onItemClick" />
      </block>
    </mescroll-body>
  </view>
</template>

<script>
// 2. 导入 mixin
import MescrollMixin from '@/uni_modules/mescroll-uni/components/mescroll-uni/mescroll-mixins.js';
import { getHotVideoList } from 'api/video';
import { mapMutations } from 'vuex';
export default {
  // 3. 注册 mixin
  mixins: [MescrollMixin],
  data() {
    return {
      // 数据源
      videoList: [],
      size: 10,
      page: 1,
      // 是否为 init
      isInit: true,
      // 实例
      mescroll: null
    };
  },
  created() {
    // this.loadHotVideoList();
  },
  mounted() {
    this.mescroll = this.$refs.mescrollRef.mescroll;
  },
  methods: {
    ...mapMutations('video', ['setVideoData']),
    /**
     * 获取列表数据
     */
    async loadHotVideoList() {
      const { data: res } = await getHotVideoList({ page: this.page, size: this.size });
      // 判断是否为第一页数据
      if (this.page === 1) {
        this.videoList = res.list;
      } else {
        this.videoList = [...this.videoList, ...res.list];
      }
    },
    // 4. 实现回调方法
    /**
     * List 组件的首次加载
     */
    async mescrollInit() {
      await this.loadHotVideoList();
      this.isInit = false;
      // 结束 上拉加载 && 下拉刷新
      this.mescroll.endSuccess();
    },
    /**
     * 下拉刷新的回调
     */
    async downCallback() {
      if (this.isInit) return;
      this.page = 1;
      await this.loadHotVideoList();
      // 结束 上拉加载 && 下拉刷新
      this.mescroll.endSuccess();
    },
    /**
     * 上拉加载的回调
     */
    async upCallback() {
      if (this.isInit) return;
      this.page += 1;
      await this.loadHotVideoList();
      // 结束 上拉加载 && 下拉刷新
      this.mescroll.endSuccess();
    },
    /**
     * item 点击事件
     */
    onItemClick(data) {
      // 保存当前点击的 video 数据到 vuex
      this.setVideoData(data);
      // 进行页面跳转
      uni.navigateTo({
        url: `/subpkg/pages/video-detail/video-detail`
      });
    }
  }
};
</script>

<style lang="scss" scoped>
.hot-video-container {
  background-color: $uni-bg-color-grey;
}
</style>
```

components\hot-video-item\hot-video-item.vue

```vue
<template>
  <view class="hot-video-item-container">
    <view class="video-box">
      <video id="myVideo" class="video" :src="data.play_url" enable-danmu danmu-btn controls />
    </view>
    <view @click="$emit('click', data)">
      <hot-video-info :data="data" />
    </view>
  </view>
</template>

<script>
export default {
  props: {
    data: {
      type: Object,
      required: true
    }
  },
  data() {
    return {};
  }
};
</script>

<style lang="scss" scoped>
.hot-video-item-container {
  margin-bottom: $uni-spacing-col-lg;
  position: relative;
  .video {
    width: 100%;
    height: 230px;
  }
}
</style>
```

components\hot-video-info\hot-video-info.vue

```vue
<template>
  <view class="hot-video-info-container">
    <view class="video-title"> {{ data.title }} </view>
    <view class="video-info">
      <view class="author-box">
        <image class="avatar" :src="data.poster_small" />
        <text class="author-txt">{{ data.source_name }}</text>
      </view>
      <view class="barrage-box">
        <uni-icons class="barrage-icon" type="videocam" />
        <text class="barrage-num">{{ data.fmplaycnt }}</text>
      </view>
    </view>
  </view>
</template>

<script>
export default {
  name: 'hot-video-info',
  props: {
    data: {
      type: Object,
      required: true
    }
  },
  data() {
    return {};
  }
};
</script>

<style lang="scss">
.hot-video-info-container {
  .video-title {
    position: absolute;
    top: $uni-spacing-col-big;
    left: $uni-spacing-row-lg;
    color: $uni-text-color-inverse;
    font-size: $uni-font-size-lg;
  }
  .video-info {
    display: flex;
    justify-content: space-between;
    background-color: $uni-bg-color;
    padding: $uni-spacing-col-sm $uni-spacing-row-lg;
    .author-box {
      display: flex;
      align-items: center;
      .author-txt {
        margin-left: $uni-spacing-row-sm;
        font-size: $uni-font-size-base;
        color: $uni-text-color;
        font-weight: bold;
      }
    }
    .barrage-box {
      display: flex;
      align-items: center;
      .barrage-num {
        margin-left: $uni-spacing-row-sm;
        font-size: $uni-font-size-sm;
        color: $uni-text-color;
      }
    }
  }
}
</style>
```

subpkg\pages\video-detail\video-detail.vue

```vue
<template>
  <view class="video-detail-container">
    <view class="video-box">
      <video
        id="myVideo"
        class="video"
        :src="videoData.play_url"
        :danmu-list="danmuList"
        enable-danmu
        danmu-btn
        controls
      />

      <hot-video-info :data="videoData" />
    </view>
    <!-- 弹幕模块 -->
    <view class="danmu-box">
      <!-- 加载动画 -->
      <uni-load-more status="loading" v-if="isLoadingComment"></uni-load-more>
      <!-- 无弹幕 -->
      <empty-data v-else-if="commentList.length === 0"></empty-data>
      <!-- 弹幕列表 -->
      <view class="comment-container" v-else>
        <view class="all-comment-title">全部弹幕</view>
        <view class="list">
          <block v-for="(item, index) in commentList" :key="index">
            <article-comment-item :data="item" />
          </block>
        </view>
      </view>
    </view>
    <!-- 底部功能区 -->
    <article-operate
      :placeholder="'发个弹幕，开心一下'"
      :articleData="videoData"
      @commitClick="onCommit"
      @changePraise="onChangePraise"
      @changeCollect="onChangeCollect"
    />
    <!-- 输入弹幕的popup -->
    <uni-popup ref="popup" type="bottom" @change="onCommitPopupChange">
      <article-comment-commit
        v-if="isShowCommit"
        :articleId="videoData.id"
        @success="onSendBarrage"
      />
    </uni-popup>
  </view>
</template>

<script>
import { getRandomColor } from 'utils';
import { getVideoDanmuList } from 'api/video';
import { mapState, mapMutations } from 'vuex';
export default {
  data() {
    return {
      // 弹幕数据源
      danmuList: [],
      // 列表数据源
      commentList: [],
      // 输入框是否显示
      isShowCommit: false,
      // video 组件上下文
      videoContext: null,
      // 弹幕列表数据加载中
      isLoadingComment: true
    };
  },
  computed: {
    ...mapState('video', ['videoData'])
  },
  created() {
    this.loadVideoDanmuList();
  },
  onReady: function (res) {
    // 获取 video 组件上下文
    this.videoContext = uni.createVideoContext('myVideo');
  },
  methods: {
    ...mapMutations('video', ['setVideoData']),
    /**
     * 获取弹幕数据
     */
    async loadVideoDanmuList() {
      const { data: res } = await getVideoDanmuList({
        videoId: this.videoData.id
      });
      // 定义随机颜色
      res.list.forEach((item) => {
        item.color = getRandomColor();
      });
      this.danmuList = [...res.list];
      this.commentList = [...res.list];
      this.isLoadingComment = false;
    },
    /**
     * 发布弹幕点击事件
     */
    onCommit() {
      // 通过组件定义的ref调用uni-popup方法
      this.$refs.popup.open();
    },
    /**
     * 发布弹幕的 popup 切换事件
     */
    onCommitPopupChange(e) {
      // 修改对应的标记，当 popup 关闭时，为了动画平顺，进行延迟处理
      if (e.show) {
        this.isShowCommit = e.show;
      } else {
        setTimeout(() => {
          this.isShowCommit = e.show;
        }, 200);
      }
    },
    /**
     * 弹幕发布成功之后的回调
     */
    onSendBarrage(data) {
      // 发送弹幕
      this.videoContext.sendDanmu({
        text: data.info.content,
        color: getRandomColor()
      });
      // 添加弹幕到数据源
      this.commentList.unshift(data.info);
      // 关闭 pop
      this.$refs.popup.close();
      // 关闭标记
      this.isShowCommit = false;
      // 提示用户
      uni.showToast({
        title: '发表成功'
      });
    },
    /**
     * 点赞处理回调
     */
    onChangePraise(isPraise) {
      this.setVideoData({ ...this.videoData, isPraise });
    },
    /**
     * 收藏处理回调
     */
    onChangeCollect(isCollect) {
      this.setVideoData({ ...this.videoData, isCollect });
    }
  }
};
</script>

<style lang="scss" scoped>
.video-detail-container {
  .video-box {
    background-color: $uni-bg-color;
    position: sticky;
    top: 0;
    z-index: 9;
    .video {
      width: 100%;
      height: 230px;
    }
  }
  .danmu-box {
    border-top: $uni-spacing-col-sm solid $uni-bg-color-grey;
    margin-bottom: 36px;
    .comment-container {
      padding: $uni-spacing-col-lg $uni-spacing-row-lg;
      .all-comment-title {
        font-size: $uni-font-size-lg;
        font-weight: bold;
      }
    }
  }
}
</style>
```

utils\index.js

```js
/**
 * 返回随机色值
 */
export let getRandomColor = () => {
  const rgb = [];
  for (let i = 0; i < 3; ++i) {
    let color = Math.floor(Math.random() * 256).toString(16);
    color = color.length == 1 ? '0' + color : color;
    rgb.push(color);
  }
  return '#' + rgb.join('');
};
```

## 样式

styles\global.scss

```scss
/**
 * 这里是共用样式的定义位置
 */

.line-clamp-2 {
  overflow: hidden;
  text-overflow: ellipsis;
  display: -webkit-box;
  -webkit-box-orient: vertical;
  -webkit-line-clamp: 2;
}

// 卡片视图
.card {
  border: 1px solid #f9f9f9;
  border-radius: 5px;
  margin: 12px;
  padding: 12px;
  box-shadow: 2px 2px 5px 1px rgba(143, 143, 143, 0.1);
}

// 单行不换行
.nowrap {
  white-space: nowrap;
  text-overflow: ellipsis;
  overflow: hidden;
}

// 头像
.avatar {
  width: 32px;
  height: 32px;
  border-radius: 50%;
  border: 2px solid #e5e5e5;
}
```

styles\article-detail.scss

```scss
html {
  font-size: 52px;
}
body {
  font-size: 16px;
}
.markdown_views {
  font-family: -apple-system, SF UI Text, Arial, PingFang SC, Hiragino Sans GB, Microsoft YaHei,
    WenQuanYi Micro Hei, sans-serif, SimHei, SimSun;
  font-size: 16px;
  width: 710rpx;
  overflow-x: hidden;
}
.markdown_views .p-cls {
  font-size: 0.32rem;
  color: #4f4f4f;
  font-weight: normal;
  line-height: 0.52rem;
  margin: 0 0 0.32rem 0;
}
.markdown_views .strong-cls {
  font-weight: bold;
}
.markdown_views i,
cite,
em,
var,
address,
dfn {
  font-style: italic;
}
.markdown_views * {
  -webkit-box-sizing: border-box;
  box-sizing: border-box;
}
#content_views.night .h1-cls,
#content_views.night .h2-cls,
#content_views.night .h3-cls,
#content_views.night .h4-cls,
#content_views.night .h5-cls,
#content_views.night .h6-cls {
  color: #4f4f4f;
  margin-top: 0 !important;
  font-weight: bold;
}
.markdown_views .ul-cls,
.markdown_views .ol-cls {
  margin: 0 0 0.48rem 0;
  padding: 0;
}
.markdown_views .ul-cls .ol-cls {
  margin: 0 0 0.48rem 0.64rem;
}
.markdown_views .ul-cls .li-cls {
  list-style-type: disc;
  margin: 0.16rem 0 0 0.64rem;
}
.markdown_views .ol-cls .li-cls {
  list-style-type: decimal;
  margin-left: 0.8rem;
  margin-top: 0.16rem;
}
.markdown_views .img-cls {
  max-width: 100%;
  vertical-align: baseline;
}
html .htmledit_views .h1-cls,
html .markdown_views .h1-cls {
  font-size: 0.4rem;
  line-height: 0.6rem;
}
html .htmledit_views .h2-cls,
html .markdown_views .h2-cls {
  font-size: 0.38rem;
  line-height: 0.6rem;
}
html .htmledit_views .h3-cls,
html .markdown_views .h3-cls {
  font-size: 0.36rem;
  line-height: 0.6rem;
}
html .htmledit_views .h4-cls,
html .markdown_views .h4-cls {
  font-size: 0.34rem;
  line-height: 0.54rem;
}
html .htmledit_views .h5-cls,
html .markdown_views .h5-cls {
  font-size: 0.32rem;
  line-height: 0.54rem;
}
html .htmledit_views .h6-cls,
html .markdown_views .h6-cls {
  font-size: 0.3rem;
  line-height: 0.54rem;
}
.markdown_views .h1-cls .code-cls {
  font-size: 0.56rem;
}
.markdown_views .h2-cls .code-cls {
  font-size: 0.48rem;
}
.markdown_views .h3-cls .code-cls {
  font-size: 0.44rem;
}
.markdown_views .h4-cls .code-cls {
  font-size: 0.4rem;
}
.markdown_views .h5-cls .code-cls {
  font-size: 0.36rem;
}
.markdown_views .h6-cls .code-cls {
  font-size: 0.32rem;
}
.markdown_views .blockquote-cls {
  display: block;
  padding: 0.32rem;
  margin: 0 0 0.48rem 0;
  border-left: 0.16rem solid #dddfe4;
  background: #eef0f4;
  overflow: auto;
  overflow-scrolling: touch;
  word-wrap: break-word;
}
.markdown_views .blockquote-cls .ul-cls,
.markdown_views .blockquote-cls .ol-cls {
  margin-bottom: 0;
  padding: 0;
  font-size: 0.28rem;
  line-height: 0.44rem;
}
.markdown_views .blockquote-cls .ul-cls .li-cls {
  margin-bottom: 0;
}
.markdown_views .blockquote-cls .ol-cls .li-cls {
  margin-bottom: 0;
}
.markdown_views .blockquote-cls p {
  font-size: 0.28rem;
  line-height: 0.44rem;
  color: #999;
  font-weight: normal;
  margin-bottom: 0;
}
.markdown_views .hr-cls {
  margin: 0.48rem 0;
  border: none;
  border-bottom: solid #ddd 0.02rem;
}
.markdown_views tbody {
  border: 0;
}
.markdown_views .table-cls .tr-cls {
  border: 0;
  border-top: 0.02rem solid #ddd;
  background-color: #fff;
}
.table-box {
  max-width: 100%;
  overflow-x: auto;
}
.markdown_views .table-cls {
  border-collapse: collapse;
  display: table;
  width: 100%;
  text-align: center;
  margin-bottom: 0.48rem;
}
.markdown_views tbody {
  border: 0;
}
.markdown_views .table-cls .tr-cls:nth-child(2n) {
  background-color: #f7f7f7;
}
.markdown_views .table-cls .tr-cls .th-cls,
.markdown_views .table-cls .tr-cls .td-cls {
  font-size: 0.24rem !important;
  color: #4f4f4f;
  line-height: 0.44rem;
  border: 0.02rem solid #ddd;
  padding: 0.16rem 0.16rem;
  text-align: left;
  word-break: normal;
  vertical-align: middle;
}
.markdown_views .table-cls .tr-cls .th-cls .code-cls,
.markdown_views .table-cls .tr-cls .td-cls .code-cls {
  white-space: normal;
  word-wrap: break-word;
}
.markdown_views .table-cls .tr-cls .th-cls {
  font-weight: bold;
  background-color: #eff3f5;
}
.markdown_views .dl-cls {
  margin: 0.48rem;
}
.markdown_views .dl-cls .dt-cls {
  margin: 0.16rem;
  font-weight: bold;
}
.markdown_views .dl-cls .dt-cls .dd-cls {
  margin: 0.16rem;
}
.markdown_views abbr[title],
.markdown_views abbr[data-original-title] {
  cursor: help;
  border-bottom: 0.02rem dotted #999;
}
.markdown_views .initialism {
  font-size: 90%;
  text-transform: uppercase;
}
.markdown_views .pre-cls {
  margin-bottom: 0.48rem;
  background-color: #282c34;
  color: #fff;
  width: 100%;
  overflow-x: scroll;
  padding: 4px 8px;
}
.markdown_views .a-cls {
  color: #4ea1db;
  text-decoration: none;
}
.markdown_views .a-cls:hover,
.markdown_views .a-cls:focus {
  color: #ca0c16;
}
.markdown_views .a-cls:visited {
  color: #6795b5;
}
.markdown_views .footnote {
  vertical-align: top;
  position: relative;
  top: -0.08rem;
  font-size: 0.24rem;
}
.markdown_views .footnotes .ol-cls .li-cls {
  font-size: 0.28rem;
  line-height: 0.44rem;
  margin: 0 0 0.16rem 0.48rem;
}
.markdown_views .sequence-diagram,
.markdown_views .flow-chart {
  text-align: center;
  margin-bottom: 0.48rem;
}
.markdown_views .sequence-diagram,
.markdown_views .flow-chart {
  text-align: center;
  margin-bottom: 0.48rem;
  font-size: 0.28rem !important;
}
.markdown_views .sequence-diagram [fill='#000'],
.markdown_views .flow-chart [fill='#000'],
.markdown_views .sequence-diagram [fill='#000000'],
.markdown_views .flow-chart [fill='#000000'],
.markdown_views .sequence-diagram [fill='black'],
.markdown_views .flow-chart [fill='black'] {
  fill: #4f4f4f;
}
.markdown_views .sequence-diagram [stroke='#000000'],
.markdown_views .flow-chart [stroke='#000000'] {
  stroke: #4f4f4f;
}
.markdown_views .MathJax_SVG_Display {
  text-align: center;
  margin: 0.48rem 0;
  font-size: 0.36rem;
  font-weight: 400;
  color: #4f4f4f;
  position: relative;
  text-indent: 0;
  max-width: none;
  max-height: none;
  min-width: 0;
  min-height: 0;
  width: 100%;
}
.markdown_views .toc {
  font-size: 0.32rem;
  line-height: 0.48rem;
  margin: 0 0 0.48rem 0;
  padding: 0;
}
.markdown_views .toc .ul-cls {
  margin: 0 0 0.16rem 0;
  padding: 0;
}
.markdown_views .toc .ul-cls .li-cls {
  list-style-type: none;
  margin: 0.16rem 0 0 0.48rem;
}
.markdown_views .pre-numbering .li-cls {
  padding: 0 0.16rem;
  list-style: none;
  margin: 0;
}
.markdown_views .dl-cls .dd-cls {
  margin: 0 0 0.16rem 0.8rem;
}
.markdown_views .kbd-cls {
  padding: 0.04rem 0.16rem;
  border: 0.02rem solid rgba(63, 63, 63, 0.25);
  -webkit-box-shadow: 0 0.02rem 0 rgba(63, 63, 63, 0.25);
  box-shadow: 0 0.02rem 0 rgba(63, 63, 63, 0.25);
  background-color: #fff;
  color: #333;
  border-radius: 0.08rem;
  display: inline-block;
  margin: 0 0.04rem;
  white-space: nowrap;
}
.markdown_views mark {
  color: #555963;
}
.markdown_views .katex-display,
.markdown_views .MathJax_Display {
  overflow-y: hidden;
  overflow-x: auto;
}
.markdown_views .pre-cls .code-cls {
  display: block;
  font-size: 14px;
  line-height: 22px;
  overflow-x: auto;
  padding: 0 !important;
  color: #000;
  white-space: pre;
  word-wrap: normal;
  word-break: normal !important;
  background-color: #f6f8fa;
  border-radius: 4px;
}
@media screen and (-webkit-min-device-pixel-ratio: 0) {
  .markdown_views .pre-cls .code-cls {
    min-width: 94%;
  }
}
.markdown_views .pre-cls.prettyprint,
.markdown_views .prettyprint {
  margin: 0 0 24px 0;
  padding: 8px 16px 6px 56px;
  background-color: #f6f8fa;
  border: none;
}
.prettyprint {
  position: relative;
  overflow-y: hidden;
  overflow-x: auto;
}
.markdown_views .prettyprint .pre-numbering {
  position: absolute;
  width: 48px;
  background-color: #eef0f4;
  top: 0;
  left: 0;
  margin: 0;
  padding: 10px 0 8px;
  list-style: none;
  text-align: right;
}
.markdown_views .pre-numbering .li-cls {
  padding: 0 8px;
  list-style: none;
  margin: 0;
}
.markdown_views.prism-atom-one-dark .pre-cls .code-cls {
  background-color: #282c34;
  color: #abb2bf;
}
.markdown_views.prism-atom-one-dark .pre-cls .code-cls.hljs * {
  color: #abb2bf;
}
.markdown_views.prism-atom-one-dark .prettyprint,
.markdown_views.prism-atom-one-dark .pre-cls.prettyprint {
  background-color: #282c34;
}
.markdown_views.prism-atom-one-dark .prettyprint .pre-numbering {
  background-color: #282c34;
}
.markdown_views.prism-atom-one-dark .pre-numbering .li-cls {
  color: #abb2bf !important;
  border-right: 1px solid #c5c5c5;
}
.markdown_views.prism-atom-one-light .pre-cls .code-cls {
  background-color: #fafafa;
}
.markdown_views.prism-atom-one-light .prettyprint,
.markdown_views.prism-atom-one-light .pre-cls.prettyprint {
  background-color: #fafafa;
}
.markdown_views.prism-atom-one-light .prettyprint .pre-numbering {
  background-color: #fafafa;
}
.markdown_views.prism-atom-one-light .pre-numbering .li-cls {
  color: #383a42 !important;
  border-right: 1px solid #c5c5c5;
}
.markdown_views.prism-tomorrow-night .pre-cls .code-cls {
  background-color: #1d1f21;
  color: #c5c8c6;
}
.markdown_views.prism-tomorrow-night .pre-cls .code-cls.hljs * {
  color: #c5c8c6;
}
.markdown_views.prism-tomorrow-night .prettyprint,
.markdown_views.prism-tomorrow-night .pre-cls.prettyprint {
  background-color: #1d1f21;
}
.markdown_views.prism-tomorrow-night .prettyprint .pre-numbering {
  background-color: #1d1f21;
}
.markdown_views.prism-tomorrow-night .pre-numbering .li-cls {
  color: #c5c8c6 !important;
  border-right: 1px solid #c5c5c5;
}
.markdown_views.prism-dracula .pre-cls .code-cls {
  background-color: #282a36;
  color: #f8f8f2;
}
.markdown_views.prism-dracula .pre-cls .code-cls.hljs * {
  color: #f8f8f2;
}
.markdown_views.prism-dracula .prettyprint,
.markdown_views.prism-dracula .pre-cls.prettyprint {
  background-color: #282a36;
}
.markdown_views.prism-dracula .prettyprint .pre-numbering {
  background-color: #282a36;
}
.markdown_views.prism-dracula .pre-numbering .li-cls {
  color: #f8f8f2 !important;
  border-right: 1px solid #c5c5c5;
}
.markdown_views.prism-github-gist .pre-cls .code-cls {
  background-color: #f3f4f5;
}
.markdown_views.prism-github-gist .prettyprint,
.markdown_views.prism-github-gist .pre-cls.prettyprint {
  background-color: #f3f4f5;
}
.markdown_views.prism-github-gist .prettyprint .pre-numbering {
  background-color: #f3f4f5;
}
.markdown_views.prism-github-gist .prettyprint .prism {
  background-color: #f3f4f5;
}
.markdown_views.prism-github-gist .pre-numbering .li-cls {
  color: #5e6687 !important;
  border-right: 1px solid #c5c5c5;
}
.markdown_views.prism-kimbie-light .pre-cls .code-cls {
  background-color: #fbebd4;
}
.markdown_views.prism-kimbie-light .prettyprint,
.markdown_views.prism-kimbie-light .pre-cls.prettyprint {
  background-color: #fbebd4;
}
.markdown_views.prism-kimbie-light .prettyprint .pre-numbering {
  background-color: #fbebd4;
}
.markdown_views.prism-kimbie-light .pre-numbering .li-cls {
  color: #84613d !important;
  border-right: 1px solid #c5c5c5;
}
.markdown_views.prism-tomorrow-night-eighties .pre-cls .code-cls {
  background-color: #2d2d2d;
  color: #ccc;
}
.markdown_views.prism-tomorrow-night-eighties .pre-cls .code-cls.hljs * {
  color: #ccc;
}
.markdown_views.prism-tomorrow-night-eighties .prettyprint,
.markdown_views.prism-tomorrow-night-eighties .pre-cls.prettyprint {
  background-color: #2d2d2d;
}
.markdown_views.prism-tomorrow-night-eighties .prettyprint .pre-numbering {
  background-color: #2d2d2d;
}
.markdown_views.prism-tomorrow-night-eighties .pre-numbering .li-cls {
  color: #ccc !important;
  border-right: 1px solid #c5c5c5;
}
.markdown_views.prism-atelier-sulphurpool-light .pre-cls .code-cls {
  background-color: #f5f7ff;
}
.markdown_views.prism-atelier-sulphurpool-light .prettyprint,
.markdown_views.prism-atelier-sulphurpool-light .pre-cls.prettyprint {
  background-color: #f5f7ff;
}
.markdown_views.prism-atelier-sulphurpool-light .prettyprint .pre-numbering {
  background-color: #f5f7ff;
}
.markdown_views.prism-atelier-sulphurpool-light .pre-numbering .li-cls {
  color: #5e6687 !important;
  border-right: 1px solid #c5c5c5;
}
html body.night-body,
.night {
  background-color: #2a2d33;
}
html body.night-body .img-cls.mathcode {
  -webkit-filter: invert(1);
  filter: invert(1);
}
.night .h1-cls,
.night .h2-cls,
.night .h3-cls,
.night .h4-cls,
.night .h5-cls,
.night .h6-cls,
.night p,
.night p span,
.night .li-cls,
.night .dl-cls,
.night .dt-cls,
.night .dd-cls,
.night .strong-cls,
.night .table-cls,
.night .table-cls .tr-cls,
.night .table-cls .tr-cls .th-cls,
.night .table-cls .tr-cls .td-cls,
.night .table-cls .tr-cls:nth-child(2n) {
  color: #dadfe8 !important;
}
.night .p-cls,
.night .strong-cls,
.night .h1-cls,
.night .h2-cls,
.night .h3-cls,
.night .h4-cls,
.night .h5-cls,
.night .h6-cls,
.night .ol-cls .li-cls,
.night .ul-cls .li-cls {
  background-color: #2a2d33 !important;
}
.night .code-cls .ol-cls,
.night .code-cls .ul-cls,
.night .prettyprint .li-cls {
  color: #888e99 !important;
}
.night .blockquote-cls .p-cls,
.night .blockquote-cls {
  background-color: #25272b !important;
}
.night .blockquote-cls {
  border-left: 4px solid #34373d;
}
.night .blockquote-cls .p-cls {
  color: #888e99;
}
.night .code-cls .ol-cls .li-cls div.hljs-ln-numbers {
  border-right-style: none;
}
.night .prettyprint .pre-numbering,
.night .prettyprint .pre-numbering .li-cls,
.night .code-cls .ol-cls .li-cls div.hljs-ln-numbers .hljs-ln-line {
  background: #34373d !important;
}
.night .prettyprint .pre-numbering,
.night .prettyprint .pre-numbering .li-cls {
  border-right-style: none;
}
.night .code-cls .ol-cls,
.night .code-cls .ul-cls {
  padding-left: 0;
  background-color: #25272b;
}
.night .table-cls .tr-cls,
.night .table-cls .tr-cls .th-cls,
.night .table-cls .tr-cls:nth-child(2n) {
  background-color: #2a2d33;
}
.night .table-cls .tr-cls,
.night .table-cls .tr-cls .th-cls,
.night .table-cls .tr-cls .td-cls,
.night .table-cls .tr-cls:nth-child(2n) {
  border: 1px solid #555963 !important;
}
.night .hljs {
  padding: 0;
}
.article_content.night {
  background-color: #2a2d33;
}
.night .pre-cls .code-cls {
  color: #fff;
  background-color: #25272b !important;
}
.night .hljs,
.night .pre-cls.prettyprint,
.night .pre-cls {
  background-color: #25272b;
}
.night svg {
  background-color: #fff;
}
.night .prettyprint .prism,
.night .prettyprint div[style] {
  background-color: #25272b !important;
  padding-left: 20px;
  padding-top: 10px;
  padding-bottom: 8px;
}
.night .prettyprint .prism .token.comment {
  color: #999aaa;
}
.night .pre-cls .code-cls .ol-cls .li-cls {
  background-color: #25272b !important;
}
.night .markdown_views .code-cls {
  background-color: transparent;
}
.night .markdown_views .hr-cls {
  border-bottom-color: #555963 !important;
}
.night .markdown_views .prettyprint .pre-numbering {
  top: 0;
}
.night .markdown_views .pre-cls.prettyprint,
.night .markdown_views .prettyprint {
  padding: 0 16px 6px 50px;
}
.night .markdown_views mark {
  color: #2a2d33;
}
.mermaid {
  line-height: initial;
}
.mermaid span.edgeLabel {
  font-size: inherit !important;
}
```

uni.scss

```scss
/**
 * 这里是uni-app内置的常用样式变量
 *
 * uni-app 官方扩展插件及插件市场（https://ext.dcloud.net.cn）上很多三方插件均使用了这些样式变量
 * 如果你是插件开发者，建议你使用scss预处理，并在插件代码中直接使用这些变量（无需 import 这个文件），方便用户通过搭积木的方式开发整体风格一致的App
 *
 */

/**
 * 如果你是App开发者（插件使用者），你可以通过修改这些变量来定制自己的插件主题，实现自定义主题功能
 *
 * 如果你的项目同样使用了scss预处理，你也可以直接在你的 scss 代码中使用如下变量，同时无需 import 这个文件
 */

/* 颜色变量 */

/* 行为相关颜色 */
$uni-color-primary: #007aff;
$uni-color-success: #4cd964;
$uni-color-warning: #f0ad4e;
$uni-color-error: #dd524d;

/* 文字基本颜色 */
$uni-text-color-title: #000; //标题色
$uni-text-color: #333; //基本色
$uni-text-color-inverse: #fff; //反色
$uni-text-color-grey: #999; //辅助灰色，如加载更多的提示信息
$uni-text-color-placeholder: #808080;
$uni-text-color-disable: #c0c0c0;
$uni-text-color-hot: #f94d2a; // 热点颜色
$uni-text-color-more: #5d83a8; // 更多颜色

/* 背景颜色 */
$uni-bg-color: #ffffff;
$uni-bg-color-grey: #f8f8f8;
$uni-bg-color-hover: #f1f1f1; //点击状态颜色
$uni-bg-color-mask: rgba(0, 0, 0, 0.4); //遮罩颜色

/* 边框颜色 */
$uni-border-color: #e5e5e5;

/* 尺寸变量 */

/* 文字尺寸 */
$uni-font-size-sm: 12px;
$uni-font-size-base: 14px;
$uni-font-size-lg: 16px;

/* 图片尺寸 */
$uni-img-size-sm: 20px;
$uni-img-size-base: 26px;
$uni-img-size-lg: 40px;

/* Border Radius */
$uni-border-radius-sm: 2px;
$uni-border-radius-base: 3px;
$uni-border-radius-lg: 6px;
$uni-border-radius-circle: 50%;

/* 水平间距 */
$uni-spacing-row-sm: 5px;
$uni-spacing-row-base: 10px;
$uni-spacing-row-lg: 15px;

/* 垂直间距 */
$uni-spacing-col-sm: 4px;
$uni-spacing-col-base: 8px;
$uni-spacing-col-lg: 12px;
$uni-spacing-col-big: 24px;

/* 透明度 */
$uni-opacity-disabled: 0.3; // 组件禁用态的透明度

/* 文章场景相关 */
$uni-color-title: #2c405a; // 文章标题颜色
$uni-font-size-title: 20px;
$uni-color-subtitle: #555555; // 二级标题颜色
$uni-font-size-subtitle: 26px;
$uni-color-paragraph: #3f536e; // 文章段落颜色
$uni-font-size-paragraph: 15px;
```

## 多平台适配

App.vue

```vue
<script>
export default {
  onLaunch: function () {
    /* #ifdef H5 */
    console.log('当前处于 H5 编译平台');
    /* #endif */
    /* #ifndef H5 */
    console.log('当前处于非 H5 编译平台');
    /* #endif */
  },
  onShow: function () {},
  onHide: function () {}
};
</script>

<style>
/*每个页面公共css */
</style>
```

index.html

```html
<!DOCTYPE html>
<html lang="zh-CN">

<head>
  <meta charset="utf-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <title>
    慕课热搜
  </title>
  <!-- 访问来源信息不随着请求一起发送 -->
  <!-- 解决 web 访问商品 403 的问题 -->
  <meta name="referrer" content="no-referrer" />
  <!-- Open Graph data -->
  <!-- <meta property="og:title" content="Title Here" /> -->
  <!-- <meta property="og:url" content="http://www.example.com/" /> -->
  <!-- <meta property="og:image" content="http://example.com/image.jpg" /> -->
  <!-- <meta property="og:description" content="Description Here" /> -->
  <script>
    var coverSupport = 'CSS' in window && typeof CSS.supports === 'function' && (CSS.supports('top: env(a)') || CSS.supports('top: constant(a)'))
    document.write('<meta name="viewport" content="width=device-width, user-scalable=no, initial-scale=1.0, maximum-scale=1.0, minimum-scale=1.0' + (coverSupport ? ', viewport-fit=cover' : '') + '" />')
  </script>
  <link rel="stylesheet" href="<%= BASE_URL %>static/index.<%= VUE_APP_INDEX_CSS_HASH %>.css" />
</head>

<body>
  <noscript>
    <strong>Please enable JavaScript to continue.</strong>
  </noscript>
  <div id="app"></div>
  <!-- built files will be auto injected -->
</body>

</html>
```

manifest.json

```json
{
  "name": "imooc-blog",
  "appid": "",
  "description": "",
  "versionName": "1.0.0",
  "versionCode": "100",
  "transformPx": false,
  "app-plus": {
    /* 5+App特有相关 */
    "usingComponents": true,
    "nvueCompiler": "uni-app",
    "splashscreen": {
      "alwaysShowBeforeRender": true,
      "waiting": true,
      "autoclose": true,
      "delay": 0
    },
    "modules": {},
    /* 模块配置 */
    "distribute": {
      /* 应用发布信息 */
      "android": {
        /* android打包配置 */
        "permissions": [
          "<uses-permission android:name=\"android.permission.CHANGE_NETWORK_STATE\"/>",
          "<uses-permission android:name=\"android.permission.MOUNT_UNMOUNT_FILESYSTEMS\"/>",
          "<uses-permission android:name=\"android.permission.VIBRATE\"/>",
          "<uses-permission android:name=\"android.permission.READ_LOGS\"/>",
          "<uses-permission android:name=\"android.permission.ACCESS_WIFI_STATE\"/>",
          "<uses-feature android:name=\"android.hardware.camera.autofocus\"/>",
          "<uses-permission android:name=\"android.permission.ACCESS_NETWORK_STATE\"/>",
          "<uses-permission android:name=\"android.permission.CAMERA\"/>",
          "<uses-permission android:name=\"android.permission.GET_ACCOUNTS\"/>",
          "<uses-permission android:name=\"android.permission.READ_PHONE_STATE\"/>",
          "<uses-permission android:name=\"android.permission.CHANGE_WIFI_STATE\"/>",
          "<uses-permission android:name=\"android.permission.WAKE_LOCK\"/>",
          "<uses-permission android:name=\"android.permission.FLASHLIGHT\"/>",
          "<uses-feature android:name=\"android.hardware.camera\"/>",
          "<uses-permission android:name=\"android.permission.WRITE_SETTINGS\"/>"
        ]
      },
      "ios": {},
      /* ios打包配置 */
      "sdkConfigs": {}
    }
  },
  /* SDK配置 */
  "quickapp": {},
  /* 快应用特有相关 */
  "mp-weixin": {
    /* 小程序特有相关 */
    "appid": "wx18d819fdfaf58089",
    "setting": {
      "urlCheck": false,
      "checkSiteMap": false
    },
    "usingComponents": true
  },
  "h5": {
    "template": "index.html"
  }
}
```

























































































































































































































































































































