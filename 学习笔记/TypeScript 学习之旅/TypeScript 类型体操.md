

Partial 就是将接口类型中的所有键变成可选，实现如下：

```ts
type MyPartial<T> = {
  [K in keyof T]?: T[K]
};
```

Required 和上面的 Partial 刚好相反，Partial 类型是将接口中的键都变成可选类型，而 Required 则将所有的键变成必选，也就是去掉了`?`的标记。

```ts
// 这里使用了上面提到的修饰符-，取消可选
type MyRequired<T> = {
  [K in keyof T]-?: T[K]
}
```

Readonly 顾名思义就是将接口类型中所有的键变成只读类型，同理，

```ts
type MyReadonly<T> = {
  readonly [K in keyof T]: T[K]
};
```

Pick<T, U> 从 T 中选择 U 指定的键值对组成一个新类型

```ts
type MyPick<T, U extends keyof T> = {
  [K in U]: T[K]
};
```

Exclude<T, U> 就是将 U 类型从 T 中排除，只有联合类型才能作为 T 类型使用。

可以理解为 Pick 类型的取反，但是类型参数是不一样的。

```ts
interface Person {
  name: string;
  age: number;
  height: number;
}

type MyExclude<T, U> = T extends U ? never : T;

// 直接这么去除是无效的
type Test = MyExclude<Person, 'name'>;

// Pick 类型则是可以的
type Test1 = MyPick<Person, 'name'>; // { name: string }

// Exclude 可以把T类型变成联合类型
type Test2 = MyExclude<keyof Person, 'name'>; // 得到的结果也是联合类型：'age' | 'height'，并不是我们想要的排除的效果

// 可以遍历所有的键，如果键继承了给定的 U 类型，则排除掉，这样就留下了剩下的键
type IExclude<T, U> = {
  [K in keyof T as K extends U ? never : K]: T[K]
}

type Test3 = IExclude<Person, 'name'>; // { age: number; height: number }
```

Omit<T, U> 从 T 中删除 U 类型，可以理解为从接口中删除指定的键

```ts
interface Todo {
  title: string
  description: string
  completed: boolean
}

type MyOmit<T, U> = {
  [K in keyof T as K extends U ? never : K]: T[K]
}

type Test = MyOmit<Todo, 'description' | 'title'>; // { title: string }

// 结合上面的内置类型，可以发现排除某个键，可以使用 Exclude 来实现
type MyOmit1<T, U> = Pick<T, Exclude<keyof T, 'description' | 'title'>>

// 这里要注意 Exclude 接受的是一个联合类型，因此需要对 T 进行转换
```

type-utils

```ts
export type Expect<T extends true> = T;
export type ExpectTrue<T extends true> = T;
export type ExpectFalse<T extends false> = T;
export type IsTrue<T extends true> = T;
export type IsFalse<T extends false> = T;

export type Equal<X, Y> = (<T>() => T extends X ? 1 : 2) extends <
  T,
>() => T extends Y ? 1 : 2
  ? true
  : false;
export type NotEqual<X, Y> = true extends Equal<X, Y> ? false : true;

// https://stackoverflow.com/questions/49927523/disallow-call-with-any/49928360#49928360
export type IsAny<T> = 0 extends 1 & T ? true : false;
export type NotAny<T> = true extends IsAny<T> ? false : true;

export type Debug<T> = { [K in keyof T]: T[K] };
export type MergeInsertions<T> = T extends object
  ? { [K in keyof T]: MergeInsertions<T[K]> }
  : T;

export type Alike<X, Y> = Equal<MergeInsertions<X>, MergeInsertions<Y>>;

export type ExpectExtends<VALUE, EXPECTED> = EXPECTED extends VALUE
  ? true
  : false;
export type ExpectValidArgs<
  FUNC extends (...args: any[]) => any,
  ARGS extends any[],
> = ARGS extends Parameters<FUNC> ? true : false;

export type UnionToIntersection<U> = (
  U extends any ? (k: U) => void : never
) extends (k: infer I) => void
  ? I
  : never;
```

