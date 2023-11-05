# React 项目练习

## 项目规范

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1629806816809-8d2f3999-e09c-4fd4-8549-b74aa3a9895e.jpeg)

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1629807411843-f4574d15-1d80-4dcf-a417-18d196135765.jpeg)

## 初始化项目结构

```shell
create-react-app fe-web-music
yarn start
```

清空文件，修改 favicon

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1629809745923-06dc8cc9-bbf0-494f-9a6a-7a0fcf0d2789.jpeg)

目录结构

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1629810369150-e99a3dc3-7f58-46e8-b1d6-df870c1b3303.jpeg)

## 初始化 css 与公共 css

```
yarn add normalize.css
```

~ 表示 webpack 的 alias 路径，可以与后面拼接成绝对路径

assets/css/reset.css

```css
@import "~normalize.css";
```

index.js

```jsx
import React from 'react';
import ReactDOM from 'react-dom';

import "./assets/css/reset.css"; 

import App from './App';

ReactDOM.render(
  <App />,
  document.getElementById('root')
);

```

assets/css/reset.css 添加自己设置的公共 css， assets/img 添加图片

比如字体大小、加省略号、居中布局、一大块布局的宽度、精灵图、图片的玻璃质感

```css
@import "~normalize.css";
@import "~antd/dist/antd.css";

/* 样式的重置 */
body, html, h1, h2, h3, h4, h5, h6, ul, ol, li, dl, dt, dd, header, menu, section, p, input, td, th, ins {
  padding: 0;
  margin: 0;
}

ul, ol, li {
  list-style: none;
}

a {
  text-decoration: none;
  color: #666;
}

a:hover {
  color: #666;
  text-decoration: underline;
}

i, em {
  font-style: normal;
}

input, textarea, button, select, a {
  outline: none;
  border: none;
}

table {
  border-collapse: collapse;
  border-spacing: 0;
}

img {
  border: none;
  vertical-align: middle;
}

/* 全局样式 */
body, textarea, select, input, button {
  font-size: 12px;
  color: #333;
  font-family: Arial, Helvetica, sans-serif;
  background-color: #f5f5f5;
}

.text-nowrap {
  white-space: nowrap;
  text-overflow: ellipsis;
  overflow: hidden;
}

.wrap-v1 {
  width: 1100px;
  margin: 0 auto;
}

.wrap-v2 {
  width: 980px;
  margin: 0 auto;
}

.sprite_01 {
  background: url(../img/sprite_01.png) no-repeat 0 9999px;
}

.sprite_02 {
  background: url(../img/sprite_02.png) no-repeat 0 9999px;
}

.sprite_covor {
  background: url(../img/sprite_cover.png) no-repeat 0 9999px;
}

.sprite_icon {
  background: url(../img/sprite_icon.png) no-repeat 0 9999px;
}

.sprite_icon2 {
  background: url(../img/sprite_icon2.png) no-repeat 0 9999px;
}

.sprite_button {
  background: url(../img/sprite_button.png) no-repeat 0 9999px;
}

.sprite_button2 {
  background: url(../img/sprite_button2.png) no-repeat 0 9999px;
}

.sprite_table {
  background: url(../img/sprite_table.png) no-repeat 0 9999px;
}

.image_cover {
  position: absolute;
  left: 0;
  right: 0;
  top: 0;
  bottom: 0;
  text-indent: -9999px;
  background: url(../img/sprite_cover.png) no-repeat -145px -57px;
}

.sprite_player {
  background: url(../img/playbar_sprite.png) no-repeat 0 9999px;
}

/* 歌词样式 */
.lyric-class .ant-message-notice-content {
  position: fixed;
  left: 50%;
  transform: translateX(-50%);
  bottom: 50px;
  background-color: rgba(0, 0, 0, .3);
  color: #fff;
}

.lyric-class .ant-message-custom-content {
  font-size: 30px;
  text-shadow: 1px 2px 3px rgba(254, 98 ,154);
}

/* 顶部轮播图指示点样式 */
.ant-carousel .slick-dots-bottom {
  height: 20px;
}

.ant-carousel .slick-dots li button {
  width: 20px;
  height: 20px;
  background: url(../img/banner_sprite.png); 
  background-position: 3px -343px;
  opacity: 1;
  transition: none; 
}

.ant-carousel .slick-dots li {
  margin: 0;
  width: 20px;
  height: 20px;
  transition: none; 
}

.ant-carousel .slick-dots li.slick-active{
  margin: 0;
  width: 20px;
  height: 20px;
}

.ant-carousel .slick-dots li.slick-active button {
  background: url(../img/banner_sprite.png);
  background-position: -16px -343px;
}
```

另外，环境的划分：一般来说，一个环境一个服务器

## 设置路径别名

```shell
yarn add @craco/craco
```

修改 package.json

```json
"scripts": {
    "start": "craco start",
    "build": "craco build",
    "test": "craco test",
    "eject": "react-scripts eject"
  }
```

在项目目录下（非 src）创建 craco.config.js

```js
const path = require("path");

// 拼接 __dirname  dir
const resolve = dir => path.resolve(__dirname, dir);

module.exports = {
  webpack: {
    alias: {
      "@": resolve("src"),
      "components": resolve("src/components")
    }
  }
}
```

index.js 修改引入路径

```jsx
import React from 'react';
import ReactDOM from 'react-dom';

import "@/assets/css/reset.css"; 

import App from './App';

ReactDOM.render(
  <App />,
  document.getElementById('root')
);
```

## 布局结构划分

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1629894156115-1067c382-3263-466e-817c-0e47b29c2bdb.jpeg)

组件采用名字前缀 HY

components/app-header/index.js

```jsx
import React, { memo } from 'react';

export default memo(function HYAppHeader() {
  return (
    <div>
      <h2>HYAppHeader</h2>
    </div>
  )
})
```

components/app-footer/index.js

```jsx
import React, { memo} from 'react';

export default memo(function HYAppFooter() {
  return (
    <div>
      <h2>HYAppFooter</h2>
    </div>
  )
})
```

App.js

```jsx
import React, { memo } from 'react';

import HYAppHeader from "components/app-header";
import HYAppFooter from "components/app-footer";

export default memo(function App() {
  return (
    <div>
      <HYAppHeader/>
      <h2>Content</h2>
      <HYAppFooter/>
    </div>
  )
})
```

初始效果

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1629895651903-05bb5814-4e5a-4a7d-a0f5-80430a3c2dae.jpeg)

## 头部路由的搭建

Content 组件需要根据路由进行配置，不同 tab 路径的切换对应地切换组件，不能固定一个组件

```shell
yarn add react-router-dom
yarn add react-router-config
```

import 导入规范：第三方、其它、组件

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1629896301823-585346bd-e4ec-470f-83cb-bb914c3b52cc.jpeg)

App.js

```jsx
import React, { memo } from 'react';
import { renderRoutes } from 'react-router-config';

import routes from './router';

import HYAppHeader from "components/app-header";
import HYAppFooter from "components/app-footer";
import { HashRouter } from 'react-router-dom'

export default memo(function App() {
  return (
    <div>
      <HashRouter>
        <HYAppHeader/>
        {renderRoutes(routes)}
        <HYAppFooter/>
      </HashRouter>
    </div>
  )
})
```

router/index.js

```js
import React from 'react';
import { Redirect } from "react-router-dom";

import HYDiscover from "@/pages/discover";
import HYMine from "@/pages/mine";
import HYFriend from "@/pages/friend";

const routes = [
  {
    path: "/",
    exact: true,
    // 默认路由从 / 重定向到 /#/discover
    render: () => (
      <Redirect to="/discover"/>
    )
  },
  {
    path: "/discover",
    component: HYDiscover,
  },
  {
    path: "/mine",
    component: HYMine
  },
  {
    path: "/friend",
    component: HYFriend
  },
];

export default routes;
```

pages/discover/index.js

```jsx
import React, { memo } from 'react';

export default memo(function HYDiscover() {
  return (
    <div>
      <h2>HYDiscover</h2>
    </div>
  )
})
```

pages/friend/index.js

```jsx
import React, { memo } from 'react';

export default memo(function HYFriend() {
  return (
    <div>
      <h2>HYFriend</h2>
    </div>
  )
})
```

pages/mine/index.js

```jsx
import React, { memo } from 'react';

export default memo(function HYMine() {
  return (
    <div>
      <h2>HYMine</h2>
    </div>
  )
})
```

components/app-header/index.js

```jsx
import React, { memo } from 'react';

import { NavLink } from 'react-router-dom';

export default memo(function HYAppHeader() {
  return (
    <div>
      <NavLink to="/">发现音乐</NavLink>
      <NavLink to="/mine">我的音乐</NavLink>
      <NavLink to="/friend">朋友</NavLink>
    </div>
  )
})
```

路由效果

![img](https://cdn.nlark.com/yuque/0/2021/gif/1614731/1629898380516-e7d347a5-82be-4f1c-a7ba-8b3c6c037618.gif)

## 头部组件布局与样式

头部分为两部分：

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1629898732182-598a80c4-1aa3-4226-a221-b98f29ff3dad.jpeg)

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1629898743717-453c9566-3931-4f40-9273-35a5f51d5c46.jpeg)

```shell
yarn add styled-components
```

如果给一个组件写 styled-components 的样式，先给组件最外层做个包裹 wrapper，写上基本的样式：

高度、背景颜色、字体大小。

```js
import styled from 'styled-components';

export const DiscoverWrapper = styled.div`

`
```

然后分成 content 和 divider 两部分添加样式。

content 里使用 flex 布局分为左右两部分：左边网易云，右边为 Tab 区域 。

左边如果使用 NavLink 改变样式需要多包裹一层，所以采用 a 标签，

a 标签里的文字（为了 seo）可以使用 text-indent: -9999px; 来隐藏，

右边里面的链接都有一一对应的关系，所以要建个数组一一遍历即可。

showSelectItem 

前三个：发现音乐、我的音乐、朋友都是内部路由跳转。后面三个是超链接跳转，跳到新页面。

前三个 NavLink 选中时会默认添加 active ，添加 exact 精准匹配实现选中时下方添加三角形

搜索框可以使用 ant-design 组件库

```shell
yarn add antd
yarn add @ant-design/icons
```

assets/css/reset.css

```css
@import "~antd/dist/antd.css";
```

src/common/local-data.js

```js
export const headerLinks = [
  {
    title: "发现音乐",
    link: "/discover"
  },
  {
    title: "我的音乐",
    link: "/mine"
  },
  {
    title: "朋友",
    link: "/friend"
  },
  {
    title: "商城",
    link: "https://music.163.com/store/product"
  },
  {
    title: "音乐人",
    link: "https://music.163.com/nmusician/web/index#/"
  },
  {
    title: "下载客户端",
    link: "https://music.163.com/#/download"
  }
]
```

src/components/app-header/index.js

```jsx
import React, { memo } from 'react';

import { headerLinks } from "@/common/local-data";

import { NavLink } from 'react-router-dom';
import { SearchOutlined } from '@ant-design/icons'
import { Input } from "antd";
import {
  HeaderWrapper,
  HeaderLeft,
  HeaderRight
} from './style';

export default memo(function HYAppHeader() {

  // 页面代码
  const showSelectItem = (item, index) => {
    if (index < 3) {
      return (
        <NavLink to={item.link}>
          {item.title}
          <i className="sprite_01 icon"></i>
        </NavLink>
      )
    } else {
      return <a href={item.link}>{item.title}</a>
    }
  }

  // 返回的jsx
  return (
    <HeaderWrapper>
      <div className="content wrap-v1">
        <HeaderLeft>
          <a href="#/" className="logo sprite_01">网易云音乐</a>
          <div className="select-list">
            {
              headerLinks.map((item, index) => {
                return (
                  <div key={item.title} className="select-item">
                    {showSelectItem(item, index)}
                  </div>
                )
              })
            }
          </div>
        </HeaderLeft>
        <HeaderRight>
          <Input className="search" placeholder="音乐/视频/电台/用户" prefix={<SearchOutlined />}/>
          <div className="center">创作者中心</div>
          <div>登录</div>
        </HeaderRight>
      </div>
      <div className="divider"></div>
    </HeaderWrapper>
  )
})
```

src/components/app-header/style.js

```js
import styled from "styled-components";

export const HeaderWrapper = styled.div`
  height: 75px;
  font-size: 14px;
  color: #fff;
  background-color: #242424;

  .content {
    height: 70px;

    display: flex;
    justify-content: space-between;
  }

  .divider {
    height: 5px;
    background-color: #C20C0C;
  }
`

export const HeaderLeft = styled.div`
  display: flex;

  .logo {
    display: block;
    width: 176px;
    height: 69px;
    background-position: 0 0;
    text-indent: -9999px;
  }

  .select-list {
    display: flex;
    line-height: 70px;
    
    .select-item {
      position: relative;
      a {
        display: block;
        padding: 0 20px;
        color: #ccc;
      }

      :last-of-type a {
        position: relative;
        ::after {
          position: absolute;
          content: "";
          width: 28px;
          height: 19px;
          background-image: url(${require("@/assets/img/sprite_01.png")});
          background-position: -190px 0;
          top: 20px;
          right: -15px;
        }
      }

      &:hover a, a.active {
        color: #fff;
        background: #000;
        text-decoration: none;
      }
      
      .active .icon {
        position: absolute;
        display: inline-block;
        width: 12px;
        height: 7px;
        bottom: -1px;
        left: 50%;
        transform: translate(-50%, 0);
        background-position: -226px 0;
      }
    }
  }
`

export const HeaderRight = styled.div`
  display: flex;
  align-items: center;
  color: #ccc;
  font-size: 12px;


  .search {
    width: 158px;
    height: 32px;
    border-radius: 16px;

    input {
      &::placeholder {
        font-size: 12px;
      }
    }
  }

  .center {
    width: 90px;
    height: 32px;
    line-height: 32px;
    text-align: center;
    border: 1px solid #666;
    border-radius: 16px;
    margin: 0 16px;
    background-color: transparent;
  }
`
```

效果图

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1629973517964-d3f47ae1-835c-4216-a896-d64fae12be74.jpeg)

## 底部组件布局与样式

**注意：引入背景图需要加 .default**

https://www.cnblogs.com/shellon/p/14240630.html

https://blog.csdn.net/Ting_hao/article/details/109661227

```js
background: url(${require("@/assets/img/sprite_footer_02.png").default})
```

src\common\local-data.js

```js
export const headerLinks = [
  {
    title: "发现音乐",
    link: "/discover"
  },
  {
    title: "我的音乐",
    link: "/mine"
  },
  {
    title: "朋友",
    link: "/friend"
  },
  {
    title: "商城",
    link: "https://music.163.com/store/product"
  },
  {
    title: "音乐人",
    link: "https://music.163.com/nmusician/web/index#/"
  },
  {
    title: "下载客户端",
    link: "https://music.163.com/#/download"
  }
]

export const footerLinks = [
  {
    title: "服务条款",
    link: "https://st.music.163.com/official-terms/service"
  },
  {
    title: "隐私政策",
    link: "https://st.music.163.com/official-terms/privacy"
  },
  {
    title: "儿童隐私政策",
    link: "https://st.music.163.com/official-terms/children"
  },
  {
    title: "版权投诉指引",
    link: "https://music.163.com/st/staticdeal/complaints.html"
  },
  {
    title: "意见反馈",
    link: "#"
  },
  {
    title: "广告合作",
    link: "mailto:yyyx@list.nie.netease.com"
  }
]

export const footerImages = [
  {
    link: "https://music.163.com/st/userbasic#/auth"
  },
  {
    link: "https://music.163.com/recruit"
  },
  {
    link: "https://music.163.com/web/reward"
  },
  {
    link: "https://music.163.com/uservideo#/plan"
  }
]
```

src\components\app-footer\index.js

```jsx
import React, { memo, Fragment } from 'react';

import { footerLinks, footerImages } from "@/common/local-data";

import {
  AppFooterWrapper,
  FooterLeft,
  FooterRight,
} from './style';

export default memo(function HYAppFooter() {
  return (
  <AppFooterWrapper>
    <div className="wrap-v2 content">
      <FooterLeft className="left">
        <div className="link">
          {
            footerLinks.map(item => {
              return (
                <Fragment key={item.title}>
                  <a href={item.link} target="_blank" rel="noopener noreferrer">{item.title}</a>
                  <span className="line">|</span>
                </Fragment>
              )
            })
          }
        </div>
        <div className="copyright">
          <span>网易公司版权所有©1997-2020</span>
          <span>
            杭州乐读科技有限公司运营：
            <a href="https://p1.music.126.net/Mos9LTpl6kYt6YTutA6gjg==/109951164248627501.png" rel="noopener noreferrer" target="_blank">浙网文[2018]3506-263号</a>
          </span>
        </div>
        <div className="report">
          <span>违法和不良信息举报电话：0571-89853516</span>
          <span>
            举报邮箱：
            <a href="mailto:ncm5990@163.com" target="_blank" rel="noopener noreferrer">ncm5990@163.com</a>
          </span>
        </div>
        <div className="info">
          <span>粤B2-20090191-18&nbsp;&nbsp;</span>
          <a href="http://www.beian.miit.gov.cn/publish/query/indexFirst.action" rel="noopener noreferrer" target="_blank">
            工业和信息化部备案管理系统网站
          </a>
        </div>
      </FooterLeft>
      <FooterRight className="right">
        {
          footerImages.map((item, index) => {
            return (
              <li className="item" key={item.link}>
                <a className="link" href={item.link} rel="noopener noreferrer" target="_blank"> </a>
                <span className="title">{item.title}</span>
              </li>
            )
          })
        }
      </FooterRight>
    </div>
  </AppFooterWrapper>
  )
})

```

src\components\app-footer\style.js

```js
import styled from 'styled-components';

export const AppFooterWrapper = styled.div`
  height: 172px;
  background-color: #f2f2f2;
  color: #666;
  border-top: 1px solid #d3d3d3;

  .content {
    display: flex;
    justify-content: space-between;
    align-items: center;
  }
`

export const FooterLeft = styled.div`
  padding-top: 15px;
  line-height: 24px;

  .link {
    a {
      color: #999;
    }

    .line {
      margin: 0 10px;
      color: #999;
    }
    .line:last-child {
      display: none;
    }
  }

  .report {
    span:nth-child(1) {
      margin-right: 14px;
    }
  }

  .copyright {
    span {
      margin-right: 15px;
    }
  }
`

export const FooterRight = styled.ul`
  display: flex;

  .item {
    display: flex;
    flex-direction: column;
    align-items: center;
    margin-right: 40px;

    .link {
      display: block;
      width: 50px;
      height: 45px;

      background-image: url(${require("@/assets/img/sprite_footer_02.png").default});
      background-size: 110px 450px;
    }

    :nth-child(1) .link {
      background-position: -60px -101px;
    }
    :nth-child(2) .link {
      background-position: 0 0;
    }
    :nth-child(3) .link {
      background-position: -60px -50px;
    }
    :nth-child(4) .link {
      background-position: 0 -101px;
    }

    .title {
      margin-top: 5px;
      display: block;
      width: 52px;
      height: 10px;
      background-image: url(${require("@/assets/img/sprite_footer_01.png").default});
      background-size: 180px 100px;
    }

    :nth-child(1) .title {
      background-position: -1px -90px;
    }
    :nth-child(2) .title {
      background-position: 0 0;
      margin-top: 7px;
    }
    :nth-child(3) .title {
      background-position: 0 -54px;
      margin-top: 6px;
    }

    :nth-child(4) .title {
      background-position: -1px -72px;
      margin-top: 6px;
    }
  }
`
```

效果图

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1629974715626-52b64190-3320-4ac8-b013-ae109855587c.jpeg)

## 头部子路由

接下来做发现音乐中间的两部分，先搭建头部

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1629979639335-c10306cf-fb46-489a-8311-99b429c7a8a4.jpeg)

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1629980285877-cb6464d0-ec8f-42fa-8143-6b50763f93ab.jpeg)

src/common/local-data.js

```js
export const headerLinks = [
  {
    title: "发现音乐",
    link: "/discover"
  },
  {
    title: "我的音乐",
    link: "/mine"
  },
  {
    title: "朋友",
    link: "/friend"
  },
  {
    title: "商城",
    link: "https://music.163.com/store/product"
  },
  {
    title: "音乐人",
    link: "https://music.163.com/nmusician/web/index#/"
  },
  {
    title: "下载客户端",
    link: "https://music.163.com/#/download"
  }
]

export const footerLinks = [
  {
    title: "服务条款",
    link: "https://st.music.163.com/official-terms/service"
  },
  {
    title: "隐私政策",
    link: "https://st.music.163.com/official-terms/privacy"
  },
  {
    title: "儿童隐私政策",
    link: "https://st.music.163.com/official-terms/children"
  },
  {
    title: "版权投诉指引",
    link: "https://music.163.com/st/staticdeal/complaints.html"
  },
  {
    title: "意见反馈",
    link: "#"
  },
  {
    title: "广告合作",
    link: "mailto:yyyx@list.nie.netease.com"
  }
]

export const footerImages = [
  {
    link: "https://music.163.com/st/userbasic#/auth"
  },
  {
    link: "https://music.163.com/recruit"
  },
  {
    link: "https://music.163.com/web/reward"
  },
  {
    link: "https://music.163.com/uservideo#/plan"
  }
]

// discover 发现音乐中的数据
export const dicoverMenu = [
  {
    title: "推荐",
    link: "/discover/recommend"
  },
  {
    title: "排行榜",
    link: "/discover/ranking"
  },
  {
    title: "歌单",
    link: "/discover/songs"
  },
  {
    title: "主播电台",
    link: "/discover/djradio"
  },
  {
    title: "歌手",
    link: "/discover/artist"
  },
  {
    title: "新碟上架",
    link: "/discover/album"
  },
]
```

src/pages/discover/index.js

```jsx
import React, { memo } from 'react';

import { dicoverMenu } from "@/common/local-data";

import { NavLink } from 'react-router-dom';
import {
  DiscoverWrapper,
  TopMenu
} from './style';

export default memo(function HYDiscover(props) {
  return (
    <DiscoverWrapper>
      <div className="menu">
        <div className="top">
          <TopMenu className="wrap-v1">
            {
              dicoverMenu.map((item, index) => {
                return (
                  <div className="item" key={item.title}>
                    <NavLink to={item.link}>{item.title}</NavLink>
                  </div>
                )
              })
            }
          </TopMenu>
        </div>
      </div>
    </DiscoverWrapper>
  )
})
```

src/pages/discover/style.js

```js
import styled from 'styled-components';

export const DiscoverWrapper = styled.div`
  .top {
    height: 30px;
    background-color: #C20C0C;
  }
`

export const TopMenu = styled.div`
  display: flex;
  padding-left: 180px;
  position: relative;
  top: -4px;

  .item {
    a {
      display: inline-block;
      height: 20px;
      line-height: 20px;
      padding: 0 13px;
      margin: 7px 17px 0;
      color: #fff;

      &:hover, &.active {
        text-decoration: none;
        background-color: #9B0909;
        border-radius: 20px;
      }
    }
  }
`
```

效果图

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1629983661488-a2882788-6dac-4f80-94f6-6a3d2ad1bf0b.jpeg)

配置头部子路由

src/pages/discover/child-pages/album/index.js

```jsx
import React, { memo } from 'react'

export default memo(function HYAlbum() {
  return (
    <div>
      <h2>HYAlbum</h2>
    </div>
  )
})

```

src/pages/discover/child-pages/artist/index.js

```jsx
import React, { memo } from 'react'

export default memo(function HYArtist() {
  return (
    <div>
      <h2>HYArtist</h2>
    </div>
  )
})

```

src/pages/discover/child-pages/djradio/index.js

```jsx
import React, { memo } from 'react'

export default memo(function HYDjradio() {
  return (
    <div>
      <h2>HYDjradio</h2>
    </div>
  )
})

```

src/pages/discover/child-pages/ranking/index.js

```jsx
import React, { memo } from 'react'

export default memo(function HYRanking() {
  return (
    <div>
      <h2>HYRanking</h2>
    </div>
  )
})
```

src/pages/discover/child-pages/recommend/index.js

```jsx
import React, { memo } from 'react'

export default memo(function HYRecommend() {
  return (
    <div>
      <h2>HYRecommend</h2>
    </div>
  )
})
```

src/pages/discover/child-pages/songs/index.js

```jsx
import React, { memo } from 'react'

export default memo(function HYSongs() {
  return (
    <div>
      <h2>HYSongs</h2>
    </div>
  )
})
```

src/router/index.js

