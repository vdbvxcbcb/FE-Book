## 学习模块化设计[#](https://vue3.chengpeiquan.com/guide.html#学习模块化设计)

在了解 Node 项目之后，就要开始通过编码来加强对 Node.js 的熟悉程度了，但在开始使用之前，还需要了解一些概念。

在未来的日子里（不限于本教程，与前端工程化相关的工作内容息息相关），会频繁的接触到两个词：模块（ Module ）和包（ Package ）。

模块和包是 Node 开发最重要的组成部分，不管是全部自己实现一个项目，还是依赖各种第三方轮子来协助开发，项目的构成都离不开这两者。

### 模块化解决了什么问题[#](https://vue3.chengpeiquan.com/guide.html#模块化解决了什么问题)

在软件工程的设计原则里，有一个原则叫 “单一职责” 。

假设一个代码块负责了多个职责的功能支持，在后续的迭代过程中，维护成本会极大的增加，虽然只需要修改这个代码块，但需要兼顾职责 1 、职责 2 、职责 3 … 等多个职责的兼容性，稍不注意就会引起工程运行的崩溃。

“单一职责” 的目的就是减少功能维护带来的风险，把代码块的职责单一化，让代码的可维护性更高。

一个完整业务的内部实现，不应该把各种代码都耦合在一起，而应该按照职责去划分好代码块，再进行组合，形成一个 “高内聚，低耦合” 的工程设计。

模块化就是由此而来，在前端工程里，每个单一职责的代码块，就叫做模块（ Module ） ，模块有自己的作用域，功能与业务解耦，非常方便复用和移植。

TIP

