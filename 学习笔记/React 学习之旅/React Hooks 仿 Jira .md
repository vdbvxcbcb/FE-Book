# React Hooks + TS 仿 Jira 

[课件地址](https://www.notion.so/React-491ad0643476437cafde50bee4dde6ed)

[项目地址](https://github.com/sindu12jun/imooc-jira)

**小技巧**

rafc/rafce 通常用 rafc ，代码片段插件，自动创建函数组件

alt 键跳转组件

**工程用什么把 TS 编译成 JS 文件？**

很多同学可能会觉得既然我们用 ts 那肯定是 tsc 编译的，其实不是，

目前大多数的 ts 工程都是 ts 类型检查 + babel 编译 这样的组合，我们的工程也不例外

(可以去项目 node_modules 下面看一下，会发现有个 @babel 文件夹)

用 babel 编译 ts，就可以实现这样一种效果：babel 编译一切，降低开发/配置成本

我们代码中的 jsx/tsx 文件，

就是用 @babel/plugin-transform-react-jsx 这个 babel 插件转换的：

[插件地址](https://babeljs.io/docs/en/babel-plugin-transform-react-jsx)

**为什么不需要再引入 React 了？**

因为上面说的那种转换方式是上一个版本的转换方式了。

去年年底 plugin-transform-react-jsx 发布了新版本，从 v7.9.0开始，就不用手动引入`React`了，

因为转换变成这样了：

```jsx
const profile = (
  <div>
    <img src="avatar.png" className="profile" />
    <h3>{[user.firstName, user.lastName].join(" ")}</h3>
  </div>
);
```

编译成：

```jsx
import { jsx as _jsx } from "react/jsx-runtime";
import { jsxs as _jsxs } from "react/jsx-runtime";

const profile = _jsxs("div", {
  children: [
    _jsx("img", {
      src: "avatar.png",
      className: "profile",
    }),
    _jsx("h3", {
      children: [user.firstName, user.lastName].join(" "),
    }),
  ],
});
```

这个功能默认是关闭的，但是从 create-react-app 4.0 开始，默认就是打开的了。

而 4.0 版本是 2020年10月24号(https://github.com/facebook/create-react-app/releases/tag/v4.0.0)发布的。

## 初始化项目

```shell
npx create-react-app jira --template typescript
```

npx 可以不用全局安装就直接使用 npm 的包

public 下的文件：

manifest.json PWA 设置

robots.txt 设置爬虫规则，比如不允许爬虫爬的文件

src 下的文件：

react-app-env.d.ts 引入预先定义的 ts 类型声明

reportWebVitals.ts 埋点上报

setupTests.ts 配置单元测试

字体、图片等代码用到的可以放到 src 目录下

## 配置规范工程 

### 绝对路径配置

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1636295510240-d325338e-4edb-40db-8f67-77e13418b582.jpeg)

### 代码格式化配置

https://prettier.io/

安装 prettier

```sh
yarn add --dev --exact prettier
```

创建空的配置文件

```sh
echo {}> .prettierrc.json
```

.prettierignore 文件

```sh
build
coverage
```

Pre-commit Hook 代码提交之前自动格式化

```sh
npx mrm@2 lint-staged
```

添加其它要格式化的文件后缀

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1636295510293-ca147cf3-ad84-4620-bbb0-f714e3741369.jpeg)

cra 自带 ESLint，prettier 与 ESLint 会冲突，需要

```sh
yarn add eslint-config-prettier -D
```

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1636295510334-1bfea153-d932-4478-b080-bdb387bf6c68.jpeg)

### git 提交规范配置

使用 commitLint

https://github.com/conventional-changelog/commitlint

```sh
yarn add @commitlint/config-conventional @commitlint/cli -D
```

```sh
echo "module.exports = {extends: ['@commitlint/config-conventional']}" > commitlint.config.js
```

```sh
npx husky add .husky/commit-msg 'npx --no-install commitlint --edit "$1"' 
```

## Mock

### 简要说明

在接下来章节的实战代码中大家将会使用`window.fetch`请求后端接口

但是在真实工作环境中，由于后端与前端并行开发，所以在前期是没有后端接口可以使用的

所以学会最适合自己的 Mock 数据的方法就非常重要

这一节会给大家对比业界常见的 Mock 方案，选择并配置其中最合适的方案

### 常见 MOCK 方案

#### 1. 代码侵入 (直接在代码中写死 Mock 数据，或者请求本地的 JSON 文件)

优点：无

缺点：

1. 和其他方案比 Mock 效果不好
2. 与真实 Server 环境的切换非常麻烦，一切需要侵入代码切换环境的行为都是不好的

#### 2. 请求拦截

代表：[Mock.js](http://mockjs.com/)

示例：

```js
Mock.mock(/\\\\/api\\\\/visitor\\\\/list/, 'get', {
  code: 2000,
  msg: 'ok',
  'data|10': [
    {
      'id|+1': 6,
      'name': '@csentence(5)',
      'tag': '@integer(6, 9)-@integer(10, 14)岁 @cword("零有", 1)基础',
      'lesson_image': "<https://images.pexels.com/3737094/pexels-photo-3737094.jpeg>",
      'lesson_package': 'L1基础指令课',
      'done': '@integer(10000, 99999)',
    }
  ]
})
```

优点：

1. 与前端代码分离
2. 可生成随机数据

缺点：

1. 数据都是动态生成的假数据，无法真实模拟增删改查的情况
2. 只支持 ajax，不支持 fetch

(想要了解 ajax 和 fetch 区别的同学来点[我](https://zhuanlan.zhihu.com/p/24594294))

#### 3. 接口管理工具

代表：[rap](https://github.com/thx/RAP), [swagger](https://swagger.io/), [moco](https://github.com/dreamhead/moco), [yapi](https://github.com/YMFE/yapi)

优点：

1. 配置功能强大，接口管理与 Mock 一体，后端修改接口 Mock 也跟着更改，可靠

缺点：

1. 配置复杂，依赖后端，可能会出现后端不愿意出手，或者等配置完了，接口也开发出来了的情况。
2. 一般会作为大团队的基础建设而存在， 没有这个条件的话慎重考虑

#### 4. 本地 node 服务器

代表：[json-server](https://github.com/typicode/json-server)

优点：

1. 配置简单，json-server 甚至可以 0 代码 30 秒启动一个 REST API Server
2. 自定义程度高，一切尽在掌控中
3. 增删改查真实模拟

缺点：

1. 与接口管理工具相比，无法随着后端 API 的修改而自动修改

#### 本课程 Mock 计划

从本章开始，使用 json-server Mock 2 章，

在这 2 章里让大家尽可能多的接触到不同的(GET, POST, DELETE, PATCH)Mock 场景，

剩下的章节里使用真实的接口

#### REST API

一句话总结：URI 代表 资源/对象，METHOD 代表行为

```txt
GET /tickets // 列表
GET /tickets/12 // 详情
POST /tickets  // 增加
PUT /tickets/12 // 替换
PATCH /tickets/12 // 修改
DELETE /tickets/12 // 删除
```

点 [我](https://segmentfault.com/q/1010000005685904) 了解 `patch vs put`

全局安装

```sh
npm i json-server -g
或
yarn global add json-server
 根目录下创建一个 db.json，即可启动 Mock 服务
json-server --watch db.json 
```

项目安装

```sh
yarn add json-server -D
```

<img src="https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1637079928403-8f64f2e5-40b1-4c48-bfae-61ba4480b71a.jpeg" alt="img" style="zoom:67%;" />

<img src="https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1637079928325-1e26d039-368a-4c36-a115-158856b8362b.jpeg" alt="img" style="zoom: 67%;" />

启动 Mock 服务

```sh
yarn req-json
```

其实改为 mock 直接 yarn mock 好了

## 工程列表 demo

<img src="https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1637173860275-60f6d46d-9e9a-4cf4-a104-5622b3eb5746.jpeg" alt="img" style="zoom: 80%;" />

src/screens/project-list/index.jsx

```jsx
import { List } from "./list"
import { SearchPanel } from "./search-panel"

export const ProjectList = () => {
  return (
    <div>
      <List/>
      <SearchPanel/>
    </div>
  )
} 
```

src/screens/project-list/search-panel.jsx

```jsx
import { useState, useEffect } from "react";

export const SearchPanel = () => {
  const [params, setParams] = useState({
    name: '',
    id: ''
  });

  const [users, setUsers] = useState([]);
  const [list, setList] = useState([]);

  useEffect(() => {
    fetch().then(async res => {
      if (res.ok) {
        setList(await res.json())
      }
    })
  }, [params]);

  return (
    <form>
      {/*相当于 setParam(Object.assign({}, params, {name:e.target.value}))*/}    
      <input type="text" value={params.name} onChange={e => setParams({
        ...params,
        name: e.target.value
      })} />
      <select value={params.id} onChange={e => setParams({
        ...params,
        id: e.target.value
      })}>
        <options value="">负责人</options>
        {
          users.map(item => {
            <options value={user.id}>{user.name}</options>
          })
        }
      </select>
    </form>
  )
}
```

src/screens/project-list/list.jsx

```jsx
export const List = () => {
  return (
    <table></table>
  )
}
```

## 状态提升

<img src="https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1637173860354-904c4f10-23e6-4e7b-9323-c5ca4e08421a.jpeg" alt="img" style="zoom:80%;" />

将 SearchPanel 组件的 list 数据分享给 List 组件

注意：开启 ESLint，养成好习惯

`__json-server-mock__`/db.json

```json
{
  "users": [
    {
      "id": 1,
      "name": "高修文"
    },
    {
      "id": 2,
      "name": "熊天成"
    },
    {
      "id": 3,
      "name": "郑华"
    },
    {
      "id": 4,
      "name": "王文静"
    }
  ],
  "projects": [
    {
      "id": 1,
      "name": "骑手管理",
      "personId": 1,
      "organization": "外卖组",
      "created": 1604989757139
    },
    {
      "id": 2,
      "name": "团购 APP",
      "personId": 2, // 关联的是熊天成
      "organization": "团购组",
      "created": 1604989757139
    },
    {
      "id": 3,
      "name": "物料管理系统",
      "personId": 2, // 关联的是熊天成
      "organization": "物料组",
      "created": 1546300800000
    },
    {
      "id": 4,
      "name": "总部管理系统",
      "personId": 3,
      "organization": "总部",
      "created": 1604980000011
    },
    {
      "id": 5,
      "name": "送餐路线规划系统",
      "personId": 4,
      "organization": "外卖组",
      "created": 1546900800000
    }
  ]
}

```

src/App.tsx

```tsx
import './App.css';
import { ProjectList } from "./screens/project-list"; 

function App() {
  return (
    <div className="App">
      <ProjectList />
    </div>
  );
}

export default App;
```

src/utils/index.js

```js
const isFalsy = (val) => val === 0 ? false : !val ;

// 将对象值为空的键清理掉
// 在一个函数里，改变传入的对象本身是不好的
export const cleanObject = (object) => {
  const result = {...object};
  Object.keys(result).forEach(key => {
    const value = result[key];  
    if (isFalsy(value)) {
      delete(result[key]) 
    }
  }) 
  // 处理后记得返回
  return result
}
```

```sh
yarn add qs
```

.env.development.local（yarn start 时自动设置环境变量）

```
REACT_APP_API_URL=http://localhost:3001
```

src/screens/project-list/index.jsx

```jsx
import { useState, useEffect } from "react";

import { SearchPanel } from "./search-panel";
import { List } from "./list"; 
import * as qs from "qs";
import { cleanObject } from "../../utils";

const apiUrl = process.env.REACT_APP_API_URL;

export const ProjectList = () => {
  const [params, setParams] = useState({
    name: "",
    personId: ""
  });
  const [users, setUsers] = useState([]); 
  const [list, setList] = useState([]);

  useEffect(() => {
    // apiUrl无效，需要添加的是 env.development.local，并且变量要带有 REACT_APP
    // 这里出错会报 Uncaught (in promise) SyntaxError: Unexpected token < in JSON at position 0
    // qs.stringify() 将对象转为 name=value&name=value 形式
    fetch(`${apiUrl}/projects?${qs.stringify(cleanObject(params))}`)
      .then(async response => {
        if (response.ok) {
          setList(await response.json())
        }
      })
  }, [params]);
  
  // 只执行一次
  useEffect(() => {
    fetch(`${apiUrl}/users`).then(async response => {
      if (response.ok) {
        setUsers(await response.json())
      }
    })
  }, []);

  return (
    <div>
      <SearchPanel params={params} setParams={setParams} users={users}/> 
      <List list={list} users={users}/>      
    </div>
  )
} 
```

src/screens/project-list/search-panel.jsx

```jsx
export const SearchPanel = ({params, setParams, users}) => {
  return (
    <form>
      <input type="text" value={params.name} onChange={e => setParams({
        ...params,
        name: e.target.value
      })} />
      <select value={params.personId} onChange={e => setParams({
        ...params,
        personId: e.target.value
      })}>
        <option value="">负责人</option>
        {
          users.map(user => {
            return (<option key={user.id} value={user.id}>{user.name}</option>)
          })
        }
      </select>
    </form>
  )
}
```

src/screens/project-list/list.jsx

```jsx
export const List = ({list, users}) => {
  return (
    <table>
      <thead>
        <tr>
          <th>项目名称</th>
          <th>负责人</th>
        </tr>
      </thead>  
      <tbody>
        {
          list.map(project => 
            <tr key={project.id}>
              <td>{project.name}</td>
              {/* 注意这里是 id 与 personId 匹配 */}
              <td>{users.find(user => user.id === project.personId)?.name || '未知'}</td>
            </tr>
          )
        } 
      </tbody>
    </table>
  )
}
```

效果图

![img](https://cdn.nlark.com/yuque/0/2021/gif/1614731/1637219300751-93b14d9b-fba7-49d2-8cc0-c8ebf010750f.gif)

## 添加自定义 Hook

<img src="https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1637245866512-739477ec-b00e-4f72-aeb0-f08e03dc024b.jpeg" alt="img" style="zoom: 50%;" />

添加生命周期与防抖的自定义 Hook 

src/utils/index.js

```js
import { useState, useEffect } from "react";

const isFalsy = (val) => val === 0 ? false : !val ;

// 在一个函数里，改变传入的对象本身是不好的
// 不需要其它 hook，所以不需要改造为自定义 hook
export const cleanObject = (object) => {
  const result = {...object};
  Object.keys(result).forEach(key => {
    const value = result[key];  
    if (isFalsy(value)) {
      delete(result[key]) 
    }
  }) 
  // 处理后记得返回
  return result
}

export const useMount = (callback) => {
  useEffect(() => {
    callback()
  }, [])
}


export const useDebounce = (value, delay) => {
  // 为了不影响入参，另外设置一个值为 debounceValue
  // 将 value 转换为处理后的 debounceValue
  const [debounceValue, setDebounceValue] = useState(value);
  useEffect(() => {
    // 每次在 value 变化时设置一个定时器
    const timeout = setTimeout(() => setDebounceValue(value) , delay);
    // 每次在上一个 useEffect 处理完后才执行，清除定时器
    return () => clearTimeout(timeout);
  }, [value, delay])
  // 返回一个新的值
  return debounceValue;
}
```

src/screens/project-list/index.jsx

```jsx
import { useState, useEffect } from "react";

import { SearchPanel } from "./search-panel";
import { List } from "./list"; 
import * as qs from "qs";
import { cleanObject, useMount, useDebounce } from "../../utils";

const apiUrl = process.env.REACT_APP_API_URL;

export const ProjectList = () => {
  const [users, setUsers] = useState([]);

  const [params, setParams] = useState({
    name: "",
    personId: "",
    // 如果需要模糊搜索需要使用 ?q=，SearchPanel 组件的 input 中添加 q ，设置 q 的值即可
    q: ""
  });
  
  const [list, setList] = useState([]);
  
  // SearchPanel 子组件每次触发 params 变化，都使 ProjectList 重新渲染，触发一次 useDebounce
  const debounceParams = useDebounce(params, 1000);

  useEffect(() => {
    // apiUrl无效，需要添加的是 env.development.local，并且变量要带有 REACT_APP
    // 这里出错会报 Uncaught (in promise) SyntaxError: Unexpected token < in JSON at position 0
    fetch(`${apiUrl}/projects?${qs.stringify(cleanObject(debounceParams))}`)
      .then(async response => {
        if (response.ok) {
          setList(await response.json())
        }
      })
  }, [debounceParams]);
  
  useMount(() => {
    fetch(`${apiUrl}/users`).then(async response => {
      if (response.ok) {
        setUsers(await response.json())
      }
    })
  });

  return (
    <div>
      <SearchPanel params={params} setParams={setParams} users={users}/> 
      <List list={list} users={users}/>      
    </div>
  )
} 
```

## jsx 改造为 tsx

cra 项目中 jsx 改为 tsx，可以提供更好的、静态的代码检查功能（好习惯）

某些包是 js 写的，没有类型声明文件，需要自己安装：

```sh
yarn add @types/qs -D
```

.d.ts 作用：说明书，给 js 打一个 ts 的补丁，一般用于开源库

js + d.ts = ts

jsx 改为 tsx ：一般在变量、入参、返回值时定义类型

传入的参数都需要类型定义（定义接口说明书），否则隐式是 any

unkown 类型不能随便赋值、调用任何方法

ts 的泛型可以静态分析类型， js 的 typeof 等方法只能运行时判断类型

<img src="https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1637326325426-279bfacd-be95-4e2b-97bd-8368bd730fc3.jpeg" alt="img" style="zoom: 80%;" />

src/utils/index.ts

```ts
import { useState, useEffect } from "react";

const isFalsy = (val: unknown) => (val === 0 ? false : !val);

// 在一个函数里，改变传入的对象本身是不好的
// 不需要其它 hook，所以不需要改造为自定义 hook
export const cleanObject = <T,>(object: T): T => {
  const result = { ...object };
  Object.keys(result).forEach((key) => {
    const value = result[key as keyof typeof result];
    if (isFalsy(value)) {
      delete result[key as keyof typeof result];
    }
  });
  // 处理后记得返回
  return result;
};

// 如果这里添加 callback 作为依赖项，会出现无限循环，
// 因为 callback 里有 setUsers，每次 setUsers 后，ProjectList 组件重新渲染，callback 是不同的
export const useMount = (callback: () => void) => {
  useEffect(() => {
    callback();
  }, []);
};

// 注意，使用的是 <V,> ，否则报错
export const useDebounce = <V,>(value: V, delay?: number): V => {
  // 为了不影响入参，另外设置一个值为 debounceValue
  // 将 value 转换为处理后的 debounceValue
  const [debounceValue, setDebounceValue] = useState(value);
  useEffect(() => {
    // 每次在 value 变化时设置一个定时器
    const timeout = setTimeout(() => setDebounceValue(value), delay);
    // 每次在上一个 useEffect 处理完后才执行，用于清除定时器
    return () => clearTimeout(timeout);
  }, [value, delay]);
  // 返回一个新的值
  return debounceValue;
};

// T[] 从入参的数组中提取对象的类型 T
export const useArray = <T,>(persons: T[]) => {
  const [values, setValues] = useState(persons);
  return {
    values,
    add: (item: T) => {
      setValues([...values, item]);
    },
    removeIndex: (index: number) => {
      const copy = [...values];
      copy.splice(index, 1);
      setValues(copy);
    },
    clear: () => {
      setValues([]);
    },
  };
};
```

src/screens/project-list/index.tsx

```tsx
import { useState, useEffect } from "react";

import { SearchPanel } from "./search-panel";
import { List } from "./list";
import * as qs from "qs";
import { cleanObject, useMount, useDebounce } from "../../utils";

const apiUrl = process.env.REACT_APP_API_URL;

export const ProjectList = () => {
  const [users, setUsers] = useState([]);

  const [params, setParams] = useState({
    name: "",
    personId: "",
    // 如果需要模糊搜索需要使用 ?q=，SearchPanel 组件的 input 中设置 q 的值即可
    q: "",
  });

  const [list, setList] = useState([]);

  const debounceParams = useDebounce(params, 1000);

  useEffect(() => {
    fetch(
      `${apiUrl}/projects?${qs.stringify(cleanObject(debounceParams))}`
    ).then(async (response) => {
      if (response.ok) {
        setList(await response.json());
      }
    });
  }, [debounceParams]);

  useMount(() => {
    fetch(`${apiUrl}/users`).then(async (response) => {
      if (response.ok) {
        setUsers(await response.json());
      }
    });
  });

  return (
    <div>
      <SearchPanel params={params} setParams={setParams} users={users} />
      <List list={list} users={users} />
    </div>
  );
};
```

src/screens/project-list/search-panel.tsx

```tsx
export interface Users {
  id: number;
  name: string;
}

// 注意接口里可以使用接口本身定义，就像使用对象一样 params: SearchPanelProps["params"]
interface SearchPanelProps {
  params: {
    name: string;
    personId: string;
    q: string;
  };
  setParams: (params: SearchPanelProps["params"]) => void;
  users: Users[];
}

export const SearchPanel = ({ params, setParams, users }: SearchPanelProps) => {
  return (
    <form>
      <input
        type="text"
        value={params.name}
        onChange={(e) =>
          setParams({
            ...params,
            name: e.target.value,
          })
        }
      />
      <select
        value={params.personId}
        onChange={(e) =>
          setParams({
            ...params,
            personId: e.target.value,
          })
        }
      >
        <option value="">负责人</option>
        {users.map((user) => {
          return (
            <option key={user.id} value={user.id}>
              {user.name}
            </option>
          );
        })}
      </select>
    </form>
  );
};
```

src/screens/project-list/list.tsx

```tsx
// 导入接口
import { Users } from "./search-panel";

interface Projects {
  id: number;
  name: string;
  personId: number;
  organization: string;
  created: string;
}

// 接口相互嵌套使用
interface ListProps {
  list: Projects[];
  users: Users[];
}

export const List = ({ list, users }: ListProps) => {
  return (
    <table>
      <thead>
        <tr>
          <th>项目名称</th>
          <th>负责人</th>
        </tr>
      </thead>
      <tbody>
        {list.map((project) => (
          <tr key={project.id}>
            <td>{project.name}</td>
            {/* 注意这里是 id 与 personId 匹配 */}
            <td>
              {users.find((user) => user.id === project.personId)?.name ||
                "未知"}
            </td>
          </tr>
        ))}
      </tbody>
    </table>
  );
};
```

src/test.tsx

```tsx
// 导入操作数组工具 hook
import { useArray } from "utils";

export const TsReactTest = () => {
  const persons: { name: string; age: number }[] = [
    { name: "jack", age: 25 },
    { name: "rose", age: 23 },
  ];
  const { add, removeIndex, clear, values } = useArray(persons);
  return (
    <div>
      <button onClick={() => add({ name: "john", age: 25 })}>add john</button>
      <button onClick={() => removeIndex(0)}>remove 0</button>
      <button style={{ marginBottom: "50px" }} onClick={() => clear()}>
        clear
      </button>
      {values.map((person: { name: string; age: number }, index: number) => (
        <div style={{ marginBottom: "30px" }}>
          <span style={{ color: "red" }}>{index}</span>
          <span>{person.name}</span>
        </div>
      ))}
    </div>
  );
};
```

## 实现登录表单

定义了泛型，声明时必须将具体的类型传过去，否则报错

接口可以继承其它类型，继承后的接口可以更强大，继承后的接口有的类型，被继承的接口不一定有。

鸭子类型：

赋值时即使两个类型所有属性没有完全对应，只要右边的类型属性与左边的类型属性部分匹配，就不会报错。

下面以 test(a) 为例：

<img src="https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1637334647319-d435067b-1f9d-44c7-9846-eba12126d917.jpeg" alt="img" style="zoom:50%;" />

<img src="https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1637334647487-7a22f989-1d7a-4f35-b1c3-dd685836d8ae.jpeg" alt="img" style="zoom:50%;" />

<img src="https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1637334647485-f3636523-ae2c-481f-af67-71ae42090cf2.jpeg" alt="img" style="zoom: 80%;" />

 src/login/index.tsx

```tsx
import { FormEvent } from "react";

const apiUrl = process.env.REACT_APP_API_URL;

export const Login = () => {
  const login = (params: {username: string, password: string}) => {
    fetch(`${apiUrl}/login`, {
      method: "POST",
      headers: {
        "Content-Type": "application/json"
      },
      body: JSON.stringify(params)
    }).then(
      async (response) => {
        if (response.ok) {
        }
      }
    );
  }
  // 按 Alt 键查看 <form> onSubmit 的类型来添加回调函数 handleSubmit 的入参类型
  const handleSubmit = (e: FormEvent<HTMLFormElement>) => {
     // 阻止默认刷新
     e.preventDefault();
     const username = (e.currentTarget.elements[0] as HTMLFormElement).value;
     const password = (e.currentTarget.elements[1] as HTMLFormElement).value;
     const params = {username, password};
     login(params);
  }

  return (
    <form onSubmit={handleSubmit}>
      <div>
        <label htmlFor="username">用户名:</label>
        <input type="text" id="username"/> 
      </div>
      <div>
        <label htmlFor="password">密码:</label>
        <input type="password" id="password"/>
      </div>
      <button type="submit">登录</button>
    </form>
  )
}
```

`__json-server-mock__`/middlewares.js

```js
module.exports = (req, res, next) => {
  if (req.method === "POST" && req.path === "/login") {
    if (req.body.username === "jack" && req.body.password === "123456") {
      return res.status(200).json({
        user: {
          token: "123"
        }
      })
    }
    else {
      return res.status(400).json({message: "用户名或密码错误"})
    }
  }
  next();
}
```

package.json

```json
"scripts": {
    "mock": "json-server __json-server-mock__/db.json --watch --port 3001 --middlewares ./__json-server-mock__/middlewares.js"
 },
```

```sh
yarn mock
yarn start
```



## 安装开发控制台工具

```sh
yarn add jira-dev-tool
npx msw init ./public
```

src/index.tsx

```tsx
import { loadDevTools } from "jira-dev-tool";

loadDevTools(() => {
  ReactDOM.render(
    <React.StrictMode>
      <App />
    </React.StrictMode>,
    document.getElementById("root")
  );
});
```

## auth-provider 实现

src/screens/project-list/search-panel.tsx

```tsx
export interface Users {
  id: number;
  name: string;
  token: string;
}
```

src/context/auth-provider.ts

```ts
import { Users } from "screens/project-list/search-panel";

const apiUrl = process.env.REACT_APP_API_URL;

const localStorageKey = "__auth_provider_token___";

export const getToken = () => window.localStorage.getItem(localStorageKey);

export const handleUserResponse = ({user} : {user: Users}) => {
  window.localStorage.setItem(localStorageKey, user.token || "");
  return user;
};

export const login = (data: { username: string; password: string }) => {
  return fetch(`${apiUrl}/login`, {
    method: "POST",
    headers: {
      "Content-Type": "application/json",
    },
    body: JSON.stringify(data),
  }).then(async (response) => {
    if (response.ok) {
      return handleUserResponse(await response.json());
    }
  });
};

export const register = (data: { username: string; password: string }) => {
  return fetch(`${apiUrl}/register`, {
    method: "POST",
    headers: {
      "Content-Type": "application/json",
    },
    body: JSON.stringify(data),
  }).then(async (response) => {
    if (response.ok) {
      return handleUserResponse(await response.json());
    }
  });
};

export const logout = () => window.localStorage.removeItem(localStorageKey); 
```

## 用 useContext 存储全局用户信息

<img src="https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1637483409480-051d2c85-cb29-4078-910d-005b6da3dced.jpeg" alt="img" style="zoom:80%;" />

src/context/auth-provider.ts

```ts
import { Users } from "screens/project-list/search-panel";

const apiUrl = process.env.REACT_APP_API_URL;

const localStorageKey = "__auth_provider_token___";

export const getToken = () => window.localStorage.getItem(localStorageKey)

export const handleUserResponse = ({user} : {user: Users}) => {
  window.localStorage.setItem(localStorageKey, user.token || "");
  return user;
}

export const login = (data: { username: string; password: string }) => {
  return fetch(`${apiUrl}/login`, {
    method: "POST",
    headers: {
      "Content-Type": "application/json",
    },
    body: JSON.stringify(data),
  }).then(async (response) => {
    if (response.ok) {
      return handleUserResponse(await response.json());
    }
    else {
      return Promise.reject(await response.json());
    }
  });
};

export const register = (data: { username: string; password: string }) => {
  return fetch(`${apiUrl}/register`, {
    method: "POST",
    headers: {
      "Content-Type": "application/json",
    },
    body: JSON.stringify(data),
  }).then(async (response) => {
    if (response.ok) {
      return handleUserResponse(await response.json());
    }
    else {
      return Promise.reject(await response.json());
    }
  });
};

export const logout = async () => window.localStorage.removeItem(localStorageKey); 
```

src/context/auth-context.tsx

```tsx
import React, { ReactNode, useState } from  "react";
import { Users } from "screens/project-list/search-panel";
import * as auth from "./auth-provider";

interface AuthForm {
  username: string,
  password: string
}

interface AuthContextProps {
  user: Users | null,
  register: (form: AuthForm) => Promise<void>,
  login: (form: AuthForm) => Promise<void>,
  logout: (form: AuthForm) => Promise<void>
}

const AuthContext = React.createContext<AuthContextProps | undefined>(undefined);
// 这行用于 devtool，除此之外没什么用
AuthContext.displayName = "AuthContext";

export const AuthProvider = ({children}: { children: ReactNode }) => {
  const [user, setUser] = useState<Users | null>(null);
  
  // setUser 函数式编程 point free
  const register = (form: AuthForm) => auth.register(form).then(setUser);
  const login = (form: AuthForm) => auth.login(form).then(setUser);
  const logout = () => auth.logout().then(() => setUser(null));

  return <AuthContext.Provider children={children} value={{user, register, login, logout}}/>
}

export const useAuth = () => {
  const context = React.useContext(AuthContext);
  if (!context) {
    throw new Error("userAuth 必须在 AuthProvider 中使用");
  }
  return context;
}
```

src/context/index.tsx

```tsx
import React, { ReactNode } from  "react";
import { AuthProvider } from "./auth-context"; 

export const AppProviders = ({children}: {children: ReactNode}) => {
  // return <AuthProvider children={children}/> 与下面代码等价
  return (<AuthProvider>{children}</AuthProvider>)
}
```

src/index.tsx

```tsx
import React from 'react';
import ReactDOM from 'react-dom';
import './index.css';
import App from './App';
import reportWebVitals from './reportWebVitals';
import { loadDevTools } from "jira-dev-tool";
import { AppProviders } from "context";

loadDevTools(() => {
  ReactDOM.render(
    <React.StrictMode>
      <AppProviders>
        <App />
      </AppProviders>
    </React.StrictMode>,
    document.getElementById("root")
  );
});

reportWebVitals();
```

src/login/index.tsx

```tsx
import { useAuth } from "context/auth-context";
import { FormEvent } from "react";

export const Login = () => {
  const {register, login, user} = useAuth();

  const handleSubmit = (e: FormEvent<HTMLFormElement>) => {
    e.preventDefault();
    const username = (e.currentTarget.elements[0] as HTMLFormElement).value;
    const password = (e.currentTarget.elements[1] as HTMLFormElement).value;
    const params = { username, password };
    login(params);
  };

  return (
    <form onSubmit={handleSubmit}>
      {user ? <div>
        <div>登录成功，用户名: {user?.name} </div> 
        <div>token: {user?.token} </div> 
      </div> : null}
      <div>
        <label htmlFor="username">用户名:</label>
        <input type="text" id="username" />
      </div>
      <div>
        <label htmlFor="password">密码:</label>
        <input type="password" id="password" />
      </div>
      <button type="submit">登录</button>
    </form>
  );
};
```

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1637482839021-a63ebb51-8869-4ec7-88e8-cdf708bfed9a.jpeg)

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1637482839033-976f605b-91f9-4b84-b020-1578341fbbba.jpeg)

## 用 useAuth 切换登录与非登录状态

<img src="https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1637496093607-7ca5d23f-3fd4-4bdf-ab1a-27103aab21c5.jpeg" alt="img" style="zoom:80%;" />

src/App.tsx

```tsx
import "./App.css";
import { useAuth } from "context/auth-context";
import { AuthenticatedApp } from "authenticated-app";
import { UnauthenticatedApp } from "unauthenticated-app";

function App() {
  const {user} = useAuth();

  return (
    <div className="App">
      {user ? <AuthenticatedApp/> : <UnauthenticatedApp/>}
    </div>
  );
}

export default App;
```

src/authenticated-app/index.tsx

```tsx
import { ProjectList } from "screens/project-list";
import { useAuth } from "context/auth-context";

export const AuthenticatedApp = () => {
  const { logout } = useAuth();
  return (
    <div>
      <button onClick={logout}>登出</button>
      <ProjectList/>
    </div>
  )
}
```

src/unauthenticated-app/index.tsx

```tsx
import { useState } from "react";
import { Login } from "./login";
import { Register } from "./register";

export const UnauthenticatedApp = () => {
  const [isRegister, setIsRegister] = useState(false);
  return (
    <div>
      {isRegister? <Register/> : <Login/>}
      <button onClick={() => setIsRegister(!isRegister)}>切换到{isRegister ? "登录" : "注册"}</button>
    </div>    
  )
}
```

src/unauthenticated-app/login.tsx

```tsx
import { useAuth } from "context/auth-context";
import { FormEvent } from "react";

export const Login = () => {
  const { login } = useAuth();

  const handleSubmit = (e: FormEvent<HTMLFormElement>) => {
    e.preventDefault();
    const username = (e.currentTarget.elements[0] as HTMLFormElement).value;
    const password = (e.currentTarget.elements[1] as HTMLFormElement).value;
    const params = { username, password };
    login(params);
  };

  return (
    <form onSubmit={handleSubmit}>
      <div>
        <label htmlFor="username">用户名:</label>
        <input type="text" id="username" />
      </div>
      <div>
        <label htmlFor="password">密码:</label>
        <input type="password" id="password" />
      </div>
      <button type="submit">登录</button>
    </form>
  );
};
```

src/unauthenticated-app/register.tsx

```tsx
import { useAuth } from "context/auth-context";
import { FormEvent } from "react";

export const Register = () => {
  const { register } = useAuth();

  const handleSubmit = (e: FormEvent<HTMLFormElement>) => {
    e.preventDefault();
    const username = (e.currentTarget.elements[0] as HTMLFormElement).value;
    const password = (e.currentTarget.elements[1] as HTMLFormElement).value;
    const params = { username, password };
    register(params);
  };

  return (
    <form onSubmit={handleSubmit}>
      <div>
        <label htmlFor="username">用户名:</label>
        <input type="text" id="username" />
      </div>
      <div>
        <label htmlFor="password">密码:</label>
        <input type="password" id="password" />
      </div>
      <button type="submit">注册</button>
    </form>
  );
};
```

![img](https://cdn.nlark.com/yuque/0/2021/gif/1614731/1637496105250-69c0cfd2-e0e6-4525-9fb8-dad2c9951a69.gif)

注册后/登录后

<img src="https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1637496093609-79b66a71-7a35-4a80-a76c-8496dca592fa.jpeg" alt="img" style="zoom:50%;" />

## 用 fetch 抽象通用 HTTP 请求方法

src/utils/http.ts

```ts
import qs from "qs";  
import * as auth from "context/auth-provider";

const apiUrl = process.env.REACT_APP_API_URL;

// 从声明文件中继承接口 RequestInit
interface Config extends RequestInit {
  data?: object,
  token?: string
}

const http = async (endpoint: string, { headers, token, data, ...customConfig}: Config) => {
  const config = {
    method: "GET",
    headers: {
      "Authorization": token ? `Bear ${token}` : "",
      "Content-Type": data ? "application/json" : "",
    },
    ...customConfig
  }

  if (config.method.toUpperCase() === "GET") {
    endpoint += `?${qs.stringify(data)}`
  }
  else {
    config.body = JSON.stringify(data || {})
  }

  // axios 和 fetch 的表现不一样，axios 可以直接在返回状态不为 2xx 的时候抛出异常
  return window.fetch(`${apiUrl}/${endpoint}`, config)
    .then(async response => {
      if (response.status === 401) {
        await auth.logout();
        window.location.reload();
        return Promise.reject("请重新登录");
      }
      const data = await response.json();
      if (response.ok) {
        return data;
      } 
      // fetch 需要 response.ok 为 false 时用 Promise.reject 来捕获异常，无法通过 .catch 捕获异常
      else {
        return Promise.reject(data);
      }
    }) 
}
```

## 使用 useHttp 并添加登录状态维持

<img src="https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1637509117021-c73472e5-5e11-4ef4-936d-131f64f16f57.jpeg" alt="img" style="zoom:80%;" />

src/utils/http.ts

```ts
import * as qs from "qs";  
import * as auth from "context/auth-provider";
import { useAuth } from "context/auth-context";

const apiUrl = process.env.REACT_APP_API_URL;

interface Config extends RequestInit {
  data?: object,
  token?: string
}

// axios 和 fetch 的表现不一样，axios 可以直接在返回状态不为 2xx 的时候抛出异常
export const http = async (endpoint: string, {data, headers, token, ...customConfig}: Config = {}) => {
  const config = {
    method: "GET",
    headers: {
      Authorization: token ? `Bearer ${token}` : "",
      "Content-Type": data ? "application/json" : "",
    },
    ...customConfig
  }

  if (config.method.toUpperCase() === "GET") {
    endpoint += `?${qs.stringify(data)}`
  }
  else {
    config.body = JSON.stringify(data || {})
  }

  return window.fetch(`${apiUrl}/${endpoint}`, config) 
    .then(async response => {
      if (response.status === 401) {
        await auth.logout();
        window.location.reload();
        return Promise.reject({message: "请重新登录"});
      }
      const data = await response.json();
      if (response.ok) {
        return data;
      } 
      // fetch 需要 Promise.reject 来捕获异常，无法通过 .catch 捕获异常
      else {
        return Promise.reject(data);
      }
    }) 
}

export const useHttp = () => {
  const { user } = useAuth();
  // 下面返回的函数的参数与 http 函数的参数基本等价，只是简写
  // 下面的 typeof 是 TS 中静态的
  return (...[endpoint, config]: Parameters<typeof http>) => http(endpoint, {...config, token: user?.token})
}
```

src/screens/project-list/index.tsx

```tsx
import { useState, useEffect } from "react";

import { SearchPanel } from "./search-panel";
import { List } from "./list";
import { cleanObject, useMount, useDebounce } from "../../utils";
import { useHttp } from "utils/http";

export const ProjectList = () => {
  const [params, setParams] = useState({
    name: "",
    personId: "",
    // 如果需要模糊搜索需要使用 ?q=，SearchPanel 组件的 input 中设置 q 的值即可
    q: "",
  });
  const [users, setUsers] = useState([]);
  const [list, setList] = useState([]);

  const debounceParams = useDebounce(params, 1000);
  const client = useHttp();

  // 以下都是默认 GET 请求
  useEffect(() => {
    client("projects", {data: cleanObject(debounceParams)}).then(setList);
  }, [debounceParams]);

  useMount(() => {
    client("users").then(setUsers);
  });

  return (
    <div>
      <SearchPanel params={params} setParams={setParams} users={users} />
      <List list={list} users={users} />
    </div>
  );
};
```

src/context/auth-context.tsx

```tsx
import React, { ReactNode, useState } from  "react";
import { Users } from "screens/project-list/search-panel";
import * as auth from "./auth-provider";
import { http } from "../utils/http";
import { useMount } from "utils";

interface AuthForm {
  username: string,
  password: string
}

interface AuthContextProps {
  user: Users | null,
  register: (form: AuthForm) => Promise<void>,
  login: (form: AuthForm) => Promise<void>,
  logout: () => Promise<void>
}

const bootStrapUser = async () => {
  let user = null;
  const token = auth.getToken();
  if (token) {
    const data = await http("me", {token});
    user = data.user;
  }
  return user;
}

const AuthContext = React.createContext<AuthContextProps | undefined>(undefined);
AuthContext.displayName = "AuthContext";

export const AuthProvider = ({children}: { children: ReactNode }) => {
  const [user, setUser] = useState<Users | null>(null);

  const register = (form: AuthForm) => auth.register(form).then(setUser);
  const login = (form: AuthForm) => auth.login(form).then(setUser);
  const logout = () => auth.logout().then(() => setUser(null));

  useMount(() => {
    bootStrapUser().then(setUser);
  })

  return <AuthContext.Provider children={children} value={{user, register, login, logout}}/>
}

export const useAuth = () => {
  const context = React.useContext(AuthContext);
  if (!context) {
    throw new Error("userAuth 必须在 AuthProvider 中使用");
  }
  return context;
}
```

<img src="https://cdn.nlark.com/yuque/0/2021/gif/1614731/1637509116944-60d1bf91-a7b2-4eda-b243-a7008873f533.gif" alt="img" style="zoom:80%;" />



## 使用 antd 组件库美化

```sh
yarn add antd
yarn add @craco/craco
yarn add craco-less
```

craco.config.js

```js
const CracoLessPlugin = require('craco-less');

module.exports = {
  plugins: [
    {
      plugin: CracoLessPlugin,
      options: {
        lessLoaderOptions: {
          lessOptions: {
            modifyVars: { '@primary-color': 'rgb(0, 82, 204)', '@font-size': '16px' },
            javascriptEnabled: true,
          },
        },
      },
    },
  ],
};
```

src\index.tsx

```tsx
import React from "react";
import ReactDOM from "react-dom";
import "./index.css";
import App from "./App";
import reportWebVitals from "./reportWebVitals";
import { loadDevTools } from "jira-dev-tool";
// 覆盖 jira-dev-tool 的样式
import "antd/dist/antd.less";
import { AppProviders } from "context";

loadDevTools(() => {
  ReactDOM.render(
    <React.StrictMode>
      <AppProviders>
        <App />
      </AppProviders>
    </React.StrictMode>,
    document.getElementById("root")
  );
});

reportWebVitals();
```

src\unauthenticated-app\register.tsx

```tsx
import { useAuth } from "context/auth-context";
import { Form, Input, Button } from "antd";

export const Login = () => {
  const { login } = useAuth();

  const handleSubmit = (values: {username: string, password: string}) => {
    login(values);
  };

  return (
    <Form onFinish={handleSubmit}>
      <Form.Item name={"username"} rules={[{required: true, message: "请输入用户名"}]}>
        <Input placeholder={"用户名"} type="text" id={"username"} />
      </Form.Item>
      <Form.Item name={"password"} rules={[{required: true, message: "请输入密码"}]}>
        <Input placeholder={"密码"} type="password" id={"password"} />
      </Form.Item>
      <Form.Item>
        <Button htmlType={"submit"} type={"primary"}>登录</Button>
      </Form.Item>
    </Form>
  );
};
```

src\unauthenticated-app\register.tsx

```tsx
import { useAuth } from "context/auth-context";
import { Form, Input, Button } from "antd";

export const Register = () => {
  const { register } = useAuth();

  const handleSubmit = (values: {username: string, password: string}) => {
    register(values);
  };

  return (
    <Form onFinish={handleSubmit}>
      <Form.Item name={"username"} rules={[{required: true, message: "请输入用户名"}]}>
        <Input placeholder={"用户名"} type="text" id={"username"} />
      </Form.Item>
      <Form.Item name={"password"} rules={[{required: true, message: "请输入密码"}]}>
        <Input placeholder={"密码"} type="password" id={"password"} />
      </Form.Item>
      <Form.Item>
        <Button htmlType={"submit"} type={"primary"}>注册</Button>
      </Form.Item>
    </Form>
  );
};
```

src\unauthenticated-app\index.tsx

```tsx
import { useState } from "react";
import { Login } from "./login";
import { Register } from "./register";
import { Card } from "antd";

export const UnauthenticatedApp = () => {
  const [isRegister, setIsRegister] = useState(false);
  return (
    <div style={{display: "flex", justifyContent: "center"}}>
      <Card>
        {isRegister ? <Register /> : <Login />}
        <button onClick={() => setIsRegister(!isRegister)}>
          切换到{isRegister ? "登录" : "注册"}
        </button>
      </Card>
    </div>
  );
};
```

src\screens\project-list\list.tsx

```tsx
import { Users } from "./search-panel";
import { Table } from "antd";

interface Projects {
  id: number;
  name: string;
  personId: number;
  organization: string;
  created: string;
}

interface ListProps {
  list: Projects[];
  users: Users[];
}

export const List = ({ list, users }: ListProps) => {
  return (
    <Table pagination={false} dataSource={list} columns={[{
      title: "名称",
      dataIndex: "name",
      // 按中文名排序
      sorter: (a, b) => a.name.localeCompare(b.name),
    }, {
      title: "负责人",
      render(value, project) {
        return <span>
          {users.find((user) => user.id === project.personId)?.name || "未知"}
        </span>
      }
    }]}>
    </Table>
  );
};

```

src\screens\project-list\search-panel.tsx

```tsx
import { Input, Select } from "antd";

export interface Users {
  id: number;
  name: string;
  token: string;
}

interface SearchPanelProps {
  params: {
    name: string;
    personId: string;
    q: string;
  };
  setParams: (params: SearchPanelProps["params"]) => void;
  users: Users[];
}

export const SearchPanel = ({ params, setParams, users }: SearchPanelProps) => {
  return (
    <form>
      <Input
        type="text"
        value={params.name}
        onChange={(e) =>
          setParams({
            ...params,
            name: e.target.value,
          })
        }
      />
      <Select
        value={params.personId}
        onChange={(value) =>
          setParams({
            ...params,
            personId: value,
          })
        }
      >
        <Select.Option value="">负责人</Select.Option>
        {users.map((user) => {
          return (
            <Select.Option key={user.id} value={user.id}>
              {user.name}
            </Select.Option>
          );
        })}
      </Select>
    </form>
  );
};
```

![img](https://cdn.nlark.com/yuque/0/2021/gif/1614731/1637841584338-d5f01c0c-84f9-4db9-a53c-c35c7f999902.gif)

## 使用 emotion 优化登录注册界面

```sh
 yarn add @emotion/react @emotion/styled
```

App.css

```css
html {
  /* rem em */
  /*em 相对于父元素的font-size*/
  /*rem 相对于根元素html的font-size, r就是root的意思*/
  /*16 * 62.5% = 10px*/
  /*1rem === 10px*/
  font-size: 62.5%;
}

/*viewport height === vh*/
html body #root .App {
  min-height: 100vh;
}
```

src\unauthenticated-app\index.tsx

```tsx
import { useState } from "react";
import { Login } from "./login";
import { Register } from "./register";
import { Card, Divider, Button } from "antd";
import styled from "@emotion/styled";
import logo from "assets/logo.svg";
import left from "assets/left.svg";
import right from "assets/right.svg";

export const UnauthenticatedApp = () => {
  const [isRegister, setIsRegister] = useState(false);
  return (
    <Container>
      <Header />
      <Background />
      <ShadowCard>
        <Title>{isRegister ? "请注册" : "请登录"}</Title>
        {isRegister ? <Register /> : <Login />}
        <Divider />
        <Button type={"link"} onClick={() => setIsRegister(!isRegister)}>
          {isRegister ? "已经有账号了？直接登录" : "没有账号？注册新账号"}
        </Button>
      </ShadowCard>
    </Container>
  );
};

export const LongButton = styled(Button)`
  width: 100%;
`;

const Container = styled.div`
  display: flex;
  flex-direction: column;
  align-items: center;
  min-height: 100vh;
`;

const Header = styled.header`
  background: url(${logo}) no-repeat center;
  padding: 5rem 0;
  background-size: 8rem;
  width: 100%;
`;

const Background = styled.div`
  position: absolute;
  width: 100%;
  height: 100%;
  background-repeat: no-repeat;
  background-attachment: fixed;
  background-position: left bottom, right bottom;
  background-size: calc(((100vw - 40rem) / 2) - 3.2rem),
    calc(((100vw - 40rem) / 2) - 3.2rem), cover;
  background-image: url(${left}), url(${right});
`;

const ShadowCard = styled(Card)`
  width: 40rem;
  min-height: 56rem;
  padding: 3.2rem 4rem;
  border-radius: 0.3rem;
  box-sizing: border-box;
  box-shadow: rgba(0, 0, 0, 0.1) 0 0 10px;
  text-align: center;
`;


const Title = styled.h2`
  margin-bottom: 2.4rem;
  color: rgb(94, 108, 132);
`;
```

src\unauthenticated-app\login.tsx

```tsx
import { useAuth } from "context/auth-context";
import { Form, Input, Button } from "antd";
import { LongButton } from "unauthenticated-app/index";

export const Login = () => {
  const { login } = useAuth();

  const handleSubmit = (values: { username: string; password: string }) => {
    login(values);
  };

  return (
    <Form onFinish={handleSubmit}>
      <Form.Item
        name={"username"}
        rules={[{ required: true, message: "请输入用户名" }]}
      >
        <Input placeholder={"用户名"} type="text" id={"username"} />
      </Form.Item>
      <Form.Item
        name={"password"}
        rules={[{ required: true, message: "请输入密码" }]}
      >
        <Input placeholder={"密码"} type="password" id={"password"} />
      </Form.Item>
      <Form.Item>
        <LongButton htmlType={"submit"} type={"primary"}>
          登录
        </LongButton>
      </Form.Item>
    </Form>
  );
};
```

src\unauthenticated-app\register.tsx

```tsx
import { useAuth } from "context/auth-context";
import { Form, Input, Button } from "antd";
import { LongButton } from "unauthenticated-app/index";

export const Register = () => {
  const { register } = useAuth();

  const handleSubmit = (values: { username: string; password: string }) => {
    register(values);
  };

  return (
    <Form onFinish={handleSubmit}>
      <Form.Item
        name={"username"}
        rules={[{ required: true, message: "请输入用户名" }]}
      >
        <Input placeholder={"用户名"} type="text" id={"username"} />
      </Form.Item>
      <Form.Item
        name={"password"}
        rules={[{ required: true, message: "请输入密码" }]}
      >
        <Input placeholder={"密码"} type="password" id={"password"} />
      </Form.Item>
      <Form.Item>
        <LongButton htmlType={"submit"} type={"primary"}>
          注册
        </LongButton>
      </Form.Item>
    </Form>
  );
};
```

src\index.tsx

```tsx
import React from "react";
import ReactDOM from "react-dom";
// 删除 index.css ，使用 APP.css 作为全局样式
import App from "./App";
import reportWebVitals from "./reportWebVitals";
import { loadDevTools } from "jira-dev-tool";
// 覆盖 jira-dev-tool 的样式
import "antd/dist/antd.less";
import { AppProviders } from "context";

loadDevTools(() => {
  ReactDOM.render(
    <React.StrictMode>
      <AppProviders>
        <App />
      </AppProviders>
    </React.StrictMode>,
    document.getElementById("root")
  );
});

reportWebVitals();
```

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1637847738604-2e79c3a8-4c6c-47c7-bd3a-dea999b8bfd1.jpeg)

## 优化项目列表页面

```sh
yarn add dayjs
```

src\components\lib.tsx

```tsx
import styled from "@emotion/styled";

export const Row = styled.div<{
  gap?: number | boolean;
  between?: boolean;
  marginBottom?: number;
}>`
  display: flex;
  align-items: center;
  justify-content: ${(props) => (props.between ? "space-between" : undefined)};
  margin-bottom: ${(props) => props.marginBottom + "rem"};

  > * {
    margin-top: 0 !important;
    margin-bottom: 0 !important;
    margin-right: ${(props) =>
      typeof props.gap === "number"
        ? props.gap + "rem"
        : props.gap
        ? "2rem"
        : undefined};
  }
`;
```

src\authenticated-app\index.tsx

```tsx
import styled from "@emotion/styled";
import { Dropdown, Menu, Button } from "antd";

import { ProjectList } from "screens/project-list";
import { Row } from "components/lib";
import { ReactComponent as SoftwareLogo } from "assets/software-logo.svg";

import { useAuth } from "context/auth-context";

export const AuthenticatedApp = () => {
  const { logout, user } = useAuth();
  return (
    <Container>
      <Header between={true}>
        <HeaderLeft gap={true}>
          <SoftwareLogo width={"18rem"} color={"rgb(38, 132, 255)"} />
          <h2>项目</h2>
          <h2>用户</h2>
        </HeaderLeft>
        <HeaderRight>
          <Dropdown
             overlay={
               <Menu>
                 <Menu.Item key={"logout"}>
                   <Button onClick={logout} type={"link"}>
                     登出
                   </Button>
                 </Menu.Item>
               </Menu>
             }
           >
             <Button type={"link"} onClick={(e) => e.preventDefault()}>
               Hi, {user?.name}
             </Button>
           </Dropdown>
        </HeaderRight> 
      </Header>
      <Main>
        <ProjectList />
      </Main>
    </Container>
  );
};

const Container = styled.div`
  display: grid;
  grid-template-rows: 6rem 1fr;
  height: 100vh;
`;

// grid-area 用来给grid子元素起名字
const Header = styled(Row)`
  padding: 3.2rem;
  box-shadow: 0 0 5px 0 rgba(0, 0, 0, 0.1);
  z-index: 1;
`;

const HeaderLeft = styled(Row)``;
const HeaderRight = styled.div``;
const Main = styled.main`
  overflow: hidden;
`;
```

src\screens\project-list\index.tsx

```tsx
import { useState, useEffect } from "react";
import styled from "@emotion/styled";

import { SearchPanel } from "./search-panel";
import { List } from "./list";
import { cleanObject, useMount, useDebounce } from "../../utils";
import { useHttp } from "utils/http";

export const ProjectList = () => {
  const [params, setParams] = useState({
    name: "",
    personId: "",
    // 如果需要模糊搜索需要使用 ?q=，SearchPanel 组件的 input 中设置 q 的值即可
    q: "",
  });
  const [users, setUsers] = useState([]);
  const [list, setList] = useState([]);

  const debounceParams = useDebounce(params, 1000);
  const client = useHttp();

  useEffect(() => {
    client("projects", { data: cleanObject(debounceParams) }).then(setList);
  }, [debounceParams]);

  useMount(() => {
    client("users").then(setUsers);
  });

  return (
    <Container>
      <h1>项目列表</h1>
      <SearchPanel params={params} setParams={setParams} users={users} />
      <List list={list} users={users} />
    </Container>
  );
};

const Container = styled.div`
  padding: 3.2rem;
`;
```

src\screens\project-list\search-panel.tsx

```tsx
import { Input, Select, Form } from "antd";

export interface Users {
  id: number;
  name: string;
  token: string;
}

interface SearchPanelProps {
  params: {
    name: string;
    personId: string;
    q: string;
  };
  setParams: (params: SearchPanelProps["params"]) => void;
  users: Users[];
}

export const SearchPanel = ({ params, setParams, users }: SearchPanelProps) => {
  return (
     <Form style={{ marginBottom: "2rem" }} layout={"inline"}>
      <Form.Item>
        <Input
          type="text"
          value={params.name}
          onChange={(e) =>
            setParams({
              ...params,
              name: e.target.value,
            })
          }
        />
      </Form.Item> 
      <Form.Item>
        <Select
          value={params.personId}
          onChange={(value) =>
            setParams({
              ...params,
              personId: value,
            })
          }
        >
          <Select.Option value="">负责人</Select.Option>
          {users.map((user) => {
            return (
              <Select.Option key={user.id} value={user.id}>
                {user.name}
              </Select.Option>
            );
          })}
        </Select>
      </Form.Item> 
    </Form>
  );
};
```

src\screens\project-list\list.tsx

```tsx
import { Users } from "./search-panel";
import { Table } from "antd";
import dayjs from "dayjs";

interface Projects {
  id: number;
  name: string;
  personId: number;
  organization: string;
  created: number;
}

interface ListProps {
  list: Projects[];
  users: Users[];
}

export const List = ({ list, users }: ListProps) => {
  return (
    <Table
      pagination={false}
      dataSource={list}
      columns={[
        {
          title: "名称",
          dataIndex: "name",
          // 按中文名排序
          sorter: (a, b) => a.name.localeCompare(b.name),
        },
        {
          title: "部门",
          dataIndex: "organization",
        },
        {
          title: "负责人",
          render(value, project) {
            return (
              <span>
                {users.find((user) => user.id === project.personId)?.name ||
                  "未知"}
              </span>
            );
          },
        },
        {
          title: "创建时间",
          render(value, project) {
            return (
              <span>
                {project.created
                  ? dayjs(project.created).format("YYYY-MM-DD")
                  : "无"}
              </span>
            );
          },
        },
      ]}
    ></Table>
  );
};
```

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1638099157888-259eaed1-bd92-41fe-abf8-42d20fa767ee.jpeg)

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1638099157895-20db2cbb-705b-485d-ba84-683378c18799.jpeg)

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1638099157976-eb65fc3a-9688-4d39-ba11-24fdf5137031.jpeg)

src\utils\index.ts

修改 cleanObject

```ts
import { useState, useEffect } from "react";

const isFalsy = (val: unknown) => (val === 0 ? false : !val);

const isVoid = (value: unknown) => value === undefined || value === null || value === "";

// 在一个函数里，改变传入的对象本身是不好的
// 不需要其它 hook，所以不需要改造为自定义 hook
export const cleanObject = (object: {[key: string]: unknown}) => {
  const result = { ...object };
  Object.keys(result).forEach((key) => {
    const value = result[key];
    if (isVoid(value)) {
      delete result[key];
    }
  });
  // 处理后记得返回
  return result;
};
```

修复 devtool 引发的控制台报错

```sh
yarn add jira-dev-tool@next
yarn add react-query
```

src\index.tsx

```tsx
import React from "react";
import ReactDOM from "react-dom";
import App from "./App";
import reportWebVitals from "./reportWebVitals";
import { DevTools, loadServer } from "jira-dev-tool";
// 覆盖 jira-dev-tool 的样式
import "antd/dist/antd.less";
import { AppProviders } from "context";

loadServer(() => {
  ReactDOM.render(
    <React.StrictMode>
      <AppProviders>
         <DevTools />
        <App />
      </AppProviders>
    </React.StrictMode>,
    document.getElementById("root")
  );
});

reportWebVitals();
```

src\context\index.tsx

```tsx
import React, { ReactNode } from "react";
import { AuthProvider } from "./auth-context";
import { QueryClient, QueryClientProvider } from "react-query";

export const AppProviders = ({ children }: { children: ReactNode }) => {
  // return <AuthProvider children={children}/>
  const queryClient = new QueryClient();

  return (
    <QueryClientProvider client={queryClient}>
        <AuthProvider>{children}</AuthProvider>
    </QueryClientProvider>
  );
};
```

## 添加列表的 Loading 和 Error 处理

src\screens\project-list\index.tsx

```tsx
import { useState, useEffect } from "react";
import styled from "@emotion/styled";
import { Typography } from "antd";

import { SearchPanel } from "./search-panel";
import { List } from "./list";

import { cleanObject, useMount, useDebounce } from "../../utils";
import { useHttp } from "utils/http";

export const ProjectList = () => {
  const [params, setParams] = useState({
    name: "",
    personId: "",
    // 如果需要模糊搜索需要使用 ?q=，SearchPanel 组件的 input 中设置 q 的值即可
    q: "",
  });
  const [users, setUsers] = useState([]);
  const [list, setList] = useState([]);
  const [isLoading, setIsLoading] = useState(false);
  const [error, setError] = useState<null | Error>(null);

  const debounceParams = useDebounce(params, 1000);
  const client = useHttp();

  useEffect(() => {
    setIsLoading(true);
    client("projects", { data: cleanObject(debounceParams) })
      .then((res) => {
        setList(res);
        setError(null);
      })
      .catch((error) => {
        setList([]);
        setError(error);
      })
      .finally(() => setIsLoading(false));
  }, [debounceParams]);

  useMount(() => {
    client("users").then(setUsers);
  });

  return (
    <Container>
      <h1>项目列表</h1>
      <SearchPanel params={params} setParams={setParams} users={users} />
      {error ? <Typography.Text type={"danger"}>{error.message}</Typography.Text> : null}
      <List users={users} dataSource={list} loading={isLoading}/>
    </Container>
  );
};

const Container = styled.div`
  padding: 3.2rem;
`;
```

src\screens\project-list\list.tsx

```tsx
import { Users } from "./search-panel";
import { Table, TableProps } from "antd";
import dayjs from "dayjs";

interface Projects {
  id: number;
  name: string;
  personId: number;
  organization: string;
  created: number;
}

interface ListProps extends TableProps<Projects>{
  users: Users[];
}

// ...props 的类型相当于 type PropsType = Omit<ListProps, 'users'>
export const List = ({ users, ...props }: ListProps) => {
  return (
    <Table
      pagination={false}
      columns={[
        {
          title: "名称",
          dataIndex: "name",
          // 按中文名排序
          sorter: (a, b) => a.name.localeCompare(b.name),
        },
        {
          title: "部门",
          dataIndex: "organization",
        },
        {
          title: "负责人",
          render(value, project) {
            return (
              <span>
                {users.find((user) => user.id === project.personId)?.name ||
                  "未知"}
              </span>
            );
          },
        },
        {
          title: "创建时间",
          render(value, project) {
            return (
              <span>
                {project.created
                  ? dayjs(project.created).format("YYYY-MM-DD")
                  : "无"}
              </span>
            );
          },
        },
      ]}
      {...props}
    ></Table>
  );
};
```

## 自定义 useAsync 统一处理  Loading 和 Error 

<img src="https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1638357921041-91131a33-6778-48e1-96f2-100ee5e159a5.jpeg" alt="img" style="zoom: 80%;" />

src\screens\project-list\index.tsx

```tsx
import { useState } from "react";
import styled from "@emotion/styled";
import { Typography } from "antd";

import { SearchPanel } from "./search-panel";
import { List } from "./list";

import { useDebounce } from "../../utils";
import { useProjects } from "../../utils/projects";
import { useUsers } from "../../utils/users";

export const ProjectList = () => {
  const [params, setParams] = useState({
    name: "",
    personId: "",
    // 如果需要模糊搜索需要使用 ?q=，SearchPanel 组件的 input 中设置 q 的值即可
    q: "",
  });

  const debounceParams = useDebounce(params, 1000);
  const { isLoading, error, data: list } = useProjects(debounceParams);
  const { data: users } = useUsers();

  return (
    <Container>
      <h1>项目列表</h1>
      <SearchPanel params={params} setParams={setParams} users={users || []} />
      {error ? <Typography.Text type={"danger"}>{error.message}</Typography.Text> : null}
      <List users={users || []} dataSource={list || []} loading={isLoading}/>
    </Container>
  );
};

const Container = styled.div`
  padding: 3.2rem;
`;
```

src\screens\project-list\list.tsx

```tsx
import { Users } from "./search-panel";
import { Table, TableProps } from "antd";
import dayjs from "dayjs";

export interface Projects {
  id: number;
  name: string;
  personId: number | string; // 传入时是 string 类型，用于 fetch 传参，数据返回时是 number
  organization: string;
  created: number;
}

interface ListProps extends TableProps<Projects>{
  users: Users[];
}

// ...props 的类型 type PropsType = Omit<ListProps, 'users'>
export const List = ({ users, ...props }: ListProps) => {
  return (
    <Table
      pagination={false}
      columns={[
        {
          title: "名称",
          dataIndex: "name",
          // 按中文名排序
          sorter: (a, b) => a.name.localeCompare(b.name),
        },
        {
          title: "部门",
          dataIndex: "organization",
        },
        {
          title: "负责人",
          render(value, project) {
            return (
              <span>
                {users.find((user) => user.id === project.personId)?.name ||
                  "未知"}
              </span>
            );
          },
        },
        {
          title: "创建时间",
          render(value, project) {
            return (
              <span>
                {project.created
                  ? dayjs(project.created).format("YYYY-MM-DD")
                  : "无"}
              </span>
            );
          },
        },
      ]}
      {...props}
    ></Table>
  );
};
```

src\utils\use-async.ts

```ts
import { useState } from "react";

interface State<D> {
  stat: "idle" | "loading" | "success" | "error";
  data: D | null;
  error: Error | null; 
}

const defaultInitialState: State<null> = {
  stat: "idle",
  data: null,
  error: null,
};

export const useAsync = <D>(initialState?: State<D>) => {
  const [state, setState] = useState<State<D>>({
    ...defaultInitialState,
    ...initialState
  });
 
  const setData = (data: D) => setState({
    stat: "success",
    data,
    error: null,
  })

  const setError = (error: Error) => setState({
    stat: "error",
    data: null,
    error,
  })

  // run 用来触发异步请求
  const run = (promise: Promise<D>) => {
    if (!promise || !promise.then) {
      throw new Error("请传入 Promise 类型数据");
    }
    setState({ ...state, stat: "loading" });
    return promise
      .then((data) => {
        setData(data);
        return data;
      })
      .catch((error) => {
        // catch会消化异常，如果不主动抛出，外面是接收不到异常的
        setError(error);
        return error;
      });
  };

  return {
    isIdle: state.stat === "idle",
    isLoading: state.stat === "loading",
    isSuccess: state.stat === "success",
    isError: state.stat === "error",
    run,
    setData,
    setError,
    ...state,
  };
};
```

src\utils\projects.ts

```ts
import { useEffect } from "react";

import { Projects } from "screens/project-list/list";

import { useHttp } from "./http";
import { cleanObject } from "./index";
import { useAsync } from "./use-async";

export const useProjects = (param?: Partial<Projects>) => {
  const client = useHttp();
  const {run, ...result}  = useAsync<Projects[]>();

  useEffect(() => {
    run(client("projects", { data: cleanObject(param || {}) }))
  }, [param]);
  
  return result;
};
```

src\utils\users.ts

```ts
import { useEffect } from "react";

import { Users } from "screens/project-list/search-panel";

import { useHttp } from "./http";
import { cleanObject } from "./index";
import { useAsync } from "./use-async";

export const useUsers = (param?: Partial<Users>) => {
  const client = useHttp();
  const {run, ...result}  = useAsync<Users[]>();

  useEffect(() => {
    run(client("users", { data: cleanObject(param || {}) }))
  }, [param]);
  
  return result;
};
```

效果图

<img src="https://cdn.nlark.com/yuque/0/2021/gif/1614731/1638357106952-fd22a99c-3245-4303-851d-fb35ff482f76.gif" alt="img"  />

## 添加登录和注册的 Loading 和 Error 处理

src\utils\use-async.ts

```ts
import { useState } from "react";

interface State<D> {
  stat: "idle" | "loading" | "success" | "error";
  data: D | null;
  error: Error | null; 
}

const defaultConfig = {
  throwOnError: false
}

const defaultInitialState: State<null> = {
  stat: "idle",
  data: null,
  error: null,
};

export const useAsync = <D>(initialState?: State<D>, initialConfig?: typeof defaultConfig) => {
  const config = { ...defaultConfig, ...initialConfig };
  const [state, setState] = useState<State<D>>({
    ...defaultInitialState,
    ...initialState
  });
 
  const setData = (data: D) => setState({
    stat: "success",
    data,
    error: null,
  })

  const setError = (error: Error) => setState({
    stat: "error",
    data: null,
    error,
  })

  // run 用来触发异步请求
  const run = (promise: Promise<D>) => {
    if (!promise || !promise.then) {
      throw new Error("请传入 Promise 类型数据");
    }
    setState({ ...state, stat: "loading" });
    return promise
      .then((data) => {
        setData(data);
        return data;
      })
      .catch((error) => {
        // catch会消化异常，如果不主动抛出，外面是接收不到异常的
        setError(error);
        if (config.throwOnError) return Promise.reject(error);
        return error;
      });
  };

  return {
    isIdle: state.stat === "idle",
    isLoading: state.stat === "loading",
    isSuccess: state.stat === "success",
    isError: state.stat === "error",
    run,
    setData,
    setError,
    ...state,
  };
};
```

src\unauthenticated-app\index.tsx

```tsx
import { useState } from "react";
import { Login } from "./login";
import { Register } from "./register";
import { Card, Divider, Button, Typography } from "antd";
import styled from "@emotion/styled";
import logo from "assets/logo.svg";
import left from "assets/left.svg";
import right from "assets/right.svg";

export const UnauthenticatedApp = () => {
  const [isRegister, setIsRegister] = useState(false);
  const [error, setError] = useState<Error | null>(null);

  return (
    <Container>
      <Header />
      <Background />
      <ShadowCard>
        <Title>{isRegister ? "请注册" : "请登录"}</Title>
        {error ? <Typography.Text type={"danger"}>{error.message}</Typography.Text> : null}
        {isRegister ? <Register onError={setError}/> : <Login onError={setError}/>}
        <Divider />
        <Button type={"link"} onClick={() => { setIsRegister(!isRegister); setError(null); }}>
          {isRegister ? "已经有账号了？直接登录" : "没有账号？注册新账号"}
        </Button>
      </ShadowCard>
    </Container>
  );
};

export const LongButton = styled(Button)`
  width: 100%;
`;

const Container = styled.div`
  display: flex;
  flex-direction: column;
  align-items: center;
  min-height: 100vh;
`;

const Header = styled.header`
  background: url(${logo}) no-repeat center;
  padding: 5rem 0;
  background-size: 8rem;
  width: 100%;
`;

const Background = styled.div`
  position: absolute;
  width: 100%;
  height: 100%;
  background-repeat: no-repeat;
  background-attachment: fixed;
  background-position: left bottom, right bottom;
  background-size: calc(((100vw - 40rem) / 2) - 3.2rem),
    calc(((100vw - 40rem) / 2) - 3.2rem), cover;
  background-image: url(${left}), url(${right});
`;

const ShadowCard = styled(Card)`
  width: 40rem;
  min-height: 56rem;
  padding: 3.2rem 4rem;
  border-radius: 0.3rem;
  box-sizing: border-box;
  box-shadow: rgba(0, 0, 0, 0.1) 0 0 10px;
  text-align: center;
`;

const Title = styled.h2`
  margin-bottom: 2.4rem;
  color: rgb(94, 108, 132);
`;
```

src\unauthenticated-app\login.tsx

```tsx
import { useAuth } from "context/auth-context";
import { Form, Input } from "antd";

import { LongButton } from "unauthenticated-app/index";

import { useAsync } from "../utils/use-async";

export const Login = ({onError}: {onError: (error: Error) => void}) => {
  const { login } = useAuth();
  const { run, isLoading } = useAsync(undefined, { throwOnError: true }); // 异步微任务完成后才会有 error 

  const handleSubmit = async (values: { username: string; password: string }) => {
    try {
      await run(login(values));
      //  捕获的执行是同步的，无法捕获异步微任务里面抛出的 error，造成下面代码第一次 error 为 null
      //  if (error) {
      //    onError(error)
      //  }
    }
    catch(e: any) {
      onError(e)
    }
  };

  return (
    <Form onFinish={handleSubmit}>
      <Form.Item
        name={"username"}
        rules={[{ required: true, message: "请输入用户名" }]}
      >
        <Input placeholder={"用户名"} type="text" id={"username"} />
      </Form.Item>
      <Form.Item
        name={"password"}
        rules={[{ required: true, message: "请输入密码" }]}
      >
        <Input placeholder={"密码"} type="password" id={"password"} />
      </Form.Item>
      <Form.Item>
        <LongButton htmlType={"submit"} type={"primary"} loading={isLoading}>
          登录
        </LongButton>
      </Form.Item>
    </Form>
  );
};
```

src\unauthenticated-app\register.tsx

```tsx
import { useAuth } from "context/auth-context";
import { Form, Input } from "antd";

import { LongButton } from "unauthenticated-app/index";

import { useAsync } from "../utils/use-async";

export const Register = ({onError}: {onError: (error: Error) => void}) => {
  const { register } = useAuth();
  const { run, isLoading } = useAsync(undefined, { throwOnError: true }); 

  const handleSubmit = async ({confirmPassword, ...values}: { username: string; password: string, confirmPassword: string }) => {
    if (confirmPassword !== values.password) {
      onError(new Error("请确认两次输入的密码相同"))
      return;
    } 
    try {
      await run(register(values));
    }
    catch(e: any) {
      onError(e)
    }
  };

  return (
    <Form onFinish={handleSubmit}>
      <Form.Item
        name={"username"}
        rules={[{ required: true, message: "请输入用户名" }]}
      >
        <Input placeholder={"用户名"} type="text" id={"username"} />
      </Form.Item>
      <Form.Item
        name={"password"}
        rules={[{ required: true, message: "请输入密码" }]}
      >
        <Input placeholder={"密码"} type="password" id={"password"} />
      </Form.Item>
      <Form.Item
        name={"confirmPassword"}
        rules={[{ required: true, message: "请确认密码" }]}
      >
        <Input placeholder={"确认密码"} type="password" id={"confirmPassword"} />
      </Form.Item>
      <Form.Item>
        <LongButton htmlType={"submit"} type={"primary"} loading={isLoading}>
          注册
        </LongButton>
      </Form.Item>
    </Form>
  );
};
```

效果图

![img](https://cdn.nlark.com/yuque/0/2021/gif/1614731/1638365989378-f5a5a22f-4232-4cb7-9892-2e682f20be66.gif)

## 获取用户信息的 Loading 和 Error 处理 

src\context\auth-context.tsx

```tsx
import React, { ReactNode } from "react";

import { Users } from "screens/project-list/search-panel";
import * as auth from "./auth-provider";

import { http } from "../utils/http";
import { useMount } from "utils";
import { useAsync } from "../utils/use-async";
import { FullPageErrorFallback, FullPageLoading } from "components/lib";

interface AuthForm {
  username: string;
  password: string;
}

interface AuthContextProps {
  user: Users | null;
  register: (form: AuthForm) => Promise<void>;
  login: (form: AuthForm) => Promise<void>;
  logout: () => Promise<void>;
}

const bootStrapUser = async () => {
  let user = null;
  const token = auth.getToken();
  if (token) {
    const data = await http("me", { token });
    user = data.user;
  }
  return user;
};

const AuthContext = React.createContext<AuthContextProps | undefined>(
  undefined
);
AuthContext.displayName = "AuthContext";

export const AuthProvider = ({ children }: { children: ReactNode }) => {
  const {isIdle, isLoading, isError, run, data: user, setData: setUser, error} = useAsync<Users | null>();

  const register = (form: AuthForm) => auth.register(form).then(setUser);
  const login = (form: AuthForm) => auth.login(form).then(setUser);
  const logout = () => auth.logout().then(() => setUser(null));

  useMount(() => {
    run(bootStrapUser());
  });

  if (isIdle || isLoading) {
    return <FullPageLoading />
  }

  if (isError) {
    return <FullPageErrorFallback error={error}/>
  }

  return (
    <AuthContext.Provider
      children={children}
      value={{ user, register, login, logout }}
    />
  );
};

export const useAuth = () => {
  const context = React.useContext(AuthContext);
  if (!context) {
    throw new Error("userAuth 必须在 AuthProvider 中使用");
  }
  return context;
};
```

src\components\lib.tsx

```tsx
import styled from "@emotion/styled";
import { Spin, Typography } from "antd";
import { DevTools } from "jira-dev-tool";

export const Row = styled.div<{
  gap?: number | boolean;
  between?: boolean;
  marginBottom?: number;
}>`
  display: flex;
  align-items: center;
  justify-content: ${(props) => (props.between ? "space-between" : undefined)};
  margin-bottom: ${(props) => props.marginBottom + "rem"};

  > * {
    margin-top: 0 !important;
    margin-bottom: 0 !important;
    margin-right: ${(props) =>
      typeof props.gap === "number"
        ? props.gap + "rem"
        : props.gap
        ? "2rem"
        : undefined};
  }
`;

const FullPage = styled.div`
  height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
`;

export const FullPageLoading = () => (
  <FullPage>
    <Spin size={"large"} />
  </FullPage>
);

export const FullPageErrorFallback = ({ error }: { error: Error | null }) => (
  <FullPage>
    <Typography.Text type={"danger"}>{error?.message}</Typography.Text>
    <DevTools />
  </FullPage>
);
```

效果图

![img](https://cdn.nlark.com/yuque/0/2021/gif/1614731/1638369345557-a1367d40-ca57-400f-8ce5-a86f72aaf433.gif)

## 错误边界处理渲染阶段的异常

例如当渲染组件所需的数据为空（出现未捕获的错误）时会导致整个组件出错并卸载掉。

推荐的库：https://github.com/bvaughn/react-error-boundary

src\components\error-boundary.tsx

```tsx
import React from "react";
type FallbackRender = (props: { error: Error | null }) => React.ReactElement;

export class ErrorBoundary extends React.Component<
  React.PropsWithChildren<{ fallbackRender: FallbackRender }>,
  { error: Error | null }
> {
  state = { error: null };

  // 当子组件抛出异常，这里会接收到并且调用
  static getDerivedStateFromError(error: Error) {
    return { error };
  }

  render() {
    const { error } = this.state;
    const { fallbackRender, children } = this.props;
    if (error) {
      return fallbackRender({ error });
    }
    return children;
  }
}
```

src\App.tsx

```tsx
import "./App.css";
import { useAuth } from "context/auth-context";
import { AuthenticatedApp } from "authenticated-app";
import { UnauthenticatedApp } from "unauthenticated-app";
import { ErrorBoundary } from "components/error-boundary";
import { FullPageErrorFallback } from "components/lib";

function App() {
  const { user } = useAuth();

  return (
    <div className="App">
      <ErrorBoundary fallbackRender={FullPageErrorFallback}>
        {user ? <AuthenticatedApp /> : <UnauthenticatedApp />}
       </ErrorBoundary>  
    </div>
  );
}

export default App;
```

效果图

![img](https://cdn.nlark.com/yuque/0/2021/gif/1614731/1638371437949-f8c8d3f4-fef3-4d02-99db-3e646eb5bbb0.gif)

## 实现 useDocumentTitle 

<img src="https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1638443642611-007a540c-fd35-41a3-a90d-0cbcb0c8fa0f.jpeg" alt="img" style="zoom:67%;" />

<img src="https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1638443642561-ac62858f-ed1e-4767-9ab1-d5f50dba00cf.jpeg" alt="img" style="zoom:67%;" />

在 useEffect 里 return 函数时会出现闭包，

如果依赖为 []，则每次读取的都是不变的初始值（虽然每次都是新的，但每次初始值不变），

指定正确的依赖即可解决

src\utils\index.ts

```ts
import { useState, useEffect, useRef } from "react";

const isFalsy = (val: unknown) => (val === 0 ? false : !val);

const isVoid = (value: unknown) =>
  value === undefined || value === null || value === "";

// 在一个函数里，改变传入的对象本身是不好的
// 不需要其它 hook，所以不需要改造为自定义 hook
export const cleanObject = (object: { [key: string]: unknown }) => {
  const result = { ...object };
  Object.keys(result).forEach((key) => {
    const value = result[key];
    if (isVoid(value)) {
      delete result[key];
    }
  });
  // 处理后记得返回
  return result;
};

// 如果这里添加 callback 作为依赖项，会出现无限循环，
// 因为 callback 里有 setUsers，每次 setUsers 后，callback 是不同的
export const useMount = (callback: () => void) => {
  useEffect(() => {
    callback();
  }, []);
};

export const useDebounce = <V>(value: V, delay?: number): V => {
  // 为了不影响入参，另外设置一个值为 debounceValue
  // 将 value 转换为处理后的 debounceValue
  const [debounceValue, setDebounceValue] = useState(value);
  useEffect(() => {
    // 每次在 value 变化时设置一个定时器
    const timeout = setTimeout(() => setDebounceValue(value), delay);
    // 每次在上一个 useEffect 处理完后才执行，清除定时器
    return () => clearTimeout(timeout);
  }, [value, delay]);
  // 返回一个新的值
  return debounceValue;
};

// T[] 从入参的数组中提取对象的类型 T
export const useArray = <T>(persons: T[]) => {
  const [values, setValues] = useState(persons);
  return {
    values,
    add: (item: T) => {
      setValues([...values, item]);
    },
    removeIndex: (index: number) => {
      const copy = [...values];
      copy.splice(index, 1);
      setValues(copy);
    },
    clear: () => {
      setValues([]);
    },
  };
};

export const useDocumentTitle = (title: string, keepOnUnMount = true) => {
  const oldTitle = useRef(document.title).current;
  // 首页加载时: oldTitle === 旧title "React App"
  // 进入项目列表后：oldTitle === 新title "项目列表"
  useEffect(() => {
    document.title = title;
  }, [title]);
  
  // 退出登录时，切换 title
  useEffect(() => {
    return () => {
      if (!keepOnUnMount) {
        // 如果不指定依赖，读到的就是旧title
        // 为了代码直观，需要指定依赖并与 useRef 配合，
        // 因为 useRef 在整个生命周期里值都不变，即永远保留旧 title
        document.title = oldTitle;
      }
    };
  }, [keepOnUnMount, oldTitle]);
};
```

与下面代码效果一致：

<img src="https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1638443642612-ea48b978-8e6f-43b8-a5a4-f0322667dd7a.jpeg" alt="img" style="zoom: 67%;" />

src\screens\project-list\index.tsx

```tsx
import { useState } from "react";
import styled from "@emotion/styled";
import { Typography } from "antd";

import { SearchPanel } from "./search-panel";
import { List } from "./list";

import { useDebounce, useDocumentTitle } from "utils";
import { useProjects } from "utils/projects";
import { useUsers } from "utils/users";

export const ProjectList = () => {
  useDocumentTitle("项目列表", false);
  
  const [params, setParams] = useState({
    name: "",
    personId: "",
    // 如果需要模糊搜索需要使用 ?q=，SearchPanel 组件的 input 中设置 q 的值即可
    q: "",
  });

  const debounceParams = useDebounce(params, 1000);
  const { isLoading, error, data: list } = useProjects(debounceParams);
  const { data: users } = useUsers();

  return (
    <Container>
      <h1>项目列表</h1>
      <SearchPanel params={params} setParams={setParams} users={users || []} />
      {error ? (
        <Typography.Text type={"danger"}>{error.message}</Typography.Text>
      ) : null}
      <List users={users || []} dataSource={list || []} loading={isLoading} />
    </Container>
  );
};

const Container = styled.div`
  padding: 3.2rem;
`;
```

src\unauthenticated-app\index.tsx

```tsx
import { useState } from "react";
import { Login } from "./login";
import { Register } from "./register";
import { Card, Divider, Button, Typography } from "antd";
import styled from "@emotion/styled";
import logo from "assets/logo.svg";
import left from "assets/left.svg";
import right from "assets/right.svg";
import { useDocumentTitle } from "utils";

export const UnauthenticatedApp = () => {
  useDocumentTitle("请登录注册以继续");
  
  const [isRegister, setIsRegister] = useState(false);
  const [error, setError] = useState<Error | null>(null);

  return (
    <Container>
      <Header />
      <Background />
      <ShadowCard>
        <Title>{isRegister ? "请注册" : "请登录"}</Title>
        {error ? (
          <Typography.Text type={"danger"}>{error.message}</Typography.Text>
        ) : null}
        {isRegister ? (
          <Register onError={setError} />
        ) : (
          <Login onError={setError} />
        )}
        <Divider />
        <Button
          type={"link"}
          onClick={() => {
            setIsRegister(!isRegister);
            setError(null);
          }}
        >
          {isRegister ? "已经有账号了？直接登录" : "没有账号？注册新账号"}
        </Button>
      </ShadowCard>
    </Container>
  );
};

export const LongButton = styled(Button)`
  width: 100%;
`;

const Container = styled.div`
  display: flex;
  flex-direction: column;
  align-items: center;
  min-height: 100vh;
`;

const Header = styled.header`
  background: url(${logo}) no-repeat center;
  padding: 5rem 0;
  background-size: 8rem;
  width: 100%;
`;

const Background = styled.div`
  position: absolute;
  width: 100%;
  height: 100%;
  background-repeat: no-repeat;
  background-attachment: fixed;
  background-position: left bottom, right bottom;
  background-size: calc(((100vw - 40rem) / 2) - 3.2rem),
    calc(((100vw - 40rem) / 2) - 3.2rem), cover;
  background-image: url(${left}), url(${right});
`;

const ShadowCard = styled(Card)`
  width: 40rem;
  padding: 3.2rem 4rem;
  border-radius: 0.3rem;
  box-sizing: border-box;
  box-shadow: rgba(0, 0, 0, 0.1) 0 0 10px;
  text-align: center;
`;

const Title = styled.h2`
  margin-bottom: 2.4rem;
  color: rgb(94, 108, 132);
`;
```

效果图

![img](https://cdn.nlark.com/yuque/0/2021/gif/1614731/1638443642814-975f8e52-147e-48ef-8b47-e1640b496d2a.gif)

## 添加项目列表路由、项目详情的项目与看板的路由

```sh
yarn add react-router@6 react-router-dom@6 history
```

src\authenticated-app\index.tsx

```tsx
import styled from "@emotion/styled";
import { Dropdown, Menu, Button } from "antd";
import { Navigate, Route, Routes } from "react-router";
import { BrowserRouter as Router } from "react-router-dom";

import { ProjectList } from "screens/project-list";
import { Row } from "components/lib";
import { ReactComponent as SoftwareLogo } from "assets/software-logo.svg";
import { Project } from "screens/project";

import { useAuth } from "context/auth-context";
import { resetRoute } from "utils/index";

export const AuthenticatedApp = () => {
  return (
    <Container>
      <PageHeader/>
      <Main>
        <Router>
          <Routes>
            <Route path={"/projects"} element={<ProjectList/>}></Route>
            <Route path={"/projects/:projectId/*"} element={<Project/>}></Route>
            <Route path={"*"} element={<Navigate to={"/projects"}/>}></Route>
          </Routes>
        </Router>
      </Main>
    </Container>
  );
};

const PageHeader = () => { 
  const { logout, user } = useAuth();
  return <Header between={true}>
    <HeaderLeft gap={true}>
      <Button type={"link"} onClick={resetRoute}>
        <SoftwareLogo width={"18rem"} color={"rgb(38, 132, 255)"} />
      </Button>
      <h2>项目</h2>
      <h2>用户</h2>
    </HeaderLeft>
    <HeaderRight>
      <Dropdown
        overlay={
          <Menu>
            <Menu.Item key={"logout"}>
              <Button onClick={logout} type={"link"}>
                登出
              </Button>
            </Menu.Item>
          </Menu>
        }
      >
        <Button type={"link"} onClick={(e) => e.preventDefault()}>
          Hi, {user?.name}
        </Button>
      </Dropdown>
    </HeaderRight>
  </Header>
}      

const Container = styled.div`
  display: grid;
  grid-template-rows: 6rem 1fr;
  height: 100vh;
`;

// grid-area 用来给grid子元素起名字
const Header = styled(Row)`
  padding: 3.2rem;
  box-shadow: 0 0 5px 0 rgba(0, 0, 0, 0.1);
  z-index: 1;
`;

const HeaderLeft = styled(Row)``;
const HeaderRight = styled.div``;
const Main = styled.main`
  overflow: hidden;
`;
```

src\screens\project-list\list.tsx

```tsx
import { Users } from "./search-panel";
import { Table, TableProps } from "antd";
import dayjs from "dayjs";
import { Link } from "react-router-dom";

export interface Projects {
  id: number;
  name: string;
  personId: number | string;
  organization: string;
  created: number;
}

interface ListProps extends TableProps<Projects> {
  users: Users[];
}

// ...props 的类型 type PropsType = Omit<ListProps, 'users'>
export const List = ({ users, ...props }: ListProps) => {
  return (
    <Table
      pagination={false}
      columns={[
        {
          title: "名称",
          dataIndex: "name",
          // 按中文名排序
          sorter: (a, b) => a.name.localeCompare(b.name),
          render(value, project) {
            return (
              // 跳转显示为 /projects/4 的形式
              <Link to={String(project.id)}>{project.name}</Link>
            );
          },
        },
        {
          title: "部门",
          dataIndex: "organization",
        },
        {
          title: "负责人",
          render(value, project) {
            return (
              <span>
                {users.find((user) => user.id === project.personId)?.name ||
                  "未知"}
              </span>
            );
          },
        },
        {
          title: "创建时间",
          render(value, project) {
            return (
              <span>
                {project.created
                  ? dayjs(project.created).format("YYYY-MM-DD")
                  : "无"}
              </span>
            );
          },
        },
      ]}
      {...props}
    ></Table>
  );
};
```

src\screens\project\index.tsx

```tsx
import { Link } from "react-router-dom";
import { Navigate, Route, Routes } from "react-router";

import { KanBan } from "screens/kanban";
import { Epic } from "screens/epic";

export const Project = () => {
  return <div>
    <h1>项目</h1>
    <Link to={"kanban"}>看板</Link>
    <Link to={"epic"}>任务组</Link>
    <Routes>
      {/* /projects/:id/kanban */}
      <Route path={"/kanban"} element={<KanBan/>}></Route>
      {/* /projects/:id/epic */}
      <Route path={"/epic"} element={<Epic/>}></Route>
      {/* 以上路径都不匹配则使用默认路由，例如打开 /projects/4 时跳转到 /projects/4/kanban */}
      <Route path={"*"} element={<Navigate to={window.location.pathname + "/kanban"} replace={true} />}></Route>
    </Routes>
  </div>  
}
```

src\screens\kanban\index.tsx

```tsx
export const KanBan = () => {
  return <h1>看板</h1> 
}
```

src\screens\epic\index.tsx

```tsx
export const Epic = () => {
  return <h1>任务组</h1>
}
```

效果图

![img](https://cdn.nlark.com/yuque/0/2021/gif/1614731/1638706676549-4d34f3b9-6d5f-46c0-bf08-036277764062.gif)

## 实现 useUrlQueryParam 管理 URL 参数状态

单页应用：切换路由不会加载刷新文档

用 url 进行状态管理，例如 url 为 ?name=快递 ，页面会读取这个参数自动填入输入框

**避免无限循环渲染**

**基本类型，可以放到依赖数组里；组件状态，可以放到依赖数组里；**

**非组件状态的对象，绝不可以放到依赖数组里，会造成无限循环渲染**

<img src="https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1638713411896-cd206e43-21af-4bf7-b6a4-78d2cf420a77.jpeg" alt="img" style="zoom:80%;" />

数组、 对象、Map 都有遍历器，都可以使用 fo...of

遍历器多次使用 next() 类似链表

<img src="https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1638713709853-dab23163-4e0b-4073-b340-da8b5d500ec5.jpeg" alt="img" style="zoom: 80%;" />

<img src="https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1638713709697-c2ec12d9-d930-4610-9348-e6db63b4d508.jpeg" alt="img" style="zoom: 80%;" />

对象模拟遍历器

<img src="https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1638713411911-d2dc284e-5e57-439c-a0f9-2f31934fd855.jpeg" alt="img" style="zoom: 67%;" />

[Symbol.iterator] 验证是否有遍历器，

Object.fromEntries 将键值对列表（ Array 、 Map 或者其它实现了遍历器的可迭代对象）转为对象

<img src="https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1638772868006-ea16177f-9058-43b0-a503-c372b757d1a5.jpeg" alt="img" style="zoom:80%;" />

src\utils\url.ts

```ts
import { URLSearchParamsInit, useSearchParams } from "react-router-dom";
import { useMemo } from "react";
import { cleanObject } from "utils/index";

/**
 * 返回页面url中，指定键的参数值
 */
// 泛型解决 返回 {name: string; personId: string} ，
// 泛型将传入的数组推断为 <"name" | "personId">(keys: ("name" | "personId")[])
export const useUrlQueryParam = <K extends string>(keys: K[]) => {
  const [searchParams, setSearchParams] = useSearchParams();
  // useSearchParams 相当于 useState() , react 只在 setState() 时才会去改变 state 的值
  // useMemo 解决依赖不同、无限循环渲染的问题，只在 searchParams 改变时才重新计算运行 reduce
  // reduce 返回的是函数最终累计处理的一个值，而不是数组
  // as const 将数组转为最原始的类型，
  // 例如 ["12"] ts 推断为 string[]，["12"] as const 解释为 readonly["12"]
  return [
    useMemo(
      () => keys.reduce((prev, key) => {
        return {...prev, [key]: searchParams.get(key) || ""} 
      }, {} as {[key in K]: string}), 
      [searchParams]
    ),
    // 传入的对象键值必须是 K 其中一个，URLSearchParamsInit 为 setSearchParams 的参数类型
    (params: Partial<{[key in K]: unknown}>) => {
      const o = cleanObject({
        ...Object.fromEntries(searchParams),
        ...params,
      }) as URLSearchParamsInit;
      return setSearchParams(o);
    }
  ] as const;
};
```

src\screens\project-list\index.tsx

```tsx
import { useState } from "react";
import styled from "@emotion/styled";
import { Typography } from "antd";

import { SearchPanel } from "./search-panel";
import { List } from "./list";

import { useDebounce, useDocumentTitle } from "utils";
import { useProjects } from "utils/projects";
import { useUsers } from "utils/users";
import { useUrlQueryParam } from "utils/url";

export const ProjectList = () => {
  const [params, setParams] = useUrlQueryParam(["name", "personId"]);
  const debounceParams = useDebounce(params, 1000);
  const { isLoading, error, data: list } = useProjects(debounceParams);
  const { data: users } = useUsers();
  useDocumentTitle("项目列表", false);

  return (
    <Container>
      <h1>项目列表</h1>
      <SearchPanel params={params} setParams={setParams} users={users || []} />
      {error ? (
        <Typography.Text type={"danger"}>{error.message}</Typography.Text>
      ) : null}
      <List users={users || []} dataSource={list || []} loading={isLoading} />
    </Container>
  );
};

const Container = styled.div`
  padding: 3.2rem;
`;
```

src\screens\project-list\search-panel.tsx

```tsx
import { Input, Select, Form } from "antd";

export interface Users {
  id: number;
  name: string;
  token: string;
}

interface SearchPanelProps {
  params: {
    name: string;
    personId: string;
  };
  setParams: (params: SearchPanelProps["params"]) => void;
  users: Users[];
}

export const SearchPanel = ({ params, setParams, users }: SearchPanelProps) => {
  return (
    <Form style={{ marginBottom: "2rem" }} layout={"inline"}>
      <Form.Item>
        <Input
          type="text"
          value={params.name}
          onChange={(e) =>
            setParams({
              ...params,
              name: e.target.value,
            })
          }
        />
      </Form.Item>
      <Form.Item>
        <Select
          value={params.personId}
          onChange={(value) =>
            setParams({
              ...params,
              personId: value,
            })
          }
        >
          <Select.Option value="">负责人</Select.Option>
          {users.map((user) => {
            return (
              <Select.Option key={user.id} value={String(user.id)}>
                {user.name}
              </Select.Option>
            );
          })}
        </Select>
      </Form.Item>
    </Form>
  );
};
```

效果图

![img](https://cdn.nlark.com/yuque/0/2021/gif/1614731/1638713108453-7b429d30-93ba-4a0d-bf0c-177122d49327.gif)

## 实现 user-select 组件解决 id 问题

解决 id 类型，服务返回的 id Number 类型， Select 组件转为 String 的问题

空值则匹配为默认选项

src\types\index.ts

```ts
export type Raw = string | number;
```

src\types\project.ts

```ts
export interface Project {
  id: number;
  name: string;
  personId: number;
  pin: boolean;
  organization: string;
  created: number;
}
```

src\components\id-select.tsx

```tsx
import React from "react";
import { Raw } from "types";
import { Select } from "antd";

// 拿到 antd Select 组件的类型
type SelectProps = React.ComponentProps<typeof Select>;

// 透传用户给组件传的剩余的 props
interface IdSelectProps extends Omit<SelectProps, "value" | "onChange" | "options"> {
  value?: Raw | null | undefined;
  onChange?: (value?: number) => void;
  defaultOptionName?: string;
  options?: { name: string; id: number }[];
}

/**
 * value 可以传入多种类型的值
 * onChange只会回调 number|undefined 类型
 * 当 isNaN(Number(value)) 为true的时候，代表选择默认类型
 * 当选择默认类型的时候，onChange会回调undefined
 * @param props
 * @constructor
 */
export const IdSelect = (props: IdSelectProps) => {
  const { value, onChange, defaultOptionName, options, ...restProps } = props;
  return (
    <Select
      value={options?.length ? toNumber(value) : 0}
      onChange={(value) => onChange?.(toNumber(value) || undefined)}
      {...restProps}
    >
      {defaultOptionName ? (
        <Select.Option value={0}>{defaultOptionName}</Select.Option>
      ) : null}
      {options?.map((option) => (
        <Select.Option key={option.id} value={option.id}>
          {option.name}
        </Select.Option>
      ))}
    </Select>
  );
};

const toNumber = (value: unknown) => (isNaN(Number(value)) ? 0 : Number(value));
```

src\components\user-select.tsx

```tsx
import { useUsers } from "utils/users";
import { IdSelect } from "components/id-select";

export const UserSelect = (props: React.ComponentProps<typeof IdSelect>) => {
  const { data: users } = useUsers();
  return <IdSelect options={users || []} {...props} />;
};
```

src\screens\project-list\search-panel.tsx

```tsx
import { Input, Form } from "antd";
import { Project } from "types/project";
import { UserSelect } from "components/user-select";

export interface Users {
  id: number;
  name: string;
  token: string;
}

interface SearchPanelProps {
  params: Partial<Pick<Project, "name" | "personId">>;
  setParams: (params: SearchPanelProps["params"]) => void;
  users: Users[];
}

export const SearchPanel = ({ params, setParams, users }: SearchPanelProps) => {
  return (
    <Form style={{ marginBottom: "2rem" }} layout={"inline"}>
      <Form.Item>
        <Input
          type="text"
          value={params.name}
          onChange={(e) =>
            setParams({
              ...params,
              name: e.target.value,
            })
          }
        />
      </Form.Item>
      <Form.Item>
        <UserSelect
          defaultOptionName={"负责人"}
          value={params.personId}
          onChange={(value) =>
            setParams({
              ...params,
              personId: value,
            })
          }
        />
      </Form.Item>
    </Form>
  );
};
```

src\screens\project-list\list.tsx

```tsx
import { Users } from "./search-panel";
import { Table, TableProps } from "antd";
import dayjs from "dayjs";
import { Link } from "react-router-dom";

export interface Projects {
  id: number;
  name: string;
  personId: number;
  organization: string;
  created: number;
}

interface ListProps extends TableProps<Projects> {
  users: Users[];
}

// ...props 的类型 type PropsType = Omit<ListProps, 'users'>
export const List = ({ users, ...props }: ListProps) => {
  return (
    <Table
      pagination={false}
      columns={[
        {
          title: "名称",
          dataIndex: "name",
          // 按中文名排序
          sorter: (a, b) => a.name.localeCompare(b.name),
          render(value, project) {
            return (
              // 跳转显示为 /projects/4 的形式
              <Link to={String(project.id)}>{project.name}</Link>
            );
          },
        },
        {
          title: "部门",
          dataIndex: "organization",
        },
        {
          title: "负责人",
          render(value, project) {
            return (
              <span>
                {users.find((user) => user.id === project.personId)?.name ||
                  "未知"}
              </span>
            );
          },
        },
        {
          title: "创建时间",
          render(value, project) {
            return (
              <span>
                {project.created
                  ? dayjs(project.created).format("YYYY-MM-DD")
                  : "无"}
              </span>
            );
          },
        },
      ]}
      {...props}
    ></Table>
  );
};
```

src\screens\project-list\util.ts

```ts
import { useUrlQueryParam } from "utils/url";
import { useMemo } from "react";

// 项目列表搜索的参数
export const useProjectsSearchParams = () => {
  const [params, setParams] = useUrlQueryParam(["name", "personId"]);
  return [
    useMemo(
      () => ({ ...params, personId: Number(params.personId) || undefined }),
      [params]
    ),
    setParams,
  ] as const;
};
```

src\screens\project-list\index.tsx

```tsx
import styled from "@emotion/styled";
import { Typography } from "antd";

import { SearchPanel } from "./search-panel";
import { List } from "./list";

import { useDebounce, useDocumentTitle } from "utils";
import { useProjects } from "utils/projects";
import { useUsers } from "utils/users";
import { useProjectsSearchParams } from "screens/project-list/util";

export const ProjectList = () => {
  useDocumentTitle("项目列表", false);
  const [params, setParams] = useProjectsSearchParams();
  const { isLoading, error, data: list } = useProjects(useDebounce(params, 1000));
  const { data: users } = useUsers();

  return (
    <Container>
      <h1>项目列表</h1>
      <SearchPanel params={params} setParams={setParams} users={users || []} />
      {error ? (
        <Typography.Text type={"danger"}>{error.message}</Typography.Text>
      ) : null}
      <List users={users || []} dataSource={list || []} loading={isLoading} />
    </Container>
  );
};

const Container = styled.div`
  padding: 3.2rem;
`;
```

## 使用 useEditProject 编辑数据 与 retry 重新请求刷新数据

src\components\pin.tsx

```tsx
import { Rate } from "antd";

interface PinProps extends React.ComponentProps<typeof Rate> {
  checked: boolean;
  onCheckedChange?: (checked: boolean) => void;
}

export const Pin = ({ checked, onCheckedChange, ...restProps }: PinProps) => {
  return (
    <Rate
      count={1}
      value={checked ? 1 : 0}
      onChange={(num) => onCheckedChange?.(!!num)}
      {...restProps}
    />
  );
};
```

src\utils\projects.ts

```ts
import { useEffect } from "react";

import { Projects } from "screens/project-list/list";

import { useHttp } from "./http";
import { cleanObject } from "./index";
import { useAsync } from "./use-async";

import { Project } from "types/project";

export const useProjects = (param?: Partial<Projects>) => {
  const client = useHttp();
  const { run, ...result } = useAsync<Projects[]>();

  useEffect(() => {
    run(client("projects", { data: cleanObject(param || {}) }));
  }, [param]);

  return result;
};

export const useEditProject = () => {
  const {run, ...asyncResult} = useAsync();
  const client = useHttp();
  const mutate =  (params: Partial<Project>) => {
    return run(client(`projects/${params.id}`, {
      data: params,
      method: "PATCH",
    }))
  };
  return {
    mutate,
    ...asyncResult
  }    
};

export const useAddProject = () => {
  const {run, ...asyncResult} = useAsync();
  const client = useHttp();
  const mutate =  (params: Partial<Project>) => {
    return run(client(`projects/${params.id}`, {
      data: params,
      method: "POST",
    }))
  };
  return {
    mutate,
    ...asyncResult
  }    
};
```

src\screens\project-list\list.tsx

```tsx
import { Users } from "./search-panel";
import { Table, TableProps } from "antd";
import dayjs from "dayjs";
import { Link } from "react-router-dom";
import { Pin } from "components/pin";
import { useEditProject } from "utils/projects";

export interface Projects {
  id: number;
  name: string;
  personId: number;
  pin: boolean;
  organization: string;
  created: number;
}

interface ListProps extends TableProps<Projects> {
  users: Users[];
}

// ...props 的类型 type PropsType = Omit<ListProps, 'users'>
export const List = ({ users, ...props }: ListProps) => {
  // Hook 需要放在组件的顶层，不能在其它位置，
  // 如果有些东西要放在其它位置（例如下面作为事件处理函数），需要使用解构
  const { mutate } = useEditProject();
  // 柯里化风格
  const pinProject = (id: number) => (pin: boolean) => mutate({ id, pin });
  return (
    <Table
      pagination={false}
      columns={[
        {
          title: <Pin checked={true} disabled={true} />,
          render(value, project) {
            return (
              <Pin
                checked={project.pin}
                onCheckedChange={pinProject(project.id)}
              />
            );
          },
        },
        {
          title: "名称",
          dataIndex: "name",
          // 按中文名排序
          sorter: (a, b) => a.name.localeCompare(b.name),
          render(value, project) {
            return (
              // 跳转显示为 /projects/4 的形式
              <Link to={String(project.id)}>{project.name}</Link>
            );
          },
        },
        {
          title: "部门",
          dataIndex: "organization",
        },
        {
          title: "负责人",
          render(value, project) {
            return (
              <span>
                {users.find((user) => user.id === project.personId)?.name ||
                  "未知"}
              </span>
            );
          },
        },
        {
          title: "创建时间",
          render(value, project) {
            return (
              <span>
                {project.created
                  ? dayjs(project.created).format("YYYY-MM-DD")
                  : "无"}
              </span>
            );
          },
        },
      ]}
      {...props}
    ></Table>
  );
};
```

点击星标收藏/取消收藏后，因为无法获取服务器最新的数据，页面无法刷新。

使用 retry 解决。

src\components\pin.tsx

```tsx
import { Rate } from "antd";

interface PinProps extends React.ComponentProps<typeof Rate> {
  checked: boolean;
  onCheckedChange?: (checked: boolean) => void;
}

export const Pin = ({ checked, onCheckedChange, ...restProps }: PinProps) => {
  return (
    <Rate
      count={1}
      value={checked ? 1 : 0}
      onChange={(num) => onCheckedChange?.(!!num)}
      {...restProps}
    />
  );
};
```

src\screens\project-list\index.tsx

```tsx
import styled from "@emotion/styled";
import { Typography } from "antd";

import { SearchPanel } from "./search-panel";
import { List } from "./list";

import { useDebounce, useDocumentTitle } from "utils";
import { useProjects } from "utils/projects";
import { useUsers } from "utils/users";
import { useProjectsSearchParams } from "screens/project-list/util";

export const ProjectList = () => {
  useDocumentTitle("项目列表", false);
  const [params, setParams] = useProjectsSearchParams();
  const {
    isLoading,
    error,
    data: list,
    retry
  } = useProjects(useDebounce(params, 1000));
  const { data: users } = useUsers();

  return (
    <Container>
      <h1>项目列表</h1>
      <SearchPanel params={params} setParams={setParams} users={users || []} />
      {error ? (
        <Typography.Text type={"danger"}>{error.message}</Typography.Text>
      ) : null}
      <List refresh={retry} users={users || []} dataSource={list || []} loading={isLoading} />
    </Container>
  );
};

const Container = styled.div`
  padding: 3.2rem;
`;
```

这样写，run 内部无法得到整个函数，得到的是 new Promise 实例

<img src="https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1638975568658-d2bae639-58b2-4cc9-aa91-dc77ae8f28c9.jpeg" alt="img" style="zoom: 67%;" />

<img src="https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1638975594431-25f1134b-108e-41f4-b746-ba45392f0389.jpeg" alt="img" style="zoom: 67%;" />

src\utils\projects.ts

```ts
import { useEffect } from "react";

import { Projects } from "screens/project-list/list";

import { useHttp } from "./http";
import { cleanObject } from "./index";
import { useAsync } from "./use-async";

import { Project } from "types/project";

export const useProjects = (param?: Partial<Projects>) => {
  const client = useHttp();
  const { run, ...result } = useAsync<Projects[]>();
  const fetchProjects = () => client("projects", { data: cleanObject(param || {}) })

  useEffect(() => {
    run(fetchProjects(), {
      retry: fetchProjects
    });
  }, [param]);

  return result;
};

export const useEditProject = () => {
  const {run, ...asyncResult} = useAsync();
  const client = useHttp();
  const mutate =  (params: Partial<Project>) => {
    return run(client(`projects/${params.id}`, {
      data: params,
      method: "PATCH",
    }))
  };
  return {
    mutate,
    ...asyncResult
  }    
};

export const useAddProject = () => {
  const {run, ...asyncResult} = useAsync();
  const client = useHttp();
  const mutate =  (params: Partial<Project>) => {
    return run(client(`projects/${params.id}`, {
      data: params,
      method: "POST",
    }))
  };
  return {
    mutate,
    ...asyncResult
  }    
};
```

useState 直接传入函数相当于惰性初始化，容易产生无限循环渲染的问题。

ProjectList 组件触发 useProjects ，useProjects 在 useEffect 中触发 useAsync 的 run 函数，

每次初始化，都会进行一次惰性初始化，导致 setRetry 的函数体自动执行一次。

src\utils\use-async.ts

```ts
import { useState } from "react";

interface State<D> {
  stat: "idle" | "loading" | "success" | "error";
  data: D | null;
  error: Error | null;
}

const defaultConfig = {
  throwOnError: false,
};

const defaultInitialState: State<null> = {
  stat: "idle",
  data: null,
  error: null,
};

export const useAsync = <D>(
  initialState?: State<D>,
  initialConfig?: typeof defaultConfig
) => {
  const config = { ...defaultConfig, ...initialConfig };
  const [state, setState] = useState<State<D>>({
    ...defaultInitialState,
    ...initialState,
  });
  // useState 记住每一次的请求，每次刷新都留有上一次的
  const [retry, setRetry] = useState(() => () => {});

  const setData = (data: D) =>
    setState({
      stat: "success",
      data,
      error: null,
    });

  const setError = (error: Error) =>
    setState({
      stat: "error",
      data: null,
      error,
    });

  // run 用来触发异步请求
  const run = (promise: Promise<D>, runConfig?: { retry: () => Promise<D>}) => {
    if (!promise || !promise.then) {
      throw new Error("请传入 Promise 类型数据");
    }
    setRetry(() => () => {
      if (runConfig?.retry) {
        run(runConfig?.retry(), runConfig);
      }
    });
    setState({ ...state, stat: "loading" });
    return promise
      .then((data) => {
        setData(data);
        return data;
      })
      .catch((error) => {
        // catch会消化异常，如果不主动抛出，外面是接收不到异常的
        setError(error);
        if (config.throwOnError) return Promise.reject(error);
        return error;
      });
  };

  return {
    isIdle: state.stat === "idle",
    isLoading: state.stat === "loading",
    isSuccess: state.stat === "success",
    isError: state.stat === "error",
    run,
    setData,
    setError,
    // retry 被调用时重新跑一遍run，让state刷新一遍
    retry,
    ...state,
  };
};
```

src\screens\project-list\list.tsx

```tsx
import { Users } from "./search-panel";
import { Table, TableProps } from "antd";
import dayjs from "dayjs";
import { Link } from "react-router-dom";
import { Pin } from "components/pin";
import { useEditProject } from "utils/projects";

export interface Projects {
  id: number;
  name: string;
  personId: number;
  pin: boolean;
  organization: string;
  created: number;
}

interface ListProps extends TableProps<Projects> {
  users: Users[];
  refresh: () => void;
}

// ...props 的类型 type PropsType = Omit<ListProps, 'users'>
export const List = ({ users, ...props }: ListProps) => {
  const { mutate } = useEditProject();
  const pinProject = (id: number) => (pin: boolean) => mutate({ id, pin }).then(props.refresh);
  return (
    <Table
      pagination={false}
      columns={[
        {
          title: <Pin checked={true} disabled={true} />,
          render(value, project) {
            return (
              <Pin
                checked={project.pin}
                onCheckedChange={pinProject(project.id)}
              />
            );
          },
        },
        {
          title: "名称",
          dataIndex: "name",
          // 按中文名排序
          sorter: (a, b) => a.name.localeCompare(b.name),
          render(value, project) {
            return (
              // 跳转显示为 /projects/4 的形式
              <Link to={String(project.id)}>{project.name}</Link>
            );
          },
        },
        {
          title: "部门",
          dataIndex: "organization",
        },
        {
          title: "负责人",
          render(value, project) {
            return (
              <span>
                {users.find((user) => user.id === project.personId)?.name ||
                  "未知"}
              </span>
            );
          },
        },
        {
          title: "创建时间",
          render(value, project) {
            return (
              <span>
                {project.created
                  ? dayjs(project.created).format("YYYY-MM-DD")
                  : "无"}
              </span>
            );
          },
        },
      ]}
      {...props}
    ></Table>
  );
};
```

效果图

![img](https://cdn.nlark.com/yuque/0/2021/gif/1614731/1638975495838-3a50d07d-38de-414c-8164-e5a6a319a4cd.gif)

惰性初始化：

组件初始化时会自动执行一次 someExpenscieComputation(props) ，执行的结果 initialState 会赋给 state

<img src="https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1638967412465-606b0c2f-8c83-4a2a-9338-25379dcb7495.jpeg" alt="img" style="zoom: 67%;" />

刷新后初始化时自动弹出（函数被自动执行了）：

<img src="https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1638967412452-a2d7f613-d870-4c01-a62b-d8dd01bc16f9.jpeg" alt="img" style="zoom:67%;" />

点击 setCallback 时弹出（函数被自动执行了）：

<img src="https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1638967412494-940866e6-f299-41f2-98a9-586a7a3650fb.jpeg" alt="img" style="zoom:67%;" />

如果要使用 useState 保存函数，为了解决这个问题，可以利用自动执行的特点来返回一个函数

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1638967412667-7a921142-624f-4a11-89e2-ac86c12b98d0.jpeg)

初始化时不会有弹出框，点击 setCallback 也不会有弹出框，点击 call callback 后才弹出：

<img src="https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1638968470935-897eb663-fc3e-406c-ae81-5ba0a025988b.jpeg" alt="img" style="zoom: 67%;" />

也可以使用 useRef 保存函数，

要注意的是： useRef 并不会像 useState() 那样触发组件重新渲染，需要重新 set 后再重新调用：

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1638968800835-73093314-5d90-4488-8465-a83490bed280.jpeg)

## useCallback 优化异步请求

列表请求没完成就登出，会报警告：阻止在已卸载组件上请求数据后赋值，使用 useMountedRef 解决

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1639403140067-b0cb3264-5b95-4187-8a6d-8c4710a3ac23.jpeg)

src\utils\index.ts

```ts
import { useState, useEffect, useRef } from "react";

const isFalsy = (val: unknown) => (val === 0 ? false : !val);

const isVoid = (value: unknown) =>
  value === undefined || value === null || value === "";

// 在一个函数里，改变传入的对象本身是不好的
// 不需要其它 hook，所以不需要改造为自定义 hook
export const cleanObject = (object: { [key: string]: unknown }) => {
  const result = { ...object };
  Object.keys(result).forEach((key) => {
    const value = result[key];
    if (isVoid(value)) {
      delete result[key];
    }
  });
  // 处理后记得返回
  return result;
};

// 如果这里添加 callback 作为依赖项，会出现无限循环，
// 因为 callback 里有 setUsers，每次 setUsers 后，callback 是不同的
export const useMount = (callback: () => void) => {
  useEffect(() => {
    callback();
  }, []);
};

export const useDebounce = <V>(value: V, delay?: number): V => {
  // 为了不影响入参，另外设置一个值为 debounceValue
  // 将 value 转换为处理后的 debounceValue
  const [debounceValue, setDebounceValue] = useState(value);
  useEffect(() => {
    // 每次在 value 变化时设置一个定时器
    const timeout = setTimeout(() => setDebounceValue(value), delay);
    // 每次在上一个 useEffect 处理完后才执行，清除定时器
    return () => clearTimeout(timeout);
  }, [value, delay]);
  // 返回一个新的值
  return debounceValue;
};

// T[] 从入参的数组中提取对象的类型 T
export const useArray = <T>(persons: T[]) => {
  const [values, setValues] = useState(persons);
  return {
    values,
    add: (item: T) => {
      setValues([...values, item]);
    },
    removeIndex: (index: number) => {
      const copy = [...values];
      copy.splice(index, 1);
      setValues(copy);
    },
    clear: () => {
      setValues([]);
    },
  };
};

export const useDocumentTitle = (title: string, keepOnUnMount = true) => {
  const oldTitle = useRef(document.title).current;
  // 首页加载时: oldTitle === 旧title "React App"
  // 进入项目列表后：oldTitle === 新title "项目列表"
  useEffect(() => {
    document.title = title;
  }, [title]);

  // 退出登录时，切换 title
  useEffect(() => {
    return () => {
      if (!keepOnUnMount) {
        // 如果不指定依赖，读到的就是旧title
        // 为了代码直观，需要指定依赖并与 useRef 配合，因为 useRef 在整个生命周期里值都不变，即永远保留旧 title
        document.title = oldTitle;
      }
    };
  }, [keepOnUnMount, oldTitle]);
};

export const resetRoute = () => (window.location.href = window.location.origin);

/**
 * 传入一个对象，和键集合，返回对应的对象中的键值对
 * @param obj
 * @param keys
 */
export const subset = <
  O extends { [key in string]: unknown },
  K extends keyof O
>(
  obj: O,
  keys: K[]
) => {
  const filteredEntries = Object.entries(obj).filter(([key]) =>
    keys.includes(key as K)
  );
  return Object.fromEntries(filteredEntries) as Pick<O, K>;
};

/**
 * 返回组件的挂载状态，如果还没挂载或者已经卸载，返回false；反之，返回true
 */
export const useMountedRef = () => {
  const mountedRef = useRef(false);

  useEffect(() => {
    mountedRef.current = true;
    return () => {
      mountedRef.current = false;
    };
  });

  return mountedRef;
};
```

src\utils\use-async.ts

在 useEffect 中使用如下注释，可以忽略依赖警告

```ts
// eslint-disable-next-line react-hooks/exhaustive-deps
```

但是，可以使用 useCallback 和 useMemo 优化（useEffect 中非基本类型的依赖），

解决 useEffect 无限循环渲染问题，不需要用注释来压制 eslint 的规则。

因为在 useCallback 的依赖中包括了 state，而函数里又有 setState 时导致了无循环渲染，

所以需要使用 setState 的函数式更新，改为 prevState，useCallback 的依赖去掉 state

```ts
import { useMountedRef } from './index';
import { useState, useCallback } from "react";

interface State<D> {
  stat: "idle" | "loading" | "success" | "error";
  data: D | null;
  error: Error | null;
}

const defaultConfig = {
  throwOnError: false,
};

const defaultInitialState: State<null> = {
  stat: "idle",
  data: null,
  error: null,
};

export const useAsync = <D>(
  initialState?: State<D>,
  initialConfig?: typeof defaultConfig
) => {
  const config = { ...defaultConfig, ...initialConfig };
  const [state, setState] = useState<State<D>>({
    ...defaultInitialState,
    ...initialState,
  });
  const mountedRef = useMountedRef();
  // useState 记住每一次的请求，每次刷新都留有上一次的
  const [retry, setRetry] = useState(() => () => {});

  const setData = useCallback((data: D) =>
    setState({
      stat: "success",
      data,
      error: null,
    }), []);

  const setError = useCallback((error: Error) =>
    setState({
      stat: "error",
      data: null,
      error,
    }), []);

  // run 用来触发异步请求
  const run = useCallback((
    promise: Promise<D>,
    runConfig?: { retry: () => Promise<D> }
  ) => {
    if (!promise || !promise.then) {
      throw new Error("请传入 Promise 类型数据");
    }
    setRetry(() => () => {
      if (runConfig?.retry) {
        run(runConfig?.retry(), runConfig);
      }
    });
    setState(prevState => ({ ...prevState, stat: "loading" }));
    return promise
      .then((data) => {
        if (mountedRef.current) setData(data);
        return data;
      })
      .catch((error) => {
        // catch会消化异常，如果不主动抛出，外面是接收不到异常的
        setError(error);
        if (config.throwOnError) return Promise.reject(error);
        return error;
      });
  }, [config.throwOnError, mountedRef, setData, setError]);

  return {
    isIdle: state.stat === "idle",
    isLoading: state.stat === "loading",
    isSuccess: state.stat === "success",
    isError: state.stat === "error",
    run,
    setData,
    setError,
    // retry 被调用时重新跑一遍run，让state刷新一遍
    retry,
    ...state,
  };
};
```

src\utils\projects.ts

```ts
import { useCallback, useEffect } from "react";

import { Projects } from "screens/project-list/list";

import { useHttp } from "./http";
import { cleanObject } from "./index";
import { useAsync } from "./use-async";

import { Project } from "types/project";

export const useProjects = (param?: Partial<Projects>) => {
  const client = useHttp();
  const { run, ...result } = useAsync<Projects[]>();
  const fetchProjects = useCallback(
    () => client("projects", { data: cleanObject(param || {}) }), 
    [client, param]);

  useEffect(() => {
    run(fetchProjects(), {
      retry: fetchProjects,
    });
  }, [param, run, fetchProjects]);

  return result;
};

export const useEditProject = () => {
  const { run, ...asyncResult } = useAsync();
  const client = useHttp();
  const mutate = (params: Partial<Project>) => {
    return run(
      client(`projects/${params.id}`, {
        data: params,
        method: "PATCH",
      })
    );
  };
  return {
    mutate,
    ...asyncResult,
  };
};

export const useAddProject = () => {
  const { run, ...asyncResult } = useAsync();
  const client = useHttp();
  const mutate = (params: Partial<Project>) => {
    return run(
      client(`projects/${params.id}`, {
        data: params,
        method: "POST",
      })
    );
  };
  return {
    mutate,
    ...asyncResult,
  };
};
```

src\utils\http.ts

```ts
import * as qs from "qs";
import * as auth from "context/auth-provider";
import { useAuth } from "context/auth-context";
import { useCallback } from "react";

const apiUrl = process.env.REACT_APP_API_URL;

interface Config extends RequestInit {
  data?: object;
  token?: string;
}

// axios 和 fetch 的表现不一样，axios 可以直接在返回状态不为 2xx 的时候抛出异常
export const http = async (
  endpoint: string,
  { data, headers, token, ...customConfig }: Config = {}
) => {
  const config = {
    method: "GET",
    headers: {
      Authorization: token ? `Bearer ${token}` : "",
      "Content-Type": data ? "application/json" : "",
    },
    ...customConfig,
  };

  if (config.method.toUpperCase() === "GET") {
    endpoint += `?${qs.stringify(data)}`;
  } else {
    config.body = JSON.stringify(data || {});
  }

  return window
    .fetch(`${apiUrl}/${endpoint}`, config)
    .then(async (response) => {
      if (response.status === 401) {
        await auth.logout();
        window.location.reload();
        return Promise.reject({ message: "请重新登录" });
      }
      const data = await response.json();
      if (response.ok) {
        return data;
      }
      // fetch 需要 Promise.reject 来捕获异常，无法通过 .catch 捕获异常
      else {
        return Promise.reject(data);
      }
    });
};

export const useHttp = () => {
  const { user } = useAuth();
  return useCallback(
    (...[endpoint, config]: Parameters<typeof http>) =>
    http(endpoint, { ...config, token: user?.token }),
    [user?.token]
  );
};
```

## 创建项目，状态提升，组合组件与控制反转

src\components\lib.tsx

```tsx
import styled from "@emotion/styled";
import { Button, Spin, Typography } from "antd";
import { DevTools } from "jira-dev-tool";

export const Row = styled.div<{
  gap?: number | boolean;
  between?: boolean;
  marginBottom?: number;
}>`
  display: flex;
  align-items: center;
  justify-content: ${(props) => (props.between ? "space-between" : undefined)};
  margin-bottom: ${(props) => props.marginBottom + "rem"};

  > * {
    margin-top: 0 !important;
    margin-bottom: 0 !important;
    margin-right: ${(props) =>
      typeof props.gap === "number"
        ? props.gap + "rem"
        : props.gap
        ? "2rem"
        : undefined};
  }
`;

const FullPage = styled.div`
  height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
`;

export const FullPageLoading = () => (
  <FullPage>
    <Spin size={"large"} />
  </FullPage>
);

export const FullPageErrorFallback = ({ error }: { error: Error | null }) => (
  <FullPage>
    <Typography.Text type={"danger"}>{error?.message}</Typography.Text>
    <DevTools />
  </FullPage>
);

export const ButtonNoPadding = styled(Button)`
  padding: 0;
`;
```

src\components\project-popover.tsx

```tsx
import { Divider, List, Popover, Typography } from "antd";
import { useProjects } from "utils/projects";
import styled from "@emotion/styled";
import { ButtonNoPadding } from "components/lib";

export const ProjectPopover = (props: {
  setProjectModalOpen: (isOpen: boolean) => void;
}) => {
  const { data: projects } = useProjects();
  const pinnedProjects = projects?.filter((project) => project.pin);

  const content = (
    <ContentContainer>
      <Typography.Text type={"secondary"}>收藏项目</Typography.Text>
      <List>
        {pinnedProjects?.map((project) => (
          <List.Item>
            <List.Item.Meta title={project.name} />
          </List.Item>
        ))}
      </List>
      <Divider />
      <ButtonNoPadding
        onClick={() => props.setProjectModalOpen(true)}
        type={"link"}
      >
        创建项目
      </ButtonNoPadding>
    </ContentContainer>
  );

  return (
    <Popover placement={"bottom"} content={content}>
      <span style={{cursor: "pointer"}}>项目</span>
    </Popover>
  );
};

const ContentContainer = styled.div`
  min-width: 30rem;
`;
```

src\screens\project-list\project-modal.tsx

```tsx
import { Button, Drawer } from "antd";

export const ProjectModal = (props: {
  projectModalOpen: boolean;
  onClose: () => void;
}) => {
  return (
    <Drawer
      onClose={props.onClose}
      visible={props.projectModalOpen}
      width={"100%"}
    >
      <h1>Project Modal</h1>
      <Button onClick={props.onClose}>关闭</Button>
    </Drawer>
  );
};
```

src\authenticated-app\index.tsx

projectModalOpen 状态提升到这（最近的公共父组件）

```tsx
import { useState } from "react";
import styled from "@emotion/styled";
import { Dropdown, Menu, Button } from "antd";
import { Navigate, Route, Routes } from "react-router";
import { BrowserRouter as Router } from "react-router-dom";

import { ProjectList } from "screens/project-list";
import { ButtonNoPadding, Row } from "components/lib";
import { ReactComponent as SoftwareLogo } from "assets/software-logo.svg";
import { Project } from "screens/project";
import { ProjectModal } from "screens/project-list/project-modal";
import { ProjectPopover } from "components/project-popover";

import { useAuth } from "context/auth-context";
import { resetRoute } from "utils/index";

export const AuthenticatedApp = () => {
  const [projectModalOpen, setProjectModalOpen] = useState(false);

  return (
    <Container>
      <PageHeader setProjectModalOpen={setProjectModalOpen}/>
      <Main>
        <Router>
          <Routes>
            <Route path={"/projects"} element={<ProjectList setProjectModalOpen={setProjectModalOpen}/>}></Route>
            <Route
              path={"/projects/:projectId/*"}
              element={<Project />}
            ></Route>
            <Route path={"*"} element={<Navigate to={"/projects"} />}></Route>
          </Routes>
        </Router>
      </Main>
      <ProjectModal
        projectModalOpen={projectModalOpen}
        onClose={() => setProjectModalOpen(false)}
      />
    </Container>
  );
};

const PageHeader = (props: {
  setProjectModalOpen: (isOpen: boolean) => void;
}) => {
  return (
    <Header between={true}>
      <HeaderLeft gap={true}>
        <ButtonNoPadding type={"link"} onClick={resetRoute}>
          <SoftwareLogo width={"18rem"} color={"rgb(38, 132, 255)"} />
        </ButtonNoPadding>
        <ProjectPopover setProjectModalOpen={props.setProjectModalOpen} />
        <span style={{cursor: "pointer"}}>用户</span>
      </HeaderLeft>
      <HeaderRight>
        <User/>
      </HeaderRight>
    </Header>
  );
};

const User = () => {
  const { logout, user } = useAuth();
  return <Dropdown
       overlay={
         <Menu>
           <Menu.Item key={"logout"}>
             <Button onClick={logout} type={"link"}>
               登出
             </Button>
           </Menu.Item>
         </Menu>
       }
     >
       <Button type={"link"} onClick={(e) => e.preventDefault()}>
         Hi, {user?.name}
       </Button>
     </Dropdown>
};    

const Container = styled.div`
  display: grid;
  grid-template-rows: 6rem 1fr;
  height: 100vh;
`;

// grid-area 用来给grid子元素起名字
const Header = styled(Row)`
  padding: 3.2rem;
  box-shadow: 0 0 5px 0 rgba(0, 0, 0, 0.1);
  z-index: 1;
`;

const HeaderLeft = styled(Row)``;
const HeaderRight = styled.div``;
const Main = styled.main`
  overflow: hidden;
`;
```

src\screens\project-list\index.tsx

```tsx
import styled from "@emotion/styled";
import { Button, Typography } from "antd";

import { SearchPanel } from "./search-panel";
import { List } from "./list";

import { useDebounce, useDocumentTitle } from "utils";
import { useProjects } from "utils/projects";
import { useUsers } from "utils/users";
import { useProjectsSearchParams } from "screens/project-list/util";
import { Row } from "components/lib";

export const ProjectList = (props: {
  setProjectModalOpen: (isOpen: boolean) => void;
}) => {
  useDocumentTitle("项目列表", false);
  const [params, setParams] = useProjectsSearchParams();
  const {
    isLoading,
    error,
    data: list,
    retry,
  } = useProjects(useDebounce(params, 1000));
  const { data: users } = useUsers();

  return (
    <Container>
      <Row between={true}>
        <h1>项目列表</h1>
        <Button onClick={() => props.setProjectModalOpen(true)}>
          创建项目
        </Button>
      </Row>
      <SearchPanel params={params} setParams={setParams} users={users || []} />
      {error ? (
        <Typography.Text type={"danger"}>{error.message}</Typography.Text>
      ) : null}
      <List
        setProjectModalOpen={props.setProjectModalOpen}
        refresh={retry}
        users={users || []}
        dataSource={list || []}
        loading={isLoading}
      />
    </Container>
  );
};

const Container = styled.div`
  padding: 3.2rem;
`;
```

src\screens\project-list\list.tsx

```tsx
import { Dropdown, Menu, Table, TableProps } from "antd";
import dayjs from "dayjs";
import { Link } from "react-router-dom";

import { Users } from "./search-panel";
import { Pin } from "components/pin";
import { ButtonNoPadding } from "components/lib";

import { useEditProject } from "utils/projects";

export interface Projects {
  id: number;
  name: string;
  personId: number;
  pin: boolean;
  organization: string;
  created: number;
}

interface ListProps extends TableProps<Projects> {
  users: Users[];
  refresh: () => void;
  setProjectModalOpen: (isOpen: boolean) => void;
}

// ...props 的类型 type PropsType = Omit<ListProps, 'users'>
export const List = ({ users, ...props }: ListProps) => {
  const { mutate } = useEditProject();
  const pinProject = (id: number) => (pin: boolean) =>
    mutate({ id, pin }).then(props.refresh);
  return (
    <Table
      pagination={false}
      columns={[
        {
          title: <Pin checked={true} disabled={true} />,
          render(value, project) {
            return (
              <Pin
                checked={project.pin}
                onCheckedChange={pinProject(project.id)}
              />
            );
          },
        },
        {
          title: "名称",
          dataIndex: "name",
          // 按中文名排序
          sorter: (a, b) => a.name.localeCompare(b.name),
          render(value, project) {
            return (
              // 跳转显示为 /projects/4 的形式
              <Link to={String(project.id)}>{project.name}</Link>
            );
          },
        },
        {
          title: "部门",
          dataIndex: "organization",
        },
        {
          title: "负责人",
          render(value, project) {
            return (
              <span>
                {users.find((user) => user.id === project.personId)?.name ||
                  "未知"}
              </span>
            );
          },
        },
        {
          title: "创建时间",
          render(value, project) {
            return (
              <span>
                {project.created
                  ? dayjs(project.created).format("YYYY-MM-DD")
                  : "无"}
              </span>
            );
          },
        },
        {
          render(value, project) {
            return (
              <Dropdown
                overlay={
                  <Menu>
                    <Menu.Item key={"edit"}>
                      <ButtonNoPadding
                        type={"link"}
                        onClick={() => props.setProjectModalOpen(true)}
                      >
                        编辑
                      </ButtonNoPadding>
                    </Menu.Item>
                  </Menu>
                }
              >
                <ButtonNoPadding type={"link"}>...</ButtonNoPadding>
              </Dropdown>
            );
          },
        },
      ]}
      {...props}
    ></Table>
  );
};
```

**效果图**

<img src="https://cdn.nlark.com/yuque/0/2021/gif/1614731/1639403140573-c71db2b8-25d6-412d-8dfc-6a552a88b469.gif" alt="img"  />

组件组合（component composition）即将组件自身传递下去，解决状态提升后多层组件 prop 传值造成的耦合

（但是状态提升仍然会造成 prop 下钻，即多层 prop 传递）

控制反转可以减少传递的 props 数量，

在这里的控制反转指的是只需要关心渲染的组件 ProjectButton，

不关心怎么运行它，让 setProjectModalOpen 与子组件解耦。

https://react.docschina.org/docs/context.html

如果子组件需要在渲染前和父组件进行一些交流，可以进一步使用 render props 。

src\authenticated-app\index.tsx

```tsx
import { useState } from "react";
import styled from "@emotion/styled";
import { Dropdown, Menu, Button } from "antd";
import { Navigate, Route, Routes } from "react-router";
import { BrowserRouter as Router } from "react-router-dom";

import { ProjectList } from "screens/project-list";
import { ButtonNoPadding, Row } from "components/lib";
import { ReactComponent as SoftwareLogo } from "assets/software-logo.svg";
import { Project } from "screens/project";
import { ProjectModal } from "screens/project-list/project-modal";
import { ProjectPopover } from "components/project-popover";

import { useAuth } from "context/auth-context";
import { resetRoute } from "utils/index";

export const AuthenticatedApp = () => {
  const [projectModalOpen, setProjectModalOpen] = useState(false);

  return (
    <Container>
      <PageHeader 
        projectButton={
          <ButtonNoPadding
            onClick={() => setProjectModalOpen(true)}
            type={"link"}
          >
            创建项目
          </ButtonNoPadding>
        }/>
      <Main>
        <Router>
          <Routes>
            <Route
              path={"/projects"}
              element={
                <ProjectList 
                  projectButton={
                    <ButtonNoPadding
                      onClick={() => setProjectModalOpen(true)}
                      type={"link"}
                    >
                      创建项目
                    </ButtonNoPadding>
                  }/>
              }
            />
            <Route
              path={"/projects/:projectId/*"}
              element={<Project />}
            />
            <Route path={"*"} element={<Navigate to={"/projects"} />}/>
          </Routes>
        </Router>
      </Main>
      <ProjectModal
        projectModalOpen={projectModalOpen}
        onClose={() => setProjectModalOpen(false)}
      />
    </Container>
  );
};

const PageHeader = (props: {
  projectButton: JSX.Element
}) => {
  return (
    <Header between={true}>
      <HeaderLeft gap={true}>
        <ButtonNoPadding type={"link"} onClick={resetRoute}>
          <SoftwareLogo width={"18rem"} color={"rgb(38, 132, 255)"} />
        </ButtonNoPadding>
        <ProjectPopover {...props} />
        <span style={{ cursor: "pointer" }}>用户</span>
      </HeaderLeft>
      <HeaderRight>
        <User />
      </HeaderRight>
    </Header>
  );
};

const User = () => {
  const { logout, user } = useAuth();
  return (
    <Dropdown
      overlay={
        <Menu>
          <Menu.Item key={"logout"}>
            <Button onClick={logout} type={"link"}>
              登出
            </Button>
          </Menu.Item>
        </Menu>
      }
    >
      <Button type={"link"} onClick={(e) => e.preventDefault()}>
        Hi, {user?.name}
      </Button>
    </Dropdown>
  );
};

const Container = styled.div`
  display: grid;
  grid-template-rows: 6rem 1fr;
  height: 100vh;
`;

// grid-area 用来给grid子元素起名字
const Header = styled(Row)`
  padding: 3.2rem;
  box-shadow: 0 0 5px 0 rgba(0, 0, 0, 0.1);
  z-index: 1;
`;

const HeaderLeft = styled(Row)``;
const HeaderRight = styled.div``;
const Main = styled.main`
  overflow: hidden;
`;
```

src\components\project-popover.tsx

```tsx
import { Divider, List, Popover, Typography } from "antd";
import { useProjects } from "utils/projects";
import styled from "@emotion/styled";

export const ProjectPopover = (props: {
  projectButton: JSX.Element 
}) => {
  const { data: projects } = useProjects();
  const pinnedProjects = projects?.filter((project) => project.pin);

  const content = (
    <ContentContainer>
      <Typography.Text type={"secondary"}>收藏项目</Typography.Text>
      <List>
        {pinnedProjects?.map((project) => (
          <List.Item key={project.id}>
            <List.Item.Meta title={project.name} />
          </List.Item>
        ))}
      </List>
      <Divider />
      {props.projectButton}
    </ContentContainer>
  );

  return (
    <Popover placement={"bottom"} content={content}>
      <span style={{ cursor: "pointer" }}>项目</span>
    </Popover>
  );
};

const ContentContainer = styled.div`
  min-width: 30rem;
`;
```

src\screens\project-list\index.tsx

```tsx
import styled from "@emotion/styled";
import { Typography } from "antd";

import { SearchPanel } from "./search-panel";
import { List } from "./list";

import { useDebounce, useDocumentTitle } from "utils";
import { useProjects } from "utils/projects";
import { useUsers } from "utils/users";
import { useProjectsSearchParams } from "screens/project-list/util";
import { Row } from "components/lib";

export const ProjectList = (props: {
  projectButton: JSX.Element 
}) => {
  useDocumentTitle("项目列表", false);
  const [params, setParams] = useProjectsSearchParams();
  const {
    isLoading,
    error,
    data: list,
    retry,
  } = useProjects(useDebounce(params, 1000));
  const { data: users } = useUsers();

  return (
    <Container>
      <Row between={true}>
        <h1>项目列表</h1>
        {props.projectButton}
      </Row>
      <SearchPanel params={params} setParams={setParams} users={users || []} />
      {error ? (
        <Typography.Text type={"danger"}>{error.message}</Typography.Text>
      ) : null}
      <List
        projectButton={props.projectButton}
        refresh={retry}
        users={users || []}
        dataSource={list || []}
        loading={isLoading}
      />
    </Container>
  );
};

const Container = styled.div`
  padding: 3.2rem;
`;
```

src\screens\project-list\list.tsx

```tsx
import { Dropdown, Menu, Table, TableProps } from "antd";
import dayjs from "dayjs";
import { Link } from "react-router-dom";

import { Users } from "./search-panel";
import { Pin } from "components/pin";
import { ButtonNoPadding } from "components/lib";

import { useEditProject } from "utils/projects";

export interface Projects {
  id: number;
  name: string;
  personId: number;
  pin: boolean;
  organization: string;
  created: number;
}

interface ListProps extends TableProps<Projects> {
  users: Users[];
  refresh: () => void;
  projectButton: JSX.Element;
}

// ...props 的类型 type PropsType = Omit<ListProps, 'users'>
export const List = ({ users, ...props }: ListProps) => {
  const { mutate } = useEditProject();
  const pinProject = (id: number) => (pin: boolean) =>
    mutate({ id, pin }).then(props.refresh);
  return (
    <Table
      pagination={false}
      columns={[
        {
          title: <Pin checked={true} disabled={true} />,
          render(value, project) {
            return (
              <Pin
                checked={project.pin}
                onCheckedChange={pinProject(project.id)}
              />
            );
          },
        },
        {
          title: "名称",
          dataIndex: "name",
          // 按中文名排序
          sorter: (a, b) => a.name.localeCompare(b.name),
          render(value, project) {
            return (
              // 跳转显示为 /projects/4 的形式
              <Link to={String(project.id)}>{project.name}</Link>
            );
          },
        },
        {
          title: "部门",
          dataIndex: "organization",
        },
        {
          title: "负责人",
          render(value, project) {
            return (
              <span>
                {users.find((user) => user.id === project.personId)?.name ||
                  "未知"}
              </span>
            );
          },
        },
        {
          title: "创建时间",
          render(value, project) {
            return (
              <span>
                {project.created
                  ? dayjs(project.created).format("YYYY-MM-DD")
                  : "无"}
              </span>
            );
          },
        },
        {
          render(value, project) {
            return (
              <Dropdown
                overlay={
                  <Menu>
                     <Menu.Item key={"edit"}>{props.projectButton}</Menu.Item>
                  </Menu>
                }
              >
                <ButtonNoPadding type={"link"}>...</ButtonNoPadding>
              </Dropdown>
            );
          },
        },
      ]}
      {...props}
    ></Table>
  );
};
```

## 合并组件状态，实现 useUndo

https://codesandbox.io/s/5v9yoz7xn4?file=/src/index.js

```js
import React from "react";
import ReactDOM from "react-dom";
import useUndo from "use-undo";

import "./styles.css";

const App = () => {
  const [
    countState,
    {
      set: setCount,
      reset: resetCount,
      undo: undoCount,
      redo: redoCount,
      canUndo,
      canRedo
    }
  ] = useUndo(0);
  const { present: presentCount } = countState;

  return (
    <div>
      <p>You clicked {presentCount} times</p>
      <button key="increment" onClick={() => setCount(presentCount + 1)}>
        +
      </button>{" "}
      <button key="decrement" onClick={() => setCount(presentCount - 1)}>
        -
      </button>{" "}
      <button key="undo" onClick={undoCount} disabled={!canUndo}>
        undo
      </button>{" "}
      <button key="redo" onClick={redoCount} disabled={!canRedo}>
        redo
      </button>
      <button key="reset" onClick={() => resetCount(0)}>
        reset to 0
      </button>
    </div>
  );
};

const rootElement = document.getElementById("root");
ReactDOM.render(<App />, rootElement);
```

上一步 undo

下一步 redo

canUndo: undo 终点的判断

canRedo: redo 终点的判断

src/utils/use-undo.ts

```ts
import { useState } from "react";

export const useUndo = <T>(initialPresent: T) => {
  const [past, setPast] = useState<T[]>([]);
  const [present, setPresent] = useState(initialPresent);
  const [future, setFuture] = useState<T[]>([]);

  const canUndo = past.length !== 0;
  const canRedo = future.length !== 0;

  const undo = () => {
    if (!canUndo) return;

    const previous = past[past.length - 1];
    const newPast = past.slice(0, past.length - 1);

    setPast(newPast);
    setPresent(previous);
    setFuture([present, ...future]);
  };

  const redo = () => {
    if (!canRedo) return;

    const next = future[0];
    const newFuture = future.slice(1);

    setPast([...past, present]);
    setPresent(next);
    setFuture(newFuture);
  };

  const set = (newPresent: T) => {
    if (newPresent === present) {
      return;
    }
    setPast([...past, present]);
    setPresent(newPresent);
    setFuture([]);
  };

  const reset = (newPresent: T) => {
    setPast([]);
    setPresent(newPresent);
    setFuture([]);
  };

  return [
    { past, present, future },
    { set, reset, undo, redo, canUndo, canRedo },
  ] as const;
};
```

```ts
import { useCallback, useState } from "react";

export const useUndo = <T>(initialPresent: T) => {
  const [state, setState] = useState<{
    past: T[];
    present: T;
    future: T[];
  }>({
    past: [],
    present: initialPresent,
    future: [],
  });

  const canUndo = state.past.length !== 0;
  const canRedo = state.future.length !== 0;

  const undo = useCallback(() => {
    setState((currentState) => {
      const { past, present, future } = currentState;
      if (past.length === 0) return currentState;

      const previous = past[past.length - 1];
      const newPast = past.slice(0, past.length - 1);

      return {
        past: newPast,
        present: previous,
        future: [present, ...future],
      };
    });
  }, []);

  const redo = useCallback(() => {
    setState((currentState) => {
      const { past, present, future } = currentState;
      if (future.length === 0) return currentState;

      const next = future[0];
      const newFuture = future.slice(1);

      return {
        past: [...past, present],
        present: next,
        future: newFuture,
      };
    });
  }, []);

  const set = useCallback((newPresent: T) => {
    setState((currentState) => {
      const { past, present, future } = currentState;
      if (newPresent === present) {
        return currentState;
      }
      return {
        past: [...past, present],
        present: newPresent,
        future: [],
      };
    });
  }, []);

  const reset = useCallback((newPresent: T) => {
    setState(() => {
      return {
        past: [],
        present: newPresent,
        future: [],
      };
    });
  }, []);

  return [state, { set, reset, undo, redo, canUndo, canRedo }] as const;
};
```

用 useReducer 改写 useUndo

setState 通过原来的 state 产生新的 state，使用 useReducer 可以更方便处理。

useState 适合定义单个状态，useReducer 适合定义多个会互相影响的状态。

useUndo 是轻量版的 Redux 状态管理，但是并不是全局状态管理，不能在组件间共享。

```ts
import { useCallback, useReducer } from "react";

const UNDO = "UNDO";
const REDO = "REDO";
const SET = "SET";
const RESET = "RESET";

type State<T> = {
  past: T[];
  present: T;
  future: T[];
};

type Action<T> = {
  newPresent?: T;
  type: typeof UNDO | typeof REDO | typeof SET | typeof RESET;
};

const undoReducer = <T>(state: State<T>, action: Action<T>) => {
  const { past, present, future } = state;
  const { newPresent } = action;

  switch (action.type) {
    case UNDO: {
      if (past.length === 0) return state;

      const previous = past[past.length - 1];
      const newPast = past.slice(0, past.length - 1);

      return {
        past: newPast,
        present: previous,
        future: [present, ...future],
      };
    }

    case REDO: {
      if (future.length === 0) return state;

      const next = future[0];
      const newFuture = future.slice(1);

      return {
        past: [...past, present],
        present: next,
        future: newFuture,
      };
    }

    case SET: {
      if (newPresent === present) {
        return state;
      }
      return {
        past: [...past, present],
        present: newPresent,
        future: [],
      };
    }

    case RESET: {
      return {
        past: [],
        present: newPresent,
        future: [],
      };
    }
  }
  return state;
};

export const useUndo = <T>(initialPresent: T) => {
  const [state, dispatch] = useReducer(undoReducer, {
    past: [],
    present: initialPresent,
    future: [],
  } as State<T>);

  const canUndo = state.past.length !== 0;
  const canRedo = state.future.length !== 0;

  const undo = useCallback(() => dispatch({ type: UNDO }), []);

  const redo = useCallback(() => dispatch({ type: REDO }), []);

  const set = useCallback(
    (newPresent: T) => dispatch({ type: SET, newPresent }),
    []
  );

  const reset = useCallback(
    (newPresent: T) => dispatch({ type: RESET, newPresent }),
    []
  );

  return [state, { set, reset, undo, redo, canUndo, canRedo }] as const;
};
```

## 用 useReducer 改造 useAsync

```ts
import { useMountedRef } from "./index";
import { useState, useCallback } from "react";

interface State<D> {
  stat: "idle" | "loading" | "success" | "error";
  data: D | null;
  error: Error | null;
}

const defaultConfig = {
  throwOnError: false,
};

const defaultInitialState: State<null> = {
  stat: "idle",
  data: null,
  error: null,
};

export const useAsync = <D>(
  initialState?: State<D>,
  initialConfig?: typeof defaultConfig
) => {
  const config = { ...defaultConfig, ...initialConfig };
  const [state, setState] = useState<State<D>>({
    ...defaultInitialState,
    ...initialState,
  });
  const mountedRef = useMountedRef();
  // useState 记住每一次的请求，每次刷新都留有上一次的
  const [retry, setRetry] = useState(() => () => {});

  const setData = useCallback(
    (data: D) =>
      setState({
        stat: "success",
        data,
        error: null,
      }),
    []
  );

  const setError = useCallback(
    (error: Error) =>
      setState({
        stat: "error",
        data: null,
        error,
      }),
    []
  );

  // run 用来触发异步请求
  const run = useCallback(
    (promise: Promise<D>, runConfig?: { retry: () => Promise<D> }) => {
      if (!promise || !promise.then) {
        throw new Error("请传入 Promise 类型数据");
      }
      setRetry(() => () => {
        if (runConfig?.retry) {
          run(runConfig?.retry(), runConfig);
        }
      });
      setState((prevState) => ({ ...prevState, stat: "loading" }));
      return promise
        .then((data) => {
          if (mountedRef.current) setData(data);
          return data;
        })
        .catch((error) => {
          // catch会消化异常，如果不主动抛出，外面是接收不到异常的
          setError(error);
          if (config.throwOnError) return Promise.reject(error);
          return error;
        });
    },
    [config.throwOnError, mountedRef, setData, setError]
  );

  return {
    isIdle: state.stat === "idle",
    isLoading: state.stat === "loading",
    isSuccess: state.stat === "success",
    isError: state.stat === "error",
    run,
    setData,
    setError,
    // retry 被调用时重新跑一遍run，让state刷新一遍
    retry,
    ...state,
  };
};
```

改造为

```ts
import { useCallback, useReducer, useState } from "react";
import { useMountedRef } from "utils/index";

interface State<D> {
  stat: "idle" | "loading" | "success" | "error";
  data: D | null;
  error: Error | null;
}

const defaultConfig = {
  throwOnError: false,
};

const defaultInitialState: State<null> = {
  stat: "idle",
  data: null,
  error: null,
};

const useSafeDispatch = <T>(dispatch: (...args: T[]) => void) => {
  const mountedRef = useMountedRef();
  return useCallback(
    (...args: T[]) => (mountedRef.current ? dispatch(...args) : void 0),
    [dispatch, mountedRef]
  );
};

export const useAsync = <D>(
  initialState?: State<D>,
  initialConfig?: typeof defaultConfig
) => {
  const config = { ...defaultConfig, ...initialConfig };
  const [state, dispatch] = useReducer(
    (state: State<D>, action: Partial<State<D>>) => ({ ...state, ...action }),
    {
      ...defaultInitialState,
      ...initialState,
    }
  );
  const safeDispatch = useSafeDispatch(dispatch);
  // useState 记住每一次的请求，每次刷新都留有上一次的
  const [retry, setRetry] = useState(() => () => {});

  const setData = useCallback(
    (data: D) =>
      safeDispatch({
        stat: "success",
        data,
        error: null,
      }),
    [safeDispatch]
  );

  const setError = useCallback(
    (error: Error) =>
      safeDispatch({
        stat: "error",
        data: null,
        error,
      }),
    [safeDispatch]
  );

  // run 用来触发异步请求
  const run = useCallback(
    (promise: Promise<D>, runConfig?: { retry: () => Promise<D> }) => {
      if (!promise || !promise.then) {
        throw new Error("请传入 Promise 类型数据");
      }
      setRetry(() => () => {
        if (runConfig?.retry) {
          run(runConfig?.retry(), runConfig);
        }
      });
      safeDispatch({ stat: "loading" });
      return promise
        .then((data) => {
          setData(data);
          return data;
        })
        .catch((error) => {
          // catch会消化异常，如果不主动抛出，外面是接收不到异常的
          setError(error);
          if (config.throwOnError) return Promise.reject(error);
          return error;
        });
    },
    [config.throwOnError, setData, setError, safeDispatch]
  );

  return {
    isIdle: state.stat === "idle",
    isLoading: state.stat === "loading",
    isSuccess: state.stat === "success",
    isError: state.stat === "error",
    run,
    setData,
    setError,
    // retry 被调用时重新跑一遍run，让state刷新一遍
    retry,
    ...state,
  };
};
```

## 用 url 参数管理项目 Modal 模态框状态

src\screens\project-list\util.ts

```ts
import { useUrlQueryParam } from "utils/url";
import { useMemo } from "react";

// 项目列表搜索的参数
export const useProjectsSearchParams = () => {
  const [params, setParams] = useUrlQueryParam(["name", "personId"]);
  return [
    useMemo(
      () => ({ ...params, personId: Number(params.personId) || undefined }),
      [params]
    ),
    setParams,
  ] as const;
};

export const useProjectModal = () => {
  const [{ projectCreate }, setProjectCreate] = useUrlQueryParam([
    "projectCreate",
  ]);

  const open = () => setProjectCreate({ projectCreate: true });
  const close = () => setProjectCreate({ projectCreate: undefined });

  return {
    projectModalOpen: projectCreate === "true",
    open,
    close,
  };
};
```

useProjectModal  hook 返回值数量为三个以上时使用对象，否则使用元组（[] as const），元组可以让 hook 在解构时任意命名

对象则需要自己手动重新命名

src\authenticated-app\index.tsx

```tsx
import styled from "@emotion/styled";
import { Dropdown, Menu, Button } from "antd";
import { Navigate, Route, Routes } from "react-router";
import { BrowserRouter as Router } from "react-router-dom";

import { ProjectList } from "screens/project-list";
import { ButtonNoPadding, Row } from "components/lib";
import { ReactComponent as SoftwareLogo } from "assets/software-logo.svg";
import { Project } from "screens/project";
import { ProjectModal } from "screens/project-list/project-modal";
import { ProjectPopover } from "components/project-popover";

import { useAuth } from "context/auth-context";
import { resetRoute } from "utils/index";

export const AuthenticatedApp = () => {
  return (
    <Container>
      <Router>
        <PageHeader />
        <Main>
          <Routes>
            <Route path={"/projects"} element={<ProjectList />} />
            <Route path={"/projects/:projectId/*"} element={<Project />} />
            <Route path={"*"} element={<Navigate to={"/projects"} />} />
          </Routes>
        </Main>
        <ProjectModal />
      </Router>
    </Container>
  );
};

const PageHeader = () => {
  return (
    <Header between={true}>
      <HeaderLeft gap={true}>
        <ButtonNoPadding type={"link"} onClick={resetRoute}>
          <SoftwareLogo width={"18rem"} color={"rgb(38, 132, 255)"} />
        </ButtonNoPadding>
        <ProjectPopover />
        <span style={{ cursor: "pointer" }}>用户</span>
      </HeaderLeft>
      <HeaderRight>
        <User />
      </HeaderRight>
    </Header>
  );
};

const User = () => {
  const { logout, user } = useAuth();
  return (
    <Dropdown
      overlay={
        <Menu>
          <Menu.Item key={"logout"}>
            <Button onClick={logout} type={"link"}>
              登出
            </Button>
          </Menu.Item>
        </Menu>
      }
    >
      <Button type={"link"} onClick={(e) => e.preventDefault()}>
        Hi, {user?.name}
      </Button>
    </Dropdown>
  );
};

const Container = styled.div`
  display: grid;
  grid-template-rows: 6rem 1fr;
  height: 100vh;
`;

// grid-area 用来给grid子元素起名字
const Header = styled(Row)`
  padding: 3.2rem;
  box-shadow: 0 0 5px 0 rgba(0, 0, 0, 0.1);
  z-index: 1;
`;

const HeaderLeft = styled(Row)``;
const HeaderRight = styled.div``;
const Main = styled.main`
  overflow: hidden;
`;
```

src\screens\project-list\project-modal.tsx

```tsx
import { Button, Drawer } from "antd";
import { useProjectModal } from "screens/project-list/util";

export const ProjectModal = () => {
  const { projectModalOpen, close } = useProjectModal();
  return (
    <Drawer onClose={close} visible={projectModalOpen} width={"100%"}>
      <h1>Project Modal</h1>
      <Button onClick={close}>关闭</Button>
    </Drawer>
  );
};
```

src\components\project-popover.tsx

```tsx
import { Divider, List, Popover, Typography } from "antd";
import { useProjects } from "utils/projects";
import { useProjectModal } from "screens/project-list/util";

import styled from "@emotion/styled";
import { ButtonNoPadding } from "components/lib";

export const ProjectPopover = () => {
  const { open } = useProjectModal();
  const { data: projects } = useProjects();
  const pinnedProjects = projects?.filter((project) => project.pin);

  const content = (
    <ContentContainer>
      <Typography.Text type={"secondary"}>收藏项目</Typography.Text>
      <List>
        {pinnedProjects?.map((project) => (
          <List.Item key={project.id}>
            <List.Item.Meta title={project.name} />
          </List.Item>
        ))}
      </List>
      <Divider />
      <ButtonNoPadding onClick={open} type={"link"}>
        创建项目
      </ButtonNoPadding>
    </ContentContainer>
  );

  return (
    <Popover placement={"bottom"} content={content}>
      <span style={{ cursor: "pointer" }}>项目</span>
    </Popover>
  );
};

const ContentContainer = styled.div`
  min-width: 30rem;
`;
```

src\screens\project-list\index.tsx

```tsx
import styled from "@emotion/styled";
import { Typography } from "antd";
import { ButtonNoPadding, Row } from "components/lib";

import { SearchPanel } from "./search-panel";
import { List } from "./list";

import { useDebounce, useDocumentTitle } from "utils";
import { useProjects } from "utils/projects";
import { useUsers } from "utils/users";
import {
  useProjectModal,
  useProjectsSearchParams,
} from "screens/project-list/util";

export const ProjectList = () => {
  useDocumentTitle("项目列表", false);
  const { open } = useProjectModal();
  const [params, setParams] = useProjectsSearchParams();
  const {
    isLoading,
    error,
    data: list,
    retry,
  } = useProjects(useDebounce(params, 1000));
  const { data: users } = useUsers();

  return (
    <Container>
      <Row between={true}>
        <h1>项目列表</h1>
        <ButtonNoPadding onClick={open} type={"link"}>
          创建项目
        </ButtonNoPadding>
      </Row>
      <SearchPanel params={params} setParams={setParams} users={users || []} />
      {error ? (
        <Typography.Text type={"danger"}>{error.message}</Typography.Text>
      ) : null}
      <List
        refresh={retry}
        users={users || []}
        dataSource={list || []}
        loading={isLoading}
      />
    </Container>
  );
};

const Container = styled.div`
  padding: 3.2rem;
`;
```

src\screens\project-list\list.tsx

```tsx
import { Dropdown, Menu, Table, TableProps } from "antd";
import dayjs from "dayjs";
import { Link } from "react-router-dom";

import { Users } from "./search-panel";
import { Pin } from "components/pin";
import { ButtonNoPadding } from "components/lib";

import { useEditProject } from "utils/projects";

export interface Projects {
  id: number;
  name: string;
  personId: number;
  pin: boolean;
  organization: string;
  created: number;
}

interface ListProps extends TableProps<Projects> {
  users: Users[];
  refresh: () => void;
}

// ...props 的类型 type PropsType = Omit<ListProps, 'users'>
export const List = ({ users, ...props }: ListProps) => {
  const { mutate } = useEditProject();
  const pinProject = (id: number) => (pin: boolean) =>
    mutate({ id, pin }).then(props.refresh);
  return (
    <Table
      pagination={false}
      columns={[
        {
          title: <Pin checked={true} disabled={true} />,
          render(value, project) {
            return (
              <Pin
                checked={project.pin}
                onCheckedChange={pinProject(project.id)}
              />
            );
          },
        },
        {
          title: "名称",
          dataIndex: "name",
          // 按中文名排序
          sorter: (a, b) => a.name.localeCompare(b.name),
          render(value, project) {
            return (
              // 跳转显示为 /projects/4 的形式
              <Link to={String(project.id)}>{project.name}</Link>
            );
          },
        },
        {
          title: "部门",
          dataIndex: "organization",
        },
        {
          title: "负责人",
          render(value, project) {
            return (
              <span>
                {users.find((user) => user.id === project.personId)?.name ||
                  "未知"}
              </span>
            );
          },
        },
        {
          title: "创建时间",
          render(value, project) {
            return (
              <span>
                {project.created
                  ? dayjs(project.created).format("YYYY-MM-DD")
                  : "无"}
              </span>
            );
          },
        },
        {
          render(value, project) {
            return (
              <Dropdown
                overlay={
                  <Menu>
                    <Menu.Item key={"edit"}></Menu.Item>
                  </Menu>
                }
              >
                <ButtonNoPadding type={"link"}>...</ButtonNoPadding>
              </Dropdown>
            );
          },
        },
      ]}
      {...props}
    ></Table>
  );
};
```

效果图

![img](https://cdn.nlark.com/yuque/0/2022/gif/1614731/1642591124483-e31e25e4-22e9-46ba-bcec-37bc3c7fda31.gif)

## 完成编辑与添加功能

src\utils\projects.ts

```ts
import { Projects } from "screens/project-list/list";

import { useHttp } from "./http";
import { useQuery, useMutation, useQueryClient } from "react-query";

import { Project } from "types/project";

export const useProjects = (param?: Partial<Projects>) => {
  const client = useHttp();
  // param 变化会触发重新请求
  return useQuery<Project[]>(["projects", param], () =>
    client("projects", { data: param })
  );
};

export const useEditProject = () => {
  const client = useHttp();
  const queryClient = useQueryClient();

  // 第二个参数用于请求后对缓存进行更新
  return useMutation(
    (params: Partial<Project>) =>
      client(`projects/${params.id}`, {
        method: "PATCH",
        data: params,
      }),
    {
      onSuccess: () => queryClient.invalidateQueries("projects"),
    }
  );
};

export const useAddProject = () => {
  const client = useHttp();
  const queryClient = useQueryClient();

  return useMutation(
    (params: Partial<Project>) =>
      client(`projects`, {
        method: "POST",
        data: params,
      }),
    {
      onSuccess: () => queryClient.invalidateQueries("projects"),
    }
  );
};

export const useProject = (id?: number) => {
  const client = useHttp();
  // 第三个参数，如果 id 是 undefined，则不请求
  return useQuery<Project>(
    ["project", { id }],
    () => client(`projects/${id}`),
    {
      enabled: Boolean(id),
    }
  );
};
```

src\screens\project-list\util.ts

```ts
import { useSetUrlSearchParam, useUrlQueryParam } from "utils/url";
import { useMemo } from "react";
import { useProject } from "utils/projects";

// 项目列表搜索的参数
export const useProjectsSearchParams = () => {
  const [params, setParams] = useUrlQueryParam(["name", "personId"]);
  return [
    useMemo(
      () => ({ ...params, personId: Number(params.personId) || undefined }),
      [params]
    ),
    setParams,
  ] as const;
};

export const useProjectModal = () => {
  const [{ projectCreate }, setProjectCreate] = useUrlQueryParam([
    "projectCreate",
  ]);
  const [{ editingProjectId }, setEditingProjectId] = useUrlQueryParam([
    "editingProjectId",
  ]);
  const { data: editingProject, isLoading } = useProject(
    Number(editingProjectId)
  );
  const setUrlParams = useSetUrlSearchParam();

  const open = () => setProjectCreate({ projectCreate: true });
  const close = () => {
    setUrlParams({ projectCreate: "", editingProjectId: "" });
  };
  const startEdit = (id: number) =>
    setEditingProjectId({ editingProjectId: id });

  return {
    projectModalOpen: projectCreate === "true" || Boolean(editingProjectId),
    open,
    close,
    startEdit,
    editingProject,
    isLoading,
  };
};

export const useProjectsQueryKey = () => {
  const [params] = useProjectsSearchParams();
  return ["projects", params];
};
```

src\utils\url.ts

```ts
import { URLSearchParamsInit, useSearchParams } from "react-router-dom";
import { useMemo, useState } from "react";
import { cleanObject, subset } from "utils/index";

/**
 * 返回页面url中，指定键的参数值
 */
export const useUrlQueryParam = <K extends string>(keys: K[]) => {
  const [searchParams] = useSearchParams();
  const setSearchParams = useSetUrlSearchParam();
  const [stateKeys] = useState(keys);
  return [
    useMemo(
      () =>
        subset(Object.fromEntries(searchParams), stateKeys) as {
          [key in K]: string;
        },
      [searchParams, stateKeys]
    ),
    (params: Partial<{ [key in K]: unknown }>) => {
      return setSearchParams(params);
      // iterator
      // iterator: https://codesandbox.io/s/upbeat-wood-bum3j?file=/src/index.js
    },
  ] as const;
};

export const useSetUrlSearchParam = () => {
  const [searchParams, setSearchParam] = useSearchParams();
  return (params: { [key in string]: unknown }) => {
    const o = cleanObject({
      ...Object.fromEntries(searchParams),
      ...params,
    }) as URLSearchParamsInit;
    return setSearchParam(o);
  };
};
```

src\components\lib.tsx

```tsx
import styled from "@emotion/styled";
import { Button, Spin, Typography } from "antd";
import { DevTools } from "jira-dev-tool";

export const Row = styled.div<{
  gap?: number | boolean;
  between?: boolean;
  marginBottom?: number;
}>`
  display: flex;
  align-items: center;
  justify-content: ${(props) => (props.between ? "space-between" : undefined)};
  margin-bottom: ${(props) => props.marginBottom + "rem"};

  > * {
    margin-top: 0 !important;
    margin-bottom: 0 !important;
    margin-right: ${(props) =>
      typeof props.gap === "number"
        ? props.gap + "rem"
        : props.gap
        ? "2rem"
        : undefined};
  }
`;

const FullPage = styled.div`
  height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
`;

export const FullPageLoading = () => (
  <FullPage>
    <Spin size={"large"} />
  </FullPage>
);

export const FullPageErrorFallback = ({ error }: { error: Error | null }) => (
  <FullPage>
    <DevTools />
    <ErrorBox error={error} />
  </FullPage>
);

export const ButtonNoPadding = styled(Button)`
  padding: 0;
`;

// 类型守卫
const isError = (value: any): value is Error => value?.message;

export const ErrorBox = ({ error }: { error: unknown }) => {
  if (isError(error)) {
    return <Typography.Text type={"danger"}>{error?.message}</Typography.Text>;
  }
  return null;
};
```

src\unauthenticated-app\index.tsx

```tsx
import { useState } from "react";
import { Login } from "./login";
import { Register } from "./register";
import { Card, Divider, Button } from "antd";
import styled from "@emotion/styled";
import logo from "assets/logo.svg";
import left from "assets/left.svg";
import right from "assets/right.svg";
import { useDocumentTitle } from "utils";
import { ErrorBox } from "components/lib";

export const UnauthenticatedApp = () => {
  useDocumentTitle("请登录注册以继续");

  const [isRegister, setIsRegister] = useState(false);
  const [error, setError] = useState<Error | null>(null);

  return (
    <Container>
      <Header />
      <Background />
      <ShadowCard>
        <Title>{isRegister ? "请注册" : "请登录"}</Title>
        <ErrorBox error={error} />
        {isRegister ? (
          <Register onError={setError} />
        ) : (
          <Login onError={setError} />
        )}
        <Divider />
        <Button
          type={"link"}
          onClick={() => {
            setIsRegister(!isRegister);
            setError(null);
          }}
        >
          {isRegister ? "已经有账号了？直接登录" : "没有账号？注册新账号"}
        </Button>
      </ShadowCard>
    </Container>
  );
};

export const LongButton = styled(Button)`
  width: 100%;
`;

const Container = styled.div`
  display: flex;
  flex-direction: column;
  align-items: center;
  min-height: 100vh;
`;

const Header = styled.header`
  background: url(${logo}) no-repeat center;
  padding: 5rem 0;
  background-size: 8rem;
  width: 100%;
`;

const Background = styled.div`
  position: absolute;
  width: 100%;
  height: 100%;
  background-repeat: no-repeat;
  background-attachment: fixed;
  background-position: left bottom, right bottom;
  background-size: calc(((100vw - 40rem) / 2) - 3.2rem),
    calc(((100vw - 40rem) / 2) - 3.2rem), cover;
  background-image: url(${left}), url(${right});
`;

const ShadowCard = styled(Card)`
  width: 40rem;
  padding: 3.2rem 4rem;
  border-radius: 0.3rem;
  box-sizing: border-box;
  box-shadow: rgba(0, 0, 0, 0.1) 0 0 10px;
  text-align: center;
`;

const Title = styled.h2`
  margin-bottom: 2.4rem;
  color: rgb(94, 108, 132);
`;
```

src\screens\project-list\index.tsx

```tsx
import styled from "@emotion/styled";
import { ButtonNoPadding, ErrorBox, Row } from "components/lib";

import { SearchPanel } from "./search-panel";
import { List } from "./list";

import { useDebounce, useDocumentTitle } from "utils";
import { useProjects } from "utils/projects";
import { useUsers } from "utils/users";
import {
  useProjectModal,
  useProjectsSearchParams,
} from "screens/project-list/util";

export const ProjectList = () => {
  useDocumentTitle("项目列表", false);
  const { open } = useProjectModal();
  const [params, setParams] = useProjectsSearchParams();
  const {
    isLoading,
    error,
    data: list,
  } = useProjects(useDebounce(params, 1000));
  const { data: users } = useUsers();

  return (
    <Container>
      <Row between={true}>
        <h1>项目列表</h1>
        <ButtonNoPadding onClick={open} type={"link"}>
          创建项目
        </ButtonNoPadding>
      </Row>
      <SearchPanel params={params} setParams={setParams} users={users || []} />
      <ErrorBox error={error} />
      <List users={users || []} dataSource={list || []} loading={isLoading} />
    </Container>
  );
};

const Container = styled.div`
  padding: 3.2rem;
`;
```

src\screens\project-list\list.tsx

```tsx
import { Dropdown, Menu, Table, TableProps } from "antd";
import dayjs from "dayjs";
import { Link } from "react-router-dom";

import { Users } from "./search-panel";
import { Pin } from "components/pin";
import { ButtonNoPadding } from "components/lib";

import { useEditProject } from "utils/projects";
import { useProjectModal } from "./util";

export interface Projects {
  id: number;
  name: string;
  personId: number;
  pin: boolean;
  organization: string;
  created: number;
}

interface ListProps extends TableProps<Projects> {
  users: Users[];
}

// ...props 的类型 type PropsType = Omit<ListProps, 'users'>
export const List = ({ users, ...props }: ListProps) => {
  const { mutate } = useEditProject();
  const pinProject = (id: number) => (pin: boolean) => mutate({ id, pin });
  const { startEdit } = useProjectModal();
  const editProject = (id: number) => () => startEdit(id);
  return (
    <Table
      rowKey={"id"}
      pagination={false}
      columns={[
        {
          title: <Pin checked={true} disabled={true} />,
          render(value, project) {
            return (
              <Pin
                checked={project.pin}
                onCheckedChange={pinProject(project.id)}
              />
            );
          },
        },
        {
          title: "名称",
          dataIndex: "name",
          // 按中文名排序
          sorter: (a, b) => a.name.localeCompare(b.name),
          render(value, project) {
            return (
              // 跳转显示为 /projects/4 的形式
              <Link to={String(project.id)}>{project.name}</Link>
            );
          },
        },
        {
          title: "部门",
          dataIndex: "organization",
        },
        {
          title: "负责人",
          render(value, project) {
            return (
              <span>
                {users.find((user) => user.id === project.personId)?.name ||
                  "未知"}
              </span>
            );
          },
        },
        {
          title: "创建时间",
          render(value, project) {
            return (
              <span>
                {project.created
                  ? dayjs(project.created).format("YYYY-MM-DD")
                  : "无"}
              </span>
            );
          },
        },
        {
          render(value, project) {
            return (
              <Dropdown
                overlay={
                  <Menu>
                    <Menu.Item onClick={editProject(project.id)} key={"edit"}>
                      编辑
                    </Menu.Item>
                    <Menu.Item key={"delete"}>删除</Menu.Item>
                  </Menu>
                }
              >
                <ButtonNoPadding type={"link"}>...</ButtonNoPadding>
              </Dropdown>
            );
          },
        },
      ]}
      {...props}
    ></Table>
  );
};
```

src\screens\project-list\project-modal.tsx

```tsx
import styled from "@emotion/styled";
import { Button, Drawer, Form, Input, Spin } from "antd";
import { ErrorBox } from "components/lib";
import { UserSelect } from "components/user-select";
import { useEffect } from "react";
import { useProjectModal } from "screens/project-list/util";
import { useAddProject, useEditProject } from "utils/projects";

export const ProjectModal = () => {
  const { projectModalOpen, close, editingProject, isLoading } =
    useProjectModal();
  const title = editingProject ? "编辑项目" : "创建项目";

  const useMutateProject = editingProject ? useEditProject : useAddProject;
  const { mutateAsync, error, isLoading: mutateLoading } = useMutateProject();

  const [form] = Form.useForm();
  const onFinish = (values: any) => {
    mutateAsync({ ...editingProject, ...values }).then(() => {
      form.resetFields();
      close();
    });
  };
  const closeModal = () => {
    form.resetFields();
    close();
  };

  useEffect(() => {
    form.setFieldsValue(editingProject);
  }, [editingProject, form]);

  return (
    <Drawer
      forceRender={true}
      onClose={closeModal}
      visible={projectModalOpen}
      width={"100%"}
    >
      <Container>
        {isLoading ? (
          <Spin size={"large"} />
        ) : (
          <>
            <h1>{title}</h1>
            <ErrorBox error={error} />
            <Form
              form={form}
              layout={"vertical"}
              style={{ width: "40rem" }}
              onFinish={onFinish}
            >
              <Form.Item
                label={"名称"}
                name={"name"}
                rules={[{ required: true, message: "请输入项目名" }]}
              >
                <Input placeholder={"请输入项目名称"} />
              </Form.Item>

              <Form.Item
                label={"部门"}
                name={"organization"}
                rules={[{ required: true, message: "请输入部门名" }]}
              >
                <Input placeholder={"请输入部门名"} />
              </Form.Item>

              <Form.Item label={"负责人"} name={"personId"}>
                <UserSelect defaultOptionName={"负责人"} />
              </Form.Item>

              <Form.Item style={{ textAlign: "right" }}>
                <Button
                  loading={mutateLoading}
                  type={"primary"}
                  htmlType={"submit"}
                >
                  提交
                </Button>
              </Form.Item>
            </Form>
          </>
        )}
      </Container>
    </Drawer>
  );
};

const Container = styled.div`
  height: 80vh;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
`;
```

效果图

![img](https://cdn.nlark.com/yuque/0/2022/gif/1614731/1653118803612-ce223003-f972-4565-9997-81845981d076.gif)

![img](https://cdn.nlark.com/yuque/0/2022/gif/1614731/1653118803883-afc4856b-03da-4c69-bc89-5eba22e6350e.gif)

## 完成乐观更新和删除功能

src\utils\use-optimistic-options.ts

```ts
import { QueryKey, useQueryClient } from "react-query";

export const useConfig = (
  queryKey: QueryKey,
  callback: (target: any, old?: any[]) => any[]
) => {
  const queryClient = useQueryClient();
  return {
    onSuccess: () => queryClient.invalidateQueries(queryKey),
    async onMutate(target: any) {
      const previousItems = queryClient.getQueryData(queryKey);
      queryClient.setQueryData(queryKey, (old?: any[]) => {
        return callback(target, old);
      });
      return { previousItems };
    },
    onError(error: any, newItem: any, context: any) {
      queryClient.setQueryData(queryKey, context.previousItems);
    },
  };
};

export const useDeleteConfig = (queryKey: QueryKey) =>
  useConfig(
    queryKey,
    (target, old) => old?.filter((item) => item.id !== target.id) || []
  );
export const useEditConfig = (queryKey: QueryKey) =>
  useConfig(
    queryKey,
    (target, old) =>
      old?.map((item) =>
        item.id === target.id ? { ...item, ...target } : item
      ) || []
  );
export const useAddConfig = (queryKey: QueryKey) =>
  useConfig(queryKey, (target, old) => (old ? [...old, target] : []));
```

src\utils\projects.ts

```ts
import { Projects } from "screens/project-list/list";

import { useHttp } from "./http";
import { useQuery, useMutation, QueryKey } from "react-query";

import { Project } from "types/project";
import {
  useAddConfig,
  useDeleteConfig,
  useEditConfig,
} from "./use-optimistic-options";

export const useProjects = (param?: Partial<Projects>) => {
  const client = useHttp();
  // param 变化会触发重新请求
  return useQuery<Project[]>(["projects", param], () =>
    client("projects", { data: param })
  );
};

export const useEditProject = (queryKey: QueryKey) => {
  const client = useHttp();
  return useMutation(
    (params: Partial<Project>) =>
      client(`projects/${params.id}`, {
        method: "PATCH",
        data: params,
      }),
    useEditConfig(queryKey)
  );
};

export const useAddProject = (queryKey: QueryKey) => {
  const client = useHttp();

  return useMutation(
    (params: Partial<Project>) =>
      client(`projects`, {
        data: params,
        method: "POST",
      }),
    useAddConfig(queryKey)
  );
};

export const useDeleteProject = (queryKey: QueryKey) => {
  const client = useHttp();

  return useMutation(
    ({ id }: { id: number }) =>
      client(`projects/${id}`, {
        method: "DELETE",
      }),
    useDeleteConfig(queryKey)
  );
};

export const useProject = (id?: number) => {
  const client = useHttp();
  // 第三个参数，如果 id 是 undefined，则不请求
  return useQuery<Project>(
    ["project", { id }],
    () => client(`projects/${id}`),
    {
      enabled: Boolean(id),
    }
  );
};
```

src\screens\project-list\util.ts

```ts
import { useSetUrlSearchParam, useUrlQueryParam } from "utils/url";
import { useMemo } from "react";
import { useProject } from "utils/projects";

// 项目列表搜索的参数
export const useProjectsSearchParams = () => {
  const [params, setParams] = useUrlQueryParam(["name", "personId"]);
  return [
    useMemo(
      () => ({ ...params, personId: Number(params.personId) || undefined }),
      [params]
    ),
    setParams,
  ] as const;
};

export const useProjectModal = () => {
  const [{ projectCreate }, setProjectCreate] = useUrlQueryParam([
    "projectCreate",
  ]);
  const [{ editingProjectId }, setEditingProjectId] = useUrlQueryParam([
    "editingProjectId",
  ]);
  const { data: editingProject, isLoading } = useProject(
    Number(editingProjectId)
  );
  const setUrlParams = useSetUrlSearchParam();

  const open = () => setProjectCreate({ projectCreate: true });
  const close = () => {
    setUrlParams({ projectCreate: "", editingProjectId: "" });
  };
  const startEdit = (id: number) =>
    setEditingProjectId({ editingProjectId: id });

  return {
    projectModalOpen: projectCreate === "true" || Boolean(editingProjectId),
    open,
    close,
    startEdit,
    editingProject,
    isLoading,
  };
};

export const useProjectsQueryKey = () => {
  const [params] = useProjectsSearchParams();
  return ["projects", params];
};
```

src\screens\project-list\list.tsx

```tsx
import { Dropdown, Menu, Modal, Table, TableProps } from "antd";
import dayjs from "dayjs";
import { Link } from "react-router-dom";

import { Users } from "./search-panel";
import { Pin } from "components/pin";
import { ButtonNoPadding } from "components/lib";

import { useDeleteProject, useEditProject } from "utils/projects";
import { useProjectModal, useProjectsQueryKey } from "./util";
import { Project } from "types/project";

export interface Projects {
  id: number;
  name: string;
  personId: number;
  pin: boolean;
  organization: string;
  created: number;
}

interface ListProps extends TableProps<Projects> {
  users: Users[];
}

// ...props 的类型 type PropsType = Omit<ListProps, 'users'>
export const List = ({ users, ...props }: ListProps) => {
  const { mutate } = useEditProject(useProjectsQueryKey());
  const pinProject = (id: number) => (pin: boolean) => mutate({ id, pin });

  return (
    <Table
      rowKey={"id"}
      pagination={false}
      columns={[
        {
          title: <Pin checked={true} disabled={true} />,
          render(value, project) {
            return (
              <Pin
                checked={project.pin}
                onCheckedChange={pinProject(project.id)}
              />
            );
          },
        },
        {
          title: "名称",
          dataIndex: "name",
          // 按中文名排序
          sorter: (a, b) => a.name.localeCompare(b.name),
          render(value, project) {
            return (
              // 跳转显示为 /projects/4 的形式
              <Link to={String(project.id)}>{project.name}</Link>
            );
          },
        },
        {
          title: "部门",
          dataIndex: "organization",
        },
        {
          title: "负责人",
          render(value, project) {
            return (
              <span>
                {users.find((user) => user.id === project.personId)?.name ||
                  "未知"}
              </span>
            );
          },
        },
        {
          title: "创建时间",
          render(value, project) {
            return (
              <span>
                {project.created
                  ? dayjs(project.created).format("YYYY-MM-DD")
                  : "无"}
              </span>
            );
          },
        },
        {
          render(value, project) {
            return <More project={project} />;
          },
        },
      ]}
      {...props}
    ></Table>
  );
};

const More = ({ project }: { project: Project }) => {
  const { startEdit } = useProjectModal();
  const editProject = (id: number) => () => startEdit(id);
  const { mutate: deleteProject } = useDeleteProject(useProjectsQueryKey());
  const confirmDeleteProject = (id: number) => {
    Modal.confirm({
      title: "确定删除这个项目吗?",
      content: "点击确定删除",
      okText: "确定",
      onOk() {
        deleteProject({ id });
      },
    });
  };
  return (
    <Dropdown
      overlay={
        <Menu>
          <Menu.Item onClick={editProject(project.id)} key={"edit"}>
            编辑
          </Menu.Item>
          <Menu.Item
            onClick={() => confirmDeleteProject(project.id)}
            key={"delete"}
          >
            删除
          </Menu.Item>
        </Menu>
      }
    >
      <ButtonNoPadding type={"link"}>...</ButtonNoPadding>
    </Dropdown>
  );
};
```

src\screens\project-list\project-modal.tsx

```tsx
import styled from "@emotion/styled";
import { Button, Drawer, Form, Input, Spin } from "antd";
import { ErrorBox } from "components/lib";
import { UserSelect } from "components/user-select";
import { useEffect } from "react";
import {
  useProjectModal,
  useProjectsQueryKey,
} from "screens/project-list/util";
import { useAddProject, useEditProject } from "utils/projects";

export const ProjectModal = () => {
  const { projectModalOpen, close, editingProject, isLoading } =
    useProjectModal();
  const title = editingProject ? "编辑项目" : "创建项目";

  const useMutateProject = editingProject ? useEditProject : useAddProject;
  const {
    mutateAsync,
    error,
    isLoading: mutateLoading,
  } = useMutateProject(useProjectsQueryKey());

  const [form] = Form.useForm();
  const onFinish = (values: any) => {
    mutateAsync({ ...editingProject, ...values }).then(() => {
      form.resetFields();
      close();
    });
  };
  const closeModal = () => {
    form.resetFields();
    close();
  };

  useEffect(() => {
    form.setFieldsValue(editingProject);
  }, [editingProject, form]);

  return (
    <Drawer
      forceRender={true}
      onClose={closeModal}
      visible={projectModalOpen}
      width={"100%"}
    >
      <Container>
        {isLoading ? (
          <Spin size={"large"} />
        ) : (
          <>
            <h1>{title}</h1>
            <ErrorBox error={error} />
            <Form
              form={form}
              layout={"vertical"}
              style={{ width: "40rem" }}
              onFinish={onFinish}
            >
              <Form.Item
                label={"名称"}
                name={"name"}
                rules={[{ required: true, message: "请输入项目名" }]}
              >
                <Input placeholder={"请输入项目名称"} />
              </Form.Item>

              <Form.Item
                label={"部门"}
                name={"organization"}
                rules={[{ required: true, message: "请输入部门名" }]}
              >
                <Input placeholder={"请输入部门名"} />
              </Form.Item>

              <Form.Item label={"负责人"} name={"personId"}>
                <UserSelect defaultOptionName={"负责人"} />
              </Form.Item>

              <Form.Item style={{ textAlign: "right" }}>
                <Button
                  loading={mutateLoading}
                  type={"primary"}
                  htmlType={"submit"}
                >
                  提交
                </Button>
              </Form.Item>
            </Form>
          </>
        )}
      </Container>
    </Drawer>
  );
};

const Container = styled.div`
  height: 80vh;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
`;
```

效果图

![img](https://cdn.nlark.com/yuque/0/2022/gif/1614731/1653118802692-bda2cf12-3728-4581-adb6-3f0cad2e564c.gif)



![img](https://cdn.nlark.com/yuque/0/2022/png/1614731/1653121465369-f8ff0f93-6707-4d3e-9cb1-b2c9b522f3dd.png)































