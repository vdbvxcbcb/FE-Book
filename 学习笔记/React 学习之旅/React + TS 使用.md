

## React - 类型定义速查表

### `React.FC<Props>` | `React.FunctionComponent<Props>`

表示函数组件的类型

```tsx
const MyComponent: React.FC<Props> = ...
```

### `React.Component<Props, State>`

表示class组件的类型

```tsx
class MyComponent extends React.Component<Props, State> { ...}
```

### `React.ComponentType<Props>`

表示 (React.FC | React.Component) 集合的类型 - 用于 HOC

```tsx
const withState = <P extends WrappedComponentProps>(
  WrappedComponent: React.ComponentType<P>,
) => { ...}
```

### `React.ComponentProps<typeof XXX>`

取得组件 XXX 的 Props 类型（警告：无法用于静态声明的 default props 以及泛型 props）

```tsx
type MyComponentProps = React.ComponentProps<typeof MyComponent>;
```

### `React.ReactElement` | `JSX.Element`

表示 React 中 Element 概念的类型 - 表示一个原生 DOM 组件（比如 `<div />`）或用户自定义的复合组件 （比如 `<MyComponent />`）

```tsx
const elementOnly: React.ReactElement = <div /> || <MyComponent />;
```

### `React.ReactNode`

表示任意类型的 React 节点（相当于 ReactElement (包括 Fragments 和 Portals) + 原始 JS 类型）

```tsx
const elementOrPrimitive: React.ReactNode = 'string' || 0 || false || null || undefined || <div /> || <MyComponent />;
const Component = ({ children: React.ReactNode }) => ...
```

### `React.CSSProperties`

表示 JSX 中样式对象的类型 - 实现 css-in-js 风格

```tsx
const styles: React.CSSProperties = { flexDirection: 'row', ...
const element = <div style={styles} ...
```

### `React.HTMLProps<HTMLXXXElement>`

表示指定 HTML 元素的类型 - 用于扩展 HTML 元素

```tsx
const Input: React.FC<Props & React.HTMLProps<HTMLInputElement>> = props => { ... }
<Input about={...} accept={...} alt={...} ... />
```

### `React.ReactEventHandler<HTMLXXXElement>`

表示 event handler 的泛型类型 - 用于声明 event handlers

```tsx
const handleChange: React.ReactEventHandler<HTMLInputElement> = (ev) => { ... } 
<input onChange={handleChange} ... />
```

### `React.XXXEvent<HTMLXXXElement>`

表示更多特殊 event。

一些常见的 event 例如：

`ChangeEvent, FormEvent, FocusEvent, KeyboardEvent, MouseEvent, DragEvent, PointerEvent, WheelEvent, TouchEvent`。

```tsx
const handleChange = (ev: React.MouseEvent<HTMLDivElement>) => { ... }
<div onMouseMove={handleChange} ... />
```

上一段代码中的 `React.MouseEvent<HTMLDivElement>` 表示鼠标事件的类型，这个事件挂载在 `HTMLDivElement` 上。

## Function Components - FC

### Counter Component

定义

```tsx
import * as React from 'react';

type Props = {
  label: string;
  count: number;
  onIncrement: () => void;
};

export const FCCounter: React.FC<Props> = props => {
  const { label, count, onIncrement } = props;

  const handleIncrement = () => {
    onIncrement();
  };

  return (
    <div>
      <span>
        {label}: {count}
      </span>
      <button type="button" onClick={handleIncrement}>
        {`Increment`}
      </button>
    </div>
  );
};
```

使用

```tsx
import * as React from 'react';

import { FCCounter } from '.';

export default class extends React.Component<{}, { count: number }> {
  state = { count: 0 };

  render() {
    return (
      <FCCounter
        label={'FCCounter'}
        count={this.state.count}
        onIncrement={() => {
          this.setState({ count: this.state.count + 1 });
        }}
      />
    );
  }
}
```

### Counter Component with default props

定义

```tsx
import * as React from 'react';

type Props = {
  label: string;
  count: number;
  onIncrement: () => void;
};

// React.FC is unaplicable here due not working properly with default props
// React.FC 在这里不适用，因为有 default props 会无效
// https://github.com/facebook/create-react-app/pull/8177
export const FCCounterWithDefaultProps = (props: Props): JSX.Element => {
  const { label, count, onIncrement } = props;

  const handleIncrement = () => {
    onIncrement();
  };

  return (
    <div>
      <span>
        {label}: {count}
      </span>
      <button type="button" onClick={handleIncrement}>
        {`Increment`}
      </button>
    </div>
  );
};

FCCounterWithDefaultProps.defaultProps = { count: 5 };

```