```js
import React from 'react';
import { Redirect } from "react-router-dom";

import HYDiscover from "@/pages/discover";
import HYRecommend from "../pages/discover/child-pages/recommend";
import HYRanking from "../pages/discover/child-pages/ranking";
import HYSongs from "../pages/discover/child-pages/songs";
import HYDjradio from "../pages/discover/child-pages/djradio";
import HYArtist from "../pages/discover/child-pages/artist";
import HYAlbum from "../pages/discover/child-pages/album";

import HYMine from "@/pages/mine";
import HYFriend from "@/pages/friend";

const routes = [
  {
    path: "/",
    exact: true,
    render: () => (
      <Redirect to="/discover"/>
    )
  },
  {
    path: "/discover",
    component: HYDiscover,
    routes: [
      {
        path: "/discover",
        exact: true,
        render: () => (
          <Redirect to="/discover/recommend"/>
        )
      },
      {
        path: "/discover/recommend",
        component: HYRecommend
      },
      {
        path: "/discover/ranking",
        component: HYRanking
      },
      {
        path: "/discover/songs",
        component: HYSongs
      },
      {
        path: "/discover/djradio",
        exact: true,
        component: HYDjradio
      },
      {
        path: "/discover/artist",
        component: HYArtist
      },
      {
        path: "/discover/album",
        component: HYAlbum
      }
    ]
  },
  {
    path: "/mine",
    component: HYMine
  },
  {
    path: "/friend",
    component: HYFriend
  },
];

export default routes;
```

src/pages/discover/index.js

```jsx
import React, { memo } from 'react';
import { renderRoutes } from 'react-router-config';

import { dicoverMenu } from "@/common/local-data";

import { NavLink } from 'react-router-dom';
import {
  DiscoverWrapper,
  TopMenu
} from './style';

export default memo(function HYDiscover(props) {
  const { route } = props;

  return (
    <DiscoverWrapper>
      <div className="top">
        <TopMenu className="wrap-v1">
          {
            dicoverMenu.map((item, index) => {
              return (
                <div className="item" key={item.title}>
                  <NavLink to={item.link}>{item.title}</NavLink>
                </div>
              )
            })
          }
        </TopMenu>
      </div>
      {renderRoutes(route.routes)}
    </DiscoverWrapper>
  )
})
```

效果图

![img](https://cdn.nlark.com/yuque/0/2021/gif/1614731/1629986237838-409b7ee4-8224-42e4-822e-00050da139bd.gif)

## 推荐页面

该页面需要展示大量数据，所以需要先安装 axios ，然后封装一下 axios

```shell
yarn add axios
```

src/services/config.js

```js
// 开发环境 url
const devBaseURL = "http://123.207.32.32:9001";
// 生产环境 url
const proBaseURL = "http://123.207.32.32:9001";
export const BASE_URL = process.env.NODE_ENV === 'development' ? devBaseURL: proBaseURL;

export const TIMEOUT = 5000;
```

src/services/request.js

```js
import axios from 'axios';

import { BASE_URL, TIMEOUT } from "./config";

const instance = axios.create({
  baseURL: BASE_URL,
  timeout: TIMEOUT
});

instance.interceptors.request.use(config => {
  // 1.发送网络请求时, 在界面的中间位置显示Loading的组件

  // 2.某一些请求要求用户必须携带token, 如果没有携带, 那么直接跳转到登录页面

  // 3.params/data序列化的操作

  return config;
}, err => {

});

instance.interceptors.response.use(res => {
  return res.data;
}, err => {
  if (err && err.response) {
    switch (err.response.status) {
      case 400:
        console.log("请求错误");
        break;
      case 401:
        console.log("未授权访问");
        break;
      default:
        console.log("其他错误信息");
    }
  }
  return err;
});

export default instance;
```

对于服务器返回的数据，使用 Redux 进行管理，而不是放在组件里

```shell
yarn add redux react-redux redux-thunk
```

数据对应的 reducer 必然是要拆分的，否则难以维护

每一个子路由页面（归为一体的模块）都是一个 reducer

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1629989168473-026619aa-74ab-40e9-935d-1ccfbe2bb997.jpeg)

有多个 reducer 就需要在一个地方合并所有的 reducer，所以需要在 store 目录下创建一个 reducer.js

createStore() 第一个参数存放合并的 reducer，第二个参数存放加强扩展的中间件

### 初始化 redux 与 redux-thunk

src/store/reducer.js 合并reducer

```js
import { combineReducers } from "redux";

const cReducer = combineReducers({

})

export default cReducer
```

src/store/index.js 集成中间件

```js
import { createStore, applyMiddleware, compose } from "redux";
import thunk from "redux-thunk";
import reducer from "./reducer";

// 使插件可追踪 store 状态
const composeEnhancers = window.__REDUX_DEVTOOLS_EXTENSION_COMPOSE__({trace: true}) || compose;
// 创建 store 集中管理
const store = createStore(reducer, composeEnhancers(applyMiddleware(thunk)));

export default store;
```

App.js

```jsx
import React, { memo } from 'react';
import { Provider } from "react-redux";
import { renderRoutes } from 'react-router-config';

import routes from './router';
import store from "./store";

import HYAppHeader from "components/app-header";
import HYAppFooter from "components/app-footer";
import { HashRouter } from 'react-router-dom'

export default memo(function App() {
  return (
    // 使用 Provider，让包裹的组件使用 connect 来获取 store 的数据
    <Provider store={store}>
      <HashRouter>
        <HYAppHeader/>
        {renderRoutes(routes)}
        <HYAppFooter/>
      </HashRouter>
    </Provider>
  )
})
```

### 导入推荐子页面的 reducer

在推荐子页面目录下新建 store 目录

src/pages/discover/child-pages/recommend/store

在 store 目录下新建 actionCreators.js 、constant.js、index.js、reducer.js

store/constant.js

```js
export const CHANGE_TOP_BANNERS = "recommend/CHANGE_TOP_BANNERS";
```

store/reducer.js

```js
import * as actionTypes from "./constants";

const defaultState = {
  topBanners: [],
};

function reducer(state = defaultState, action) {
  switch (action.type) {
    case actionTypes.CHANGE_TOP_BANNERS:
      return {...state, topBanners: []}
    default:
      return state;
  }
}

export default reducer;
```

store/index.js

```js
import reducer from './reducer';

export {
  reducer
}
```

src/store/reducer.js

```jsx
import { combineReducers } from "redux";

import { reducer as recommendReducer } from '../pages/discover/child-pages/recommend/store/index';

const cReducer = combineReducers({
  recommend: recommendReducer
})

export default cReducer
```

效果图

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1629995004030-e8b365bc-478d-4ff3-92dc-1c0f6f4bb9d9.jpeg)

### 推荐子页面通过 redux-thunk 发送网络请求

虽然现在有了 Redux，但是还没有发送网络请求，

我们应该在 recommend 的某一个位置发送网络请求，

在 redux-thunk 中间件发送网络请求之后，拿到 data ，再 dispatch 一个 action 对象，

action 对象包含着数据，然后在 reducer 里进行数据的合并更新。

只有发送网络请求的部分属于 redux-thunk（下图红色箭头），

redux-thunk 传入一个函数给 dispatch(函数) 然后返回一个函数，

其它部分属于 redux

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1630070120086-bbf8ff90-355c-4c15-8084-bc9e1f2c2999.jpeg)

这个函数将与下图红色框部分放在 actionCreators 里

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1630070629514-3c2c7fdb-747b-4870-9f33-20ed5a317aeb.jpeg)

函数本质上是一个 action，所以要加上 Action 后缀名：getTopBannerAction 。

src/pages/discover/child-pages/recommend/store/actionCreators.js

```js
import * as actionTypes from './constants';

import { getTopBanners } from '@/services/recommend';

export const getTopBannerAction = () => {
  return dispatch => {
    getTopBanners().then(res => {
      console.log(res);
    })
  }
};
```

发送网络请求时不是直接导入使用 request.js，

而是针对每一个模块在 services 目录下新建一个 recommend.js

src/services/recommend.js

```js
import request from './request';

export function getTopBanners() {
  return request({
    url: "/banner"
  })
}
```

那么如何调用 getTopBannerAction 打印响应的数据呢？

在 src/pages/discover/child-pages/recommend/index.js 调用的话，

怎么才能拿到 getTopBannerAction 的 dispatch 呢？

**使用 connect 在 React 中使用 Redux**

1、获取数据 

2 、进行 dispatch 操作

注意：dispatch(函数调用) ，而不是 dispatch(函数声明)，

因为 getTopBannerAction = () => 的 () 可能需要添加额外的参数，根据额外的参数来生成另一个函数。

src/pages/discover/child-pages/recommend/index.js

```jsx
import React, { memo } from 'react';
import { connect } from "react-redux";

import { getTopBannerAction } from "./store/actionCreators"

function HYRecommend(props) {
  const { getBanners } = props;
  
  useEffect(() => {
    getBanners();
  }, [getBanners])

  return (
    <div>
      <h2>HYRecommend</h2>
    </div>
  )
}

const mapStateToProps = state => ({
  topBanners: state.recommend.topBanners
});

const mapDispatchToProps = dispatch => ({
  getBanners: () => {
    dispatch(getTopBannerAction())
  }
})

export default connect(mapStateToProps, mapDispatchToProps)(memo(HYRecommend));
```

效果图

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1630073966039-f96833a2-f998-49e1-8977-dbdaf1323814.jpeg)

**将请求放进 reducer 里变成可追踪的**

src/pages/discover/child-pages/recommend/store/actionCreators.js

```js
import * as actionTypes from './constants';

import { getTopBanners } from '@/services/recommend';

const changeTopBannerAction = (res) => ({
  type: actionTypes.CHANGE_TOP_BANNERS,
  topBanners: res.banners
});

export const getTopBannerAction = () => {
  return dispatch => {
    getTopBanners().then(res => {
      dispatch(changeTopBannerAction(res));
    })
  }
};
```

src/pages/discover/child-pages/recommend/store/reducer.js

```js
import * as actionTypes from "./constants";

const defaultState = {
  topBanners: [],
};

function reducer(state = defaultState, action) {
  switch (action.type) {
    case actionTypes.CHANGE_TOP_BANNERS:
      return {...state, topBanners: action.topBanners}
    default:
      return state;
  }
}

export default reducer;
```

效果图

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1630075020013-8f7d5817-8bfe-4c1d-83b9-74bdc05bbfbc.jpeg)

**获取映射到 recommend 函数组件里的数据**

打印

```
{topBanners.length}
```

src/pages/discover/child-pages/recommend/index.js

```jsx
import React, { memo, useEffect } from 'react';
import { connect } from "react-redux";

import { getTopBannerAction } from "./store/actionCreators"

function HYRecommend(props) {
  const {getBanners, topBanners} = props;

  useEffect(() => {
    getBanners();
  }, [getBanners])

  return (
    <div>
      <h2>HYRecommend:{topBanners.length}</h2>
    </div>
  )
}

// state 参数为 reducer 合并后的 state
const mapStateToProps = state => ({
  topBanners: state.recommend.topBanners
});

const mapDispatchToProps = dispatch => ({
  getBanners: () => {
    dispatch(getTopBannerAction())
  }
})

export default connect(mapStateToProps, mapDispatchToProps)(memo(HYRecommend));
```

效果图

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1630075463608-c12bc64e-1b42-4435-a7d8-0d90a70a5f61.jpeg)

### 优化代码

#### 使用 redux hooks 

每个子路由下的组件都需要和 Redux 联系起来的话，那么以下代码写起来将会相当繁琐

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1630411407215-8aa5400c-a0eb-4717-8a82-e07754df894e.jpeg)

所以，需要使用 redux hooks 优化这些代码

src/pages/discover/child-pages/recommend/index.js

```jsx
import React, { memo, useEffect } from 'react';
import { useSelector, useDispatch, shallowEqual } from "react-redux";

import { getTopBannerAction } from "./store/actionCreators"

function HYRecommend(props) {

  // 组件和 redux 关联：1、获取数据 2 、进行 dispatch 操作
  // react-redux hooks ：useSelector 和 useDispatch
  // useSelector 与 mapStateToProps 效果类似：
  // 订阅 state 的更新，将 state 映射到函数组件的 props
  // 第一个参数为回调函数，回调函数的返回值作为 useSelector 的返回值（必须是对象）
  // 下面回调函数的 state 为 combineReducers 参数里的对象
  // useSelector 第二个参数建议加上 shallowEqual 进行浅比较的函数，用于决定组件是否重新渲染。否则默认是用 === 比较。
  const { topBanners } = useSelector(state => ({
    topBanners: state.recommend.topBanners
  }), shallowEqual);
  // 获取 dispatch 对象
  const dispatch = useDispatch();

  // 发送网络请求
  // dispatch 会引起组件重新渲染，所以会要求 dispatch 放进依赖数组，否则会有警告
  // 一般 dispatch 不会变化
  useEffect(() => {
    dispatch(getTopBannerAction())
  }, [dispatch])

  return (
    <div>
      <h2>HYRecommend:{topBanners.length}</h2>
    </div>
  )
}

export default memo(HYRecommend);
```

代码还存在一些性能问题，比如每次改变一点点数据就要浅拷贝大量数据，消耗性能、浪费内存

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1630415719319-68579b9e-7539-4b03-9541-0315b07768c9.jpeg)

这时就需要 Immutable.js 库

####  immutable

```shell
yarn add immutable
```

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1630416101271-3c1b263e-6c6f-4693-be29-03fb853e8128.jpeg)

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1630416489615-30febab3-576e-49c0-92e9-cef27c4fec25.jpeg)

![img](https://cdn.nlark.com/yuque/0/2021/gif/1614731/1630747559317-04fd8d5b-cafd-4164-bf58-8b3bfcfd0cbd.gif)

Immutable.js 的使用

```javascript
const im = Immutable;

// Map的使用
// const info = {
//   name: "kobe",
//   age: 30,
//   friend: {
//     name: "james",
//     age: 25
//   }
// }

// const infoIM = im.Map(info);

// const obj = infoIM;
// const infoIM2 = infoIM.set("name", "why");
// 旧数据
// console.log(obj.get("name"));
// 新数据
// console.log(infoIM2.get("name"));

// List的使用
// const names = ["abc", "cba", "nba"];
// // const arr = names;
// // names[0] = "why";
// // console.log(arr);
// const namesIM = im.List(names);
// const arrIM = namesIM.set(0, "why");
// 旧数据
// console.log(namesIM.get(0));
// 新数据
// console.log(arrIM.get(0));

const info = {
  name: "kobe",
  age: 30,
  friend: {
    name: "james",
    age: 25
  }
}

// const infoIM = im.Map(info);
// console.log(infoIM.get("friend"));
// 数据转成 Immutable 类型，深层转化，深层修改时使用
const infoIM = im.fromJS(info);
console.log(infoIM.get("friend"));
```

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1630417235641-72d5704c-b20e-40df-8485-01cd946f52ad.jpeg)

注重手动性能优化的细节，比如 memo

服务器返回的数据使用 Immutable.js 的 map() 将原有的 state 改造为 Immutable 对象就可以了

```js
// 设置值
state.set("topBanners", action.topBanners);
```

src/pages/discover/child-pages/recommend/store/reducer.js

```js
import { Map } from "immutable";

import * as actionTypes from "./constants";

const defaultState = Map({
  topBanners: [],
})

function reducer(state = defaultState, action) {
  switch (action.type) {
    case actionTypes.CHANGE_TOP_BANNERS:
      return state.set("topBanners", action.topBanners);
    default:
      return state;
  }
}

export default reducer;
```

合并 reducer 时也是每次返回新的对象，使用 ... 扩展运算符并不合适

但是如果使用 Map() 包裹 reducer ，它的操作是非常频繁的，

而且 map() 之后 combineReducers() 对 Immutable 对象使用 Object.keys() 会有问题

```js
const cReducer = combineReducers(Map({
  recommend: recommendReducer
})
```

所以需要引入 redux-immutable

```shell
yarn add redux-immutable
```

src/store/reducer.js

import { combineReducers } from "immutable";  改为

import { combineReducers } from "redux-immutable"; 

```js
import { combineReducers } from "redux-immutable";

import { reducer as recommendReducer } from '../pages/discover/child-pages/recommend/store/index';

const cReducer = combineReducers({
  recommend: recommendReducer
})

export default cReducer
```

之后 state.recommend.topBanners 需改为 state.get("recommend").get("topBanners")

src/pages/discover/child-pages/recommend/index.js

```js
import React, { memo, useEffect } from 'react';
import { useSelector, useDispatch, shallowEqual } from "react-redux";

import { getTopBannerAction } from "./store/actionCreators"

function HYRecommend(props) {
  const { topBanners } = useSelector(state => ({
    topBanners: state.get("recommend").get("topBanners")
  }), shallowEqual);
  
  const dispatch = useDispatch();

  useEffect(() => {
    dispatch(getTopBannerAction())
  }, [dispatch])

  return (
    <div>
      <h2>HYRecommend:{topBanners.length}</h2>
    </div>
  )
}

export default memo(HYRecommend);
```

两次 get() 写起来麻烦，使用 getIn() 语法糖简化 ：

```js
import React, { memo, useEffect } from 'react';
import { useSelector, useDispatch, shallowEqual } from "react-redux";

import { getTopBannerAction } from "./store/actionCreators"

function HYRecommend(props) {
  const { topBanners } = useSelector(state => ({
    topBanners: state.getIn(["recommend", "topBanners"])
  }), shallowEqual);

  const dispatch = useDispatch();

  useEffect(() => {
    dispatch(getTopBannerAction())
  }, [dispatch])

  return (
    <div>
      <h2>HYRecommend:{topBanners.length}</h2>
    </div>
  )
}

export default memo(HYRecommend);
```

### 轮播图

使用 antd 组件实现渐入渐出的轮播图，轮播图分为两部分：图片+背景色

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1630830045108-7ab4d18f-4482-47eb-97b3-dcb8447f6336.jpeg)

搭建代码结构，在 recommend 目录下创建 child-cpns 目录，

在 child-cpns 目录下创建 top-banner 组件目录

目录结构

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1630838038541-3923f4ab-b878-4206-92a3-cae8daa0211f.jpeg)

把获取 topBanners 数据的逻辑交给对应的组件 top-banner 管理 

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1630838050779-5aa38c61-b5a3-45bd-838f-e67ed04b2cd4.jpeg)

src/pages/discover/child-pages/recommend/child-cpns/top-banner/index.js

```js
import React, { memo, useEffect } from 'react';
import { useSelector, useDispatch, shallowEqual } from "react-redux";

import { getTopBannerAction } from "../../store/actionCreators";

import { 
  BannerWrapper, 
  BannerLeft, 
  BannerRight,
  BannerControl 
} from './style';

export default memo(function HYTopBanner() {
  const { topBanners } = useSelector(state => ({
    topBanners: state.getIn(["recommend", "topBanners"])
  }), shallowEqual);

  const dispatch = useDispatch();

  useEffect(() => {
    dispatch(getTopBannerAction())
  }, [dispatch])

  return (
    <BannerWrapper>
      <div className="banner wrap-v2">
        <BannerLeft></BannerLeft>
        <BannerRight></BannerRight>
      </div>
    </BannerWrapper>
  )
})
```

src/pages/discover/child-pages/recommend/child-cpns/top-banner/style.js

```js
import styled from 'styled-components';

export const BannerWrapper = styled.div`
  background: url(${props => props.bgImage}) center center/6000px;

  .banner {
    height: 270px;
    background-color: red;

    display: flex;
    position: relative;
  }
`

export const BannerLeft = styled.div`
  width: 730px;

  .banner-item {
    display: block;
    height: 270px;
    cursor: pointer;
    .image {
      width: 100%;
    }
  }
`

export const BannerRight = styled.a.attrs({
  href: "https://music.163.com/#/download",
  target: "_blank"
})`
  width: 254px;
  height: 270px;
  background: url(${require("@/assets/img/download.png").default});
`

export const BannerControl = styled.div`
  position: absolute;
  left: 0;
  right: 0;
  top: 50%;
  transform: translateY(-50%);

  .btn {
    position: absolute;
    width: 37px;
    height: 63px;
    background-image: url(${require("@/assets/img/banner_sprite.png").default});
    background-color: transparent;
    cursor: pointer;

    &:hover {
      background-color: rgba(0, 0, 0, .1);
    }
  }

  .left {
    left: -68px;
    background-position: 0 -360px;
  }

  .right {
    right: -68px;
    background-position: 0 -508px;
  }
`
```

src/pages/discover/child-pages/recommend/index.js

```js
import React, { memo } from 'react';

import { RecommendWrapper } from  "./stlye"; 
import HYTopBanner from "./child-cpns/top-banner";

function HYRecommend(props) {

  return (
    <RecommendWrapper>
      <HYTopBanner/>
    </RecommendWrapper>
  )
}

export default memo(HYRecommend);
```

src/pages/discover/child-pages/recommend/style.js

```js
import styled  from "styled-components";

export const RecommendWrapper = styled.div`

`
```

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1630838432426-fe77883a-732b-4c0d-9d7e-5dac7bcc5dcf.jpeg)

之前忘了在 reset.css 引入 antd 样式，

```css
@import "~antd/dist/antd.css";
```

导入 Input 组件 和 Carousel 组件时

导致 app-heder 组件 与 top-banner 组件出现位置样式问题，

以及 wrap-v1 、wrap-v2 公共类名没引入，

导致 HYDiscover 组件与  top-banner 组件出现位置样式问题，

所以又把之前的代码调整了一下（需要注意细节）



通过 ref 拿到轮播图组件箭头才能控制轮播图的前进 prev() 、后退 next() 。

通过 beforeChange 监听轮播图的切换（切换之前回调），控制高斯模糊的背景图切换。

如果要把函数作为参数传入一个组件时要使用 useCallback() ，防止组件重新渲染。

src/pages/discover/child-pages/recommend/child-cpns/top-banner/index.js

```js
import React, { memo, useState, useEffect, useRef, useCallback } from 'react';
import { useSelector, useDispatch, shallowEqual } from "react-redux";

import { getTopBannerAction } from "../../store/actionCreators";

import { Carousel } from "antd";
import { 
  BannerWrapper, 
  BannerLeft, 
  BannerRight,
  BannerControl
} from './style';

export default memo(function HYTopBanner() {
  // state
  const [currentIndex, setCurrentIndex] = useState(0);

  // 组件和redux关联: 获取数据和进行操作
  const { topBanners } = useSelector(state => ({
    topBanners: state.getIn(["recommend", "topBanners"])
  }), shallowEqual);
  
  const dispatch = useDispatch();
  useEffect(() => {
    dispatch(getTopBannerAction())
  }, [dispatch])

  // 其它 hooks
  const bannerRef = useRef();

  const bannerChange = useCallback((from, to) => {
      setCurrentIndex(to)
    }, [])
  
  // 其它业务逻辑
  const bgImage = topBanners[currentIndex] && 
  (topBanners[currentIndex].imageUrl + '?imageView&blur=40x20')

  return (
    <BannerWrapper bgImage={bgImage}>
      <div className="banner wrap-v2">
        <BannerLeft>
          <Carousel effect="fade" autoplay ref={bannerRef} beforeChange={bannerChange}>
            {
              topBanners.map((item, index) => {
                return (
                  <a href="localhost:3000" className="banner-item" key={item.targetId}>
                    <img className="image" src={item.imageUrl} alt={item.typeTitle} />
                  </a>
                )
              })
            }
          </Carousel>
        </BannerLeft>
        <BannerRight></BannerRight>
        <BannerControl>
          <div className="btn left" onClick={e => {bannerRef.current.prev()}}></div>
          <div className="btn right" onClick={e => {bannerRef.current.next()}}></div>
        </BannerControl>
      </div>
    </BannerWrapper>
  )
})
```

效果图

![img](https://cdn.nlark.com/yuque/0/2021/gif/1614731/1630861215713-2be9d12f-f8a4-4786-8359-67e4d1ac17c3.gif)

### 热门推荐

热门推荐分为左右两部分：

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1631010848964-8a89cc90-8550-47a1-948c-5b0ce8df52f9.jpeg)

先从 HYRecommend 开始，

**依然是从上层组件开始编写，父组件已经写过一次 Wrapper 接下来使用 Content 或其它命名， **

**子组件先写 Wrapper，再拆分布局**

src/pages/discover/child-pages/recommend/style.js

```js
import styled  from "styled-components";

export const RecommendWrapper = styled.div`

`

export const Content = styled.div`
  background-color: #fff;
  display: flex;
`

export const RecommendLeft = styled.div`
  padding: 20px;
  width: 729px;
`

export const RecommendRight = styled.div`
  width: 250px;
  border: 1px solid #d3d3d3;
  border-width: 0 1px;
`
```

src/pages/discover/child-pages/recommend/index.js

```js
import React, { memo } from 'react';

import { 
  RecommendWrapper,
  Content,
  RecommendLeft,
  RecommendRight 
} from  "./stlye"; 
import HYTopBanner from "./child-cpns/top-banner";
import HYHotRecommend from './child-cpns/hot-recommend';
import HYNewAlbum from './child-cpns/new-album';
import HYRecommendRanking from './child-cpns/recommend-ranking'

function HYRecommend(props) {

  return (
    <RecommendWrapper>
      <HYTopBanner/>
      <Content class="wrap-v2 content">
        <RecommendLeft>
          <HYHotRecommend/>
          <HYNewAlbum/>
          <HYRecommendRanking/>
        </RecommendLeft>
        <RecommendRight></RecommendRight>
      </Content>
    </RecommendWrapper>
  )
}

export default memo(HYRecommend);
```

左边部分又可以分为上下两部分，上部分为 Header

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1631010849570-5524c085-a729-41f5-8952-0f1fd1edf1d0.jpeg)