## 1、实现 Pick

实现 TS 内置的 `Pick<T, K>`，但不可以使用它。

**从类型 `T` 中选择出属性 `K`，构造成一个新的类型**。

例如：

```ts
interface Todo {
  title: string
  description: string
  completed: boolean
}

type TodoPreview = MyPick<Todo, 'title' | 'completed'>

const todo: TodoPreview = {
    title: 'Clean room',
    completed: false,
}
```

解答：

```ts
type MyPick<T, K extends keyof T> = {
  [key in K]: T[key]
}
```

keyof: 取 interface 的键后保存为联合类型，即将接口类型转换为联合类型

```ts
interface userInfo {
  name: string
  age: number
}
type keyofValue = keyof userInfo
// keyofValue = "name" | "age"
```

in: 取联合类型的值，主要用于数组和对象的构建

切记不要用于interface, 否则会报错

```ts
type name = 'firstname' | 'lastname'
type TName = {
  [key in name]: string
}
// TName = { firstname: string, lastname: string }
```

用于实际开发，举个例子：

```ts
function getValue(o:object, key: string){
  return o[key]
}
const obj1 = { name: '张三', age: 18 }
const values = getValue(obj1, 'name')
```

这样写丧失了 ts 的优势：

1. 无法确定返回值类型
2. 无法对 key 进行约束

```ts
function getValue<T extends Object,K extends keyof T>(o: T,key: K): T[K] {
  return o[key]
}
const obj1 = { name: '张三'， age: 18}
const values = getValue(obj1, 'name')
// 如果第二个参数不是obj1中的参数就会报错
```

## 2、实现 Readonly

不使用内置的`Readonly<T>`，自己实现一个。

该 `Readonly` 会接收一个 *泛型参数*，并返回一个完全一样的类型，只是所有属性都会被 `readonly` 所修饰。

也就是不可以再对该对象的属性赋值。

例如：

```ts
interface Todo {
  title: string
  description: string
}

const todo: MyReadonly<Todo> = {
  title: "Hey",
  description: "foobar"
}

todo.title = "Hello" // Error: cannot reassign a readonly property
todo.description = "barFoo" // Error: cannot reassign a readonly property
```

解答：

```ts
type MyReadonly<T> = {
  readonly [P in keyof T]: T[P];
};
```

## 3、元组转换为对象

传入一个元组类型，将这个元组类型转换为对象类型，这个对象类型的键/值都是从元组中遍历出来。

例如：

```ts
const tuple = ['tesla', 'model 3', 'model X', 'model Y'] as const

type result = TupleToObject<typeof tuple> // expected { tesla: 'tesla', 'model 3': 'model 3', 'model X': 'model X', 'model Y': 'model Y'}
```

解答：

如何遍历元组中的每一项？

`T[number]`将元组类型转化为联合类型

可以通过 number 索引，也就是 T[number] 获取到每一项。

```ts
type TupleToObject<T extends readonly (string | symbol | number)[]> = {
  [P in T[number]]: P
}
```

也可以直接用`ts`内置的类型`PropertyKey`, 类型定义是`declare type PropertyKey = string | number | symbol;`

```ts
type TupleToObject<T extends readonly PropertyKey[]> = {
  [K in T[number]]: K
}
```

keyof any，意思就是提取一个 any 类型的对象键，但是对象键只可能是 string number symbol，

所以即使对于 any，也就是任何类型进行 keyof 提取对象键，只可能出现这三种类型

```ts
type TupleToObject<T extends readonly (keyof any)[]> = {
  [key in T[number]]: key;
};
```

```ts
type TupleToObject<T extends ReadonlyArray<PropertyKey>> = {
  [key in T[number]]: key;
};
```

```ts
// 元组类型转为联合类型
type TupleToUnion<T extends unknown[]> = T[number];

// 元组转为对象类型
type TupleToObject<T extends any[]> = {
  [P in T[number]]: P
};
```