使用

```tsx
import { action } from '@storybook/addon-actions';
import * as React from 'react';

import { FCCounterWithDefaultProps } from '.';

export default () => (
  <FCCounterWithDefaultProps
    label={'FCCounterWithDefaultProps'}
    onIncrement={action('onIncrement')}
  />
);

```

### Spreading attributes in Component

定义

```tsx
import * as React from 'react';

type Props = React.PropsWithChildren<{
  className?: string;
  style?: React.CSSProperties;
}>;

export const FCSpreadAttributes: React.FC<Props> = (props) => {
  const { children, ...restProps } = props;

  return <div {...restProps}>{children}</div>;
};

```

使用

```tsx
import * as React from 'react';

import { FCSpreadAttributes } from '.';

export default () => (
  <FCSpreadAttributes
    className={'classy'}
    style={{ backgroundColor: 'lightcyan' }}
  >
    {`I'll spread every property you give me!`}
  </FCSpreadAttributes>
);

```

## Class Components

### Class Counter Component

定义

```tsx
import * as React from 'react';

type Props = {
  label: string;
};

type State = {
  count: number;
};

export class ClassCounter extends React.Component<Props, State> {
  readonly state: State = {
    count: 0,
  };

  handleIncrement = () => {
    this.setState({ count: this.state.count + 1 });
  };

  render() {
    const { handleIncrement } = this;
    const { label } = this.props;
    const { count } = this.state;

    return (
      <div>
        <span>
          {label}: {count}
        </span>
        <button type="button" onClick={handleIncrement}>
          {`Increment`}
        </button>
      </div>
    );
  }
}

```

使用

```tsx
import * as React from 'react';

import { ClassCounter } from '.';

export default () => <ClassCounter label={'ClassCounter'} />;

```

### Class Component with default props

定义

```tsx
import * as React from 'react';

type Props = {
  label: string;
  initialCount: number;
};

type State = {
  count: number;
};

export class ClassCounterWithDefaultProps extends React.Component<
  Props,
  State
> {
  static defaultProps = {
    initialCount: 0,
  };

  readonly state: State = {
    count: this.props.initialCount,
  };

  handleIncrement = () => {
    this.setState({ count: this.state.count + 1 });
  };

  render() {
    const { handleIncrement } = this;
    const { label } = this.props;
    const { count } = this.state;

    return (
      <div>
        <span>
          {label}: {count}
        </span>
        <button type="button" onClick={handleIncrement}>
          {`Increment`}
        </button>
      </div>
    );
  }
}

```

使用

```tsx
import * as React from 'react';

import { ClassCounter } from '.';

export default () => <ClassCounter label={'ClassCounter'} />;

```

## Generic Components

定义

```tsx
import * as React from 'react';

export interface GenericListProps<T> {
  items: T[];
  itemRenderer: (item: T) => JSX.Element;
}

export class GenericList<T> extends React.Component<GenericListProps<T>, {}> {
  render() {
    const { items, itemRenderer } = this.props;

    return (
      <div>
        {items.map(itemRenderer)}
      </div>
    );
  }
}

```

使用

```tsx
import * as React from 'react';

import { IUser, User } from '../models';
import { GenericList } from '../components';

const users = [
  new User('Rosamonte', 'Especial'),
  new User('Aguantadora', 'Despalada'),
  new User('Taragui', 'Vitality'),
];

export class UserList extends GenericList<IUser> {}

export default () => (
  <UserList
    items={users}
    itemRenderer={item => <div key={item.id}>{item.fullName}</div>}
  />
);

```

## Context

### ThemeContext

```tsx
import * as React from 'react';

export type Theme = React.CSSProperties;

type Themes = {
  dark: Theme;
  light: Theme;
};

export const themes: Themes = {
  dark: {
    color: 'black',
    backgroundColor: 'white',
  },
  light: {
    color: 'white',
    backgroundColor: 'black',
  },
};

export type ThemeContextProps = { theme: Theme; toggleTheme?: () => void };
const ThemeContext = React.createContext<ThemeContextProps>({ theme: themes.light });

export default ThemeContext;

```

### ThemeProvider

```tsx
import React from 'react';
import ThemeContext, { themes, Theme } from './theme-context';
import ToggleThemeButton from './theme-consumer';

