---
layout:       post
title:        "Typescript类型操作总结"
author:       "Hmt"
header-style: text
catalog:      true
tags:
    - Typescript
---

归纳Typescript工具函数以及常见类型操作

## 工具函数(Utility Types)

> [官方文档](https://www.typescriptlang.org/docs/handbook/utility-types.html#awaitedtype)

### Partial
```ts
// 对象类型全部属性标注为可选属性

// 基础使用
interface PartialTestObj {
    name: string
    age: number
}

const partialTestObj: Partial<PartialTestObj> = {
    name: 'tom'
}
// 实现
type Partial<T> = { [P in keyof T]?: T[P] | undefined; }
```

### Required
```ts
// 给对象类型的全部属性标注为必选

// 基础使用
interface RequiredTestObj {
    name?: string
    age: number
}

const requiredTestObj: Required<RequiredTestObj> = {
    name: 'jack',
    age: 18
}
// 实现
type Required<T> = { [P in keyof T]-?: T[P]; }
```

### Readonly
```ts
// 给对象类型的全部属性标注为必选

// 基础使用
interface ReadonlyTestObj {
    name: string
}

const readonlyTestObj: Readonly<ReadonlyTestObj> = {
    name: 'readonly'
}

readonlyTestObj.name = 'jack' // 无法为“name”赋值，因为它是只读属性

// 实现
type Readonly<T> = { readonly [P in keyof T]: T[P]; }
```


### Record
```ts
// 快速生成一个对象类型

// 基础使用
type Position = Record<'x' | 'y', number> // { x: number, y: number }
// 实现
type Record<K extends string | number | symbol, T> = { [P in K]: T; }
```

### Pick
```ts
// Pick<Type, Keys> 根据已有对象Type以及传入的Keys 创建 新对象类型

// 基础使用
type PickTestType = {
    name: string
    age: number
    sex: 'male' | 'female'
}

type PickType = Pick<PickTestType, 'name' | 'age'> // { name: string, age: number }
// 实现
type Pick<T, K extends keyof T> = { [P in K]: T[P]; }
```

### Exclude
```ts
// 从联合类型中排除指定类型

// 基础使用
type ExcludeTestType = 'a' | 'b' | 'c' | 'd'
type ExcludeTestType2 = Exclude<ExcludeTestType, 'a' | 'c'> // "b" | "d"
// 实现
type Exclude<T, U> = T extends U ? never : T
```

### Omit
```ts
// Omit<Type, Keys> 根据Type创建一个新的类型 排除keys包含属性

// 基础使用
type OmitTestType = {
    name: string
    age: number
    sex: 'male' | 'female'
}

type OmitTestType2 = Omit<OmitTestType, 'sex'> // { name: string; age: number}

// 实现
type Omit<T, K extends string | number | symbol> = { [P in Exclude<keyof T, K>]: T[P]; }
```

### Extract
```ts
// Extract<Type, Union> 和Exclude相反 根据Union查找属性

// 基础使用
type ExtractTestType = 'a' | 'b' | 'c' | 'd'
type ExtractTestType2 = Extract<ExtractTestType, 'a' | 'c'> // "a" | "c"
// 实现
type Extract<T, U> = T extends U ? T : never
```

### NonNullable
```ts
// NonNullable<Type> 接受一个联合类型 排除null undefined

// 基础使用
type NonNullableTestType = NonNullable<string | undefined | boolean | null> // string | boolean
// 实现
null & {} // never
undefined & {} // never
type MyNonNullable<T> = T & {}
```

### Parameters
```ts
// 基础使用
type ParametersFn1 = Parameters<() => string> // []
type ParametersFn2 = Parameters<(name: string, age: number) => number> // [name: string, age: number]
type ParametersFn2Tuple = Parameters[number] // string | number
type ParametersFn3 = Parameters<<T>(arg: T) => T> // [arg: unknown]
type ParametersFn4 = Parameters<any> // unknown[]
// 实现
type Parameters<T extends (...args: any) => any> = T extends (...args: infer P) => any ? P : never
```

### ReturnType

```ts
// 基础使用
type ReturnTypeFn1 = MyReturnType<() => string> // string
type ReturnTypeFn2 = MyReturnType<() => Promise<string>> // Promise<string>
type ReturnTypeFn3 = MyReturnType<() => any> // any
type ReturnTypeFn4 = MyReturnType<() => never> // never

// 实现
type ReturnType<T> = T extends (...args: any) => infer P ? P : any
```

### Awaited
```ts
// 获取Promise的类型

// 基础使用
type PromiseType1 = Awaited<Promise<string>> // string
type PromiseType2 = Awaited<Promise<Promise<number>>> // number 
type PromiseType3 = Awaited<Promise<boolean | string | Promise<number>>> // string | number | boolean
```

### 字符串相关函数
```ts
// 基础使用
type StringUtilType1 = Uppercase<'hello world'> // "HELLO WORLD"
type StringUtilType2 = Lowercase<'HeLlo World'> // "hello world"
type StringUtilType3 = Capitalize<'hello world'> // "Hello world"
type StringUtilType4 = Uncapitalize<'HELlo World'> // "hELlo World"
```

## extends
> [官方文档](https://www.typescriptlang.org/docs/handbook/2/conditional-types.html)

extends作为条件类型(Conditional Types)的表现
```ts
type T103001 = true extends boolean ? 1 : 2 // 1
type T103002 = false extends boolean ? 1 : 2 // 1
type T103003 = string extends boolean ? 1 : 2 // 2
// null undefinde never 满足any
type T103004 = null extends any ? 1 : 2 // 1
type T103005 = undefined extends any ? 1 : 2 // 1
type T103006 = never extends any ? 1 : 2 // 1
type T103101 = never extends never ? 1 : 2 // 1
type T103007 = (<T>() => T) extends any ? 1 : 2 // 1

type T1030A = {
    name: string
}
type T1030B = {
    name: string
    age: number
}

// A extends B  B上面的属性A都满足吗
type T103008 = T1030A extends T1030B ? 1 : 2 // 2
type T103009 = T1030B extends T1030A ? 1 : 2 // 1
type T1030010 = ((...args: any) => any) extends Function ? 1 : 2 // 1
type T1030011 = Function extends (() => any)   ? 1 : 2 // 2

type T103102 = never extends any[] ? 1 : 2 // 1

// 判断类型是否是never
type IsNever<T> = [T] extends [never] ? 1 : 2
type T103103 = IsNever<never> // 1
```
## 常见类型操作