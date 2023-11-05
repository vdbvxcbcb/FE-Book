## 使用 `:not()` 选择器

先为元素添加边框

```css
/* 添加边框 */
.nav li {
  border-right: 1px solid #666;
}
```

为最后一个元素去除边框

```css
/* 去掉边框 */
.nav li:last-child {
  border-right: none;
}
```

不过不要这么做，使用 `:not()` 伪类来达到同样的效果：

```css
.nav li:not(:last-child) {
  border-right: 1px solid #666;
}
```

## 使用负的 `nth-child` 来选择元素

使用负的 `nth-child` 可以选择 1 至 n 个元素。

```css
li {
  display: none;
}

/* 选择第 1 至第 3 个元素并显示出来 */
li:nth-child(-n+3) {
  display: block;
}
```

或许你已经掌握了[如何使用 `:not()`](https://github.com/AllThingsSmitty/css-protips/blob/master/translations/zh-CN/README.md#use-not-to-applyunapply-borders-on-navigation)这个技巧，试下这个：

```css
/* 选择除前3个之外的所有项目，并显示它们 */
li:not(:nth-child(-n+3)) {
  display: none;
}
```

## 【相邻兄弟选择器】💜相邻兄弟选择器之常用场景

![img](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2019/7/29/16c3d4f9f7e99a80~tplv-t2oaga2asx-zoom-in-crop-mark:3024:0:0:0.awebp)

## 逗号分隔列表

使列表的每项都由逗号分隔：

```
ul > li:not(:last-child)::after {
  content: ",";
}
```

因最后一项不加逗号，可以使用 `:not()` 伪类。

**注意： 这一技巧对于无障碍，特别是屏幕阅读器而言并不理想。而且复制粘贴并不会带走 CSS 生成的内容，需要注意。**

## 1px 问题

媒体查询 + transfrom

先放大、后缩小：在目标元素的后面追加一个 ::after 伪元素，

让这个元素布局为 absolute 之后、整个伸展开铺在目标元素上，然后把它的宽和高都设置为目标元素的两倍，

border值设为 1px。接着借助 CSS 动画特效中的放缩能力，把整个伪元素缩小为原来的 50%。

此时，伪元素的宽高刚好可以和原有的目标元素对齐，而 border 也缩小为了 1px 的二分之一，间接地实现了 0.5px 的效果。

```vue
<template>
  <main class="main">
    <span class="one-pixel-line top"></span>
    <span class="one-pixel-line right"></span>
    <span class="one-pixel-line bottom"></span>
    <span class="one-pixel-line left"></span>
  </main>
</template>

<style>
  main {
    width: 100%;
    padding: 52px 29px;
    display: flex;
    justify-content: center;
  }
  span.one-pixel-line {
    display: block;
    width: 229px; 
    height: 229px;
    position: relative;
  }
  span.one-pixel-line.right,
  span.one-pixel-line.bottom,
  span.one-pixel-line.left {
    margin-left: -229px;
  }
  span.one-pixel-line::before,
  span.one-pixel-line::after {
    content: "";
    display: block;
    position: absolute;
    transform-origin: 0 0;
  }
  /* top line */
  span.one-pixel-line.top::before {
    width: 100%;
    top: 0; 
    left: 0;
    border-top: 1px solid #b4a078;
    transform-origin: 0 top;
  }
  @media (min-resolution: 2dppx) {
    span.one-pixel-line.top::before {
      width: 200%;
      transform: scale(.5) translateZ(0);
    }
  }
  @media (min-resolution: 3dppx) {
    span.one-pixel-line.top::before {
      width: 300%;
      transform: scale(.333333) translateZ(0);
    }
  }
  /* right line */
  span.one-pixel-line.right::after {
    height: 100%;
    top: 0; 
    right: 0;
    border-right: 1px solid #b4a078;
    transform-origin: right 0;
  }
  @media (min-resolution: 2dppx) {
    span.one-pixel-line.right::after {
      height: 200%;
      transform: scale(.5) translateZ(0);
    }
  }
  @media (min-resolution: 3dppx) {
    span.one-pixel-line.right::after {
      height: 300%;
      transform: scale(.333333) translateZ(0);
    }
  }
  /* bottom line */
  span.one-pixel-line.bottom::after {
    width: 100%;
    bottom: 0; 
    left: 0;
    border-bottom: 1px solid #b4a078;
    transform-origin: 0 bottom;
  }
  @media (min-resolution: 2dppx) {
    span.one-pixel-line.bottom::after {
      width: 200%;
      transform: scale(.5) translateZ(0);
    }
  }
  @media (min-resolution: 3dppx) {
    span.one-pixel-line.bottom::after {
      width: 300%;
      transform: scale(.333333) translateZ(0);
    }
  }
  /* left line */
  span.one-pixel-line.left::before {
    height: 100%;
    top: 0; 
    left: 0;
    border-left: 1px solid #b4a078;
    transform-origin: 0 left;
  }
  @media (min-resolution: 2dppx) {
    span.one-pixel-line.left::before {s
      height: 200%;
      transform: scale(.5) translateZ(0);
    }
  }
  @media (min-resolution: 3dppx) {
    span.one-pixel-line.left::before {
      height: 300%;
      transform: scale(.333333) translateZ(0);
    }
  }
</style>


<script>
</script>
```



```vue
<template>
  <span class="one-pixel-border" v-for="(tag, index) in item.tags" :key="index">{{tag}}</span>
</template>


<style>
  .one-pixel-border {
    position: relative;
    &::after{
      content:" ";
      position: absolute;
      top: 0;
      left: 0;
      transform-origin: left top;
      box-sizing: border-box;
      border: 1px solid #999;
      border-radius: 14px;
   }
 }
 @media (min-resolution: 2dppx) {
   .one-pixel-border::after {
     width: 200%;
     height: 200%;
     transform: scale(.5) translateZ(0);
   }
 }
 @media (min-resolution: 3dppx) {
   .one-pixel-border::after {
      width: 300%;
      height: 300%;
      transform:  scale(.333333) translateZ(0);
   }
 }
</style>
```



```css
.card-record-title::after {
  content:"";
  position: absolute;
  width: 100%;
  top: 88rpx; 
  left: 0;
  transform-origin: 0 bottom;
  border-bottom: 2rpx solid #eee;
}
```



## 画三角形

```html
<div id="demo"></div>
<div></div>
```

```css
/* 把上、左、右三条边隐藏掉（颜色设为 transparent） */
#demo {
  width: 0;
  height: 0;
  border-width: 20px;
  border-style: solid;
  border-color: transparent transparent red transparent;
}

/* 下三角 */
div {   
    width: 0;    
    height: 0;    
    border-top: 50px solid red;   
    border-right: 50px solid transparent;    
    border-left: 50px solid transparent;
}

/* 上三角 */
div {
    width: 0;
    height: 0;
    border-bottom: 50px solid red;
    border-right: 50px solid transparent;
    border-left: 50px solid transparent;
}

/* 右三角 */
div {
    width: 0;
    height: 0;
    border-left: 50px solid red;
    border-top: 50px solid transparent;
    border-bottom: 50px solid transparent;
}

/* 左三角 */
div {
    width: 0;
    height: 0;
    border-right: 50px solid red;
    border-top: 50px solid transparent;
    border-bottom: 50px solid transparent;
}

/* 直角三角 */
div {
    width: 0;
    height: 0;
    border-top: 100px solid red;
    border-right: 100px solid transparent;
}
```

## 画箭头

```css
.arrow::after {
  display: inline-block;
  content: "";
  width: 10px;
  height: 10px;
  border-top: 2px solid #999;
  border-right: 2px solid #999;
  transform: rotate(45deg);
}
```

## css 省略号

单行

```css
// 注意宽度是必须的
.article-container {
  width: 500px;
  overflow: hidden;
  text-overflow: ellipsis;
  white-space: nowrap;
}
```

多行

```css
.article-container {
  display: -webkit-box;
  overflow: hidden;
  text-overflow: ellipsis;
  word-break: break-all;
  -webkit-box-orient: vertical;
  -webkit-line-clamp: 4; // 需要显示的行数  
}
```

## 父元素宽度根据内容自适应

```vue
<template>
  <div>
   <p>Let’s assume we have some text here. Bacon ipsum dolor sit amet turkey veniam shankle, culpa short ribs kevin t-bone occaecat.</p>
   <figure>
    <img src="https://gimg2.baidu.com/image_search/src=http%3A%2F%2Fwww.jinlicaijing.com%2Fwp-content%2Fuploads%2F2021%2F03%2F4463097e16c2425f9c86e0b463a45dd9-1.jpg&refer=http%3A%2F%2Fwww.jinlicaijing.com&app=2002&size=f9999,10000&q=a80&n=0&g=0n&fmt=auto?sec=1662457132&t=b5dccbcf70e3976b93060800fec9a3b9" />
    <figcaption>
      The great Sir Adam Catlace was named after Countess Ada Lovelace, the first programmer ever.
    </figcaption>
   </figure>
   <p>We also have some more text here. Et laborum venison nostrud, ut veniam sint kielbasa ullamco pancetta.</p>
  </div>
</template>

<style>
figure {
  padding: 10px;
  border: 1px solid silver;
}

figure {
  max-width: 300px;
  max-width: min-content;
  margin: auto;
}

figure > img { 
  max-width: inherit 
}
</style>
```

## 子元素宽高比自适应

创建具有固定比例的一个盒子，需要做的就是给 div 的顶部或底部设置一个 padding：

```css
/* padding-top/bottom的百分比值是根据容器width计算 */
.box {
  position: relative;
  width: 100%;
  height: 0;
  padding-top: 75%;
}

.wrap {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
}
```

使用  75% 的 padding-top 使得父元素的高度等于其宽度的 75%  。

与视口宽度无关，子元素的 div 将保持其宽高比（100％ /  75％ = 4 : 3）

## 图片自适应

当图片比例不固定时，想要让图片自适应，一般都会用 background-size:cover/contain，但是这个只适用于背景图。

css3 中可使用 object-fit 属性来解决图片被拉伸或是被缩放的问题。使用的提前条件：图片的父级容器要有宽高。

```css
img {
  width: 100%;
  height: 100%;
  object-fit: cover;
}
```

### 【object-fit】🍓图片在指定尺寸后，可以设置 object-fit 为 contain 或 cover 保持比例

适用

对于容器固定而图片尺寸不确定，或者需要响应式布局时，图片固定而容器宽度不确定，需要等比例展示图片或视频。

![img](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2019/7/29/16c3d50c9c447a1a~tplv-t2oaga2asx-zoom-in-crop-mark:3024:0:0:0.awebp)

## 绝对定位元素宽度根据内容自动撑开

```html
<div class="position-container">
  <div class="hover-item">
    <div class="tip">
      <div>列缺霹靂，丘巒崩摧。洞天石扉，訇然中開</div>
    </div>
  </div>
</div>
```

```css
.tip {
    white-space: nowrap;
    padding: 5px 14px;
    border: 1px solid #00adb5;
    border-radius: 21px;
    position: absolute;
    left:50%;
    transform: translateX(-50%);
    bottom:40px;
    background-color: #fff;
    font-size: 16px;
    color:#00adb5;
}
```

```scss
.position-container {
  height: 200px;
  position: relative;
  .hover-item {
    width: 30px;
    height: 30px;
    background: #fff;
    border: 8px solid #00adb5;
    color: #00adb5;
    border-radius: 50%;
    box-sizing: border-box;
    position: absolute;
    left: 0;
    right: 0;
    bottom: 50px;
    margin: auto;
  }
  .tip {
    padding: 5px 14px;
    border: 1px solid #00adb5;
    border-radius: 21px;
    position: absolute;
    left: 50%;
    transform: translateX(-50%);
    bottom: 40px;
    white-space: nowrap; // width: max-content 也行
    background-color: #fff;
    font-size: 16px;
    color: #00adb5;
    &::before {
      content: "";
      width: 0;
      height: 0;
      border-style: solid;
      border-width: 10px 10px 0 10px;
      border-color: #fff transparent transparent transparent;
      position: absolute;
      bottom: -10px;
      left: 0;
      right: 0;
      margin: auto;
      z-index: 2;
    }
    &::after {
      content: "";
      width: 0;
      height: 0;
      border-style: solid;
      border-width: 10px 10px 0 10px;
      border-color: #00adb5 transparent transparent transparent;
      position: absolute;
      bottom: -11px;
      left: 0;
      right: 0;
      margin: auto;
      z-index: 1;
    }
  }
}
```

## 文本换行

```css
<div class="content">
  <span class="br">海客談瀛洲，煙濤微茫信難求。</span>
  <span class="br">越人語天姥，雲霞明滅或可覩。</span>
  <span class="br">天姥連天向天橫，勢拔五嶽掩赤城。</span>
  <span class="br">天台四萬八千丈，對此欲倒東南傾。</span>
</div>
```

```css
.content {
  .br::after {
    content: "\A";
    white-space: pre;
  }
}
```

## 提示气泡

```html
  <div class="poptip btn" aria-controls="弹出气泡">poptip</div>
```

```scss
$poptipBg: #30363d;
$color: #fff;
$triangle: 8px;
$distance: -12px;
.poptip {
  position: relative;
  z-index: 101;
  &::before,
  &::after {
    visibility: hidden;
    opacity: 0;
    transform: translate3d(0, 0, 0);
    transition: all 0.3s ease 0.2s;
    box-sizing: border-box;
  }
  &::before {
    content: "";
    position: absolute;
    width: 0;
    height: 0;
    border-style: solid;
    border-width: $triangle $triangle 0 $triangle;
    border-color: $poptipBg transparent transparent transparent;
    left: calc(50% - #{$triangle});
    top: 0px;
    transform: translateX(0%) translateY($distance);
  }

  &::after {
    font-size: 14px;
    color: $color;
    content: attr(aria-controls);
    position: absolute;
    padding: 6px 12px;
    white-space: nowrap;
    z-index: -1;
    left: 50%;
    bottom: 100%;
    transform: translateX(-50%) translateY($distance);
    background: $poptipBg;
    line-height: 1;
    border-radius: 2px;
  }
  &:hover::before,
  &:hover::after {
    visibility: visible;
    opacity: 1;
  }
}

.btn {
  min-width: 100px;
  line-height: 1.5;
  padding: 5px 10px;
  color: #fff;
  background: #00adb5;
  border-radius: 4px;
  text-align: center;
  cursor: pointer;
}
```

> 更改定位及 `translate` 即可修改气泡位置

## 参数列表自适应对齐

```html
<div class="params-container">
  <div class="param-item">
    <div class="param-cell font-bold">key</div>
    <div class="param-cell font-bold">value</div>
  </div>
  <div class="param-item">
    <div class="param-cell">message</div>
    <div class="param-cell">消息文字</div>
  </div>
  ...
</div>
```

```scss
.params-container {
  display: table;
  border: 1px solid #ddd;
  .param-item {
    display: table-row;
  }
  .param-cell {
    display: table-cell;
    padding: 10px;
  }
  .font-bold {
    font-weight: bold;
  }
}
```

## tab 栏下划线跟随

```html
<div class="tab-container2">
  <div class="title">click to change</div>
  <ul class="tabs">
    <li class="tab-item" v-for="n in 5" :class="{active:isActive==n}" @click="setActive(n)">tab{{n}}</li>
  </ul>
</div>
```

```scss
  ul, li {
    margin: 0;
    padding: 0;
    list-style: none;
  }

  .tab-container2 {
    border: 1px solid #eee;
    margin: 50px auto;
    padding: 20px 0;
    .title{
      padding-left:30px;
      font-size: 12px;
      color:#ddd;
    }
    .tabs {
      display: flex;
      justify-content: center;
      align-items: center;
      li {
        height: 40px;
        line-height: 40px;
        padding: 0 15px;
        cursor: pointer;
        position: relative;
        font-size: 18px;
        color: #909399;
        background-color: #fff;
        &::after {
          content: "";
          width: 0;
          height: 2px;
          background-color: #00adb5;
          position: absolute;
          left: 100%;
          bottom: 0;
          transition: all .4s;
        }

        &.active {
          color: #00adb5;
          &::after {
            width: 100%;
            left: 0;
            transition-delay: 0.1s;
          }
          & ~ li::after {
            left: 0;
          }
        }
      }
    }
  }
```

```js
  export default {
    name: 'clickTab',
    data() {
      return {
        isActive: 1
      }
    },
    methods: {
      setActive(index) {
        this.isActive = index
      }
    }
  }
```

## 滚动条样式

```vue
<template>
  <div class="scroll-container">
    <p class="row" v-for="(value,index) in poem.split('。')">{{value}}</p>
  </div>
</template>

<script>
export default {
  name: "scrollTemp",
  data() {
    return {
      poem:
        "海客談瀛洲，煙濤微茫信難求。越人語天姥，雲霞明滅或可覩。天姥連天向天橫，勢拔五嶽掩赤城。天台四萬八千丈，對此欲倒東南傾。我欲因之夢吳越，一夜飛渡鏡湖月。湖月照我影，送我至剡溪。謝公宿處今尚在，淥水蕩漾清猨啼。腳著謝公屐，身登青雲梯。半壁見海日，空中聞天雞。千巖萬轉路不定，迷花倚石忽已暝。熊咆龍吟殷巖泉，慄深林兮驚層巔。雲青青兮欲雨，水澹澹兮生煙。列缺霹靂，丘巒崩摧。洞天石扉，訇然中開。青冥浩蕩不見底，日月照耀金銀臺。霓爲衣兮風爲馬，雲之君兮紛紛而來下。虎鼓瑟兮鸞迴車，仙之人兮列如麻。忽魂悸以魄動，怳驚起而長嗟。惟覺時之枕席，失向來之煙霞。世間行樂亦如此，古來萬事東流水。別君去兮何時還？且放白鹿青崖間，須行即騎訪名山。安能摧眉折腰事權貴，使我不得開心顏。"
    }
  }
};
</script>

<style scoped lang="scss">
.scroll-container {
  height: 250px;
  border: 1px solid #ddd;
  padding: 15px;
  overflow: auto;
  .row {
    margin: 0;
    line-height: 1.5;
  }

  &::-webkit-scrollbar {
    width: 8px;
    background: white;
  }
  &::-webkit-scrollbar-corner, /* 滚动条角落 */
  &::-webkit-scrollbar-thumb,
  &::-webkit-scrollbar-track {
    border-radius: 4px;
  }
  &::-webkit-scrollbar-corner,
  &::-webkit-scrollbar-track {
    /* 滚动条轨道 */
    background-color: rgba(180, 160, 120, 0.1);
    box-shadow: inset 0 0 1px rgba(180, 160, 120, 0.5);
  }
  &::-webkit-scrollbar-thumb {
    /* 滚动条手柄 */
    background-color: #00adb5;
  }
}
</style>
```

兼容性很差，并且很多样式不可控，建议在生产环境中采用类似 [perfect-scrollbar](https://github.com/utatti/perfect-scrollbar) 的方案。

## 粘性底部

```vue
<template>
  <div>
    <header>
      <h1>Site name</h1>
    </header>
    <main>
      <input type="checkbox" id="contents" />
      <label for="contents">Toggle contents</label>
      <p v-for="n in 13" :key="n">{{n}}. Bacon ipsum dolor sit amet turkey veniam shankle, culpa short ribs kevin t-bone occaecat. Et laborum venison nostrud, ut veniam sint kielbasa ullamco pancetta. Qui drumstick tail, bacon leberkas shoulder capicola laborum. Minim ipsum bacon, mollit laboris t-bone pariatur. Ham hock reprehenderit sint beef, sausage pig eiusmod t-bone shankle strip steak.</p></main>
    <footer>
      <p>© 2015 No rights reserved.</p><p>Made with ♥ by an anonymous pastafarian.</p>
    </footer>
  </div>
</template>

<style>
body {
  margin: 0;
  font: 100%/1.5 Palatino Linotype, Palatino, serif;
}

header { 
  text-align: center;
  height: 48px; 
}

h1 { 
  margin: 8px 0 0; 
}

main, footer {
  display: block;
  padding: 8px calc(50% - 400px);
}

main {
  min-height: calc(100vh - 192px);
}

#contents:checked ~ p { 
  display: none 
}

footer {
  background: linear-gradient(#222, #444);
  color: white;
  height: 96px;
}
</style>
```



```css
<div class="body_box">
    <div class="main">main</div>
</div>
<div class="footer">footer</div>

.body_box {
    position: relative;
    min-height: 100%;
    width: 100%;
    overflow: hidden;
}
.footer {
    position: relative;
    margin-top: -31px;
    width: 100%;
    text-align: center;
    height: 30px;
    border-top: 1px solid #e5e5e5;
    color: #666;
    z-index: 10;
    overflow: hidden;
}
```

flex

```html
<body class="Site">
  <header>…</header>
  <main class="Site-content">…</main>
  <footer>…</footer>
</body>
```

```css
.Site {
  display: flex;
  min-height: 100vh;
  flex-direction: column;
}

.Site-content {
  flex: 1;
}
```

## 自定义开关

```vue
<template>
  <main>
    <input type="checkbox" id="switch" checked hidden>
    <label for="switch"></label>
    <input type="checkbox" id="switch-disabled" disabled hidden>
    <label for="switch-disabled" disabled></label>
    <input type="checkbox" id="switch-checked-disabled" checked disabled hidden>
    <label for="switch-checked-disabled" disabled></label>
  </main>
</template>

<style>
  main {
    width: 100%;
    padding: 60px 0;
    display: flex;
    justify-content: space-around;
    align-items: center;
    flex-wrap: wrap;
    user-select: none;
    font: 12px / 1 Helvetica, sans-serif;
  }
  label {
    position: relative;
    width: 40px;
    height: 20px;
    background: lightgrey;
    border-radius: 10px;
    cursor: pointer;
    transition: background .3s;
  }
  label[disabled] {
    cursor: not-allowed;
    opacity: .5;
  }
  label::before,
  label::after {
    transition: all .3s;
    position: absolute;
  }
  label::before {
    content: '关';
    top: 4px;
    left: 22px;
    color: white;
  }
  label::after {
    content: '';
    top: 1px;
    left: 1px;
    width: 18px;
    height: 18px;
    border-radius: 9px;
    background: white;
  }
  input[type="checkbox"]:checked + label {
    background: #b4a078;
  }
  input[type="checkbox"]:checked + label::before {
    content: '开';
    left: 6px;
  }
  input[type="checkbox"]:active + label::after {
    width: 23px;
  }
  input[type="checkbox"]:checked + label::after {
    left: 21px;
  }
  input[type="checkbox"]:checked:active + label::after {
    left: 16px;
  }
</style>
```



## 加载动画

```html
<div class="loading"></div>
```

```css
.loading {
    width: 50px;
    height: 50px;
    display: inline-block;
    border: 5px solid #ddd;
    border-left-color: #FFB5BF;
    border-radius: 50%; 
}

.loading {
    animation: loading-animation 1.2s linear infinite;
}

@keyframes loading-animation {
    0% {
      transform: rotate(0deg);
    }
    100% {
      transform: rotate(360deg);
    }
}
```

## 为图裂定义样式

只要一点CSS就可以美化破损的图片：

```css
img {  
  display: block;
  font-family: sans-serif;
  font-weight: 300;
  height: auto;
  line-height: 2;
  position: relative;
  text-align: center;
  width: 100%;
}
```

以添加伪元素的法则来显示用户信息和URL的引用：

```css
img::before {  
  content: "We're sorry, the image below is broken :(";
  display: block;
  margin-bottom: 10px;
}

img::after {  
  content: "(url: " attr(src) ")";
  display: block;
  font-size: 12px;
}
```

## 使用 `max-height` 来建立纯 CSS 的滑块

`max-height` 与 overflow hidden 一起来建立纯 CSS 的滑块：

```css
.slider {
  max-height: 200px;
  overflow-y: hidden;
  width: 300px;
}

.slider:hover {
  max-height: 600px;
  overflow-y: scroll;
}
```

鼠标移入滑块元素时增大它的 `max-height` 值，便可以显示溢出部分。

## 进度条

```css
<template>
  <main>
    <div class="progress-outer">  <!--更好的扩展-->
      <div class="progress-enter">
        <div class="progress-bg"></div>
      </div>
      <!-- <span>60%</span> -->
    </div>
  </main>
</template>

<style>
  main {
    width: 100%;
    padding: 80px 0px;
    display: flex;
    flex-wrap: wrap;
    justify-content: space-around; 
  }
  .progress-outer {
    width: 60%; height: 12px;
    border-radius: 8px;
    overflow: hidden;
    position: relative; 
  }    
  .progress-enter {  
    height: inherit;
    background: rgba(180, 160, 120, .2); 
  }
  .progress-bg {
    width: 60%; height: inherit;
    border-radius: 6px; 
    background: repeating-linear-gradient(-45deg, #D9CFBB  25%, #C3B393 0, #C3B393 50%,
                    #D9CFBB 0, #D9CFBB 75%, #C3B393 0);
    background-size: 16px 16px;
    animation: panoramic 20s linear infinite;
  }
  @keyframes panoramic{
    to {
      background-position: 200% 0;
    }
  }
</style>

<script>
</script>
```

## 不规则卡片（优惠券）

```vue
<template>
  <main>
    <div class="coupon-card"></div>
  </main>
</template>

<style>
  main {
    width: 100%;
    padding: 80px 0px;
    display: flex;
    flex-wrap: wrap;
    justify-content: space-around; 
  }
  .coupon-card {
    width: 200px;
    height: 120px;
    background-image: radial-gradient(circle at 100px -8px, transparent 20px, #b4a078 21px);
  }
</style>

<script>
</script>
```

## 伪元素扩大可点击区域

扩大 10px

```css
button {
  position: relative;
  padding: .3em .5em;
  background: #58a;
  border-radius: 50%;
  border: 1px solid rgba(0,0,0,.3);
  box-shadow:  0 .1em .2em -.05em rgba(0,0,0,.5);
  color: white;
  font: bold 150%/1 sans-serif;
  cursor: pointer;
}

button:before {
  content: '';
  position: absolute;
  top: -10px; 
  right: -10px;
  bottom: -10px;
  left: -10px;
}
```

## 【鼠标状态】🍒按钮禁用时，不要忘了设置鼠标状态

![img](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2019/7/29/16c3d50df31971f3~tplv-t2oaga2asx-zoom-in-crop-mark:3024:0:0:0.awebp)

## 【背景虚化】🍑使用CSS滤镜实现背景虚化

![img](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2019/7/29/16c3d50e8cadbfc4~tplv-t2oaga2asx-zoom-in-crop-mark:3024:0:0:0.awebp)

## 【模态框】🖤要使模态框背景透明，用rgba是一种简单方式

![img](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2019/7/29/16c3d4fb360585bf~tplv-t2oaga2asx-zoom-in-crop-mark:3024:0:0:0.awebp)

纯 css 模态框

```html
<h1>Popup/Modal Windows without JavaScript</h1>
<div id="wrapper">
  <p><a class="button" href="#popup1">Click Me</a></p>
	<p><a class="button" href="#popup2">Click Me Too</a></p>
</div>

<div id="popup1" class="overlay">
	<div class="popup">
		<h2>Info box</h2>
		<a class="close" href="#">&times;</a>
		<div class="content">
			<p>This is done totally without JavaScript. Just HTML and CSS.</p>
		</div>
	</div>
</div>

<div id="popup2" class="overlay light">
	<a class="cancel" href="#"></a>
	<div class="popup">
		<h2>What the what?</h2>
		<div class="content">
           <p>Click outside the popup to close.</p>
		</div>
	</div>
</div>
```



```css
body {
  font-family: Arial, sans-serif;
	background: #ddd;
}

h1 {
	text-align: center;
	font-family: "Trebuchet MS", Tahoma, Arial, sans-serif;
	color: #333;
	text-shadow: 0 1px 0 #fff;
	margin: 50px 0;
}

#wrapper {
	width: 100px;
	margin: 0 auto;
	background: #fff;
	padding: 20px;
	border: 10px solid #aaa;
	border-radius: 15px;
	background-clip: padding-box;
	text-align: center;
}

.button {
	font-family: Helvetica, Arial, sans-serif;
	font-size: 13px;
	padding: 5px 10px;
	border: 1px solid #aaa;
	background-color: #eee;
	background-image: linear-gradient(top, #fff, #f0f0f0);
	border-radius: 2px;
	box-shadow: 0 1px 3px rgba(0,0,0,0.15);
	color: #666;
	text-decoration: none;
	text-shadow: 0 1px 0 #fff;
	cursor: pointer;
	transition: all 0.2s ease-out;
  &:hover {
    border-color: #999;
    box-shadow: 0 1px 3px rgba(0,0,0,0.25);
  }
  &:active {
    box-shadow: 0 1px 3px rgba(0,0,0,0.25) inset;
  }
}

.overlay {
	position: absolute;
	top: 0;
	bottom: 0;
  left: 0;
  right: 0;
  background: rgba(0,0,0,0.5);
	transition: opacity 200ms;
  visibility: hidden;
	opacity: 0;
	&.light {
    background: rgba(255,255,255,0.5);
  }
  .cancel {
    position: absolute;
    width: 100%;
    height: 100%;
    cursor: default;
  }
  &:target {
    visibility: visible;
    opacity: 1;
  }
}

.popup {
	margin: 75px auto;
	padding: 20px;
	background: #fff;
	border: 1px solid #666;
	width: 300px;
	box-shadow: 0 0 50px rgba(0,0,0,0.5);
	position: relative;
  .light & {
    border-color: #aaa;
    box-shadow: 0 2px 10px rgba(0,0,0,0.25);
  }
  h2 {
    margin-top: 0;
    color: #666;
    font-family: "Trebuchet MS", Tahoma, Arial, sans-serif;
  }
  .close {
    position: absolute;
    width: 20px;
    height: 20px;
    top: 20px;
    right: 20px;
    opacity: 0.8;
    transition: all 200ms;
    font-size: 24px;
    font-weight: bold;
    text-decoration: none;
    color: #666;
    &:hover {
      opacity: 1;
    }
  }
  .content {
    max-height: 400px;
    overflow: auto;
  }
  p {
    margin: 0 0 1em;
    &:last-child {
      margin: 0;
    }
  }
}
```



## 【min-content/max-content】🍍可以设置宽度为 min-content 和 max-content ，前者让内容尽可能地收缩，后者让内容尽可能地展开

![img](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2019/7/29/16c3d511d0ea8214~tplv-t2oaga2asx-zoom-in-crop-mark:3024:0:0:0.awebp)

## 切换按钮

![切换按钮](https://cdn.nlark.com/yuque/0/2023/gif/1614731/1698729199210-6fd35ae7-5b1a-4c43-96cc-25dd61a3ccfc.gif)

```css
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta http-equiv="X-UA-Compatible" content="IE=edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Document</title>
    <style>
      body {
        display: flex;
      }

      label {
        display: flex;
        align-items: center;
      }

      .switch {
        width: 120px;
        height: 60px;
        border-radius: 60px;
        border: 1px solid #dfdfdf;
        background-color: #fdfdfd;
        box-shadow: #dfdfdf 0 0 0 0 inset;
        transition: all 0.3s ease;
        -webkit-appearance: none;
        user-select: none;
        outline: none;
        position: relative;
      }

      .switch:checked {
        border-color: #64bd63;
        background-color: #64bd63;
      }

      .switch::before {
        content: "";
        height: 58px;
        width: 58px;
        position: absolute;
        top: 0;
        right: 0;
        border-radius: 60px;
        box-shadow: 0 1px 3px rgba(0, 0, 0, 0.4);
        background-color: #fdfdfd;
        transition: all 0.3s ease;
      }

      .switch:checked::before {
        transition: right 0.3s;
        right: 60px;
      }

      .switch-anim {
        width: 120px;
        height: 60px;
        border: 1px solid #dfdfdf;
        background-color: #fdfdfd;
        box-shadow: #dfdfdf 0 0 0 0 inset;
        border-radius: 60px;
        -webkit-appearance: none;
        user-select: none;
        outline: none;
        position: relative;
        transition: background-color cubic-bezier(0, 0, 0, 1) 0.4s, box-shadow cubic-bezier(0, 0, 0, 1) 0.4s;
      }

      .switch-anim::before {
        content: "";
        width: 58px;
        height: 58px;
        border-radius: 60px;
        position: absolute;
        top: 0;
        right: 0;
        background-color: #fff;
        box-shadow: 0 1px 3px rgba(0, 0, 0, 0.4);
        transition: right 0.3s;
      }

      .switch-anim:checked {
        border-color: #64bd63;
        box-shadow: #64bd63 0 0 0 30px inset;
        transition: border ease 0.4s, box-shadow ease 0.4s, background-color ease 1.2s;
      }

      .switch-anim:checked::before {
        right: 60px;
        transition: right 0.3s;
      }
    </style>
  </head>
  <body>
    <label>
      基础按钮-切换颜色渐变
      <input type="checkbox" class="switch" checked />
    </label>
    <label>
      进阶按钮-切换缩放效果
      <input type="checkbox" class="switch-anim" checked />
    </label>
  </body>
</html>

```











































































