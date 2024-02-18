---
layout:       post
title:        "Typescript装饰器"
author:       "Hmt"
header-style: text
catalog:      true
tags:
    - Typescript
---

简单学习下Typescript装饰器语法，便于学习依赖注入实现。[官方文档介绍](https://www.typescriptlang.org/docs/handbook/decorators.html)

优点
------
1. 提高代码可读性 清晰明了的装饰器命名相当于注释
2. 不修改原始代码扩展功能
3. 装饰器语法使得代码更具有复用性和组合性 装饰器本身就是一个函数


代码示例
------
```ts
// 装饰器位于ts编译阶段执行 

@classDecorator
export class Student {
    @propertyDecorator
    private name: string;

    constructor(@parameterDecorator name: string) {
        this.name = name
    }

    @methodDecorator
    print(@parameterDecorator name: string, @parameterDecorator age: number) {
        return `hello ${name} ${age}`
    }

    @configuration(false)
    get _name() {
        return this.name
    }
}

// 类装饰器
function classDecorator<T extends { new (...args: any[]): {}}>(TargetConstructor: T) {
    /**
     * TargetConstructor: 类构造器
     */
    console.log('classDecorator', TargetConstructor)
    class BetterStudent extends TargetConstructor {
        private tag = 'better'
    }
    
    return BetterStudent
}

// 方法装饰器
function methodDecorator(target: any, propertyKey: string, descriptor: PropertyDescriptor) {
    /**
     * target: 类原型对象
     * propertyKey: 方法名
     * descriptor: 方法描述
     */
    console.log('methodDecorator', target === Student.prototype, propertyKey, descriptor) // true print {...descriptor}
}

// 属性装饰器
function propertyDecorator(target: any, propertyKey: string) {
    /**
     * target: 类原型对象
     * propertyKey: 属性名
     */
    console.log('propertyDecorator', target === Student.prototype, propertyKey) // true name
}

// 参数装饰器
function parameterDecorator(target: any, propertyKey?: string, parameterIndex?: number) {
    /**
     * target: 类原型对象
     * propertyKey: 方法名
     * parameterIndex: 参数索引
     * 构造函数使用此修饰器 propertyKey是undefined
     */
    console.log('parameterDecorator', target === Student.prototype, propertyKey, parameterIndex) 
    // true print 1
    // true print 0
}

// 访问器装饰器
function configuration(value: boolean) {
    return function (target: any, propertyKey: string, descriptor: PropertyDescriptor) {
        console.log('accessorDecorator', target === Student.prototype, propertyKey, descriptor) // true _name {...descriptor}
        descriptor.configurable = value
    }
}

```