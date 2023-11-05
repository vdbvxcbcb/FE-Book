# React-Hooks

## 为什么需要 Hook ？

### class  组件相对于函数式组件的优势

class 组件可以定义自己的 state，用来保存组件自己内部的状态；

- 函数式组件不可以，因为函数每次调用都会产生新的临时变量

class 组件有自己的生命周期，我们可以在对应的生命周期中完成自己的逻辑；

- 比如在`componentDidMount`中发送网络请求，并且该生命周期函数只会执行一次；
- 在学习 hooks 之前，如果在函数式组件中发送网络请求，意味着每次重新渲染都会重新发送一次网络请求

class 组件在状态改变时只会重新执行 render 函数以及我们希望重新调用的生命周期函数，

比如 componentDidUpdate 等；

- 函数式组件在重新渲染时，整个函数都会被执行，似乎没有什么地方可以只让它们调用一次

### class 组件 的缺点

**复杂组件变得难以理解：**

- 我们在最初编写一个 class 组件时，往往逻辑比较简单，并不会非常复杂。

- 但是随着业务的增多，我们的 class 组件会变得越来越复杂；

- 比如 componentDidMount 中，可能就会包含大量的逻辑代码：

  包括网络请求、一些事件的监听（还需要在 componentWillUnmount 中移除）；

- 而对于这样的 class 组件，实际上非常难以拆分：

  因为它们的逻辑往往混在一起，强行拆分反而会造成过度设计，增加代码的复杂度；

**组件复用状态很难**：

- 在前面为了一些状态的复用我们需要通过高阶组件或 render props；
- 像 redux 中 connect 或者 react-router中的 withRouter，这些高阶组件设计的目的就是为了状态的复用；
- 或者使用 Provider、Consumer 来共享一些状态，但是多次使用 Consumer 时，我们的代码就会存在很多嵌套；
- 这些代码让我们不管是编写和设计上来说，都变得非常困难；

### class 组件与 函数组件使用 Hook 对比

严格地说，类组件和函数组件是有差异的。

不同的写法，代表了不同的编程方法论。

**类（class）是数据和逻辑的封装。**

**函数一般来说，只应该做一件事，就是返回一个值。**

根据这种理念，React 的函数组件只应该做一件事情：根据输入的参数，返回组件的 HTML 代码。

**这种只进行单纯的数据计算（换算）的函数，在函数式编程里面称为 "纯函数"（pure function）。**

**函数式编程将那些跟数据计算无关的操作，都称为 "副作用" （side effect） 。**

如果函数内部直接包含产生副作用的操作，就不再是纯函数了，我们称之为不纯的函数。

**钩子（hook）就是 React 函数组件的副作用解决方案，用来为函数组件引入副作用。**

**函数组件的主体只应该用来返回组件的 HTML 代码，所有的其他操作（副效应）都必须通过钩子引入。**

**class 组件逻辑复用**

```jsx
const withWindowSize = Component => {
  // 产生一个高阶组件 WrappedComponent，只包含监听窗口大小的逻辑
  class WrappedComponent extends React.PureComponent {
    constructor(props) {
      super(props);
      this.state = {
        size: this.getSize()
      };
    }
    componentDidMount() {
      window.addEventListener("resize", this.handleResize); 
    }
    componentWillUnmount() {
      window.removeEventListener("resize", this.handleResize);
    }
    getSize() {
      return window.innerWidth > 1000 ? "large" ："small";
    }
    handleResize = ()=> {
      const currentSize = this.getSize();
      this.setState({
        size: this.getSize()
      });
    }
    render() {
      // 将窗口大小传递给真正的业务逻辑组件
      return <Component size={this.state.size} />;
    }
  }
  return WrappedComponent;
};
```

```jsx
class MyComponent extends React.Component{
  render() {
    const { size } = this.props;
    if (size === "small") return <SmallComponent />;
    else return <LargeComponent />;
  }
}

// 使用 withWindowSize 产生高阶组件，用于产生 size 属性传递给真正的业务组件
export default withWindowSize(MyComponent); 
```

**Hook 逻辑复用**

```jsx
const getSize = () => {
  return window.innerWidth > 1000 ? "large" : "small";
}

const useWindowSize = () => {
  const [size, setSize] = useState(getSize());
  useEffect(() => {
    const handler = () => {
      setSize(getSize())
    };
    window.addEventListener('resize', handler);
    return () => {
      window.removeEventListener('resize', handler);
    };
  }, []);
  
  return size;
};
```

```jsx
const Demo = () => {
  const size = useWindowSize();
  if (size === "small") return <SmallComponent />;
  else return <LargeComponent />;
};
```

**Hook 简化了逻辑复用，代码逻辑更为集中，容易理解和维护。**

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1632311727748-c4b29eed-dbf2-4503-851e-6992b6ccf114.jpeg)

Hook 的出现，可以解决上面提到的这些问题。

如果是一个旧的项目，并不需要直接将所有的代码重构为 Hooks，

它完全向下兼容，可以渐进式地来使用它。

**对于已有项目中的 Class 组件，是否要重构到函数组件和 Hooks 呢？**

**答案其实很明确：完全没必要。**

在 React 中，Class 组件和函数组件是完全可以共存的。

对于新的功能，更推荐使用函数组件。

而对于已有的功能，则维持现状就可以。

除非要进行大的功能改变，可以顺便把相关的类组件进行重构，否则是没有必要进行迁移的。

因为终究来说，能正确工作的代码就是好代码。

**Class 组件和函数组件是两种实现 React 应用的方式，**

**虽然它们是等价的，但是开发的思想有很大不同。**

**如果是从 Class 组件转换到 Hooks 的方式，那么很重要的一点就是，**

**要学会忘掉 Class 组件中的生命周期概念，**

**干万不要将原来习惯的 Class 组件开发方式映射到函数组件。**

比如，一个用于显示博客文章的组件接收一个文章的 id 作为参数，

然后根据这个 id 从服务器端获取文章的内容并显示出来。

那么当 id 变化的时候，你就需要检测到这个变化，并重新发送请求，显示在界面上。

```jsx
class BlogView extends React.Component {
  // ...
  componentDidMount() {
    // 组件第一次加载时去获取 Blog 数据
    fetchBlog(this.props.id);
  }
  componentDidUpdate(prevProps) {
    if (prevProps.id !== this.props.id) {
      // 当 Blog 的 id 发生变化时去获取博客文章
      fetchBlog(this.props.id);
    }
  }
  // ...
}
```

在 Class 组件中，需要在两个生命周期方法中去实现副作用，

一个是首次加载，另外一个则是每次 UI 更新后。

而在函数组件中不再有生命周期的概念，而是提供了 useEffect 这样一个Hook 专门用来执行副作用，

因此，只需下面的代码即可实现同样的功能

```jsx
function BlogView({ id }) {
  useEffect(() => {
    // 当 id 变化时重新获取博客文章
    fetchBlog(id);
  }, [id]); // 定义了依赖项 id
}
```

**在函数组件中你要思考的方式永远是：当某个状态发生变化时，我要做什么，**

**而不再是在 Class 组件中的某个生命周期方法中我要做什么。**

## Hook 是什么？

函数组件的问题是只是单纯地接收 props、绑定事件、返回 jsx，本身是无状态的组件，

依赖 props 传入的 handler 来响应数据（状态）的变更，

函数组件不能脱离类组件来存在！

**函数组件是否能脱离类组件独立存在，**

**关键在于让函数组件自身具备状态处理能力，**

**即在组件首次 render 之后，“组件自身能够通过某种机制再触发状态的变更并且引起 re-render ”，**

**而这种“机制”就是 Hooks！**

**Hook 是一个特殊的函数，它可以“钩入” React 的特性，**

**让我们在不编写 class 的情况下可以使用 state 以及其它的 React 特性。**

**Hooks 就是在函数组件内，负责钩进外部功能的函数。（想要什么种类的鱼，自己钓）**

**Hooks 的设计目的，就是加强版函数组件，完全不使用"类"，就能写出一个全功能的组件。**

**Hooks 的意思是，组件尽量写成纯函数，如果需要外部功能和副作用，就用钩子把外部代码"钩"进来**

在 React 中，Hooks 就是把某个目标结果钩到某个可能会变化的数据源或者事件源上，

当被钩到的数据或事件发生变化时，产生这个目标结果的代码会重新执行，产生更新后的结果。

例如，useState 是允许在 React 函数组件中添加 state 的 Hook。

## Hook 使用规则

- 只能在 **React 的函数组件中**调用 Hook。

  不要在其它 JavaScript 函数中调用（但是可以在自定义的 Hook 中调用 Hook）。

- 只能在**函数最外层**调用 Hook。

  **不要在循环、条件判断或者子函数中调用 Hook。**

复习一下，React 的函数组件是这样的：

```jsx
const Example = (props) => {
  // 你可以在这使用 Hook
  return <div />;
}
```

或是这样：

```jsx
function Example(props) {
  // 你可以在这使用 Hook
  return <div />;
}
```

React 提供了七大常用 Hook ：`useState`、`useEffect` 、`useContext` 、`useReducer`、`useCallback` 、`useMemo` 、`useRef` 

## 基础 Hook

### useState（组件状态数据）

#### state 保存数据的原则

通常来说，我们要遵循的一个原则就是：

**state 中永远不要保存可以通过计算得到的值。（建议使用 useMemo）**

**比如：**

**1.从 props 传递过来的值。**

有时候 props 传递过来的值无法直接使用，而是要通过一定的计算后再在 UI 上展示，比如说排序。

我们要做的是每次用的时候，都重新排序一下，

或者利用某些 cache 机制，而不是将结果直接放到 state 里。

**2.从 URL 中读到的值。**

比如有时需要读取 URL 中的参数，把它作为组件的一部分状态。

那么我们可以在每次需要用的时候从 URL 中读取，而不是读出来直接放到 state 里。