Header 部分又可以给下面其它组件复用，只有中间一些歌曲分类的标签不一样而已

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1631010849455-3b8e3984-9be5-42d7-9d46-f2d0ea5208cf.jpeg)

所以可以在 src/components 里封装为公共组件，

为了防止公共组件判断逻辑过多，推荐页面每个子路由下内容里的 Header 都会抽成各自一个，

而不是全部放一个公共组件里

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1631010849431-206295d7-fae0-4144-96b2-91ace0ee6730.jpeg)

所以取名为 theme-heder-rcm 表示热门推荐专属 Header

Heder 部分又可以分为左右两部分，左边又可以分为三部分

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1631010849432-1a2968a9-af60-479e-ad49-e45614950288.jpeg)

项目里先把界面展示做好，再处理跳转等逻辑

#### 封装公共的头部组件

**公共头部组件 theme-header-rcm**

src/components/theme-header-rcm/style.js

```js
import styled from 'styled-components';

export const HeaderWrapper = styled.div`
  height: 33px;
  border-bottom: 2px solid #C10D0C;
  padding: 0 10px 4px 34px;
  background-position: -225px -156px;

  display: flex;
  justify-content: space-between;
  align-items: center;

  .left {
    display: flex;
    align-items: center;

    .title {
      font-size: 20px;
      font-family: "Microsoft Yahei", Arial, Helvetica, sans-serif;
      margin-right: 20px;
    }

    .keyword {
      display: flex;

      .item {
        .divider {
          margin: 0 15px;
          color: #ccc;
        }
      }
    }
  }

  .right {
    display: flex;
    align-items: center;
    .icon {
      display: inline-block;
      width: 12px;
      height: 12px;
      margin-left: 4px;
      background-position: 0 -240px;
    }
  }
`
```

src/components/theme-header-rcm/index.js

```js
import React, { memo } from 'react';
import PropTypes from "prop-types";

import { HeaderWrapper } from './style';

export const HYThemeHeaderRCM = memo(function(props) {
  const { title, keywords } = props

  return (
    <div>
      <HeaderWrapper className="sprite_02">
        <div className="left">
          <h3 className="title">{title}</h3>
          <div className="keyword">
            {
              keywords.map((item, index) => {
                return(
                  <div className="item" key={index}>
                    <a href="localhost:3000">{item}</a>
                    <span className="divider">|</span>
                  </div>
                )      
              })
            }
          </div>
          
        </div>
        <div className="right">
          <a href="localhost:3000">更多</a>
          <i className="icon sprite_02"></i>
        </div>
      </HeaderWrapper>
    </div>
  )
})

HYThemeHeaderRCM.propTypes = {
  title: PropTypes.string.isRequired,
  keywords: PropTypes.array
}

HYThemeHeaderRCM.defaultProps = {
  keywords: []
}
```

ES6 结构对象时可以赋默认值来避免漏传 props 导致传的是 undefined 从而页面崩溃

```jsx
const { titile, keywords = [] } = props
```

也可以使用 propTypes 校验

**热门推荐头部组件**

src/pages/discover/child-pages/recommend/child-cpns/hot-recommend/style.js

```js
import styled from "styled-components";

export const HotRecommendWrapper = styled.div`
  .recommend-list {
    display: flex;
    flex-wrap: wrap;
    justify-content: space-between;
  }
`
```

src/pages/discover/child-pages/recommend/child-cpns/hot-recommend/index.js

```js
import React, { memo } from 'react';

import { HotRecommendWrapper } from './style'
import { HYThemeHeaderRCM } from "@/components/theme-header-rcm"

export default memo(function HYHotRecommend() {
  return (
    <HotRecommendWrapper>
      <HYThemeHeaderRCM title="热门推荐" keywords={['华语', '流行', '民谣', '摇滚', '电子']}/>
    </HotRecommendWrapper>
  )
})
```

**新碟上架头部组件**

src/pages/discover/child-pages/recommend/child-cpns/new-album/style.js

```js
import styled from "styled-components";

export const AlbumWrapper = styled.div`
  margin-top: 50px;

  .content {
    height: 186px;
    background-color: #f5f5f5;
    border: 1px solid #d3d3d3;
    margin: 20px 0 37px;
    display: flex;
    align-items: center;

    .arrow {
      width: 25px;
      height: 25px;
      cursor: pointer;
    }

    .arrow-left {
      background-position: -260px -75px;
    }

    .arrow-right {
      background-position: -300px -75px;
    }

    .album {
      width: 640px;
      height: 150px;

      .ant-carousel .slick-slide {
        height: 150px;
        overflow: hidden;
      }

      .page {
        display: flex !important;
        justify-content: space-between;
        align-items: center;
      }
    }
  }
`
```

src/pages/discover/child-pages/recommend/child-cpns/new-album/index.js

```js
import React, { memo } from 'react';

import { AlbumWrapper } from './style'
import { HYThemeHeaderRCM } from "@/components/theme-header-rcm"

export default memo(function HYNewAlbum() {
  return (
    <AlbumWrapper>
      <HYThemeHeaderRCM title="新碟上架"/>
    </AlbumWrapper>
  )
})

```

**榜单头部组件**

src/pages/discover/child-pages/recommend/child-cpns/recommend-ranking/style.js

```js
import styled from 'styled-components';

export const BannerWrapper = styled.div`
  background: url(${props => props.bgImage}) center center/6000px;

  .banner {
    height: 270px;
    background-color: red;

    display: flex;
    position: relative;
  }
`

export const BannerLeft = styled.div`
  width: 730px;

  .banner-item {
    overflow: hidden;
    height: 270px;
    .image {
      width: 100%;
    }
  }
`

export const BannerRight = styled.a.attrs({
  href: "https://music.163.com/#/download",
  target: "_blank"
})`
  width: 254px;
  height: 270px;
  background: url(${require("@/assets/img/download.png")});
`

export const BannerControl = styled.div`
  position: absolute;
  left: 0;
  right: 0;
  top: 50%;
  transform: translateY(-50%);

  .btn {
    position: absolute;
    width: 37px;
    height: 63px;
    background-image: url(${require("@/assets/img/banner_sprite.png")});
    background-color: transparent;
    cursor: pointer;

    &:hover {
      background-color: rgba(0, 0, 0, .1);
    }
  }

  .left {
    left: -68px;
    background-position: 0 -360px;
  }

  .right {
    right: -68px;
    background-position: 0 -508px;
  }
`
```

src/pages/discover/child-pages/recommend/child-cpns/recommend-ranking/index.js

```js
import React, { memo } from 'react';

import { BannerWrapper } from './style'
import { HYThemeHeaderRCM } from "@/components/theme-header-rcm"

export default memo(function HYRecommendRanking() {
  return (
    <BannerWrapper>
      <HYThemeHeaderRCM title="榜单"/>
    </BannerWrapper>
  )
})

```

效果图

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1631011956989-6e18604e-a913-4656-8cd3-8b5a3a290d6c.jpeg)

#### 请求歌曲封面数据并使用 Redux 处理

接下来就是把数据请求下来，放到 Redux 里，然后展示数据

第一步操作，先在 service 目录下写好网络请求函数

网易云音乐接口可以传 limit 参数限制传过来的数据条数

src/services/recommend.js

```js
import request from './request';

export function getTopBanners() {
  return request({
    url: "/banner"
  })
}

export function getHotRecommends(limit) {
  return request({
    url: "/personalized",
    params: {
      limit
    }
  })
}
```

第二步操作就是来到 recommend （即 HYRecommend 组件）的 store 目录下

修改 reducer.js 和 constans.js 

先写 getHotRecommendAction 再写 changeHotRecommendAction ，

最后在 getHotRecommendAction 里 dispatch(changeHotRecommendAction)

src/pages/discover/child-pages/recommend/store/constant.js

```js
export const CHANGE_TOP_BANNERS = "recommend/CHANGE_TOP_BANNERS";
export const CHANGE_HOT_RECOMMENDS = "recommend/CHANGE_HOT_RECOMMENDS";
```

src/pages/discover/child-pages/recommend/store/reducer.js

```js
import { Map } from "immutable";

import * as actionTypes from "./constants";

const defaultState = Map({
  topBanners: [],
  hotRecommends: []
})

function reducer(state = defaultState, action) {
  switch (action.type) {
    case actionTypes.CHANGE_TOP_BANNERS:
      return state.set("topBanners", action.topBanners);
    case actionTypes.CHANGE_HOT_RECOMMENDS:
      return state.set("hotRecommends", action.hotRecommends);
    default:
      return state;
  }
}

export default reducer;
```

src/pages/discover/child-pages/recommend/store/actionCreators.js

```js
import * as actionTypes from './constants';

import { getTopBanners, getHotRecommends } from '@/services/recommend';

const changeTopBannerAction = (res) => ({
  type: actionTypes.CHANGE_TOP_BANNERS,
  topBanners: res.banners
});

const changeHotRecommendAction = (res) => ({
  type: actionTypes.CHANGE_HOT_RECOMMENDS,
  hotRecommends: res.result
});

export const getTopBannerAction = () => {
  return dispatch => {
    getTopBanners().then(res => {
      dispatch(changeTopBannerAction(res));
    })
  }
};

export const getHotRecommendAction = (limit) => {
  return dispatch => {
    getHotRecommends(limit).then(res => {
      dispatch(changeHotRecommendAction(res));
    })
  }
};
```

第三步操作，来到子组件 hot-recommend 目录下 修改 index.js 展示数据 

先写 useDispatch() 和 useEffect() ，最后 reducer 里 dispatch 后使用 useSelector() 获取数据

src/pages/discover/child-pages/recommend/child-cpns/hot-recommend/index.js

```js
import React, { memo, useEffect } from 'react';
import { useSelector, useDispatch, shallowEqual } from "react-redux";

import { HotRecommendWrapper } from './style'
import { HYThemeHeaderRCM } from "@/components/theme-header-rcm"
import HYSongsCover from "@/components/songs-cover"

import { getHotRecommendAction } from "../../store/actionCreators"

export default memo(function HYHotRecommend() {
  // state

  // redux hooks
  const { hotRecommends } = useSelector(state => ({
    hotRecommends: state.getIn(["recommend", "hotRecommends"])
  }), shallowEqual);

  const dispatch = useDispatch();

  // other hooks
  useEffect(() => {
    dispatch(getHotRecommendAction(8))
  }, [dispatch])

  return (
    <HotRecommendWrapper>
      <HYThemeHeaderRCM title="热门推荐" keywords={['华语', '流行', '民谣', '摇滚', '电子']}/>
      <div className="recommend-list">
        {
          hotRecommends.map((item, index) => {
            return <HYSongsCover key={item.id} info={item} source={false} ellipsis={false}/>
          })
        }
      </div>
    </HotRecommendWrapper>
  )
})
```

redux 处理数据请求过程：

```js
// 所有推荐页面的数据（轮播图、热门推荐）都放到一个 redux 的 reducer 里，
// 即 recommend 的 reducer
// src/pages/discover/child-pages/recommend/store/reducer.js

// 1、组件挂载完成触发 dispatch，dispatch(函数action) 来请求数据
// src/pages/discover/child-pages/recommend/child-cpns/hot-recommend/index.js
const dispatch = useDispatch();
useEffect(() => {
  dispatch(getHotRecommendAction(8))
}, [dispatch])

// src/services/recommend.js
export function getHotRecommends(limit) {
  return request({
    url: "/personalized",
    params: {
      limit
    }
  })
} 

// 2、定义对象 action 的类型 
// src/pages/discover/child-pages/recommend/store/constants.js
export const CHANGE_HOT_RECOMMENDS = "recommend/CHANGE_HOT_RECOMMENDS";

// 3、函数 action 发送网络请求接收到数据后 dispatch 真正的对象 action，
// 这样才可以交给 reducer 处理，也可以传更多参数
// src/pages/discover/child-pages/recommend/store/actionCreators.js
export const getHotRecommendAction = (limit) => {
  return dispatch => {
    getHotRecommends(limit).then(res => {
      dispatch(changeHotRecommendAction(res));
    })
  }
};

const changeHotRecommendAction = (res) => ({
  type: actionTypes.CHANGE_HOT_RECOMMENDS,
  hotRecommends: res.result
});

// src/store/index.js
import { reducer as recommendReducer } from '../pages/discover/child-pages/recommend/store/index';

const cReducer = combineReducers({
  recommend: recommendReducer
})

// 4、store 将对象 action（具有 type 和 payload 的对象）传给 reducer
// src/store/reducer.js reducer 即 cReducer
const store = createStore(reducer, composeEnhancers(applyMiddleware(thunk)));

// 5、reducer 根据对象 action 的 type 处理 defaultState ，
//    根据对象 action 的 payload 来设置并返回新的 state
// src/pages/discover/child-pages/recommend/store/reducer.js
switch (action.type) {
  case actionTypes.CHANGE_HOT_RECOMMENDS:
    return state.set("hotRecommends", action.hotRecommends);
  default:
      return state;
}  
  
const defaultState = Map({
  topBanners: [],
  hotRecommends: []
})  

// 6、拿到 store 新的 state 后返回一个对象给函数组件
// src/pages/discover/child-pages/recommend/child-cpns/hot-recommend/index.js
const { hotRecommends } = useSelector(state => ({
  hotRecommends: state.getIn(["recommend", "hotRecommends"])
}), shallowEqual);
```

#### 封装公共的热门推荐封面组件

接下来就是封装这样的公共组件展示数据

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1631020659287-8d85c779-e1ae-42ad-a4ef-84eb470af1a8.jpeg)

虽然其它子路由下该组件展示数据会多（比如多一行作者），但可以自己做个判断

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1631020659796-e49819f4-a47d-4083-b383-f3988d6a0f94.jpeg)

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1631020659855-d2d93d30-a5e8-4f87-b69d-8570747a6f4e.jpeg)

该组件分为上下两部分，上面使用子绝父相做玻璃效果和图标，下面又可以一整块或分为两部分

对图片给一个固定宽高，加载图片时布局才不会乱

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1631020659815-a7cdfd0f-352a-4b2b-8de7-fc850c4d228c.jpeg)

src/components/songs-cover/style.js

```js
import styled from "styled-components";

export const SongsCoverWrapper = styled.div`
  width: 140px;
  margin: 20px ${props => (props.right || 0)} 20px 0;

  .cover-top {
    position: relative;

    &>img {
      width: 140px;
      height: 140px;
    }

    .cover {
      position: absolute;
      top: 0;
      left: 0;
      width: 100%;
      height: 100%;
      background-position: 0 0;
      cursor: pointer;

      .info {
        display: flex;
        justify-content: space-between;
        align-items: center;
        padding: 0 10px;
        position: absolute;
        bottom: 0;
        left: 0;
        right: 0;
        background-position: 0 -537px;
        color: #ccc;
        height: 27px;

        .erji {
          margin-right: 5px;
          display: inline-block;
          width: 14px;
          height: 11px;
          background-position: 0 -24px;
        }

        .play {
          display: inline-block;
          width: 16px;
          height: 17px;
          background-position: 0 0;
        }
      }
    }
  }

  .cover-bottom {
    font-size: 14px;
    color: #000;
    margin-top: 5px;
  }

  .cover-source {
    color: #666;
  }
`
```

src/components/songs-cover/index.js

```jsx
import React, { memo } from 'react'

import { getCount, getSizeImage } from "@/utils/format-utils";

import { SongsCoverWrapper } from './style';

export default memo(function HYSongsCover(props) {
  const {info, source, ellipsis} = props;

  return (
    <SongsCoverWrapper>
      <div className="cover-top">
        <img src={getSizeImage(info.picUrl, 140)} alt="" />
        <a href={info.picUrl} title={info.name} className="cover sprite_covor">
          <div className="info sprite_covor">
            <span>
              <i className="sprite_icon erji"></i>
              {getCount(info.playCount)}
            </span>
            <i className="sprite_icon play"></i>
          </div>
        </a>
      </div>
      <div className={`title ${ellipsis ? 'text-nowrap' : ''}`}>
        {info.name}
      </div>
      {source &&
      <div className="cover-source text-nowrap">
        by {info.copywriter ? info.copywriter : info.creator.nickname}
      </div>}
    </SongsCoverWrapper>
  )
})
```

utils 里，

对数字格式化为字符串，展示播放量

对图片地址传尺寸参数 param=100x100 ，而不是默认传大图，优化加载速度

src/utils/format-utils.js

```js
export function getCount(count) {
  if (count < 0) return;
  if (count < 10000) {
    return count;
  } else if (Math.floor(count / 10000) < 10000) {
    return Math.floor(count / 1000) / 10 + "万";
  } else {
    return Math.floor(count / 10000000) / 10 + "亿";
  }
}

export function getSizeImage(imgUrl, size) {
  return `${imgUrl}?param=${size}x${size}`;
}
```

效果图

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1631020660903-95167ef0-f63d-42cb-bdc3-c772acf6d6b4.jpeg)

### 新碟上架

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1631095173290-920a7cf3-3984-4a89-b0a4-c339579ecc40.jpeg)

还是先请求拿到数据放到对应页面的 Redux 里，即 recommend reducer

当然也可以像下图代码，引入请求方法，直接使用 useState() 将数据保存在组件里

这是没有问题的，只是个人喜好，没有对错 

#### 请求数据

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1631077701403-4bb4c6b3-02a2-4ff1-a9c3-c25c7ccf2a59.jpeg)

如果要放在 Redux 里，就统一将请求的数据都放在 Redux 里进行管理，数据可追踪，架构清晰

src/services/recommend.js

```js
import request from './request';

export function getTopBanners() {
  return request({
    url: "/banner"
  })
}

export function getHotRecommends(limit) {
  return request({
    url: "/personalized",
    params: {
      limit
    }
  })
}

export function getNewAlbums(limit) {
  return request({
    url: "/top/album",
    params: {
      limit
    }
  })
}
```

src/pages/discover/child-pages/recommend/store/constants.js

```js
export const CHANGE_TOP_BANNERS = "recommend/CHANGE_TOP_BANNERS";
export const CHANGE_HOT_RECOMMENDS = "recommend/CHANGE_HOT_RECOMMENDS";
export const CHANGE_NEW_ALBUMS = "recommend/CHANGE_NEW_ALBUMS";
```

src/pages/discover/child-pages/recommend/store/reducer.js

```js
import { Map } from "immutable";

import * as actionTypes from "./constants";

const defaultState = Map({
  topBanners: [],
  hotRecommends: [],
  newAlbums: []
})

function reducer(state = defaultState, action) {
  switch (action.type) {
    case actionTypes.CHANGE_TOP_BANNERS:
      return state.set("topBanners", action.topBanners);
    case actionTypes.CHANGE_HOT_RECOMMENDS:
      return state.set("hotRecommends", action.hotRecommends);
    case actionTypes.CHANGE_NEW_ALBUMS:
      return state.set("newAlbums", action.newAlbums);  
    default:
      return state;
  }
}

export default reducer;
```

src/pages/discover/child-pages/recommend/store/actionCreators.js

```js
import * as actionTypes from './constants';

import { getTopBanners, getHotRecommends, getNewAlbums } from '@/services/recommend';

const changeTopBannerAction = (res) => ({
  type: actionTypes.CHANGE_TOP_BANNERS,
  topBanners: res.banners
});

const changeHotRecommendAction = (res) => ({
  type: actionTypes.CHANGE_HOT_RECOMMENDS,
  hotRecommends: res.result
});

const changeNewAlbumAction = (res) => ({
  type: actionTypes.CHANGE_NEW_ALBUMS,
  newAlbums: res.albums
});

export const getTopBannerAction = () => {
  return dispatch => {
    getTopBanners().then(res => {
      dispatch(changeTopBannerAction(res));
    })
  }
};

export const getHotRecommendAction = (limit) => {
  return dispatch => {
    getHotRecommends(limit).then(res => {
      dispatch(changeHotRecommendAction(res));
    })
  }
};

export const getNewAlbumAction = (limit) => {
  return dispatch => {
    getNewAlbums(limit).then(res => {
      dispatch(changeNewAlbumAction(res));
    })
  }
};
```

#### 分页轮播图

轮播的话需要使用 antd 的走马灯组件，将数据截取为两部分，每部分 5 条数据

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1631095185030-c654f3bc-2ec3-42e0-9976-e8ce92f811c4.jpeg)

有些值无法见名知意时，可以放进一个 common/constant.js 的常量里，提高可读性

轮播图为 Content ，分为左右两个箭头 + 中间的走马灯组件

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1631095201645-9131c3ae-21a8-472c-b0a4-7c080f5f2c23.jpeg)

src/pages/discover/child-pages/recommend/child-cpns/new-album/style.js

```js
import styled from "styled-components";

export const AlbumWrapper = styled.div`
  margin-top: 50px;

  .content {
    height: 186px;
    background-color: #f5f5f5;
    border: 1px solid #d3d3d3;
    margin: 20px 0 37px;
    display: flex;
    align-items: center;

    .arrow {
      width: 25px;
      height: 25px;
      cursor: pointer;
    }

    .arrow-left {
      background-position: -260px -75px;
    }

    .arrow-right {
      background-position: -300px -75px;
    }

    .album {
      width: 640px;
      height: 150px;

      .ant-carousel .slick-slide {
        height: 150px;
        overflow: hidden;
      }

      .page {
        display: flex !important;
        justify-content: space-between;
        align-items: center;
      }
    }
  }
`
```

src/pages/discover/child-pages/recommend/child-cpns/new-album/index.js

```js
import React, { memo, useEffect, useRef } from 'react';
import { useSelector, useDispatch, shallowEqual } from "react-redux";

import { getNewAlbumAction } from "../../store/actionCreators";

import { AlbumWrapper } from "./style";
import { HYThemeHeaderRCM } from "@/components/theme-header-rcm";
import { Carousel } from "antd";
import HYAlbumCover from "@/components/album-cover";

export default memo(function HYNewAlbum() {
  // redux hooks
  const { newAlbums } = useSelector(state => ({
    newAlbums: state.getIn(["recommend", "newAlbums"])  
  }), shallowEqual);
  
  // other hooks
  const dispatch = useDispatch();
  useEffect(() => {
    dispatch(getNewAlbumAction(10))
  }, [dispatch]) 
  
  const pageRef = useRef();
  
  return (
    <AlbumWrapper>
      <HYThemeHeaderRCM title="新碟上架"/>
       <div className="content">
        <button className="arrow arrow-left sprite_02" 
                onClick={e => pageRef.current.prev()}></button>
        <div className="album">
          <Carousel dots={false} ref={pageRef}>
            {
              [0, 1].map(item => {
                return (
                  <div key={item} className="page">
                    {
                      newAlbums.slice(item * 5, (item + 1) * 5).map(iten => {
                        return <HYAlbumCover key={iten.id} 
                                             info={iten} 
                                             size={100} 
                                             width={118} 
                                             bgp="-570px"/>
                      })
                    }
                  </div>
                )
              })
            }
          </Carousel>
        </div>
        <button className="arrow arrow-right sprite_02"
                onClick={e => pageRef.current.next()}></button>
      </div>
    </AlbumWrapper>
  )
})
```

#### 封装公共的新碟封面组件

新碟的封面是在其他地方是可以复用的，所以要封装为公共组件，

但是图片和文字的宽高不能写死，而是由使用者决定

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1631095211260-41fecf70-5e38-4cde-8beb-7bd4bfa1c99d.jpeg)

src/components/album-cover/style.js

```js
import styled from 'styled-components';

export const AlbumWrapper = styled.div`
  width: ${props => props.width + "px"};

  .album-image {
    position: relative;
    width: ${props => props.width + "px"};
    height: ${props => props.size + "px"};
    overflow: hidden;
    margin-top: 15px;

    img {
      width: ${props => props.size + "px"};
      height: ${props => props.size + "px"};
    }

    .cover {
      position: absolute;
      left: 0;
      right: 0;
      top: 0;
      bottom: 0;
      background-position: 0 ${props => props.bgp};
      text-indent: -9999px;
    }
  }

  .album-info {
    font-size: 12px;
    width: ${props => props.size};
    .name {
      color: #000;
      white-space: nowrap;
      text-overflow: ellipsis;
      overflow: hidden;
    }

    .artist {
      color: #666;
    }
  }
`
```

src/components/album-cover/index.js

```js
import React, { memo } from 'react';

import { getSizeImage } from '@/utils/format-utils';

import { AlbumWrapper } from './style';

export default memo(function HYAlbumCover(props) {
  // state and props
  const { info, size = 130, width = 153, bgp = "-845px" } = props;

  return (
    <AlbumWrapper size={size} width={width} bgp={bgp}>
      <div className="album-image">
        <img src={getSizeImage(info.picUrl, size)} alt="" />
        <a 
          href="localhost:3000" 
          title={info.name} 
          className="cover image_cover">
            {info.name}
        </a>
      </div>
      <div className="album-info">
        <div className="name text-nowrap">{info.name}</div>
        <div className="artist text-nowrap">{info.artist.name}</div>
      </div>
    </AlbumWrapper>
  )
})
```

效果图

![img](https://cdn.nlark.com/yuque/0/2021/gif/1614731/1631096056855-ea6b5b7d-6229-4d5c-a936-a871836017aa.gif)

### 榜单

榜单后面的背景只是一张背景图

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1631106358320-da5111b9-e8e5-49d1-9eb8-bbad3ff0744f.jpeg)

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1631106357581-209621d5-c16e-41a7-8917-c97464664d9f.jpeg)

