## TypeScript 简单入门

```typescript
const a: string = 1;
```

上面这行代码与普通 JS 代码的区别是，在变量后面加了一个:和 string，这代表只能给变量 a 赋 string 类型的值。将一个 number 类型的值赋值给变量 a，所以报错：number 类型不可分配给 string 类型。
在 TS 中，这叫做类型注解，类型注解是一种为函数或者变量添加约束的方式。

### 1、基本数据类型

```typescript
const str: string = "hello";
const num: number = "666";
const bool: boolean = true;
const u: undefined = undefined;
const n: null = null;
const big: bigint = 100n;
const sym: symbol = Symbol("me");
const obj: object = { x: 1 };
```

**null 和 undefined**

默认情况下这 null 和 undefined 是所有类型的子类型，可以赋值给其他任何类型

```typescript
// null 和 undefined 赋值给 number
let num: number = 1;
num = null;
num = undefined;

// null 和 undefined 赋值给 boolean
let bool: boolean = false;
bool = null;
bool = undefined;

// null 和 undefined 赋值给 object
let obj: object = {};
obj = null;
obj = undefined;

// 如果在tsconfig.json里配置了"strictNullChecks": true，null就只能赋值给any、unknown和它本身的类型（null），undefined就只能赋值给any、unknown、void和它本身的类型（undefined）。
```

**number 和 bigint**

虽然这两个都表示为数字，但是这两个类型并不兼容

```typescript
let big: bigint = 100n;
let num: number = 1;
num = big; // Type 'bigint' is not assignable to type 'number'
```

**其他类型**

Array,数组定义有两种方式

```typescript
let arr: string[] = ["剑圣", "蛮王"];
let array: Array<string> = ["剑姬", "锐雯"];

// 这两种写法意味着，数组里面的值只能是string类型，否则会报错
arr.push(8); //报错
array = ["剑姬", "瑞文", 6]; // 这里也会报错

// 如果不仅想在数组中存储number类型的值，还想存储string类型的值，可以这样写:
let arr: (number | string)[] = [1, "1"];
```

**元组**

python 里面也有元组

```python
a=(1,2,3)
print(type(a))

b=tuple(1,2,3)
print(type(b))

c=1,2,3
print(type(c))
```

在 typescript 里面，元组的特点是可以限制元素的个数和类型

```typescript
// [string, number] 就是元组类型。数组 x 的类型必须严格匹配，且个数必须为2
let x: [string, number];

x = ["Hi", 666]; // OK
x = [666, "Hi"]; // error
x = ["Hi", 666, 888]; // error
```

元组只能表示一个已知元素数量和类型的数组，越界会报错，如果一个数组有多种数据类型，并且数量不确定，直接使用 any[]

**元组的解构赋值**
ss

```typescript
let arr: [string, number] = ["德彪", 888];
let [lol, action] = arr;
console.log(lol); // 德彪
console.log(action); //888

// 使用这种方式需要注意，解构数组元素的个数不能超过元组中的元素个数
// 元组类型[string,number]的长度是2，在位置索引2处没有任何元素
```

**元组类型的可选元素**

在定义元组类型时，也可以通过?来声明元组类型的可选元素

```typescript
// 要求包含一个必须的字符串属性，和一个可选的布尔值属性
let arr: [string, boolean?];

arr = ["一个能打的都没有", true];
console.log(arr); // ['一个能打的都没有', true]

arr = ["如果暴力不是为了杀戮"];
console.log(arr); // ['如果暴力不是为了杀戮']
```

**元组类型的剩余元素**

元组类型最后一个元素可以是剩余元素，形式为...x，可以把它当作 ES6 中的剩余参数
代表元组可以有 0 个或者多个额外的元素

```typescript
let arr: [number, ...string[]];
arr = [1, "赵信"];
arr = [1, "赵信", "李四", "张三"];
```

只读的元组类型
可以为任何元组类型加上 readonly 关键字前缀，使其成为只读元组

```typescript
const arr: readonly [string, number] = ["断剑重铸之日", 666];

// 使用readonly来修饰元组之后，任何企图改变元组中元素的操作都会报错
arr[0] = "骑士归来之时"; // 这里会报错

arr.push(6); // 这里也会报错
```

### 2、函数