[可以从 props 里获取](https://www.jianshu.com/p/2f126b383192)

**3.从 cookie、localStorage 中读取的值。**

通常来说，也是每次要用的时候直接去读取，而不是读出来后放到 state 里。

#### state 的弊端

state 虽然便于维护状态，但也有自己的弊端，

一旦组件有自己的状态，意味着组件如果重新创建，就需要有恢复状态的过程，

这通常会让组件变得更复杂。

比如一个组件想在服务器端请求获取一个用户列表并显示，

如果把读取到的数据放到组件的 state 里，那么每个用到这个 state 的组件，就都需要重新获取一遍。

**如果通过一些状态管理框架，去管理所有组件的 state 的话，比如 Redux，那么组件本身就可以是无状态的。**

**无状态组件可以成为更纯粹的表现层，没有太多的业务逻辑，从而更易于使用、测试和维护。**

#### useState 介绍

```jsx
const [state, setState] = useState(initialState);
```

useState 来自 react，需要从 react 中导入，它是一个 hook

- 参数：初始值，如果不设置，则为 undefined；

  ​           唯一的、只读的参数，

  ​           该参数可传：任意类型的值或者返回任意类型值的函数。

- 返回值：数组，包含两个元素

  - 元素一：当前状态的值（useState 第一次调用为初始值）；

  - 元素二：设置状态值的函数，一个 setter 函数；

    ​              setter 函数可传任意类型的值，或者一个接收 state 旧值的函数（其返回值作为 state 新值）。

**为什么 useState 返回的是 array 而不是 object？**

如果 useState 返回的是数组，那么使用者可以对数组中的元素命名，代码看起来也比较干净

如果 useState 返回的是对象，在解构对象的时候必须要和 useState 内部实现返回的对象同名，

想要使用多次的话，必须得设置别名才能使用返回值

```jsx
// 第一次使用
const { state, setState } = useState(false);
// 第二次使用
const { state: counter, setState: setCounter } = useState(0) 
```

**为什么叫 useState 而不叫 createState?**

“create” 可能不是很准确，因为 state 只在组件首次渲染的时候被创建，不是每次都创建。

在下一次重新渲染时，useState 返回给我们当前的 state 。

#### useState 基本使用

下面示例中，点击 button 按钮后，会完成两件事情：

- 调用 setCount，设置一个新的值；
- 组件重新渲染，并且根据新的值返回 DOM 结构；

class 组件

```jsx
import React, { PureComponent } from 'react'

export default class Counter extends PureComponent {
  constructor(props) {
    super(props);

    this.state = {
      count: 0
    }
  }
  
  increment() {
    this.setState({count: this.state.count + 1});
  }

  decrement() {
    this.setState({count: this.state.count - 1});
  }

  render() {
    return (
      <div>
        <h2>当前计数: {this.state.count}</h2>
        <button onClick={e => this.increment()}>+1</button>
        <button onClick={e => this.decrement()}>-1</button>
      </div>
    )
  }
}
```

函数式组件使用 hook

```jsx
import React, { useState } from 'react';

export default function Counter() {
  const [count, setCount] = useState(0);

  return (
    <div>
      <h2>当前计数: {count}</h2>
      <button onClick={e => setCount(count + 1)}>+1</button>
      <button onClick={e => setCount(count - 1)}>-1</button>
    </div>
  )
}
```

React 在重新渲染时，会保留 state 状态，并不会每次都使用初始值。

```jsx
const [count, setCount] = useState(0);

// 等价于
const counter = useState(0);
const count = counter[0];
const setCount = counter[1];
```

#### class 组件与函数组件使用 hook  的不同

##### 设置初始值

不同于 class 组件的是，函数组件可以按需使用数字或字符串设置初始值，而不一定是对象。

class 组件

```jsx
this.state = {
  count: 0
}
```

函数组件使用 hook 

```jsx
const [count, setCount] = useState(0);
```

##### 读取 State

当我们想在 class 组件中显示当前的 count，我们需要读取 `this.state.count`：

```jsx
 <h2>当前计数: {this.state.count}</h2>
```

在函数组件中，我们可以直接用 `count`:

```jsx
 <h2>当前计数: {count}</h2>
```

##### 更新 State

在 class 组件中，我们需要调用 `this.setState()` 来更新 `count` 值：

```jsx
 <button onClick={e => this.setState({count: this.state.count + 1})}>+1</button>
```

在函数组件中，我们已经有了 `setCount` 和 `count` 变量，所以我们不需要 `this`:

```jsx
<button onClick={e => setCount(count + 1)}>+1</button>
```

##### 多个 useState  定义多个变量和复杂变量

class 组件的 state 只有一个，而且一般是一个对象。

useState 可以创建多个 state

```jsx
import React, { useState } from 'react';

export default function Home() {
  const [age, setAge] = useState(0);
  const [names, setNames] = useState(["abc", "cba"]);
  const [info, setInfo] = useState({name: "why", age: 18});
  const [students, setStudents] = useState([
    { id: 110, name: "why", age: 18 },
    { id: 111, name: "kobe", age: 30 },
    { id: 112, name: "lilei", age: 25 },
  ]);
  
  // 错误的做法
  // React 判断新旧 state 比较是相等的，不会返回新的 jsx ，所以函数不会重新调用
  // push() 会影响原来的数组，但是不会渲染出来
  // 如果使用 [...names, "nba"] 再次拷贝会拷贝 push 后的数组，造成结果错误
  function addFriend() {
    names.push("nba");
    console.log(names);
    setNames(names);
  }
  
  // 正确的做法
  function incrementAgeWithIndex(index) {
    const newStudents = [...students];
    newStudents[index].age += 1;
    setStudents(newStudents);
  }

  return (
    <div>
      <h2>当前年龄: {age}</h2>
      <button onClick={e => setAge(age + 1)}>age+1</button>
      <h2>朋友列表</h2>
      <ul>
        {
          names.map((item, index) => {
            return <li key={index}>{item}</li>
          })
        }
      </ul>
      <button onClick={e => setNames([...names, "nba"])}>添加好友</button>
      {/* 错误的做法 */}
      <button onClick={addFriend}>添加好友</button>
      <h2>我的信息:</h2>
      <div>我的名字: {info.name}</div>
      <button onClick={e => setInfo({...info, name: "lilei"})}>修改名字</button>
      <h2>学生列表</h2>
      <ul>
        {
          students.map((item, index) => {
            return (
              <li key={item.id}>
                <span>名字: {item.name} 年龄: {item.age}</span>
                  {/* 正确的做法 */}
                <button onClick={e => incrementAgeWithIndex(index)}>age+1</button>
              </li>
            )
          })
        }
      </ul>
    </div>
  )
}
```

####  函数式更新

根据新值是否依赖旧值来判断 useState 传参是使用函数还是 value 形式

```jsx
import React, {useState} from 'react';

export default function CounterHook() {
  const [count, setCount] = useState(() => 10);

  console.log("CounterHook渲染");

  function handleBtnClick() {
    // 一次加 10
    // setCount(count + 10);
    // setCount(count + 10);
    // setCount(count + 10);
    // setCount(count + 10);
    // 一次加 40
    // 等价于使用 for 循环
    setCount(prevCount => prevCount + 10);
    setCount(prevCount => prevCount + 10);
    setCount(prevCount => prevCount + 10);
    setCount(prevCount => prevCount + 10);
  }

  return (
    <div>
      <h2>当前计数: {count}</h2>
      <button onClick={e => setCount(count + 1)}>+1</button>
      <button onClick={e => setCount(count - 1)}>-1</button>
      <button onClick={e => setCount(prevCount => prevCount + 10)}>+10</button>
      <button onClick={handleBtnClick}>+10</button>
    </div>
  )
}
```

#### 惰性初始 state

如果初始 state 需要通过复杂计算获得，则可以传入一个函数，

在函数中计算并返回初始的 state，此函数只在初始渲染时被调用：

**initialState 参数只会在组件的初始渲染中起作用，后续渲染时会被忽略。**

**惰性：只在某个时候使用**

```jsx
const [state, setState] = useState(() => {
  const initialState = someExpensiveComputation(props);
  return initialState;
});
```

#### 函数组件的 setState 与 class 组件的 setState 区别

与 class 组件中的 setState 方法不同，useState 的 setter 函数不会自动合并更新对象，而是替换对象

可以用函数式的 setState （接收 state 旧值的 setter 函数）结合展开运算符来达到手动合并更新对象

```jsx
setState(prevState => {
  // 也可以使用 Object.assign
  return {...prevState, ...updatedValues};
})
```

```jsx
import React, {useState} from 'react';

export default function Position() {
  const [state, setState] = useState({ x: 0, y: 0 });
  
  const addX = () => {
    setState((prevState) => {
      return { ...prevState, x: prevState.x + 1 };
    })
  }
  
  const addY = () => {
    setState((prevState) => {
      return { ...prevState, y: prevState.y + 1 };
    })
  }
  
  return (
    <div className="Position">
      <button onClick={addX}>+1,此时x:{state.x}</button>
      <button onClick={addY}>+1,此时y:{state.y}</button>
    </div>
  );
}
```

```jsx
import React, { useState } from 'react';

function Cmp() {
  const [person, setPerson] = useState({name: 'abc', age: 18});

  const nameChangeHandler = (e) => {
    setPerson({...person, name: e.target.value});
  }

  const ageChangeHandler = (e) => {
    setPerson({...person, age: e.target.value});
  }

  return (
    <div>
      <input type='text' value={person.name} onChange={nameChangeHandler} />
      <input type='number' value={person.age} onChange={ageChangeHandler} />
      {JSON.stringify(person)}
    </div>
  )  
}

export default Cmp;
```

```jsx
import React, { useState } from 'react';

function Cmp() {
  const [str, setStr] = useState('');
  const [arr, setArr] = useState(['React', 'Node']);

  const inputChangeHandler = (e) => {
    setStr(e.target.value);
  }

  const addHeadHandler = (e) => {
    setArr([str, ...arr]); // 添加至头
    setStr('');
  }

  const addEndHandler = (e) => {
    setArr([...arr, str]); // 添加至尾
    setStr('');
  }

  const delHeadHandler = (e) => {
    let new_arr = [...arr];
    new_arr.shift(); // 从头删除一项
    setArr(new_arr);
  }

  const delEndHandler = (e) => {
    let new_arr = [...arr];
    new_arr.pop();  // 从尾删除一项
    setArr(new_arr);
  }

  const delByIndex = (e) => {
    let index = e.target.attributes.index.value;
    let new_arr = [...arr];
    new_arr.splice(index,1); // 删除当前项
    setArr(new_arr);
  }

  return (
    <div>
      <input type='text' value={str} onChange={inputChangeHandler} />
      <button onClick={addHeadHandler} >添加至头</button>
      <button onClick={addEndHandler} >添加至尾</button>
      <button onClick={delHeadHandler} >从头删除1项</button>
      <button onClick={delEndHandler} >从尾删除1项</button>
      <ul>
        {arr.map(
           (item, index) => {
             return (
               <li key={`item${index}`}>学习{index} -  {item}
                 <span index={index} onClick={delByIndex} style={{ cursor: 'pointer' }}>删除</span>
               </li>)
         })}
      </ul>
    </div>
  )
}

export default Cmp;
```

注意：

- 如果数据结构简单，可以根据数据结构将变量需要放在多个不同的 useState 中，避免全部放入一个对象中，大量使用类似`{...state, value}`形式。（<https://react.docschina.org/docs/hooks-faq.html#should-i-use-one-or-many-state-variables>）
- 如果数据结构复杂，建议使用 useReducer 管理组件的 state。

#### useState 的正确使用姿势

<https://juejin.cn/post/7046358484610187277#heading-8>

1. 将完全不相关的 state 拆分为多组 state。比如 size 和 position

2. 某些 state 是相互关联的，或者需要一起发生改变，就可以把它们合并为一组 state，例如：

```js
const [saveModalVisible, setSaveModalVisible] = useState(false);

const [modalInitData, setModalInitData] = useState(null);
=>
const [saveModalInfo, setSaveModalInfo] = useState({
    visible: false,
    data: null,
});
```

### useEffect（watch + 生命周期，根据依赖做什么）

<https://overreacted.io/zh-hans/a-complete-guide-to-useeffect/>

<https://zhuanlan.zhihu.com/p/60277120>

#### 什么是副作用？

与药物的副作用类似：减肥药（拉肚子），头孢（过敏），泰诺（头痛）。

但是，程序中的副作用是中性词，很多代码需要借助副作用才能实现

**简单地说，副作用就是，当调用函数的时候会对外部产生影响， 函数的执行可能会直接或间接影响其他函数的执行，例如修改了全局变量。** 

**副作用与纯函数相反，指一个函数处理了与返回值无关的事情，**

**输入参数一样，而输出结果不确定的情况就是副作用。**

纯函数输入一个 x 对应输出一个 y，副作用输入一个 x 可以对应输出多个 y 。

**从定义上来说，纯函数必须要能够根据相同的输入返回相同的输出；如果函数需要跟外部事物打交道，那么就无法保证这一点了。**

**通常来说，副作用是指一段和当前执行结果无关的代码。**

**比如说修改函数传入的参数或外部的某个变量，发起一个请求，修改 DOM 元素，甚至 console.log() 等等。**

在 React 中，state 状态的改变，生命周期，甚至 construtor 构建函数都可以看作是副作用的函数。

**事实上，类似于网络请求、手动更新 DOM 、一些事件的监听，**

**都是 React 更新 DOM 的一些副作用（Side Effects），**

**除了返回值以外，影响到函数外部的任何东西（显示器、网络、文件、全局变量等）的效果都叫 side effect ，**

**只要是跟函数外部环境发生的交互或者一个函数修改了自己范围之外的资源，那就叫做副作用。**

**所以对于完成这些功能的 Hook 被称之为 Effect Hook 。**

#### useEffect 介绍

```jsx
useEffect(effect, array);
```

useEffect 接收两个参数，没有返回值。

**默认情况下（不加第二个参数），**

**useEffect 的 effect 函数在第一次渲染之后和每次更新之后都会执行。**

第一个参数为 effect 函数，回调函数

- 该 effect 函数可以返回一个函数（returnFunction）。

- **注意：与 componentDidMount 、 componentDidUpdate 不同之处是，**

  **effect 函数触发时间总是在浏览器完成渲染之后。**

  **如果需要在渲染之前触发，需要使用 useLayoutEffect 。**

**为什么要在 effect 中返回一个函数？**

**这是 effect 可选的清除机制。每个 effect 都可以返回一个清除函数。**

**这个回调函数不仅在组件销毁时执行，**

**而且在每次 effect 重新执行之前都会执行，用于清理上一次 effect 的执行结果。**

第二个参数为 array， 依赖数组

- **如果不传，在页面渲染完成， 组件第一次渲染之后触发一次 effect，之后组件更新先触发一次 returnFunction（如果有的话），再触发一次 effect  。最后在组件销毁时触发一次 returnFunction 。**

- **如果为空数组 [] ， 组件第一次渲染之后只触发一次 effect，之后组件更新都不触发 effect 和 returnFunction  ， 只在组件销毁时触发一次 returnFunction 。通常用于清除引用的 DOM、取消绑定事件、清除定时器和网络请求。**

- **如果只需要在某些变量变化时触发 returnFunction 和 effect，需要将用到的变量放入数组，**

  **表示 useEffect 在哪些 state 发生变化时，才重新执行，实现性能优化。**

它类似生命周期使用时有三个使用场景：

1. 作为 componentDidMount 使用，第二个参数为空数组 `[]`
2. 作为 componentDidUpdate 使用，第二个参数为指定依赖
3. 作为 componentWillUnmount 使用，通过 return

总结一下，useEffect 让我们能够在下面四种时机去执行一个回调函数产生副作用：

1.每次 render 后执行：不提供第二个依赖项参数。比如

```jsx
useEffect(() => {})
```

2.仅第一次 render 后执行：提供一个空数组作为依赖项。比如

```jsx
useEffect(() => {},  [])
```

3.第一次以及依赖项发生变化后执行：提供依赖项数组。比如

```jsx
useEffect(() => {}, [deps])
```

4.组件 unmount 后执行：返回一个回调函数。比如

```jsx
useEffect(() => { return () => {} }, [])
```



```jsx
useEffect(() => {
  // componentDidMount + componentDidUpdate
  console.log('这里基本等价于 componentDidMount + componentDidUpdate');
  return () => {
    // componentWillUnmount
    console.log('这里基本等价于 componentWillUnmount');
  }
}, [deps])
```

“基本等价于”这个说法，什么意思呢？

指的就是这个写法并没有完全等价于传统的这几个生命周期方法。主要有两个原因：

1、一方面，

useEffect（callback）这个 Hook 接收的 callback，只有在依赖项变化时才被执行。

而传统的 componentDidUpdate 则一定会执行。

这样来看，Hook 的机制其实更具有语义化，

因为过去在 componentDidUpdate 中，

我们通常都需要手动判断某个状态是否发生变化，然后再执行特定的逻辑。

2、另一方面，

callback 返回的函数（一般用于清理工作）在下一次依赖项发生变化以及组件销毁之前执行，

而传统的 componentWillUnmount 只在组件销毁时才会执行。

也就是说，

**在函数组件的当次执行过程中，useEffect 中代码的执行是不影响渲染出来的 UI 的。**

**不要按照把 useEffect 对应到某个或者某几个生命周期的方法学习 useEffect 。**

**只要记住，useEffect 是每次组件 render 完后判断依赖并执行就可以了。**

**Effect Hook 可以让你来完成一些类似于 class 中生命周期的功能。**



#### 每个 effect 属于一次特定的渲染（Capture Value）

当 React 渲染组件时，会保存已使用的 effect，并在更新完 DOM 后执行它。

这个过程在每次渲染时都会发生，包括首次渲染。

**每次重新渲染，都会生成新的 effect，替换掉之前的。—— 每个 effect “属于”一次特定的渲染。**

**因为函数组件和 Hook 依赖闭包，**

**每一个组件内的函数（包括事件处理函数，effects，定时器或者 API 调用等等）**

**都会捕获某次渲染中定义的 props 和 state 。**

**也就是说：**

**每一次函数组件被渲染，都是一次全新的开始；**

**每一次全新的开始，所有的局部变量全都重来；**

**每一次函数组件都有自己全新的 props 与 state ，全新的事件处理， useEffect 有全新的 effect 执行 。**

在组件内什么时候去读取 props 或者 state 是无关紧要的。

因为它们不会改变。

**在单次渲染的范围内，props 和 state 始终保持不变。**

初始状态下 count 值为 0，

而随着按钮被点击，在每次 Render 过程中，count 的值都会被固化为 1、2、3：

```jsx
// During first render
function Counter() {
  // ...
  useEffect(
    // Effect function from first render
    () => {
      document.title = `You clicked ${0} times`;
    }
  );
  // ...
}

// After a click, our function is called again
function Counter() {
  // ...
  useEffect(
    // Effect function from second render
    () => {
      document.title = `You clicked ${1} times`;
    }
  );
  // ...
}

// After another click, our function is called again
function Counter() {
  // ...
  useEffect(
    // Effect function from third render
    () => {
      document.title = `You clicked ${2} times`;
    }
  );
  // ..
}
```

```jsx
function Counter() {
  const [count, setCount] = useState(0);

  useEffect(() => {
    setTimeout(() => {
      console.log(`You clicked ${count} times`);
    }, 3000);
  });

  return (
    <div>
      <p>You clicked {count} times</p>
      <button onClick={() => setCount(count + 1)}>
        Click me
      </button>
    </div>
  );
}
```

![img](https://cdn.nlark.com/yuque/0/2021/gif/1614731/1631340333176-f4a2c33a-7d36-4d91-93bf-f4278ea9dba5.gif)

#### 理解 Hook 的依赖

Hooks 提供了让你监听某个数据变化的能力。

这个变化可能会触发组件的刷新，也可能是去创建一个副作用，又或者是刷新一个缓存。

那么定义要监听哪些数据变化的机制，其实就是指定 Hooks 的依赖项。

不过需要注意的是，依赖项并不是内置 Hooks 的一个特殊机制，可以认为是一种设计模式。

有类似需求的 Hooks 都可以用这种模式去实现。

#### 依赖项原则

1. 依赖数组依赖的值最好不要超过 3 个，否则会导致代码会难以维护

2. 依赖项过多问题 

   a. 去掉不必要的依赖 

   b. 将 Hook 拆分为更小的单元，每个 Hook 依赖于各自的依赖数组 

   c. 合并相关的 state，将多个依赖值聚合为一个 

   d. 通过 setState 回调函数获取最新的 state，以减少外部依赖

   e. 通过 ref 来读取可变变量的值，不过需要注意控制修改它的途径

3. useEffect 和生命周期函数不做类比，useEffect 单纯用作处理副作用

#### 定义依赖项的注意点

1.依赖项中定义的变量一定要是在回调函数中会用到的，否则声明依赖项是没有意义的。

   **只有一种情况，需要把变量放到 deps 数组中，那就是当该变量变化时，需要触发 useEffect 函数执行。**

   **而不是因为 useEffect 中用到了这个变量！**

2.依赖项一般是一个常量数组，而不是一个变量。

3.**React 会使用浅比较来对比依赖项是否发生了变化，所以要特别注意数组或者对象类型。**

如果你是每次创建一个新对象，即使和之前的值是等价的，也会被认为是依赖项发生了变化。

这是一个刚开始使用 Hooks 时很容易导致 Bug 的地方。

```jsx
function Sample() {
  // 这里在每次组件执行时创建了一个新数组
  const todos = [{ text: 'Learn hooks.'}];
    
  useEffect(() => {
    console.log('Todos changed.');
  }, [todos]);
}
```

代码的原意可能是在 todos 变化的时候去产生一些副作用，

但是这里的 todos 变量是在函数内创建的，实际上每次都产生了一个新数组。

所以在作为依赖项的时候进行引用的比较，实际上被认为是发生了变化的。

##### 依赖项技巧

**避免使用数组、对象作为依赖项，而是使用对象的属性或基本数据类型值，**

**或者给数组使用 useRef**

```jsx
import React, { useEffect, useState, useRef } from "react";
export default function Home() {
  const [value, setValue] = useState(["b"]);
    
  const { current: a } = useRef(["a"]);
    
  useEffect(() => {
    setValue(["c"]);
  }, [a]);
}
```

**给对象使用 useMemo**

```jsx
import React, { useMemo, useEffect, useState } from "react";

export default function App() {
  const [count, setCount] = useState(0);
    
  const data = useMemo(() => ({
    is_fetched: false,
  }), []); // <- dependencies
    
  useEffect(() => {
    setCount(count + 1);
  }, [data]);

  return (
    <div className="App">
      <h1>Hello CodeSandbox</h1>
      <button onClick={() => setCount(count + 1)}>{count}</button>
      <h2>Start editing to see some magic happen!</h2>
    </div>
  );
}
```

**给函数使用 useCallback**

```jsx
import React, { useCallback, useEffect, useState } from "react";

export default function App() {
  const [count, setCount] = useState(0);
    
  const getData = useCallback(() => {
    return window.localStorage.getItem("token");
  }, []); // <- dependencies
    
  const [dep, setDep] = useState(getData());
    
  useEffect(() => {
    setCount(count + 1);
  }, [getData]);
    
  return (
    <div className="App">
      <h1>Hello CodeSandbox</h1>
      <button onClick={() => setCount(count + 1)}>{count}</button>
      <h2>Start editing to see some magic happen!</h2>
    </div>
  );
}
```

<https://www.zhihu.com/question/479470936>

#### 关于 useEffect 的依赖项

**如果当前渲染中的依赖项和上一次运行 effect 依赖项的所有值都一样，**

**因为没有什么需要同步，React 会自动跳过这次 effect 。**

**如果设置了错误的依赖，比如我们将 [] 设为 effect 的依赖，新的 effect 函数不会运行。**

在第一次渲染中，count 是 0 。

因此，setCount(count + 1) 在第一次渲染中等价于 setCount(0 + 1) 。

既然我们设置了 [] 依赖，effect 不会重新运行，它后面每一秒都会调用 setCount(0 + 1) ：

```jsx
const count = // ...

useEffect(() => {
  const id = setInterval(() => {
    setCount(count + 1);
  }, 1000);
  return () => clearInterval(id);
}, []);
```

我们对 React 撒谎说我们的 effect 不依赖组件内的任何值，可实际上我们的 effect 有依赖！

我们的 effect 依赖 count  —— 它是组件内的值（不过在 effect 外面定义）

因此，设置 [] 为依赖会引入一个 bug 。

React 会对比依赖，并且跳过后面的 effect：

![img](https://cdn.nlark.com/yuque/0/2021/gif/1614731/1631342758809-5269188c-43a0-4589-91e9-072d4b2f86e3.gif)

##### 错误依赖的两种解决办法

**设置正确的依赖**

在依赖中包含所有 effect 中用到的组件内的值

```jsx
function Counter() {
  const [count, setCount] = useState(0);

  useEffect(() => {
    const id = setInterval(() => {
      setCount(count + 1);
    }, 1000);
    return () => clearInterval(id);
  }, [count]);

  return <h1>{count}</h1>;
}
```

**函数式更新**

修改 effect 内部的代码，确保它包含的值在需要的时候可以发生变更。

```jsx
function Counter() {
  const [count, setCount] = useState(0);

  useEffect(() => {
    const id = setInterval(() => {
      setCount(c => c + 1);
    }, 1000);
    return () => clearInterval(id);
  }, []);

  return <h1>{count}</h1>;
}
```

**注意：useEffect 要么什么都不返回，要么就要返回一个清除函数**

#### 常见问题

##### 如何用 useEffect 模拟 componentDidMount 生命周期？

虽然可以使用 useEffect(fn, [])，但它们并不完全相等。

和 componentDidMount 不一样，useEffect 会捕获 props 和 state 。

所以即便在回调函数里，拿到的还是初始的 props 和 state 。

如果想得到“最新”的值，可以使用 ref 。

不过，通常会有更简单的实现方式，所以并不一定要用 ref 。

##### 为什么有时候在 effect 里拿到的是旧的 state 或 prop 呢？（闭包陷阱）

https://zhuanlan.zhihu.com/p/113216415

```jsx
function ErrorDemo() {
  const [count, setCount] = useState(0);
  const dom = useRef(null);
  useEffect(() => {
    dom.current.addEventListener('click', () => setCount(count + 1));
  }, []);
  return <div ref={dom}>{count}</div>;
}
```

这段代码的初始想法是：每当用户点击 dom，count 就加 1 。

理想中的效果是一直点，一直加。

但实际效果是 {count} 到「1」以后就加不上了。

**也是 useEffect(() => {}, []) 时产生的问题**

**因为每次 count 都是重新声明的变量，指向一个全新的数据；**

**每次的 setCount 虽然是重新声明的，但指向的是同一个引用。**

**每次 render，内部的 count 其实都是全新的一个变量。**

**那我们绑定的点击事件方法，也就是：setCount(count + 1) ，**

**这里的 count，其实一直指的是首次 render 时的那个 count，所以一直是 0 ，**

**因此触发 setCount，一直是设置 count 为 1 。**

状态变更 触发 页面渲染的本质是什么？

本质就是 ui = fn(props, state, context) 。props、内部状态、上下文的变更，

都会导致渲染函数（此处就是ErrorDemo）的重新执行，然后返回新的 view。

那现在问题来了， ErrorDemo 这个函数执行了多次，第一次函数内部的 count 跟后面几次的 count 会有关系吗？

这么一想，感觉又应该没有关系了。

那为什么 第二次又知道 count 是 1，而不是 0 了呢？

第一次的 setCount 跟后面的是同一个函数吗？

这背后涉及到 hooks 的一些底层原理，也关系到了为什么 hooks 的声明需要声明在函数顶部，不允许在条件语句中声明。

**因为 deps 设置了空数组，那多次 render，只有第一次会执行传入的函数。**

**state 是变化的，但是 useEffect 中执行的那个函数却一直引用着最开始的 state。**

**解决思路1：函数式更新消除依赖（推荐）**

利用 setCount 的另一个用法：函数式更新

不需要关心当前值是什么，只要对 “旧的值” 进行修改即可。

这样虽然代码永远运行在第一次 render 中，但总是可以访问到最新的 state。

```jsx
function ErrorDemo() {
  const [count, setCount] = useState(0);
  const dom = useRef(null);
  useEffect(() => {
    dom.current.addEventListener('click', () => setCount(prevCount => ++prevCount));
  }, []);
  return <div ref={dom}>{count}</div>;
}
```

**解决思路2：重新绑定事件**

每次重新 render 前移除事件，render 后绑定事件

```jsx
function ErrorDemo() {
  const [count, setCount] = useState(0);
  const dom = useRef(null);
  useEffect(() => {
    const $dom = dom.current;
    const event = () => {
      console.log(count);
      setCount(prev => ++prev);
    };
    $dom.addEventListener('click', event);
    return () => $dom.removeEventListener('click', event);
  }, [count]);
  return <div ref={dom}>{count}</div>;
}
```

**解决思路3：使用 useRef（推荐）**

```jsx
function ErrorDemo() {
  const [count, setCount] = useState(0);
  const countRef = useRef(count);
  useEffect(() => {
    dom.current.addEventListener('click', () => {
      console.log(countRef.current);
      setCount(prevCount => {
        const newCount = ++prevCount;
        countRef.current = newCount;
        return newCount;
      });
    });
  }, []);
  return <div ref={dom}>{count}</div>;
}
```

Effect 拿到的总是定义它的那次渲染中的 props 和 state 。

这能够避免一些 bugs，但在一些场景中又会有些讨人嫌。

对于这些场景，你可以明确地使用可变的 ref 保存一些值。

如果你觉得在渲染中拿到了一些旧的 props 和 state，且不是你想要的，你很可能遗漏了一些依赖。

**模拟 class 组件拿到最新值（绕过 Capture Value）**

有时候你可能想在 effect 的回调函数里读取最新的值而不是捕获的值，

下面代码中的计数器版本模拟了 class 中的行为：

从过去渲染中的函数里读取未来的 props 和 state，你是在逆潮而动，虽然它并没有错。

```jsx
function Example() {
  const [count, setCount] = useState(0);
  const latestCount = useRef(count);

  useEffect(() => {
    // Set the mutable latest value
    latestCount.current = count;
    setTimeout(() => {
      // Read the mutable latest value
      console.log(`You clicked ${latestCount.current} times`);
    }, 3000);
  })
  // ...
```

![img](https://cdn.nlark.com/yuque/0/2021/gif/1614731/1631340754361-c698ecad-6817-45a3-a4b3-b41c7078ff48.gif)

**在延迟调用的场景下，一定会存在闭包问题。** 

什么是延迟调用？

1. 使用 setTimeout、setInterval、事件监听、Promise.then 等（定时器和事件都一样，必须有卸载函数）
2. useEffect 的卸载函数

```jsx
const getUsername = () => {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      resolve('John');
    }, 3000);
  })
}

function Demo() {
  const [count, setCount] = useState(0);

  // setTimeout 会造成闭包问题
  useEffect(() => {
    const timer = setTimeout(() => {
      console.log(count);
    }, 3000);
    return () => {
      clearTimeout(timer);
    }
  }, [])

  // setInterval 会造成闭包问题
  useEffect(() => {
    const timer = setInterval(() => {
      console.log(count);
    }, 3000);
    return () => {
      clearInterval(timer);
    }
  }, [])

  // Promise.then 会造成闭包问题
  useEffect(() => {
    getUsername().then(() => {
      console.log(count);
    });
  }, [])

  // useEffect 卸载函数会造成闭包问题
  useEffect(() => {
    return () => {
      console.log(count);
    }
  }, []);

  return (
    <button
      onClick={() => setCount(c => c + 1)}
    >
      click
    </button>
  )
}
```

在以上示例代码中，四种情况均会出现闭包问题，永远输出 0。

这四种情况的根因都是一样的，我们看一下代码的执行顺序：

1. 组件初始化，此时 count = 0

2. 执行 useEffect，此时 useEffect 的函数执行，JS 记录了对 count=0 的引用关系
3. 点击 button，count 变化，但对之前的引用已经无能为力了

**解决办法**

```js
const [count, setCount] = useState(0);

// 通过 ref 来记忆 state 最新的 count
const countRef = useRef(count);
countRef.current = count;

useEffect(() => {
  const timer = setTimeout(() => {
    console.log(countRef.current)
  }, 3000);
  return () => {
    clearTimeout(timer);
  }
}, [])
```

##### 我应该把函数当做 effect 的依赖吗？

一般建议把不依赖 props 和 state 的函数提到你的组件外面，而是应该把那些仅被 effect 使用的函数放到 effect 里面，如果这样做了以后，

你的 effect 还是需要用到组件内的函数（包括通过 props 传进来的函数），可以在定义它们的地方用 useCallback 包一层。

##### 为什么有时候会出现无限重复请求的问题？

**这个通常发生于你在 effect 里做数据请求并且没有设置 effect 依赖参数的情况。**

没有设置依赖，effect 会在每次渲染后执行一次，然后在 effect 中更新了状态引起渲染并再次触发effect 

**无限循环的发生也可能是因为你设置的依赖总是会改变。**

你可以通过一个一个移除的方式排查出哪个依赖导致了问题。

但是，移除你使用的依赖（或者盲目地使用[]）通常是一种错误的解决方式。

你应该做的是解决问题的根源。

举个例子，依赖是函数可能会导致这个问题，

（useEffect 的第一个参数 effect 里有 setXxx() 触发了渲染，函数变成新的函数）

**你可以把函数体移到 useEffect 内部，或者把函数提到组件外面，或者用 useCallback 包一层。**

**useMemo 可以做类似的事情以避免重复生成对象。**

具体方案：<https://www.zhihu.com/question/479470936>

#### useEffect 基本使用

class 组件

```jsx
import React, { PureComponent } from 'react'

export default class ClassCounterTitleChange extends PureComponent {
  constructor(props) {
    super(props);

    this.state = {
      counter: 0
    }
  }

  componentDidMount() {
    // 1.修改 DOM，初始化（第一次渲染）时获得 state 初始值
    document.title = this.state.counter;

    // 2.订阅事件
    console.log("订阅一些事件");

    // 3.网络请求
  }

  componentWillUnmount() {
    // 2.取消订阅事件
    console.log("取消事件订阅");
  }

  componentDidUpdate() {
    // 1.state 更新时获得 state 最新值
    document.title = this.state.counter;
  }

  render() {
    return (
      <div>
        <h2>当前计数: {this.state.counter}</h2>
        <button onClick={e => this.setState({counter: this.state.counter + 1})}>+1</button>
      </div>
    )
  }
}
```

**无需清除的 effect**

```jsx
import React, { useState, useEffect } from 'react'

export default function HookCounterChangeTitle() {
  const [counter, setCounter] = useState(0);
  
  // componentDidMount 和 componentDidUpdate 合二为一
  useEffect(() => {
    document.title = counter;
  })

  return (
    <div>
      <h2>当前计数: {counter}</h2>
      <button onClick={e => setCounter(counter + 1)}>+1</button>
    </div>
  )
}
```

**为什么在组件内部调用 `useEffect`？** 

将 `useEffect` 放在组件内部可以让我们在 effect 中直接访问 `count` state 变量（或其他 props）。

我们不需要特殊的 API 来读取它 —— 它已经保存在函数作用域中。

Hook 使用了 JavaScript 的闭包机制，

所以不用在 JavaScript 已经提供了解决方案的情况下引入特定的 React API 。

**需要清除的 effect**

```jsx
import React, { useEffect, useState } from 'react'

export default function EffectHookCancelDemo() {

  const [count, setCount] = useState(0);
 
  // 传 [] ，componentDidMount 和 componentWillUnmount 合二为一
  useEffect(() => {
    // 第一次渲染时在 returnFunction 之前执行，重新 render 时在 returnFunction 之后执行
    // componentDidMount
    console.log("订阅一些事件");
    
    // componentWillUnmount
    return () => {
      console.log("取消订阅事件")
    }
  }, []);

  return (
    <div>
      <h2>EffectHookCancelDemo</h2>
      <h2>{count}</h2>
      <button onClick={e => setCount(count + 1)}>+1</button>
    </div>
  )
}
```

```jsx
import React, { useState, createContext } from 'react';
import EffectHookCancelDemo from './EffectHookCancelDemo';

export default function App() {
  const [show, setShow] = useState(true);

  return (
    <div>
      {show && <EffectHookCancelDemo/>} 
      <button onClick={e => setShow(!show)}>切换</button>
    </div>
  )
}
```

如果第二个参数不加 [] ，

点击 +1 则会不断地在 componentDidUpdate 时先触发 returnFunction 再触发 effect，

先打印取消订阅事件，

接着还打印订阅事件，

returnFunction 和 effect 都会执行一次。

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1629295095924-01b6b9bc-9226-490b-9c46-44bb9d65b1dc.jpeg)

第二个参数为 []，

点击切换， 

componentWillUnmount 时触发 returnFunction 取消订阅事件，

componentDidMount 时触发 effect 订阅事件。

#### 多个 useEffect 的使用

```jsx
import React, { useState, useEffect } from 'react'

export default function MultiEffectHookDemo() {
  const [count, setCount] = useState(0);
  const [isLogin, setIsLogin] = useState(true);
  
  // 按顺序执行
  // 1.修改 DOM，只在 count 改变时执行
  useEffect(() => {
    console.log("修改DOM", count);
  }, [count]);
  
   // 2.订阅事件
  useEffect(() => {
    console.log("订阅事件");
  }, []);
  
  // 3.网络请求
  useEffect(() => {
    console.log("网络请求");
  }, []);

  return (
    <div>
      <h2>MultiEffectHookDemo</h2>
      <h2>{count}</h2>
      <button onClick={e => setCount(count + 1)}>+1</button>
      <h2>{isLogin ? "Tom": "未登录"}</h2>
      <button onClick={e => setIsLogin(!isLogin)}>登录/注销</button>
    </div>
  )
}
```

#### 定时器清除（事件监听同理，重要）

```js
useEffect(() => {
  const interval = setInterval(() => {
    setCount(c => c + 1);
  }, 500);
  return () => clearInterval(interval);
}, []);

function App() {
  useEffect(() => {
    const timer = setTimeout(() => {
        console.log('print log after 1s!');
    }, 1000);
    window.addEventListener('load', loadHandle);

    return () => window.removeEventListener('load', loadHandle); // 执行清理
  }, []);
}
```

#### 网络请求

```jsx
import React, { useState, useEffect } from 'react';
import axios from 'axios';
 
function App() {
  const [data, setData] = useState({ hits: [] });
 
  useEffect(() => {
    const fetchData = async () => {
      const result = await axios(
        'https://hn.algolia.com/api/v1/search?query=redux',
      );
 
      setData(result.data);
    };
 
    fetchData();
  }, []);
 
  return (
    <ul>
      {data.hits.map(item => (
        <li key={item.objectID}>
          <a href={item.url}>{item.title}</a>
        </li>
      ))}
    </ul>
  );
}
 
export default App;
```

#### 网络请求 loading

```jsx
import React, { Fragment, useState, useEffect } from 'react';
import axios from 'axios';
 
function App() {
  const [data, setData] = useState({ hits: [] });
  const [query, setQuery] = useState('redux');
  const [url, setUrl] = useState(
    'https://hn.algolia.com/api/v1/search?query=redux',
  );
  const [isLoading, setIsLoading] = useState(false);
 
  useEffect(() => {
    const fetchData = async () => {
      setIsLoading(true);
 
      const result = await axios(url);
 
      setData(result.data);
      setIsLoading(false);
    };
 
    fetchData();
  }, [url]);
 
  return (
    <Fragment>
      <input
        type="text"
        value={query}
        onChange={event => setQuery(event.target.value)}
      />
      <button
        type="button"
        onClick={() =>
          setUrl(`http://hn.algolia.com/api/v1/search?query=${query}`)
        }
      >
        Search
      </button>
 
      {isLoading ? (
        <div>Loading ...</div>
      ) : (
        <ul>
          {data.hits.map(item => (
            <li key={item.objectID}>
              <a href={item.url}>{item.title}</a>
            </li>
          ))}
        </ul>
      )}
    </Fragment>
  );
}
 