#### 请求数据

三个榜单通过 ?idx ，分别使用不同的查询字符串值获取：0——飙升榜、2——新歌榜、3——原创榜

src/services/recommend.js

```js
import request from './request';

export function getTopBanners() {
  return request({
    url: "/banner"
  })
}

export function getHotRecommends(limit) {
  return request({
    url: "/personalized",
    params: {
      limit
    }
  })
}

export function getNewAlbums(limit) {
  return request({
    url: "/top/album",
    params: {
      limit
    }
  })
}

export function getTopList(idx) {
  return request({
    url: "/top/list",
    params: {
      idx
    }
  })
}
```

src/pages/discove/child-pages/recommend/store/constants.js

```js
export const CHANGE_TOP_BANNERS = "recommend/CHANGE_TOP_BANNERS";
export const CHANGE_HOT_RECOMMENDS = "recommend/CHANGE_HOT_RECOMMENDS";
export const CHANGE_NEW_ALBUMS = "recommend/CHANGE_NEW_ALBUMS";

export const CHANGE_UP_RANKING = "recommend/CHANGE_UP_RANKING";
export const CHANGE_NEW_RANKING = "recommend/CHANGE_New_RANKING";
export const CHANGE_ORIGIN_RANKING = "recommend/CHANGE_ORIGIN_RANKING";
```

src/pages/discove/child-pages/recommend/store/actionCreators.js

```js
import * as actionTypes from './constants';

import { 
  getTopBanners,
  getHotRecommends,
  getNewAlbums,
  getTopList 
} from '@/services/recommend';

const changeTopBannerAction = (res) => ({
  type: actionTypes.CHANGE_TOP_BANNERS,
  topBanners: res.banners
});

const changeHotRecommendAction = (res) => ({
  type: actionTypes.CHANGE_HOT_RECOMMENDS,
  hotRecommends: res.result
});

const changeNewAlbumAction = (res) => ({
  type: actionTypes.CHANGE_NEW_ALBUMS,
  newAlbums: res.albums
});

const changeUpRankingAction = (res) => ({
  type: actionTypes.CHANGE_UP_RANKING,
  upRanking: res.playlist
})

const changeNewRankingAction = (res) => ({
  type: actionTypes.CHANGE_NEW_RANKING,
  newRanking: res.playlist
})

const changeOriginRankingAction = (res) => ({
  type: actionTypes.CHANGE_ORIGIN_RANKING,
  originRanking: res.playlist
})


export const getTopBannerAction = () => {
  return dispatch => {
    getTopBanners().then(res => {
      dispatch(changeTopBannerAction(res));
    })
  }
};

export const getHotRecommendAction = (limit) => {
  return dispatch => {
    getHotRecommends(limit).then(res => {
      dispatch(changeHotRecommendAction(res));
    })
  }
};

export const getNewAlbumAction = (limit) => {
  return dispatch => {
    getNewAlbums(limit).then(res => {
      dispatch(changeNewAlbumAction(res));
    })
  }
};

export const getTopListAction = (idx) => {
  return dispatch => {
    getTopList(idx).then(res => {
      switch (idx) {
        case 0:
          dispatch(changeNewRankingAction(res));
          break;
        case 2:
          dispatch(changeOriginRankingAction(res));
          break;
        case 3:
          dispatch(changeUpRankingAction(res));
          break;
        default:
      }
    });
  }
}
```

src/pages/discove/child-pages/recommend/store/reducer.js

```js
import { Map } from "immutable";

import * as actionTypes from "./constants";

const defaultState = Map({
  topBanners: [],
  hotRecommends: [],
  newAlbums: [],

  upRanking: {},
  newRanking: {},
  originRanking: {},
})

function reducer(state = defaultState, action) {
  switch (action.type) {
    case actionTypes.CHANGE_TOP_BANNERS:
      return state.set("topBanners", action.topBanners);
    case actionTypes.CHANGE_HOT_RECOMMENDS:
      return state.set("hotRecommends", action.hotRecommends);
    case actionTypes.CHANGE_NEW_ALBUMS:
      return state.set("newAlbums", action.newAlbums);  
      
    case actionTypes.CHANGE_UP_RANKING:
      return state.set("upRanking", action.upRanking);
    case actionTypes.CHANGE_NEW_RANKING:
      return state.set("newRanking", action.newRanking);
    case actionTypes.CHANGE_ORIGIN_RANKING:
      return state.set("originRanking", action.originRanking);
    default:
      return state;
  }
}

export default reducer;
```

#### 封装公共的列表组件

榜单列表只要写好一个列表组件（该组件可以多次复用，所以封装为公共组件），

其它的列表传不同的数据就好了

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1631106358105-aab5f1bb-b035-4acc-ad7f-93b652b872a1.jpeg)

src/pages/discover/child-pages/recommend/child-cpns/recommend-ranking/style.js

```js
import styled from "styled-components";

export const RankingWrapper = styled.div`
  .tops {
    margin: 30px 0;
    display: flex;
    background-image: url(${require("@/assets/img/recommend-top-bg.png").default});
    height: 472px;
  }
`
```

src/pages/discover/child-pages/recommend/child-cpns/recommend-ranking/index.js

```js
import React, { memo, useEffect } from 'react';
import { useDispatch, useSelector, shallowEqual } from 'react-redux';


import { HYThemeHeaderRCM } from '@/components/theme-header-rcm';
import HYTopRanking from '@/components/top-ranking';
import { RankingWrapper } from './style';
import { getTopListAction } from '../../store/actionCreators';

export default memo(function HYRecomendRanking() {
  // redux hooks
  const { upRanking, newRanking, originRanking } = useSelector(state => ({
    upRanking: state.getIn(["recommend", "upRanking"]),
    newRanking: state.getIn(["recommend", "newRanking"]),
    originRanking: state.getIn(["recommend", "originRanking"]),
  }), shallowEqual);
  const dispatch = useDispatch();

  // other hooks
  useEffect(() => {
    dispatch(getTopListAction(0));
    dispatch(getTopListAction(2));
    dispatch(getTopListAction(3));
  }, [dispatch]);

  return (
    <RankingWrapper>
      <HYThemeHeaderRCM title="榜单" />
      <div className="tops">
        <HYTopRanking info={upRanking}/>
        <HYTopRanking info={newRanking}/>
        <HYTopRanking info={originRanking}/>
      </div>
    </RankingWrapper>
  )
})
```

**觉得 style.js 样式过多时可以拆分为多个 styled 组件，将样式迁移到新拆分的组件里**

src/components/top-ranking/style.js

```js
import styled from 'styled-components';

export const TopRankingWrapper = styled.div`
  flex: 1;

  .header {
    height: 100px;
    display: flex;

    margin: 20px 0 0 20px;

    .image {
      width: 80px;
      height: 80px;
      position: relative;

      img {
        width: 80px;
        height: 80px;
      }
    }

    .info {
      margin: 5px 0 0 10px;

      a {
        font-size: 14px;
        color: #333;
        font-weight: 700;
      }

      .btn {
        display: inline-block;
        text-indent: -9999px;
        width: 22px;
        height: 22px;
        margin: 8px 10px 0 0;
        cursor: pointer;
      }

      .play {
        background-position: -267px -205px;
        &:hover {
          background-position: -267px -235px;
        }
      }

      .favor {
        background-position: -300px -205px;
        &:hover {
          background-position: -300px -235px;
        }
      }
    }
  }

  .list {
    .list-item {
      position: relative;
      display: flex;
      align-items: center;
      height: 32px;

      :nth-child(-n+3) .rank {
        color: #c10d0c;
      }

      .rank {
        width: 35px;
        text-align: center;
        margin-left: 10px;
        font-size: 16px;
      }

      .info {
        color: #000;
        width: 170px;
        height: 17px;
        line-height: 17px;
        display: flex;
        justify-content: space-between;

        .name {
          flex: 1;
        }

        .operate {
          display: flex;
          align-items: center;
          display: none;
          width: 82px;

          .btn {
            width: 17px;
            height: 17px;
            margin-left: 8px;
            cursor: pointer;
          }

          .play {
            background-position: -267px -268px;
            &:hover {
              background-position: -267px -288px;
            }
          }

          .addto {
            position: relative;
            top: 2px;
            background-position: 0 -700px;
            &:hover {
              background-position: -22px -700px;
            }
          }

          .favor {
            background-position: -297px -268px;
            &:hover {
              background-position: -297px -288px;
            }
          }
        }
      }

      &:hover {
        .operate {
          display: block;
        }
      }
    }
  }

  .footer {
    height: 32px;
    display: flex;
    align-items: center;
    margin-right: 32px;
    justify-content: flex-end;

    a {
      color: #000;
    }
  }
`
```

src/components/top-ranking/index.js

```js
import React, { memo } from 'react';

import { getSizeImage } from '@/utils/format-utils';

import { TopRankingWrapper } from './style';

export default memo(function HYTopRanking(props) {
  // props and state
  const { info } = props;
  const { tracks = [] } = info;

  return (
    <TopRankingWrapper>
      <div className="header">
        <div className="image">
          <img src={getSizeImage(info.coverImgUrl)} alt="" />
          <a title={info.name} href="localhost:3000" className="image_cover">ranking</a>
        </div>
        <div className="info">
          <a href="localhost:3000">{info.name}</a>
          <div>
            <button title="播放" className="btn play sprite_02"></button>
            <button title="收藏" className="btn favor sprite_02"></button>
          </div>
        </div>
      </div>
      <div className="list">
        {
          tracks.slice(0, 10).map((item, index) => {
            return (
              <div key={item.id} className="list-item">
                <div className="rank">{index + 1}</div>
                <div className="info">
                  <a href="localhost:3000" 
                     className="name text-nowrap" 
                     title={item.name}>
                    {item.name}
                  </a>
                  <div className="operate">
                    <button title="播放" className="btn sprite_02 play"></button>
                    <button title="添加到播放列表" className="btn sprite_icon2 addto"></button>
                    <button title="收藏" className="btn sprite_02 favor"></button>
                  </div>
                </div>
              </div>
            )
          })
        }
      </div>
      <div className="footer">
        <a href="localhost:3000">查看全部 &gt;</a>
      </div>
    </TopRankingWrapper>
  )
})
```

效果图

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1631106358356-b5cb06b6-4a49-48dc-af0b-fbff70c76008.jpeg)

## 播放器组件

播放器组件属于全局的组件，无论是跳到哪个路由页面都会显示在下方

但是由于播放组件与某些页面联系紧密，特别是点击当前播放歌曲封面后打开的播放歌曲页面

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1631786130521-1547f1d4-6986-4926-9dd9-507a09086ae9.jpeg)

所以新建 pages/player 目录，把播放歌曲页和播放器组件放在该目录下

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1631786145684-9e32d56d-81ec-4092-b47b-37d6db60be6d.jpeg)

注意，不能将播放的歌曲数据放在组件里，放在 redux 里更好，

因为其它地方也有可能改变当前播放的歌曲

src/services/player.js

```js
import request from './request';

export function getSongDetail(ids) {
  return request({
    url: "/song/detail",
    params: {
      ids
    }
  })
}

export function getLyric(id) {
  return request({
    url: "/lyric",
    params: {
      id
    }
  })
}
```

src/utils/format-utils.js

```js
export function getCount(count) {
  if (count < 0) return;
  if (count < 10000) {
    return count;
  } else if (Math.floor(count / 10000) < 10000) {
    return Math.floor(count / 1000) / 10 + "万";
  } else {
    return Math.floor(count / 10000000) / 10 + "亿";
  }
}

export function getSizeImage(imgUrl, size, letter) {
  if(letter) return `${imgUrl}?param=${size}${letter}${size}`;
  return `${imgUrl}?param=${size}x${size}`;
}

export function formatDate(time, fmt) {
  let date = new Date(time);

  if (/(y+)/.test(fmt)) {
    fmt = fmt.replace(RegExp.$1, (date.getFullYear() + '').substr(4 - RegExp.$1.length));
  }
  let o = {
    'M+': date.getMonth() + 1,
    'd+': date.getDate(),
    'h+': date.getHours(),
    'm+': date.getMinutes(),
    's+': date.getSeconds()
  };
  for (let k in o) {
    if (new RegExp(`(${k})`).test(fmt)) {
      let str = o[k] + '';
      fmt = fmt.replace(RegExp.$1, (RegExp.$1.length === 1) ? str : padLeftZero(str));
    }
  }
  return fmt;
};

function padLeftZero(str) {
  return ('00' + str).substr(str.length);
};

export function formatMonthDay(time) {
  return formatDate(time, "MM月dd日");
}

export function formatMinuteSecond(time) {
  return formatDate(time, "mm:ss");
}

export function getPlaySong(id) {
  return `https://music.163.com/song/media/outer/url?id=${id}.mp3`;
}
```

src/utils/math-utils.js

```js
export function getRandomNumber(num) {
  return Math.floor(Math.random() * num);
}
```

src/utils/parse-lyric.js

```js
/**
[00:00.000] 作曲 : 许嵩
[00:01.000] 作词 : 许嵩
[00:22.240]天空好想下雨
[00:24.380]我好想住你隔壁
[00:26.810]傻站在你家楼下
[00:29.500]抬起头数乌云
[00:31.160]如果场景里出现一架钢琴
[00:33.640]我会唱歌给你听
[00:35.900]哪怕好多盆水往下淋
[00:41.060]夏天快要过去}
 */

// [00:31.160]如果场景里出现一架钢琴
const parseExp = /\[(\d{2}):(\d{2})\.(\d{2,3})\]/;

export function parseLyric(lyricString) {
  const lineStrings = lyricString.split("\n");

  const lyrics = [];
  for (let line of lineStrings) {
    if (line) {
      const result = parseExp.exec(line);
      if (!result) continue;
      const time1 = result[1] * 60 * 1000;
      const time2 = result[2] * 1000;
      const time3 = result[3].length === 3? result[3]*1: result[3]*10;
      const time = time1 + time2 + time3;
      const content = line.replace(parseExp, "").trim();
      const lineObj = {time, content};
      lyrics.push(lineObj);
    }
  }
  return lyrics;
}
```

src/components/top-ranking/index.js

```jsx
import React, { memo } from 'react';
import { useDispatch } from 'react-redux';

import { getSizeImage } from '@/utils/format-utils';
import { getSongDetailAction } from '@/pages/player/store';

import { TopRankingWrapper } from './style';

export default memo(function HYTopRanking(props) {
  // props and state
  const { info } = props;
  const { tracks = [] } = info;

  // redux hooks
  const dispatch = useDispatch();

  // other handle
  const playMusic = (item) => {
    dispatch(getSongDetailAction(item.id));
  }

  return (
    <TopRankingWrapper>
      <div className="header">
        <div className="image">
          <img src={getSizeImage(info.coverImgUrl)} alt="" />
          <a title={info.name} href="localhost:3000" className="image_cover">ranking</a>
        </div>
        <div className="info">
          <a href="localhost:3000">{info.name}</a>
          <div>
            <button title="播放" className="btn play sprite_02"></button>
            <button title="收藏" className="btn favor sprite_02"></button>
          </div>
        </div>
      </div>
      <div className="list">
        {
          tracks.slice(0, 10).map((item, index) => {
            return (
              <div key={item.id} className="list-item">
                <div className="rank">{index + 1}</div>
                <div className="info">
                  <a href="localhost:3000" 
                     className="name text-nowrap" 
                     title={item.name}>
                    {item.name}
                  </a>
                  <div className="operate">
                    <button title="播放" className="btn sprite_02 play" onClick={e => playMusic(item)}></button>
                    <button title="添加到播放列表" className="btn sprite_icon2 addto"></button>
                    <button title="收藏" className="btn sprite_02 favor"></button>
                  </div>
                </div>
              </div>
            )
          })
        }
      </div>
      <div className="footer">
        <a href="localhost:3000">查看全部 &gt;</a>
      </div>
    </TopRankingWrapper>
  )
})
```

src/pages/player/app-player-bar/index.js

```jsx
import React, { memo, useState, useEffect, useRef, useCallback } from 'react';
import { useDispatch, useSelector, shallowEqual } from 'react-redux';

import { getSizeImage, formatDate, getPlaySong } from '@/utils/format-utils';
import { 
  getSongDetailAction,
  changeSequenceAction,
  changeCurrentIndexAndSongAction,
  changeCurrentLyricIndexAction 
} from '../store/actionCreators';

import { message } from 'antd';
import { NavLink } from 'react-router-dom';
import { Slider } from 'antd';
import {
  PlaybarWrapper,
  Control,
  PlayInfo,
  Operator
} from './style';

export default memo(function HYAppPlayerBar() {
  // props and state
  const [currentTime, setCurrentTime] = useState(0);
  const [progress, setProgress] = useState(0);
  const [isChanging, setIsChanging] = useState(false);
  const [isPlaying, setIsPlaying] = useState(false);

  // redux hook
  const { 
    currentSong, 
    sequence, 
    lyricList,
    currentLyricIndex
  } = useSelector(state => ({
    currentSong: state.getIn(["player", "currentSong"]),
    sequence: state.getIn(["player", "sequence"]),
    lyricList: state.getIn(["player", "lyricList"]),
    currentLyricIndex: state.getIn(["player", "currentLyricIndex"])
  }), shallowEqual);
  const dispatch = useDispatch();

  // other hooks
  const audioRef = useRef();
  useEffect(() => {
    dispatch(getSongDetailAction(167876));
  }, [dispatch]);

  useEffect(() =>  {
    audioRef.current.src = getPlaySong(currentSong.id);
    audioRef.current.play().then(res => {
      setIsPlaying(true);
    }).catch(err => {
      setIsPlaying(false);
    });
  }, [currentSong]);

  // other handle
  const picUrl = (currentSong.al && currentSong.al.picUrl) || "";
  const singerName = (currentSong.ar && currentSong.ar[0].name) || "未知歌手";
  const duration = currentSong.dt || 0;
  const showDuration = formatDate(duration, "mm:ss");
  const showCurrentTime = formatDate(currentTime, "mm:ss");

  // handle function
  const playMusic = useCallback(() => {
    isPlaying ? audioRef.current.pause(): audioRef.current.play();
    setIsPlaying(!isPlaying);
  }, [isPlaying]);

  const timeUpdate = (e) => {
    const currentTime = e.target.currentTime;
    if (!isChanging) {
      setCurrentTime(currentTime * 1000);
      setProgress(currentTime * 1000 / duration * 100);
    }

    // 获取当前的歌词
    let i = 0;
    for (; i < lyricList.length; i++) {
      let lyricItem = lyricList[i];
      if (currentTime > 0 && currentTime * 1000 < lyricItem.time) {
        const lyricContent = lyricList[i - 1] && lyricList[i - 1].content;
        if (lyricContent) break;
      }
    }

    if (currentLyricIndex !== i - 1) {
      dispatch(changeCurrentLyricIndexAction(i - 1));
      const content = lyricList[i - 1] && lyricList[i - 1].content;
      if (currentTime > 0 && content) {
        message.open({
          key: "lyric",
          content: content,
          duration: 0,
          className: "lyric-class"
        })
      } 
    }
  }

  const changeSequence = () => {
    let currentSequence = sequence + 1;
    if (currentSequence > 2) {
      currentSequence = 0;
    }
    dispatch(changeSequenceAction(currentSequence));
  }

  const changeMusic = (tag) => {
    dispatch(changeCurrentIndexAndSongAction(tag));
  }

  const handleMusicEnded = () => {
    if (sequence === 2) { // 单曲循环
      audioRef.current.currentTime = 0;
      audioRef.current.play();
    } else {
      dispatch(changeCurrentIndexAndSongAction(1));
    }
  }

  const sliderChange = useCallback((value) => {
    setIsChanging(true);
    const currentTime = value / 100 * duration;
    setCurrentTime(currentTime);
    setProgress(value);
  }, [duration]);

  const sliderAfterChange = useCallback((value) => {
    const currentTime = value / 100 * duration / 1000;
    audioRef.current.currentTime = currentTime;
    setCurrentTime(currentTime * 1000);
    setIsChanging(false);

    if (!isPlaying) {
      playMusic();
    }
  }, [duration, isPlaying, playMusic]);

  return (
    <PlaybarWrapper className="sprite_player">
      <div className="content wrap-v2">
        <Control isPlaying={isPlaying}>
          <button className="sprite_player prev"
                  onClick={e => changeMusic(-1)}></button>
          <button className="sprite_player play" 
                  onClick={e => playMusic()}></button>
          <button className="sprite_player next"
                  onClick={e => changeMusic(1)}></button>
        </Control>
        <PlayInfo>
          <div className="image">
            <NavLink to="/discover/player">
              <img src={getSizeImage(picUrl, 35)} alt="" />
            </NavLink>
          </div>
          <div className="info">
            <div className="song">
              <span className="song-name">{currentSong.name}</span>
              <a href="#/" className="singer-name">{singerName}</a>
            </div>
            <div className="progress">
              <Slider defaultValue={30} 
                      value={progress}
                      tipFormatter={null}
                      onChange={sliderChange}
                      onAfterChange={sliderAfterChange}/>
              <div className="time">
                <span className="now-time">{showCurrentTime}</span>
                <span className="divider">/</span>
                <span className="duration">{showDuration}</span>
              </div>
            </div>
          </div>
        </PlayInfo>
        <Operator sequence={sequence}>
          <div className="left">
            <button className="sprite_player btn favor"></button>
            <button className="sprite_player btn share"></button>
          </div>
          <div className="right sprite_player">
            <button className="sprite_player btn volume"></button>
            <button className="sprite_player btn loop" onClick={e => changeSequence()}></button>
            <button className="sprite_player btn playlist"></button>
          </div>
        </Operator>
      </div>
      <audio ref={audioRef} 
             onTimeUpdate={e => timeUpdate(e)} 
             onEnded={e => handleMusicEnded()}/>
    </PlaybarWrapper>
  )
});
```

src/pages/player/app-player-bar/style.js

```jsx
import styled from 'styled-components';

export const PlaybarWrapper = styled.div`
  position: fixed;
  left: 0;
  right: 0;
  bottom: 0;
  height: 52px;
  background-position: 0 0;
  background-repeat: repeat;

  .content {
    display: flex;
    align-items: center;
    justify-content: space-between;
    position: absolute;
    left: 50%;
    transform: translateX(-50%);
    bottom: 0;
    height: 47px;
  }
`

export const Control = styled.div`
  display: flex;
  align-items: center;

  .prev, .next {
    width: 28px;
    height: 28px;
  }

  .prev {
    background-position: 0 -130px;
  }

  .play {
    width: 36px;
    height: 36px;
    margin: 0 8px;
    background-position: 0 ${props => props.isPlaying ? "-165px": "-204px"};
  }

  .next {
    background-position: -80px -130px;
  }
`

export const PlayInfo = styled.div`
  display: flex;
  width: 642px;
  align-items: center;

  .image {
    width: 34px;
    height: 34px;
    border-radius: 5px;
  }

  .info {
    flex: 1;
    color: #a1a1a1;
    margin-left: 10px;

    .song {
      color: #e1e1e1;
      position: relative;
      top: 8px;
      left: 8px;

      .singer-name {
        color: #a1a1a1;
        margin-left: 10px;
      }
    }

    .progress {
      display: flex;
      align-items: center;

      .ant-slider {
        width: 493px;
        margin-right: 10px;

        .ant-slider-rail {
          height: 9px;
          background: url(${require("@/assets/img/progress_bar.png").default}) right 0;
        }

        .ant-slider-track {
          height: 9px;
          background: url(${require("@/assets/img/progress_bar.png").default}) left -66px;
        }

        .ant-slider-handle {
          width: 22px;
          height: 24px;
          border: none;
          margin-top: -7px;
          background: url(${require("@/assets/img/sprite_icon.png").default}) 0 -250px;
          &:focus {
            border-color: none;
            outline: none; 
            box-shadow: none; 
          }
        }
      }

      .time {
        .now-time {
          color: #e1e1e1;
        }
        .divider {
          margin: 0 3px;
        }
      }
    }
  }
`

export const Operator = styled.div`
  display: flex;
  position: relative;
  top: 5px;

  .btn {
    width: 25px;
    height: 25px;
    cursor: pointer;
  }

  .favor {
    background-position: -88px -163px;
  }

  .share {
    background-position: -114px -163px;
  }

  .right {
    width: 126px;
    padding-left: 13px;
    background-position: -147px -248px;
    
    .volume {
      background-position: -2px -248px;
    }

    .loop {
      background-position: ${props => {
        switch(props.sequence) {
          case 1:
            return "-66px -248px"
          case 2:
            return "-66px -344px"
          default:
            return "-3px -344px"
        }
      }};
    }

    .playlist {
      width: 59px;
      background-position: -42px -68px;
    }
  }
`
```

src/pages/player/store/actionCreators.js

```js
import { getSongDetail, getLyric } from '@/services/player';
import { getRandomNumber } from '@/utils/math-utils';
import { parseLyric } from '@/utils/parse-lyric';

import * as actionTypes from './constants';

const changeCurrentSongAction = (currentSong) => ({
  type: actionTypes.CHANGE_CURRENT_SONG,
  currentSong
});

