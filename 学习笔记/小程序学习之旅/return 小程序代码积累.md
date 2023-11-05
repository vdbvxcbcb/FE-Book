## 自定义 tabBar

app.json

```json
{
  "pages": [
    "pages/index/index",
    "pages/order/order",
    "pages/my/my"
  ],
  "window": {
    "backgroundTextStyle": "light",
    "navigationBarBackgroundColor": "#d6ecea",
    "navigationBarTitleText": "蕾粉+",
    "navigationBarTextStyle": "black"
  },
  "tabBar": {
    "custom": true,
    "selectedColor": "#2ad987",
    "backgroundColor": "#ffffff",
    "borderStyle": "black",
    "list": [
      {
        "pagePath": "pages/index/index",
        "iconPath": "./assets/home.png",
        "selectedIconPath": "./assets/home-active.png",
        "text": "首页"
      },
      {
        "pagePath": "pages/order/order",
        "iconPath": "./assets/alarm-clock.png",
        "selectedIconPath": "./assets/alarm-clock-active.png",
        "text": "预约"
      },
      {
        "pagePath": "pages/my/my",
        "iconPath": "./assets/tips.png",
        "selectedIconPath": "./assets/tips-active.png",
        "text": "我的"
      }
    ]
  },
  "style": "v2",
  "sitemapLocation": "sitemap.json"
}
```

custom-tab-bar\index.js

```js
// custom-tab-bar/index.js
Component({
  data: {
    selected: 0,
    color: "#7A7E83",
    selectedColor: "#3cc51f",
    list: [
      {
        pagePath: "/pages/index/index",
        iconPath: "/assets/home.png",
        selectedIconPath: "/assets/home-active.png",
        text: "首页"
      }, 
      {
        pagePath: "/pages/order/order",
        iconPath: "/assets/alarm-clock.png",
        selectedIconPath: "/assets/alarm-clock-active.png",
        text: "预约"
      },
      {
        pagePath: "/pages/my/my",
        iconPath: "/assets/tips.png",
        selectedIconPath: "/assets/tips-active.png",
        text: "我的"
      }
    ]
  },
  attached() {
  },
  methods: {
    switchTab(e) {
      const data = e.currentTarget.dataset
      const url = data.path
      wx.switchTab({url})
    }
  }
})
```

custom-tab-bar\index.wxml

```html
<!--miniprogram/custom-tab-bar/index.wxml-->
<view class="tab-bar">
  <view class="tab-bar-border"></view>
  <view wx:for="{{list}}" wx:key="index" class="tab-bar-item" data-path="{{item.pagePath}}" data-index="{{index}}" bindtap="switchTab">
    <image src="{{selected === index ? item.selectedIconPath : item.iconPath}}"></image>
    <view style="color: {{selected === index ? selectedColor : color}}">{{item.text}}</view>
  </view>
</view>
```

custom-tab-bar\index.wxss

```css
/* custom-tab-bar/index.wxss */
.tab-bar {
  position: fixed;
  bottom: 0;
  left: 0;
  right: 0;
  height: 48px;
  background: white;
  display: flex;
  z-index: 2;
  padding-bottom: env(safe-area-inset-bottom);
}

/* .tab-bar-border {
  background-color: rgba(0, 0, 0, 0.33);
  position: absolute;
  left: 0;
  top: 0;
  width: 100%;
  height: 1rpx;
} */

.tab-bar-item {
  display: flex;
  flex-direction: column;
  flex: 1;
  justify-content: center;
  align-items: center;
  text-align: center;
}

.tab-bar-item image {
  width: 50rpx;
  height: 50rpx;
}

.tab-bar-item view {
  font-size: 20rpx;
}

```

pages\index\index.json

```json
{
  "usingComponents": {
    "v-tab-bar": "/custom-tab-bar/index"
  }
}
```

pages\index\index.wxml

```html
<view class="container">
  <view class="swiper">
    <swiper indicator-dots="true" 	indicator-active-color="#4d9896"
      autoplay="true" interval="2000" duration="500" style="height: 400rpx;">
      <block wx:for="{{ background }}" wx:key="*this">
        <swiper-item class="swiper-item">
          <image class="swiper-item-image {{ item }}" src="../../assets/swiper.png"></image>
        </swiper-item>
      </block>
    </swiper>
  </view>
  <view class="content"> 
   ...
  </view>
  <v-tab-bar></v-tab-bar>
</view>
```

pages\index\index.js

```js
// index.js
// 获取应用实例
const app = getApp()

Page({
  data: {
    background: ["demo-text-1", "demo-text-2", "demo-text-3"],
    userInfo: {},
    hasUserInfo: false,
    beautician: "张笑笑",
    commentImage: ["../../assets/comment-1.png", "../../assets/comment-2.png", "../../assets/comment-1.png"]
  },
    
  onLoad() {
    const hasUserInfo = wx.getStorageSync("hasUserInfo")
    const userInfo = wx.getStorageSync("userInfo")
    if (wx.getUserProfile && !hasUserInfo) {
      wx.showModal({
        content: '为了更好的用户体验，我们将获取您的头像信息和名称',
        success: (res) => {
          this.getUserProfile()
        }
      })
      console.log(this.data.userInfo)
    }
    if (hasUserInfo) {
      this.setData({
        userInfo,
        hasUserInfo
      })
    }
  },
    
  onShow() {
    if (typeof this.getTabBar === 'function' && this.getTabBar()) {
      this.getTabBar().setData({
        selected: 0
      })
    }
  },
  getUserProfile(e) {
    // 推荐使用wx.getUserProfile获取用户信息，开发者每次通过该接口获取用户个人信息均需用户确认，开发者妥善保管用户快速填写的头像昵称，避免重复弹窗
    wx.getUserProfile({
      desc: '展示用户信息', // 声明获取用户个人信息后的用途，后续会展示在弹窗中，请谨慎填写
      success: (res) => {
        this.setData({
          userInfo: res.userInfo,
          hasUserInfo: true
        })
        // 缓存用户信息
        wx.setStorageSync('userInfo', res.userInfo)
        wx.setStorageSync('hasUserInfo', true)
      }
    })
  },
})
```

pages\my\my.json

```json
{
  "navigationBarTitleText": "我的",
  "navigationBarBackgroundColor": "#f1f6fa",
  "usingComponents": {
    "v-tab-bar": "/custom-tab-bar/index"
  }
}
```

pages\my\my.wxml

```html
<!--pages/my/my.wxml-->
<view class="my-wrapper">
  ...
  <v-tab-bar></v-tab-bar>
</view>
```

pages/my/my.js

```js
// pages/my/my.js
Page({

  /**
   * 页面的初始数据
   */
  data: {
    userInfo: {},
    introduce: [
      {
        path: "../../assets/shop.png",
        text: "天宝路店"
      },
      {
        path: "../../assets/beautician.png",
        text: "张笑笑"
      },  
    ],
    services: [
      {
        path: "../../assets/transaction-order.png",
        text: "消费订单"
      },
      {
        path: "../../assets/doc-search.png",
        text: "产品柜"
      },
      {
        path: "../../assets/folder-quality.png",
        text: "服务项目"
      }
    ],
    list: [
      {
        path: "../../assets/coupon.png",
        text: "卡券包"
      },
      {
        path: "../../assets/newspaper-folding.png",
        text: "商品订单"
      },
      {
        path: "../../assets/box.png",
        text: "千店连锁"
      },
      {
        path: "../../assets/mail.png",
        text: "投诉建议"
      }
    ]
  },

  /**
   * 生命周期函数--监听页面加载
   */
  onLoad() {
    const hasUserInfo = wx.getStorageSync("hasUserInfo")
    const userInfo = wx.getStorageSync("userInfo")
    if (hasUserInfo) {
      this.setData({
        userInfo
      })
    }
  },

  /**
   * 生命周期函数--监听页面显示
   */
  onShow() {
    if (typeof this.getTabBar === 'function' && this.getTabBar()) {
      this.getTabBar().setData({
        selected: 2
      })
    }
  },
})
```

## 首页中间部分

