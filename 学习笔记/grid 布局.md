## 基础概念

```html
<div class="wrapper">
  <div class="one item">One</div>
  <div class="two item">Two</div>
  <div class="three item">Three</div>
  <div class="four item">Four</div>
  <div class="five item">Five</div>
  <div class="six item">Six</div>
</div>
```

```css
.wrapper {
  margin: 60px;
  /* 声明一个容器 */
  display: grid;
  /*  声明列的宽度  */
  grid-template-columns: repeat(3, 200px);
  /*  声明行间距和列间距  */
  grid-gap: 20px;
  /*  声明行的高度  */
  grid-template-rows: 100px 200px;
}
.one {
  background: #19CAAD;
}
.two { 
  background: #8CC7B5;
}
.three {
  background: #D1BA74;
}
.four {
  background: #BEE7E9;
}
.five {
  background: #E6CEAC;
}
.six {
  background: #ECAD9E;
}
.item {
  text-align: center;
  font-size: 200%;
  color: #fff;
}

```

1、容器和项目：我们通过在元素上声明 `display：grid` 或 `display：inline-grid` 来创建一个网格容器。

一旦我们这样做，这个元素的所有直系子元素将成为网格项目。

比如上面 `.wrapper` 所在的元素为一个网格容器，其直系子元素将成为网格项目。

2、网格轨道：`grid-template-columns` 和 `grid-template-rows` 属性来定义网格中的行和列。

容器内部的水平区域称为行，垂直区域称为列。上图中 `One`、`Two`、`Three` 组成了一行，`One`、`Four` 则是一列

3、网格单元：一个网格单元是在一个网格元素中最小的单位， 从概念上来讲其实它和表格的一个单元格很像。

图中 `One`、`Two`、`Three`、`Four`...都是一个个的网格单元