```typescript
// 函数声明
// 表示参数x,y,类型都为number，返回值也是number类型
function sum(x: number, y: number): number {
  return x + y;
}

// 函数表达式

const sum = function (x: number, y: number): number {
  return x + y;
};

// 箭头函数

const sum = (x: number, y: number): number => x + y;

// 函数的可选参数
// 可选参数的后面不允许再出现必需参数
function queryUserInfo(name: string, age?: number) {
  if (age) {
    return `我叫${name},${age}岁`;
  }
  return `我叫${name},年龄保密`;
}

queryUserInfo("王思聪", 18); // 我叫王思聪，18岁（有钱人永远18岁！）
queryUserInfo("孙一宁"); // 我叫孙一宁，年龄保密

// 参数默认值
// 可以给一个参数一个默认值，当调用者没有传该参数或者传入了undefined时，这个默认值就生效了
// 默认值也可以在必需参数之前，想要触发默认值，必须要主动的传入undefined

function queryUserInfo(name: string, age: number, sex: string = "不详") {
  return `姓名:${name}，年龄:${age}，性别:${sex}`;
}

queryUserInfo("xxx", 26); // 姓名:xxx，年龄:26，性别:不详

// 函数的剩余参数

function push(arr: any[], ...items: any[]) {
  items.forEach((item) => arr.push(item));
}

let array: any[] = [];
push(array, 1, 2, 3, "迪丽热巴", "古力娜扎");
console.log(array); // [1, 2, 3, '迪丽热巴', '古力娜扎']

// 函数重载

type UnionType = number | string;

function sum(x: number, y: number): number;
function sum(x: string, y: string): string;
function sum(x: string, y: number): string;
function sum(x: number, y: string): string;
function sum(x: UnionType, y: UnionType) {
  if (typeof x === "string" || typeof y === "string") {
    return x.toString() + y.toString();
  }
  return x + y;
}

const res = sum("你", "好");
res.split("");
```

### 3、一些类型

**any**

TS 中，任何类型都可以被归为 any 类型，现在 go 也有这个关键字
any 类型时类型系统的顶级类型
普通类型，不允许赋值过程中改变类型

```typescript
let a: string = "hello";
a = 333; // 这里就会出错

let b: any = "123";
b = 123;
b = {};
b = [];

// 这里都不会报错
// 变量在声明的时候，如果不指定其类型，那么会被识别为any类型

let something;
something = "123";
something = 111;
something = false;
```

**unknown**

```typescript
// unknown与any十分相似，所有类型都可以分配给unknown类型

let a: unknown = 250;
a = "面对疾风吧";
a = true;
```

- unknown 与 any 最大的区别是：任何类型的值都可以赋值给 any，同时 any 类型的值也可以赋值给任何类型（never 除外）。任何类型的值都可以赋值给 unknown，但 unknown 类型的值只能赋值给 unknown 和 any：

```typescript
let a: unknown = 520;
let b: any = a; // ok

let a: any = 520;
let b: unknown = a; // ok

let a: unknown = 520;
let b: number = a; // error

// 如果不缩小类型，就无法对unknown类型执行任何操作
// 可以使用typeof或者类型断言等方式来缩小未知范围

const a: unknown = "超神!";
a.split(""); // error

if (typeof a === "string") {
  a.split(""); // ok
}

// 类型断言，后面会讲到
(a as string).split(""); // ok
```

**void**

void 表示没有任何类型，和其他类型是平等关系，不能直接赋值

```typescript
let a: void;
let b: number = a; // Type 'void' is not assignable to type 'number'

// 声明一个void类型的变量没有任何意义，一般只有在函数没有返回值的时候才会使用它
```

**never**

never 表示那些用不存在的值的类型
值会永不存在有两种情况： 1.如果一个函数执行抛出了异常，那么这个函数就永远不存在返回值 2.函数在执行无限循环的代码

```typescript
// 抛出异常
function error(msg: string): never {
  // ok
  throw new Error(msg);
}

// 死循环
function loopForever(): never {
  // ok
  while (true) {}
}
```

never 和 null 和 undefined 一样，也是任何类型的子类型，也可以赋值给任何类型
但是没有类型是 never 的子类型或可以赋值给 never 类型，除了 never 本身之外，即时 any 也不可以赋值给 never:

```typescript
let a: never;
let b: never;
let c: any;

a = 250; //error
a = b; //ok
b = c; // error
```

在 ts 中，可以利用 never 类型的特性来实现全面性检查

```typescript
type Type = string | number;

function inspectWithNerver(param: Type) {
  if (typeof param === "string") {
    // 在这里收窄为 string 类型
  } else if (typeof param === "number") {
    // 在这里收窄为 number 类型
  } else {
    // 在这里是 never 类型
    const check: never = param;
  }
}

// 在 else 分支里，把既不是string类型也不是number类型的param赋值给了一个显式声明的never类型的变量，如果一切逻辑正确，那么就可以编译通过。假如有一天你的同事修改了Type的类型：

type Type = string | number | boolean;

// 然而他忘记了同时修改inspectWithNever方法中的控制流程，这时else分支的param类型会被收窄为boolean类型，导致无法赋值给never类型，此时就会出现一个错误提示。
// 通过这种方法，可以确保inspectWithNever方法总是穷尽了Type的所有可能类型，使得代码的类型绝对安全。
```

**object、Object、{}**

小 object 代表的是所有非原始类型，也就是不能把 number string 等原始类型赋值给小 object，在严格模式下,null 和 undeined 类型也不能赋值给小 object

一下类型被视为 string、number、boolean、null、undeined、bigInt、symbol

```typescript
let obj: object;

obj = 1; //error
obj = "人在塔在!"; // error
obj = true; // error
obj = null; // error
obj = undefined; // error
obj = 100n; // error
obj = Symbol(); // error
obj = {}; // ok
```

大 Object 代表所有拥有 toString hasOwnProperty 方法的类型，所以，所有原始类型和非原始类型都可以赋值给大 Object。同样，在严格模式下 null 和 undefined 类型也不能赋给大 Object

```typescript
let obj: Object;

obj = 1; // ok
obj = "人在塔在!"; // ok
obj = true; // ok
obj = null; // error
obj = undefined; // error
obj = 100n; // ok
obj = Symbol(); // ok
obj = {}; // ok
```

大 Object 包含原始类型，而小 object 仅包含非原始类型。你可能会想，那么大 Object 是不是小 object 的父类型？实际上，大 Object 不仅是小 object 的父类型，同时也是小 object 的子类型。

```typescript
type FatherType = object extends Object ? true : false; // true
type ChildType = Object extends object ? true : false; // true
```

> 空对象和大 Object 可以互相代替，它们两的特性一致

**Number、String、Boolean、Symbol**

首字母大写的 Number String Boolean Symbol 很容易与小写的原始类型 number string boolean symbol 混淆，前者是相应原始类型的包装对象，愿称之为对象类型

```typescript
let a: number = 520;
let b: Number = 250;

a = b; // Type 'Number' is not assignable to type 'number'
b = a; // ok
```

不要使用对象类型来注解值的类型，没有任何意义。

### 4、类型推断

```typescript
let str: string = "我的大刀!"; // let str: string
let num: number = 250; // let num: number
let bool: boolean = false; // let bool: boolean

const str: string = "我的大刀!"; // const str: string
const num: number = 250; // const num: number
const bool: boolean = false; // const bool: boolean

// 自动类型推导，有点类型go
let str = "我的大刀!"; // 同上
let num = 250; // 同上
let bool = false; // 同上

const str = "我的大刀!"; // const str: "我的大刀!"
const num = 250; // const num: 250
const bool = false; // const bool: false

// 把 TS 这种基于赋值表达式推断类型的能力称之为类型推断。
// ，函数返回值、具有初始化值的变量、有默认值的函数参数的类型都可以根据上下文推断出来

function sum(x: number, y: number) {
  return x + y;
}

const value = sum(1, 2); //推断出value是number类型

function sum(x: number, y = 2) {
  return x + y;
}

const value = sum(1); //推断出value的类型是number
const v = sum(1, "2"); // 出错

// 如果定义的时候没有赋值，不管之后有没有赋值，都会被推断为any类型

let a;
a = "你的剑，就是我的剑";
a = 666;
a = true;
```

### 5、类型断言