interface State {
  theme: Theme;
}
export class ThemeProvider extends React.Component<{}, State> {
  readonly state: State = { theme: themes.light };

  toggleTheme = () => {
    this.setState(state => ({
      theme: state.theme === themes.light ? themes.dark : themes.light,
    }));
  }

  render() {
    const { theme } = this.state;
    const { toggleTheme } = this;
    return (
      <ThemeContext.Provider value={{ theme, toggleTheme }}>
        <ToggleThemeButton />
      </ThemeContext.Provider>
    );
  }
}
```

### ThemeConsumer

```tsx
import * as React from 'react';
import ThemeContext from './theme-context';

type Props = {};

export default function ToggleThemeButton(props: Props) {
  return (
    <ThemeContext.Consumer>
      {({ theme, toggleTheme }) => <button style={theme} onClick={toggleTheme} {...props} />}
    </ThemeContext.Consumer>
  );
}
```

### ThemeConsumer in class component

```tsx
import * as React from 'react';
import ThemeContext from './theme-context';

type Props = {};

export class ToggleThemeButtonClass extends React.Component<Props> {
  static contextType = ThemeContext;
  declare context: React.ContextType<typeof ThemeContext>;

  render() {
    const { theme, toggleTheme } = this.context;
    return (
      <button style={theme} onClick={toggleTheme}>
        Toggle Theme
      </button>
    );
  }
}
```

## Hooks

### useState

```tsx
import * as React from 'react';

type Props = { initialCount: number };

export default function Counter({initialCount}: Props) {
  const [count, setCount] = React.useState(initialCount);
  return (
    <>
      Count: {count}
      <button onClick={() => setCount(initialCount)}>Reset</button>
      <button onClick={() => setCount(prevCount => prevCount + 1)}>+</button>
      <button onClick={() => setCount(prevCount => prevCount - 1)}>-</button>
    </>
  );
}

```

### useContext

```tsx
import * as React from 'react';
import ThemeContext from '../context/theme-context';

type Props = {};

export default function ThemeToggleButton(props: Props) {
  const { theme, toggleTheme } = React.useContext(ThemeContext);
  return (
    <button onClick={toggleTheme} style={theme} >
      Toggle Theme
    </button>
  );
}

```

### useReducer

```tsx
import * as React from 'react';

interface State {
  count: number;
}

type Action = { type: 'reset' } | { type: 'increment' } | { type: 'decrement' };

function reducer(state: State, action: Action): State {
  switch (action.type) {
    case 'increment':
      return { count: state.count + 1 };
    case 'decrement':
      return { count: state.count - 1 };
    case 'reset':
      return { count: 0 };
    default:
      throw new Error();
  }
}

interface CounterProps {
  initialCount: number;
}

function Counter({ initialCount }: CounterProps) {
  const [state, dispatch] = React.useReducer(reducer, {
    count: initialCount,
  });

  return (
    <>
      Count: {state.count}
      <button onClick={() => dispatch({ type: 'reset' })}>Reset</button>
      <button onClick={() => dispatch({ type: 'increment' })}>+</button>
      <button onClick={() => dispatch({ type: 'decrement' })}>-</button>
    </>
  );
}

export default Counter;

```

## Render Props

### Name Provider Component

定义

```tsx
import * as React from 'react';

interface NameProviderProps {
  children: (state: NameProviderState) => React.ReactNode;
}

interface NameProviderState {
  readonly name: string;
}

export class NameProvider extends React.Component<NameProviderProps, NameProviderState> {
  readonly state: NameProviderState = { name: 'Piotr' };

  render() {
    return this.props.children(this.state);
  }
}
```

使用

```tsx
import * as React from 'react';

import { NameProvider } from './name-provider';

export default () => (
  <NameProvider>
    {({ name }) => (
      <div>{name}</div>
    )}
  </NameProvider>
);

```

### Mouse Provider Component

定义

```tsx
import * as React from 'react';

export interface MouseProviderProps {
  render: (state: MouseProviderState) => React.ReactNode;
}

interface MouseProviderState {
  readonly x: number;
  readonly y: number;
}

export class MouseProvider extends React.Component<MouseProviderProps, MouseProviderState> {
  readonly state: MouseProviderState = { x: 0, y: 0 };

  handleMouseMove = (event: React.MouseEvent<HTMLDivElement>) => {
    this.setState({
      x: event.clientX,
      y: event.clientY,
    });
  };