![173895918ee0ecb6tplv-t2oaga2asx](https://cdn.nlark.com/yuque/0/2023/jpeg/1614731/1686135881295-1ac41deb-921e-4cdb-a220-b615a7b9002e.jpeg)

4、网格线：划分网格的线，称为"网格线"。应该注意的是，当我们定义网格时，我们定义的是网格轨道，而不是网格线。

Grid 会为我们创建编号的网格线来让我们来定位每一个网格元素。m 列有 m + 1 根垂直的网格线，n 行有 n + 1 跟水平网格线。

上图示例中就有 4 根垂直网格线。

一般而言，是从左到右，从上到下，1，2，3 进行编号排序。

当然也可以从右到左，从下到上，按照 -1，-2，-3...顺序进行编号排序

![17389591934e1560tplv-t2oaga2asx](https://cdn.nlark.com/yuque/0/2023/jpeg/1614731/1686136007905-dc904d84-70ca-4785-aeb1-db4f1b310ebc.jpeg)

Grid 布局属性可以分为两大类，一类是容器属性，一类是项目属性。

## 容器属性

### display 属性

在元素上声明 `display：grid` 或 `display：inline-grid` 来创建一个网格容器

声明 `display：grid` 则该容器是一个块级元素，设置成 `display: inline-grid` 则容器元素为行内元素。

```html
<div>
  <div class="title">test1</div>
  <div class="wrapper">
    <div class="one item">One</div>
    <div class="two item">Two</div>
    <div class="three item">Three</div>
    <div class="four item">Four</div>
    <div class="five item">Five</div>
    <div class="six item">Six</div>
  </div>
  <div class="title">test2</div>
</div>

<div>
   <div class="title">test1</div>
    <div class="wrapper-1">
      <div class="one item">One</div>
      <div class="two item">Two</div>
      <div class="three item">Three</div>
      <div class="four item">Four</div>
      <div class="five item">Five</div>
      <div class="six item">Six</div>
    </div>
  <div class="title">test2</div>
</div>
```

```css
.wrapper, .wrapper-1 {
  margin: 0px;
  display: grid;
  grid-template-columns: repeat(3, 200px);
  grid-gap: 5px;
  grid-auto-rows: 50px 50px;
}

.title {
  display: inline-block;
  font-size: 25px;
  font-weight: bold;
}

.wrapper-1 {
  display: inline-grid;
}

.one {
  background: #19CAAD;
}

.two { 
  background: #8CC7B5;
}

.three {
  background: #D1BA74;
}

.four {
  background: #BEE7E9;
}

.five {
  background: #E6CEAC;
}

.six {
  background: #ECAD9E;
}

.item {
  text-align: center;
  font-size: 200%;
  color: #fff;
}
```

![17389591baa442eftplv-t2oaga2asx](https://cdn.nlark.com/yuque/0/2023/jpeg/1614731/1686136328824-4b5b14f7-2658-41e9-a992-d9182fe70c62.jpeg)

![17389591c03b6883tplv-t2oaga2asx](https://cdn.nlark.com/yuque/0/2023/jpeg/1614731/1686136332859-129e4167-089f-4ecd-a17a-a509baba426d.jpeg)

### grid-template-columns 属性和 grid-template-rows 属性

`grid-template-columns` 属性设置列宽，`grid-template-rows` 属性设置行高，这两个属性在 `Grid` 布局中尤为重要

固定的列宽和行高

```pgsql
.wrapper {
  display: grid;
  /*  声明了三列，宽度分别为 200px 100px 200px */
  grid-template-columns: 200px 100px 200px;
  grid-gap: 5px;
  /*  声明了两行，行高分别为 50px 50px  */
  grid-template-rows: 50px 50px;
}
```

以上表示固定列宽为 200px 100px 200px，行高为 50px 50px

![17389591c0fc1214tplv-t2oaga2asx](https://cdn.nlark.com/yuque/0/2023/jpeg/1614731/1686136535113-f3de12a3-4aab-4249-ba2e-87f0214fd7ef.jpeg)

repeat() 函数：可以简化重复的值。

该函数接受两个参数，第一个参数是重复的次数，第二个参数是所要重复的值。

比如上面行高都是一样的，我们可以这么去实现，实际效果是一模一样的

```css
.wrapper-1 {
  display: grid;
  grid-template-columns: 200px 100px 200px;
  grid-gap: 5px;
  /*  2行，而且行高都为 50px  */
  grid-template-rows: repeat(2, 50px);
}
```

auto-fill 关键字：表示自动填充，让一行（或者一列）中尽可能的容纳更多的单元格。

grid-template-columns: repeat(auto-fill, 200px) 表示列宽是 200 px，但列的数量是不固定的，

只要浏览器能够容纳得下，就可以放置元素，

代码以及效果如下图所示：

```css
.wrapper-2 {
  display: grid;
  grid-template-columns: repeat(auto-fill, 200px);
  grid-gap: 5px;
  grid-auto-rows: 50px;
}
```

![screen1](https://cdn.nlark.com/yuque/0/2023/gif/1614731/1686136788931-4f386d5f-05c7-43a2-a072-06a44513d7a2.gif)

**fr 关键字**：`Grid` 布局还引入了一个另外的长度单位来帮助我们创建灵活的网格轨道。

`fr` 单位代表网格容器中可用空间的一等份。

`grid-template-columns: 200px 1fr 2fr` 表示第一个列宽设置为 200px，后面剩余的宽度分为两部分，宽度分别为剩余宽度的 1/3 和 2/3。

代码以及效果如下图所示：

```css
.wrapper-3 {
  display: grid;
  grid-template-columns: 200px 1fr 2fr;
  grid-gap: 5px;
  grid-auto-rows: 50px;
}
```

![screen1](https://cdn.nlark.com/yuque/0/2023/gif/1614731/1686137011354-ccd4b314-2de7-4755-9c4a-42fb63b811f6.gif)

**minmax() 函数**：

给网格元素一个最小和最大的尺寸，`minmax()` 函数产生一个长度范围，表示长度就在这个范围之中都可以应用到网格项目中。

它接受两个参数，分别为最小值和最大值。

`grid-template-columns: 1fr 1fr minmax(300px, 2fr)` 的意思是，第三个列宽最少也是要 300px，但是最大不能大于第一第二列宽的两倍。

代码以及效果如下：

```css
.wrapper-4 {
  display: grid;
  grid-template-columns: 1fr 1fr minmax(300px, 2fr);
  grid-gap: 5px;
  grid-auto-rows: 50px;
}
```

![screen1](https://cdn.nlark.com/yuque/0/2023/gif/1614731/1686137156739-3ddb6eb9-b17f-4e22-bc22-37f6bc6a153f.gif)

### grid-row-gap 属性、grid-column-gap 属性以及 grid-gap 属性

`grid-row-gap` 属性、`grid-column-gap` 属性分别设置行间距和列间距。

`grid-gap` 属性是两者的简写形式。

`grid-row-gap: 10px` 表示行间距是 10px，`grid-column-gap: 20px` 表示列间距是 20px。`grid-gap: 10px 20px` 实现的效果是一样的

```pgsql
.wrapper {
  display: grid;
  grid-template-columns: 200px 100px 100px;
  grid-gap: 10px 20px;
  grid-auto-rows: 50px;
}
pgsql复制代码.wrapper-1 {
  display: grid;
  grid-template-columns: 200px 100px 100px;
  grid-auto-rows: 50px;
  grid-row-gap: 10px;
  grid-column-gap: 20px;
}
```

以上两种写法效果是一样的。

![17389591f78de6f2tplv-t2oaga2asx](https://cdn.nlark.com/yuque/0/2023/jpeg/1614731/1686137326255-66ec3487-8fea-41c3-8c58-b6e967493cdf.jpeg)

### grid-template-areas 属性

`grid-template-areas` 属性用于定义区域，一个区域由一个或者多个单元格组成

一般这个属性跟网格元素的 `grid-area` 一起使用，我们在这里一起介绍。 `grid-area` 属性指定项目放在哪一个区域

```css
.wrapper {
  display: grid;
  grid-gap: 10px;
  grid-template-columns: 120px  120px  120px;
  grid-template-areas:
    ". header  header"
    "sidebar content content";
  background-color: #fff;
  color: #444;
}
```

上面代码表示划分出 6 个单元格，其中值得注意的是 `.` 符号代表空的单元格，也就是没有用到该单元格。

```css
.sidebar {
  grid-area: sidebar;
}

.content {
  grid-area: content;
}

.header {
  grid-area: header;
}
```

以上代码表示将类 `.sidebar` `.content` `.header`所在的元素放在上面 `grid-template-areas` 中定义的 `sidebar` `content` `header` 区域中

```html
<div class="wrapper">
  <div class="box header">Header</div>
  <div class="box sidebar">Sidebar</div>
  <div class="box content">Content</div>
</div>
```

```css
.sidebar {
  grid-area: sidebar;
}

.content {
  grid-area: content;
}

.header {
  grid-area: header;
}

.wrapper {
  margin: 50px;
  display: grid;
  grid-gap: 10px;
  grid-template-columns: 120px  120px  120px;
  grid-template-areas:
    ". header  header"
    "sidebar content content";
  background-color: #fff;
  color: #444;
}

.box {
  background-color: #444;
  color: #fff;
  border-radius: 5px;
  padding: 20px;
  font-size: 150%;
}

.header {
  background-color: #999;
}
```

![173895920bbe824atplv-t2oaga2asx](https://cdn.nlark.com/yuque/0/2023/jpeg/1614731/1686137510854-fc25be32-34e4-4677-9553-d96d44b760fe.jpeg)

### grid-auto-flow 属性

`grid-auto-flow`  属性控制着自动布局算法怎样运作，精确指定在网格中被自动布局的元素怎样排列。

默认的放置顺序是"先行后列"，即先填满第一行，再开始放入第二行，即下图英文数字的顺序 `one`,`two`,`three`...。

这个顺序由 `grid-auto-flow` 属性决定，默认值是 `row`。

```css
.wrapper {
  display: grid;
  grid-template-columns: 100px 200px 100px;
  grid-auto-flow: row;
  grid-gap: 5px;
  grid-auto-rows: 50px;
}
```

![173895921548265ctplv-t2oaga2asx](https://cdn.nlark.com/yuque/0/2023/jpeg/1614731/1686137631724-0307d33f-7970-488f-be2c-47145e09ac0d.jpeg)

第五个项目和第六个项目之间有个空白，这个是由于第六块的长度大于了空白处的长度，被挤到了下一行导致的。

在实际应用中，我们可能想让下面长度合适的填满这个空白，这个时候可以设置  `grid-auto-flow: row dense`，表示尽可能填满表格。

```css
.wrapper-2 {
  display: grid;
  grid-template-columns: 100px 200px 100px;
  grid-auto-flow: row dense;
  grid-gap: 5px;
  grid-auto-rows: 50px;
}
```

![173895923612a19btplv-t2oaga2asx](https://cdn.nlark.com/yuque/0/2023/jpeg/1614731/1686137713881-d7093b11-b141-4a87-b530-0bbeb92e4c33.jpeg)

设置 `grid-auto-flow: column`，表示先列后行，代码以及效果如下图所示：

```css
.wrapper-1 {
  display: grid;
  grid-auto-columns: 100px;
  grid-auto-flow: column;
  grid-gap: 5px;
  grid-template-rows:  50px 50px;
}
```

![173895923f11dd83tplv-t2oaga2asx](https://cdn.nlark.com/yuque/0/2023/jpeg/1614731/1686137743561-e67feaed-e6c8-4cc2-a979-a6956ad183fc.jpeg)

### justify-items 属性、align-items 属性以及 place-items 属性

`justify-items` 属性设置单元格内容的水平位置（左中右），

`align-items` 属性设置单元格的垂直位置（上中下）

它们都有如下属性：

```css
.container {
  justify-items: start | end | center | stretch;
  align-items: start | end | center | stretch;
}
```

代码实现以及效果如下：

```css
.wrapper, .wrapper-1, .wrapper-2, .wrapper-3 {
  display: grid;
  grid-template-columns: 100px 200px 100px;
  grid-gap: 5px;
  grid-auto-rows: 50px;
  justify-items: start;
}

.wrapper-1 {
  justify-items: end;
}

.wrapper-2 {
  justify-items: center;
}

.wrapper-3 {
  justify-items: stretch;
}
```

- start：对齐单元格的起始边缘

![1738959244947d96tplv-t2oaga2asx](https://cdn.nlark.com/yuque/0/2023/jpeg/1614731/1686137911004-c1b64bdd-b39a-4e1f-a6f5-622bdb83d27a.jpeg)

- end：对齐单元格的结束边缘

![17389592560e3fc2tplv-t2oaga2asx](https://cdn.nlark.com/yuque/0/2023/jpeg/1614731/1686137916178-1de6dd00-d2a1-4ed1-93e8-e458fe0c7042.jpeg)

- center：单元格内部居中

![173895925bd879fatplv-t2oaga2asx](https://cdn.nlark.com/yuque/0/2023/jpeg/1614731/1686137922130-3078b7f0-c68c-4d65-bd93-bf93d4ce5b3d.jpeg)

- stretch：拉伸，占满单元格的整个宽度（默认值）

![1738959270057d0ctplv-t2oaga2asx](https://cdn.nlark.com/yuque/0/2023/jpeg/1614731/1686137927477-c883c7ee-563d-41fb-8468-038325f811ba.jpeg)

### justify-content 属性、align-content 属性以及 place-content 属性

`justify-content` 属性是整个内容区域在容器里面的水平位置（左中右），

`align-content` 属性是整个内容区域的垂直位置（上中下）。

```css
.container {
  justify-content: start | end | center | stretch | space-around | space-between | space-evenly;
  align-content: start | end | center | stretch | space-around | space-between | space-evenly;  
}
```

`justify-content` 属性为例进行讲解，`align-content` 属性同理，只是方向为垂直方向

- start - 对齐容器的起始边框
- end - 对齐容器的结束边框
- center - 容器内部居中

```CSS
.wrapper, .wrapper-1, .wrapper-2, .wrapper-3, .wrapper-4, .wrapper-5, .wrapper-6 {
  display: grid;
  grid-template-columns: 100px 200px 100px;
  grid-gap: 5px;
  grid-auto-rows: 50px;
  justify-content: start;
}

.wrapper-1 {
  justify-content: end;
}

.wrapper-2 {
  justify-content: center;
}
```

![173895926d20f5d6tplv-t2oaga2asx](https://cdn.nlark.com/yuque/0/2023/jpeg/1614731/1686138069458-09d3c43a-6f75-4695-abb3-4121803af17b.jpeg)

- space-around - 每个项目两侧的间隔相等。所以，项目之间的间隔比项目与容器边框的间隔大一倍
- space-between - 项目与项目的间隔相等，项目与容器边框之间没有间隔
- space-evenly - 项目与项目的间隔相等，项目与容器边框之间也是同样长度的间隔
- stretch - 项目大小没有指定时，拉伸占据整个网格容器

```CSS
.wrapper-3 {
  justify-content: space-around;
}

.wrapper-4 {
  justify-content: space-between;
}

.wrapper-5 {
  justify-content: space-evenly;
}

.wrapper-6 {
  justify-content: stretch;
}
```

![173895927ba770c4tplv-t2oaga2asx](https://cdn.nlark.com/yuque/0/2023/jpeg/1614731/1686138073969-d0b1f777-ff8b-4f05-a31f-e3dd71598e15.jpeg)

### grid-auto-columns 属性和 grid-auto-rows 属性

**隐式和显式网格**：

显式网格包含了你在 `grid-template-columns` 和 `grid-template-rows` 属性中定义的行和列。

如果你在网格定义之外又放了一些东西，或者因为内容的数量而需要的更多网格轨道的时候，网格将会在隐式网格中创建行和列.

假如有多余的网格（也就是上面提到的隐式网格），那么它的行高和列宽可以根据 `grid-auto-columns` 属性和 `grid-auto-rows` 属性设置。

它们的写法和`grid-template-columns` 和 `grid-template-rows` 完全相同。

如果不指定这两个属性，浏览器完全根据单元格内容的大小，决定新增网格的列宽和行高

```CSS
.wrapper {
  display: grid;
  grid-template-columns: 200px 100px;
  /*  只设置了两行，但实际的数量会超出两行，超出的行高会以 grid-auto-rows 算 */
  grid-template-rows: 100px 100px;
  grid-gap: 10px 20px;
  grid-auto-rows: 50px;
}
```

`grid-template-columns` 属性和 `grid-template-rows` 属性只是指定了两行两列，但实际有九个元素，就会产生隐式网格。

通过 `grid-auto-rows` 可以指定隐式网格的行高为 50px

![173895927d99af1ctplv-t2oaga2asx](https://cdn.nlark.com/yuque/0/2023/jpeg/1614731/1686138225732-2d98d90f-f96d-419a-8636-782a308ff743.jpeg)

## 项目属性

### grid-column-start 属性、grid-column-end 属性、grid-row-start 属性以及grid-row-end 属性

指定网格项目所在的四个边框，分别定位在哪根网格线，从而指定项目的位置

- grid-column-start 属性：左边框所在的垂直网格线
- grid-column-end 属性：右边框所在的垂直网格线
- grid-row-start 属性：上边框所在的水平网格线
- grid-row-end 属性：下边框所在的水平网格线

```CSS
.wrapper {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  grid-gap: 20px;
  grid-auto-rows: minmax(100px, auto);
}

.one {
  grid-column-start: 1;
  grid-column-end: 2;
  background: #19CAAD;
}

.two { 
  grid-column-start: 2;
  grid-column-end: 4;
  grid-row-start: 1;
  grid-row-end: 2;
  /*   如果有重叠，就使用 z-index */
  z-index: 1;
  background: #8CC7B5;
}

.three {
  grid-column-start: 3;
  grid-column-end: 4;
  grid-row-start: 1;
  grid-row-end: 4;
  background: #D1BA74;
}

.four {
  grid-column-start: 1;
  grid-column-end: 2;
  grid-row-start: 2;
  grid-row-end: 5;
  background: #BEE7E9;
}

.five {
  grid-column-start: 2;
  grid-column-end: 2;
  grid-row-start: 2;
  grid-row-end: 5;
  background: #E6CEAC;
}

.six {
  grid-column: 3;
  grid-row: 4;
  background: #ECAD9E;
}
```

上面代码中，类 `.two` 所在的网格项目，垂直网格线是从 2 到 4，水平网格线是从 1 到 2。

其中它跟 `.three` （垂直网格线是从3 到 4，水平网格线是从 1 到 4） 是有冲突的,可以设置 `z-index` 去决定它们的层级关系.

![173895928bc7e88etplv-t2oaga2asx](https://cdn.nlark.com/yuque/0/2023/jpeg/1614731/1686138427029-a9889720-6334-4165-a6f5-997432c40224.jpeg)

### grid-area 属性

grid-area 属性指定项目放在哪一个区域

### justify-self 属性、align-self 属性以及 place-self 属性

`justify-self` 属性设置单元格内容的水平位置（左中右），跟 `justify-items` 属性的用法完全一致，但只作用于单个项目

`align-self` 属性设置单元格内容的垂直位置（上中下），跟align-items属性的用法完全一致，也是只作用于单个项目

两者很相像，这里只拿 `justify-self` 属性演示，`align-self` 属性同理，只是作用于垂直方向。

`place-self` 是设置 `align-self` 和 `justify-self` 的简写形式。

```css
.item {
  justify-self: start | end | center | stretch;
  align-self: start | end | center | stretch;
}
```

```css
.item {
  justify-self: start;
}

.item-1 {
  justify-self: end;
}

.item-2 {
  justify-self: center;
}

.item-3 {
  justify-self: stretch;
}
```

- start：对齐单元格的起始边缘

![1738959292160e78tplv-t2oaga2asx](https://cdn.nlark.com/yuque/0/2023/jpeg/1614731/1686138661123-3842e4ac-a647-4543-9a71-b0a5449c60b5.jpeg)

- end：对齐单元格的结束边缘

![17389592a0d5a3c0tplv-t2oaga2asx](https://cdn.nlark.com/yuque/0/2023/jpeg/1614731/1686138663568-64dd015e-fbdb-4a84-bba3-1528aa564e2a.jpeg)

- center：单元格内部居中

![17389592b1378c8dtplv-t2oaga2asx](https://cdn.nlark.com/yuque/0/2023/jpeg/1614731/1686138690285-c99c8514-4c5c-433e-861e-4f893f633ee7.jpeg)

- stretch：拉伸，占满单元格的整个宽度（默认值）


![17389592b895f0edtplv-t2oaga2asx](https://cdn.nlark.com/yuque/0/2023/jpeg/1614731/1686138693592-17a2fda2-616b-4c71-a8ea-793d79d6c430.jpeg)











































































