const changePlayListAction = (playList) => ({
  type: actionTypes.CHANGE_PLAY_LIST,
  playList
});

const changeCurrentSongIndexAction = (index) => ({
  type: actionTypes.CHANGE_CURRENT_SONG_INDEX,
  index
});

const changLyricListAction = (lyricList) => ({
  type: actionTypes.CHANGE_LYRIC_LIST,
  lyricList
})


// 对外暴露的action
export const changeSequenceAction = (sequence) => ({
  type: actionTypes.CHANGE_SEQUENCE,
  sequence
});

export const changeCurrentLyricIndexAction = (index) => ({
  type: actionTypes.CHANGE_CURRENT_LYRIC_INDEX,
  index
})

export const changeCurrentIndexAndSongAction = (tag) => {
  return (dispatch, getState) => {
    const playList = getState().getIn(["player", "playList"]);
    const sequence = getState().getIn(["player", "sequence"]);
    let currentSongIndex = getState().getIn(["player", "currentSongIndex"]);

    switch (sequence) {
      case 1: // 随机播放
        let randomIndex = getRandomNumber(playList.length);
        while (randomIndex === currentSongIndex) {
          randomIndex = getRandomNumber(playList.length);
        }
        currentSongIndex = randomIndex;
        break;
      default: // 顺序播放
        currentSongIndex += tag;
        if (currentSongIndex >= playList.length) currentSongIndex = 0;
        if (currentSongIndex < 0) currentSongIndex = playList.length - 1;
    }

    const currentSong = playList[currentSongIndex];
    dispatch(changeCurrentSongAction(currentSong));
    dispatch(changeCurrentSongIndexAction(currentSongIndex));

    // 请求歌词
    dispatch(getLyricAction(currentSong.id));
  }
}

export const getSongDetailAction = (ids) => {
  return (dispatch, getState) => {
    // 1.根据id查找playList中是否已经有了该歌曲
    const playList = getState().getIn(["player", "playList"]);
    const songIndex = playList.findIndex(song => song.id === ids);

    // 2.判断是否找到歌曲
    let song = null;
    if (songIndex !== -1) { // 查找歌曲
      dispatch(changeCurrentSongIndexAction(songIndex));
      song = playList[songIndex];
      dispatch(changeCurrentSongAction(song));
      dispatch(getLyricAction(song.id));
    } else { // 没有找到歌曲
      // 请求歌曲数据
      getSongDetail(ids).then(res => {
        song = res.songs && res.songs[0];
        if (!song) return;

        // 1.将最新请求到的歌曲添加到播放列表中
        const newPlayList = [...playList];
        newPlayList.push(song);

        // 2.更新redux中的值
        dispatch(changePlayListAction(newPlayList));
        dispatch(changeCurrentSongIndexAction(newPlayList.length - 1));
        dispatch(changeCurrentSongAction(song));

        // 3.请求歌词
        dispatch(getLyricAction(song.id));
      })
    }
  }
}

export const getLyricAction = (id) => {
  return dispatch => {
    getLyric(id).then(res => {
      const lyric = res.lrc.lyric;
      const lyricList = parseLyric(lyric);
      dispatch(changLyricListAction(lyricList));
    })
  }
}
```

src/pages/player/store/constants.js

```js
export const CHANGE_CURRENT_SONG = "player/CHANGE_CURRENT_SONG";
export const CHANGE_PLAY_LIST = "player/CHANGE_PLAY_LIST";
export const CHANGE_CURRENT_SONG_INDEX = "player/CHANGE_CURRENT_SONG_INDEX";

export const CHANGE_SEQUENCE = "player/CHANGE_SEQUENCE";

export const CHANGE_LYRIC_LIST = "player/CHANGE_LYRIC_LIST";
export const CHANGE_CURRENT_LYRIC_INDEX = "player/CHANGE_CURRENT_LYRIC_INDEX";
```

src/pages/player/store/reducer.js

```js
import { Map } from 'immutable';

import * as actionTypes from './constants';

const defaultState = Map({
  playList: [
    {
      "name": "有何不可",
      "id": 167876,
      "pst": 0,
      "t": 0,
      "ar": [
        {
          "id": 5771,
          "name": "许嵩",
          "tns": [],
          "alias": []
        }
      ],
      "alia": [],
      "pop": 100,
      "st": 0,
      "rt": "600902000007916021",
      "fee": 8,
      "v": 49,
      "crbt": null,
      "cf": "",
      "al": {
        "id": 16953,
        "name": "自定义",
        "picUrl": "https://p1.music.126.net/Md3RLH0fe2a_3dMDnfqoQg==/18590542604286213.jpg",
        "tns": [],
        "pic_str": "18590542604286213",
        "pic": 18590542604286212
      },
      "dt": 241840,
      "h": {
        "br": 320000,
        "fid": 0,
        "size": 9675799,
        "vd": -21099
      },
      "m": {
        "br": 192000,
        "fid": 0,
        "size": 5805497,
        "vd": -18400
      },
      "l": {
        "br": 128000,
        "fid": 0,
        "size": 3870346,
        "vd": -16900
      },
      "a": null,
      "cd": "1",
      "no": 3,
      "rtUrl": null,
      "ftype": 0,
      "rtUrls": [],
      "djId": 0,
      "copyright": 2,
      "s_id": 0,
      "mark": 8192,
      "originCoverType": 0,
      "single": 0,
      "noCopyrightRcmd": null,
      "mv": 0,
      "mst": 9,
      "cp": 14026,
      "rtype": 0,
      "rurl": null,
      "publishTime": 1231516800000
    },
    {
      "name": "雅俗共赏",
      "id": 411214279,
      "pst": 0,
      "t": 0,
      "ar": [
        {
          "id": 5771,
          "name": "许嵩",
          "tns": [],
          "alias": []
        }
      ],
      "alia": [],
      "pop": 100,
      "st": 0,
      "rt": null,
      "fee": 8,
      "v": 31,
      "crbt": null,
      "cf": "",
      "al": {
        "id": 34749138,
        "name": "青年晚报",
        "picUrl": "https://p1.music.126.net/Wcs2dbukFx3TUWkRuxVCpw==/3431575794705764.jpg",
        "tns": [],
        "pic": 3431575794705764
      },
      "dt": 249621,
      "h": {
        "br": 320000,
        "fid": 0,
        "size": 9987177,
        "vd": -22200
      },
      "m": {
        "br": 192000,
        "fid": 0,
        "size": 5992323,
        "vd": -19600
      },
      "l": {
        "br": 128000,
        "fid": 0,
        "size": 3994896,
        "vd": -17800
      },
      "a": null,
      "cd": "1",
      "no": 2,
      "rtUrl": null,
      "ftype": 0,
      "rtUrls": [],
      "djId": 0,
      "copyright": 0,
      "s_id": 0,
      "mark": 8192,
      "originCoverType": 0,
      "single": 0,
      "noCopyrightRcmd": null,
      "mv": 5302271,
      "rtype": 0,
      "rurl": null,
      "mst": 9,
      "cp": 14026,
      "publishTime": 1461723397683
    }, {
      "name": "嚣张",
      "id": 1382596189,
      "pst": 0,
      "t": 0,
      "ar": [
        {
          "id": 32220939,
          "name": "en",
          "tns": [],
          "alias": []
        }
      ],
      "alia": [],
      "pop": 100,
      "st": 0,
      "rt": "",
      "fee": 8,
      "v": 10,
      "crbt": null,
      "cf": "",
      "al": {
        "id": 80816891,
        "name": "嚣张",
        "picUrl": "https://p2.music.126.net/NhkuFBphLFaSmYMeW1-frQ==/109951164271814514.jpg",
        "tns": [],
        "pic_str": "109951164271814514",
        "pic": 109951164271814510
      },
      "dt": 253994,
      "h": {
        "br": 320000,
        "fid": 0,
        "size": 10162605,
        "vd": -55669
      },
      "m": {
        "br": 192000,
        "fid": 0,
        "size": 6097581,
        "vd": -53082
      },
      "l": {
        "br": 128000,
        "fid": 0,
        "size": 4065069,
        "vd": -51369
      },
      "a": null,
      "cd": "01",
      "no": 1,
      "rtUrl": null,
      "ftype": 0,
      "rtUrls": [],
      "djId": 0,
      "copyright": 0,
      "s_id": 0,
      "mark": 0,
      "originCoverType": 0,
      "single": 0,
      "noCopyrightRcmd": null,
      "mv": 0,
      "rtype": 0,
      "rurl": null,
      "mst": 9,
      "cp": 1372818,
      "publishTime": 0
    }
  ],
  currentSongIndex: 0,
  currentSong: {},
  sequence: 0, // 0 循环 1 随机 2 单曲
  lyricList: [],
  currentLyricIndex: 0
});

function reducer(state = defaultState, action) {
  switch (action.type) {
    case actionTypes.CHANGE_CURRENT_SONG:
      return state.set("currentSong", action.currentSong);
    case actionTypes.CHANGE_PLAY_LIST:
      return state.set("playList", action.playList);
    case actionTypes.CHANGE_CURRENT_SONG_INDEX:
      return state.set("currentSongIndex", action.index);
    case actionTypes.CHANGE_SEQUENCE:
      return state.set("sequence", action.sequence);
    case actionTypes.CHANGE_LYRIC_LIST:
      return state.set("lyricList", action.lyricList);
    case actionTypes.CHANGE_CURRENT_LYRIC_INDEX:
      return state.set("currentLyricIndex", action.index);
    default:
      return state;
  }
}

export default reducer;
```

src/pages/player/store/index.js

```js
import reducer from './reducer';
import {
  getSongDetailAction
} from './actionCreators';

export {
  reducer,
  getSongDetailAction
}
```

src/store/reducer.js

```js
import { combineReducers } from 'redux-immutable';

import { reducer as recommendReducer } from '../pages/discover/child-pages/recommend/store';
import { reducer as playerReducer } from '../pages/player/store';

const cReducer = combineReducers({
  recommend: recommendReducer,
  player: playerReducer
});

export default cReducer;
```

src/App.js

```jsx
import React, { memo } from 'react';
import { Provider } from "react-redux";
import { renderRoutes } from 'react-router-config';

import routes from './router';
import store from "./store";

import HYAppHeader from "components/app-header";
import HYAppFooter from "components/app-footer";
import HYAppPlayerBar from './pages/player/app-player-bar';
import { HashRouter } from 'react-router-dom';

export default memo(function App() {
  return (
    // 使用 Provider，让包裹的组件使用 connect 来获取 store 的数据
    <Provider store={store}>
      <HashRouter>
        <HYAppHeader/>
        {renderRoutes(routes)}
        <HYAppFooter/>
        <HYAppPlayerBar/>
      </HashRouter>
    </Provider>
  )
})
```

## 播放歌曲页（未完成）

src/router/index.js

```js
import React from 'react';
import { Redirect } from "react-router-dom";

import HYDiscover from "@/pages/discover";
import HYRecommend from "../pages/discover/child-pages/recommend";
import HYRanking from "../pages/discover/child-pages/ranking";
import HYSongs from "../pages/discover/child-pages/songs";
import HYDjradio from "../pages/discover/child-pages/djradio";
import HYArtist from "../pages/discover/child-pages/artist";
import HYAlbum from "../pages/discover/child-pages/album";
import HYPlayer from "../pages/player";

import HYMine from "@/pages/mine";
import HYFriend from "@/pages/friend";

const routes = [
  {
    path: "/",
    exact: true,
    render: () => (
      <Redirect to="/discover"/>
    )
  },
  {
    path: "/discover",
    component: HYDiscover,
    routes: [
      {
        path: "/discover",
        exact: true,
        render: () => (
          <Redirect to="/discover/recommend"/>
        )
      },
      {
        path: "/discover/recommend",
        component: HYRecommend
      },
      {
        path: "/discover/ranking",
        component: HYRanking
      },
      {
        path: "/discover/songs",
        component: HYSongs
      },
      {
        path: "/discover/djradio",
        exact: true,
        component: HYDjradio
      },
      {
        path: "/discover/artist",
        component: HYArtist
      },
      {
        path: "/discover/album",
        component: HYAlbum
      },
      {
        path: "/discover/player",
        component: HYPlayer
      }
    ]
  },
  {
    path: "/mine",
    component: HYMine
  },
  {
    path: "/friend",
    component: HYFriend
  },
];

export default routes;
```

src/pages/player/index.js

```js
import React, { memo } from 'react';

import {
  PlayerWrapper,
  PlayerLeft,
  PlayerRight
} from './style';

export default memo(function HYPlayer() {
  return (
    <PlayerWrapper>
      <div className="content wrap-v2">
        <PlayerLeft>
          <h2>HYPlayerInfo</h2>
          <h2>HYSongContent</h2>
        </PlayerLeft>
        <PlayerRight>
          <h2>HYSimiPlaylist</h2>
          <h2>HYSimiSong</h2>
          <h2>Download</h2>
        </PlayerRight>
      </div>
    </PlayerWrapper>
  )
})
```

src/pages/player/style.js

```js
import styled from 'styled-components';

export const PlayerWrapper = styled.div`
  .content {
    background: url(${require("@/assets/img/wrap-bg.png")}) repeat-y;
    background-color: #fff;
    display: flex;
  }
`

export const PlayerLeft = styled.div`
  width: 710px;
`

export const PlayerRight = styled.div`
  width: 270px;
  padding: 20px 40px 40px 30px;
`
```

## 打包部署

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1631786130759-945514e2-64c8-41f3-99ff-b384aa2dace7.jpeg)

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1631786130750-48d5b951-b89c-4eaa-b503-11ac2807b87a.jpeg)

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1631786130708-b8349829-f009-4ce5-9864-41f98aa3297d.jpeg)

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1631786130713-ec6bee51-e51b-4daa-9a17-9c43aa8e4d89.jpeg)

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1631786131998-1a337492-ad1a-4ebb-9215-5d3b63985442.jpeg)

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1631786131995-81fa815d-fa8a-4fdf-b12f-e19e1265d5fa.jpeg)

## SSR

单页面富应用的局限

问题一：首屏显示速度较慢

问题二：不利于 SEO 优化

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1632981305190-c7efa4cf-fae7-4bbe-941b-7df93d0690bd.jpeg)

如何解决这个问题呢？

使用服务端渲染

### 什么是 SSR？

SSR（Server Side Rendering，服务端渲染），

指的是页面在服务器端已经生成了完成的 HTML 页面结构，不需要浏览器解析。

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1632981303798-c5d90505-ff9f-4d47-85d9-a9dd25296d20.jpeg)

对应的是 CSR（Client Side Rendering，客户端渲染），

我们开发 SPA 页面通常依赖的就是客户端渲染。

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1632977985240-579c8d1a-0bfb-4cc8-abe6-04083d5b805e.jpeg)

单纯实现 SSR 很简单，

毕竟这是传统技术，且和语言无关，随便用 php 、jsp、asp、node 等都可以实现。

下面用 node 实现一个基本的 ssr

1、创建一个 node 服务

2、模拟数据请求方法 fetchData

3、将fetchData结果转换为 html 字符串

4、输出完整的 html 内容

```js
const http = require('http');

//模拟数据的获取
const fetchData = function () {
    return {
        list: [{
                name: '包子',
                num: 100
            },
            {
                name: '饺子',
                num: 2000
            }, {
                name: '馒头',
                num: 10
            }
        ]
    }
}

//数据转换为 html 内容
const dataToHtml=(data)=>{
   
    var html='';
    data.list.forEach(item=>{
        html += `<div>${item.name}有${item.num}个</div>`
    });

    return html;
}

//服务
http.createServer((req, res) => {

    res.writeHead(200, {
        'Content-Type': 'text/html'
    });

    const html = dataToHtml(fetchData());

    res.end(`<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>传统 ssr</title>
</head>
<body>
    <div id="root">
       ${html}
    </div>
</body>
</html>
</body>
`);

}).listen(9001);

console.log('server start...9001');
```

早期的服务端渲染包裹 PHP、JSP、ASP 等方式，

但是在目前 "前后端分离" 的开发模式下，

前端开发人员不太可能再去学习 PHP、 JSP 等技术来开发网页，

不过我们可以借助于 Node 来帮助我们执行 JavaScript 代码，提前完成页面的渲染。

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1632401027325-6ee97787-6d19-4d72-aaea-ea498e2f3d35.jpeg)

### 什么是同构？

**同构是共同构建，首屏是后端渲染，之后是前端渲染。**

**同构也是 SSR，是现代 SSR 的一种表现形式。**

**一套代码既可以在服务端又可以在客户端运行，这就是同构应用。**


**当用户发出请求时，先在服务器通过 SSR 渲染出首页的内容；**


**但是对应的代码同样可以在客户端执行，**

**执行的目的包括事件绑定等，其它页面切换时也可以在客户端被渲染，**

**在服务器上拿到 html 页面并发送请求，之后的的结果直接响应给客户端。**

一般来说，当用户第一次进入页面或刷新页面时，使用服务端渲染，

刷新过后的路由跳转则使用客户端渲染，不需要发送大量网络请求，

也就是说，第一次打开页面是服务端渲染，基于第一次访问，用户的后续交互是 `SPA` 的效果和体验，

实现两种技术的结合，同时可以最大限度地重用代码。

每次刷新页面的时候数据是从服务端直出，

后续的访问是 `spa` 的体验，

既能解决`SEO`问题，也能保持页面切换的效率，服务器的压力要比传统的`ssr`也相对小。

「同构渲染」加入了一个中间层的概念，node 中间层从后端接过渲染的逻辑，

首次渲染时使用 Node.js 来直出 HTML，后续客户端交互包括当前页路由切换直接在客户端完成。

一般来说同构渲染是介于前后端中的共有部分。

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1632978444042-48696cd1-f27e-4fe0-b795-95cf650e684d.jpeg)

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1631786132683-b17d4d67-d9ee-4b68-b2ea-16911b6eb7e5.jpeg)

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1631786132939-b2ba0b1d-047c-4916-8058-6c9a3ac3b1f6.jpeg)

### 双端对比机制

为了实现服务端渲染，打造同构应用，

React 内部实现了相关的 API，可以让我们方便的将一个组件转换为 html 字符串。

下面介绍相关 API

```js
import ReactDOMServer from 'react-dom/server'
```

ReactDOMServer 类可以帮我们在服务端渲染组件 - 得到组件的 html 字符串。

下面是介绍该模块的两个方法

#### renderToString()

```js
ReactDOMServer.renderToString(element)
```


**该方法把一个 React 组件渲染为原始的 HTML 。**

**我们可以用这个方法在服务端生成 HTML 字符串，**

**然后将该字符串返回给浏览器端，完成页面内容的初始化，**

**同时让搜索引擎可以抓取你的页面来达到优化 SEO 的目的。**

在 react 16 前，该方法生成的 html 内容的每一个 DOM 节点都有一个 data-react-id 属性，

根节点会有一个 data-react-checksum 属性。

**组件在服务端渲染后，在浏览器端再渲染一次，完成组件的交互等逻辑。**

**渲染时，react 在浏览器端会计算出组件的 data-react-checksum 属性值，**

**如果发现和服务端计算的值一致，则不会进行客户端渲染。**

**所以 data-react-checksum 属性的作用是为了完成组件的双端对比。**

**如果两个组件的 props 和 DOM 结构是相同的，那么计算出的该属性值就是一致的。**

也可以换个角度来理解，

**当双端渲染的组件的 props 和 DOM 结构一致时，**

**那么该组件只会渲染一次，客户端会采用服务端渲染的结果，仅作事件绑定等处理，**

**这会让我们的应用有一个非常高效的初次加载体验。**

ps: data-react-checksum 属性值是通过 Adler-32 校验算法实现的。

#### renderToStaticMarkup()

```js
ReactDOMServer.renderToStaticMarkup(element)
```

该方法就比较轻量了，仅仅是为了将组件渲染为 html 字符串，不会带有 data-react-checksum 属性。

和上面方法的能力不同，当然使用场景也不同，

如果只是单纯服务端渲染的话可以用该方法，

性能肯定要比 renderToString() 高，因为不需要计算，还能减少直出的内容体积。

#### 性能提升

**从 react 16 开始，服务端渲染 renderToString() 渲染的结果不再有 data-react-* 属性，**

**当然也相应的提供了一个客户端渲染API - ReactDOM.hydrate()，**

从使用上来说，和 ReactDOM.render() 没有差别。

**在浏览器端渲染时，**

**ReactDOM.hydrate() 会最大限度的保留服务端使用 renderToString() 渲染的内容，**

**同时添加事件绑定等交互。**

**hydrate 就是“注水”，一个完整的网页可以看成是干货掺了水的结果，**

**纯数据只是干巴巴的干货，不是给人看的，但是“注水”之后，变成可以展示的HTML，就变成浏览器可以解释用户能看的东西了，**

**这过程就是 hydrate 。**

**dehydrate 是“脱水”，一般指的是服务器端渲染的时候，准备纯数据的过程，这些数据随 HTML一起发给浏览器，**

因为 **React 需要用这些数据重新渲染一遍（v16之前是这样），**

**在浏览器端，根据这些数据重新渲染一遍的过程，就叫做 rehydrate。**

##### renderToNodeStream 和 renderToStaticNodeStream

另外， react 16 在性能上还做了改进，提供了可以将组件转换为字节流的 renderToNodeStream 方法。

其实使用 renderToNodeStream 或者 renderToString 对最终的渲染结果没有影响。

不过 renderToNodeStream 的性能要好的多，可以有效缩短 TTFB 时间。

因为组件渲染为字符串，是一次性处理完后才开始向浏览器端返回结果。

而采用流的话，可以边读边输出，可以要让页面更快的展现，缩短首屏展现时间。

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1632981026850-278604d7-962e-4b73-a2da-420111fc8e8a.jpeg)

renderToStaticNodeStream 与 renderToStaticMarkup 作用差不多。

<img src="https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1632981026721-dce0c23b-8d93-4a5b-bd56-cf669ca71e9e.jpeg" alt="img"  />

### 使用 React SSR

r**eact ssr 技术开发出的最终产物其实就是 SPA + SSR 的结合。**

**其中的 SSR 指的是在服务端渲染组件，**

**而组件可以在服务端渲染的根本原因就是虚拟 DOM 。**

虚拟 DOM 除了在渲染时用于提高渲染性能，以最小的代价来更新视图的作用外，

另一个作用就是为组件的跨平台渲染提供可能。

同构的最大优点是双端可以公用一套代码，

但它是一把双刃剑，因为它还涉及到服务端，所以复杂性大大增加。

另外双端也不是完全能公用一套代码，还需要做很多差异化的处理，

不只是代码层面的，还会涉及到架构和工程化。

所以我们需要一个轮子，这个轮子本身已经帮我们完备了双端的差异处理，

开发者只需要关心自身业务逻辑，开发中无差异化。

方式一: 手动搭建一个 SSR 框架;

方式二: 使用已经成熟的 SSR 框架: Next.js

### 使用 Next.js

**安装 Next.js 框架的脚手架**

```shell
npm install -g create-next-app
```

**创建 Next.js 项目**

```shell
create-next-app next-demo
```

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1632401662204-7d2e80d4-5034-4630-894b-ec2ae8d4a6ab.jpeg)

start 命令的作用是让 build 命令打包出来的项目跑起来

dev 命令开发环境使用

```shell
yarn dev
```

#### 首页的展示

Next.js 默认已经给我们配置好了路径和组件的映射关系（后端路由）；

pages 目录中配置相关的组件都会自动生成对应的路径；

如果是 pages/recommend/index.js ，则 index.js 组件将作为 /recommend 路径的映射  

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1632402081036-c8c9c202-b32a-40ac-9447-8c4cdcf1bc78.jpeg)

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1632402025185-6532ad05-9941-4530-b77a-9bcbbedac7e8.jpeg)

#### 页面跳转

链接方式跳转是服务端渲染，页面会刷新。

使用 Postman 请求 localhost:3000/about ，可以看到对应的 html 结构。

如果使用 Next 提供的 Link 组件则是前端渲染，不会刷新、重新发送请求。

```js
import Link from "next/link"
```

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1632402265656-fafc905c-e545-4e5b-9e29-758a07f0961c.jpeg)

#### Layout 组件

我们发现 home 和 about 是两个相互独立的组件:

如果它们有一些公共的内容: 比如头部、尾部都是一样的，是否每个地方都需要写一遍呢？

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1632402738602-3333424a-0d3c-4583-802a-43e63caae7b8.jpeg)

方案一

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1632403232258-d28dbdfd-cf8d-4fb4-aaca-2c9d7d274e18.jpeg)

方案二

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1632402898971-7cba92da-a7b3-45aa-9d4e-8a53becd8bf4.jpeg)

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1632402909851-1c75ef0a-4c3b-47dd-98f1-49d7a834a53e.jpeg)

#### Next.js 支持各种样式

Next.js 支持各种样式

方式一: 全局样式引入

方式二: module.css

style 目录下 .module.css

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1632403656774-107a88ba-56e9-4b35-8903-98ae14f98765.jpeg)

方式三: Next.js 默认集成 styled-jsx

```
<style>{`
  p {
    color: #f879c6;
  }

`}</style>
```


方式四: 其他 css in js 方案，比如 styled-components


styled-components在Next服务器上渲染的的问题：

组件使用了 styled-components 显示是没有问题的，这是在客户端进行渲染的，

但是只要我们手动刷新，className 就无法进行匹配，

这是因为在服务端渲染和在客户端渲染的 className 不匹配

如何解决：

引入相关依赖，创建和编辑 .babelrc 文件

```shell
yarn add styled-components

