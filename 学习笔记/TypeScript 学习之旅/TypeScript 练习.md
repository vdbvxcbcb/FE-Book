## 泛型约束与返回值

```ts
type User = {
  id: number;
  kind: string;
};

// T extends User 表示约束泛型 T 符合 User 类型结构，
// 但不局限于 User 类型结构（属性可以多，但是不能少，T 可以兼容 User 类型）
// 所以返回值 T 可能不是 User 类型，报错
function makeCustomer<T extends User>(u: T): T {
  // Error（TS 编译器版本：v4.4.2）
  // Type '{ id: number; kind: string; }' is not assignable to type 'T'.
  // '{ id: number; kind: string; }' is assignable to the constraint of type 'T', 
  // but 'T' could be instantiated with a different subtype of constraint 'User'.
  return {
    id: u.id,
    kind: 'customer'
  }
  
}
// 函数接收这种参数时，就会有问题
makeCustomer({ id: 1, kind: 'foo', age: 24 })
```

假如： MyUser 的类型符合 User 类型的约束，但是还存在 age 类型，

makeCustomer 显然函数的返回值类型 T 因为少了个 age ，所以例子中的返回值

```
return {
  id: u.id,
  kind: 'customer'
}
```

并不满足 MyUser 类型

```
{
  id: number;
  kind: string;
  age: number
}  
```

解决办法是：让返回值类型满足泛型 T（即传入的 MyUser）

```ts
type User = {
  id: number;
  kind: string;
};

interface MyUser extends User {
  age: number
}

function makeCustomer<T extends User>(u: T): T {
  return {
    ...u, // 注意这行
    id: u.id,
    kind: 'customer'
  }
}

let obj:MyUser = {id:1, kind:'foo', age:24}
makeCustomer<MyUser>(obj)
```

或者让 TS 自动推导返回值类型

```ts
type User = {
  id: number;
  kind: string;
};

interface MyUser extends User {
  age: number
}

function makeCustomer<T extends User>(u: T) {
  return {
    id: u.id,
    kind: 'customer'
  }
}

let obj:MyUser = {id:1, kind:'foo', age:24}
makeCustomer<MyUser>(obj)
```

## SetOptional 工具类型

定义一个 SetOptional 工具类型，支持把给定的 keys 对应的属性变成可选的？

```ts
type Foo = {
	a: number;
	b?: string;
	c: boolean;
}

type Simplify<T> = {
  [P in keyof T]: T[P]
}

type SetOptional<T, K extends keyof T> = 
  Simplify<Partial<Pick<T, K>> & Pick<T, Exclude<keyof T, K>>>

// 测试用例
type SomeOptional = SetOptional<Foo, 'a' | 'b'>;
// type SomeOptional = {
// 	a?: number; // 该属性已变成可选的
// 	b?: string; // 保持不变
// 	c: boolean;
// }
```

## SetRequired 工具类型

```ts
type Foo = {
	a?: number;
	b: string;
	c?: boolean;
}

type Simplify<T> = {
  [P in keyof T]: T[P]
}

type SetRequired<T, K extends keyof T> = Simplify<Pick<T, Exclude<keyof T, K>> & Required<Pick<T, K>>>

// 测试用例
type SomeRequired = SetRequired<Foo, 'b' | 'c'>;
// type SomeRequired = {
// 	a?: number;
// 	b: string; // 保持不变
// 	c: boolean; // 该属性已变成必填
// }
```



```ts
type SetOptional<T, K extends keyof T> = Omit<T, K> & Partial<Pick<T, K>>;
type SetOptionalOmit<T, K extends keyof T> = Pick<T, K> & Partial<Omit<T, K>>;
type SetRequired<T, K extends keyof T> = Omit<T, K> & Required<Pick<T, K>>;
type SetRequiredOmit<T, K extends keyof T> = Pick<T, K> & Required<Omit<T, K>>;
```