  render() {
    return (
      <div style={{ height: '100%' }} onMouseMove={this.handleMouseMove}>
        {/*
          Instead of providing a static representation of what <Mouse> renders,
          use the `render` prop to dynamically determine what to render.
        */}
        {this.props.render(this.state)}
      </div>
    );
  }
}

```

使用

```tsx
import * as React from 'react';

import { MouseProvider } from './mouse-provider';

export default () => (
  <MouseProvider
    render={mouse => (
      <p>The mouse position is {mouse.x}, {mouse.y}</p>
    )}
  />
);

```

## Higher-Order Components

### HOC wrapping a component

定义

```tsx
import React from 'react';
import { Diff } from 'utility-types';

// These props will be injected into the base component
interface InjectedProps {
  count: number;
  onIncrement: () => void;
}

export const withState = <BaseProps extends InjectedProps>(
  BaseComponent: React.ComponentType<BaseProps>
) => {
  type HocProps = Diff<BaseProps, InjectedProps> & {
    // here you can extend hoc with new props
    initialCount?: number;
  };
  type HocState = {
    readonly count: number;
  };

  return class Hoc extends React.Component<HocProps, HocState> {
    // Enhance component name for debugging and React-Dev-Tools
    static displayName = `withState(${BaseComponent.name})`;
    // reference to original wrapped component
    static readonly WrappedComponent = BaseComponent;

    readonly state: HocState = {
      count: Number(this.props.initialCount) || 0,
    };

    handleIncrement = () => {
      this.setState({ count: this.state.count + 1 });
    };

    render() {
      const { ...restProps } = this.props;
      const { count } = this.state;

      return (
        <BaseComponent
        {...(restProps as BaseProps)}
          count={count} // injected
          onIncrement={this.handleIncrement} // injected
        />
      );
    }
  };
};

```

使用

```tsx
import * as React from 'react';

import { withState } from '../hoc';
import { FCCounter } from '../components';

const FCCounterWithState = withState(FCCounter);

export default () => <FCCounterWithState label={'FCCounterWithState'} />;

```

###  HOC wrapping a component and injecting props

定义

```tsx
import React from 'react';

const MISSING_ERROR = 'Error was swallowed during propagation.';

export const withErrorBoundary = <BaseProps extends {}>(
  BaseComponent: React.ComponentType<BaseProps>
) => {
  type HocProps = React.PropsWithChildren<{
    // here you can extend hoc with new props
  }>;
  type HocState = {
    readonly error: Error | null | undefined;
  };

  return class Hoc extends React.Component<HocProps, HocState> {
    // Enhance component name for debugging and React-Dev-Tools
    static displayName = `withErrorBoundary(${BaseComponent.name})`;
    // reference to original wrapped component
    static readonly WrappedComponent = BaseComponent;

    readonly state: HocState = {
      error: undefined,
    };

    componentDidCatch(error: Error | null, info: object) {
      this.setState({ error: error || new Error(MISSING_ERROR) });
      this.logErrorToCloud(error, info);
    }

    logErrorToCloud = (error: Error | null, info: object) => {
      // TODO: send error report to service provider
    };

    render() {
      const { children, ...restProps } = this.props;
      const { error } = this.state;

      if (error) {
        return <BaseComponent {...(restProps as BaseProps)} />;
      }

      return children;
    }
  };
};

```

使用

```tsx
import * as React from 'react';

import { withConnectedCount } from '../hoc';
import { FCCounter } from '../components';

const FCCounterWithConnectedCount = withConnectedCount(FCCounter);

export default () => (
  <FCCounterWithConnectedCount overrideCount={5} label={'FCCounterWithState'} />
);

```

## 自己的总结

函数/方法/组件如果有参数，参数必须加类型，返回值类型可加可不加

复杂的数据类型可以对接口进行接口赋类型

```tsx
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
```



```tsx
// 1
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
    
// 2
interface Users {
  id: number;
  name: string;
  token: string;
}
    
export const handleUserResponse = ({user} : {user: Users}) => {   
```



```tsx
import { Users } from "./search-panel";

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
```



```tsx
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

// props.children 解构为 {children}
export const AuthProvider = ({children}: { children: ReactNode }) => {
  const [user, setUser] = useState<Users | null>(null);
  
  // setUser 函数式编程 point free
  const register = (form: AuthForm) => auth.register(form).then(setUser);
  const login = (form: AuthForm) => auth.login(form).then(setUser);
  const logout = () => auth.logout().then(() => setUser(null));

  return <AuthContext.Provider children={children} value={{user, register, login, logout}}/>
}
```



























































































































































