yarn add -D babel-plugin-styled-components
```


编辑 .babelrc 文件

```
{
  "presets": [
    "next/babel"
  ],
  "plugins": [
    ["styled-components"]
  ]
}
```

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1632403973549-f7a0eec0-1a25-443e-b32b-ad9973cb06f3.jpeg)

#### 路由的补充

##### 路由的嵌套(子路由)

文件夹的嵌套，最后就可以形成子路由；

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1632404558103-a3011384-60a1-402d-9e02-c8f32aa04dcd.jpeg)

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1632404679096-c6b0fd3f-94c6-46f0-b6f0-8328a400c8df.jpeg)

##### 路由的传参

Next.js 中无法通过 /user/:id 方式传递参数；

只能通过 /user?id=123 的方式来传递参数


传递参数由两种方法：

- Link 中的路径

  ![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1632405194841-b496af8d-5449-496b-a97d-5bea1cbd44a9.jpeg)

  ![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1632405235685-cfcf0bf2-3cb5-480d-ab8c-d9963a2708a0.jpeg)

- Router.push( pathname,query )

```jsx
import Router from 'next/router'
const itemClick = (item) => {
  Router.push({
    pathname: 'recommend',
    query: {
      id: item
    }
  })
}
```

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1632405411494-d5ce85cb-5d87-4b3a-acf8-fe159adc35eb.jpeg)

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1632405235685-cfcf0bf2-3cb5-480d-ab8c-d9963a2708a0.jpeg)

### 请求数据 getInitialProps

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1632405697799-baedef0f-1bee-47b3-b99d-68b5186f683f.jpeg)

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1632405697940-cd0e9100-9f04-4e10-828b-93a69ce656d8.jpeg)

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1632405796321-1d69a6a3-3e21-4d19-a04e-189941f71edb.jpeg)

### 手动搭建一个 SSR 框架

首先通过同构应用流程图对我们的应用骨架有个宏观的认识

<img src="https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1651140134827-6ddd4825-8c96-4d83-bcc6-06383a35b5f6.jpeg?x-oss-process=image%2Fresize%2Cw_795%2Climit_0" alt="16fe82eb96f4a852tplv-t2oaga2asx.jpg"  />

﻿后面就进入实践阶段，一步一步打造我们的应用骨架了。

#### 实现最基本的 React SSR

```bash
npm i react react-dom
npm i @babel/core @babel/cli @babel/preset-react
```

package.json

```json
{
    "name": "base-react-ssr",
    "version": "1.0.0",
    "description": "",
    "main": "index.js",
    "scripts": {
      "test": "echo \"Error: no test specified\" && exit 1"
    },
    "author": "",
    "license": "ISC",
    "dependencies": {
      "@babel/cli": "^7.7.7",
      "@babel/core": "^7.7.7",
      "@babel/preset-react": "^7.7.4",
      "react": "^16.12.0",
      "react-dom": "^16.12.0"
    },
    "devDependencies": {
      "babel-cli": "^6.26.0"
    }
  }
```

index.js

```js
const React = require('react');

const { renderToString } = require('react-dom/server');

const http = require('http');

// 组件
class Index extends React.Component {
    constructor(props) {
        super(props);
    }

    render() {
        return <h1>hello react ssr!</h1>
    }
}

// 服务
http.createServer((req, res) => {
    res.writeHead(200, {
        'Content-Type': 'text/html'
    });

    const html = renderToString(<Index/>);
    res.end(`<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>React SSR</title>
</head>
<body>
    <div id="root">
       ${html}
    </div>
</body>
</html>`);

    }
).listen(9001);

console.log('server start. 9001');
```

使用 babel 进行代码转换。

这里我们使用 babel cli 命令模式行来编译代码。

```bash
npx babel index.js --out-file index-compiled.js --presets=@babel/preset-react
```

index-compiled.js

```js
const React = require('react');

const {
  renderToString
} = require('react-dom/server');

const http = require('http'); 

// 组件
class Index extends React.Component {
  constructor(props) {
    super(props);
  }

  render() {
    // jsx 语法转换为了React.createElement 方法  
    return /*#__PURE__*/React.createElement("h1", null, "hello react ssr!");
  }

} 

// 服务
http.createServer((req, res) => {
  res.writeHead(200, {
    'Content-Type': 'text/html'
  });
    
  // jsx 语法转换为了 React.createElement 方法  
  const html = renderToString( /*#__PURE__*/React.createElement(Index, null)); 

  res.end(`<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>React SSR</title>
</head>
<body>
    <div id="root">
       ${html}
    </div>
</body>
</html>`);
}).listen(9001);
console.log('server start. 9001');
```

效果

![image.png](https://cdn.nlark.com/yuque/0/2022/png/1614731/1651141347896-c8da133c-5784-49c7-9a5c-3e73501672a0.png)

![image.png](https://cdn.nlark.com/yuque/0/2022/png/1614731/1651141336923-b5338cd7-2716-41b7-8379-c338b81e34be.png)

到这里我们就实现了最基本的 react ssr，当然这很简陋，也仅仅是个 demo，

#### 交互实现

单纯的 ssr 也没啥意义，也只能把组件当做一个模板来用，连个最基本的交互都没有。

比如：我想点击页面某个元素的时候给我一个反馈提示。

元素事件是基于浏览器执行的，只有在浏览器端执行了相应的 js 代码才能绑定事件。

在上一节我们实现的这是一个 ssr 直出效果，也就是说只是一个静态页面。

所以我们需要让代码在浏览器端也执行一次，组件在浏览器端挂载完后 react 会自动完成事件绑定。

**浏览器也执行一次代码，组件不会重复渲染吗？**

浏览器接管页面后，react-dom 在渲染组件前会先和页面中的节点做对比，

对比失败的时候会采用客户端的内容进行渲染，且 react 会尽量多的复用已有的节点。

**那需要写两套代码？**

基于同构，浏览器和服务端可以运行同一份代码，服务端直出组件后，浏览器接管页面，然后剩下的工作由浏览器来完成。

**整体实现思路**

- 使用 `koa` 创建一个基础 `http` 服务，可以直出 `Index` 组件。
- 然后编写客户端代码，增加 `Index` 组件的渲染入口，使用`react-dom` 库渲染 `Index` 组件。
- 然后使用 `webpack` 将`js`代码打包到一个文件内 `index.js` 内。
- 服务端直出的时候输出这个 `js` 资源到浏览器。
- 在运行前，需要使用`webpack`将客户端代码编译打包，使用`babel cli`打包编译服务端代码。

**安装插件、工具、库**

```bash
react react-dom // react 基础库
@babel/core @babel/cli // babel 基础库
@babel/preset-react // 编译 react 代码
@babel/preset-env // 配置 babel 编译的一些选项
babel-loader // 编译 js 代码
webpack webpack-cli   // webpack 两个核心库
koa2 // web 开发框架
koa-static // 实现静态资源的访问
```

ps:

@babel/preset-env 是一个预设集合，代替了以往的 stage-* babel-preset-es2015等包，

可以根据开发者的配置，按需加载插件，还可以通过设置 target 属性对 node 或者浏览器端进行编译输出设置。

**package.json**

```json
{
  "name": "my-react-ssr",
  "version": "1.0.0",
  "description": "",
  "main": "app.js",
  "scripts": {
    "dev": "webpack --config ./webpack/webpack.dev.config.js",
    "babel-node": "babel  src -d dist/src"
  },
  "author": "",
  "license": "ISC",
  "dependencies": {
    "@babel/cli": "^7.7.4",
    "@babel/core": "^7.7.4",
    "@babel/plugin-proposal-class-properties": "^7.7.4",
    "@babel/preset-env": "^7.7.4",
    "@babel/preset-react": "^7.7.4",
    "babel-loader": "^8.0.6",
    "koa-static": "^5.0.0",
    "koa2": "^2.0.0-alpha.7",
    "react": "^16.12.0",
    "react-dom": "^16.12.0",
    "webpack": "^4.41.2",
    "webpack-cli": "^3.3.10"
  }
}
```

**创建基础 http 服务**

app.js

```js
// web 服务启动入口文件 放在根目录方便以后的部署
// 这是一个中间件，它用于处理 web 请求，实现 react ssr，将组件转换为 html 字符串
const reactSsr  = require('./dist/src/server/middlewares/react-ssr').default;
const Koa = require('koa2');
const koaStatic =require('koa-static');
const path = require('path');

const app = new Koa();

// 设置可访问的静态资源
app.use(koaStatic(
    path.join(__dirname, './dist/static')
));

console.log(path.join(__dirname, './dist/static'));

// ssr 中间件
app.use(reactSsr);

// 启动服务
app.listen(9001);

console.log('server is start .9001');
```

**react ssr 中间件**

src/server/middlewares/react-ssr.js

```js
// 完成 react ssr 工作的中间件,组件在服务端渲染的逻辑都在这个文件内
// 引入 Index 组件
import React from 'react';
import Index from '../../client/pages/index';
import { renderToString } from 'react-dom/server';

// 服务端返回 html 字符串，放到响应体
export default (ctx,next) => {
    const html = renderToString(<Index/>);
    ctx.body=`<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>my react ssr</title>
</head>
<body>
    <div id="root">
       ${html} <span>测试内容</span>
    </div>
</body>
</html><script type="text/javascript"  src="index.js"></script>  // 这里绑定了 index.js代码，浏览器会下载后执行
`;
    return next();
}
```

```js
// /src/server/middlewares/react-ssr.js

// 完成 react ssr 工作的中间件
// 引入 Index 组件
import React from 'react';
import Index from '../../client/pages/index';
import { renderToString} from 'react-dom/server';

export default  (ctx,next)=>{

    console.log('ctx.request.path', ctx.request.path);
    console.log('ctx.request.url', ctx.request.url);

    const html = renderToString(<Index/>);
    ctx.body=`<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>my react ssr</title>
</head>
<body>
    <div id="root">
       ${html} <span>测试内容</span>
    </div>
</body>
</html>
<script type="text/javascript"  src="index.js"></script>
`;

    return next();
}
```

**Index 组件定义**

src/client/pages/index/index.js

```js
import React from 'react';
// Index 组件
export default class Index extends React.Component {
    constructor(props) {
        super(props);
    }
   handlerClick(){
       alert('一起来玩 react ssr 呀,点我有反应吗？');
   }
    render() {
        return <h1 onClick={this.handlerClick}>hello react ssr!</h1>
    }
}
```

**实现组件在浏览器端渲染和挂载**

浏览器端执行组件渲染的入口文件,也是 webpack 进行资源构建的 entry 入口。

src/client/app/index.js

```js
// 浏览器端页面结构渲染入口
import React from 'react';
import ReactDom from 'react-dom';
import Index from '../pages/index';

// 渲染 Index 组件，浏览器端注水
ReactDom.hydrate(<Index />, document.getElementById('root'))
```

**webpack 配置**

webpack/webpack.dev.config.js

```js
const path = require('path')
// 定一个通用的路径转换方法
const resolvePath = (pathstr) => path.resolve(__dirname, pathstr);

module.exports = {
    mode: 'development',
    entry: resolvePath('../src/client/app/index.js'),// 入口文件
    output: {
        filename: 'index.js',
        path: resolvePath('../dist/static')
    },
    module: {
        rules: [{
                test: /\.jsx?$/,
                loader: 'babel-loader',
                exclude: /node_modules/
            }
        ]
    }
}
```

**babel 配置**

把配置单独放在 .babelrc 里面，当然也可以放到 webpack 配置文件内

.babelrc

```js
{
    "env": {
        "development": {
            "presets": [
                [
                    "@babel/preset-env",
                    {
                        "targets": {
                            "browsers": [
                                ">1%",
                                "last 2 versions",
                                "not ie <= 8"
                            ]
                        }
                    }
                ],
                "@babel/preset-react"
            ],
            "plugins": [
                "@babel/plugin-proposal-class-properties"
            ]
        }
    }
}
```

上面配置中 env 和 development 。

env 用于设置对应环境下的配置, 在编译的时候 babel 会根据当前环境变量的值来决定采用哪个配置。

env 字段的值会从 process.env.BABEL_ENV 获取，如果 BABEL_ENV 不存在，则从 process.env.NODE_ENV 获取，

如果 NODE_ENV 还不存在，则取默认值 development，

使用这样方式进行配置可以定义多个不同的配置项，同时可以通过环境变量来控制要读取的配置。

**客户端代码打包**

webapck 构建，配置一个 npm script 命令

```json
"dev": "webpack --config ./webpack/webpack.dev.config.js",
```

**服务端代码打包**

node 端代码使用的是 es6 module 方式，所以需要编译一次。

node 端所需要的 react 组件代码需要使用 babel 进行编译。

babel 除了可以编译单独的文件外，还可以直接编译整个目录。

这里我们也为其配一个 npm script 命令，并将代码打包到 dist/src 目录下

```json
"babel-node": "babel src -d dist/src"
```

**启动**

执行上面的两个命令

```bash
npm run dev
npm run babel-node
```

ok，到这里浏览器端和服务端所需的最终代码已转换完成。

http 服务启动

```bash
node ./app.js
```

效果

![image.png](https://cdn.nlark.com/yuque/0/2022/png/1614731/1651149359998-279d2653-e1ca-40b1-a44f-67279ecb7fdf.png)

<img src="https://cdn.nlark.com/yuque/0/2022/png/1614731/1651150222369-b7438773-50e4-41d7-8007-5a3c1f1dcd63.png" alt="image.png" style="zoom: 50%;" />

<img src="https://cdn.nlark.com/yuque/0/2022/png/1614731/1651150254048-1c7c49b0-6250-4ad2-aa8a-11119ea930dd.png" alt="image.png" style="zoom: 50%;" />

**双端节点对比**

**意思是浏览器端代码执行时生成的节点结构会和网页内已有的结构进行对比。**

**如果对比失败，则采用浏览器端的结构。**

**这个对比过程由 `react` 完成。**

现在我们就来测试一下，更具象地理解这个概念。

**我们在`react ssr` 中间件内多增加一个标签。**

```jsx
<body>
    <div id="root">
       ${html} <span>测试内容</span> // 增加了span 标签
    </div>
</body>
```

**再次运行服务查看页面，`span` 标签内容会一闪而过。**

**因为节点对比失败，结果使用的是客户端的节点。**

当然还有一个重点就是

**浏览器端的组件渲染和服务端渲染的差别：**

**服务端只是生成`html` 字符串，只会执行组件的`componentWillMount`方法。**

**在浏览器端渲染同时会对比节点，进行节点重用，完成事件的绑定。**

#### 开发体验升级

骨架能用于实际项目开发，最基本的就是要开发方便，用起来省事儿，另一个就是具备一些基础的能力，让我们只专注于业务开发。

哪些能力呢？

比如路由处理、副作用处理、seo支持、css 支持等。

我们现在的 `demo`存在严重的体验问题。

服务的运行需要经过多次手动操作。

前端代码的构建需要手动执行编译

```bash
npm run dev
```

服务端代码的编译需要手动执行编译

```bash
npm run babel-node
```

最后手动启动服务

```bash
node app.js
```

首先要明确我们想达到的一个具体的目标。

- 前后端代码可以自动编译
- 每次修改代码，`node` 服务可以自动重启

**前端代码构建体验优化**

前端代码的构建可以使用 webpack 搞定，开启 watch 功能就可以了，更改了文件就会自动打包。

**后端代码构建**

后端代码这里分为两部分，一个是用来处理请求的 node 代码，只运行于服务端。

另一部分就是组件以及组件相关的代码，也需要在服务端运行来完成组件的 ssr，所以我们也需要打包一份给服务端使用。

之前使用的是 babel cli 来编译的，当然这是一个有效的方法，还有一个更好的办法就是使用 webpack 来编译。

不要以为 webpack 只能用于前端代码的构建，一切皆模块，只要是 js 代码都可以被编译，只是编译的目标平台不同而已。

**node 服务自启**

`node` 服务这里要分两部分来看待。

- 服务的启动
- 服务的自启

单纯的服务启动和自启，很简单，可以使用工具`nodemon`来完成。

不过除了使用工具外是否还有其他的方法处理？

**我们可以使用自定义进程**

让一个服务运行在我们创建的进程中，然后在适当的时机杀掉进程（关闭服务），重新创建一个服务进程启动服务（重启启动）。

**综合分析**

单纯的实现每一步应该没什么难度，但是多个操作如何才能统一为一个操作呢？

也就是可以通过一个命令来启动前端代码编译和监听、后端代码编译和监听、同时启动 node 服务并且能够自动重启。

**自定义进程**

上面的多个操作可以看做是独立的服务，可以让每个服务运行在独立的进程中，然后利用进程间通信来达到我们要的效果。

**启动阶段**

在这个阶段启动各个服务。

**监听阶段**

`webpack`开启 `watch`选项后可以自动编译，这个不用我们来干预。

`node`服务的自启，可以在服务端代码构建完成的时候执行。

```
├── dist // 生产环境打包后的资源存放目录
│ ├── static // 打包的静态资源文件
│ | ├── index.js // 打包后的文件
│ ├── server // 服务端文件目录
│ | ├── app.js // node server 启动入口
├── src // 源码目录
│ ├── client // 前端代码
│ │ ├── app // 前端渲染入口
│ | ├── pages // 业务页面
│ │ | ├── index //默认首页
│ │ ├── router // 路由配置
│ │ ├── common // 存放通用组件和通用模块
│ ├── server // node 代码
│ │ ├── app // 服务入口 
│ │ ├── middlewares //中间件
│ │ | ├──  react-ssr.js //ssr 中间件
│ ├── share // 双端共享的代码可以放这里
├── webpack //构建配置
│ ├── scripts //构建脚本目录
│ │ | ├──  start.js //开发环境的所有服务启动入口
│ ├── webpack.dev.config.js //前端代码的开发环境编译配置
│ ├── webpack.server.config.js 服务端代码的编译配置
```

原来的 app.js 放入 src/server/app/index.js

![image.png](https://cdn.nlark.com/yuque/0/2022/png/1614731/1651154432257-267466d6-f664-481b-a562-d644367afa55.png)

package.json

```json
{
  "name": "my-react-ssr",
  "version": "1.0.0",
  "description": "",
  "main": "app.js",
  "scripts": {
    "dev": "node webpack/scripts/start.js",
    "fe:watch": "webpack --config  ./webpack/webpack.dev.config.js --watch",
    "svr:watch": "node ./webpack/scripts/svr-code-watch.js"
  },
  "author": "",
  "license": "ISC",
  "dependencies": {
    "@babel/cli": "^7.7.4",
    "@babel/core": "^7.7.4",
    "@babel/plugin-proposal-class-properties": "^7.7.4",
    "@babel/preset-env": "^7.7.4",
    "@babel/preset-react": "^7.7.4",
    "babel-loader": "^8.0.6",
    "chalk": "^3.0.0",
    "koa2": "^2.0.0-alpha.7",
    "npm-run-all": "^4.1.5",
    "react": "^16.12.0",
    "react-dom": "^16.12.0",
    "supervisor": "^0.12.0",
    "webpack": "^4.41.2",
    "webpack-cli": "^3.3.10",
    "webpack-node-externals": "^1.7.2",
    "xl_close_port": "^1.0.1",
    "koa-static": "^5.0.0",
    "cross-env": "^6.0.3"
  }
}
```

开发环境的启动入口

```bash
npm run dev // 用来启动开发环境
```

对应的`package.json`配置

```json
"dev": "node webpack/scripts/start.js",
```

**前端代码编译开启 watch**

```json
 "scripts": {
    //...
    "fe:watch": "webpack --config  ./webpack/webpack.dev.config.js --watch",
    //...
  },
```

**服务端代码打包配置**

和前端的打包配置差不多,需要配置 target=node , 增加 externals 选项，使用 webpack-node-externals 来排除不需要打包的模块

webpack/webpack.server.config.js

```js
// webpack 配置文件
const path = require('path')
const nodeExternals = require('webpack-node-externals')
const webpack = require('webpack');
const resolvePath = (pathstr) => path.resolve(__dirname, pathstr);

process.env.BABEL_ENV = 'node'; // 设置 babel 的运行的环境变量

const isProd = process.env.NODE_ENV === 'production';

module.exports = {
    target: 'node',
    entry: resolvePath('../src/server/app/index.js'),// 入口文件
    output: {
        filename: 'app.js',
        path: resolvePath('../dist/server')
    },
    externals: [nodeExternals()],
    module: {
        rules: [{
            test: /\.jsx?$/,
            loader: 'babel-loader',
            exclude: /node_modules/
        }
        ]
    }
}
```

**通过 api 方式启动服务端代码监听**

因为我们需要监听服务端代码的构建过程，

当每次编译完成时，通知主进程重启 `node` 服务，`cli` 模式下无法满足需要，所以需要调用`api` 来执行构建监听。

webpack/scripts/svr-code-watch.js

```js
// 基于 webpack  开启对服务端代码的编译和监听 
// 配置文件为 webpack.server.config.js

const webpack  = require('webpack');
const config =  require('../webpack.server.config');
const constantCode = require('./constant');

config.mode='development';//设置编译模式

// 编译对象
const compiler = webpack(config);

const watching = compiler.watch({
    aggregateTimeout: 300, // 类似节流功能,聚合多个更改一起构建
    ignored: /node_modules/, // 排除文件
    poll: 2000, // 轮循的方式检查变更 单位：秒  ,如果监听没生效，可以试试这个选项.
    'info-verbosity': 'verbose',// 在增量构建的开始和结束时，向控制台发送消息
}, (err, stats) => {
   
    let json = stats.toJson("minimal");
    if(json.errors){
        json.errors.forEach(item => {
            console.log(item);
        });
    }
    if (json.warnings) {
        json.warnings.forEach(item => {
            console.log(item);
        });
    }

    // 编译完成后 通知主进程来重启node 服务
    console.log(constantCode.SVRCODECOMPLETED);
});

compiler.hooks.done.tap('done',function (data) {
    console.log('\n svr code done' ); // 编译完成的时候  可以监听每次的监听
});

// 收到退出信号 退出自身进程
process.stdin.on('data', function (data) {
    if (data.toString() === 'exit') {
        process.exit();
    }
});
```

webpack/scripts/constant.js

该文件用来存放开发环境构建过程中的常量

```js
// 用于开发环境的构建过程中的常量
module.exports = {
    SVRCODECOMPLETED: 'SVRCODECOMPLETED',//服务端代码编译完成
}
```

**配置服务端代码构建执行命令**

```json
  "scripts": {
   //...
    "svr:watch": "node ./webpack/scripts/svr-code-watch.js"
  },
```

**创建 node 服务启动入口**

webpack/scripts/free-port.js

```js
// mac linux 释放指定端口
module.exports = function (port) {
    if (process.platform && process.platform !== 'win32') {
        // mac linux等
        const args = process.argv.slice(2);

        let portArg = args && args[0];
        if (portArg && portArg.indexOf('--') > 0) {
            port = portArg.split('--')[1];
        }
        let order = `lsof -i :${port}`;
        let exec = require('child_process').exec;
        exec(order, (err, stdout, stderr) => {
            if (err) {
                // return console.log(`查看端口命令出错 ${err}`);
            }
            stdout.split('\n').filter(line => {
                let p = line.trim().split(/\s+/);
                let address = p[1];
                if (address != undefined && address != "PID") {
                    exec('kill -9 ' + address, (err, stdout, stderr) => {
                        if (err) {
                            return console.log('释放指定端口失败！！');
                        }
                        console.log('port kill');
                    });
                }
            });
        });
    }
}
```

为了方便控制，单独创建一个开发环境的`node` 服务启动入口,引入打包后的入口文件即可。

webpack/scripts/svr-dev-server.js

```js
// 开发环境 node 服务启动入口
const proConfig = require('../../src/share/pro-config');
// node server port
const nodeServerPort = proConfig.nodeServerPort;

// 启动前检查端口是否占用，杀掉占用端口的进程
require('./free-port')(nodeServerPort);
require('../../dist/server/app');
```

**最终编译入口处理**

通过创建子进程的方式，整合多个服务到统一入口。

使用`const {spawn} = require('child_process');//用于创建子进程` 来创建子进程，此方式为异步执行。

然后通过子进程的`std`进行通信，达到重启 `node`服务的作用。

启动入口 webpack/scripts/start.js