有时候会遇到这样的情况，你会比 TS 更了解某个值的详细信息，你清楚的知道它的类型比现有类型更加确切：

```typescript
const arr: number[] = [1, 2, 3];
const res: number = arr.find((num) => num > 2); // Type 'undefined' is not assignable to type 'number'
```

这里，res 的值一定是 3，所以它的类型应该是 number，但是 ts 的类型检测无法做到绝对智能，在 ts 看来，res 的类型既可能是 number 也可能是 undefined，所以提示错误信息:不能把 undefined 类型分配给 number 类型

类型断言就派上用场了。类型断言是一种笃定的方式，它只作用于类型层面的强制类型转换（可以理解成一种暂时的善意的谎言，不会影响运行效果），告诉编译器应该按照我们的方式来做类型检查。

**使用 as 做类型断言**

```typescript
const arr: number[] = [1, 2, 3];
const res: number = arr.find((num) => num > 2) as number;
```

**尖括号**

使用尖括号做类型断言

```typescript
const value: any = "我好想点什么!";
const valueLength: number = (<string>value).length;
```

> 以上两种语法虽然没有区别，但是尖括号格式会与 react 中的 JSX 产生语法冲突，因此更推荐使用 as 语法。

**非空断言**

当类型检查系统无法从上下文中断定类型时，非空断言操作符！可以用来断言操作对象是非 null 和 undefined 类型，简单说 v!将从 v 的值域中排除掉 null 和 undefined:

```typescript
let v: null | undefined | string;
v.toString(); // Object is possibly 'null' or 'undefined'
v!.toString(); // ok

type FuncType = () => number;

function fn(getNum: FuncType | undefined) {
  // Object is possibly 'undefined'
  // Cannot invoke an object which is possibly 'undefined'
  const value1 = getNum();
  const value2 = getNum!(); // ok
}
```

**确定赋值断言**

TS 允许在实例属性和变量声明后面添加一个！，用来告诉类型系统该属性会被明确的赋值

```typescript
let x: number;
init();

console.log(x + 1); // Variable 'x' is used before being assigned

function init() {
  x = 1;
}
```

上面的例子，提示错误信息，变量 x 在赋值之前被使用，可以用确定赋值断言来解决这个问题

```typescript
let x!: number;

inti();

console.log(x + 1); //ok

function init() {
  x = 1;
}
```

通过 let x!: number 确定赋值断言，TS 编译器就会知道该属性会被明确地赋值。

> 注意： !不要轻易使用，如果值本身就是 null 或者 undefined，使用!仅仅是绕过了检查，程序仍会报错。

### 6、字面量类型

在 TS 中，字面量不仅可以表示值，还可以表示类型，即字面量类型。
目前支持三种字面量类型：字符串字面量类型、数字字面量类型、布尔值字面量类型，对应的字符串字面量、数字字面量、布尔值字面量分别拥有与其值一样的字面量类型：

```typescript
let x: "是时候表演真正的技术了!" = "是时候表演真正的技术了!";
let y: 666 = 666;
let z: false = false;

// 冒号后面的'是时候表演真正的技术了!在这里表示一个字符串字面量类型，它其实是string类型，准确地说是string类型的子类型。而string类型不一定是字符串字面量类型

let a: "长枪依在!" = "长枪依在!";
let b: string = "你要来几发么?";
a = b; // Type 'string' is not assignable to type '"长枪依在!"'
b = a; // ok

// 上面的栗子同样适用于其它字面量类型。实际上，定义单个的字面量类型并没有太大的用处，它真正的应用场景是把多个字面量类型组合成一个联合类型，用来描述有明确成员的实用的集合。联合类型后面会讲到

type Direction = "up" | "down";

function move(dir: Direction) {
  // ...
}

move("up"); // ok
move("left"); // Argument of type '"left"' is not assignable to parameter of type 'Direction'

// 通过使用字面量类型组合而成的联合类型，可以限制函数的入参为更具体的类型。这既提升了代码的可读性，也更加安全
```

**let 和 const**