模块化还可以解决本章开头所讲述的 [传统开发的弊端](https://vue3.chengpeiquan.com/guide.html#传统开发的弊端) 里提到的大部分问题，随着下面内容一步步深入，将一步步的理解它。

### 如何实现模块化[#](https://vue3.chengpeiquan.com/guide.html#如何实现模块化)

在前端工程的发展过程中，不同时期诞生了很多不同的模块化机制，最为主流的有以下几种：

| 模块化方案 |            全称             |    适用范围     |
| :--------: | :-------------------------: | :-------------: |
|    CJS     |          CommonJS           |     Node 端     |
|    AMD     |   Async Module Definition   |     浏览器      |
|    CMD     |  Common Module Definition   |     浏览器      |
|    UMD     | Universal Module Definition | Node 端和浏览器 |
|    ESM     |          ES Module          | Node 端和浏览器 |

其中 AMD 、CMD 、 UMD 都已经属于偏过去式的模块化方案，在新的业务里，结合各种编译工具，可以直接用最新的 ESM 方案来实现模块化，所以可以在后续有接触的时候再了解。

ESM （ ES Module ） 是 JavaScript 在 ES6（ ECMAScript 2015 ）版本推出的模块化标准，旨在成为浏览器和服务端通用的模块解决方案。

CJS （ CommonJS ） 原本是服务端的模块化标准（设计之初也叫 ServerJS ），是为 JavaScript 设计的用于浏览器之外的一个模块化方案， Node 默认支持了该规范，在 Node 12 之前也只支持 CJS ，但从 Node 12 开始，已经同时支持 ES Module 的使用。

至此，不论是 Node 端还是浏览器端， ES Module 是统一的模块化标准了！

但由于历史原因， CJS 在 Node 端依然是非常主流的模块化写法，所以还是值得进行了解，因此下面的内容将主要介绍 CJS 和 ESM 这两种模块化规范是如何实际运用。

TIP

在开始体验模块化的编写之前，请先在电脑里 [安装好 Node.js](https://vue3.chengpeiquan.com/guide.html#下载和安装-node) ，然后打开 [命令行工具](https://vue3.chengpeiquan.com/guide.html#命令行工具) ，通过 `cd` 命令进入平时管理项目的目录路径， [初始化一个 Node 项目](https://vue3.chengpeiquan.com/guide.html#初始化一个项目) 。

另外，在 CJS 和 ESM ，一个独立的文件就是一个模块，该文件内部的变量必须通过导出才能被外部访问到，而外部文件想访问这些变量，需要导入对应的模块才能生效。

### 用 CommonJS 设计模块[#](https://vue3.chengpeiquan.com/guide.html#用-commonjs-设计模块)

虽然现在推荐使用 ES Module 作为模块化标准，但是日后在实际工作的过程中，还是不免会遇到要维护一些老项目，因此了解 CommonJS 还是非常有必要的。

以下简称 CJS 代指 CommonJS 规范。

#### 准备工作[#](https://vue3.chengpeiquan.com/guide.html#准备工作)

延续在 [Hello Node](https://vue3.chengpeiquan.com/guide.html#hello-node) 部分创建的 Node.js demo 项目，先调整一下目录结构：

1. 删掉 `index.js` 文件
2. 创建一个 `src` 文件夹，在里面再创建一个 `cjs` 文件夹
3. 在 `cjs` 文件夹里面创建两个文件： `index.cjs` 和 `module.cjs`

TIP

请注意这里使用了 `.cjs` 文件扩展名，其实它也是 JS 文件，但这个扩展名是 Node 专门为 CommonJS 规范设计的，可以在 [了解 package.json](https://vue3.chengpeiquan.com/guide.html#了解-package-json) 部分的内容了解更多。

此时目录结构应该如下：

bash

```
hello-node
│ # 源码文件夹
├─src
│ │ # 业务文件夹
│ └─cjs
│   │ # 入口文件
│   ├─index.cjs
│   │ # 模块文件
│   └─module.cjs
│ # 项目清单
└─package.json
```

这是一个常见的 Node 项目目录结构，通常源代码都会放在 `src` 文件夹里面统一管理。

接下来再修改一下 package.json 里面的 scripts 部分，改成如下：

json

```
{
  "scripts": {
    "dev:cjs": "node src/cjs/index.cjs"
  }
}
```

后面在命令行执行 `npm run dev:cjs` 命令，就可以测试刚刚添加的 CJS 模块了。

#### 基本语法[#](https://vue3.chengpeiquan.com/guide.html#基本语法)

CJS 使用 `module.exports` 语法导出模块，可以导出任意合法的 JavaScript 类型，例如：字符串、布尔值、对象、数组、函数等等。

使用 `require` 导入模块，在导入的时候，当文件扩展名是 `.js` 时，可以只写文件名，而此时使用的是 `.cjs` 扩展名，所以需要完整的书写。

#### 默认导出和导入[#](https://vue3.chengpeiquan.com/guide.html#默认导出和导入)

默认导出的意思是，一个模块只包含一个值；而导入默认值则意味着，导入时声明的变量名就是对应模块的值。

在 `src/cjs/module.cjs` 文件里，写入以下代码，导出一句 `Hello World` 信息：

js

```
// src/cjs/module.cjs
module.exports = 'Hello World'
```

TIP

自己在写入代码的时候，不需要包含文件路径那句注释，这句注释只是为了方便阅读时能够区分代码属于哪个文件，以下代码均如此。

在 `src/cjs/index.cjs` 文件里，写入以下代码，导入刚刚编写的模块。

js

```
// src/cjs/index.cjs
const m = require('./module.cjs')
console.log(m)
```

在命令行输入 `npm run dev:cjs` ，可以看到成功输出了 `Hello World` 信息：

bash

```
npm run dev:cjs

> demo@1.0.0 dev:cjs
> node src/cjs/index.cjs

Hello World
```

可以看到，在导入模块时，声明的 `m` 变量拿到的值，就是整个模块的内容，可以直接使用，此例子中它是一个字符串。

再改动一下，把 `src/cjs/module.cjs` 改成如下，这次导出一个函数：

js

```
// src/cjs/module.cjs
module.exports = function foo() {
  console.log('Hello World')
}
```

相应的，这次变成了导入一个函数，所以可以执行它：

js

```
// src/cjs/index.cjs
const m = require('./module.cjs')
m()
```

得到的结果也是打印一句 `Hello World` ，不同的是，这一次的打印行为是在模块里定义的，入口文件只是执行模块里的函数。

bash

```
npm run dev:cjs

> demo@1.0.0 dev:cjs
> node src/cjs/index.cjs

Hello World
```

#### 命名导出和导入[#](https://vue3.chengpeiquan.com/guide.html#命名导出和导入)

默认导出的时候，一个模块只包含一个值，有时候如果想把很多相同分类的函数进行模块化集中管理，例如想做一些 utils 类的工具函数文件、或者是维护项目的配置文件，全部使用默认导出的话，会有非常多的文件要维护。

那么就可以用到命名导出，这样既可以导出多个数据，又可以统一在一个文件里维护管理，命名导出是先声明多个变量，然后通过 `{}` 对象的形式导出。

再来修改一下 `src/cjs/module.cjs` 文件，这次改成如下：

js

```
// src/cjs/module.cjs
function foo() {
  console.log('Hello World from foo.')
}

const bar = 'Hello World from bar.'

module.exports = {
  foo,
  bar,
}
```

这个时候通过原来的方式去拿模块的值，会发现无法直接获取到函数体或者字符串的值，因为打印出来的也是一个对象。

js

```
// src/cjs/index.cjs
const m = require('./module.cjs')
console.log(m)
```

控制台输出：

bash

```
npm run dev:cjs

> demo@1.0.0 dev:cjs
> node src/cjs/index.cjs

{ foo: [Function: foo], bar: 'Hello World from bar.' }
```

需要通过 `m.foo()` 、 `m.bar` 的形式才可以拿到值。

此时可以用一种更方便的方式，利用 ES6 的对象解构来直接拿到变量：

js

```
// src/cjs/index.cjs
const { foo, bar } = require('./module.cjs')
foo()
console.log(bar)
```

这样子才可以直接调用变量拿到对应的值。

#### 导入时重命名[#](https://vue3.chengpeiquan.com/guide.html#导入时重命名)

以上都是基于非常理想的情况下使用模块，有时候不同的模块之间也会存在相同命名导出的情况，来看看模块化是如何解决这个问题的。

的模块文件保持不变，依然导出这两个变量：

js

```
// src/cjs/module.cjs
function foo() {
  console.log('Hello World from foo.')
}

const bar = 'Hello World from bar.'

module.exports = {
  foo,
  bar,
}
```

这次在入口文件里也声明一个 `foo` 变量，在导入的时候对模块里的 `foo` 进行了重命名操作。

js

```
// src/cjs/index.cjs
const {
  foo: foo2,  // 这里进行了重命名
  bar,
} = require('./module.cjs')

// 就不会造成变量冲突
const foo = 1
console.log(foo)

// 用新的命名来调用模块里的方法
foo2()

// 这个不冲突就可以不必处理
console.log(bar)
```

再次运行 `npm run dev:cjs` ，可以看到打印出来的结果完全符合预期：

bash

```
npm run dev:cjs

> demo@1.0.0 dev:cjs
> node src/cjs/index.cjs

1
Hello World from foo.
Hello World from bar.
```

这是利用了 ES6 解构对象的 [给新的变量名赋值](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment#给新的变量名赋值) 技巧。

以上是针对命名导出时的重命名方案，如果是默认导出，那么在导入的时候用一个不冲突的变量名来声明就可以了。

### 用 ES Module 设计模块[#](https://vue3.chengpeiquan.com/guide.html#用-es-module-设计模块)

ES Module 是新一代的模块化标准，它是在 ES6（ ECMAScript 2015 ）版本推出的，是原生 JavaScript 的一部分。

不过因为历史原因，如果要直接在浏览器里使用该方案，在不同的浏览器里会有一定的兼容问题，需要通过 Babel 等方案进行代码的版本转换（可在 [控制编译代码的兼容性](https://vue3.chengpeiquan.com/guide.html#控制编译代码的兼容性) 一节了解如何使用 Babel ）。

因此一般情况下都需要借助构建工具进行开发，工具通常会提供开箱即用的本地服务器用于开发调试，并且最终打包的时候还可以抹平不同浏览器之间的差异。

随着 ESM 的流行，很多新推出的构建工具都默认只支持该方案（ e.g. Vite 、 Rollup ），如果需要兼容 CJS 反而需要另外引入插件单独配置。除了构建工具，很多语言也是默认支持 ESM ，例如 TypeScript ，因此了解 ESM 非常重要。

以下简称 ESM 代指 ES Module 规范。

TIP

在阅读本小节之前，建议先阅读 [用 CommonJS 设计模块](https://vue3.chengpeiquan.com/guide.html#用-commonjs-设计模块) 以了解前置内容，本小节会在适当的内容前后与 CJS 的写法进行对比。

#### 准备工作[#](https://vue3.chengpeiquan.com/guide.html#准备工作-1)

继续使用在 [用 CommonJS 设计模块](https://vue3.chengpeiquan.com/guide.html#用-commonjs-设计模块) 时使用的 hello-node 项目作为 demo ，当然也可以重新创建一个新的。

一样的，先调整一下目录结构：

1. 在 `src` 文件夹里面创建一个 `esm` 文件夹
2. 在 `esm` 文件夹里面创建两个 MJS 文件： `index.mjs` 和 `module.mjs`

TIP

注意这里使用了 `.mjs` 文件扩展名，因为默认情况下， Node 需要使用该扩展名才会支持 ES Module 规范。

也可以在 package.json 里增加一个 `"type": "module"` 的字段来使 `.js` 文件支持 ESM ，但对应的，原来使用 CommonJS 规范的文件需要从 `.js` 扩展名改为 `.cjs` 才可以继续使用 CJS 。

为了减少理解上的门槛，这里选择了使用 `.mjs` 新扩展名便于入门，可以在 [了解 package.json](https://vue3.chengpeiquan.com/guide.html#了解-package-json) 部分的内容了解更多。

此时目录结构应该如下：

bash

```
hello-node
│ # 源码文件夹
├─src
│ │ # 上次用来测试 CommonJS 的相关文件
│ ├─cjs
│ │ ├─index.cjs
│ │ └─module.cjs
│ │
│ │ # 这次要用的 ES Module 测试文件
│ └─esm
│   │ # 入口文件
│   ├─index.mjs
│   │ # 模块文件
│   └─module.mjs
│
│ # 项目清单
└─package.json
```

同样的，源代码放在 `src` 文件夹里面管理。

然后再修改一下 package.json 里面的 scripts 部分，参照上次配置 CJS 的格式，增加一个 ESM 版本的 script ，改成如下：

json

```
{
  "scripts": {
    "dev:cjs": "node src/cjs/index.cjs",
    "dev:esm": "node src/esm/index.mjs"
  }
}
```

后面在命令行执行 `npm run dev:esm` 就可以测试的 ESM 模块了。

TIP

注意， script 里的 `.mjs` 扩展名不能省略。

另外，在实际项目中，可能不需要做这些处理，因为很多工作脚手架已经帮处理过了，比如的 Vue3 项目。

#### 基本语法[#](https://vue3.chengpeiquan.com/guide.html#基本语法-1)

ESM 使用 `export default` （默认导出）和 `export` （命名导出）这两个语法导出模块，和 CJS 一样， ESM 也可以导出任意合法的 JavaScript 类型，例如：字符串、布尔值、对象、数组、函数等等。

使用 `import ... from ...` 导入模块，在导入的时候，如果文件扩展名是 `.js` 则可以省略文件名后缀，否则需要把扩展名也完整写出来。

#### 默认导出和导入[#](https://vue3.chengpeiquan.com/guide.html#默认导出和导入-1)

ESM 的默认导出也是一个模块只包含一个值，导入时声明的变量名，它对应的数据就是对应模块的值。

在 `src/esm/module.mjs` 文件里，写入以下代码，导出一句 `Hello World` 信息：

js

```
// src/esm/module.mjs
export default 'Hello World'
```

在 `src/esm/index.mjs` 文件里，写入以下代码，导入刚刚编写的模块。

js

```
// src/esm/index.mjs
import m from './module.mjs'
console.log(m)
```

在命令行输入 `npm run dev:esm` ，可以看到成功输出了 `Hello World` 信息：

bash

```
npm run dev:esm

> demo@1.0.0 dev:esm
> node src/esm/index.mjs

Hello World
```

可以看到，在导入模块时，声明的 `m` 变量拿到的值，就是整个模块的内容，可以直接使用，此例子中它是一个字符串。

像在 CJS 的例子里一样，也来再改动一下，把 `src/esm/module.mjs` 改成导出一个函数：

js

```
// src/esm/module.mjs
export default function foo() {
  console.log('Hello World')
}
```

同样的，这次也是变成了导入一个函数，可以执行它：

js

```
// src/esm/index.mjs
import m from './module.mjs'
m()
```

一样可以从模块里的函数得到一句 `Hello World` 的打印信息。

bash

```
npm run dev:esm

> demo@1.0.0 dev:esm
> node src/esm/index.mjs

Hello World
```

TIP

可以看到， CJS 和 ESM 的默认导出是非常相似的，在未来如果有老项目需要从 CJS 往 ESM 迁移，大部分情况下只需要把 `module.exports` 改成 `export default` 即可。

#### 命名导出和导入[#](https://vue3.chengpeiquan.com/guide.html#命名导出和导入-1)

虽然默认导出的时候， CJS 和 ESM 的写法非常相似，但命名导出却完全不同！

在 CJS 里，使用命名导出后的模块数据默认是一个对象，可以导入模块后通过 `m.foo` 这样的方式去调用对象的属性，或者在导入的时候直接解构拿到对象上的某个属性：

js

```
// CJS 支持导入的时候直接解构
const { foo } = require('./module.cjs')
```

但 ES Module 的默认导出不能这样做，例如下面这个例子，虽然默认导出了一个对象：

js

```
// 在 ESM ，通过这样导出的数据也是属于默认导出
export default {
  foo: 1,
}
```

但是无法和 CJS 一样通过大括号的方式导入其中的某个属性：

js

```
// ESM 无法通过这种方式对默认导出的数据进行 “解构”
import { foo } from './module.mjs'
```

这样操作在运行过程中，控制台会抛出错误信息：

bash

```
import { foo } from './module.mjs'
         ^^^
SyntaxError:
The requested module './module.mjs' does not provide an export named 'foo'
```

正确的方式应该是通过 `export` 对数据进行命名导出，先将 `src/esm/module.mjs` 文件修改成如下代码，请留意 `export` 关键字的使用：

js

```
// src/esm/module.mjs
export function foo() {
  console.log('Hello World from foo.')
}

export const bar = 'Hello World from bar.'
```

通过 `export` 命名导出的方式，现在才可以使用大括号将它们进行命名导入：

js

```
// src/esm/index.mjs
import { foo, bar } from './module.mjs'

foo()
console.log(bar)
```

这一次程序可以顺利运行了：

bash

```
npm run dev:esm

> demo@1.0.0 dev:esm
> node src/esm/index.mjs

Hello World from foo.
Hello World from bar.
```

那么有没有办法像 CJS 一样使用 `m.foo` 调用对象属性的方式一样，去使用这些命名导出的模块呢？

答案是肯定的！命名导出支持使用 `* as 变量名称` 的方式将其所有命名挂在某个变量上，该变量是一个对象，每一个导出的命名都是其属性：

ts

```
// src/esm/index.mjs
// 注意这里使用了另外一种方式，将所有的命名导出都挂在了 `m` 变量上
import * as m from './module.mjs'

console.log(typeof m)
console.log(Object.keys(m))

m.foo()
console.log(m.bar)
```

运行 `npm run dev:esm` ，将输出：

bash

```
npm run dev:esm

> demo@1.0.0 dev:esm
> node src/esm/index.mjs

object
[ 'bar', 'foo' ]
Hello World from foo.
Hello World from bar.
```

#### 导入时重命名[#](https://vue3.chengpeiquan.com/guide.html#导入时重命名-1)

接下来看看 ESM 是如何处理相同命名导出的问题，项目下的模块文件依然保持不变，还是导出两个变量：

js

```
// src/esm/module.mjs
export function foo() {
  console.log('Hello World from foo.')
}

export const bar = 'Hello World from bar.'
```

入口文件里面，也声明一个 `foo` 变量，然后导入的时候对模块里的 `foo` 进行重命名操作：

js

```
// src/esm/index.mjs
import {
  foo as foo2,  // 这里进行了重命名
  bar
} from './module.mjs'

// 就不会造成变量冲突
const foo = 1
console.log(foo)

// 用新的命名来调用模块里的方法
foo2()

// 这个不冲突就可以不必处理
console.log(bar)
```

可以看到，在 ESM 的重命名方式和 CJS 是完全不同的，它是使用 `as` 关键字来操作，语法为 `<old-name> as <new-name>` 。

现在再次运行 `npm run dev:esm` ，可以看到打印出来的结果也是完全符合预期了：

bash

```
npm run dev:esm

> demo@1.0.0 dev:esm
> node src/esm/index.mjs

1
Hello World from foo.
Hello World from bar.
```

以上是针对命名导出时的重命名方案，如果是默认导出，和 CJS 一样，在导入的时候用一个不冲突的变量名来声明就可以了。

#### 在浏览器里访问 ESM[#](https://vue3.chengpeiquan.com/guide.html#在浏览器里访问-esm)

ES Module 除了支持在 Node 环境使用，还可以和普通的 JavaScript 代码一样在浏览器里运行。

要在浏览器里体验 ESM ，需要使用现代的主流浏览器（如 Chrome ），并注意其访问限制，例如本地开发不能直接通过 `file://` 协议在浏览器里访问本地 HTML 文件，这是因为浏览器对 JavaScript 的安全性要求，会触发 [CORS](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/CORS) 错误，因此需要启动本地服务并通过 `http://` 协议访问。

TIP

CORS （全称 Cross-Origin Resource Sharing ）是指跨源资源共享，可以决定浏览器是否需要阻止 JavaScript 获取跨域请求的响应。

现代浏览器默认使用 “同源安全策略” ，这里的 “源” 指 URL 的 `origin` 部分，例如网页可以通过 `window.location.origin` 获取到如 `https://example.com` 这样格式的数据，就是网页的 `origin` 。

默认情况下，非同源的请求会被浏览器拦截，最常见的场景是通过 XHR 或者 Fetch 请求 API 接口，需要网页和接口都部署在同一个域名才可以请求成功，否则就会触发跨域限制。

如果网页和接口不在同一个域名，例如网页部署在 `https://web.example.com` ，接口部署在 `https://api.example.com` ，此时需要在 `https://api.example.com` 的 API 服务端程序里，配置 `Access-Control-Allow-Origin: *` 允许跨域请求（ `*` 代表允许任意外域访问，也可以指定具体的域名作为白名单列表）。

##### 添加服务端程序[#](https://vue3.chengpeiquan.com/guide.html#添加服务端程序)

接下来搭建一个简单的本地服务，并通过 HTML 文件来引入 ESM 模块文件，体验浏览器端如何使用 ESM 模块。

在 hello-node 项目的根目录下创建名为 server 的文件夹（与 src 目录同级），并添加 index.js 文件，敲入以下代码：

js

```
// server/index.js
const { readFileSync } = require('fs')
const { resolve } = require('path')
const { createServer } = require('http')

/**
 * 判断是否 ESM 文件
 */
function isESM(url) {
  return String(url).endsWith('mjs')
}

/**
 * 获取 MIME Type 信息
 * @tips `.mjs` 和 `.js` 一样，都使用 JavaScript 的 MIME Type
 */
function mimeType(url) {
  return isESM(url) ? 'application/javascript' : 'text/html'
}

/**
 * 获取入口文件
 * @returns 存放在本地的文件路径
 */
function entryFile(url) {
  const file = isESM(url) ? `../src/esm${url}` : './index.html'
  return resolve(__dirname, file)
}

/**
 * 创建 HTTP 服务
 */
const app = createServer((request, response) => {
  // 获取请求时的相对路径，如网页路径、网页里的 JS 文件路径等
  const { url } = request

  // 转换成对应的本地文件路径并读取其内容
  const entry = entryFile(url)
  const data = readFileSync(entry, 'utf-8')

  // 需要设置正确的响应头信息，浏览器才可以正确响应
  response.writeHead(200, { 'Content-Type': mimeType(url) })
  response.end(data)
})

/**
 * 在指定的端口号启动本地服务
 */
const port = 8080
app.listen(port, '0.0.0.0', () => {
  console.log(`Server running at:`)
  console.log()
  console.log(`  ➜  Local:  http://localhost:${port}/`)
  console.log()
})
```

这是一个基础的 Node.js 服务端程序，利用了 HTTP 模块启动本地服务，期间利用 FS 模块的 I/O 能力对本地文件进行读取，而 PATH 模块则简化了文件操作过程中的路径处理和兼容问题（例如众所周知的 Windows 与 macOS 的路径斜杆问题）。

TIP

在这段服务端程序代码里，请留意 `mimeType` 方法，要让浏览器能够正确解析 `.mjs` 文件，需要在服务端响应文件内容时，将其 MIME Type 设置为 和 JavaScript 文件一样，这一点非常重要。

##### 添加入口页面[#](https://vue3.chengpeiquan.com/guide.html#添加入口页面)

继续在 server 目录下添加一个 index.html 并写入以下 HTML 代码，它将作为网站的首页文件：

TIP

可以在 VSCode 先新建一个空文件，文件语言设置为 HTML ，并写入英文感叹号 `!` ，再按 Tab 键（或者鼠标选择第一个代码片段提示），可快速生成基础的 HTML 结构。

html

```
<!-- server/index.html -->
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta http-equiv="X-UA-Compatible" content="IE=edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>ESM run in browser</title>
  </head>
  <body>
    <script type="module" src="./index.mjs"></script>
  </body>
</html>
```

请注意在 `<script />` 标签这一句代码上，比平时多了一个 `type="module"` 属性，这代表这个 script 是使用了 ESM 模块，而 `src` 属性则对应指向了上文在 src/esm 目录下的入口文件名。

之所以无需使用 `../src/esm/index.mjs` 显式的指向真实目录，是因为在 [添加服务端程序](https://vue3.chengpeiquan.com/guide.html#添加服务端程序) 时，已通过服务端代码里的 `entryFile` 方法重新指向了文件所在的真实路径，所以在 HTML 文件里可以使用 `./` 简化文件路径。

##### 启动服务并访问[#](https://vue3.chengpeiquan.com/guide.html#启动服务并访问)

打开 package.json 文件，在 `scripts` 字段追加一个 `serve` 命令如下：

json

```
{
  "scripts": {
    "dev:cjs": "node src/cjs/index.cjs",
    "dev:esm": "node src/esm/index.mjs",
    "serve": "node server/index.js"
  }
}
```

在命令行运行 `npm run serve` 即可启动本地服务：

bash

```
❯ npm run serve

> demo@1.0.0 serve
> node server/index.js

Server running at:

  ➜  Local:  http://localhost:8080/
```

根据命令行提示，在浏览器访问 `http://localhost:8080/` 地址，即可访问本地服务。

TIP

如遭遇端口号冲突，可在 server/index.js 的 `const port = 8080` 代码处修改为其他端口号。

因为在编写 HTML 文件时没有写入内容，只引入了 ESM 模块文件，因此需要按 F12 唤起浏览器的控制台查看 Log ，可以看到控制台根据模块的文件内容，输出了这三句 Log （如果没有 Log ，可在控制台唤起的情况下按 F5 重新载入页面）：

bash

```
1                                                   index.mjs:8
Hello World from foo.                               module.mjs:2
Hello World from bar.                               index.mjs:14
```

分别来自 src/esm/index.mjs 本身的 `console.log` 语句，以及 `import` 进来的 module.mjs 里的 `console.log` 语句。

如果未能出现这三句 Log ，请留意 `.mjs` 文件内容是否为上一小节最后的内容：

src/esm/index.mjs 文件内容为：

js

```
// src/esm/index.mjs
import {
  foo as foo2, // 这里进行了重命名
  bar,
} from './module.mjs'

// 就不会造成变量冲突
const foo = 1
console.log(foo)

// 用新的命名来调用模块里的方法
foo2()

// 这个不冲突就可以不必处理
console.log(bar)
```

src/esm/module.mjs 文件内容为：

js

```
// src/esm/module.mjs
export function foo() {
  console.log('Hello World from foo.')
}

export const bar = 'Hello World from bar.'
```

##### 内联的 ESM 代码[#](https://vue3.chengpeiquan.com/guide.html#内联的-esm-代码)

到目前为止， server/index.html 文件里始终是通过文件的形式引入 ESM 模块，其实 `<script type="module" />` 也支持编写内联代码，和普通的 `<script />` 标签用法相同：

html

```
<script type="module">
  // ESM 模块的 JavaScript 代码
</script>
```

请移除 `<script />` 标签的 `src` 属性，并在标签内写入 src/esm/index.mjs 文件里的代码，现在该 HTML 文件的完整代码如下：

html

```
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta http-equiv="X-UA-Compatible" content="IE=edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>ESM run in browser</title>
  </head>
  <body>
    <!-- 标签内的代码就是 src/esm/index.mjs 的代码 -->
    <script type="module">
      import {
        foo as foo2, // 这里进行了重命名
        bar,
      } from './module.mjs'

      // 就不会造成变量冲突
      const foo = 1
      console.log(foo)

      // 用新的命名来调用模块里的方法
      foo2()

      // 这个不冲突就可以不必处理
      console.log(bar)
    </script>
  </body>
</html>
```

回到浏览器刷新 `http://localhost:8080/` ，可以看到浏览器控制台依然输出了和引入 `src="./index.mjs"` 时一样的 Log 信息：

bash

```
1                                                   (index):21
Hello World from foo.                               module.mjs:2
Hello World from bar.                               (index):27
```

##### 了解模块导入限制[#](https://vue3.chengpeiquan.com/guide.html#了解模块导入限制)

虽然以上例子可以完美地在浏览器里引用现成的 ESM 模块代码并运行，但不代表工程化项目下所有的 ES Module 模块化方式都适合浏览器。

先做一个小尝试，将 src/esm/index.mjs 文件内容修改如下，导入项目已安装的 md5 工具包：

js

```
// src/esm/index.mjs
import md5 from 'md5'
console.log(md5('Hello World'))
```

回到浏览器刷新 `http://localhost:8080/` ，观察控制台，可以发现出现了一个红色的错误信息：

bash

```
Uncaught TypeError: Failed to resolve module specifier "md5".
Relative references must start with either "/", "./", or "../".
```

这是因为不论是通过 `<script type="module" />` 标签还是通过 `import` 语句导入，模块的路径都必须是以 `/` 、 `./` 或者是 `../` 开头，因此无法直接通过 npm 包名进行导入。

这种情况下需要借助另外一个 script 类型： `importmap` ，在 server/index.html 里追加 `<script type="importmap" />` 这一段代码：

html

```
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta http-equiv="X-UA-Compatible" content="IE=edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>ESM run in browser</title>
  </head>
  <body>
    <!-- 注意需要先通过 `importmap` 引入 npm 包的 CDN -->
    <script type="importmap">
      {
        "imports": {
          "md5": "https://esm.run/md5"
        }
      }
    </script>

    <!-- 然后才能在 `module` 里 `import xx from 'xx'` -->
    <script type="module" src="./index.mjs"></script>
  </body>
</html>
```

再次刷新页面，可以看到控制台成功输出了 `b10a8db164e0754105b7a99be72e3fe5` 这个字符串，也就是 `Hello World` 被 MD5 处理后的结果。

可以看到 `importmap` 的声明方式和 package.json 的 dependencies 字段非常相似， JSON 的 key 是包名称， value 则是支持 ESM 的远程地址。

TIP

Import Maps 的运行机制是通过 import 映射来控制模块说明符的解析，类似于构建工具常用的 `alias` 别名机制。

这是一个现代浏览器才能支持的新特性，建议使用 Chrome 最新版本体验完整功能，可以在其 [GitHub 仓库](https://github.com/WICG/import-maps) 查看更多用法。

上方例子里， md5 对应的远程地址是使用了来自 `esm.run` 网站的 URL ，而不是 npm 包同步到 jsDelivr CDN 或者 UNPKG CDN 的地址，这是因为 md5 这个包本身不支持 ES Module ，需要通过 `esm.run` 这个网站进行在线转换才可以在 `<script type="module" />` 上使用。

![esm.run 网站上的包转换操作界面](https://vue3.chengpeiquan.com/assets/img/esm-run.jpg)

esm.run 网站上的包转换操作界面

该网站的服务是 jsDelivr CDN 所属的服务商提供，因此也可以通过 jsDelivr CDN 的 URL 添加 `/+esm` 参数来达到转换效果，以 md5 包为例：

bash

```
# 默认是一个 CJS 包
https://cdn.jsdelivr.net/npm/md5

# 可添加 `/+esm` 参数变成 ESM 包
https://cdn.jsdelivr.net/npm/md5/+esm
```

总的来说，现阶段在浏览器使用 ES Module 并不是一个很好的选择，建议开发者还是使用构建工具来开发，工具可以抹平这些浏览器差异化问题，降低开发成本。