```js
// 整个编译服务的启动入口
// 包括以下功能----
// 服务端代码的首次编译 
// 文件的监听
// 前端代码的打包和 watch
// node服务的启动

// 服务端代码编译完成后 启动node 服务
// 服务端代码每次编译后，重启node 服务

const {spawn} = require('child_process');// 用于创建子进程
const constantCode = require('./constant');
const chalk = require('chalk');// 为控制台输出的信息增加点色彩
const log = console.log;
const proConfig  = require('../../src/share/pro-config');

// node server port
const nodeServerPort = proConfig.nodeServerPort;

log(chalk.red('servers starting....'));


// 前端代码构建 服务进程
const feCodeWatchProcess = spawn('npm', ['run', 'fe:watch'],{stdio:'inherit',shell: process.platform === 'win32'});

// 服务端代码监控和编译进程
const svrCodeWatchProcess = spawn('npm', ['run', 'svr:watch'],{shell: process.platform === 'win32'});

// node 服务进程
let nodeServerProcess=null;
// 启动 node 服务
const startNodeServer = () => { // 重启 node 服务 
    nodeServerProcess && nodeServerProcess.kill();
    nodeServerProcess = spawn('node',['./webpack/scripts/svr-dev-server.js'],{shell: process.platform === 'win32'});
    nodeServerProcess.stdout.on('data', print);
}

// 控制台输出信息
function print(data) {
    let str = data.toString();
    if (str.indexOf(constantCode.SVRCODECOMPLETED) > -1) { // 服务端代码编译完成
        startNodeServer();// 重启 node 服务
    } else {
        console.log(str);
    }
}

// 监听服务端代码构建服务的对外输出  stdout 事件
svrCodeWatchProcess.stdout.on('data',print);

// 杀掉子进程
const killChild=()=>{
    svrCodeWatchProcess && svrCodeWatchProcess.kill();
    nodeServerProcess && nodeServerProcess.kill();
    feCodeWatchProcess && feCodeWatchProcess.kill();
}

// 主进程关闭退出子进程
process.on('close', (code) => {
    console.log('main process  close', code);
    killChild();
});

// 主进程关闭退出子进程
process.on('exit', (code) => {
    console.log('main process  exit', code);
    killChild();
});

// 非正常退出情况
process.on('SIGINT', function () {
    svrCodeWatchProcess.stdin.write('exit', (error) => {
        console.log('svr code watcher process exit!');
    });
    killChild();
});
```

src/share/pro-config.js

该文件是服务端和客户端的公用基础配置文件，所以放在了share目录下

```js
// 双端公用的配置文件

module.exports = {
    wdsPort:9002,//wds 服务的运行端口
    nodeServerPort:9001,//node server 的监听端口
}
```

最后执行下面命令即可

```bash
npm run dev 
```

