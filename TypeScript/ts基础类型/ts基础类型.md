## TS基础类型

### boolean类型
```ts
let isDone: boolean = false
```

### Number类型
```ts
let num: number = 1
```

### String类型
```ts
let str: string = "cjx"
```

### Array类型
```ts
let arr: array = [1,5,'jk']
```

### Enum类型
数字枚举,字符串枚举,异构枚举
```ts
//数字枚举 默认情况下初始值为0，递增1
enum Direction {
  north,
  south,
  east,
  west
}
//编译后生成的代码：
use strict
var Direction;
(function(Direction){
  Direction[Direction['north'] = 0] = 'north',
  Direction[Direction['south'] = 1] = 'south',
  Direction[Direction['east'] = 2] =  'east',
  Direction[direction['west'] = 3] = 'west'
})(Direction || (Direction = {}))

//可以设置初始值
enum Direction {
  north= 1,
  south,
  west,
  east
}

//字符串枚举
enum Str {
  north= 'NORTH',
  south= 'SOUTH',
  east= 'EAST',
  west= 'WEST'
}
//编译后的代码
(function Str {
  Str['north'] = 'NORTH',
  Str['south'] = 'SOUTH',
  Str['east'] = 'EAST',
  Str['west'] = 'WEST'
})(Str || (Str = {}))

//异构枚举
//异构枚举的成员值是数字和字符串的混合
enum Enum {
  A ,
  B ,
  C = "cc",
  D = "dd",
  E = 6
  F
}
//编译后的代码
(function Enum() {
  Enum[Enum["A"] = 0] = "A",
  Enum[Enum["B"] = 1] = "B",
  Enum["C"] = "cc",
  Enum["D"] = "dd",
  Enum[Enum["E"] = 6] = "E",
  Enum[Enum["F"] = 7] = "F"
})(Enum || (Enum = {}))
```

### Any类型
在ts中，任何类型都可以归为any类型，这让any类型成为类型系统的顶级类型（也称全局超级类型）
```ts
let noteSure: any = 666;
noteSure = 'hhh';
noteSure = false
```
ts允许我们对any类型的值执行任何操作，而不需先执行任何形式的检查
```ts
let value: any;
value.length;
value.foo.bar
value.trim
new value()
```
在很多场景中，使用any类型，可以很容易地编写类型正确但在运行时有问题的代码，如果我们使用any类型，就无法使用ts提供的大量保护机制，为了解决any带来的问题，ts3.0引入了unknown类型


### Unknown类型
就像所有类型都可以赋值给any，所有类型也都可以赋值给unknown，这使得unknown成为ts类型系统的另一种顶级类型，(另一种是any，下面我们来看一下unknown类型的使用示范)
```ts
let value: unknown

value = true
value = 42
value = "hello"
```
对value变量的所有赋值都被认为是类型正确的，当我们将类型为unknown的值赋值给其他类型的变量时，会报错
```ts
let value:unknown

let value:unknown = value  //ok
let value:any = value  //ok
let value:number = value //Error
let value:object = value //Error
let value:any[] = value //Error
```
unknown类型只能被赋值给any类型和unknown类型本身，这是有道理的，只能能够保存任意类型值的才能保存unknown类型的值，毕竟我们不知道value存储了什么类型的值

* 将value的值设置为unknown后，这些操作都不再被认为是类型正确，通过将any类型改为unknown类型，我们已将允许所有更改的默认设置，更改为禁止任何更改

### Tuple类型
数组一般由同种类型的值组成，但有时我们需要在单个变量中存储不同类型的值，这时候我们可以使用元组，在js是没有元组的，元组是ts特有的类型，其工作方式类似于数组
```ts
let tupleType = [string, boolean]
tupleType = ['cjx', false] 

//在元组初始化的时候，如果出现类型不匹配会报错
tupleType  = [false, 'cjx']

//在元组初始化的时候，我们还必须提供每个属性的值，不然也会报错
tupleType = ["cjx"]
```

### Void类型
在某种情况来说，void类型就像与any类型相反，他表示没有任何类型，当一个函数没有返回值时，通常会看到其返回值类型是void
```ts
// 声明函数返回值为void
function fn():void {
  console.log('213')
}

```
需要注意，声明一个void类型的变量没有什么作用，因为它的值只能为undef或null
```js
let unusable: void = undefined
```

### Null和Undefined类型
ts中，undefined和null两者有各自的类型分别为undefined和null
```ts
let u:undefined = undefined
let n:null = null
```
默认情况下null和undefined是所有类型的子类型，就是可以把null和undefined赋值给其他类型的变量。然而如果指定了--strictNullChecks标记，null和undefined只能赋值给void和他们各自的类型

### Never类型
never类型表示的是那些永不存在的值的类型，例如，never类型那些总是会抛出异常或根本就不会有返回值的函数表达式或者箭头函数表达式的返回值类型
```ts
//返回never的函数必须存在无法到达的终点
function error(message: string): never {
  throw new Error(message)
}

```    
在ts中，可以利用never类型的特性来实现全面性检查
```ts
type Foo = string || number

function fn (foo: Foo) {
  if(typeof foo === "string") {

  } else if (typeof foo === "number") {
    
  }else {
    const check: never = foo
  }
}
```   
注意在else分支里面，我们把收窄为never的foo赋值给一个显示声明的never变量，如果一切逻辑正常，是可以编译通过的，但是如果Foo的类型发生改变
```ts
type Foo = string || number || boolean

```
然而他忘记修改fn方法中的控制流程，这时候的else分支的foo类型被收窄为boolean类型，导致无法赋值给never类型，出现报错 ，通过这个方式，我们可以确保fn方法总是能穷尽Foo的所有可能类型。  
* 结论：使用never避免出现新增了联合类型没有对应的实现，目的就是写出类型绝对安全的代码