## 4、第一个元素

实现一个通用`First<T>`，它接受一个数组`T`并返回它的第一个元素的类型。

例如：

```ts
type arr1 = ['a', 'b', 'c']
type arr2 = [3, 2, 1]

type head1 = First<arr1> // expected to be 'a'
type head2 = First<arr2> // expected to be 3
```

解答：

T["length"]是为了获取元组的长度的，可以理解成 js 数组的 length 

T[number] 是为了遍历元组中的每一项，需要与 in 结合，比如 [P in T[number]]

```ts
type First<T extends any[]> = T extends [] ? never : T[0]

type First<T extends any[]> = T['length'] extends 0 ? never : T[0]

type First<T extends any[]> = T extends [infer P, ...infer Rest] ? P : never

type First<T extends any[]> = T[number] extends never ? never : T[0]
```

## 5、获取元组长度

创建一个通用的`Length`，接受一个`readonly`的数组，返回这个数组的长度。

例如：

```ts
type tesla = ['tesla', 'model 3', 'model X', 'model Y']
type spaceX = ['FALCON 9', 'FALCON HEAVY', 'DRAGON', 'STARSHIP', 'HUMAN SPACEFLIGHT']

type teslaLength = Length<tesla> // expected 4
type spaceXLength = Length<spaceX> // expected 5
```

解答：

```ts
type Length<T extends readonly any[]> = T extends { length: infer L }  ?  L : never;
```

## 6、实现 Exclude

实现内置的 Exclude <T, U> 类型，但不能直接使用它本身。

> 从联合类型 T 中排除 U 的类型成员，来构造一个新的类型。

例如：

```ts
type Result = MyExclude<'a' | 'b' | 'c', 'a'> // 'b' | 'c'
```

解答：

```ts
type MyExclude<T, U> = T extends U ? never : T;
/**
 * 泛型遇到联合类型和 extends 的时候，会拆解开一一进行判断，如下：
 *
 * MyExclude<'a' | 'b' | 'c', 'a'>
 *  'a' extends 'a' ? never : 'a' |
 *  'b' extends 'a' ? never : 'b' |
 *  'c' extends 'a' ? never : 'c'
 *  => 'b' | 'c'
 *
 * ------
 *
 * MyExclude<'a' | 'b' | 'c', 'a' | 'b'>
 *  'a' extends  'a' | 'b' ? never : 'a' |
 *  'b' extends  'a' | 'b' ? never : 'b' |
 *  'c' extends  'a' | 'b' ? never : 'c' 
 *  => 'c'
 */
```

## 7、Awaited 

假如我们有一个 Promise 对象，这个 Promise 对象会返回一个类型。

在 TS 中，我们用 Promise 中的 T 来描述这个 Promise 返回的类型。请你实现一个类型，可以获取这个类型。

例如：`Promise<ExampleType>`，请你返回 ExampleType 类型。

```ts
type ExampleType = Promise<string>

type Result = MyAwaited<ExampleType> // string
```

解答：

```ts
type MyAwaited<T extends Promise<unknown>> = T extends Promise<infer U> ? (U extends Promise<unknown> ? MyAwaited<U> : U) : never 
```

先用条件推断，泛型 T 是否是 Promise 返回，并用 infer U 指代返回值。

U 有两种情况：

1、普通返回值类型
2、Promise 类型

如果 U 是 Promise 类型，则需要递归检查。对应的代码是：

```ts
U extends Promise<any> ? MyAwaited<U> : U 
```

如果是普通返回值类型，则直接返回 U 。

为什么要加 `extends Promise<any>` ?

`MyAwaited<T extends Promise<any>>` 的含义，是为了避免用户传入非Promise function。

如果用户违反规则，TypeScript会按报错处理。

## 8、If 

