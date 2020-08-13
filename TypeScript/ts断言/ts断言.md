## ts断言
* 有时候你会遇到这样子的情况，你会比ts更加熟悉某个值的详细信息。通常会发生在你清楚一个实体具有比它现有类型更确切的类型
* 类型断言好比其他语言里的类型转换，但是不进行特殊的数据检查和解构，他没有运行时的影响，只是在编译阶段起作用
* 类型断言的两张形式

### '尖括号'语法
```ts
let someValue: any = 'this is a string'
let strLength: number = (<string>someValue).length
```

### as语法
```ts
let someValue: any = "this is a string"
let strLength: number = (someValue as string).length
```