export default App;
```

#### 按钮手动触发网络请求

```jsx
function App() {
  const [data, setData] = useState({ hits: [] });
  const [query, setQuery] = useState('redux');
  const [url, setUrl] = useState(
    'https://hn.algolia.com/api/v1/search?query=redux',
  );
 
  useEffect(() => {
    const fetchData = async () => {
      const result = await axios(url);
 
      setData(result.data);
    };
 
    fetchData();
  }, [url]);
 
  return (
    <Fragment>
      <input
        type="text"
        value={query}
        onChange={event => setQuery(event.target.value)}
      />
      <button
        type="button"
        onClick={() =>
          setUrl(`http://hn.algolia.com/api/v1/search?query=${query}`)
        }
      >
        Search
      </button>
 
      <ul>
        {data.hits.map(item => (
          <li key={item.objectID}>
            <a href={item.url}>{item.title}</a>
          </li>
        ))}
      </ul>
    </Fragment>
  );
}
```

#### 请求错误处理

```jsx
import React, { Fragment, useState, useEffect } from 'react';
import axios from 'axios';
 
function App() {
  const [data, setData] = useState({ hits: [] });
  const [query, setQuery] = useState('redux');
  const [url, setUrl] = useState(
    'https://hn.algolia.com/api/v1/search?query=redux',
  );
  const [isLoading, setIsLoading] = useState(false);
  const [isError, setIsError] = useState(false);
 
  useEffect(() => {
    const fetchData = async () => {
      setIsError(false);
      setIsLoading(true);
 
      try {
        const result = await axios(url);
 
        setData(result.data);
      } catch (error) {
        setIsError(true);
      }
 
      setIsLoading(false);
    };
 
    fetchData();
  }, [url]);
 
  return (
    <Fragment>
      <input
        type="text"
        value={query}
        onChange={event => setQuery(event.target.value)}
      />
      <button
        type="button"
        onClick={() =>
          setUrl(`http://hn.algolia.com/api/v1/search?query=${query}`)
        }
      >
        Search
      </button>
 
      {isError && <div>Something went wrong ...</div>}
 
      {isLoading ? (
        <div>Loading ...</div>
      ) : (
        <ul>
          {data.hits.map(item => (
            <li key={item.objectID}>
              <a href={item.url}>{item.title}</a>
            </li>
          ))}
        </ul>
      )}
    </Fragment>
  );
}
 