```typescript
const str = "我还以为你从来都不会选我呢"; // str: '我还以为你从来都不会选我呢'
const num = 1; // num: 1
const bool = true; // bool: true

// 上面代码中，用const定义不可变的常量，在没有添加类型注解的情况下，TS 推断出常量的类型为赋值字面量的类型

let str = "hello"; //string
let num = 1; //number
let bool = false; //boolean

// 没有给使用let定义的变量显式地添加类型注解，但是变量的类型自动地转换成了赋值字面量类型的爸爸类型。
// 这种设计符合编程预期，所以可以将任何string类型的值赋给str，也可以将任何number类型的值赋给num

str = "我还没脚软呢，泥腿子!";
num = 888;
bool = false;
```

**类型拓宽**

所有通过 let 和 var 定义的变量、函数的形参，对象的非只读属性，如果满足指定了初始值且未显式添加类型注解的条件，那么它们推断出来的类型就是指定的初始值字面量类型拓宽后的类型，这就是字面量类型拓宽。

```typescript
let str = "我用双手成就你的梦想"; // str: string

let fn = (x = "奉均衡之命!") => x; // fn: (x?: string) => string

const a = "明智之选"; // a: '明智之选'
let b = a; // b: string
let func = (c = a) => c; // func: (c?: string) => string

// 除了字面量类型拓宽之外，TS 对某些特定类型值也有类似类型拓宽的设计。例如对null和undefined的类型进行拓宽，通过let var定义的变量如果满足未显式添加类型注解且被赋予了null或undefined值，则推断出这些变量的类型为any

let x = null; //x:any
let y = undefined; // y:any

const a = null; // a:null
const b = undefined; // b:undefined

type ObjType = {
  a: number;
  b: number;
  c: number;
};

type KeyType = "a" | "b" | "c";

function fn(object: Object, key: KeyType) {
  return object[key];
}

let object = { a: 123, b: 456, c: 789 };
let key = "a";
fn(object, key); //  Argument of type 'string' is not assignable to parameter of type '"a" | "b" | "c"'

// 这里之所以会报错，变量key的类型被推断成了string类型（类型拓宽） ，但是函数期望它的第二个参数是一个更具体的类型，所以报错。
```

TS 提供了一些控制拓宽过程的方法，其中一种是使用 const，如果用 const 声明一个变量，那么它的类型会更窄

```typescript
type ObjType = {
  a: number;
  b: number;
  c: number;
};

type KeyType = "a" | "b" | "c";

function fn(object: ObjType, key: KeyType) {
  return object[key];
}

let object = { a: 123, b: 456, c: 789 };
const key = "a"; // ok
fn(object, key); // 这里会报错
```

这里之所以会报错，这是因为变量 key 的类型被推断成了 string 类型（类型拓宽） ，但是函数期望它的第二个参数是一个更具体的类型，所以报错

**类型拓宽的控制过程方法**

```typescript
// 使用const,使用const声明一个变量，那么它的类型会更窄
type ObjType = {
  a: number;
  b: number;
  c: number;
};

type KeyType = "a" | "b" | "c";

function fn(object: ObjType, key: KeyType) {
  return object[key];
}

let object = { a: 123, b: 456, c: 890 };
const key = "a"; //ok
fn(object, key);

// const能解决上面的问题，但有时也不起作用
const obj = {
    x: 250,
}

obj.x = 520; // ok
obj.x = '520'; // Type 'string' is not assignable to type 'number'
obj.y = 1314; // Property 'y' does not exist on type '{ x: number; }'

```

对于对象而言，TS 的拓宽算法会将其内部属性视为赋值给let关键字声明的变量，进而来推断其属性的类型。因此，obj的类型为{x: number}。obj.x的值可以是任何number类型的值，但不允许是string类型的，同时也不允许给obj对象添加其它的属性

要解决上面的问题，可以使用const断言.

```typescript
// TS: {x: number; y: number}
const obj1 = {
    x: 1,
    y: 2,
}

// TS: {x: 1; y: number}
const obj2 = {
    x: 1 as const,
    y: 2,
}

// TS: {readonly x: 1; readonly y: 2}
const obj3 = {
    x: 1,
    y: 2,
} as const;

const arr1 = [1, 2, 3]; // TS: number[]
const arr2 = [1, 2, 3] as const; // TS: readonly [1, 2, 3]

// 当在某个值后面使用了const断言时，TS 会为这个值推断出最窄的类型，没有拓宽。对于真正的常量，这通常是你想要的
```

**类型缩小**
