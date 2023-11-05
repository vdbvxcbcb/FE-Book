



基于 pages 的路由，pages 下的目录和文件组成路由



客户端导航 <Link> 不会是页面整个刷新，而是使用 js 实现页面间跳转，原生的 <a> 会刷新页面

使用方式：<Link> 嵌套 <a> ，类似 className 的特性要添加到 <a> 上而不是 <link>

例如主页背景颜色改成其他颜色后，跳转到其他页面再返回，主页背景颜色不会变

代码分割只加载该页面所需的代码，其它页面的代码不会加载，优化加载速度

Next.js 在后台预先加载该页面相关链接的其它页面代码，所以页面切换很快！



Next.js 内置支持 CSS、 CSS Modules 和 Sass，允许 import .css 和 .scss/.sass 文件

Tailwind CSS 也支持



添加静态资源 

Next.js 确保图像是响应式的。

Next.js不是在构建时对图片进行优化，而是在用户请求时按需对图片进行优化。

与静态网站生成器和纯静态解决方案不同，无论是运送10张图片还是1000万张图片，构建时间都不会增加。

默认情况下，图片是懒加载的。这意味着页面速度不会因为视口外的图像而受到影响。图片在滚动到视口时才被加载.



自定义<head> 

<Head> 是 Next.js 内置的 React 组件，用于代替原生的 <head>



<Script> 组件加载第三方脚本。

strategy 控制何时加载第三方脚本。

lazyOnload 告诉 Next.js 在浏览器空闲时延迟加载这个特定的脚本

onLoad 用于在脚本加载完成后立即运行任何 JavaScript 代码。





Next.js 内置了 CSS-in-JS 库 styled-jsx， React component 的方式写 css，css 样式是局部的

CSS Modules 添加局部 CSS，pages/_app.js 添加全局 css

CSS Modules 

代码分割对 CSS Modules 同样有效 ，确保每个页面加载最小量的 css 

CSS Modules 在构建时从 JavaScript 包中提取，生成 .css文件，由 Next.js 自动加载。



共享的 CSS 布局组件 <layout>

components/layout



只能在 pages/_app.js 添加全局 css



Next.js 使用 PostCSS 编译 css，顶级目录下添加 postcss.config.js 可自定义配置



Next.js 会预渲染每个页面。这意味着 Next.js 会提前为每个页面生成 HTML，

而不是由客户端的 JavaScript 完成。

每个生成的 HTML 都与该页面所需的最小 JavaScript 代码相关联。

当一个页面被浏览器加载时，它的 JavaScript 代码就会运行，使页面可交互。（这个过程叫注水）



Next.js 可以让应用程序在没有 JavaScript 的情况下被渲染。

这是因为 Next.js 已经将应用程序预先渲染成静态 HTML，

使你能够在不运行 JavaScript 的情况下看到应用程序的界面。



Next.js有两种形式的预渲染。静态生成和服务器端渲染。区别在于它何时为一个页面生成 HTML。

静态生成在构建时生成 HTML ,预先渲染的 HTML会在每个请求中被重复使用。

服务器端渲染是在每次请求时生成 HTML 的预渲染方法。



Next.js 允许选择对每个页面使用哪种预渲染形式。

可以创建一个 "混合 " 的 Next.js应用程序，对大多数页面使用静态生成，对其他页面使用服务器端渲染。

建议尽可能使用静态生成（不管有数据还是无数据），

因为页面可以一次建成，并由CDN提供服务，这比每次请求时由服务器渲染页面要快得多。

如果页面显示的是经常更新的数据，而且每次请求时页面内容都会改变。

在这种情况下，使用服务器端渲染 。

这会比较慢，但预渲染的页面将始终是最新的。

或者可以跳过预渲染，使用客户端的 JavaScript 来填充经常更新的数据，例如，

这种方法对用户仪表板页面很有效，

因为仪表盘是一个私人的、针对用户的页面，SEO 并不重要，

而且该页面不需要预先渲染。数据是经常更新的，这就需要在请求时获取数据。。



Next.js中，导出一个页面组件时，

可以导出一个叫做 getStaticProps 的异步函数。

如果这样做，那么 getStaticProps 会在构建时运行，

并且在这个函数中，可以获取外部数据并将其作为 prop 给页面

```js
export default function Home(props) { ... }

export async function getStaticProps() {
  // Get external data from the file system, API, DB, etc.
  const data = ...

  // The value of the `props` key will be
  //  passed to the `Home` component
  return {
    props: ...
  }
}
```

本质上，getStaticProps告诉Next.js："嘿，这个页面有一些依赖的数据--所以当你在构建并预渲染这个页面时，确保先获取它们！"

getStaticProps 只能从页面中导出。你不能从非页面文件中导出它。

这个限制的原因之一是 React 需要在页面渲染之前拥有所有需要的数据。



Next.js 团队创建了一个名为 SWR 的 React hook  用于获取数据。

如果要在客户端获取数据，强烈推荐它。

它可以处理缓存、重新验证、间隔重新获取数据等等。

```js
import useSWR from 'swr'

function Profile() {
  const { data, error } = useSWR('/api/user', fetch)

  if (error) return <div>failed to load</div>
  if (!data) return <div>loading...</div>
  return <div>hello {data.name}!</div>
}
```