实现一个 `IF` 类型，它接收一个条件类型 `C` ，一个判断为真时的返回类型 `T` ，以及一个判断为假时的返回类型 `F`。

 `C` 只能是 `true` 或者 `false`， `T` 和 `F` 可以是任意类型。

例如：

```ts
type A = If<true, 'a', 'b'>  // expected to be 'a'
type B = If<false, 'a', 'b'> // expected to be 'b'
```

解答：

```ts
type If<C extends boolean, T, F> = C extends true ? T : F;

/**
 * 稍微拓展下，什么时候会触发联合类型的 distributive
 */
type T1<C extends boolean> = C extends C ? 1 : 2;

type Q1 = T1<true | false>; // 1
type Q2 = T1<true>; // 1
type Q3 = T1<false>; // 1
type Q4 = T1<boolean>; // 1
// ↑↑↑，上面这四个，可以认为是 distributive 后，所有的结果都是 1，然后 1 取联合类型，还是 1

type T2<C extends boolean> = C extends true ? 1 : 2;
type Q5 = T2<boolean>; // 1 | 2 这个是因为触发了 distributive ，在最后合并的过程中有两个结果，所以是联合类型
type Q6 = T2<true>; // 1 未触发
type Q7 = T2<false>; // 2 未触发

// 如果是明确的值，也会触发 distributive ，但是结果不会合并，只有都满足才会走到正确的表达式判断中：
type Q8 = boolean extends true ? 1 : 2; // 2
/**
 * ↑↑↑，带入计算伪代码：true extends true ? 1 : 2 && false extends true ? 1 : 2
 * 可以看到第二个 false 字面量类型 不是 true 字面量类型的子类，不成立
 * 所以这个表达式，会返回 2
 */
```

## 9、Concat

在类型系统里实现 JavaScript 内置的 `Array.concat` 方法，这个类型接受两个参数，返回的新数组类型应该按照输入参数从左到右的顺序合并为一个新的数组。

例如：

```ts
type Result = Concat<[1], [2]> // expected to be [1, 2]
```

解答：

```ts
type Concat<T extends unknown[], U extends unknown[]> = [...T, ...U]
```

## 10、Includes

在类型系统里实现 JavaScript 的 `Array.includes` 方法，这个类型接受两个参数，返回的类型要么是 `true` 要么是 `false`。

例如：

```ts
type isPillarMen = Includes<['Kars', 'Esidisi', 'Wamuu', 'Santana'], 'Dio'> // expected to be `false`
```



```ts
type IsEqual<T, U> =
	(<G>() => G extends T ? 1 : 2) extends
	(<G>() => G extends U ? 1 : 2)
		? true
		: false;

type Includes<Value extends any[], Item> =
	IsEqual<Value[0], Item> extends true
		? true
		: Value extends [Value[0], ...infer rest]
			? Includes<rest, Item>
			: false;
```

## 11、Push 

在类型系统里实现通用的 `Array.push` 。

例如：

```ts
type Result = Push<[1, 2], '3'> // [1, 2, '3']
```

解答：

```ts
type Push<T extends unknown[], U> = [...T, U]
```

## 12、Unshift 

实现类型版本的 `Array.unshift`。

例如：

```ts
type Result = Unshift<[1, 2], 0> // [0, 1, 2]
```

解答：

```ts
type Unshift<T extends unknown[], U> = [U, ...T]
```

## 13、Parameters

实现内置的 Parameters 类型，而不是直接使用它，可参考[TypeScript官方文档](https://www.typescriptlang.org/docs/handbook/utility-types.html#parameterstype)。

例如：

```ts
const foo = (arg1: string, arg2: number): void => {}

type FunctionParamsType = MyParameters<typeof foo> // [arg1: string, arg2: number]
```

解答：

```ts
type MyParameters<T extends (...args: any[]) => any> = T extends (...args: infer P) => unknown ? P : never;
```

使用 infer表示待推断的类型变量。

由于`...args` 本身已经是元组类型，因此 infer P 最终推导出的，也是元组类型。

























