![img](https://cdn.nlark.com/yuque/0/2022/png/1614731/1658663434442-b498a915-5f2d-4be1-a098-f81bf35de4c8.png)



```css
<view class="content"> 
</view>
```



```css
.content {
  box-sizing: border-box;
  width: 100%;
  height: 1700rpx;
  padding: 0 30rpx;
  background-color: #f3f6f8;
}
```



## 毛玻璃背景

![img](https://cdn.nlark.com/yuque/0/2022/png/1614731/1658661708334-7b03e994-0ea0-405f-877b-e8b4ef9ba29b.png)



```html
<view class="live-card">
    <view class="live-card-item">
        <view class="live-card-background"></view>
        <view class="live-card-frontground">
            <view class="live-card-content">
                <image class="live-card-image" src="../../assets/live.png"></image>
                <view class="live-card-text">
                    <text class="live-theme">如何利用蕾粉+做好客情服务</text> 
                    <text class="live-anchor">主播：文凤老师、郭思</text> 
                </view> 
            </view>
            <view class="order-time">
                <text class="order-left-time">距离开播还有2天10时3分1秒</text>
                <view class="order-btn">
                    预约  
                </view>
            </view>
        </view>
    </view>
</view>
```



```css
.live-card-wrapper {
  background: #fff;
  border-radius: 20rpx;
  margin-top: 20rpx;
  padding: 30rpx 0;
}

.live-card-title {
  display: flex;
  justify-content: space-between;
  padding: 0 40rpx 20rpx 36rpx;
  border-bottom: 2rpx solid #999;
  border-color: #e8e7e7;
}

.live-text {
  font-size: 30rpx;
}

.more {
  display: inline-flex;
  align-items: center;
  font-size: 24rpx;
  color: #9b9b98;
}

.more::after {
  display: inline-block;
  content: "";
  width: 10rpx;
  height: 10rpx;
  border-top: 2rpx solid #999;
  border-right: 2rpx solid #999;
  transform: rotate(45deg);
}

.live-card {
  padding: 0 30rpx;
  margin-top: 10rpx;
  margin-bottom: 30rpx;
}

.live-card-item {
  position: relative;
}

.live-card-background {
  position: absolute;
  background: no-repeat center url("https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1658590440313-061b8b65-5d46-4d07-9a1a-488a41c88bf8.jpeg");
  width: 100%;
  height: 100%;
  filter: blur(5rpx);
  border-radius: 20rpx;
}

.live-card-frontground {
  position: relative;
  background: rgba(0, 0, 0, 0.3);
  border-radius: 20rpx;
}

.live-card-content {
  display: flex;
  padding-left: 24rpx;
  padding-right: 50rpx;
}

.live-card-image {
  width: 200rpx;
  height: 160rpx;
  border-radius: 20rpx;
  margin: 24rpx 18rpx 0 0;
}

.live-card-text {
  display: flex;
  flex-direction: column;
  justify-content: space-between;
  flex-wrap: wrap;
  font-size: 28rpx;
  width: 320rpx;
  padding: 36rpx 0;
}

.live-anchor {
  margin-top: 18rpx;
}

.live-theme {
  color: #fff;
}

.live-anchor {
  color: #aeb4c3;
}

.order-time {
  display: flex;
  justify-content: space-between;
  background: rgba(153,147,146, 0.5);
  border-radius: 0 0 20rpx 20rpx;
  padding: 8rpx 20rpx 8rpx 24rpx;
  font-size: 24rpx;
  color: #fff;
}

.order-left-time {
  padding: 12rpx 0;
}

.order-btn {
  display: flex;
  justify-content: center;
  background: #36dd88;
  padding: 14rpx 40rpx;
  border-radius: 60rpx;
}
```



## 多行省略（不需要定宽）

![Snipaste_2022-07-24_19-33-55.png](https://cdn.nlark.com/yuque/0/2022/png/1614731/1658662440888-b9e99211-e2c6-4633-8f11-cc0d05a57234.png)



```html
<view class="praise-wrapper">
    <view class="praise-text">
        <text class="praise-title">好评专区</text>
        <text class="more">查看更多</text>
    </view>
    <view class="praise-card">
        <view class="praise-user-wrapper">
            <view class="praise-user">
                <view class="praise-user-avartar"></view>
                <view class="praise-user-info">
                    <text class="praise-user-name">程欣欣</text>
                    <text class="praise-comment-time">2020.04.19 10:00</text>
                </view>
            </view>
            <view class="expression-container">
                <view class="expression-comment">
                    <image class="expression-image" src="../../assets/expression.png"></image> 
                </view>
                <text class="comment-level">强烈推荐</text>
            </view>
        </view>
        <view class="praise-content">
            <view class="praise-text-contianer">
                <view class="praise-content-text">非常满意，美容师态度很好，说话耐心且有礼貌，手法娴熟、专业，做完项目后皮肤有显著的变化QWEERT</view>
            </view>
            <view class="comment-image-wrapper">
                <view class="comment-image-container" wx:for="{{ commentImage }}" wx:key="index">
                    <image class="comment-image" src="{{ item }}"></image> 
                </view>
            </view>
        </view>
    </view>
</view>
```



```css
.praise-wrapper {
  margin-top: 50rpx;
}

.praise-text {
  display: flex;
  justify-content: space-between;
  padding-bottom: 20rpx;
}

.praise-text .more {
  position: relative;
  right: 4rpx;
}

.praise-card {
  padding: 30rpx 24rpx 30rpx 22rpx;
  background: #fff;
  border-radius: 20rpx;
}

.praise-user-wrapper {
  display: flex;
  justify-content: space-between;
} 

.praise-user {
  display: flex;
}

.praise-user-avartar {
  width: 70rpx;
  height: 70rpx;
  border-radius: 50%;
  background: #000;
  margin-right: 16rpx;
}

.praise-user-info {
  display: flex;
  flex-direction: column;
}

.praise-user-name {
  font-size: 30rpx;
}

.praise-comment-time {
  font-size: 20rpx;
  color: #94969d;
}

.expression-container {
  display: flex;
  align-items: center;
}

.expression-comment {
  font-size: 0;
  margin-right: 10rpx;
}

.expression-image {
  width: 38rpx;
  height: 38rpx;
}

.comment-level {
  font-size: 26rpx;
}

.praise-content {
  padding-left: 86rpx;
} 

.praise-content-text {
  display: -webkit-box;
  overflow: hidden;
  text-overflow: ellipsis;
  word-break: break-all;
  -webkit-box-orient: vertical;
  -webkit-line-clamp: 2;
  font-size: 26rpx;
  margin: 20rpx 0;
}

.comment-image-wrapper {
  display: flex;
}

.comment-image-container {
  margin-right: 20rpx;
}

.comment-image {
  width: 146rpx;
  height: 146rpx;
  border-radius: 10rpx;
}
```



## 弧形边框

![img](https://cdn.nlark.com/yuque/0/2022/png/1614731/1658664980280-154ea78e-9641-41ed-80de-e7e02b10da3f.png)

```html
<!--pages/my/my.wxml-->
<view class="my-wrapper">
  <!-- 用户信息 -->
  <view class="background-wrapper">
    <view class="user-wrapper">
      <view class="username-experience">
        <view class="name-avatar">
          <image class="user-avatar" src="{{ userInfo.avatarUrl }}" mode="cover"></image>
          <text>{{ userInfo.nickName }}</text>
        </view>
        <view class="user-experience">
          <view class="image-wrapper">
            <image class="user-experience-image" src="../../assets/experience.png"></image>
          </view>
          经验值: 200
        </view>
      </view>
      <view class="user-coupon">
        <text class="coupon-text">优惠券</text>
        <text class="coupon-text">12张</text>
        <view class="check-details-btn">查看详情</view>
      </view>
    </view>
  </view>
  <!-- 门店美容师 -->
  <view class="shop-beautician-wrapper">
    <view wx:for="{{ introduce }}" wx:key="index" class="introduce-shop-beautician">
      <text wx:if="{{ index === 0 }}" class="introduce-text">{{ item.text }}</text>
      <text wx:else class="introduce-text">美容师:{{ item.text }}</text>
      <image class="introduce-image" src="{{ item.path }}"></image>
      <view class="introduce-btn">
        <text wx:if="{{ index === 0 }}" class="introduce-btn-text">去门店</text>
        <text wx:else class="introduce-btn-text">去预约</text>
        <view class="arrow-wrapper">
          <view class="arrow"></view>
        </view>
      </view>
    </view>   
  </view>
  ...
  <v-tab-bar></v-tab-bar>
</view>
```



```css
.my-wrapper {
  background: #f9f8f8;
  width: 100%;
  height: 1500rpx;
}

.background-wrapper {
  background: #fff;
}

.user-wrapper {
  padding: 60rpx 60rpx 0 60rpx;
  background: linear-gradient(to right bottom, #f5f5f9, #e8f0f9);
  border-radius: 0 0 30% 30%;
}

.username-experience {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.name-avatar {
  display: flex;
  align-items: center;
  font-size: 40rpx;
}

.user-avatar {
  overflow: hidden;
  width: 108rpx;
  height: 108rpx;
  margin-right: 20rpx;
  border-radius: 50%;
}

.user-experience {
  display: flex;
  align-items: center;
  color: #8c979f;
  font-size: 26rpx;
}

.image-wrapper {
  font-size: 0;
  margin-right: 4rpx;
}

.user-experience-image {
  width: 28rpx;
  height: 28rpx;
}

/* .user-experience::after{
  display: block;
  background: #f6ba93;
  width: 26rpx;
  height: 26rpx;
  border-radius: 50%;
} */

.user-coupon {
  display: flex;
  justify-content: space-between;
  padding: 50rpx 44rpx;
  margin-top: 26rpx;
  border-radius: 20rpx 20rpx 26% 26%;
  background: url("https://img0.baidu.com/it/u=3316953316,4033444958&fm=253&fmt=auto&app=138&f=JPEG?w=780&h=377");
}

.coupon-text {
  color: #b66d3a;
  font-size: 36rpx;
}

.check-details-btn {
  font-size: 24rpx;
  color: #fff;
  background:#b66d3a;
  padding: 8rpx 10rpx;
  border-radius: 20rpx ;
}

.shop-beautician-wrapper {
  display: flex;
  justify-content: space-between;
  background: #fff;
  padding: 40rpx 110rpx;
  margin-bottom: 20rpx;
  /* clip-path: polygon(0 0, 48% 5%, 100% 0, 100% 80%, 100% 100%, 45% 100%, 0 100%, 0 20%); */
}

.introduce-shop-beautician {
  display: flex;
  flex-direction: column;
  align-items: center;
}

.introduce-text {
  font-size: 30rpx;
}

.introduce-image {
  width: 220rpx;
  height: 130rpx;
  border-radius: 10rpx;
  margin-top: 20rpx;
}

.introduce-btn {
  position: relative;
  bottom: 20rpx;
  display: flex;
  align-items: center;
  font-size: 24rpx;
  color: #fff;
  background: #4fd894;
  padding: 15rpx 10rpx 15rpx 35rpx;
  border-radius: 30rpx;
}

.introduce-btn-text {
  margin-right: 10rpx;
}

.arrow-wrapper {
  position: relative;
  width: 28rpx;
  height: 28rpx;
  background: #fff;
  border-radius: 50%;
}

.arrow {
  position: absolute;
  top: 50%;
  left: 50%; 
  transform: translate(-75%, -50%) rotate(45deg);
  width: 8rpx;
  height: 8rpx;
  border-top: 2rpx solid #4fd894;
  border-right: 2rpx solid #4fd894;
}
```



## CSS 展开收起

![Snipaste_2022-07-24_21-17-58.png](https://cdn.nlark.com/yuque/0/2022/png/1614731/1658668696547-87ce2f2f-6ab7-4ce0-ae0b-f3133654af71.png)

pages\details\details.js

```js

Page({

  data: {
    originalPrice: true,
    isOriginalPrice: false,
    listData: [
      {
        title: '护理产品',
        pic: '/assets/list.png',
        goods_introduce: '依云矿泉水能量冰膜焕活紧致收细专属套组',
        goods_discount: '限时折扣',
        goods_reduction: '满减送',
        goods_preferential: '特惠套餐',
        goods_price: '￥500',
        goods_number: '×1',
        total_price: '实际需付 ￥100.00',
        goods_id: 1,
        tap_pic: '/assets/3.png',
        buttom_pic: '/assets/4.png',
        status: false,
        original_status: false,
        deadline_discount: 
          {
            discount: '限时折扣',
            discount_number: '5折',
            reduction: '满减送',
            reduction_number: '满500减100',
            discount_price: '￥-250.00',
            reduction_price: '￥-100.00',
            status: false,
          },
        package_details: 
          {
            title: '套餐详情',
            tap_pic: '/assets/3.png',
            buttom_pic: '/assets/4.png',
            status: false,
          },
        details_list: [
          {
              goods_id: 0,
              pic: '/assets/list 2.png',
              goods_introduce: '依云矿泉水能量冰膜焕活紧致收细专属套组',
              goods_price: '￥500',
              goods_number: '×1',
              status: false,
          },
          {
              goods_id: 1,
              pic: '/assets/list 3.png',
              goods_introduce: '依云矿泉水能量冰膜焕活紧致收细专属套组',
              goods_price: '￥500',
              goods_number: '×1',
              status: false,
          },
        ]
      },
      {
        pic: '/assets/list.png',
        goods_introduce: '依云矿泉水能量冰膜焕活紧致收细专属套组',
        goods_discount: '限时折扣',
        goods_reduction: '满减送',
        goods_preferential: '特惠套餐',
        goods_price: '￥500',
        goods_number: '×1',
        total_price: '实际需付 ￥100.00',
        goods_id: 1,
        tap_pic: '/assets/3.png',
        buttom_pic: '/assets/4.png',
        status: false,
        original_status: false,
        deadline_discount: 
          {
            discount: '限时折扣',
            discount_number: '5折',
            reduction: '满减送',
            reduction_number: '满500减100',
            discount_price: '￥-250.00',
            reduction_price: '￥-100.00',
            status: false,
          },
        package_details: 
          {
            title: '套餐详情',
            tap_pic: '/assets/3.png',
            buttom_pic: '/assets/4.png',
            status: false,
          },
        details_list: [
          {
              goods_id: 0,
              pic: '/assets/list 2.png',
              goods_introduce: '依云矿泉水能量冰膜焕活紧致收细专属套组',
              goods_price: '￥500',
              goods_number: '×1',
              status: false,
          },
          {
              goods_id: 1,
              pic: '/assets/list 3.png',
              goods_introduce: '依云矿泉水能量冰膜焕活紧致收细专属套组',
              goods_price: '￥500',
              goods_number: '×1',
              status: false,
          },
        ]
      },
    ],
  },

  isShow (e) {
    const { index,status } = e.currentTarget.dataset
    let name = `listData[${index}].status`
    this.setData({
      [name]: !status,
    })
  },

  clickShow (e) {
    const { index, status } = e.currentTarget.dataset
    console.log(e);
    let name = `listData[${index}].deadline_discount.status`
    this.setData({
      [name]: !status,
    })
  },
  
  clickPackage (e) {
    const { index, status } = e.currentTarget.dataset
    console.log(e);
    let name = `listData[${index}].package_details.status`
    this.setData({
      [name]: !status
    })
  },

  clickOriginal (e) {
    const { index,status } = e.currentTarget.dataset
    let name = `listData[${index}].original_status`
    this.setData({
      [name]: !status
    })
  }
})
```

pages\details\details.wxml

```html
<!-- 商品详情 -->
<view>
    <view class="details-list" wx:for="{{ listData }}" wx:key="index">
        <view wx:if="{{ item.title }}" class="title">{{ item.title }}</view>
        <!-- 商品 -->
        <view class="list">
            <view class="list-icon-wrapper">
                <view class="list-icon" wx:if="{{ item.goods_discount }}" class="goods-discount reuse">{{ item.goods_discount }}</view>
                <view class="list-icon" wx:if="{{ item.goods_reduction }}" class="goods-reduction reuse">{{ item.goods_reduction }}</view>
                <view class="list-icon" wx:if="{{ item.goods_preferential }}" class="goods-preferential reuse">{{ item.goods_preferential }}</view>
            </view>   
            <view class="list-information-wrapper">
                <view bindtap="isShow" class="list-img" data-index="{{ index }}" data-status="{{ item.status }}">
                    <image class="list-img" src="{{ item.pic }}"></image>
                </view>
                <view class="goods-introduce">
                    <text>{{ item.goods_introduce }}</text>
                </view>
                <view class="price">
                    <text>{{ item.goods_price }}</text>
                    <text class="right">{{ item.goods_number }}</text>
                </view>
                <!-- 实际需付 -->
                <text class="total-price">{{ item.total_price }}</text>
                <view class="image" bindtap="clickShow" data-index="{{ index }}" data-status="{{ item.deadline_discount.status }}">
                    <image src="{{ item.deadline_discount.status === true ? item.tap_pic : item.buttom_pic }}"></image>
                </view>
            </view>
            <!-- 折扣 展开/收起 -->
            <view class="discount-list-wrapper">
                <view class="goods-list-discount {{ item.deadline_discount.status  ? 'goods-discount-clicked' : '' }}">
                    <view class="goods-discount-text">
                        <text class="discount">{{ item.deadline_discount.discount }}</text>
                        <text>{{ item.deadline_discount.discount_number }}</text>
                        <text class="discount-price">{{ item.deadline_discount.discount_price }}</text>
                    </view>
                    <view class="tap goods-discount-text">
                        <text class="discount">{{ item.deadline_discount.reduction }}</text>
                        <text>{{ item.deadline_discount.reduction_number }}</text>
                        <text class="discount-price">{{ item.deadline_discount.reduction_price }}</text>
                    </view>
                </view>
            </view>
        </view>   
        <!-- 套餐详情 -->
        <block>
            <view class="package-detail-wrapper">
                <view class="package-detail">
                    <text>套餐详情</text>
                    <view bindtap="clickPackage" class="image" data-index="{{ index }}" data-status="{{ item.package_details.status }}">
                        <image src="{{ item.package_details.status === true ? item.tap_pic : item.buttom_pic }}"></image>
                    </view>
                </view>
            </view>
        </block>
        <!-- 套餐详情 展开/收起 -->
        <view class="package-list-wrapper  {{ item.package_details.status ? 'package-detail-clicked' : '' }}">
            <view class="package-list" wx:for="{{ item.details_list }}" wx:for-item="items" wx:key="index">
                <view class="list-img">
                    <image class="list-img" src="{{ items.pic }}"></image>
                </view>
                <view class="goods-introduce">
                    <text>{{ items.goods_introduce }}</text>
                </view>
                <view class="price">
                    <text>{{ items.goods_price }}</text>
                    <text class="right">{{ items.goods_number }}</text>
                </view>
            </view>
        </view>
    </view>
</view>
<!-- 原价合计 -->
<view class="original-price">
    <text>原价合计</text>
    <view bindtap="clickOriginal" wx:for="{{ listData }}" wx:key="index" class="image" data-index="{{ index }}" data-status="{{ item.original_status }}">
        <image src="{{ item.original_status ? item.tap_pic : item.buttom_pic }}"></image>
    </view>
    <text class="original-price-num">￥800</text>
</view>
<!-- 原价合计 展开/收起 -->
<block wx:for="{{ listData }}" wx:key="index">
    <view class="original-discount-wrapper">
        <view class="goods-list-discount {{ item.original_status ?    'goods-discount-clicked' : '' }}">
            <view class="goods-discount-text">
                <text class="discount">{{ item.deadline_discount.discount }}</text>
                <text>{{ item.deadline_discount.discount_number }}</text>
                <text class="discount-price">{{ item.deadline_discount.discount_price }}</text>
            </view>
            <view class="tap goods-discount-text">
                <text class="discount">{{ item.deadline_discount.reduction }}</text>
                <text>{{ item.deadline_discount.reduction_number }}</text>
                <text class="discount-price">{{ item.deadline_discount.reduction_price }}</text>
            </view>
        </view>
    </view>
</block>
```

pages\details\details.wxss

```css
.details-list {
  background-color: #fff;
  margin-top: 20rpx;
  padding-bottom: 20rpx;
  border-radius: 16rpx;
}

.details .details-list:last-child  {
  border-radius: 16rpx 16rpx 0 0; 
}

.details-list .title {
  height: 80rpx;
  line-height: 80rpx;
  border-bottom: 1px solid #eee;
  font-weight: 700;
  color: #303030;
  font-size: 32rpx;
  padding: 0 20rpx;
}

.list {
  position: relative;
  display: flex;
  flex-direction: column;
  width: 670rpx;
  padding: 20rpx;
}

.list-icon-wrapper {
  display: flex;
}

.list-icon-wrapper .list-icon {
  display: inline-block;
}

.list-information-wrapper {
  position: relative;
  display: flex;
  flex: start;
  margin-top: 16rpx;
}

.list-img {
  display: inline-block;
  font-size: 0;
}

.list-img image {
  width: 140rpx;
  height: 140rpx;
}

.price {
  display: flex;
  flex-direction: column;
  margin-left: 100rpx;
  font-size: 20rpx;
  color: #999;
}

.price .right {
  text-align: right;
}

.goods-introduce {
  display: inline-block;
  width: 50%;
  font-size: 25rpx;
  margin-left: 20rpx;
}

.reuse {
  padding: 2rpx 10rpx;
  border-radius: 6rpx;
  margin-right: 10rpx;
  color: #fff;
  font-size: 20rpx;
}

.goods-discount {
  background: linear-gradient(90deg, #a060ec, #c85fd4);
}

.goods-reduction {
  background: linear-gradient(90deg, #ff896a, #ff3a39);
}

.goods-preferential {
  background: linear-gradient(90deg, #fb5b56, #f10652);
}

.total-price {
  position: absolute;
  bottom: 20rpx;
  right: 60rpx;
  font-weight: 700;
  font-size: 28rpx;
}

.list-information-wrapper .image {
  position: absolute;
  bottom: 35rpx;
  right: 30rpx;
  width: 20rpx;
  height: 10rpx;
  font-size: 0;
}

.image image {
  width: 100%;
  height: 100%;
}

.discount-list-wrapper {
  margin-top: 15rpx;
  background-color: #f8fafe;
}

.goods-list-discount {
  font-size: 24rpx;
  color: #999;
  max-height: 0;
  transition: 1s;
  -moz-transition: 1s;
  -webkit-transition: 1s;
  -o-transition: 1s;
  overflow: hidden;
}

.goods-discount-clicked {
  max-height: 500rpx;
}

.goods-discount-text {
  padding: 10rpx 20rpx;
}

.goods-list-discount .discount {
  display: inline-block;
  width: 120rpx;
  font-size: 26rpx;
  color: #303030;
  margin-right: 10rpx;
}

.goods-list-discount .discount-price {
  float: right;
}

.original-discount-wrapper {
  background-color: #f8fafe;
}

.tap {
  margin-top: 10rpx;
  padding-bottom: 18rpx;
  border-bottom: 1px dashed #ccc;
}

.tap:last-child {
  border-bottom: none;
}
```

## 直播间

![img](https://cdn.nlark.com/yuque/0/2022/png/1614731/1659871803163-576c0f00-41cf-4cb9-a1b1-ab83a839cf34.png)

```html
<!-- 直播间列表 -->
<view class="living-list-wrapper">
  <!-- 正在直播 -->
  <view>
    <view wx:if="{{ false }}" class="living-title">正在直播</view>
    <view class="living-title">最新直播·今日19:00</view>
    <view class="living" bindtap="livePlayer">
      <image class="living-image" src="../../../images/live-banner.jpg" mode="aspectFill"></image>
      <view class="mask"></view>
      <view class="living-status">
        <image src="{{ oss_img_url }}/lte_pro_img/icon/start_icon.png"></image>
        <text>直播中</text>
      </view>
      <view class="banner-bottom">
        <text class="banner-text">如何借助蕾粉+做好客情服务</text>
        <view class="banner-order-btn">预约</view>
      </view>
    </view>
    <view class="living-predict">直播预告</view>
    <view class="living-list-card">
      <block wx:for="{{[1, 2, 3]}}" wx:key="index">
        <view class="living-list-item">
          <view class="list-top">
            <view class="image-wrapper">
              <image src="../../../images/live.png"></image>
            </view>
            <view class="living-info">
              <text class="live-theme">如何利用蕾粉+做好客情服务</text>
              <text class="live-anchor">主播：文凤老师、郭思</text> 
            </view>
          </view>
          <view class="list-bottom">
            <view class="order-time">
              <text class="order-left-time">距离开播还有2天10时3分1秒</text>
              <view class="order-btn">预约</view>
            </view>
          </view>
        </view>
      </block>
    </view>
  </view>
</view> 
```



```css
/* 直播 */
.living-list-wrapper {
  padding: 14rpx 30rpx 0;
}

.living {
  position: relative;
  font-size: 0;
}

.living-image {
  width: 690rpx;
  height: 364rpx;
  border-radius: 20rpx;
}

.mask {
  position: absolute;
  top: 0;
  left: 0;
  background: rgba(0 , 0, 0, 0.4);
  width: 690rpx;
  height: 364rpx;
  border-radius: 20rpx;
} 

.living-title{
  margin-bottom: 18rpx;
  font-weight: bold;
  font-size: 34rpx;
  color: #333333;
}

.living-status {
  position: absolute;
  top: 22rpx;
  left: 22rpx;
  width: 122rpx;
  height: 40rpx;
  text-align: center;
  line-height: 40rpx;
  background: linear-gradient(90deg, #FF3737 0%, #FF6037 100%);
  border-radius: 10rpx;
  font-size: 22rpx;
  font-weight: bold;
  color: #FFFFFF;
  letter-spacing: 5rpx;
}

.living-status image {
  display: inline-block;
  width: 18rpx;
  height: 20rpx;
  padding-right: 10rpx;
}

.banner-bottom {
  display: flex;
  justify-content: space-between;
  padding: 0 30rpx;
  margin-top: -80rpx;
  margin-bottom: 32rpx;
  z-index: 2;
}

.banner-text {
  font-size: 32rpx;
  color: #fff;
  z-index: 2;
}

.banner-order-btn {
  padding: 4rpx 36rpx;
  font-size: 30rpx;
  color: #FF8C51;
  background: #FFFFFF;
  border-radius: 100rpx;
  z-index: 2;
}

.living-predict {
  font-weight: bold;
  font-size: 34rpx;
  color: #333333;
  padding: 28rpx 0 14rpx;
}

.living-list-card {
  padding: 30rpx 30rpx 0;
  background: #fff;
  border-radius: 10rpx;
}

.living-list-item:last-child .list-bottom {
  border-bottom: 0;
  padding-bottom: 83rpx;
}

.list-top {
  display: flex;
  justify-content: space-between;
  margin-bottom: 7rpx;
}

.image-wrapper {
  font-size:  0;
  margin-right: 16rpx;
}

.image-wrapper image{
  width: 200rpx;
  height: 160rpx;
  border-radius: 10rpx;
}

.living-info {
  display: flex;
  flex-direction: column;
  justify-content: space-between;
  padding: 9rpx 0 19rpx;
}

.live-theme {
  font-size: 32rpx;
  color: #333;
  font-weight: bold;
}

.live-anchor {
  font-size: 24rpx;
  font-weight: 500;
  color: #7B7B7B;
}

.list-bottom {
  border-bottom: 1rpx solid #E6E6E6;
  margin-bottom: 32rpx;
}

.order-time {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding-bottom: 40rpx;
}

.order-left-time {
  font-size: 28rpx;
  color: #6C6CA5;
}

.order-btn {
  padding: 8rpx 24rpx;
  font-size: 24rpx;
  color: #fff;
  background: linear-gradient(270deg, #39DB8B 0%, #18CB83 100%);
  border-radius: 100rpx;
}
```

## 转店转诊

```html
<!-- 转诊列表 -->
<!-- tab 栏 -->
<view wx:if="{{ tabMenu.list[tabMenu.b_index].statusList.length }}" class="tab-box">
  <view class="tab-list">
    <view
    wx:for="{{ tabMenu.list[tabMenu.b_index].statusList }}"
    wx:key="index"
    class="tab-item {{ tabMenu.t_index == index ? 'active' : '' }}"
    data-index="{{ index }}"
    data-id="{{ item.value }}"
    bindtap="onTopTabChange">{{ item.label }}</view>
  </view>
  <view class="line-box" style="width: {{ 100 / tabMenu.list[tabMenu.b_index].statusList.length +'%' }}; transform: translateX({{ 100 * tabMenu.t_index +'%' }})">
    <text class="line"></text>
  </view>
</view>
<view class="filter-wrap">
  <view class="search-box">
    <image src='{{ oss_img_url }}/image/treatment/search.png' class="search-icon"></image>
    <input
      placeholder="请输入"
      value="{{ listParams.name }}"
      class="search-content"
      placeholder-class="search-placeholder"
      bindinput="handleSearchChange"
      bindblur="init"/>
  </view>
  <view wx:if="{{ listParams.type == 1 }}" class="add-btn" bindtap="handleAddTransfer">
    <image src="{{ oss_img_url }}/image/costomer_transfer/add.png"></image>
    <text>新建{{ type == 'store' ? '转店' : '转诊' }}</text>
  </view>
</view>

<view wx:if="{{ list.length }}" class="card-wrap">
  <view wx:for="{{ list }}" wx:key="index" class="card" data-is_initiate="{{ item.is_initiate }}" data-id="{{ item.id }}" bindtap="handleCard">
    <!-- 发起方 -->
    <view wx:if="{{ tabMenu.b_index == 0 }}" class="status-tag {{ item.status_obj.class }}">{{ item.status_obj.text }}</view>
    <!-- 接收方 -->
    <image wx:if="{{ tabMenu.b_index == 1 }}" class="status-img" src="{{ oss_img_url }}/image/costomer_transfer/{{ item.status_obj.class }}.png" />
    <view class="info">
      <view class="row name">
        <text class="label">转诊顾客：</text>
        <text class="value name overflow">{{ item.user_name }}</text>
        <image wx:if="{{ item.file_id && tabMenu.b_index == 0 }}" class="file-icon" src="{{ oss_img_url }}/image/costomer_transfer/file_icon.png" data-file_id="{{ item.file_id }}" catchtap="handleCustomerDetail" />
        <image wx:if="{{ item.file_id && item.status == 1 && tabMenu.b_index == 1  }}" class="file-icon" src="{{ oss_img_url }}/image/costomer_transfer/file_icon.png" data-file_id="{{ item.file_id }}" catchtap="handleCustomerDetail" />
      </view>
      <!-- 托管 -->
      <view wx:if="{{ item.old_stores }}" class="row">
        <text class="label">转出门店：</text>
        <text class="value">{{ item.old_stores.fname }}</text>
      </view>
      <view wx:if="{{ item.new_stores }}" class="row">
        <text class="label">接收门店：</text>
        <text class="value">{{ item.new_stores.fname }}</text>
      </view>
      <!-- 转店 -->
      <view wx:if="{{ item.source_store_name }}" class="row">
        <text class="label">转出门店：</text>
        <text class="value">{{ item.source_store_name }}</text>
      </view>
      <view wx:if="{{ item.store_name }}" class="row">
        <text class="label">接收门店：</text>
        <text class="value">{{ item.store_name }}</text>
      </view>
      <view wx:if="{{ item.add_time }}" class="row">
        <text class="label">提交时间：</text>
        <text class="value">{{ item.add_time_text }}</text>
      </view>
      <view wx:if="{{ item.end_time }}" class="row">
        <text class="label">到期时间：</text>
        <text class="value">{{ item.end_time_text }}</text>
      </view>
    </view>
    <view class="btn-wrap">
      <!-- 托管的按钮 -->
      <block wx:if="{{ type == 'trusteeship' }}">
        <!-- 发起方 -->
        <block wx:if="{{ listParams.type == 1 }}">
          <text wx:if="{{ item.status == 0 && module_permissions.hasTrusteeshipDel && item.pay_status != 1 }}" class="btn cancel" data-index="{{ index }}" data-type="cancel" catchtap="handleBtn">取消托管</text>
          <text wx:if="{{ item.status == 1 && module_permissions.hasTrusteeshipCancel }}" class="btn end" data-index="{{ index }}" data-type="end" catchtap="handleBtn">结束托管</text>
        </block>
        <!-- 接收方 -->
        <block wx:if="{{ listParams.type == 2 }}">
          <view class="l-tips">
            <image src="{{ oss_img_url }}/image/costomer_transfer/tips_icon.png" class="tips-icon" />
            <text>临时转诊顾客禁止开发</text>
          </view>
          <!-- <text class="detail">查看详情 ></text> -->
          <text class="detail ">{{ item.status == 0? '去接收' : '查看详情'}}</text>
        </block>
      </block>
      <!-- 转店的按钮 -->
      <block wx:if="{{ type == 'store' }}">
        <!-- 发起方 -->
        <block wx:if="{{ listParams.type == 1 }}">
          <text wx:if="{{ item.status == 0 && module_permissions.hasTransferShopCancel && item.pay_status != 1 }}" class="btn cancel" data-id="{{ item.id }}" data-name="{{ item.user_name }}" catchtap="revokeTransferShop">取消转店</text>
          <text wx:if="{{ item.status === 1 && module_permissions.hasChangeStore && item.service_count > 0 }}" class="btn again" data-id="{{ item.id }}" data-file_id="{{ item.file_id }}" data-num="{{ item.children.length }}" data-type="apply" catchtap="againTransfer">二次转诊</text>
          <text wx:if="{{ module_permissions.hasServiceTrack }}" class="btn track" data-id="{{ item.id }}" catchtap="handleTapTrack">服务追踪</text>
        </block>
        <!-- 接收方 -->
        <block wx:if="{{ listParams.type == 2 }}">
          <!-- <text class="detail">查看详情 ></text> -->
          <text class="detail ">{{ item.status == 0? '去接收' : '查看详情'}}</text>
        </block>
      </block>
    </view>
    <view wx:if="{{ item.children.length }}" class="referral-again">
      <view class="toggle-btn">
        <image src='{{ oss_img_url }}/image/put_away_icon.png' class="{{ item.show ? 'kind-icon-open-top ' : 'kind-icon-open-down' }}" data-index="{{ index }}" catchtap="handleTapChange"></image>
      </view>
      <view class="list {{ item.show ? 'kind-list-item-bd-show' : 'kind-list-item-bd' }}">
        <view class="{{ item.show ? 'transfer-box-show' : 'transfer-box' }}">
          <view wx:for="{{ item.children }}" wx:for-item='children' wx:key="index" class="item" data-id="{{ children.id }}" data-type="detail" catchtap="againTransfer">
            <view class="head">
              <text>再次转诊</text>
              <!-- 发起方 -->
              <view class="status-tag {{ children.status_obj.class }}">{{ children.status_obj.text }}</view>
            </view>
            <view class="time">{{ tabMenu.b_index == 0 ? '提交时间：' : '接收时间：' }}{{ children.add_time_text }}</view>
            <view class="action-box">
              <!-- 发起方 -->
              <block wx:if="{{ listParams.type == 1 }}">
                <view wx:if="{{ module_permissions.hasChangeStoreDel && children.status === 0 }}" class="action-btn cancel" data-name="{{ item.user_name }}" data-id="{{ children.id }}" catchtap="revokeTransferShop">取消转诊</view>
                <view wx:if="{{ module_permissions.hasChangeStore }}" class="action-btn" data-id="{{ children.id }}" data-type="detail" catchtap="againTransfer">详情</view>
              </block>
              <!-- 接收方 -->
              <block wx:else>
                <view wx:if="{{ module_permissions.hasChangeStore }}" class="action-btn detail" data-id="{{ children.id }}" data-type="detail" catchtap="againTransfer">查看详情</view>
                <view wx:if="{{ module_permissions.hasChangeStoreDel && children.status === 0 }}" class="action-btn receive" data-id="{{ children.id }}" data-type="receive" catchtap="againTransfer">去接收</view>
              </block>
            </view>
          </view>
        </view>
      </view>
    </view>
  </view>
</view>
<view wx:else class="empty-wrap">
  <view class="empty-content">
    <image src="{{ oss_img_url }}/image/costomer_transfer/empty_list.png" mode="widthFix" />
    <text>-暂无转诊信息哦-</text>
  </view>
</view>

<!-- 底部切换 -->
<view class="transfer-bottom">
  <view wx:for="{{ tabMenu.list }}" wx:key="index" class="approve-item" data-index="{{ index }}" data-id="{{ item.id }}" bindtap="onBottomTabChange">
    <image src="{{ oss_img_url }}{{ tabMenu.b_index == index ? item.sele_icon : item.icon }}"></image>
    <text class="{{ tabMenu.b_index == index ? 'active' : '' }}">{{ item.name }}</text>
  </view>
</view>

<water-mark />
```



```js
// 转诊列表
import {
  getTrusteeshipList,
  stopTrusteeship,
  delTrusteeship,
  getTransferShop,
  revokeTransferShop
} from '../../../api/transfer'
import { formatTimeTwo, getStorageData, checkPageNum, Message } from '../../..//utils/util'

const app = getApp()
const { oss_img_url } = app

// 托管状态
const statusTextObj = {
  '0': '待确认', '1': '转诊中', '2': '已结束', '3': '已终止', '4': '已拒绝'
}
const statusClassObj = {
  '0': 'wait', '1': 'ing', '2': 'end', '3': 'stop', '4': 'refused'
}
// 转店状态
const statusTextObjByStore = {
  '0': '待确认', '1': '已同意', '2': '已拒绝'
}
const statusClassObjByStore = {
  '0': 'wait', '1': 'confirmed', '2': 'refused'
}
// tab 菜单
const tabMenu = {
  // 转店
  'store': [
    {
      id: 1,
      name: '转店方',
      statusList: [{ label: '全部', value: -2 }, { label: '待确认', value: 0 }, { label: '已同意', value: 1 }, { label: '已拒绝', value: 2 }],
      icon: '/image/costomer_transfer/store.png',
      sele_icon: '/image/costomer_transfer/store_active.png'
    },
    {
      id: 2,
      name: '接收方',
      statusList: [{ label: '全部', value: -2 }, { label: '待确认', value: 0 }, { label: '已同意', value: 1 }, { label: '已拒绝', value: 2 }],
      icon: '/image/costomer_transfer/receive.png',
      sele_icon: '/image/costomer_transfer/receive_active.png'
    }
  ],
  // 托管
  'trusteeship': [
    {
      id: 1,
      name: '转诊方',
      statusList: [{ label: '全部', value: -1 }, { label: '待确认', value: 0 }, { label: '转诊中', value: 1 }, { label: '已结束', value: 2 }, { label: '已终止', value: 3 }, { label: '已拒绝', value: 4 }],
      icon: '/image/costomer_transfer/trusteeship.png',
      sele_icon: '/image/costomer_transfer/trusteeship_active.png'
    },
    {
      id: 2,
      name: '接收方',
      statusList: [{ label: '全部', value: -1 }, { label: '待确认', value: 0 }, { label: '转诊中', value: 1 }, { label: '已结束', value: 2 }, { label: '已终止', value: 3 }, { label: '已拒绝', value: 4 }],
      icon: '/image/costomer_transfer/receive.png',
      sele_icon: '/image/costomer_transfer/receive_active.png'
    }
  ]
}

Page({
  data: {
    oss_img_url,
    // 转诊类型 store->转店转诊  trusteeship->托管转诊
    type: '',
    sysUserInfo: {},
    tabMenu: {
      // 顶部状态栏下标
      t_index: 0,
      // 底部类型栏下标
      b_index: 0,
      list: []
    },
    // 转店弹窗
    popup: {
      // 同意modal
      agree_show: false
    },
    // 同意，拒绝转店提交参数
    storeParmas: {
      id: '',
      status: '',
      type: ''
    },
    listParams: {
      page: 1,
      pagesize: 10,
      keywords: '',
      // 1发起方  2接收方
      type: 1,
      // store_ids: []
    },
    list: []
  },
  async onLoad(o) {
    // 设置权限
    app.setUserPermissions(this, {
      // 新增托管
      hasTrusteeshipAdd: ['api/trusteeship/add'],
      // 取消（删除）托管
      hasTrusteeshipDel: ['api/trusteeship/del'],
      // 终止（结束）托管
      hasTrusteeshipCancel: ['api/trusteeship/cancel'],
      // 支付托管
      hasTrusteeshipPay: ['api/trusteeship/pay'],
      // 取消转店
      hasTransferShopCancel: ['api/changestore/delrecord'],
      // 新增转店
      hasChangeStoreAdd: ['api/customer/changestore'],
      // 支付转店
      hasChangeStorePay: ['api/trusteeship/cancel'],
      // 二次转诊
      hasChangeStore: ['api/customer/changestore'],
      // 取消转诊按钮
      hasChangeStoreDel: ['api/changestore/delrecord'],
      // 添加二次转诊
      hasAddRepeat: ['api/changestore/add_repeat'],
      // 服务追踪
      hasServiceTrack: ['api/changestore/get/track/list']
    })

    const { type = 'trusteeship' } = o
    // 设置tab
    const tabList = tabMenu[type]
    // 设置标题
    const title = {
      'store': '转店转诊',
      'trusteeship': '临时转诊'
    }
    wx.setNavigationBarTitle({ title: title[type] })

    // 是否多选门店权限
    const isManager = await app.IsMultipleStore()
    // if (isManager) {
    //   const storeIds = await app.getStorageDefaultIds()
    //   this.setData({
    //     'listParams.store_ids': storeIds
    //   })
    // }
    // 获取用户信息
    const sysUserInfo = await getStorageData('sys_user_info')

    this.setData({
      type, isManager, sysUserInfo,
      'tabMenu.list': tabList,
      'listParams.status': tabList[this.data.tabMenu.b_index].statusList[this.data.tabMenu.t_index].value
    }, () => {
      this.init()
    })
  },
  // 初始化页面
  init() {
    this.page = 1
    this.pagesize = 10
    this.total = 0
    Object.assign(this.data.listParams, { page: this.page, pagesize: this.pagesize })
    this.setData({ list: [] }, () => {
      this.getList()
    })
  },
  // 获取列表
  getList() {
    switch (this.data.type) {
      case 'store':
        this.getTransferShop()
        break
      case 'trusteeship':
        this.getTrusteeshipList()
        break
    }
  },
  // 底部 tab 切换
  onBottomTabChange(e) {
    const tabIdx = this.data.tabMenu.b_index
    const { index, id } = e.currentTarget.dataset
    if (index == tabIdx) return
    this.setData({
      'listParams.status': this.data.tabMenu.list[tabIdx].statusList[0].value,
      'tabMenu.t_index': 0,
      'listParams.type': id,
      'tabMenu.b_index': index
    }, () => {
      wx.pageScrollTo({
        scrollTop: 0
      })
      this.init()
    })
  },
  // 顶部 tab 切换
  onTopTabChange(e) {
    const tabIdx = this.data.tabMenu.t_index
    const { index, id } = e.currentTarget.dataset
    if(tabIdx == index) return
    this.setData({
      'listParams.status': id,
      'tabMenu.t_index': index
    }, () => {
      this.init()
    })
  },
  // 获取托管列表
  async getTrusteeshipList() {
    let { data: { list, total }} = await getTrusteeshipList(this.data.listParams)
    this.total = total
    const { isManager, sysUserInfo } = this.data
    let storeIds = []
    if (isManager) {
      storeIds = sysUserInfo.stores.map(v => v.id)
    } else {
      storeIds = [sysUserInfo.store_id]
    }
    list.map(v => {
      // 处理状态
      v.status_obj = {
        text: statusTextObj[v.status],
        class: statusClassObj[v.status]
      }
      // 处理门店
      v.old_stores.fname = `${v.old_stores.province_name}-${v.old_stores.city_name}-${v.old_stores.name}`
      v.new_stores.fname = `${v.new_stores.province_name}-${v.new_stores.city_name}-${v.new_stores.name}`
      // 处理时间
      v.add_time_text = formatTimeTwo(v.add_time, 'Y-M-D h:m:s')
      v.end_time_text = formatTimeTwo(v.end_time, 'Y-M-D h:m:s')
      // 是否为发起方
      v.is_initiate = storeIds.includes(v.old_store_id || 0)
      // 是否为接收方
      v.is_receive = storeIds.includes(v.new_store_id || 0)
    })

    this.setData({ list: [...this.data.list, ...list] })
  },
	// 获取转店记录列表
	async getTransferShop() {
    // 传入 is_new 用于区别旧转店 1新 0旧
    const res = await getTransferShop({ ...this.data.listParams, is_new: 1 })
    const { list, total, childs } = res.data
		if (!list.length) {
			this.setData({
				listData: []
			})
			return
		}
		this.total = total
		list.map(v => {
      // 处理状态
      v.status_obj = {
        text: statusTextObjByStore[v.status],
        class: statusClassObjByStore[v.status]
      }
			v.user_sex = v.user_sex == null ? 2 : v.user_sex
      v.add_time_text = formatTimeTwo(v.add_time, 'Y-M-D h:m')
      v.show = true
      //  整理成树形结构
      let arr = childs.filter(val => {
        if (val.origin_id === v.id) {
          // 处理状态
          val.status_obj = {
            text: statusTextObjByStore[val.status],
            class: statusClassObjByStore[val.status]
          }
          val.add_time_text = formatTimeTwo(val.add_time, 'Y-M-D h:m')
        }

        return val.origin_id === v.id
      })
      v.children = arr || []
		})
    this.setData({ list: [...this.data.list, ...list] })
  },
  // 结束托管
  async stopTrusteeship(id) {
    const res = await stopTrusteeship({ id })
    Message.warns(res.msg)
    setTimeout(() => {
      this.init()
    }, 900)
  },
  // 删除托管（取消托管的时候调用）
  async delTrusteeship(id) {
    const res = await delTrusteeship({ id })
    Message.warns(res.msg)
    setTimeout(() => {
      this.init()
    }, 900)
  },
  // 撤销转店
  async revokeTransferShop(e) {
    const { name, id } = e.currentTarget.dataset
    Message.modal(`是否撤销顾客【${name}】的转店申请？`)
      .then(async bol => {
        if (bol) {
          const res = await revokeTransferShop({ id })
          Message.warn(res.msg).then(() => {
            this.init()
          })
        }
      })
  },
  // 获取多选门店返回参数
  getMultiStore(val) {
    this.setData({
      'listParams.store_ids': val.selIds
    })
    this.init()
  },
  // 搜索输入
  handleSearchChange(e) {
    this.setData({
      'listParams.keywords': e.detail.value
    })
  },
  // 点击新增转诊
  handleAddTransfer() {
    if ((this.data.type == 'store' && !this.data.module_permissions.hasChangeStoreAdd && !this.data.module_permissions.hasChangeStore) || (this.data.type == 'trusteeship' && !this.data.module_permissions.hasTrusteeshipAdd)) {
      Message.warns('暂无权限')
      return
    }
    wx.navigateTo({
      url: `/package_one/pages/customer-transfer/agreement/agreement?type=${this.data.type}`
    })
  },
  // 点击转诊卡片
  handleCard(e) {
    const type = this.data.type
    const { id } = e.currentTarget.dataset
    // 接收方 进入详情
    if (this.data.listParams.type != 1) {
      wx.navigateTo({
        url: `/package_one/pages/customer-transfer/detail/detail?type=${type}&id=${id}`
      })
      return
    }
    // 发起方 进入操作页面
    // 权限判断
    const { hasChangeStoreAdd, hasChangeStorePay, hasTrusteeshipAdd, hasTrusteeshipConfirm, hasTrusteeshipPay } = this.data.module_permissions
    if ((type == 'store' && !(hasChangeStoreAdd || hasChangeStorePay)) || (type == 'trusteeship' && !(hasTrusteeshipAdd || hasTrusteeshipConfirm || hasTrusteeshipPay))) {
      Message.warns('无此权限')
      return
    }

    wx.navigateTo({ url: `/package_one/pages/customer-transfer/operate/operate?type=${type}&id=${id}` })
  },
  // 点击查看顾客详情
  handleCustomerDetail(e) {
    const { file_id } = e.currentTarget.dataset
    wx.navigateTo({ url: `/pages/customers/detail/detail?file_id=${file_id}` })
  },
  // 点击按钮
  handleBtn(e) {
    const { index, type } = e.currentTarget.dataset
    const item = this.data.list[index]
    const { user_name, id } = item
    const typeText = {
      'cancel': '取消',
      'end': '结束'
    }
    const msg = `是否确定【${typeText[type]}】顾客【${user_name}】的托管`
    Message.modal(msg, {
      confirmText: '取消',
      cancelColor: '#46A1FF',
      cancelText: '确定',
      confirmColor: '#AEB1BB'
    })
      .then(res => {
        if (!res) {
          switch (type) {
            case 'cancel':
              // 调用删除
              this.delTrusteeship(id)
              break
            case 'end':
              // 调用终止
              this.stopTrusteeship(id)
              break
          }
        }
      })
  },
  // 单个折叠
  handleTapChange(e){
    let { index } = e.currentTarget.dataset
    let list = this.data.list
    list[index].show = !list[index].show
    this.setData({
      list
    })
  },
  // 再次转诊
  againTransfer(e) {
    const { id, file_id, type, num } = e.currentTarget.dataset
    // 接收方 进入详情
    if (this.data.listParams.type != 1) {
      wx.navigateTo({ url: `/package_one/pages/customer-transfer/again-detail/again-detail?type=${type}&id=${id}`})
      return
    }
    // 发起方 进入操作页面
    // 权限判断
    const { hasAddRepeat } = this.data.module_permissions
    if (!hasAddRepeat) {
      Message.warns('无此权限')
      return
    }

    wx.navigateTo({ url: `/package_one/pages/customer-transfer/again/again?file_id=${file_id}&id=${id}&type=${type}&num=${num + 1}` })
  },
  handleTapTrack(e) {
    const { id } = e.currentTarget.dataset
    const { hasServiceTrack } = this.data.module_permissions
    if (!hasServiceTrack) {
      Message.warns('暂无服务追踪权限')
      return
    }
    wx.navigateTo({ url: `/package_three/pages/service-track/service-track?id=${id}` })
  },
  // 上拉加载
  onReachBottom() {
    this.page++
    if (checkPageNum(this.page, this.pagesize, this.total)) {
      return
    }
    // 更新页数
    Object.assign(this.data.listParams, { page: this.page })
    this.getList()
  },
  // 下拉刷新
  onPullDownRefresh() {
    this.init()
    wx.stopPullDownRefresh()
  }
})
```



## 服务追踪

![img](https://cdn.nlark.com/yuque/0/2022/png/1614731/1660490739840-0c3f71c3-0e82-40a3-ba35-c304a8b2fef5.png)

```html
<!--package_three/pages/customer-transfer/service-track.wxml-->
<view id="service-track-page" class="service-track-wrapper">
  <view class="user-wrapper">
    <view class="image-wrapper">
      <image wx:if="{{ fileInfo.sex === 0 }}" class="user-avatar" src="{{ fileInfo.head_pic ? fileInfo.head_pic : oss_img_url + '/image/user-girl.png' }}"></image>
      <image wx:if="{{ fileInfo.sex === 1 }}" class="user-avatar" src="{{ fileInfo.head_pic ? fileInfo.head_pic : oss_img_url + '/image/user-boy.png' }}"></image>
      <image wx:if="{{ fileInfo.sex > 1 }}" class="user-avatar" src="{{ fileInfo.head_pic ? fileInfo.head_pic : oss_img_url + '/image/user-no-sex.png' }}"></image>
    </view>
    <view class="info-wrapper">
      <view> 
        <text class="info-name">{{ fileInfo.name }}</text>
        <image class="file-icon" src="{{ oss_img_url }}/image/costomer_transfer/file_icon.png" data-file_id="{{ fileInfo.id}}" catchtap="handleCustomerDetail" />
      </view>
      <text class="info-text">{{ fileInfo.phone }}</text>
      <text class="info-text">{{ fileInfo.city_name }}-{{fileInfo.store_name}}</text>
    </view>
  </view>
  <view class="service-title-wrapper">
    <view class="blue-border"></view>
    <text class="track-title">追踪记录</text>
    <text class="init-text">初始消费服务追踪记录</text>
    <!-- 名称释义组件 -->
    <floating-noun tool-tip-class="tool-tip-class" noun-tip-class="noun-tip-class" noun-sing="WXCRM0024"></floating-noun>
  </view>
  <view wx:if="{{ list.length }}" class="list-main">
    <view class="time-line">
      <!-- 线 -->
      <view class="line" style="{{ lineStyle }}"></view>
      <view wx:for="{{ list }}" wx:for-index="timelineIdx" wx:for-item="timelineItem" wx:key="index" class="item">
        <!-- 内容 -->
        <view class="content">
          <view class="info-box">
            <view class="info-header">
              <view class="round {{ timelineIdx === 0 ? 'first' : '' }}" id="round{{ timelineIdx }}"></view>
              <view class="time">{{ timelineItem.add_time }}</view>
              <text class="service-type" wx:if="{{ timelineIdx < list.length -1 }}">转诊服务</text>
              <text class="service-type" wx:else>原服务</text>
            </view>
            <view class="info-content">
              <text class="info-store">{{ timelineItem.city_name }}-{{ timelineItem.store_name }}</text>
              <view wx:for="{{ timelineItem.list }}" wx:key="index" class="info-service {{ item.status > 0 ? 'endBackground' : ''}}">
                <view class="card-row">
                 <text class="card-name {{ item.status > 0 ? 'endText' : ''}}">{{ item.card_name }}</text>
                 <text class="card-detail" data-file-info="{{ fileInfo }}" data-file-id="{{ item.file_id }}" data-id="{{ item.id }}" data-status="{{ item.status }}" catchtap="handleServiceDetail">详情></text>
                </view>
                <view class="card-row">
                  <text class="card-num">共{{ item.total_num }}次/已用{{ item.use_num }}次/剩余{{ item.total_num >= item.use_num ? item.total_num - item.use_num : 0 }}次</text>
                  <view class="card-status {{ item.status > 0 ? 'endStatus' : ''}}">
                    <block wx:if="{{ timelineIdx < list.length - 1 && item.status === 0 }}">进行中(转诊)</block>
                    <block wx:if="{{ timelineIdx < list.length - 1 && item.status === 1 }}">已结束</block>
                    <block wx:if="{{ timelineIdx < list.length - 1 && item.status === 2 }}">已结束(转诊)</block>
                    <block wx:if="{{ timelineIdx === list.length - 1 && item.status === 0 }}">进行中</block>
                    <block wx:if="{{ timelineIdx === list.length - 1 && item.status === 1 }}">已结束</block>
                    <block wx:if="{{ timelineIdx === list.length - 1 && item.status === 2 }}">已结束(转诊)</block>
                  </view>
                </view>
                <view class="card-row">
                  <text class="buy-time">购买时间：{{ item.add_time }}</text>
                  <text class="end-time">结束时间：{{ item.expire_time }}</text>
                </view>
              </view>
            </view>
          </view>
        </view>
      </view>
    </view>
  </view>
  <view wx:else class="not-empty">暂无数据</view>
</view>
```



```css
/* package_three/pages/customer-transfer/service-track.wxss */
.service-track-wrapper {
  background: #eff2f9;
}

.user-wrapper {
  display: flex;
  justify-content: flex-start;
  background: #FFFFFF;
}

.image-wrapper {
  padding: 30rpx 0 30rpx 30rpx;
  font-size: 0;
}

.user-avatar {
  width: 100rpx;
  height: 100rpx;
  border-radius: 50%;
}

.info-wrapper {
  display: flex;
  flex-direction: column;
  justify-content: center;  
  margin-left: 9rpx;
}

.info-name {
  font-size: 30rpx;
  color: #000;
}

.file-icon {
  width: 30rpx;
  height: 30rpx;
  padding: 0 10rpx;
}

.info-text {
  font-size: 24rpx;
  color: #606266;
}

.service-title-wrapper {
  display: flex;
  justify-content: flex-start;
  align-items: center;
  padding: 30rpx 0 24rpx 30rpx;
  background: #EFF2F9;
}

.blue-border {
  width: 8rpx;
  height: 36rpx;
  background: #316DFC;
  border-radius: 0px 5px 5px 0px;
}

.track-title {
  font-size: 30rpx;
  color: #000;
  padding-left: 8rpx;
}

.init-text {
  font-size: 24rpx;
  color: #868FAE;
  padding-left: 20rpx;
}

.service-title-wrapper .tool-tip-class {
  position: static;
  bottom: 0;
  right: 0;
}

.service-title-wrapper .tool-tip-class .noun-tip-class {
  width: 24rpx;
  height: 24rpx;
  font-size: 20rpx;
  line-height: 24rpx;
  margin-left: 10rpx;
  color: #fff;
  background: #868FAE;
  border: 2rpx solid #868FAE;
  border-radius: 50%;
}

/* 时间线 */
.time-line{
  position: relative;
}

/* 线 */
.time-line .line {
  position: absolute;
  left: 38rpx;
  top: 42rpx;
  width: 2rpx;
  background-color: #a5acba;
}

.time-line .item {
  padding: 30rpx 17rpx 10rpx 30rpx;
  background-color: #fff;
}

.time-line .item:last-child {
  padding-bottom: 30rpx;
}

.item .content{
  color: #000;
}

/* 圆 */
.content .round {
  position: relative;
  width: 20rpx;
  height: 20rpx;
  margin-right: 10rpx;
  background-color: #a5a9b7;
  border-radius: 50%;
}

.content .round.first {
  background: #2364ff;
}

.content .round.first::after{
  position: absolute;
  content: '';
  top: -4px; 
  right: -4px;
  bottom: -4px;
  left: -4px;
  border-radius: 50%;
  background: #EBF6FB;
  z-index: -1;
}

.info-box{
  color: #4e535d;
}

.info-header {
  display: flex;
  align-items: center;
}

.info-header .time {
  font-size: 28rpx;
  color: #231815;
}

.info-header .service-type {
  font-size: 28rpx;
  margin-left: 10rpx;
  color: #231815;
}

.info-content {
  padding-left: 30rpx;
}

.info-content .info-store {
  display: block;
  font-size: 28rpx;
  line-height: 28rpx;
  color: #000;
  margin: 6rpx 0 16rpx 0;
}

.info-service {
  padding: 16rpx 20rpx;
  background: #F0FCF8;
  border-radius: 10rpx;
  margin-bottom: 10rpx;
}

.info-content .info-service:last-child {
  margin-bottom: 0;
}

.card-row {
  display: flex;
  justify-content: space-between;
  margin-bottom: 10rpx;
}

.card-row:last-child {
  margin-bottom: 0;
}

.card-name {
  font-size: 28rpx;
  color: #36B897;
  font-weight: bold;
}

.card-detail {
  font-size: 24rpx;
  color: #57A5FF;
}

.card-num {
  font-size: 24rpx;
  color: #43484D;
}

.card-status {
  padding: 8rpx 14rpx;
  background: #36B897;
  border-radius: 20rpx;
  font-size: 20rpx;
  line-height: 20rpx;
  font-weight: 400;
  color: #fff;
}

.buy-time, .end-time {
  font-size: 24rpx;
  color: #43484D;
}

.buy-time {
  margin-right: 84rpx;
}

.endBackground {
  background: #EAEEFF;
}

.endText {
  color: #43484D
}

.endStatus {
  background: #A3AEC1;
}
```



```js
// package_three/pages/customer-transfer/service-track.js
import { getTrackList } from '../../../api/transfer'
import { Message, formatTimeTwo } from '../../../utils/util'

const app = getApp()
const { oss_img_url } = app

Page({
  /**
   * 页面的初始数据
   */
  data: {
    oss_img_url,
    listParams: {
      page: 1,
      pagesize: 10,
      change_id: 0
    },
    fileInfo: {},
    list: [],
    lineStyle: ''
  },

  /**
   * 生命周期函数--监听页面加载
   */
  async onLoad(o) {
    // 功能模块权限判断
    app.setUserPermissions(this, {
      // 服务卡核销详情
      hasCustomerServiceDetail: ['api/service/userserviceshistory']
    })
    const { id } = o
    this.setData({
      'listParams.change_id': id
    })
    this.initPage()
  },

  initPage() {
    this.page = 1
    this.pagesize = 10
    this.isNoMore = false
    // 更新页数
    Object.assign(this.data.listParams, { page: this.page, pagesize: this.pagesize })
    this.getTrackList()
  },

  async getTrackList() {
    try {
      const res = await getTrackList(this.data.listParams)
      const { file_info, list } = res.data
      // 判断有无更多
      if (this.page > 1 && !list.length ) {
        this.isNoMore = !list.length
        Message.warns('暂无更多数据')
        return
      }
      list.forEach(item => {
        if (item.add_time > 0) {
          item.add_time = formatTimeTwo(item.add_time, 'Y.M.D h:m')
        }
        item.list.forEach(item => {
          item.add_time = formatTimeTwo(item.add_time, 'Y.M.D')
          if (item.expire_time > 0) {
            item.expire_time = formatTimeTwo(item.expire_time, 'Y.M.D')
          } else if (item.expire_time === -1) {
            item.expire_time = '终生'
          } else {
            item.expire_time = '0'
          }
          if (item.total_num < 0) {
            item.total_num = 0
          }
          if (item.use_num < 0) {
            item.use_num = 0
          }
        })
      })
      list.reverse()
      this.setData({
        fileInfo: file_info,
        list: this.data.list.concat(list),
      }, () => {
        this.initLine()
      })
    }
    catch(e) {
      console.warn('获取转诊服务追踪记录出错', e)
    }
  },
  // 初始化时间线
  async initLine() {
	const length = this.data.list.length
	// 只有一条记录，则无需画线
	if (length === 1) {
	  return
	}
	// 第一个圆的位置
	const firstRound = await new Promise((reslove) => {
	  wx.createSelectorQuery().select(`#round0`)
	    .boundingClientRect()
		.exec((res) => {
		  if (res.length) { reslove(res[0]) }
		});
	  })
	// 最后一个圆位置
	const lastRound = await new Promise((reslove) => {
	  wx.createSelectorQuery()
		.select(`#round${length - 1}`)
		.boundingClientRect()
		.exec((res) => {
		  if (res.length) { reslove(res[0]) }
		});
    })
    if (lastRound.top && firstRound.top) {
      // 计算线的长度
	 this.setData({ lineStyle: `height:${lastRound.top - firstRound.top}px` })
    }
  },
  // 点击跳转扣卡详情
  handleServiceDetail(e) {
    if (!this.data.module_permissions.hasCustomerServiceDetail) {
      Message.warns('暂无服务追踪详情权限')
      return
    }
    const { fileInfo, fileId, id, status } = e.currentTarget.dataset
    wx.navigateTo({ url: `/package_three/pages/service-track-detail/service-track-detail?city_name=${fileInfo.city_name}&store_name=${fileInfo.store_name}&file_id=${fileId}&id=${id}&status=${status}` })
  },

  // 点击查看顾客详情
  handleCustomerDetail(e) {
    const { file_id } = e.currentTarget.dataset
    wx.navigateTo({ url: `/pages/customers/detail/detail?file_id=${file_id}` })
  },

  // 上拉加载
  onReachBottom() {
    if (this.isNoMore) {
      return
    }
    this.page++;
    // 更新页数
    Object.assign(this.data.listParams, { page: this.page })
    this.getTrackList()
  },
  // 下拉刷新
  onPullDownRefresh() {
    this.setData({
      list: []
    })
    this.initPage()
    wx.stopPullDownRefresh()
  }
})
```



```json
{
  "navigationBarTitleText": "服务追踪",
  "enablePullDownRefresh": true,
  "usingComponents": {
    "floating-noun": "/components/floating-noun/floating-noun"
  }
}
```

## 扣卡详情

![img](https://cdn.nlark.com/yuque/0/2022/png/1614731/1660490739833-d79398ea-d87b-4a3a-b152-c1cfdabea713.png)



```html
<!--package_three/pages/customer-transfer/service-track-detail/service-track-detail.wxml-->
<wxs module="filters" src="../../../filter.wxs" />
<view id="service-track-detail" class="track-detail-wrapper">
  <view class="user-wrapper">
    <view class="image-wrap">
      <image wx:if="{{ user_info.sex === 0 }}" class="user-avatar" src="{{ user_info.head_pic ? user_info.head_pic : oss_img_url + '/image/user-girl.png' }}"></image>
      <image wx:if="{{ user_info.sex === 1 }}" class="user-avatar" src="{{ user_info.head_pic ? user_info.head_pic : oss_img_url + '/image/user-boy.png' }}"></image>
      <image wx:if="{{ user_info.sex > 1 }}" class="user-avatar" src="{{ user_info.head_pic ? user_info.head_pic : oss_img_url + '/image/user-no-sex.png' }}"></image>
    </view>
    <view class="info-wrapper">
      <view> 
        <text class="info-name">{{ user_info.name }}</text>
        <image class="file-icon" src="{{ oss_img_url }}/image/costomer_transfer/file_icon.png" />
      </view>
      <text class="info-text">{{ user_info.phone }}</text>
      <text class="info-text">{{ city_name }}-{{ store_name }}</text>
    </view>
  </view>
  <view class="service-inprogress-card">
    <view class="card-info">
      <view class="card-left">
        <text class="card-text">{{ card_info.card_name }}</text>
        <text class="card-text">{{ card_info.total_num_text }}</text>
        <text class="card-time">购买时间: {{ card_info.add_time }}</text>
      </view>
      <view class="card-right">
        <view class="image-wrapper">
          <image src="{{ oss_img_url }}/image/service-track-card.png"></image>   
          <text class="left-num">{{ card_info.total_num - card_info.use_num > 0 ? card_info.total_num - card_info.use_num : 0  }}次</text>
          <text class="left-num-text">剩余次数</text>
          <view wx:if="{{ status === '0' }}" class="progress-status">
		   <view class="progress-text">进行中</view>
		 </view>
        </view>
      </view>
    </view>
  </view>
  <view class="card-record-wrapper" wx:if="{{ historys.length }}">
    <view class="card-record-title" >
      <view class="blue-border"></view>
      <text class="deduct-title">扣卡记录（{{ historys.length > 0 ? historys.length : 0 }}）</text>
    </view>
    <view class="card-history-wrapper">
      <view class="card-history-item" wx:for="{{ historys }}" wx:key="index">
        <view class="history-info-left">
          <view class="union">
            <view class="service-wrapper">
              <text class="justify-text">美容师</text>：{{ item.service_name }}
            </view>
            <view class="sign-wrapper">
              <text class="justify-sign">签名</text>
              <text class="colon">:</text>
              <view wx:if="{{ item.checked_img }}" class="sign-img-wrapper">
                <image src="{{ oss_url + item.checked_img }}" class="sign-img"></image>
              </view>
              <text wx:else>-</text>
            </view>
            <text class="use-time">使用时间<text class="use-colon">:</text>{{ item.add_time }}</text>
          </view>
        </view>
        <view class="history-info-right">
           <view class="num-wrapper">
             <text class="deduct-num">-{{ item.num }}次</text>
           </view>
           <view class="money-time">
             <text class="cost-money">卡耗金额：￥{{ filters.keepDecimal(item.cost_money) }}</text>
             <text class="operat-time">操作时间：{{ item.operat_time }}</text>
           </view>
        </view>
      </view>
      <view class="not-more">-暂无更多啦-</view>
    </view>
  </view>
  <view wx:else class="not-empty">暂无数据</view>
</view>
```



```js
// package_three/pages/customer-transfer/service-track-detail/service-track-detail.js
import { getCustomerCardHistory } from '../../../api/customers.js'
import { formatTimeTwo, checkPageNum } from '../../../utils/util'
const app = getApp();
const { oss_url, oss_img_url } = app

Page({
  /**
   * 页面的初始数据
   */
  data: {
    oss_url,
    oss_img_url,
    listParams: {
      page: 1,
      pagesize: 10,
      file_id: 0,
      id: 0
    },
    service_info: {},
    user_info: {}, // 服务列表信息
    card_info: {}, // 卡项信息
    historys: [], // 卡核销记录
    // 当前时间戳、主要用于服务有效期判断、服务是否已过期
    today_time: parseInt(new Date().getTime() / 1000),
    city_name: '',
    store_name: '',    
    status: ''
  },

  /**
   * 生命周期函数--监听页面加载
   */
  onLoad(o) {
    const { city_name, store_name, file_id, id, status } = o
    this.setData({
      city_name: decodeURIComponent(city_name),
      store_name: decodeURIComponent(store_name),
      status
    })
    Object.assign(this.data.listParams, { file_id, id })
    this.initPage()
  },

  initPage() {
    this.page = 1;
    this.pagesize = 10
    this.totalCount = 1;
    // 更新页数
    Object.assign(this.data.listParams, { page: this.page, pagesize: this.pagesize })
    this.getCustomerCardHistory()
  },

 // 获取顾客档案服务记录卡的核销历史
 async getCustomerCardHistory() {
  const params = this.data.listParams
  try {
    const res = await getCustomerCardHistory(params)
    let { card_info, historys, total, service_info, user_info } = res.data
  
    this.totalCount = total
    // 数据清洗
    // 使用历史会有空的情况
    historys = historys ? historys : [];
    // 时间戳转换
    card_info.add_time = formatTimeTwo(card_info.add_time, 'Y.M.D h:m');
    // 处理服务卡项过期时间
    card_info.expire_time = card_info.expire_time > 0 ? formatTimeTwo(card_info.expire_time, 'Y.M.D h:m') : ''
  
    if (historys && historys.length) {
      historys.map(v => {
        v.add_time = formatTimeTwo(v.add_time, 'Y.M.D')
        v.operat_time = formatTimeTwo(v.operat_time, 'Y.M.D h:m');
      })
    }
  
    // 总次数-1判断 追加自定义字段
    // residue: 剩余次数
    if (card_info.trusteeship_num == -1) {
      card_info.residue = -1;
      card_info.total_num_text = '无限次'
    } else {
      // 剩余次数 = 托管次数
      card_info.residue = Number(card_info.trusteeship_num)
      card_info.total_num = card_info.total_num > 0 ? card_info.total_num : 0 
      card_info.total_num_text = `共${ card_info.total_num }次`
    }
  
    this.setData({
      service_info,
      card_info,
      user_info,
      historys: this.data.historys.concat(historys)
    })
  } catch(e) {
    console.warn('获取顾客档案服务记录卡的核销历史出错', e)
  }
},

  /**
   * 生命周期函数--监听页面初次渲染完成
   */
  onReady() {

  },

  /**
   * 生命周期函数--监听页面显示
   */
  onShow() {

  },

  /**
   * 生命周期函数--监听页面隐藏
   */
  onHide() {

  },

  /**
   * 生命周期函数--监听页面卸载
   */
  onUnload() {

  },

  // 上拉加载
  onReachBottom() {
    this.page++;
    if (checkPageNum(this.page, this.pagesize, this.totalCount)) {
      return
    }
    // 更新页数
    Object.assign(this.data.listParams, { page: this.page })
    this.getCustomerCardHistory()
  },
  // 下拉刷新
  onPullDownRefresh() {
    this.setData({
      historys: []
    })
    this.initPage()
    wx.stopPullDownRefresh()
  },

  /**
   * 用户点击右上角分享
   */
  onShareAppMessage() {

  }
})
```



```css
/* package_three/pages/customer-transfer/service-track-detail/service-track-detail.wxss */
.track-detail-wrapper {
  background: #eff2f9;
}

.user-wrapper {
  display: flex;
  justify-content: flex-start;
  background: #FFFFFF;
}

.image-wrap {
  padding: 30rpx 0 40rpx 30rpx;
  font-size: 0;
}

.user-avatar {
  width: 100rpx;
  height: 100rpx;
  border-radius: 50%;
}

.info-wrapper {
  display: flex;
  flex-direction: column;
  justify-content: center;  
  margin-left: 9rpx;
}

.info-name {
  font-size: 30rpx;
  color: #000;
}

.file-icon {
  width: 30rpx;
  height: 30rpx;
  padding: 0 10rpx;
}

.info-text {
  font-size: 24rpx;
  color: #606266;
}

.service-inprogress-card {
  padding: 0 30rpx 30rpx 30rpx;
  background: #fff;
}

.service-inprogress-card .card-info {
  padding: 10rpx 10rpx 10rpx 30rpx;
  border-radius: 10rpx;
  background: linear-gradient(90deg, #527AFF 0%, #5D9DFF 100%);
  box-shadow: 0rpx 8rpx 16rpx 1rpx rgba(78,131,252,0.2000);
}

.card-info {
  display: flex;
  justify-content: space-between;
}

.card-left {
  display: flex;
  flex-direction: column;
  padding-top: 20rpx;
}

.card-text {
  display: block;
  margin-top: 10rpx;
  font-size: 28rpx;
  color: #fff;
}

.card-time {
  margin-top: 10rpx;
  font-size: 24rpx;
  color: #A0C2FF;
}

.card-right {
  position: relative;
}

.image-wrapper {
  font-size: 0;
}

.image-wrapper image {
  width: 210rpx;
  height: 180rpx;
}

.left-num {
  position: absolute;
  font-size: 40rpx;
  color: #57A5FF;
  left: 86rpx;
  top: 48rpx;
}

.left-num-text {
  position: absolute;
  left: 78rpx;
  bottom: 48rpx;
  font-size: 20rpx;
  font-weight: 400;
  color: #6F7A97;
}


.progress-status {
  position: absolute;
  top: 0;
  right: 0;
  width: 100rpx;
  height: 100rpx;
  overflow: hidden;
}

.progress-text {
  width: 150rpx;
  padding: 8rpx 0 8rpx 4rpx;
  font-size: 18rpx;
  text-align: center;
  background: #1AC179;
  color: white;
  transform: rotate(45deg);
}

.card-record-wrapper {
  position: relative;
  margin-top: 20rpx;
  padding: 30rpx 0 0 30rpx;
  background: #fff;
}

.card-record-title {
  display: flex;
  justify-content: flex-start;
  align-items: center;
  padding-bottom: 32rpx;
}

.card-record-title::after {
  content:"";
  position: absolute;
  width: 100%;
  top: 88rpx; 
  left: 0;
  transform-origin: 0 bottom;
  border-bottom: 2rpx solid #eee;
}

.blue-border {
  width: 8rpx;
  height: 36rpx;
  background: #316DFC;
  border-radius: 0rpx 5rpx 5rpx 0rpx;
}

.deduct-title {
  margin-left: 8rpx;
  font-size: 30rpx;
  font-weight: bold;
}

.card-history-wrapper {
  padding: 20rpx 30rpx 158rpx 0;
  background: #fff;
}

.card-history-item {
  display: flex;
  justify-content: space-between;
  border-bottom: 2rpx solid #EEE;
  margin-bottom: 20rpx;
}

.history-info-left {
  padding-bottom: 20rpx;
}

.union {
  font-size: 28rpx;
  font-weight: 400;
  color: #313131;
}

.sign-wrapper {
  display: flex;
  justify-content: flex-start;
}

.sign-img-wrapper {
  font-size: 0;
}

.sign-img {
  width: 80rpx;
  height: 34rpx;
}

.service-wrapper, .sign-wrapper {
  margin-bottom: 10rpx;
}

.justify-text {
  letter-spacing: 4rpx;
}

.justify-sign {
  letter-spacing: 38rpx;
}

.colon {
  margin-left: -34rpx;
  margin-right: 16rpx;
}

.use-time {
  font-size: 24rpx; 
  font-weight: 400;
  color: #868D9B;
}

.use-colon {
  margin-left: 4rpx;
  margin-right: 8rpx;
}

.money-time {
  display: flex;
  flex-direction: column;
}

.cost-money {
  font-size: 28rpx;
  font-weight: 400;
  color: #313131;
}

.operat-time {
  font-size: 24rpx;
  font-weight: 400;
  color: #868D9B;
}

.money-time text {
  display: block;
  margin-bottom: 10rpx;
}

.num-wrapper {
  display: flex;
  justify-content: flex-end;
  font-size: 30rpx;
  font-weight: bold;
  color: #F96048;
  margin-bottom: 10rpx;
}

.not-more {
  text-align: center;
  font-size: 24rpx;
  margin-top: 38rpx;
  color: #DFDFDF;
}
```



```json
{
  "navigationBarTitleText": "扣卡详情",
  "enablePullDownRefresh": true
}
```