export default App;
```

#### 表单提交请求

```jsx
function App() {
  ...
 
  return (
    <Fragment>
      <form onSubmit={event => {
        setUrl(`http://hn.algolia.com/api/v1/search?query=${query}`);
 
        event.preventDefault();
      }}>
        <input
          type="text"
          value={query}
          onChange={event => setQuery(event.target.value)}
        />
        <button type="submit">Search</button>
      </form>
 
      {isError && <div>Something went wrong ...</div>}
 
      ...
    </Fragment>
  );
```

#### 自定义 Hook 获取数据

```jsx
import React, { Fragment, useState, useEffect } from 'react';
import axios from 'axios';
 
const useDataApi = (initialUrl, initialData) => {
  const [data, setData] = useState(initialData);
  const [url, setUrl] = useState(initialUrl);
  const [isLoading, setIsLoading] = useState(false);
  const [isError, setIsError] = useState(false);
 
  useEffect(() => {
    const fetchData = async () => {
      setIsError(false);
      setIsLoading(true);
 
      try {
        const result = await axios(url);
 
        setData(result.data);
      } catch (error) {
        setIsError(true);
      }
 
      setIsLoading(false);
    };
 
    fetchData();
  }, [url]);
 
  return [{ data, isLoading, isError }, setUrl];
};
 
function App() {
  const [query, setQuery] = useState('redux');
  const [{ data, isLoading, isError }, doFetch] = useDataApi(
    'https://hn.algolia.com/api/v1/search?query=redux',
    { hits: [] },
  );
 
  return (
    <Fragment>
      <form
        onSubmit={event => {
          doFetch(
            `http://hn.algolia.com/api/v1/search?query=${query}`,
          );
 
          event.preventDefault();
        }}
      >
        <input
          type="text"
          value={query}
          onChange={event => setQuery(event.target.value)}
        />
        <button type="submit">Search</button>
      </form>
 
      {isError && <div>Something went wrong ...</div>}
 
      {isLoading ? (
        <div>Loading ...</div>
      ) : (
        <ul>
          {data.hits.map(item => (
            <li key={item.objectID}>
              <a href={item.url}>{item.title}</a>
            </li>
          ))}
        </ul>
      )}
    </Fragment>
  );
}
 
export default App;
```

#### useReducer 与 自定义 Hook 结合

```jsx
const dataFetchReducer = (state, action) => {
  switch (action.type) {
    case 'FETCH_INIT':
      return {
        ...state,
        isLoading: true,
        isError: false
      };
    case 'FETCH_SUCCESS':
      return {
        ...state,
        isLoading: false,
        isError: false,
        data: action.payload,
      };
    case 'FETCH_FAILURE':
      return {
        ...state,
        isLoading: false,
        isError: true,
      };
    default:
      throw new Error();
  }
};

const useDataApi = (initialUrl, initialData) => {
  const [url, setUrl] = useState(initialUrl);
 
  const [state, dispatch] = useReducer(dataFetchReducer, {
    isLoading: false,
    isError: false,
    data: initialData,
  });
 
  useEffect(() => {
    const fetchData = async () => {
      dispatch({ type: 'FETCH_INIT' });
 
      try {
        const result = await axios(url);
 
        dispatch({ type: 'FETCH_SUCCESS', payload: result.data });
      } catch (error) {
        dispatch({ type: 'FETCH_FAILURE' });
      }
    };
 
    fetchData();
  }, [url]);
 
  return [state, setUrl];
};
```

#### 中止 useEffect 获取数据

```jsx
const useDataApi = (initialUrl, initialData) => {
  const [url, setUrl] = useState(initialUrl);
 
  const [state, dispatch] = useReducer(dataFetchReducer, {
    isLoading: false,
    isError: false,
    data: initialData,
  });
 
  useEffect(() => {
    let didCancel = false;
 
    const fetchData = async () => {
      dispatch({ type: 'FETCH_INIT' });
 
      try {
        const result = await axios(url);
 
        if (!didCancel) {
          dispatch({ type: 'FETCH_SUCCESS', payload: result.data });
        }
      } catch (error) {
        if (!didCancel) {
          dispatch({ type: 'FETCH_FAILURE' });
        }
      }
    };
 
    fetchData();
 
    return () => {
      didCancel = true;
    };
  }, [url]);
 
  return [state, setUrl];
};
```

实际上并没有中止数据获取——这可以通过 Axios Cancellation 实现，

但是对于卸载的组件可以不再设置状态。 

### useContext（数据共享）

#### useContext 介绍

**useContext 用于实现跨层级的数据共享**

之前，我们要在组件中使用共享的 Context 有两种方式：

- 类组件可以通过 类名.contextType = MyContext 方式，在类中获取 context；
- 多个 Context 或者在函数式组件中通过 MyContext.Consumer 方式共享 context；

但是多个 Context 共享时的方式会存在大量的嵌套

Context Hook 允许我们通过 Hook 来直接获取某个 Context 的值

```jsx
const value = useContext(MyContext);
```

**接收一个 context 对象（React.createContext 的返回值）并返回该 context 的当前值。**

当前 context 值由距离当前组件最近的上层组件的 <MyContext.Provider> 的 value prop 决定。

**当组件上层最近的 <MyContext.Provider> 更新时，该 Hook 会触发重新渲染，**

**并使用 <MyContext.Provider> 最新的  value 值。**

即使祖先使用 React.memo 或 shouldComponentUpdate，

也会在组件本身使用 useContext 时重新渲染。

#### useContext 基本使用

它的使用可以分为三个步骤：

1. 使用`C = createContext(initial)` 创建上下文
2. 使用`<C.provider>` 圈定作用域
3. 在作用域内使用 `useContext(C)` 来使用上下文

App.js 中提供 Context

**xxxContext = createContext() =>  xxxContext.Provider 提供全局数据**

```jsx
import React, { useState, createContext } from 'react';

export const UserContext = createContext();
export const TokenContext = createContext();

export const ThemeContext = createContext();

export default function App() {
  return (
    <div>
      <UserContext.Provider value={{name: "why", age: 18}}>
        <ThemeContext.Provider value={{fontSize: "30px", color: "red"}}>
          <ContextHookDemo/>
        </ThemeContext.Provider>
      </UserContext.Provider> 
    </div>
  )
}
```

ContextHookDemo.js 函数式组件中使用 Context Hook：

**useContext(xxxContext) 获取全局数据**

```jsx
import React, { useContext } from 'react';

import { UserContext, ThemeContext } from "../App";

export default function ContextHookDemo(props) {
  const user = useContext(UserContext);
  const theme = useContext(ThemeContext);

  console.log(user, theme);

  return (
    <div>
      <h2>ContextHookDemo</h2>
    </div>
  )
}
```

效果图

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1629300930254-6a77fbc8-92db-4c12-b00b-e65d5cc24759.jpeg)

官网示例（切换主题）

```jsx
const themes = {
  light: {
    foreground: "#000000",
    background: "#eeeeee"
  },
  dark: {
    foreground: "#ffffff",
    background: "#222222"
  }
};

const ThemeContext = React.createContext(themes.light);

function App() {
  return (
    <ThemeContext.Provider value={themes.dark}>
      <Toolbar />
    </ThemeContext.Provider>
  );
}

function Toolbar(props) {
  return (
    <div>
      <ThemedButton />
    </div>
  );
}

function ThemedButton() {
  const theme = useContext(ThemeContext);
  return (
    <button style={{ background: theme.background, color: theme.foreground }}>
      I am styled by theme context!
    </button>
  );
}
```

**Context 看上去就是一个全局的数据，**

**为什么要设计这样一个复杂的机制，而不是直接用一个全局的变量去保存数据呢？**

**答案就是为了能够进行数据的绑定。**

**当这个 Context 的数据发生变化时，使用这个数据的组件就能够自动刷新。**

但如果没有 Context，而是使用一个简单的全局变量，就很难去实现了。

不过刚才我们看到的其实是一个静态的使用 Context 的例子，直接用了 themes.dark 作为 Context 的值。

那么如何让它变得动态呢？

```jsx
// ...
function App() {
  // 使用 state 来保存 theme 从而可以动态修改
  const [theme, setTheme] = useState("light");
  // 切换 theme 的回调函数
  const toggleTheme = useCallback(() => {
    setTheme((theme) => (theme === "light" ? "dark" : "light"));
  }, []);
  return (
    // 使用 theme state 作为当前 Context
    <ThemeContext.Provider value={themes[theme]}>
      <button onClick={toggleTheme}>Toggle Theme</button>
      <Toolbar />
    </ThemeContext.Provider>
  );
}
```

**在 React 的开发中，除了像 Theme、Language 等一目了然需要全局设置的变量外，**

**我们很少会使用 Context 来做太多数据的共享。**

需要再三强调的是，

**Context 更多的是提供一个强大的机制，让 React 应用具备定义全局的响应式数据的能力。**

此外，很多状态管理框架，比如 Redux，

正是利用了 Context 的机制来提供一种更加可控的组件之间的状态管理机制。

## 额外的 Hook

### useReducer（处理复杂 state）

#### useReducer 介绍

```jsx
const [state, dispatch] = useReducer(reducer, initialArg, init);
```

useReducer 接收一个形如 (state, action) => newState 的 reducer 函数，

并返回当前的 state 以及与其配套的 dispatch 方法。

reducer 可以理解为 “折叠”，就像从左往右折叠被子一样，最终折叠成一块。

useReducer 是一个加强版的 useState 。

**当你想更新一个状态，并且这个状态更新依赖于另一个状态的值时，**

**你需要用 useReducer 去替换它们。**

**当你写类似 setSomething(something => ...) 这种代码的时候，也许就是使用 reducer 的契机。**

**reducer 可以把组件内发生了什么( actions )和状态如何响应并更新进行分开表述。**

**useReducer 仅仅是 useState 的一种替代方案，更不能替代 Redux**

- 在某些场景下，如果 state 的处理逻辑比较复杂，我们可以通过 useReducer 来对其进行拆分；
- 或者这次修改的 state 需要依赖之前的 state 时，也可以使用；

**使用场景**

- state 是一个数组或者对象等复杂数据结构
- state 变化很复杂，经常一个操作需要修改很多 state
- 希望构建自动化测试用例来保证程序的稳定性
- 需要在深层子组件里面去修改一些状态（也就是 useReducer + useContext 代替 Redux）
- 应用程序比较大，希望 UI 和业务能够分开维护

**useReducer() 参数**

第一个参数是一个 reducer 函数；

第二个参数是 reducer 中 state 的初始值；

第三个参数为可选参数，值为一个函数，可以用来惰性提供初始状态（对初始值进行计算操作后返回初始值）

**useReducer() 返回值**

返回值为一个数组

第一个元素为 state ，表示当前状态值

第二个元素为 dispatch，dispatch 一个 action 对象（初始状态的对象）来改变当前状态值

dispatch 的触发会触发组件的更新 

#### useReducer 基本使用

使用上来说，一共有四步：

1. 创建初始值 initialState
2. 创建所有操作 reducer(state, action)
3. 传给 useReducer，得到读和写 API
4. 调用 `写({type: '操作类型'})`

reducer.js

```jsx
// state 第一次接收 reducer 的初始值
export default function reducer(state, action) {
  switch(action.type) {
    case "increment":
      return {...state, counter: state.counter + 1};
    case "decrement":
      return {...state, counter: state.counter - 1};
    default:
      return state;
  }
}
```

home.js

```jsx
import React, { useState, useReducer } from 'react';

import reducer from './reducer';

export default function Home() {
  // const [count, setCount] = useState(0);
  const initial = { counter: 0 };
  const [state, dispatch] = useReducer(reducer, initial);

  return (
    <div>
      <h2>Home当前计数: { state.counter }</h2>
      <button onClick={ e => dispatch({ type: "increment" }) }>+1</button>
      <button onClick={ e => dispatch({ type: "decrement" }) }>-1</button>
    </div>
  )
}
```

profile.js

```jsx
import React, { useReducer } from 'react';

import reducer from './reducer';

export default function Profile() {
  // const [count, setCount] = useState(0);
  const initial = { counter: 0 };
  const [state, dispatch] = useReducer(reducer, initial);

  return (
    <div>
      <h2>Profile当前计数: { state.counter }</h2>
      <button onClick={ e => dispatch({ type: "increment" }) }>+1</button>
      <button onClick={ e => dispatch({ type: "decrement" }) }>-1</button>
    </div>
  )
}
```

App.js

```jsx
import React from 'react';

export default function App() {
  return (
    <div>
      <Home/>
      <Profile/>
    </div>
  )
}
```

**效果图**

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1629375027240-6a6575ec-76a3-4495-8db0-431e6358d15b.jpeg)

Home 组件与 Profile 组件的 counter 数据是不会共享的，

它们只是使用了相同的 reducer 函数而已 。

#### 惰性初始化

可以选择惰性地创建初始 state。

为此，需要将 init 函数作为 useReducer 的第三个参数传入，这样初始 state 将被设置为 init(initialArg)。

这么做可以将用于计算 state 的逻辑提取到 reducer 外部，

也为将来对重置 state 的 action 做处理提供了便利：

```jsx
function init(initialCount) {  
  return {count: initialCount};
}

function reducer(state, action) {
  switch (action.type) {
    case 'increment':
      return {count: state.count + 1};
    case 'decrement':
      return {count: state.count - 1};
    case 'reset':      
      return init(action.payload);   
    default:
      throw new Error();
  }
}

function Counter({initialCount}) {
  const [state, dispatch] = useReducer(reducer, initialCount, init);  
  return (
    <>
      Count: {state.count}
      <button
        onClick={() => dispatch({type: 'reset', payload: initialCount})}>        
        Reset
      </button>
      <button onClick={() => dispatch({type: 'decrement'})}>-</button>
      <button onClick={() => dispatch({type: 'increment'})}>+</button>
    </>
  );
}
```

#### 引用不变

useReducer 返回的 state 跟 ref 一样，引用是不变的，不会随着函数组件的重新更新而变化，

因此 useReducer 也可以解决闭包陷阱

```jsx
const setCountReducer = (state, action) => {
  switch(action.type){
    case 'add':
      return state + action.value
    case 'minus':
      return state - action.value
    default:
      return state
  }
}

const App = () => {
  const [count, dispatch] = useReducer(setCountReducer, 0)
  useEffect(() => {
    const timeId = setInterval(() => {
      dispatch({type: 'add', value: 1})
    }, 1000)
    return () => clearInterval(timeId)
  }, [])
  return (
    <span>{count}</span>
  )
}
```

#### 使用 useReducer 优化 useEffect

**优化前**

useEffect 同时依赖了两个 state ，依赖数组必须引入 step

```jsx
function Counter() {
  const [count, setCount] = useState(0);
  const [step, setStep] = useState(1);

  useEffect(() => {
    const id = setInterval(() => {
      setCount(c => c + step);
    }, 1000);
    return () => clearInterval(id);
  }, [step]);

  return (
    <>
      <h1>{count}</h1>
      <input value={step} onChange={e => setStep(Number(e.target.value))} />
    </>
  );
}
```

因为 step 是依赖项之一，修改 step 会重新渲染组件，

清除上一次的 effect ，然后重新运行新的 effect 、重新生成定时器。

造成后果：

1、计时器不准了，因为每次 `step` 变化时都会销毁并重新计时

2、频繁生成/销毁定时器带来了一定性能负担。

**优化后**

假如我们不想在 step 改变后重新运行新的 effect 、重新生成定时器，

我们该如何从 effect 中移除对 step 的依赖呢？

```jsx
const initialState = {
  count: 0,
  step: 1,
};

function reducer(state, action) {
  const { count, step } = state;
  if (action.type === 'tick') {
    return { count: count + step, step };
  } else if (action.type === 'step') {
    return { count, step: action.step };
  } else {
    throw new Error();
  }
}

function Counter() {
  const [state, dispatch] = useReducer(reducer, initialState);
  const { count, step } = state;

  useEffect(() => {
    const id = setInterval(() => {
      dispatch({ type: 'tick' });
    }, 1000);
    return () => clearInterval(id);
  }, [dispatch]);

  return (
    <>
      <h1>{count}</h1>
      <input value={step} onChange={e => {
        dispatch({
          type: 'step',
          step: Number(e.target.value)
        });
      }} />
    </>
  );
}
```

**React 会保证 dispatch 在每次渲染中都是一样的。**

**从依赖中去除 dispatch，也不会引起 effect 不必要的重复执行。**

所以上面例子中不再需要重新订阅定时器。

这使得 effect 和 step 状态解耦。

effect 不再关心怎么更新状态，它只负责告诉我们发生了什么。

更新的逻辑全都交由 reducer 去统一处理。

**在之前渲染中调用的 reducer 怎么“知道”新的 props？**

**答案是当你 dispatch 的时候，React 只是记住了 action - 它会在下一次渲染时再次调用 reducer 。**

**在那个时候，新的 props 是可以被访问到的，而且 reducer 调用也不是在 effect 里。**

**useReducer 是 Hooks 的“作弊模式” ，帮助我们移除不必需的依赖，避免不必要的 effect 调用。**

### useCallback（computed，组件性能优化，传 props 与 useEffect）

#### useCallback 介绍

```jsx
const memoizedCallback = useCallback(() => { doSomething(a, b) }, [a, b])
```

回调函数只有当依赖发生变化时才会重新执行。

第一个参数为回调函数，也就是要缓存的函数。

要在回调函数中传入参数，最好使用高阶函数，即传入函数作为参数。

第二个参数为依赖数组，数组元素作为判断是否变化的参数，并不会作为形参传入回调函数，

建议回调函数中使用到的变量都应该在数组中列出。

返回一个 memoized 回调函数。回调函数的 memoized 版本，回调函数仅在某个依赖项改变时才会更新。

**注意：useCallback 是用来优化函数式组件性能，缓存函数的 hook，一般传给子组件作为 props 时使用！**

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1629377505503-517ea3ba-557c-4701-9b87-9eefe1bccb54.jpeg)

每次组件状态发生变化的时候，函数组件实际上都会重新执行一遍。

在每次执行的时候，实际上都会创建一个新的事件处理函数 increment 。

这个事件处理函数中呢包含了 count 这个变量的闭包，以确保每次能够得到正确的结果。

这也意味着，即使 count 没有发生变化，但是函数组件因为其它状态发生变化而重新渲染时，这种写法也会每次创建一个新的函数。

创建一个新的事件处理函数，虽然不影响结果的正确性，但其实是没必要的。

这样做不仅增加了系统的开销，

更重要的是：**每次创建新函数的方式会让接收事件处理函数的组件，需要重新渲染。**

比如这个例子中的 button 组件，接收了 increment，并作为一个属性值。

**如果每次都是一个新的，那么这个 React 就会认为这个组件的 props 发生了变化，必须重新渲染。**

因此，我们需要做到的是：

**只有当 count 发生变化时，我们才需要重新定一个回调函数。**

**而这正是 useCallback 这个 Hook 的作用。**

**useCallback 保证组件不会创建重复的回调函数，接收这个回调函数作为 props （属性）的组件，也不会频繁地需要重新渲染。**

#### 建议

**useCallback 要和 shouldComponentUpdate/React.memo 配套使用**

1、**在组件内部，那些会成为 useEffect 依赖项的函数，建议用 useCallback 包裹，或者直接编写在引用它的 useEffect 中。**

2、**如果你的 function 会作为 props 传递给子组件，请一定要使用 useCallback 包裹，**

对于子组件来说，如果每次 render 都会导致你传递的函数发生变化，可能会对它造成非常大的困扰。

同时也不利于 react 做渲染优化。

3、还有一种场景，大家很容易忽视，就是：节流防抖。

**对于使用高阶函数（函数的入参为函数或返回值为函数）的场景，建议一律使用 useMemo**

```jsx
const handleClick = useMemo(() => debounce(() => { setCount(count + 1); }, 1000), [count]);
```

注意：useMemo 并不能一劳永逸解决所有高阶函数场景。

在示例的场景中，防抖的逻辑是：「连续点击后 1 秒，真正执行逻辑，在这过程中的重复点击失效」。

而如果业务逻辑改成了「点击后立即发生状态变更，之后的 1 秒内重复点击无效」，

那么我们的代码可能就变成了：

```jsx
const handleClick = useMemo(() => throttle(() => { setCount(count + 1); }, 1000), [count]);
```

然后发现失效了。

原因是点击以后，count 立即发生了变化，然后 handleClick 又重复生成了新函数，

这个节流就失效了。

所以这种场景，解决思路又变回了前面 useEffect 常见问题提到的：「消除依赖」 或 「使用ref」。

消除依赖

在这个场景里，很简单，我们主要利用 setCount 的另一个用法 ：函数式更新 functional updates 。

这样写就好了：

```jsx
() => setCount(prevCount => ++prevCount)
```

建议可以直接使用社区的库，比如 react-use，或者参考他们的实现，自己写一个 debounce 或 throttle 。

参考：<https://zhuanlan.zhihu.com/p/113216415>

#### 消除依赖 或 使用 ref

在使用 useMemo 或者 useCallback 时，确保返回的函数只创建一次

错误示例：

```js
export const useCount = () => {
  const [count, setCount] = useState(0);

  const [increase, decrease] = useMemo(() => {
    const increase = () => {
      setCount(count + 1);
    };

    const decrease = () => {
      setCount(count - 1);
    };
    return [increase, decrease];
  }, [count]);

  return [count, increase, decrease];
};
```

在 useCount Hook 中， count 状态的改变会让 useMemo 中的 increase 和 decrease 函数被重新创建。

由于闭包特性，如果这两个函数被其它 Hook 用到了，我们应该将这两个函数也添加到相应 Hook 的依赖数组中，否则就会产生 bug。

比如：

```js
function Counter() {
  const [count, increase] = useCount();

  useEffect(() => {
    const handleClick = () => {
      increase(); // 执行后 count 的值永远都是 1    
    };

    document.body.addEventListener("click", handleClick);
    
    return () => {
      document.body.removeEventListener("click", handleClick);
    };
  }, []); 

  return <h1>{count}</h1>;
}
```

在 useCount 中，increase 会随着 count 的变化而被重新创建。但是 increase 被重新创建之后， useEffect 并不会再次执行，

所以 useEffect 中取到的 increase 永远都是首次创建时的 increase 。

而首次创建时 count 的值为 0，因此无论点击多少次， count 的值永远都是 1。

解决方案：

1、消除依赖

消除依赖通过 setState 回调，让函数不依赖外部变量

```js
export const useCount = () => {
  const [count, setCount] = useState(0);

  const [increase, decrease] = useMemo(() => {
    const increase = () => {
      setCount((latestCount) => latestCount + 1);
    };

    const decrease = () => {
      setCount((latestCount) => latestCount - 1);
    };
    return [increase, decrease];
  }, []); // 保持依赖数组为空，这样 increase 和 decrease 方法都只会被创建一次
  return [count, increase, decrease];
};
```

2、使用 ref

通过 ref 来保存可变变量

```js
export const useCount = () => {
  const [count, setCount] = useState(0);
  const countRef = useRef(count);

  useEffect(() => {
    countRef.current = count;
  });

  const [increase, decrease] = useMemo(() => {
    const increase = () => {
      setCount(countRef.current + 1);
    };

    const decrease = () => {
      setCount(countRef.current - 1);
    };
    return [increase, decrease];
  }, []); // 保持依赖数组为空，这样 increase 和 decrease 方法都只会被创建一次
  return [count, increase, decrease];
};
```

#### useCallback 的基本使用

**只有当依赖的元素发生改变时，才触发 useCallback 的回调函数。**

**在依赖元素不变或没有依赖元素的情况下，多次渲染造成函数多次创建的时候，**

**useCallback 会返回一个函数的 memoized（记忆的） 值 ，返回的都是相同的函数。**

如下图，点击 increment1 的按钮后可正常加 1，

点击 increment2 的按钮后只会加一次 1，

因为每次重新渲染，useCallback 里的函数都是第一次创建的，

useCallback 里的函数是闭包（引用 0 为初始值），

虽然 console.log 还是会再次执行，但是 count 初始值保留了下来，仍然是 0 ，每次都是 setCount(0 + 1)

将 依赖项 [] 改为 [count] 即可正常加 1 。

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1629377493501-1aa0cc01-a87f-4573-ad02-40450a7998d6.jpeg)

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1629377679572-c06d3179-f9dc-462b-a6d7-41789f0fd955.jpeg)


```jsx
function App() {
  const [count, setCount] = useState(0)
  const [count2, setCount2] = useState(0)
  const fn = useCallback(() => {
    console.log(count)
  }, [count2]) 
  return (
    <div>
      {count}
      <button onClick={() => setCount(100)}>按钮A</button>
      <button onClick={() => setCount2(10000)}>按钮B</button>
      <button onClick={fn}>按钮C</button> 
    </div>
  )
}

// 依次点 A, C 打印输出 0，因为 count2 没变化，所以读取的是旧的 count: 0
// 依次点 A, B, C 打印输出 100，因为 count2 变了，所以读取的是新的 count: 100
```

#### 没有性能优化的 useCallback

**虽然 [] 中添加了 count ，表示只有当 count 发生改变时，重新创建并执行 useCallback 的回调函数，**

**而不是 [] 表示什么时候都缓存 count ，只创建一次，执行一次。**

**但是其实仍然有函数的创建过程，只缓存了函数，没有做更多的性能优化，**

![img](https://cdn.nlark.com/yuque/0/2021/png/1614731/1629377517245-71e845d2-12b0-4903-8180-61f94aca2888.png)

```jsx
import React, {useState, useCallback, useMemo} from 'react'

export default function CallbackHookDemo01() {
  const [count, setCount] = useState(0);

  const increment1 = () => {
    console.log("执行increment1函数");
    setCount(count + 1);
  }

  const increment2 = useCallback(() => {
    console.log("执行increment2函数");
    setCount(count + 1);
  }, [count]);

  return (
    <div>
      <h2>CallbackHookDemo01: {count}</h2>
      <button onClick={increment1}>+1</button>
      <button onClick={increment2}>+1</button>
    </div>
  )
}
```

#### 有性能优化的 useCallback

定义一个函数传给子组件作为 props ，防止子组件多次渲染，实现性能优化

```jsx
import React, {useState, useCallback, memo} from 'react';

/**
 * useCallback 在什么时候使用才可以优化性能?
 * 场景: 在将一个组件中的函数, 传递给子元素进行回调使用时, 使用 useCallback 对函数进行处理
 */
const HYButton = memo((props) => {
  console.log("HYButton重新渲染: " + props.title);
  return <button onClick={props.increment}>HYButton +1</button>
});

export default function CallbackHookDemo02() {
  console.log("CallbackHookDemo02重新渲染");

  const [count, setCount] = useState(0);
  const [show, setShow] = useState(true);

  const increment1 = () => {
    console.log("执行increment1函数");
    setCount(count + 1);
  }

  const increment2 = useCallback(() => {
    console.log("执行increment2函数");
    setCount(count + 1);
  }, [count]);

  return (
    <div>
      <h2>CallbackHookDemo02: {count}</h2>
      <HYButton title="btn1" increment={increment1}/>
      <HYButton title="btn2" increment={increment2}/>
      <button onClick={e => setShow(!show)}>show切换</button>
    </div>
  )
}
```

点击 show 切换， HYButton 不应该重新渲染，

所以需要 memo （类组件是使用 PureComponent），memo 浅层比较 props 决定是否更新， 

因为当点击 show 切换时，改变的是 show，而不是 useCallback 依赖的 count，

每次 useCallback 返回的值（返回的是函数，即 increment2 函数）是相同的，

所以传递给 memo 的 props 都是相同的，

因此，点击 show 切换，btn2 不会重新渲染，

而 btn1 每次都会重新定义新的 increment1 函数，传递给 memo 的 props 都是不同的，

所以 btn1 会重新渲染。

**效果图（memo 配合 useCallback 可实现性能优化）**

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1629379980908-3288da26-3f80-43e0-8474-f1311676a2d6.jpeg)

#### 使用 useCallback 优化多个相同依赖的 useEffect

**优化前**

```jsx
function SearchResults() {
  // 🔴 Re-triggers all effects on every render
  function getFetchUrl(query) {
    return 'https://hn.algolia.com/api/v1/search?query=' + query;
  }

  useEffect(() => {
    const url = getFetchUrl('react');
    // ... Fetch data and do something ...
  }, [getFetchUrl]); // 🚧 Deps are correct but they change too often

  useEffect(() => {
    const url = getFetchUrl('redux');
    // ... Fetch data and do something ...
  }, [getFetchUrl]); // 🚧 Deps are correct but they change too often

  // ...
}
```

我们的两个 effects 都依赖 getFetchUrl，而 getFetchUrl 每次渲染都不同，所以两个 effects 都会执行，

依赖数组变得无用

**优化后**

**当我们需要将函数传递下去并且函数会在子组件的 effect 中被调用的时候，**

**useCallback 是很好的技巧且非常有用。**

**或者你想试图减少对子组件的记忆负担，也不妨一试。**

```jsx
function SearchResults() {
  const [query, setQuery] = useState('react');

  // ✅ Preserves identity until query changes
  const getFetchUrl = useCallback(() => {
    return 'https://hn.algolia.com/api/v1/search?query=' + query;
  }, [query]);  // ✅ Callback deps are OK

  useEffect(() => {
    const url = getFetchUrl('react');
    // ... Fetch data and do something ...
  }, [getFetchUrl]); // ✅ Effect deps are OK

  useEffect(() => {
    const url = getFetchUrl('redux');
    // ... Fetch data and do something ...
  }, [getFetchUrl]); // ✅ Effect deps are OK
}
```

如果 query 保持不变，getFetchUrl 也会保持不变，我们的 effect 也不会重新运行。

但是如果 query 修改了，getFetchUrl 也会随之改变，因此会重新请求数据。

这就像你在 Excel 里修改了一个单元格的值，另一个使用它的单元格会自动重新计算一样。

##### 另一种办法

```jsx
// ✅ Not affected by the data flow
function getFetchUrl(query) {
  return 'https://hn.algolia.com/api/v1/search?query=' + query;
}

function SearchResults() {
  useEffect(() => {
    const url = getFetchUrl('react');
    // ... Fetch data and do something ...
  }, []); // ✅ Deps are OK

  useEffect(() => {
    const url = getFetchUrl('redux');
    // ... Fetch data and do something ...
  }, []); // ✅ Deps are OK

  // ...
}
```

### useMemo （computed，组件性能优化，传 props 与 useEffect）

#### useMemo 介绍

```jsx
const memoizedValue = useMemo(() => computeExpensiveValue(a, b), [a, b]);
```

第一个参数 fn 是产生所需数据的一个计算函数。

通常来说，fn 会使用第二个 deps 中声明的一些变量来生成一个结果，用来渲染出最终的 UI 。

**如果某个数据是通过其它数据计算得到的，那么只有当用到的数据，也就是依赖的数据发生变化的时候，才应该需要重新计算。**

**useMemo  保证每次渲染时不会重复的高开销的计算，**

**接收这个计算值作为 props （属性）的组件，也不会频繁地需要重新渲染。**

**useMemo 也是优化函数式组件性能的 hook，用来缓存计算结果的 hook**，

**返回的也是一个 memoized（记忆的） 值，没有指定是函数的 memoized 值，**

**也就是说，不要求返回值必须是函数。**

**在依赖项不变的情况下，多次定义的时候，返回的值是相同的**

- 第一个参数为一个函数，返回一个缓存的值，

  也就是仅当重新渲染时数组中的值发生改变时，回调函数才会重新计算返回的缓存值，

  可以避免在每次重新渲染时都进行复杂的数据计算。

- 第二个参数为一个依赖项数组，只有依赖项中的数据发生改变时才重新计算值，用法同 useEffect 的依赖项数组

- 如果没有提供依赖项数组，`useMemo` 在每次渲染时都会计算新的值。

**注意：useMemo 可以避免重新计算、子组件重新渲染，当 props 是复杂数据类型时，传给子组件作为 props 时使用！**

传给 useMemo 的函数应该是在渲染期间运行的，不要在 useMemo 中做任何不会在渲染期间做的事，

副作用应该使用 useEffect ，而不是 useMemo；

如果函数返回的是一个子节点，也可以用 useMemo 缓存起来。

```jsx
const Parent = ({ a, b }) => {
  // Only re-rendered if `a` changes:
  const child1 = useMemo(() => <Child1 a={a} />, [a]);
  // Only re-rendered if `b` changes:
  const child2 = useMemo(() => <Child2 b={b} />, [b]);
  return (
    <>
      {child1}
      {child2}
    </>
  );
};
```

#### useMemo 基本使用

**需要使用的场景**

a. 保持引用相等

1. 1. 对于组件内部用到的 object、array、函数等，如果用在了其他 Hook 的依赖数组中，或者作为 props 传递给了下游组件，应该使用 useMemo

   2. 自定义 Hook 中暴露出来的 object、array、函数等，都应该使用 useMemo 。以确保当值相同时，引用不发生变化

   3. 使用 Context 时，如果 Provider 的 value 中定义的值（第一层）发生了变化，即便用了 Pure Component 或者 React.memo，

      仍然会导致子组件 re-render。这种情况下，仍然建议使用 useMemo 保持引用的一致性。

b. 成本很高的计算

**无需使用的场景**

a. 返回的值是原始值：string, boolean, null, undefined, number, symbol

b. 对于一些简单的 JS 运算（数组操作）来说，我们不需要使用 useMemo

c. 仅在组件内部用到的 object、array、函数等（没有作为 props 传递给子组件），且没有用到其他 Hook 的依赖数组中，一般不需要使用 useMemo。

##### 避免重新计算

进行大量的计算操作，判断每次渲染时是否需要重新计算

如果没有提供依赖项数组，useMemo 在每次渲染时都会计算新的值

```jsx
import React, {useState, useMemo} from 'react';

function calcNumber(count) {
  console.log("calcNumber重新计算");
  let total = 0;
  for (let i = 1; i <= count; i++) {
    total += i;
  }
  return total;
}

export default function MemoHookDemo01() {
  const [count, setCount] = useState(10);
  const [show, setShow] = useState(true);

  // total = calcNumber(count);
  // 只有 count 发生改变时才调用 calcNumber(count);
  const total = useMemo(() => {
    // 返回值是数值
    return calcNumber(count);
  }, [count]);
  // 如果第二个参数是 [] ，而不是 [count]，
  // 在渲染后 calcNumber 会将 count 初始值缓存下来，
  // 相当于每次执行返回值都是相同的，每次渲染 total 值都是 55

  return (
    <div>
      <h2>计算数字的和: {total}</h2>
      <button onClick={e => setCount(count + 1)}>+1</button>
      <button onClick={e => setShow(!show)}>show切换</button>
    </div>
  )
}
```

点击三次 +1 后 calcNumber 计算 3 次，点击 show切换 ，函数 calcNumber 不再重新计算，

效果图（忽略下面的切换按钮）

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1629384879671-27c9fdb3-d699-4ab3-8221-985feed90a3f.jpeg)

useMemo 的使用场景主要是用来缓存计算量比较大的函数结果，可以避免不必要的重复计算，

有过 vue 的使用经历可能会觉得跟 Vue 里面的计算属性有异曲同工的作用。

举个例子，对于一个显示用户信息的列表，现在需要对用户名进行搜索，且 UI 上需要根据搜索关键字显示过滤后的用户，

那么这样一个功能需要有两个状态：无论是两个数据中的哪一个发生变化，都需要过滤用户列表以获得需要展示的数据。

那么如果不使用 useMemo 的话，就需要用这样的代码实现：

```jsx
import React, { useState, useEffect } from "react";

export default function SearchUserList() {
  const [users, setUsers] = useState(null);
  const [searchKey, setSearchKey] = useState("");

  useEffect(() => {
    const doFetch = async () => {
      // 组件首次加载时发请求获取用户数据
      const res = await fetch("https://reqres.in/api/users/");
      setUsers(await res.json());
    };
    doFetch();
  }, []);

  let usersToShow = null;

  if (users) {
    // 无论组件为何刷新，这里一定会对数组做一次过滤的操作
    usersToShow = users.data.filter((user) => user.first_name.includes(searchKey));
  } 

  return (
    <div>
      <input
        type="text"
        value={searchKey}
        onChange={(evt) => setSearchKey(evt.target.value)}
      />
      <ul>
        {usersToShow && 
         usersToShow.length > 0 &&
         usersToShow.map((user) => {
           return <li key={user.id}>{user.first_name}</li>;
        })}
      </ul>
    </div>
  );
}
```

在这个例子中，无论组件为何要进行一次重新渲染，实际上都需要进行一次过滤的操作。

但其实你只需要在 users 或者 searchKey 这两个状态中的某一个发生变化时，重新计算获得需要展示的数据就行了。

那么，这个时候，我们就可以用 useMemo 这个 Hook 来实现这个逻辑，缓存计算的结果：

```jsx
import React, { useState, useEffect } from "react";

export default function SearchUserList() {
  const [users, setUsers] = useState(null);
  const [searchKey, setSearchKey] = useState("");

  useEffect(() => {
    const doFetch = async () => {
      // 组件首次加载时发请求获取用户数据
      const res = await fetch("https://reqres.in/api/users/");
      setUsers(await res.json());
    };
    doFetch();
  }, []);

  // 使用 userMemo 缓存计算的结果
  const usersToShow = useMemo(() => {
    if (!users) return null;
    return users.data.filter((user) => {
      return user.first_name.includes(searchKey));
    }
  }, [users, searchKey]);

  return (
    <div>
      <input
        type="text"
        value={searchKey}
        onChange={(evt) => setSearchKey(evt.target.value)}
      />
      <ul>
        {usersToShow && 
         usersToShow.length > 0 &&
         usersToShow.map((user) => {
           return <li key={user.id}>{user.first_name}</li>;
        })}
      </ul>
    </div>
  );
}
```

##### 避免子组件重新渲染

对子组件传递相同内容的对象，使用 useMemo 进行性能优化

```jsx
import React, { useState, memo, useMemo } from 'react';

const HYInfo = memo((props) => {
  console.log("HYInfo重新渲染");
  return <h2>名字: {props.info.name} 年龄: {props.info.age}</h2>
});

export default function MemoHookDemo02() {
  console.log("MemoHookDemo02重新渲染");
  const [show, setShow] = useState(true);
  
  // useMemo 每次返回的都是相同的对象
  const info = useMemo(() => {
    // 返回的是对象
    return { name: "why", age: 18 };
  }, []);
  // 也可以使用 useState 配合 memo 防止 HYInfo 组件重新渲染 
  // const [info, setInfo] = useState({ name: "why", age: 18 });

  return (
    <div>
      <HYInfo info={info} />
      <button onClick={e => setShow(!show)}>show切换</button>
    </div>
  )
}
```

点击 show切换，只有父组件 MemoHookDemo02 重新渲染，子组件 HYInfo 没有重新渲染

效果图

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1629385963529-d450ad7d-287e-4752-88ba-42fa014de65d.jpeg)

#### useMemo 与 useCallback 的区别

useMemo 可以缓存所有对象，useCallback 只能缓存函数。

useMemo 是缓存「函数的返回值」，useCallback 是「缓存函数」；

useMemo 是对回调函数的返回值做优化，useCallback 只针对回调函数的调用做优化；

useMemo 使用范围 > useCallback使用范围，useCallback 可以转化为 useMemo  ，

简单理解：

useCallback(fn, deps) === useMemo(() => fn, deps)

useCallback(x => log(x), [m]) 等价于 useMemo(() => x => log(x), [m])

```jsx
import React, {useState, useCallback, useMemo} from 'react'

export default function CallbackHookDemo01() {
  const [count, setCount] = useState(0);

  const increment1 = () => {
    console.log("执行increment1函数");
    setCount(count + 1);
  }

  const increment2 = useCallback(() => {
    console.log("执行increment2函数");
    setCount(count + 1);
  }, [count]);
  
  // increment2 效果等价于 increment3
  const increment3 = useMemo(() => {
    return () => {
      console.log("执行increment2函数");
      setCount(count + 1);
    }
  }, [count]);

  return (
    <div>
      <h2>CallbackHookDemo01: {count}</h2>
      <button onClick={increment1}>+1</button>
      <button onClick={increment2}>+1</button>
    </div>
  )
}
```

### useRef（保存 DOM / 数据）

#### useRef 介绍

```jsx
const refContainer = useRef(initialValue);
```

useRef 返回一个可变的 ref 对象，其 .current 属性被初始化为传入的参数（initialValue）。

返回的 ref 对象在组件的整个生命周期都保持不变。

主要作用是创建一个数据的引用，并让这个数据在 render 过程中始终保持不变。

useRef 不会触发重新渲染。

**useRef 有 2 个用处：**

**保存 DOM 节点的引用；**

**在多次渲染之间共享数据。**

假设你要去做一个计时器组件，这个组件有开始和暂停两个功能。

很显然，你需要用 window.setInterval 来提供计时功能；

为了能够暂停，你就需要在某个地方保存这个 window.setInterval 返回的计数器的引用，

确保在点击暂停按钮的同时，也能用 window.clearInterval 停止计时器。

那么，这个保存计数器引用的最合适的地方，就是 useRef，因为它可以存储跨渲染的数据。

代码如下：

```jsx
import React, { useState, useCallback, useRef } from "react";

export default function Timer() {
  // 定义 time state 用于保存计时的累积时间
  const [time, setTime] = useState(0);
    
  // 定义 timer 这样一个容器用于在跨组件渲染之间保存一个变量
  const timer = useRef(null);

  // 开始计时的事件处理函数
  const handleStart = useCallback(() => {
    // 使用 current 属性设置 ref 的值
    timer.current = window.setInterval(() => {
      setTime((time) => time + 1);
    }, 100);
  }, []);

  // 暂停计时的事件处理函数
  const handlePause = useCallback(() => {
    // 使用 clearInterval 来停止计时
    window.clearInterval(timer.current);
    timer.current = null;
  }, []);
  return (
    <div>
      {time / 10} seconds.
      <br />
      <button onClick={handleStart}>Start</button>
     <button onClick={handlePause}>Pause</button>
    </div>
  );
}
```

我们使用了 useRef 来保存 window.setInterval ，

从而能够在用户点击暂停按钮时清除定时器，达到暂停计时的目的。

同时，使用 useRef 保存的数据一般是和 UI 的渲染无关的，

因此当 ref 的值发生变化时，是不会触发组件的重新渲染的，这也是 useRef 区别于 useState 的地方。

#### useRef 基本使用

定义： const count = useRef(0)，读取： count.current

最常用的 ref 是两种用法：

##### 用法一：引用 DOM（或者组件，但是需要是 class 组件）元素

```jsx
import React, { Component, useEffect, useRef } from 'react';

class TestCpn extends Component {
  render() {
    return <h2>TestCpn</h2>
  }
}

export default function RefHookDemo01() {
  const titleRef = useRef();
  const inputRef = useRef();

  function changeDOM() {
    titleRef.current.innerHTML = "Hello World";
    inputRef.current.focus();
    console.log(testRef.current);
  }

  return (
    <div>
      <h2 ref={titleRef}>RefHookDemo01</h2>
      <input ref={inputRef} type="text"/>
      <TestCpn ref={testRef}/>
      <button onClick={e => changeDOM()}>修改DOM</button>
    </div>
  )
}
```

如图，点击 修改DOM 按钮，文字变化、输入框聚焦、并打印获取的类组件实例

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1629640958754-1dc32ea2-e69f-4584-a595-2ecbdad4d0f4.jpeg)

##### 用法二：保存一个数据，这个对象在整个生命周期中可以保存不变

useRef() 接收一个参数作为默认值，该默认值可以保存不变

```jsx
import React, { useRef, useState, useEffect } from 'react'

export default function RefHookDemo02() {
  const [count, setCount] = useState(0);
  // 第一次渲染，保存 0 作为默认值，之后都不变
  const numRef = useRef(count);

  return (
    <div>
      <h2>numRef中的值: {numRef.current}</h2>
      <h2>count中的值: {count}</h2> 
      <button onClick={e => setCount(count + 10)}>+10</button>
    </div>
  )
}
```

点击 +10 后，useState 的 count 加 10，useRef 的 count 值不变，

即使再点击 +10 ，useRef 的 count 值 仍然不会变

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1629640258959-ead41a82-38ae-48c1-9f84-9768098a3c68.jpeg)

##### 保存实时/不变的值

```jsx
import React, { useRef, useState } from 'react'

function App() {
  const [count, setCount] = useState()
  function handleAlertClick() {
    setTimeout(() => {
      alert(`You clicked on ${count}`)
    }, 3000)
  }
  return (
    <div>
      <p>You click {count} times</p>
      <button onClick={() => setCount(count + 1)}>Click me</button>
      <button onClick={handleAlertClick}>Show alert</button>
    </div>
  )
}

export default App
```

点击 6 次 Click me ，点击 Show alert，之后再点击 4 次 Click me，显示的是却是点击 6 次（旧值）

![img](https://cdn.nlark.com/yuque/0/2020/gif/1614731/1609402890906-6cb847db-db46-465a-9381-89531a51f7f6.gif)

当我们更新状态的时候, 

React 会重新渲染组件, 每一次渲染都会拿到独立的 count 状态,  并重新渲染一个  handleAlertClick  函数

每一个 handleAlertClick 里面都有它自己的 count 。

你会发现，count 的值并不能够实时的显示更新的数据，

这个是由于 JS 中一值就存在的闭包机制导致的，

当点击显示弹窗的按钮时，此时的 count 的值已经确定，并且传入到了 alert 方法的回调中，形成闭包，

后续值的改变不会影响到定时器的触发。

而如果在类组件中，如果我们使用的是 this.state.count，得到的结果就会是实时的，

因为它们都是指向的同一个引用对象。

**在函数组件中，我们可以使用 useRef 来实时得到新的值，**

**这就是 useRef 的另外一种用法，它相当于 this , 可以存放任何变量。替代 this（获取实时的值）**

**useRef 可以很好的解决闭包带来的不方便性**

###### 获取实时的值

```jsx
import React, { useRef, useState } from 'react'

function App() {
  const [count, setCount] = useState(0)
  
  const latestCount = useRef()
  latestCount.current = count
  
  function handleAlertClick() {
    setTimeout(() => {
      alert(`You clicked on ${latestCount.current}`) // 实时的结果
    }, 3000)
  }
  
  return (
    <div>
      <p>You click {count} times</p>
      <button onClick={() => setCount(count + 1)}>Click me</button>
      <button onClick={handleAlertClick}>Show alert</button>
    </div>
  )
}

export default App
```

###### 不变的值

要值得注意的是，如果我们在 useRef 中传入参数，

使用下面这种方法来访问值，结果又会不同：

```jsx
import React, { useRef, useState } from 'react'

function App() {
  const [count, setCount] = useState(0)
  
  const latestCount = useRef(count) // 直接传入count
  
  function handleAlertClick() {
    setTimeout(() => {
      alert(`You clicked on ${latestCount.current}`)
    }, 3000)
  }
  return (
    <div>
      <p>You click {count} times</p>
      <button onClick={() => setCount(count + 1)}>Click me</button>
      <button onClick={handleAlertClick}>Show alert</button>
    </div>
  )
}

export default App
```

点击的时候我们会发现弹出来的值永远是 0，

因为 useRef 返回的都是相同的引用，

参数在第一个传入进去的时候已经赋值给了 current 属性，返回了一个实例，

因为已经有了实例了，所以会直接将原来的实例返回，传入的参数也就不再起作用了。

##### 保存上一次的值

保存 state 上一次的值

```jsx
import React, { useRef, useState, useEffect } from 'react'

export default function RefHookDemo02() {
  const [count, setCount] = useState(0);

  const numRef = useRef(count);

  useEffect(() => {
    numRef.current = count;
  }, [count])

  return (
    <div>
      <h2>count上一次的值: {numRef.current}</h2>
      <h2>count这一次的值: {count}</h2>
      <button onClick={e => setCount(count + 10)}>+10</button>
    </div>
  )
}

```

第一次渲染， numRef.current 值为 0，count 为 0 ，

第一次点击 +10 ，触发重新渲染， numRef.current 值依然为 0，count 为 10 ,

渲染完成，count 变化，触发 useEffect 的 effect 函数，

numRef.current 赋值为 10，但是不会引起重新渲染

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1629641475709-5b4c2f8c-1ee5-4d2e-b1a2-6bfead601159.jpeg)

第二次点击 +10 ，触发重新渲染，numRef.current 的值为 10，count 为 20，

渲染完成，count 变化，触发 useEffect 的 effect 函数，

numRef.current 赋值为 20

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1629641490012-598f3087-654a-48d9-9a24-bbb9e56ac570.jpeg)

##### 保存需要在异步回调中访问最新 state 或变化过的函数

```jsx
function Timer() {
  const intervalRef = useRef();

  useEffect(() => {
    intervalRef.current = setInterval(() => {
      // ...
    });
  });

  return (
    <>
      <button onClick={() => clearInterval(intervalRef.current)}>停止</button>
    </>
  );
}
```

##### 辅助实现 ComponentDidUpdate 生命周期

```jsx
function LifeCycleExample() {
  const firstMountRef = useRef(true);
  useEffect(() => {
    if (firstMountRef.current) {
      firstMountRef.current = false;
    } else {
      // effect here
    }
  })
  return (<p>LifeCycleExample</p>);
}
```



### useImperativeHandle （限制父组件对子组件的 dom 操作）

官方建议 useImperativeHandle 和 forwardRef 同时使用，

减少暴露给父组件的属性，避免使用 ref 这样的命令式代码。

回顾 ref 转发

```jsx
import React, { useRef, forwardRef } from 'react';

const HYInput = forwardRef((props, ref) => {
  return <input ref={ref} type="text"/>
})

export default function ForwardRefDemo() {
  const inputRef = useRef();

  return (
    <div>
      <HYInput ref={inputRef}/>
      <button onClick={e => inputRef.current.focus()}>聚焦</button>
    </div>
  )
}
```

上面的做法本身没有什么问题，但是我们将子组件的 DOM 直接暴露给了父组件：

直接暴露给父组件带来的问题是某些情况的不可控，父组件可以拿到 DOM 后进行任意的操作，

所以要限制父组件对子组件的操作，

比如限制上面代码的 ForwardRefDemo 只能对 HYInput 聚焦

#### useImperativeHandle 的使用

```jsx
import React, { useRef, forwardRef, useImperativeHandle } from 'react';

const HYInput = forwardRef((props, ref) => {
  const inputRef = useRef();

  useImperativeHandle(ref, () => ({
    focus: () => {
      inputRef.current.focus();
    }
  }), [inputRef.current])

  return <input ref={inputRef} type="text"/>
})

export default function UseImperativeHandleHookDemo() {
  const inputRef = useRef();

  return (
    <div>
      <HYInput ref={inputRef}/>
      <button onClick={e => inputRef.current.focus()}>聚焦</button>
    </div>
  )
}
```

useImperativeHandle

第一个参数，接收一个通过 forwardRef 引用父组件的 ref 实例

第二个参数为一个回调函数，返回一个对象，对象里面存储需要暴露给父组件的属性或方法

第三个参数为一个可选参数，该参数是一个依赖项数组，就像 useEffect 那样

下面例子中，

ref 指的就是 inputRef ，

将第二个参数：回调函数里对象的方法绑定到第一个参数 ref 的 current，

第三个参数 [inputRef.current] 表示当 `<input/>`  发生变化时才更新第二个参数：回调函数里对象的方法

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1629645596161-414cefc1-997d-487a-9938-acc703657be6.jpeg)

要对 HYInput 的 `<input/>` 进行聚焦，需要使用 useRef

```jsx
import React, { useRef, forwardRef, useImperativeHandle } from 'react';

const HYInput = forwardRef(function (props, ref) {
  // 创建组件内部的ref
  const inputRef = useRef();

  useImperativeHandle(ref, () => ({
    focus: () => {
      inputRef.current.focus();
    },
    printHello: () => {
      console.log("Hello World")
    }
  }))

  // 这里绑定的是组件内部的inputRef
  return <input type="text" ref={inputRef}/>
})

export default function ImperativeHandleHookForwardDemo() {
  const inputRef = useRef();

  return (
    <div>
      <HYInput ref={inputRef}/>
      <button onClick={e => inputRef.current.focus()}>聚焦</button>
      <button onClick={e => inputRef.current.printHello()}>Hello World</button>
    </div>
  )
}
```

### useLayoutEffect（更新 DOM 前执行，较少使用）

useLayoutEffect 看起来和 useEffect 非常相似，事实上它们也只有一点区别而已：

- useEffect 会在渲染的内容更新到 DOM 之后执行，不会阻塞 DOM 的更新；
- **useLayoutEffect 和浏览器渲染同步执行，会在渲染的内容更新到 DOM 之前执行，会阻塞 DOM 的更新；**

**如果我们希望在某些操作发生之后再更新 DOM ，那么应该将这个操作放到 useLayoutEffect 。**

**useEffect 与 useLayoutEffect 的对比**

useLayoutEffect 在 useEffect  之前执行

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1629648845583-3667467a-e90e-48ba-ac1e-70a28d29a13b.jpeg)

下面这段代码在开发中会发生闪烁的现象；

```jsx
import React, { useState, useEffect } from 'react'

export default function EffectCounterDemo() {
  const [count, setCount] = useState(10);

  useEffect(() => {
    if (count === 0) {
      // 可使用 sleep 函数测试
      setCount(Math.random() + 200)
    }
  }, [count]);

  return (
    <div>
      <h2>数字: {count}</h2>
      <button onClick={e => setCount(0)}>修改数字</button>
    </div>
  )
}
```

因为 DOM 会更新两次：10  更新 => 0，0 更新 => 随机数 

我们先将 count 设置为了 0，那么 DOM 会被更新，并且会执行一次 useEffect 中的回调函数；

在 useEffect 中我们发现 count 为 0，又执行一次 setCount 操作，那么 DOM 会再次被更新，

并且 useEffect 又会被执行一次；

```jsx
import React, { useState, useEffect, useLayoutEffect } from 'react'

export default function LayoutEffectCounterDemo() {
  const [count, setCount] = useState(10);

  useLayoutEffect(() => {
    if (count === 0) {
      setCount(Math.random() + 200)
    }
  }, [count]);

  return (
    <div>
      <h2>数字: {count}</h2>
      <button onClick={e => setCount(0)}>修改数字</button>
    </div>
  )
}
```

如果我们使用 useLayoutEffect，那么会等到 useLayoutEffect 代码执行完毕后，再进行 DOM 的更新；

DOM 只更新一次：10 ，setCount(0) ，count 为 0 更新=> 随机数

useLayoutEffect 与 componentDidMount、componentDidUpdate 的调用阶段是一样的。

但是，**推荐一开始先用 useEffect，只有当它出问题的时候再尝试使用 useLayoutEffect 。**

#### useLayoutEffect 与 useEffect

正常情况用默认的 useEffect 钩子就够了，这可以保证状态变更，不阻塞渲染过程，

这也符合作者说的： **useEffect 的时期是非常晚，可以保证页面是稳定下来再做事情**。

但如果 effect 更新（清理）中涉及 DOM 更新操作，用 useEffect 就会有意想不到的效果，

这时我们最好使用 useLayoutEffect 。

比如：逐帧动画 requestAnimationFrame ，要做一个 useRaf hook 就得用上后者，需要保证同步变更。

钩子的执行顺序：`useLayoutEffect > requestAnimationFrame > useEffect`

### 自定义 Hook （复用 Hook，替代 HOC，mixin 终极版）

#### 认识自定义 Hook

假如所有的组件在创建和销毁时都要进行打印

组件被创建：打印 组件被创建了

组件被销毁：打印 组件被销毁了

有时所有的组件都需要有对应的逻辑

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1629728890961-380d74df-1881-4e4c-973f-8e2548204bd6.jpeg)

所以，要对它们的逻辑进行抽取，抽到一个函数里，

只不过这个函数是自定义 Hook （前缀需要加 use 的函数）才能在函数组件里使用

Hooks 和普通函数在语义上是有区别的，

如果你创建了一个 useXXX 的函数，但是内部并没有用任何其它 Hooks，

那么这个函数就不是一个 Hook，而只是一个普通的函数。

但是如果用了其它 Hooks，那么它就是一个 Hook 。

**自定义 Hook 一方面能让逻辑得到重用，另外一方面也能让代码更加语义化，并且易于理解和维护。**

 **自定义Hook 的两个特征：**

**1.名字一定是以 use 开头的函数，这样 React 才能够知道这个函数是一个 Hook；**

**2.函数内部一定调用了其它的 Hooks，可以是内置的 Hooks，也可以是其它自定义 Hooks。**

**这样才能够让组件刷新，或者产生副作用。**

示例：计数器 Hook

```jsx
import { useState, useCallback }from 'react';
 
function useCounter() {
  // 定义 count 这个 state 用于保存当前数值
  const [count, setCount] = useState(0);
  // 实现加 1 的操作
  const increment = useCallback(() => setCount(count + 1), [count]);
  // 实现减 1 的操作
  const decrement = useCallback(() => setCount(count - 1), [count]);
  // 重置计数器
  const reset = useCallback(() => setCount(0), []);
  
  // 将业务逻辑的操作 export 出去供调用者使用
  return { count, increment, decrement, reset };
}
```

```jsx
import React from 'react';

function Counter() {
  // 调用自定义 Hook
  const { count, increment, decrement, reset } = useCounter();
  // 渲染 UI
  return (
    <div>
      <button onClick={decrement}> - </button>
      <p>{count}</p>
      <button onClick={increment}> + </button>
      <button onClick={reset}> reset </button>
    </div>
  );
}
```

示例：打印组件的生命周期 Hook

```jsx
import React, { useEffect } from 'react';

// 自定义 Hook
function useLoggingLife(name) {
  useEffect(() => {
    console.log(`${name}组件被创建出来了`);

    return () => {
      console.log(`${name}组件被销毁掉了`);
    }
  }, []);
}

const Home = (props) => {
  useLoggingLife("Home");
  return <h2>Home</h2>
}

const Profile = (props) => {
  useLoggingLife("Profile");
  return <h2>Profile</h2>
}

export default function CustomLifeHookDemo01() {
  // 使用自定义 Hook
  useLoggingLife("CustomLifeHookDemo01");
  return (
    <div>
      <h2>CustomLifeHookDemo01</h2>
      <Home/>
      <Profile/>
    </div>
  )
}
```

效果图

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1629731101270-77e600c9-7cff-4b8d-9c13-d548db5ff04d.jpeg)

#### 自定义 Hook 原则

1、编写自定义 Hook 时，返回值一定要保持引用的一致性

```js
function Example() {
  const data = useData();
  const [dataChanged, setDataChanged] = useState(false);

  useEffect(() => {
    setDataChanged((prevDataChanged) => !prevDataChanged); 
    // 当 data 发生变化时，调用 setState。
    // 如果 data 值相同而引用不同，就可能会产生非预期的结果。 
  }, [data]);

  console.log(dataChanged);

  return <ExpensiveComponent data={data} />;
}

const useData = () => {
  // 获取异步数据  
  const resp = getAsyncData([]);

  // 处理获取到的异步数据，这里使用了 Array.map。
  // 因此，即使 data 相同，每次调用得到的引用也是不同的。  
  const mapper = (data) => data.map((item) => ({...item, selected: false}));

  return resp ? mapper(resp) : resp;
};
```

> 如果因为 prop 的值相同而引用不同，从而导致子组件发生 re-render，不一定会造成性能问题。
> 因为 Virtual DOM re-render ≠ DOM re-render。但是当子组件特别大时，Virtual DOM 的 Diff 开销也很大。
>因此，还是应该尽量避免子组件 re-render。

2、自定义 Hooks 的返回值可以使用 Tuple 类型，更易于在外部重命名

```js
export const useToggle = (defaultVisible: boolean = false) => {
  const [visible, setVisible] = useState(defaultVisible);
  const show = () => setVisible(true);
  const hide = () => setVisible(false);

  return [visible, show, hide] as [typeof visible, typeof show, typeof hide];
};

const [isOpen, open, close] = useToggle(); // 在外部可以更方便地修改名字
const [visible, show, hide] = useToggle();
```

3、如果返回值的数量超过三个，建议返回一个对象

#### 自定义 Hook 实现定时器

```jsx
import React, { useState， useEffect } from "react";
import ReactDOM from "react-dom";

function App() {
  const [count, setCount] = useState(0)
  const [countInTimeout, setCountInTimeout] = useState(0)

  useEffect(() => {
    setTimeout(() => {
      setCountInTimeout(count)
    }, 3000)
    setCount(5)
  }, [])

  return (
    <div>
      Count: {count}
      <br />
      setTimeout Count: {countInTimeout}
    </div>
  )
}

ReactDOM.render(<App />, document.getElementById("root"));
```

![ec62f75772f543cf8b1362401f91ef7e~tplv-k3u1fbpfcp-zoom-in-crop-mark_4536_0_0_0](https://cdn.nlark.com/yuque/0/2023/png/1614731/1684635365424-350d574e-a7cd-41a2-bec9-a19172a83258.png)

count 发生了变化，但是 3s 后 setTimout 的 count 却还是 0 。这就是 hooks 的闭包陷阱。

因为定时器中的回调函数被引用了，形成了闭包被一直保存着，

当调用 setState 后虽然组件重新渲染，但是 setTimeout 还是上一轮的，所以拿到的 state 就还是上一轮的 1，拿不到最新的值；

我们可以使用 useRef 来保存 setTimeout 的回调函数，那么在 setState 后，组件重新渲染，定时器中的回调也会更新，就可以拿到最新的值了。

```jsx
function useTimeout(callback, delay) {
  const memorizeCallback = useRef();

  useEffect(() => {
    memorizeCallback.current = callback;
  }, [callback]);

  useEffect(() => {
    if (delay !== null) {
      const timer = setTimeout(() => {
        memorizeCallback.current();
      }, delay);
      return () => {
        clearTimeout(timer);
      };
    }
  }, [delay]);
};
```

```jsx
function useInterval(callback, delay) {
  const memorizeCallback = useRef();

  useEffect(() => {
    memorizeCallback.current = callback;
  }, [callback]);

  useEffect(() => {
    if (delay !== null) {
      const timer = setInterval(() => {
        memorizeCallback.current();
      }, delay);
      return () => {
        clearInterval(timer);
      };
    }
  }, [delay]);
};
```

#### 自定义 Hook 实现请求数据

异步请求用户列表的例子：

```jsx
import React from "react";

export default function UserList() {
  // 使用三个 state 分别保存用户列表，loading 状态和错误状态
  const [users, setUsers] = React.useState([]);
  const [loading, setLoading] = React.useState(false);
  const [error, setError] = React.useState(null);
    
  // 定义获取用户的回调函数
  const fetchUsers = async () => {
    setLoading(true);
    try {
      const res = await fetch("https://reqres.in/api/users/");
      const json = await res.json();
      // 请求成功后将用户数据放入 state
      setUsers(json.data);
    } catch (err) {
      // 请求失败将错误状态放入 state
      setError(err);
    }
    setLoading(false);
  };
    
  return (
    <div className="user-list">
      <button onClick={fetchUsers} disabled={loading}>
        {loading ? "Loading..." : "Show Users"}
      </button>
      {error && 
        <div style={{ color: "red" }}>Failed: {String(error)}</div>
      }
      <br />
      <ul>
        {users && users.length > 0 &&
          users.map((user) => {
            return <li key={user.id}>{user.first_name}</li>;
          })}
      </ul>
    </div>
  );
}
```

在这里，我们定义了 users、loading 和 error 三个状态。

如果我们在异步请求的不同阶段去设置不同的状态，这样 UI 最终能够根据这些状态展现出来。

在每个需要异步请求的组件中，其实都需要重复相同的逻辑。 

事实上，在处理这类请求的时候，模式都是类似的，通常都会遵循下面步骤：

1.创建 data，loading，error 这 3 个 state；

2.请求发出后，设置 loading state 为true；

3.请求成功后，将返回的数据放到某个 state 中，并将 loading state 设为false；

4.请求失败后，设置 error state为 true，并将 loading state 设为 false。

最后，基于 data、loading、error 这 3 个state的数据，Ul 就可以正确地显示数据，

或者将 loading、error 这些反馈给用户了。

所以，通过创建一个 自定义Hook，可以很好地将这些逻辑提取出来，成为一个可重用的模块。

使用自定义 Hook 后：

```jsx
import { useState } from 'react';

const useAsync = (asyncFunction) => {
  // 设置三个异步逻辑相关的 state
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(false);
  const [error, setError] = useState(null);
    
  // 定义一个 callback 用于执行异步逻辑
  const execute = useCallback(() => {
    // 请求开始时，设置 loading 为 true，清除已有数据和 error 状态
    setLoading(true);
    setData(null);
    setError(null);
    return asyncFunction()
      .then((response) => {
        // 请求成功时，将数据写进 state，设置 loading 为 false
        setData(response);
        setLoading(false);
      })
      .catch((error) => {
        // 请求失败时，设置 loading 为 false，并设置错误状态
        setError(error);
        setLoading(false);
      });
  }, [asyncFunction]);
    
  return { execute, loading, data, error };
};
```

有了这个 Hook，在组件中就只需要关心与业务逻辑相关的部分。

```jsx
import React from "react";
import useAsync from './useAsync';

export default function UserList() {
  // 通过 useAsync 这个函数，只需要提供异步逻辑的实现
  const {
    execute: fetchUsers,
    data: users,
    loading,
    error,
  } = useAsync(async () => {
    const res = await fetch("https://reqres.in/api/users/");
    const json = await res.json();
    return json.data;
  });
  
  return (
    // 根据状态渲染 UI...
  );
}
```

这种类型的封装我写一个工具类不可以吗？为什么一定要通过 Hooks 进行封装呢？

答案很容易就能想到。

因为在 Hooks 中，你可以管理当前组件的 state，从而将更多的逻辑写在可重用的 Hooks 中。

但是要知道，在普通的工具类中是无法直接修改组件 state 的，

那么也就无法在数据改变的时候触发组件的重新渲染。

#### 自定义Hook 实现 Context 共享

将所有自定义 Hook 放进 hooks 目录

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1629732208822-501b8056-2488-401b-b5a1-5e7c87ea588f.jpeg)

App.js

```jsx
import React, { useState, createContext } from 'react';
import CustomContextShareHook from './11_自定义Hook/02_自定义Hook练习-Context共享';

export const UserContext = createContext();
export const TokenContext = createContext();

export default function App() {
  const [show, setShow] = useState(true);
  
  return (
    <div>
      <UserContext.Provider value={{name: "why", age: 18}}>
        <TokenContext.Provider value="fdafdafafa">
          <CustomContextShareHook/>
        </TokenContext.Provider>
      </UserContext.Provider>
      <button onClick={e => setShow(!show)}>切换</button>
    </div>
  )
}
```

hooks/user-hook.js

```jsx
import { useContext } from "react";
import { UserContext, TokenContext } from "../App";

function useUserContext() {
  const user = useContext(UserContext);
  const token = useContext(TokenContext);

  return [user, token];
}

export default useUserContext;
```

 CustomContextShareHook

```jsx
import React, { useContext } from 'react';
import useUserContext from '../hooks/user-hook';

export default function CustomContextShareHook() {
  const [user, token] = useUserContext();
  console.log(user, token);

  return (
    <div>
      <h2>CustomContextShareHook</h2>
    </div>
  )
}
```

效果图

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1629730842889-b9b06ff8-6370-431a-950a-0066558fc0d3.jpeg)

#### 自定义 Hook 实现获取窗口大小

```jsx
// myhooks.js
// 下面自定义了一个获取窗口长宽值的hooks
import React, { useState, useEffect, useCallback } from 'react'

function useWinSize() {
  const [size, setSize] = useState({
    width: document.documentElement.clientWidth,
    height: document.documentElement.clientHeight
  })
  
  const onResize = useCallback(() => {
    setSize({
      width: document.documentElement.clientWidth,
      height: document.documentElement.clientHeight
    })
  }, [])

  useEffect(() => {
    window.addEventListener('resize', onResize)
    return () => {
      window.removeEventListener('resize', onResize)
    }
  }, [onResize])
  
  return size
}

export const useWinSize
```

```jsx
import { useWinSize } from './myhooks'

function MyHooksComponent() {
  const size = useWinSize()
  return (
    <div>
      页面Size:{size.width}x{size.height}
    </div>
  )
}

export default MyHooksComponent
```

Popup 组件依赖视窗宽度适配自身显示宽度、相册组件依赖视窗宽度做单/多栏布局适配

```jsx
function useWinSize() {
  const html = document.documentElement;
  const [ size, setSize ] = useState({ width: html.clientWidth, height: html.clientHeight });

  useEffect(() => {
    const onSize = e => {
      setSize({ width: html.clientWidth, height: html.clientHeight });
    };

    window.addEventListener('resize', onSize);

    return () => {
      window.removeEventListener('resize', onSize);
    };
  }, [ html ]);

  return size;
}

// 依赖win宽度，适配图片布局
function Article(props) {
  const { width } = useWinSize();
  const cls = `layout-${width >= 540 ? 'multi' : 'single'}`;

  return (
    <>
      <article>{props.content}<article>
      <div className={cls}>recommended thumb list</div>
    </>
  );
}

// 弹层宽度根据win宽高做适配
function Popup(props) {
  const { width, height } = useWinSize();
  const style = {
    width: width - 200,
    height: height - 300,
  };
  return (<div style={style}>{props.content}</div>);
}
```

#### 自定义 Hook 实现获取滚动位置

hooks/scroll-position-hook.js

```jsx
import { useState, useEffect } from 'react';

function useScrollPosition() {
  const [scrollPosition, setScrollPosition] = useState(0);

  useEffect(() => {
    const handleScroll = () => {
      setScrollPosition(window.scrollY);
    }
    document.addEventListener("scroll", handleScroll);

    return () => {
      document.removeEventListener("scroll", handleScroll)
    }
  }, []);

  return scrollPosition;
}

export default useScrollPosition;
```

CustomScrollPositionHook

```jsx
import React, { useEffect, useState } from 'react'
import useScrollPosition from '../hooks/scroll-position-hook'

export default function CustomScrollPositionHook() {
  const position = useScrollPosition();

  return (
    <div style={{padding: "1000px 0"}}>
      <h2 style={{position: "fixed", left: 0, top: 0}}>CustomScrollPositionHook: {position}</h2>
    </div>
  )
}
```

App.js

```jsx
import React, { useState, createContext } from 'react';
import CustomScrollPositionHook from './11_自定义Hook/03_自定义Hook练习-获取滚动位置';

export default function App() {
  return (
    <div>
      <CustomScrollPositionHook/> 
    </div>
  )
}
```

效果图

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1629789936466-e3883534-9d69-47a4-a509-8a0452abd2be.jpeg)

#### 自定义 Hook 实现回到顶部

```jsx
import { useState, useEffect } from 'react';

// 获取横向，纵向滚动条位置
const getPosition = () => {
  return {
    x: document.body.scrollLeft,
    y: document.body.scrollTop,
  };
};

const useScroll = () => {
  // 定一个 position 这个 state 保存滚动条位置
  const [position, setPosition] = useState(getPosition());
  useEffect(() => {
    const handler = () => {
      setPosition(getPosition(document));
    };
    // 监听 scroll 事件，更新滚动条位置
    document.addEventListener("scroll", handler);
    return () => {
      // 组件销毁时，取消事件监听
      document.removeEventListener("scroll", handler);
    };
  }, []);
  return position;
};
```

```jsx
import React, { useCallback } from 'react';
import useScroll from './useScroll';

function ScrollTop() {
  const { y } = useScroll();
  
  const goTop = useCallback(() => {
    document.body.scrollTop = 0;
  }, []);
  
  const style = {
    position: "fixed",
    right: "10px",
    bottom: "10px",
  };
  
  // 当滚动条位置纵向超过 300 时，显示返回顶部按钮
  if (y > 300) {
    return (
      <button onClick={goTop} style={style}>
        Back to Top
      </button>
    );
  }
  // 否则不 render 任何 UI
  return null;
}
```

#### 自定义 Hook 实现 localStorage 存储

hooks/scroll-position-hook.js

```jsx
import {useState, useEffect} from 'react';

function useLocalStorage(key) {
  const [name, setName] = useState(() => {
    const name = JSON.parse(window.localStorage.getItem(key));
    return name;
  });

  useEffect(() => {
    window.localStorage.setItem(key, JSON.stringify(name));
  }, [name]);

  return [name, setName];
}

export default useLocalStorage;
```

CustomDataStoreHook

```jsx
import React, { useState, useEffect } from 'react';

import useLocalStorage from '../hooks/local-store-hook';

export default function CustomDataStoreHook() {
  const [name, setName] = useLocalStorage("name");

  return (
    <div>
      <h2>CustomDataStoreHook: {name}</h2>
      <button onClick={e => setName("kobe")}>设置name</button>
    </div>
  )
}
```

App.js

```jsx
import React, { useState, createContext } from 'react';
import CustomDataStoreHook from './11_自定义Hook/04_自定义Hook练习-localStorage存储';

export default function App() {
  const [show, setShow] = useState(true);
  
  return (
    <div>
      <CustomDataStoreHook/> 
      <button onClick={e => setShow(!show)}>切换</button>
    </div>
  )
}
```

效果图

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1629790605506-bcf12f76-e311-4c2f-b57f-d66780aff503.jpeg)

#### 拆分复杂组件

设想当你接手某个项目，发现一个函数动辄就超过了 500 行，那会是什么感受？

所以**“保持每个函数的短小”这样通用的最佳实践，同样适用于函数组件。**

只有这样，才能让代码始终易于理解和维护。

关键问题就是，**怎么才能让函数组件不会太过冗长呢？**

**尽量将相关的逻辑做成独立的 Hooks，**

**然后在函数组中使用这些 Hooks，通过参数传递和返回值让 Hooks 之间完成交互。**

注意一下，拆分逻辑的目的不一定是为了重用，而可以是仅仅为了业务逻辑的隔离。

所以在这个场景下，我们不一定要把 Hooks 放到独立的文件中，而是可以和函数组件写在一个文件中。

这么做的原因是这些 Hooks 是和当前函数组件紧密相关的，所以写到一起，反而更容易阅读和理解。

设想现在有这样一个需求：我们需要展示一个博客文章的列表，并且有一列要显示文章的分类。

同时，我们还需要提供表格过滤功能，以便能够只显示某个分类的文章。

为了支持过滤功能，后端提供了两个 APl：一个用于获取文章的列表，另一个用于获取所有的分类。

这就需要我们在前端将文章列表返回的数据分类 ID 映射到分类的名字，以便显示在列表里。

如果按照直观的思路去实现，通常都会把逻辑都写在一个组件里：

```jsx
function BlogList() {
  // 获取文章列表...
  // 获取分类列表...
  // 组合文章数据和分类数据...
  // 根据选择的分类过滤文章...
  
  // 渲染 UI ...
}
```

如果你是在写一个其它的普通函数，会不会将其中一些逻辑写成单独的函数呢？

相信答案是肯定的，因为这样做可以隔离业务逻辑，让代码更加清楚。

很多人在写函数组件时没有意识到 Hooks 就是普通的函数，

所以通常不会这么去做隔离，而是习惯于一路写下来，这就会造成某个函数组件特别长。

还是老生常谈的那句话，改变这个状况的关键仍然在于开发思路的转变。

我们要**把 Hooks 就看成普通的函数，能隔离的尽量去做隔离，**

从而让代码更加模块化，更易于理解和维护。

针对这样一个功能，我们甚至可以将其拆分成4个Hooks，每一个Hook都尽量小，代码如下：

```jsx
import React, { useEffect, useCallback, useMemo, useState } from "react";
import { Select, Table } from "antd";
import _ from "lodash";
import useAsync from "./useAsync";

const endpoint = "https://myserver.com/api/";
const useArticles = () => {
  // 使用上面创建的 useAsync 获取文章列表
  const { execute, data, loading, error } = useAsync(
    useCallback(async () => {
      const res = await fetch(`${endpoint}/posts`);
      return await res.json();
    }, []),
  );
  // 执行异步调用
  useEffect(() => execute(), [execute]);
  // 返回语义化的数据结构
  return {
    articles: data,
    articlesLoading: loading,
    articlesError: error,
  };
};

const useCategories = () => {
  // 使用上面创建的 useAsync 获取分类列表
  const { execute, data, loading, error } = useAsync(
    useCallback(async () => {
      const res = await fetch(`${endpoint}/categories`);
      return await res.json();
    }, []),
  );
  // 执行异步调用
  useEffect(() => execute(), [execute]);
  // 返回语义化的数据结构
  return {
    categories: data,
    categoriesLoading: loading,
    categoriesError: error,
  };
};

const useCombinedArticles = (articles, categories) => {
  // 将文章数据和分类数据组合到一起
  return useMemo(() => {
    // 如果没有文章或者分类数据则返回 null
    if (!articles || !categories) return null;
    return articles.map((article) => {
      return {
        ...article,
        category: categories.find(
          (c) => String(c.id) === String(article.categoryId),
        ),
      };
    });
  }, [articles, categories]);
};

const useFilteredArticles = (articles, selectedCategory) => {
  // 实现按照分类过滤
  return useMemo(() => {
    if (!articles) return null;
    if (!selectedCategory) return articles;
    return articles.filter((article) => {
      console.log("filter: ", article.categoryId, selectedCategory);
      return String(article?.category?.name) === String(selectedCategory);
    });
  }, [articles, selectedCategory]);
};

const columns = [
  { dataIndex: "title", title: "Title" },
  { dataIndex: ["category", "name"], title: "Category" },
];

export default function BlogList() {
  const [selectedCategory, setSelectedCategory] = useState(null);
  // 获取文章列表
  const { articles, articlesError } = useArticles();
  // 获取分类列表
  const { categories, categoriesError } = useCategories();
  // 组合数据
  const combined = useCombinedArticles(articles, categories);
  // 实现过滤
  const result = useFilteredArticles(combined, selectedCategory);
  
  // 分类下拉框选项用于过滤
  const options = useMemo(() => {
    const arr = _.uniqBy(categories, (c) => c.name).map((c) => ({
      value: c.name,
      label: c.name,
    }));
    arr.unshift({ value: null, label: "All" });
    return arr;
  }, [categories]);
  
  // 如果出错，简单返回 Failed
  if (articlesError || categoriesError) return "Failed";
  
  // 如果没有结果，说明正在加载
  if (!result) return "Loading...";
  
  return (
    <div>
      <Select
        value={selectedCategory}
        onChange={(value) => setSelectedCategory(value)}
        options={options}
        style={{ width: "200px" }}
        placeholder="Select a category"
      />
      <Table dataSource={result} columns={columns} />
    </div>
  );
}
```

通过这样的方式，我们就把一个较为复杂的逻辑拆分成一个个独立的 Hook 了，

不仅隔离了业务逻辑，也让代码在语义上更加明确。

比如说有 useArticles、useCategories 这样与业务相关的名字，就非常易于理解。

### 认识 Fiber

电脑屏幕参数：刷新率，一秒钟刷新 60 次，60帧/秒，60 HZ

浏览器跟电脑刷新率相同，电脑 会传 vsync 同步信号给浏览器，

浏览器一旦收到  vsync 同步信号就会刷新一帧，

手机屏幕参数可达：120HZ，

视频一般是 30帧/秒，

例子：一部电影，里面有人在使用电脑，

现实中电脑来回移动鼠标比电影里人物来回移动鼠标要流畅

GPU 绘制一帧一帧的图像交给电脑，

电脑按照固定的频率取一帧一帧的图像，

电脑从一帧一帧的图像缓存里取（二级缓存、三级缓存），防止屏幕出现撕裂

电脑根据刷新率发送同步信号GPU决定什么时候绘制新的一帧图像，最后电脑从缓存中取

即使是浏览器的滚动也是从一帧图像跳到另一帧图像

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1629791985149-26e245c5-3b48-4f17-8411-1196500a07f0.jpeg)

### 异步处理

如何给所有请求都带上一个 Token 供服务器端验证？如何发起并发请求？

#### 实现 API Client 

无论大小项目，在开始实现第一个请求的时候，

通常我们要做的第一件事应该都是创建一个自己的 API Client，

之后所有的请求都会通过这个 Client 发出去。

而不是上来就用 fetch 或者是 axios 去直接发起请求，因为那会造成大量的重复代码。

实现这样一个 Client 之后，就有了一个统一的地方，

去对需要连接的服务端做一些通用的配置和处理，比如 Token 、URL 、错误处理等等。

**实现这样一个 API Client ，通常来说，会包括以下几个方面：**

**1.一些通用的 Header。**

**比如 Authorization Token 。**

**2.服务器地址的配置。**

**前端在开发和运行时可能会连接不同的服务器，**

**比如本地服务器或者测试服务器，此时这个 API Client 内部可以根据当前环境判断该连接哪个 URL 。**

**3.请求未认证的处理。**

**比如如果 Token 过期了，需要有一个统一的地方进行处理，这时就会弹出对话框提示用户重新登录。**

从经验来看，更推荐把 axios 作为基础来实现这个功能。

原因就在于，axios 比起 fetch，提供了更为方便，也更加语义化的 API，比如请求拦截。

此外，还很容易创建多个实例，让代码逻辑更简洁。

```jsx
import axios from "axios";

// 定义相关的 endpoint
const endPoints = {
  test: "https://60b2643d62ab150017ae21de.mockapi.io/",
  prod: "https://prod.myapi.io/",
  staging: "https://staging.myapi.io/"
};

// 创建 axios 的实例
const instance = axios.create({
  // 实际项目中根据当前环境设置 baseURL
  baseURL: endPoints.test,
  timeout: 30000,
  // 为所有请求设置通用的 header
  headers: { Authorization: "Bear mytoken" }
});

// 通过 axios 定义拦截器预处理所有请求
instance.interceptors.response.use(
  (res) => {
    // 可以假如请求成功的逻辑，比如 log
    return res;
  },
  (err) => {
    if (err.response.status === 403) {
      // 统一处理未授权请求，跳转到登录界面
      document.location = '/login';
    }
    return Promise.reject(err);
  }
);

export default instance;
```

定义了一个简单的 API Client，

之后所有的请求都可以通过 Client 连接到指定的服务器，

从而不再需要单独设置 Header，或者处理未授权的请求了。

从 Hooks 角度来说，我们可以认为一个 Get 请求就是一个远程数据源。

那么把这个数据源封装成 Hooks 后，使用远程 APl 将会非常方便。

下面这张图可以比较直观地描述这种模式。

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1632753887518-7444aade-c55f-4120-a188-9374676ad49d.jpeg)

对于一个 Get 类型的APl，我们完全可以将它看成一个远程的资源。

只是和本地数据不同的地方在于，它有三个状态，分别是：

1.Data：指的是请求成功后服务器返回的数据；

2.Error：请求失败的话，错误信息将放到 Error 状态里；

3.Pending：请求发出去，在返回之前会处于 Pending 状态。

有了这三个状态，我们就能够在 UI 上去显示 loading，error 或者获取成功的数据了。

使用起来会非常方便。

要实现这样一个 Hook 其实也并不难，

比起在组件内部直接发请求，我们只是把代码换了个地方，也就是写到了 Hook 里面。

下面是代码的实现：

```jsx
import { useState, useEffect } from "react";
import apiClient from "./apiClient";

// 将获取文章的 API 封装成一个远程资源 Hook
const useArticle = (id) => {
  // 设置三个状态分别存储 data, error, loading
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(false);
  const [error, setError] = useState(null);
  
  useEffect(() => {
    // 重新获取数据时重置三个状态
    setLoading(true);
    setData(null);
    setError(null);
    apiClient
      .get(`/posts/${id}`)
      .then((res) => {
        // 请求成功时设置返回数据到状态
        setLoading(false);
        setData(res.data);
      })
      .catch((err) => {
        // 请求失败时设置错误状态
        setLoading(false);
        setError(err);
      });
  }, [id]); // 当 id 变化时重新获取数据
  
  // 将三个状态作为 Hook 的返回值
  return {
    loading,
    error,
    data
  };
};
```

要显示一篇文章的时候，你的脑子里就不再是一个具体的 API 调用，

可以把它看作一个数据，只不过是个远程数据，于是很自然就有加载状态或者错误状态这些数据了。

使用的时候，我们就可以把组件的表现层逻辑写得非常简洁：

```jsx
import useArticle from "./useArticle";

const ArticleView = ({ id }) => {
  // 将 article 看成一个远程资源，有 data, loading, error 三个状态
  const { data, loading, error } = useArticle(id);
  if (error) return "Failed.";
  if (!data || loading) return "Loading...";
  return (
    <div className="exp-09-article-view">
      <h1>
        {id}. {data.title}
      </h1>
      <p>{data.content}</p>
    </div>
  );
};
```

有了这样一个 Hook，React 的函数组件几乎不需要有任何业务的逻辑，

只是把数据映射到 JSX 并显示出来就可以了，在使用的时候非常方便。

**为什么要给每个请求都定义一个 Hook 呢？**

**我们直接提供一个通用的 Hook，比如 useRemoteData，然后把 API 地址传进去，难道不可以吗？**

不是完全不可以，但**这其实是为了保证每个Hook 自身足够简单。**

1、一般来说，为了让服务器的返回数据满足 UI上的展现要求，通常需要进一步处理。

而这个对于每个请求的处理逻辑可能都不一样，通过一定的代码重复，能够避免产生太复杂的逻辑。

2、同时，某个远程资源有可能是由多个请求组成的，

因为要同时发出去多个请求，组成 UI 展现所需要的数据，

那么 Hooks 中的逻辑就会不一样，

所以，将每个Get 请求都封装成一个Hook，也是为了让逻辑更清楚。

**这个模式仅适用于 Get 请求的逻辑。**

**对于其它类型，可以使用之前的 useAsync 自定义Hook，**

**同样也是用 Hook 的思想，把请求的不同状态封装成了一个数据源供组件使用。**

#### 如何处理并发与串行请求？

我们只是简单显示了文章的内容，实际场景肯定比这个更复杂。

比如，还需要显示作者、作者头像，以及文章的评论列表。

那么，作为一个完整的页面，就需要发送三个请求：

1.获取文章内容；

2.获取作者信息，包括名字和头像的地址；

3.获取文章的评论列表；

这三个请求同时包含了并发和串行的场景：

文章内容和评论列表是两个可以并发的请求，它们都通过 Article ID 来获取；

用户的信息需要等文章内容返回，这样才能知道作者的 ID，

从而根据用户的 ID 获取用户信息，这是一个串行的场景。

如果用传统的思路去实现，可能会写下这样一段代码，或者类似的代码：

```jsx
// 并发获取文章和评论列表
const [article, comments] = await Promise.all([
  fetchArticle(articleId),
  fetchComments(articleId)
]);

// 得到文章信息后，通过 userId 获取用户信息
const user = await fetchUser(article.userId);
```

但是，React 函数组件是一个同步的函数，没有办法直接使用 await 这样的同步方法，

而是要把请求通过副作用去触发。

因此如果按照上面这种传统的思维，是很难把逻辑理顺的。

这时候我们就要回到 React 的本质，那就是状态驱动 Ul 。

这意味着我们可以从状态变化的角度去组织异步调用。

函数组件的每一次 render，其实都提供了我们根据状态变化执行不同操作的机会，

我们思考的路径，就是利用这个机制，通过不同的状态组合，来实现异步请求的逻辑。

那么显示作者和评论列表的业务需求，主要的实现思路就包括下面四点：

1.组件首次渲染，只有文章 ID 这个信息，产生两个副作用去获取文章内容和评论列表；

2.组件首次渲染，作者 ID 还不存在，因此不发送任何请求；

3.文章内容请求返回后，获得了作者 ID，然后发送请求获取用户信息；

4.展示用户信息。

这里的任何一个副作用，也就是异步请求，都是基于数据的状态去进行的。

只有当文章的数据返回之后，我们才能得到作者 ID，从而再发送另外一个请求来获取作者信息。

基于一个数据状态的变化，我们就实现了串行发送请求这个功能。

所以，在代码层面，我们首先需要对 useUser 这个 Hook 做一个改造，

使得它在没有传入 ID 的情况下，不发送请求。

对比上面的 useArticle 这个Hook，唯一的变化就是在 useEffect 里加入了 ID 是否存在的判断：

```jsx
import { useState, useEffect } from "react";
import apiClient from "./apiClient";

export default (id) => {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(false);
  const [error, setError] = useState(null);
  
  useEffect(() => {
    // 当 id 不存在，直接返回，不发送请求
    if (!id) return;
    setLoading(true);
    setData(null);
    setError(null);
    apiClient
      .get(`/users/${id}`)
      .then((res) => {
        setLoading(false);
        setData(res.data);
      })
      .catch((err) => {
        setLoading(false);
        setError(err);
      });
  }, [id]);
  
  return {
    loading,
    error,
    data
  };
};
```

文章展示页面，代码实现：

```jsx
import { useState } from "react";
import CommentList from "./CommentList";
import useArticle from "./useArticle";
import useUser from "./useUser";
import useComments from "./useComments";

const ArticleView = ({ id }) => {
  const { data: article, loading, error } = useArticle(id);
  const { data: comments } = useComments(id);
  const { data: user } = useUser(article?.userId);
  
  if (error) return "Failed.";
  if (!article || loading) return "Loading...";
  
  return (
    <div className="exp-09-article-view">
      <h1>
        {id}. {article.title}
      </h1>
      {user && (
        <div className="user-info">
          <img src={user.avatar} height="40px" alt="user" />
          <div>{user.name}</div>
          <div>{article.createdAt}</div>
        </div>
      )}
      <p>{article.content}</p>
      <CommentList data={comments || []} />
    </div>
  );
};
```

再理一下并发和串行请求的思路。

因为文章的 ID 已经传进来了，

因此 useArticle 和 useComments 这两个 Hooks 会发出两个并发的请求，来分别获取信息。

而 userUser 这个Hook 则需要等 article 内容返回后，才能获得 userld 信息，所以这是一个串行的请求：

需要等文章内容的请求完成之后才能发起。

最终页面显示效果：

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1632755229507-c9d15537-ce00-48a7-b122-bf2a9428f4a4.jpeg)

### 函数组件设计模式

#### 容器模式实现按条件执行 Hook

比如一个对话框组件，通过 visible 属性来控制是否显示。

那么在 visible 为 false 的时候，其实不应该执行任何对话框内部的逻辑，因为还没展示在 Ul 上。

需要注意，只有在 visible 为 true 的时候才应该去执行业务逻辑，展现数据。

那么我们期望的代码可能是下面的方式：

```jsx
import { Modal } from "antd";
import useUser from "../09/useUser";

function UserInfoModal({ visible, userId, ...rest }) {
  // 当 visible 为 false 时，不渲染任何内容
  if (!visible) return null;
  // 这一行 Hook 在可能的 return 之后，会报错！
  const { data, loading, error } = useUser(userId);
  
  return (
    <Modal visible={visible} {...rest}>
      {/* 对话框的内容 */}
    </Modal>
  );
};
```

我们期望在对话框隐藏时通过返回 null 不去渲染任何内容，这个逻辑看上去非常自然直观。

但是，它无法通不过编译，因为在 return 语句之后使用了useUser 这个 Hook 。

可以看到，因为 Hooks 使用规则的存在，使得有时某些逻辑无法直观地实现。

换句话说，Hooks 在带来众多好处的同时，也或多或少带来了一些局限。

因此，我们需要用一个间接的模式来实现这样的逻辑，可以称之为容器模式。

**具体做法就是把条件判断的结果放到两个组件之中，**

**确保真正 render Ul 的组件收到的所有属性都是有值的。**

针对刚才我们讲的例子，就可以在 UserlnfoModal 外层加一个容器，这样就能实现条件渲染了。

实现的代码如下：

```jsx
// 定义一个容器组件用于封装真正的 UserInfoModal
export default function UserInfoModalWrapper({
  visible,
  ...rest, // 使用 rest 获取除了 visible 之外的属性
}) {
  // 如果对话框不显示，则不 render 任何内容
  if (!visible) return null; 
  // 否则真正执行对话框的组件逻辑
  return <UserInfoModal visible {...rest} />;
}
```

#### render props 重用 UI 逻辑

**render props 就是把一个 render 函数作为属性传递给某个组件，**

**由这个组件去执行这个函数从而 render 实际的内容。**

在 Class 组件时期，render props 和 HOC（高阶组件）两种模式是进行逻辑重用的两把利器，

但是实际上，HOC 的所有场景几乎都可以用 render props 来实现。

可以说，Hooks 是逻辑重用的第一选择。

如今的函数组件情况下，**Hooks 有一个局限，那就是只能用作数据逻辑的重用**，

一旦涉及 UI 表现逻辑的重用，就有些力不从心了，而这正是 render props 擅长的地方。

所以，**即使有了 Hooks，我们也要掌握 render props 这个设计模式的用法。**

例子仍然是我们熟悉的计数器。

有两个按钮，加一和减一，并将当前值显示在界面上。执行的效果如下图所示：

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1633845772990-d0ccc072-0bfd-476b-a075-b778f85affff.jpeg)

如果不考虑 Ul 的展现，这里要抽象的业务逻辑就是计数逻辑，包括三个部分：

1.count：当前计数值；

2.increase：让数值加 1 的方法；

3.decrease：让数值减 1 的方法。

如果用 render props 模式把这部分逻辑封装起来，

那就可以在不同的组件中使用，由使用的组件自行决定UI如何展现。

下面的代码是计数器 render props 的实现：

```jsx
import { useState, useCallback } from "react";

function CounterRenderProps({ children }) {
  const [count, setCount] = useState(0);
  const increment = useCallback(() => {
    setCount(count + 1);
  }, [count]);
  const decrement = useCallback(() => {
    setCount(count - 1);
  }, [count]);
    
  return children({ count, increment, decrement });
}
```

我们要把计数逻辑封装到一个自己不 render 任何 Ul 的组件中，那么在使用的时候可以用如下的代码：

```jsx
function CounterRenderPropsExample() {
  return (
    <CounterRenderProps>
      {({ count, increment, decrement }) => {
        return (
          <div>
            <button onClick={decrement}>-</button>
            <span>{count}</span>
            <button onClick={increment}>+</button>
          </div>
        );
      }}
    </CounterRenderProps>
  );
}
```

这里利用了 children 这个特殊属性。

也就是组件开始 tag 和结束 tag 之间的内容，其实是会作为 children 属性传递给组件。

那么在使用的时候，是直接传递了一个函数过去，

由实现计数逻辑的组件去调用这个函数，

并把相关的三个参数 count，increase 和 decrease 传递给这个函数。

当然，我们完全也可以使用其它的属性名字，而不是 children 。

我们只需要把这个 render 函数作为属性传递给组件就可以了，这也正是 render props 这个名字的由来。

这个例子演示了纯数据逻辑的重用，也就是重用的业务逻辑自己不产生任何 Ul 。

在这种场景下，其实用 Hooks 是更方便的，在自定义 Hook 中，

其实已经给过计数器的 Hook 实现的例子，代码如下：

```jsx
import { useState, useCallback }from 'react';
 
function useCounter() {
  // 定义 count 这个 state 用于保存当前数值
  const [count, setCount] = useState(0);
  // 实现加 1 的操作
  const increment = useCallback(() => setCount(count + 1), [count]);
  // 实现减 1 的操作
  const decrement = useCallback(() => setCount(count - 1), [count]);
  
  // 将业务逻辑的操作 export 出去供调用者使用
  return { count, increment, decrement };
}
```

显然，使用 Hooks 的方式是更简洁的。

这也是为什么我们经常说 Hooks 能够替代 render props 这个设计模式。

但是，需要注意的是，**Hooks 仅能替代纯数据逻辑的 render props 。**

**如果有 UI 展示的逻辑需要重用，那么我们必须借助于 render props 的逻辑，**

**这就是一再强调必须要掌握 render props 这种设计模式的原因。**

比如，我们需要显示一个列表，

如果超过一定数量，则把多余的部分折叠起来，通过一个弹出框去显示。

下面这张图可以比较直观地展示这个需求的实际运行效果：

<img src="https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1633846332559-f6ce3cee-4bbb-41c7-a6a5-44150c9d90a5.jpeg" alt="img" style="zoom:80%;" />

这里展示了两个列表。

**上面的列表只显示用户名，这在一些社交软件的界面上很常见，只显示几个点赞的用户，**

**多余的用一个数字来表示，鼠标移上去则跳转或者显示完整列表。**

**下面的列表是一个表格，但是也只显示前面5个，多余的折叠到“更多…”里面。**

对于上面的列表，鼠标移上去后的效果如下图所示

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1633846574402-a38a2967-7352-4439-a04c-340e8c71d961.jpeg)

对于这一类场景，**功能相同的部分是：数据超过一定数量时，显示一个“更多…"的文字；**

**鼠标移上去则弹出一个框，用于显示其它的数据。**

**功能不同的部分是：每一个列表项如何渲染，是在使用的时候决定的。**

因此，对于这一类具有 UI 逻辑重用需求的场景，我们就无法通过 Hooks 实现，

需要通过 render props 这个 设计模式。

下面代码实现了包含 render props 的 ListWithMore 组件：

```jsx
import { Popover } from "antd";

function ListWithMore({ renderItem, data = [], max }) {
  const elements = data.map((item, index) => renderItem(item, index, data));
  const show = elements.slice(0, max);
  const hide = elements.slice(max);
    
  return (
    <span className="exp-10-list-with-more">
      {show}
      {hide.length > 0 && (
        <Popover content={<div style={{ maxWidth: 500 }}>{hide}</div>}>
          <span className="more-items-wrapper">
            and{" "}
            <span className="more-items-trigger"> {hide.length} more...</span>
          </span>
        </Popover>
      )}
    </span>
  );
}
```

可以看到，这个组件接收了三个参数，分别是：

1.renderItem：用于接收一个函数，由父组件决定如何渲染一个列表项；

2.data：需要渲染的数据；

3.max：最多显示几条数据。

这样，任何有类似需求的场景就都可以用这个组件去实现了。

下面代码展示了图中用户列表两个场景的实现：

```jsx
// 这里用一个示例数据
import data from './data';

function ListWithMoreExample () => {
  return (
    <div className="exp-10-list-with-more">
      <h1>User Names</h1>
      <div className="user-names">
        Liked by:{" "}
        <ListWithMore
          renderItem={(user) => {
            return <span className="user-name">{user.name}</span>;
          }}
          data={data}
          max={3}
        />
      </div>
      <br />
      <br />
      <h1>User List</h1>
      <div className="user-list">
        <div className="user-list-row user-list-row-head">
          <span className="user-name-cell">Name</span>
          <span>City</span>
          <span>Job Title</span>
        </div>
        <ListWithMore
          renderItem={(user) => {
            return (
              <div className="user-list-row">
                <span className="user-name-cell">{user.name}</span>
                <span>{user.city}</span>
                <span>{user.job}</span>
              </div>
            );
          }}
          data={data}
          max={5}
        />
      </div>
    </div>
  );
};
```



