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
```
