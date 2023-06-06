### 9、typescript 之类

**类成员**

```typescript
// 最基本的类，一个空类
class Point {}

// 字段(fields)
// 一个字段声明会创建一个公共可写入的属性

class Point {
  x: number;
  y: number;
}

const pt = new Point();
pt.x = 0;
pt.y = 0;

// class的类型注解是可选的，如果没有，会默认设置为any
// 类的字段可以设置初始值
class Points {
  x = 0;
  y = 0;
}

const pts = new Points();

console.log(`${pt.x},${pt.y}`);

// 一个类的初始值会被用于推断它的类型

const ptss = new Point();
ptss.x = "0"; // Type 'string' is not assignable to type 'number'.
```

strictPropertyInitialization 选项控制了类字段是否需要在构造函数里初始化

```typescript
class BadGreeter {
  name: string;
  // Property 'name' has no initializer and is not definitely assigned in the constructor.
}

class GoodGreeter {
  name: string;

  constructor() {
    this.name = "hello";
  }
}
```

注意，字段需要在构造函数自身进行初始化，Typescript 并不会分析构造函数里调用的方法，进而判断初始化的值，因为一个派生类也许会覆盖这些方法并且初始化成员失败

```typescript
class BadGreeter {
  name: string;
  // Property 'name' has no initializer and is not definitely assigned in the constructor.
  setName(): void {
    this.name = "123";
  }
  constructor() {
    this.setName();
  }
}
```

如果执意要通过其他方式初始化一个字段，而不是在构造函数里（举个例子，引入外部库补充类的部分内容），可以使用明确赋值断言操作符（definite assignment assertion operator） !:

```typescript
class OKGreeter {
  // Not initialized, but no error
  name!: string;
}
```

**Readonly**

字段可以添加 readonly 前缀进行修饰，这会阻止在构造函数之外的赋值

```typescript
class Greeter {
  readonly name: string = "world";

  constructor(otherName?: string) {
    if (otherName !== undefined) {
      this.name = otherName;
    }
  }

  err() {
    this.name = "not ok";
    // Cannot assign to 'name' because it is a read-only property.
  }
}

const g = new Greeter();
g.name = "also not ok";
// Cannot assign to 'name' because it is a read-only property.
```

**构造函数**

类的构造函数跟函数非常类似，可以使用带类型注解的参数、默认值、重载等

```typescript
class Point {
  x: number;
  y: number;

  // Normal signature with defaults
  constructor(x = 0, y = 0) {
    this.x = x;
    this.y = y;
  }
}

class Point {
  // Overloads
  constructor(x: number, y: string);
  constructor(s: string);
  constructor(xs: any, y?: any) {
    // TBD
  }
}
```

但类构造函数签名与函数签名之间也有一些区别：

- 构造函数不能有类型参数（关于类型参数，回想下泛型里的内容），这些属于外层的类声明，稍后就会学习到。
- 构造函数不能有返回类型注解，因为总是返回类实例类型

**super 调用**

就像在 JavaScript 中，如果有一个基类，需要在使用任何 this. 成员之前，先在构造函数里调用 super()

> 这不是和 java 一样吗?

```typescript
class Base {
  k = 4;
}

class Derived extends Base {
  constructor() {
    // Prints a wrong value in ES5; throws exception in ES6
    console.log(this.k);
    // 'super' must be called before accessing 'this' in the constructor of a derived class.
    super();
  }
}
```

**方法 method**

类中的函数属性被称为方法
跟函数，构造函数一样，使用相同的类型注解

```typescript
class Point {
  x = 10;
  y = 10;

  scale(n: number): void {
    this.x *= n;
    this.y *= n;
  }
}
```

注意在一个方法体内，它依然可以通过 this. 访问字段和其他的方法。方法体内一个未限定的名称（unqualified name，没有明确限定作用域的名称）总是指向闭包作用域里的内容。

```typescript
let x: number = 0;

class C {
  x: string = "hello";

  m() {
    // This is trying to modify 'x' from line 1, not the class property
    x = "world";
    // Type 'string' is not assignable to type 'number'.
  }
}
```