![image.png](https://cdn.nlark.com/yuque/0/2022/png/1614731/1651155130636-6690639b-5631-4809-9d51-03687fd0885c.png)

![16f56960104bf971tplv-t2oaga2asx.jpg](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1651155149240-f0bed277-ca31-45a6-996b-5e27fd099663.jpeg)

**另外一种方式**

可以使用`npm-run-all`工具,该工具可以同时并行多个`npm` 命令,只需要配置下就可以了。

举个栗子

```bash
npm-run-all --parallel fe:watch svr:watch node:server
```

其实本质也是通过进程来完成的。

喜欢能自己实现的尽量自己来实现，这样可以学以致用，学习的东西不实践的话永远只是纸上谈兵。

当然工具的使用也必不可少，看具体的情况，因为它确实能够帮我们大大提高效率。

﻿**升级内容**

- 前端代码的实时构建
- 服务端所需代码的实时编译
- http 服务的自动重启

**缺陷**

页面的显示内容需要刷新一次才能看到。 后续继续优化，增加热更新。

#### 双端路由同构

**路由问题**

虽然我们能展示页面并且执行事件，但我们还缺一个非常重要的能力。

那就是路由！

现在我们只有一个路由，属于服务端的根路由 / 。

我们无论怎么在浏览器内改变路由地址都会显示同一个UI。

当然我们的项目不可能只有一个页面，那我们该怎样来处理和维护项目的路由呢？

**路由同构**

由于我们打造的是基于服务端渲染的 React SPA 应用开发骨架，所以服务端和客户端都需要对路由进行处理。

我们使用的是 React ，那前端路由肯定会使用 react-router 来处理。

那服务端呢？服务端也需要单独维护一套路由？

当然不需要，这样不科学，更不合理。

现在解决这个问题的办法还是同构 - 路由同构，经过同构后服务端和客户端可以使用同一套路由。

**思路与实现**

**第一次请求页面的时候，服务端接收请求，根据当前的 path 来查找具体的路由，然后根据路由得到具体的组件，然后将组件直出。**

**服务端直出后，页面由浏览器接管，后面的渲染执行就交给前端代码。**

![image.png](https://cdn.nlark.com/yuque/0/2022/png/1614731/1651238002352-c061a630-af6d-466b-9c24-c8fe9915f1c0.png)

package.json

```json
{
    "name": "my-react-ssr",
    "version": "1.0.0",
    "description": "",
    "main": "app.js",
    "scripts": {
      "dev": "node webpack/scripts/start.js",
      "fe:watch": "webpack --config  ./webpack/webpack.dev.config.js --watch",
      "svr:watch": "node ./webpack/scripts/svr-code-watch.js"
    },
    "author": "",
    "license": "ISC",
    "dependencies": {
      "@babel/cli": "^7.7.4",
      "@babel/core": "^7.7.4",
      "@babel/plugin-proposal-class-properties": "^7.7.4",
      "@babel/preset-env": "^7.7.4",
      "@babel/preset-react": "^7.7.4",
      "babel-loader": "^8.0.6",
      "chalk": "^3.0.0",
      "koa2": "^2.0.0-alpha.7",
      "npm-run-all": "^4.1.5",
      "react": "^16.12.0",
      "react-dom": "^16.12.0",
      "react-router": "^5.0.1",
      "react-router-config": "^5.0.1",
      "react-router-dom": "^5.0.1",
      "supervisor": "^0.12.0",
      "webpack": "^4.41.2",
      "webpack-cli": "^3.3.10",
      "webpack-node-externals": "^1.7.2",
      "xl_close_port": "^1.0.1",
      "koa-static": "^5.0.0",
      "cross-env": "^6.0.3"
    }
  }
```

**创建路由对应组件**

src/client/pages/index/index.js

```jsx
import React from 'react';
import { Link } from 'react-router-dom';

export default class Index extends React.Component {
    constructor(props) {
        super(props);
        console.log(this.props);
    }

    handlerClick(){
        alert('一起来玩 react 服务端渲染');
    }

    render() {
        return <div onClick={this.handlerClick}>首页</div>
    }
}
```

src/client/pages/list/index.js

```jsx
import React from 'react';
import { Link } from 'react-router-dom';

export default class Index extends React.Component {
    constructor(props) {
        super(props);
    }
    render() {
        return <div><span>列表页</span></div>
    }
}
```

src/client/pages/about/index.js

```jsx
import React from 'react';
import { Link } from 'react-router-dom';

export default class Index extends React.Component {
    constructor(props) {
        super(props);
    }
    render() {
        return <div><span>关于</span></div>
    }
}
```

﻿**创建一个客户端路由配置**

src/client/app/layout.js

```jsx
import React from 'react';
import { Link,NavLink } from 'react-router-dom';
import {withRouter} from 'react-router';

class Index extends React.Component{
    constructor(props) {
      super(props);
    }
    
    render() {
      return (  
          <div>
              <NavLink to="/index" style={{ marginLeft: "10px" }} >首页</NavLink>
              <NavLink style={{ marginLeft: "10px" }} onClick={this.click}  to="/list">列表页</NavLink>
              <NavLink to="/about" style={{ marginLeft: "10px" }} >关于</NavLink>
              {this.props.children}
          </div>
      )    
    }
}

// 带入路由信息
export default withRouter(Index);
```

**路由组件 Route, Switch, BrowserRouter**

**Route 组件**

用于绑定组件和 path 的关系,一般使用 component 属性指定要渲染的组件,其中 exact 属性表示是否是精确匹配模式，默认是 false 。

**Switch 组件**

使用该组件只会渲染第一个匹配到的路由，否则所有的路由都会渲染。

**BrowserRouter 组件**

基于浏览器 History api 来达到浏览器地址和 UI 同步的能力。

```jsx
<BrowserRouter>
    <Switch>
        <Route path="/root"  exact={true} component={Root}></Route>
        <Route path="/list" exact={true} component={List}></Route>
    </Switch>
</BrowserRouter>
```

**完整的路由配置**

提取为独立的模块，方便维护和管理。

src/client/router/route-config.js

```js
// 路由配置文件
import Index from '../pages/index';
import List from '../pages/list';
import About from '../pages/about';

export default [
    {
        path: '/',
        component: Index,
        exact:true
    },
    {
        path:'/index',
        component:Index,
        exact:true
    },
    {
        path: '/list',
        component: List,
        exact:true
    },
    {
        path: '/about',
        component: About,
        exact: true
    }
]
```

**路由渲染入口配置**

遍历路由配置

﻿src/client/router/index.js

```jsx
import Layout from '../app/layout';
import React  from 'react';
import { Route, Switch } from 'react-router-dom';

function App({ routeList}) {
    return (
        <Layout> 
            <Switch>
                {
                    routeList.map(item=>{
                        return <Route exact key={item.path} {...item}></Route>
                    })
                }
            </Switch>
         </Layout>
    );
}

export default App;
```

**调整客户端组件渲染的入口代码**

src/client/app/index.js

```jsx
import React from 'react';
import ReactDom from 'react-dom';
import App from '../router/index';
import { BrowserRouter} from 'react-router-dom';
import routeList from '../router/route-config';

// 渲染路由
ReactDom.hydrate(
    <BrowserRouter>
        <App routeList={routeList} />
    </BrowserRouter>
, document.getElementById('root'))
```

到这里，客户端路由已生效，路由改变同时能够渲染对应的组件。

![image.png](https://cdn.nlark.com/yuque/0/2022/png/1614731/1651238511321-47cb86a1-4cb1-4e4c-94ce-6de11fd626c2.png)

![image.png](https://cdn.nlark.com/yuque/0/2022/png/1614731/1651238447562-b13e141c-2b55-4d2a-b299-87b9fa414764.png)

![image.png](https://cdn.nlark.com/yuque/0/2022/png/1614731/1651238474813-1df1dd04-84cf-4078-b5a2-77fad2fe4ac2.png)

![image.png](https://cdn.nlark.com/yuque/0/2022/png/1614731/1651238463130-289da0af-8c69-440f-809d-d1adef62b949.png)

﻿**客户端路由基本上已经配置好，服务端该如何处理？**

**服务端路由处理**

`react-router` 已经为我们提供了相关的组件来完成服务端的渲染。

```js
import { StaticRouter} from 'react-router';
```

该组件主要用于服务端渲染，可以帮助我们完成路由查找功能,无需再做手动匹配。

将服务器上接收到的`path`传递给此组件用来匹配，同时支持传入`context`特性，此组件会自动匹配到目标组件进行渲染。

`context`属性是一个普通的`JavaScript`对象。

在组件渲染时，可向该对象添加属性以存储有关渲染的信息，

比如 302 404 等结果状态，然后服务端可以针对不同的状态进行具体的响应处理。

对比来看

```jsx
// 客户端
<BrowserRouter>
    <App/>
</BrowserRouter>
     
// 服务端
<StaticRouter location={req.url} context={context}>   
    <App/>
</StaticRouter>
```

**服务端渲染处理**

src/server/middlewares/react-ssr.js

```js
import React from 'react';
import { renderToString } from 'react-dom/server';
import { StaticRouter, Route} from 'react-router';

import routeList from '../../client/router/route-config';
import Layout from '../../client/app/layout'; // 如果有 layout 组件，也需要一起转换为 html
import App from '../../client/router/index';

export default (ctx, next) => {

    console.log('ctx.request.path', ctx.request.path);

    const path = ctx.request.path;

    let context = {};

    const html = renderToString(<StaticRouter location={path} context={context}><App routeList={routeList}></App></StaticRouter>);

    ctx.body=`<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>my react ssr</title>
</head>
<body>
    <div id="root">${html}</div>
</body>
</html>
<script type="text/javascript"  src="/index.js"></script>
`;

    return next();
}
```

到这里已经实现了基本的双端路由的同构

#### 双端数据同构

实现双端异步数据的获取，数据脱水，数据注水

**什么是数据同构**

整体来说，组件的一些数据需要从接口异步获取后进行渲染，

**数据同构就是服务端和客户端能够使用同一个数据请求处理方法（一套代码），同一份数据进行组件的渲染。**

我们前面实现的组件直出只是将组件转换为了 html 字符串，但是并没有具体的数据，顶多就是个静态页。

比如现在有这么一个需求，要从接口获取数据并且渲染到页面上。

以往在单页应用中，我们一般都将数据的数据的请求处理放在 compoentDidMount 生命周期内，得到数据后更改状态，随之渲染。

异步获取数据

```jsx
  componentDidMount(){
        ...
        fetchData().then(res => {
            this.setState({
                list: res.list
            });
        })
    }
```

在 `render` 方法内组织数据

```jsx
 render() {
      ....
      let {list} = this.state;
      .....
      return <>
          {list && list.map(item => {
              return <div>{item.title}</div>
          })}
      </>
  }
```

以上代码也能在 ssr 模式中执行，

但是无法得到我们期望的效果，数据只能在客户端得到，达不到数据直出的效果，查看网页源代码也没有我们想要的数据。

<img src="https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1651239382556-9574796c-fe3e-4ec6-b199-50f83fcc6a81.jpeg" alt="img" style="zoom: 67%;" />

`componentDidMount`生命周期只会在浏览器端执行，所以如果想让数据也能在服务端渲染就需要做一些特殊的处理。

**数据预取**

在服务端渲染组件的时候要想在直出的组件内容也包含数据，

那就需要提前得到数据，然后将数据作为属性传递给组件，在`constructor`内对组件 `state` 进行初始化。

当组件有了数据，服务端渲染直出的时候自然就会有数据。

**以上这个在服务端渲染前得到数据的过程就是数据预取。**

问题1：客户端和服务端组件渲染执行的声明周期不同，双端如何使用一套代码，代码如何组织呢？

问题2：真实开发中，浏览器的 fetch api 无法在 node 端使用，如何统一呢？

以上两个问题都可以通过同构来解决。

问题2 比较简单，现在已经有很多同构的库来解决。

比如:isomorphic-fetch，axios，这里推荐使用 axios ,对开发者非常友好，可以无差别使用。

js 里无论是函数还是类，到底都是函数，同时都是特殊的对象。

所以我们可以为这些函数添加属性，这个属性也可以被称作为类的静态方法。

静态方法有什么特点？

不需要实例化就可以访问

```js
class Foo {
   run(){
       .....
       console.log('hello');
   }
}

Foo.method = function(){
    console.log('hello method');
}
```

上面的代码可以在浏览器端执行，当然也可在 `node` 端执行。

其实以上思路就是解决问题2的办法，可以把`Foo`想象成我们的`react` 组件。

可以在 `node` 端找到这个路由对应的组件，然后调用这个组件的静态方法来实现数据的预取。

**完整的思路**

- 约定并为组件添加数据预取的静态方法
- 在服务端查找到当前路由对应的组件
- 调用组件的数据预取方法得到数据
- 将数据作为属性传入组件
- 组件内 render 做相应的处理
- 服务端直出组件
- 浏览器接管页面，完成渲染

**实现数据同构**

package.json

```json
{
    "name": "my-react-ssr",
    "version": "1.0.0",
    "description": "",
    "main": "app.js",
    "scripts": {
      "dev": "node webpack/scripts/start.js",
      "fe:watch": "webpack --config  ./webpack/webpack.dev.config.js --watch",
      "svr:watch": "node ./webpack/scripts/svr-code-watch.js"
    },
    "author": "",
    "license": "ISC",
    "dependencies": {
      "@babel/cli": "^7.7.4",
      "@babel/core": "^7.7.4",
      "@babel/plugin-proposal-class-properties": "^7.7.4",
      "@babel/plugin-transform-runtime": "^7.7.6",
      "@babel/preset-env": "^7.7.4",
      "@babel/preset-react": "^7.7.4",
      "babel-loader": "^8.0.6",
      "chalk": "^3.0.0",
      "css-loader": "^3.3.2",
      "koa2": "^2.0.0-alpha.7",
      "npm-run-all": "^4.1.5",
      "postcss-loader": "^3.0.0",
      "react": "^16.8.6",
      "react-dom": "^16.8.6",
      "react-router": "^5.0.1",
      "react-router-config": "^5.0.1",
      "react-router-dom": "^5.0.1",
      "sass-loader": "^8.0.0",
      "style-loader": "^1.0.1",
      "supervisor": "^0.12.0",
      "webpack": "^4.41.2",
      "webpack-cli": "^3.3.10",
      "webpack-node-externals": "^1.7.2",
      "koa-static": "^5.0.0",
      "cross-env": "^6.0.3"
    }
  }
```

**约定数据预取方法**

首先模拟一个异步获取数据的方法，返回一个列表数据。

我这里准备了一份从掘金采集的信息，作为假数据。

src/client/pages/list/data.js

```js
const data = [
  {
    title: "深入浅出TypeScript：从基础知识到类型编程",
    desc: "Vue3 源码及开发必备基础，从基础知识到类型工具设计，从理论到实战，手把手让你从零基础成为进阶使用者。",
    img: "https://user-gold-cdn.xitu.io/2019/11/8/16e4ab5d6aff406a?imageView2/1/w/200/h/280/q/95/format/webp/interlace/1",
  },
  {
    title: "SVG 动画开发实战手册",
    desc: "从0到1，学习SVG动画开发知识，快速高效完成SVG动画效果开发。",
    img: "https://user-gold-cdn.xitu.io/2019/9/26/16d6bda264ac27e4?imageView2/1/w/200/h/280/q/95/format/webp/interlace/1",
  },
  {
    title: "JavaScript 设计模式核⼼原理与应⽤实践",
    desc: "通俗易懂的编程“套路“学。带你深入看似高深实则接地气的设计模式原理，在实际场景中内化设计模式的”道“与”术“。学会驾驭代码，而非被其奴役。",
    img: "https://user-gold-cdn.xitu.io/2019/9/16/16d382e623923d91?imageView2/1/w/200/h/280/q/95/format/webp/interlace/1",
  },
  ...
];

export default data;
```

src/client/router/route-config.js

```js
// 路由配置文件
import Index from '../pages/index';
import List from '../pages/list';

export default [
    {
        path: '/',
        component: Index,
        exact:true
    },
    {
        path:'/index',
        component:Index,
        exact:true
    },
    {
        path: '/list',
        component: List,
        exact:true
    }
]
```

我们约定所有页面组件内的数据预取方法为 getInitialProps ，用于双端调用。

src/client/pages/list/index.js

```jsx
import React from 'react';
import { Link } from 'react-router-dom';
// 导入 - 假数据
import tempData from './data';
// 组件
export default class Index extends React.Component {
    constructor(props) {
        super(props);
    }
    // 静态方法  数据预取方法
    static async getInitialProps() {
        // 模拟数据请求方法
        const fetchData= () => {
            return new Promise(resolve => {
                setTimeout(() => {
                    resolve({
                        code: 0,
                        data: tempData
                    })
                }, 100);
            })
        }

        let res = await fetchData();

        return res;
    }

    handlerClick() {
        alert('一起来玩 react 服务端渲染');
    }

    render() {
        return <div onClick={this.handlerClick}>hello world。</div>
    }
}
```

数据预取方法设置已完成，下一步需要在服务端调用这个方法

**服务端数据预取实现**

`server` 端接到客户端的请求，通过`req url path` 来进行路由匹配，然后得到需要渲染的组件后调用数据预取方法。

路由如何匹配?

每个路由都有 path 属性，所以完全可以根据路由的 path 去匹配。

最简单的方式无疑就是遍历路由配置，对比 req path 和路由 path 。

```js
import Index from '../pages/index';
import List from '../pages/list';

export default [
    {
        path:'/index',
        component:Index
    },
    {
        path: '/list',
        component: List
    }
]

// 根据请求 path 查找路由
const matchRoute = (path, routeList)=>{
    let route;
    for (var item of routeList) {
        if (item.path === path) { // 路由匹配
            route = item;
        }
        break;
    }

    return route;
}
```

上面的代码看着没什么问题,但只能处理静态路由，如果是动态路由的话上面的方法就无能为力了。

静态路由

```js
 <Route path="/item"  exact={true} component={Item}></Route>
```

动态路由

```js
<Route path="/item/:id"  exact={true} component={Root}></Route>
```

这种动态 `path` 就需要正则来进行匹配了。

**path-to-regexp**

此时我们就需要使用工具来处理了。

该工具库用来处理 `url` 中地址与参数，可以将动态路径转换为所对应的正则。

```js
const keys = [];

const regexp = pathToRegexp("/foo/:bar", keys);

// regexp = /^\/foo\/([^\/]+?)\/?$/i
// keys = [{ name: 'bar', prefix: '/', suffix: '', pattern: '[^\\/#\\?]+?', modifier: '' }]
```

还可以直接用于路径匹配

```js
const regexp = pathToRegexp("/:foo/:bar");
// keys = [{ name: 'foo', prefix: '/', ... }, { name: 'bar', prefix: '/', ... }]
 
regexp.exec("/test/route");
//=> [ '/test/route', 'test', 'route', index: 0, input: '/test/route', groups: undefined ]
```

说到这里，问题已经解决了。

不过上面只是介绍下原理，具体的应用其实`react-router`已经内置了，而且内部处理机制也是利用`pathToRegexp`这个库。

**matchPath 方法**

```js
import { matchPath } from "react-router";
```

该方法主要就是用于路由的匹配。

```js
const match = matchPath("/users/123", {
  path: "/users/:id",
  exact: true,
  strict: false
});
```

完善下组件匹配方法

```js
// 根据请求 path 匹配路由，结果返回该路由
const matchRoute = (opt) => {
    let { path } = opt;
    let route;
    for (var item of routeList) {
       if (matchPath(path, item)) {
           route = item;
           break;
       }
    }
    return route;
}
```

**完成数据预取**

- 查找到组件后，调用组件的数据预取方法得到数据
- 得到数据后，将数据传递给组件

公共方法

上面的实现过程中，服务端和浏览器端都用到了路由的匹配，所以我们可以将这个方法提出来，供双端调用。

src/share/match-route.js

```js
// 路由匹配，然后根据匹配的路由得到对应的组件
import { matchPath } from 'react-router';

export default (path, routeList) => { 
    let targetRoute, targetMatch;

    for (var item of routeList) {
        targetMatch = matchPath(path, item);
        if (targetMatch) {
            targetRoute = item; // 查找到第一个路由后停止查找
            break;
        }
    }
    return { targetRoute, targetMatch };
}
```

src/client/pages/list/index.js

```jsx
import React from 'react';

import tempData from './data';

// list 页面 组件
export default class Index extends React.Component {
    constructor(props) {
        super(props);   
        let initialData = null; // 初始化数据
        initialData = props.staticContext.initialData || {};
        this.state = initialData;
    }

    static async getInitialProps() {
        console.log('fetch data');
        // 模拟数据请求方法
        const fetchData= () => {
            return new Promise(resolve => {
                setTimeout(() => {
                    resolve({
                        code:0,
                        data: tempData
                    })
                }, 100);
            })
        }

        let res = await fetchData();

        return res;
    }

    render() {
        // 渲染数据
        const { data } = this.state;
        
        return (
            <div>
                {data && data.map((item,index)=>{
                    return <div key={index}>
                        <h3>{item.title}</h3>
                        <p>{item.desc}</p>
                    </div>
                })}
                {!data && <div>暂无数据</div>}
            </div>
        )
    }
}
```

src/server/middlewares/react-ssr.js

```jsx
// 完成 react ssr 工作的中间件
// 引入 Index 组件
import React from 'react';
import { renderToString } from 'react-dom/server';
import { StaticRouter } from 'react-router';

import routeList from '../../client/router/route-config';
import matchRoute from '../../share/match-route';
import App from '../../client/router/index';

export default async (ctx, next) => {

    const path = ctx.request.path;

    if (path.indexOf('.') > -1) {
        ctx.body = null;
        return next();
    }

    console.log('ctx.request.path.', ctx.request.path);

    // 查找到的目标路由对象
    let matchResult = matchRoute(path, routeList);
    let { targetRoute } = matchResult;

    // 得到数据
    let fetchDataFn = targetRoute.component.getInitialProps;
    let fetchResult = {};
    if (fetchDataFn) {
        fetchResult = await fetchDataFn();
    }

    // 将预取数据在这里传递过去 组件内通过 props.staticContext 获取
    const context = {
        initialData: fetchResult
    };

    const html = renderToString(<StaticRouter location={path} context={context}>
        <App routeList={routeList}></App>
    </StaticRouter>);

    ctx.body=`<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>my react ssr</title>
</head>
<body>
    <div id="root">${html}</div>
</body>
</html><script type="text/javascript"  src="/index.js"></script>`;

    await next();
}
```

效果

![img](https://cdn.nlark.com/yuque/0/2022/png/1614731/1651242145299-e87ea4af-36f3-460f-981b-250225c51e02.png)

![img](https://cdn.nlark.com/yuque/0/2022/png/1614731/1651243183400-d9698e89-0042-41e2-abd8-502ca38a67d4.png)

如果查看页面效果的话，页面内容会一闪而过，最终页面只显示一个`暂无数据` 。

因为在浏览器端进行渲染的时候，没有该数据。

结果导致双端节点对比失败，最终采用的是客户端的渲染结果。

所以，浏览器端也需要有相同的数据，使组件可以渲染出和服务端相同的结构，才能够通过双端节点对比。

才不会被客户端的结构覆盖,从而使用服务端直出的 html 结构。

浏览器端组件渲染前如何才能得到服务端的数据呢？

得到了数据如何传递给组件呢？

**数据脱水**

通过接口请求，那就是重复请求了,没意义。

服务端返回相应数据后页面就被浏览器接管了，所以只能在接管之前做一些操作。

我们可以直接把数据也吐给浏览器，将数据序列化后作为字符串直出到页面，这样在浏览器端就可以在组件渲染前很方便的得到数据。

为了防止 xss 攻击，咱们这里将数据放到了 textarea 标签内。

在直出组件的时候同时将数据源也输出给浏览器，而这个过程就叫做`数据脱水`。

src/server/middlewares/react-ssr.js

```jsx
// 完成 react ssr 工作的中间件
// 引入 Index 组件
import React from 'react';
import { renderToString } from 'react-dom/server';
import { StaticRouter } from 'react-router';

import routeList from '../../client/router/route-config';
import matchRoute from '../../share/match-route';
import App from '../../client/router/index';

export default async (ctx, next) => {

    const path = ctx.request.path;

    if (path.indexOf('.') > -1) {
        ctx.body = null;
        return next();
    }

    console.log('ctx.request.path.', ctx.request.path);

    // 查找到的目标路由对象
    let matchResult = matchRoute(path, routeList);
    let { targetRoute } = matchResult;

    // 得到数据
    let fetchDataFn = targetRoute.component.getInitialProps;
    let fetchResult = {};
    if (fetchDataFn) {
        fetchResult = await fetchDataFn();
    }

    // 将预取数据在这里传递过去 组件内通过 props.staticContext 获取
    const context = {
        initialData: fetchResult
    };

    const html = renderToString(<StaticRouter location={path} context={context}>
        <App routeList={routeList}></App>
    </StaticRouter>);

    ctx.body=`<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>my react ssr</title>
</head>
<body>
    <div id="root">${html}</div>
    <textarea id="ssrTextInitData" style="display:none;">${JSON.stringify(fetchResult)}</textarea>
</body>
</html><script type="text/javascript"  src="/index.js"></script>`;

    await next();
}
```

![img](https://cdn.nlark.com/yuque/0/2022/png/1614731/1651242243761-e43f3689-7962-4e87-8d5f-74bac9523881.png)

**数据注水**

浏览器端得到了数据后，如何使用该数据呢？

- 浏览器端在组件渲染前，得到初始化数据
- 将数据作为属性传递给组件

可以根据当前的 path 匹配到目标路由，然后在路由的 render 方法内将数据传递给组件即可。

因为在服务端渲染的时候我们传入初始数据的属性为 initialData ,所以客户端最好使用同一个属性来传递。

这个将数据和组件调和渲染的过程就是`数据注水`。

src/client/app/index.js

```jsx
// 浏览器端页面结构渲染入口
import React from 'react';
import ReactDom from 'react-dom';
import { BrowserRouter } from 'react-router-dom';

import App from '../router/index';
import routeList from '../router/route-config';
import matchRoute from '../../share/match-route';

function clientRender() {
    // 初始数据
    let initialData = JSON.parse(document.getElementById('ssrTextInitData').value);

    // 查找路由
    let matchResult = matchRoute(document.location.pathname, routeList);
    let { targetRoute } = matchResult;
    if (targetRoute) {
        // 设置组件初始化数据
        targetRoute.initialData = initialData;
    }

    // 渲染 index
    ReactDom.hydrate(
    <BrowserRouter>
        <App routeList={routeList} />
    </BrowserRouter>, document.getElementById('root'))

}
// 渲染入口
clientRender();
```

src/client/router/index.js

在 App 组件内做的一些处理

```jsx
// 路由配置文件
import React  from 'react';
import Layout from '../app/layout';

import { Route, Switch } from 'react-router-dom';

function Page404() {
    return <div>404拉</div>
}

function App({routeList}) {
    return (
        <Layout> 
           <Switch>
            {
                routeList.map(item => {
                    // 判断是否有初始数据
                    return item.initialData ? <Route key={item.path} exact={item.exact} path={item.path}  render={(props)=>{
                        return <item.component {...props} initialData={item.initialData}></item.component>
                    }}></Route> : <Route key={item.path} {...item}></Route>
                })
            }
            <Route to="*" component={Page404}></Route>
        </Switch>
        </Layout>
    );
}

export default App;
```

**解决问题**

如果我们第一次就访问`/index`路由，再点击列表页链接，列表页的数据竟然消失了。

我们都知道首次进入页面走服务端 `ssr`，后续访问就`spa`。

现在列表页的数据只能在`ssr` 模式下才能拿到，如果是 `spa` 就拿不到了。

如何处理？

这个就比较简单了，和我们平时开发`spa`一样。

我们可以在`componentDidMount`内获取数据然后更新 `state`。

ps: 实现比较简单，但是需要做个容错，判断下是否有初始化数据，以免重复请求，浪费资源。



在 webpack.dev.config.js 与 webpack.server.config.js 中分别添加自定义plugin, 标注`__SERVER__`全局变量，

用来区别是服务端渲染还是浏览器渲染。

webpack.dev.config.js

```js
const path = require('path')
const webpack = require('webpack');
const resolvePath = (pathstr) => path.resolve(__dirname, pathstr);


module.exports = {
    mode: 'development',
    entry: resolvePath('../src/client/app/index.js'),//入口文件
    output: {
        filename: 'index.js',
        path: resolvePath('../dist/static')
    },
    module: {
        rules: [{
                test: /\.jsx?$/,
                loader: 'babel-loader',
                exclude: /node_modules/
            }
        ]
    },
    plugins: [
        new webpack.DefinePlugin({
            '__SERVER__': false
        })
    ]
}
```

webpack.server.config.js

```js
// webpack 配置文件
const path = require('path')
const nodeExternals = require('webpack-node-externals')
const webpack = require('webpack');
const resolvePath = (pathstr) => path.resolve(__dirname, pathstr);

process.env.BABEL_ENV = 'node'; //设置 babel 的运行环境

const isProd = process.env.NODE_ENV === 'production';

module.exports = {
    mode:'development',
    target: 'node',
    entry: resolvePath('../src/server/app/index.js'), // 入口文件
    output: {
        filename: 'app.js',
        path: resolvePath('../dist/server')
    },
    externals: [nodeExternals()],
    module: {
        rules: [{
            test: /\.jsx?$/,
            loader: 'babel-loader',
            exclude: /node_modules/
        }]
    },
    plugins: [
        new webpack.DefinePlugin({
            '__SERVER__':true
        })
    ]
}
```

src/client/pages/list/index.js

```jsx
// index 组件
import React from 'react';
import tempData from './data';

// list 页面 组件
export default class Index extends React.Component {
    constructor(props) {
        super(props);   

        let initialData = null; // 初始化数据
        if (__SERVER__) {
            // 服务端执行
            initialData = props.staticContext.initialData || {};
        } else {
            // 客户端渲染
            initialData = props.initialData || {};
        }
        this.state = initialData;
    }

    static async getInitialProps() {
        console.log('fetch data');
        // 模拟数据请求方法
        const fetchData = () => {
            return new Promise(resolve => {
                setTimeout(() => {
                    resolve({
                        code: 0,
                        data: tempData
                    })
                }, 100);
            })
        }

        let res = await fetchData();

        return res;
    }

    componentDidMount(){
        if (!this.state.data) {
            // 如果没有数据，则进行数据请求
            Index.getInitialProps().then(res => {
                this.setState({
                    data: res.data || []
                })
            })
        }
    }

    render() {
        // 渲染数据
        const { data } = this.state;

        return (
            <div>
                {data && data.map((item,index)=>{
                    return <div key={index}>
                        <h3>{item.title}</h3>
                        <p>{item.desc}</p>
                    </div>
                })}
                {!data&&<div>暂无数据</div>}
            </div>
        )
    }
}
```

关键步骤如下:

- 方法同构： 为组件声明`getInitialProps`静态方法，这是一个同构方法，用于双端的数据获取
- 数据预取：在服务端通过路由匹配找到目标的组件，然后调用组件的数据预取方法得到数据
- 将初始化数据作为属性传递给组件
- 数据脱水：将数据序列化,和 `html`字符串 一起直出返回给浏览器端
- 数据注水: 浏览器端得到服务端直出的数据，也通过属性将数据传给组件
- 如果初始化数据不存在，则可以在`componentDidMount`生命周期内请求一次数据

#### SEO TDK 支持

TDK

- title 当前页面的标题
- description 当前页面的描述
- keywords 当前页面的关键词

SEO 优化除了需要基础数据外，TDK也是非常重要的,否则搜索引擎很难知道你这页面是干啥的。

一个网站有很多页面，每个页面的信息是不同的，所以我们需要动态的设置，让每个页面的 tdk 可以 不同。

**动态 tdk - 数据预取中做手脚**

可以从我们的数据预取方法返回当前页面的 tdk 信息 ，服务端得到数据后可直出到页面，

同时浏览器端在 componentDidMount 生命周期内通过 DOM 操作，修改当前页面的 title 信息,来避免单页跳转时 title 不变的问题。

在数据预取中返回 `tdk`还有一个好处，就是可以在预取方法内利用当前的数据来组织 `tdk`,让每个页面的 `tdk` 不同。

**组件内需要的处理**

componentDidMount内通过 DOM 操作设置页面标题，防止页面切换的时候标题不更新。

src/client/pages/list/index.js

```jsx
// index 组件
import React from 'react';

import tempData from './data';

// list 页面 组件
export default class Index extends React.Component {
    constructor(props) {
        super(props);   

        let initialData = null; // 初始化数据
        if (__SERVER__) {
            // 如果是当然是服务端执行
            initialData = props.staticContext.initialData || {};
        } else {
            // 客户端渲染
            initialData = props.initialData || {};
        }
        this.state = {
            page: initialData.page,
            fetchData: initialData.fetchData
        };
    }

    static async getInitialProps() {
        console.log('fetch data');
        // 模拟数据请求方法
        const fetchData = () => {
            return new Promise(resolve => {
                setTimeout(() => {
                    resolve({
                        code: 0,
                        data: tempData
                    })
                }, 100);
            })
        }

        let res = await fetchData();

        return {
            fetchData:res,
            page:{
                tdk:{
                    title: '淘宝网 - 淘！我喜欢',
                    keywords: '淘宝,掏宝,网上购物,C2C,在线交易,交易市场,网上交易,交易市场,网上买,网上卖,购物网站,团购,网上贸易,安全购物,电子商务,放心买,供应,买卖信息,网店,一口价,拍卖,网上开店,网络购物,打折,免费开店,网购,频道,店铺',
                    description: '淘宝网 - 亚洲较大的网上交易平台，提供各类服饰、美容、家居、数码、话费/点卡充值… 数亿优质商品，同时提供担保交易(先收货后付款)等安全交易保障服务，并由商家提供退货承诺、破损补寄等消费者保障服务，让你安心享受网上购物乐趣！'
                }
            }
        };
    }

    componentDidMount(){
        if (!this.state.data) {
            // 如果没有数据，则进行数据请求
            Index.getInitialProps().then(res => {
                this.setState({
                    fetchData: res.fetchData || [],
                    page: res.page
                })
                document.title = res.page.tdk.title;
                document.querySelector('meta[name="keywords"]').content = res.page.tdk.keywords;
                document.querySelector('meta[name="description"]').content = res.page.tdk.description;
            })
        }
        if (this.state.page && this.state.page.tdk) {
            document.title = this.state.page.tdk.title;
            document.querySelector('meta[name="keywords"]').content = this.state.page.tdk.keywords;
            document.querySelector('meta[name="description"]').content = this.state.page.tdk.description;
        }
    }

    render() {
        // 渲染数据
        const { data } = this.state.fetchData || {};

        return (
            <div>
                {data && data.map((item,index)=>{
                    return <div key={index}>
                        <h3>{item.title}</h3>
                        <p>{item.desc}</p>
                    </div>
                })}
                {!data&&<div>暂无数据</div>}
            </div>
        )
    }
}
```

**服务端获取 tdk 信息**

服务端代码调整,我们可以直接从数据预取的方法里得到页面的 tdk 信息，所以可以很方便的直出到页面内。

src/server/middlewares/react-ssr.js

```jsx
// 完成 react ssr 工作的中间件
// 引入 Index 组件
import React from 'react';
import { renderToString } from 'react-dom/server';
import { StaticRouter } from 'react-router';

import routeList from '../../client/router/route-config';
import matchRoute from '../../share/match-route';
import App from '../../client/router/index';

export default async (ctx, next) => {

    const path = ctx.request.path;

    if (path.indexOf('.') > -1) {
        ctx.body = null;
        return next();
    }

    console.log('ctx.request.path', ctx.request.path);

    // 查找到的目标路由对象
    let matchResult = matchRoute(path, routeList);
    let { targetRoute } = matchResult;

    // 得到数据
    let fetchDataFn = targetRoute.component.getInitialProps;
    let fetchResult = {};
    if (fetchDataFn) {
        fetchResult = await fetchDataFn();
    }

    let { page } = fetchResult || {};

    let tdk = {
        title: '默认标题 - my react ssr',
        keywords: '默认关键词',
        description: '默认描述'
    };

    if (page && page.tdk) {
        tdk = page.tdk;
    }

    // 将预取数据在这里传递过去 组件内通过 props.staticContext 获取
    const context = {
        initialData: fetchResult
    };

    const html = renderToString(<StaticRouter location={path} context={context}>
        <App routeList={routeList}></App>
    </StaticRouter>);

    ctx.body = `<!DOCTYPE html>
    <html lang="en">
        <head>
            <meta charset="UTF-8">
            <title>${tdk.title}</title>
            <meta name="keywords" content="${tdk.keywords}" />
            <meta name="description" content="${tdk.description}" />
        </head>
        <body>
            <div id="root">${html}</div>
            <textarea id="ssrTextInitData" style="display:none;">${JSON.stringify(fetchResult)}</textarea>
        </body>
    </html>
    <script type="text/javascript"  src="/index.js"></script>`;

    await next();
}
```



####  CSS 资源处理

CSS 模块无法在服务端解析，要把 css 融合到我们的应用骨架里，达到可以给组件添加样式，美化页面的目的。

**安装所需 loader**

```bash
 sass-loader style-loader postcss-loader css-loader autoprefixer
```

package.json（node 版本切换为 12.22.0）

```json
{
    "name": "my-react-ssr",
    "version": "1.0.0",
    "description": "",
    "main": "app.js",
    "scripts": {
        "dev": "node webpack/scripts/start.js",
        "fe:watch": "webpack --config  ./webpack/webpack.dev.config.js --watch",
        "svr:watch": "node ./webpack/scripts/svr-code-watch.js"
    },
    "author": "",
    "license": "ISC",
    "dependencies": {
        "@babel/cli": "^7.7.4",
        "@babel/core": "^7.7.4",
        "@babel/plugin-proposal-class-properties": "^7.7.4",
        "@babel/plugin-transform-runtime": "^7.7.6",
        "@babel/preset-env": "^7.7.4",
        "@babel/preset-react": "^7.7.4",
        "autoprefixer": "^10.4.5",
        "babel-loader": "^8.0.6",
        "chalk": "^3.0.0",
        "cross-env": "^6.0.3",
        "css-loader": "^3.6.0",
        "koa-static": "^5.0.0",
        "koa2": "^2.0.0-alpha.7",
        "node-sass": "^4.13.0",
        "npm-run-all": "^4.1.5",
        "postcss": "^8.1.0",
        "postcss-loader": "^4.0.3",
        "react": "^16.14.0",
        "react-dom": "^16.8.6",
        "react-router": "^5.0.1",
        "react-router-config": "^5.0.1",
        "react-router-dom": "^5.0.1",
        "sass-loader": "^7.3.1",
        "style-loader": "^1.3.0",
        "supervisor": "^0.12.0",
        "webpack": "^4.41.2",
        "webpack-cli": "^3.3.10",
        "webpack-node-externals": "^1.7.2"
    },
    "devDependencies": {
        "mini-css-extract-plugin": "^1.6.2"
    }
}
```

**浏览器端 - loader 配置**

加入 css 的相关loader配置，开发环境中使css打包进 js，style-loader会帮助我们将 css内联在页面内。

webpack/webpack.dev.config.js

```js
const path = require('path')
//提取 css  插件
const MiniCssExtractPlugin = require('mini-css-extract-plugin');
const resolvePath = (pathstr) => path.resolve(__dirname, pathstr);


module.exports = {
    mode: 'development',
    entry:{
        main: resolvePath('../src/client/app/index.js'), //入口文件
    },
    output: {
        filename: '[name].js',
        path: resolvePath('../dist/static')
    },
    module: {
        rules: [{
                test: /\.jsx?$/,
                loader: 'babel-loader',
                exclude: /node_modules/
            },
            {
                test: /\.(sa|sc|c)ss$/,
                use: [
                    {
                        loader: MiniCssExtractPlugin.loader,
                    },
                    {loader:'css-loader'},
                    {
                        loader: "postcss-loader"
                    },
                    {
                        loader: "sass-loader"
                    }
                ]
            },
            {
                test: /\.(png|jpg|gif)$/,
                use: [{
                    loader: 'file-loader',
                    options: {
                        name: 'img/[name].[ext]' //配置图片的输出路径和名称
                    }
                }]

            }
        ]
    },
    plugins: [
        new MiniCssExtractPlugin({
        filename: '[name].css' //设置名称
    })]
}
```

配置 autoprefixer 完成 css 前缀转换

在项目根目录创建 postcss.config.js 文件进行配置,也可以直接和 loader 写在一起

postcss.config.js

```
module.exports = {
    plugins: [require('autoprefixer')]  // 引用该插件即可了
}
```

**添加 css 测试代码**

给 layout 组件添加 css，并导入

src/client/app/layout.scss

```scss
body {
    background-color: #f4f5f5;
}

.layout-box{
    max-width:750px;
    margin: 0 auto;
    text-align: center;
    background: #fff;

    h1{
        margin-top: 20px;
        margin-top: 20px;
    }
}
```

src/client/app/layout.js

```jsx
import React from 'react';
import { NavLink } from 'react-router-dom';
import { withRouter } from 'react-router';
import './layout.scss';

class Index extends React.Component{
   constructor(props){
       super(props);
   }
   
   render(){
       return (
           <div className="layout-box">
               <NavLink to="/index" style={{ marginLeft: "10px" }}>首页</NavLink>   
               <NavLink style={{ marginLeft: "10px" }} to="/list">列表页</NavLink>
               {this.props.children} 
           </div>
        )
   }
}

//带入路由信息
export default withRouter(Index);
```

给index组件添加css，并导入

src/client/pages/index/index.scss

```scss
.page-index-box{
    width: 750px;
}
```

src/client/pages/index/index.js

```jsx
import React from 'react';

import './index.scss';
export default class Index extends React.Component {
    constructor(props) {
        super(props);
        console.log(this.props);
    }

    handlerClick(){
        alert('一起来玩 react 服务端渲染');
    }

    render() {
        return (<div className="page-index-box" onClick={this.handlerClick}>首页</div>)
    }
}
```

结果运行npm run dev时，服务端代码打包失败。

由于组件会在双端构建，我们在组件内导入了 css，

而服务端webpack配置文件没有配置相关的 css loader，所以服务端的代码打包失败了。

既然服务端无法处理`css`模块，而我们也不能给服务端配置添加相关的 `css loader`,否则`css`也会被打包进`js`。

所以需要采取其他方法，这里有个取巧的方式，我们可以在服务端代码构建前干掉这行代码。

- 如何删除这行导入？

方法有很多种，要么是借助工具，要么是自己写插件。

在这里我们自定义一个 `babel plugin` 来搞定。

webpack/babel/plugin/no-require-css.js

```js
/**
 * 删除 css 的引入
 * 可能社区已经有现成的插件但是不想费劲儿找了，还是自己写一个吧。^_^!
 * @param {*} param0 
 */
 module.exports = function ({ types: babelTypes }) {
    console.log('no-require-css 执行');
    return {
        name: "no-require-css",
        visitor: {
            ImportDeclaration(path, state) {
                let importFile = path.node.source.value;
                if(importFile.indexOf('.scss')>-1){
                    //如果引入了 css 需要干掉
                    path.remove();
                }
            }
        }
    };
};
```

在`.babelrc`内配置这个插件

.babelrc

```json
{
    "env": {
        "node":{
            "presets": [
                [
                    "@babel/preset-env",
                    {
                        "targets":{
                            "node":"current"
                        }
                    }
                ],
                "@babel/preset-react"
            ],
            "plugins": [
                "@babel/plugin-proposal-class-properties",
                "./webpack/babel/plugin/no-require-css"
            ]
        },
        "development": {
            "presets": [
                [
                    "@babel/preset-env",
                    {
                        "targets": {
                            "browsers": [
                                ">1%",
                                "last 2 versions",
                                "not ie <= 8"
                            ]
                        }
                    }
                ],
                "@babel/preset-react"
            ],
            "plugins": [
                "@babel/plugin-proposal-class-properties",
                "@babel/plugin-transform-runtime"
            ]
        }
    }
}
```

我们已经实现了css的渲染，但是有些勉强，效果不够好，当页面刷新或者第一次进入的时候，页面会抖动。

因为第一次进入是服务端直出的 `html` 结构，没有 `css` 。

`css` 是在客户端`js` 代码执行后动态插入到 `head` 内的，所以会出现抖动。

- 如何解决这个问题呢？

我们可以采用传统的方式来解决，将所有的`css`模块 打包成一个文件，然后在服务端直出的时候带上它，作为资源文件加载。

例如:

```html
<link rel="stylesheet" type="text/css" href="//s1.bigerfe.com/zz-static/css/styles.04403cf0.css">
```

**css 合并**

在`webpack4`里需要使用`mini-css-extract-plugin`插件来将 `css` 进行合并。

看下具体配置

将`style-loader`替换为

```js
//...
  {
    loader: MiniCssExtractPlugin.loader,
  }
//...
```

设置`plugin`

```js
  //...
  plugins: [
        new MiniCssExtractPlugin({
        filename: '[name].css' //设置名称
    })]
```

上面的`[name]`为资源的名称，会使用当前配置的`entry`的名称，所以我们调整下`entry`定义,增加入口`main`

```js
//...
 entry:{
        main: resolvePath('../src/client/app/index.js'), //入口文件
    }
//...
```

同时将`js`的 `bundle` 的名称改为占位

```js
  output: {
        filename: '[name].js',
        path: resolvePath('../dist/static')
    }
```

src/server/middlewares/react-ssr.js

```js
// 完成 react ssr 工作的中间件
// 引入 Index 组件
import React from 'react';
import { renderToString } from 'react-dom/server';
import { StaticRouter } from 'react-router';

import routeList from '../../client/router/route-config';
import matchRoute from '../../share/match-route';
import App from '../../client/router/index';

export default async (ctx, next) => {

    const path = ctx.request.path;

    if (path.indexOf('.') > -1) {
        ctx.body = null;
        return next();
    }

    console.log('ctx.request.path', ctx.request.path);

    // 查找到的目标路由对象
    let matchResult = matchRoute(path, routeList);
    let { targetRoute } = matchResult;

    // 得到数据
    let fetchDataFn = targetRoute.component.getInitialProps;
    let fetchResult = {};
    if (fetchDataFn) {
        fetchResult = await fetchDataFn();
    }

    let { page } = fetchResult || {};

    let tdk = {
        title: '默认标题 - my react ssr',
        keywords: '默认关键词',
        description: '默认描述'
    };

    if (page && page.tdk) {
        tdk = page.tdk;
    }

    // 将预取数据在这里传递过去 组件内通过 props.staticContext 获取
    const context = {
        initialData: fetchResult
    };

    const html = renderToString(<StaticRouter location={path} context={context}>
        <App routeList={routeList}></App>
    </StaticRouter>);

    ctx.body = `<!DOCTYPE html>
    <html lang="en">
        <head>
            <meta charset="UTF-8">
            <title>${tdk.title}</title>
            <meta name="keywords" content="${tdk.keywords}" />
            <meta name="description" content="${tdk.description}" />
            <link rel="stylesheet" type="text/css" href="/main.css" />
        </head>
        <body>
            <div id="root">${html}</div>
            <textarea id="ssrTextInitData" style="display:none;">${JSON.stringify(fetchResult)}</textarea>
        </body>
    </html>
    <script type="text/javascript"  src="/index.js"></script>`;

    await next();
}
```

**生产环境都需要做哪些处理呢？**

- 对应的应该拥有一个独立的生产环境配置文件
- 设置环境变量，区分开发和生产环境
- 压缩`js css`资源，体积更小，提高下载速度
- `js`分包，基础库和业务代码分别打包，可以提高缓存利用率,提高页面渲染效率，节省用户流量
- 为打包的`bundle`名称配置`hash`值,这样有利于发布和资源缓存
- 生成资源映射表，用于服务端使用
- 有独立的发布命令
- 可以在本机运行生产环境 `server`，方便本地调试
- 开发环境做相应的调整























































































