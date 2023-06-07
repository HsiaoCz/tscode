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

**Getters/Setter**

```typescript
// 类也可以有存取器
class C {
  _length = 0;
  get length() {
    return this._length;
  }
  set length(value) {
    this._length = value;
  }
}

// TypeScript 对存取器有一些特殊的推断规则：
// 如果 get 存在而 set 不存在，属性会被自动设置为 readonly
// 如果 setter 参数的类型没有指定，它会被推断为 getter 的返回类型
// getters 和 setters 必须有相同的成员可见性（Member Visibility）

// 从 TypeScript 4.3 起，存取器在读取和设置的时候可以使用不同的类型。
class Thing {
  _size = 0;

  // 注意这里返回的是 number 类型
  get size(): number {
    return this._size;
  }

  // 注意这里允许传入的是 string | number | boolean 类型
  set size(value: string | number | boolean) {
    let num = Number(value);

    // Don't allow NaN, Infinity, etc
    if (!Number.isFinite(num)) {
      this._size = 0;
      return;
    }

    this._size = num;
  }
}
```

**索引签名**

类可以声明索引签名，它和对象类型的索引签名是一样的

```typescript
class MyClass {
  [s: string]: boolean | ((s: string) => boolean);

  check(s: string) {
    return this[s] as boolean;
  }
}

// 因为索引签名类型也需要捕获方法的类型，这使得并不容易有效的使用这些类型。通常的来说，在其他地方存储索引数据而不是在类实例本身，会更好一些
```

**类继承**

implements 语句仅仅检查类是否按照接口类型实现，但它并不会改变类的类型或者方法的类型

```typescript
interface Pingable {
  ping(): void;
}

class Sonar implements Pingable {
  ping() {
    console.log("ping!");
  }
}

class Ball implements Pingable {
  // Class 'Ball' incorrectly implements interface 'Pingable'.
  // Property 'ping' is missing in type 'Ball' but required in type 'Pingable'.
  pong() {
    console.log("pong!");
  }
}

// 类也可以实现多个接口，比如 class C implements A, B {
// 实现一个可选属性的接口，并不会创建这个属性
interface A {
  x: number;
  y?: number;
}
class C implements A {
  x = 0;
}
const c = new C();
c.y = 10;

// Property 'y' does not exist on type 'C'.
```

**extends**
类可以 extend 一个基类。一个派生类有基类所有的属性和方法，还可以定义额外的成员。

```typescript
class Animal {
  move() {
    console.log("Moving along!");
  }
}

class Dog extends Animal {
  woof(times: number) {
    for (let i = 0; i < times; i++) {
      console.log("woof!");
    }
  }
}

const d = new Dog();
// Base class method
d.move();
// Derived class method
d.woof(3);
```

**覆写属性**

一个派生类可以覆写一个基类的字段或属性。可以使用 super 语法访问基类的方法。
TypeScript 强制要求派生类总是它的基类的子类型。

```typescript
class Base {
  greet() {
    console.log("Hello, world!");
  }
}

class Derived extends Base {
  greet(name?: string) {
    if (name === undefined) {
      super.greet();
    } else {
      console.log(`Hello, ${name.toUpperCase()}`);
    }
  }
}

const d = new Derived();
d.greet();
d.greet("reader");
```

派生类需要遵循它的基类实现，而且一个基类引用指向一个派生类实例，这是非常常见且合法的

```typescript
// Alias the derived instance through a base class reference
const b: Base = d;
// No problem
b.greet();
```

**初始化顺序**

有些情况下，js 类初始化顺序会感到奇怪

```typescript
class Base {
  name = "base";
  constructor() {
    console.log("My name is " + this.name);
  }
}

class Derived extends Base {
  name = "derived";
}

// Prints "base", not "derived"
const d = new Derived();
```

类的初始化顺序：

- 基类字段初始化
- 基类构造函数运行
- 派生类字段初始化
- 派生类构造函数运行

**成员可见性**

类成员默认的可见性为 public，一个 public 的成员可以在任何地方被获取：

```typescript
class Greeter {
  public greet() {
    console.log("hi!");
  }
}
const g = new Greeter();
g.greet();
```

protected 成员仅仅对子类可见

```typescript
class Greeter {
  public greet() {
    console.log("Hello, " + this.getName());
  }
  protected getName() {
    return "hi";
  }
}

class SpecialGreeter extends Greeter {
  public howdy() {
    // OK to access protected member here
    console.log("Howdy, " + this.getName());
  }
}
const g = new SpecialGreeter();
g.greet(); // OK
g.getName();
```

**受保护成员的公开**

派生类需要遵循基类的实现，但是依然可以选择公开拥有更多能力的基类子类型，这就包括让一个 protected 成员变成 public

```typescript
class Base {
  protected m = 10;
}
class Derived extends Base {
  // No modifier, so default is 'public'
  m = 15;
}
const d = new Derived();
console.log(d.m); // OK

// 这里需要注意的是，如果公开不是故意的，在这个派生类中，需要小心的拷贝 protected 修饰符
```

**交叉等级受保护成员访问**

```typescript
// 不同的 OOP 语言在通过一个基类引用是否可以合法的获取一个 protected 成员是有争议的。
class Base {
  protected x: number = 1;
}
class Derived1 extends Base {
  protected x: number = 5;
}
class Derived2 extends Base {
  f1(other: Derived2) {
    other.x = 10;
  }
  f2(other: Base) {
    other.x = 10;
    // Property 'x' is protected and only accessible through an instance of class 'Derived2'. This is an instance of class 'Base'.
  }
}
// 在 Java 中，这是合法的，而 C# 和 C++ 认为这段代码是不合法的。
// typeScript 站在 C# 和 C++ 这边。因为 Derived2 的 x 应该只有从  Derived2 的子类访问才是合法的，而 Derived1 并不是它们中的一个。此外，如果通过 Derived1 访问 x 是不合法的，通过一个基类引用访问也应该是不合法的。
// 我有一说一，typescript本就是微软搞得，肯定占同是微软的C#啊
// 只能说微软搞得语言，跟java不是一般得像
```

**private**

private 有点像 protected ，但是不允许访问成员，即便是子类

```typescript
class Base {
  private x = 0;
}
const b = new Base();
// Can't access from outside the class
console.log(b.x);
// Property 'x' is private and only accessible within class 'Base'.

class Derived extends Base {
  showX() {
    // Can't access in subclasses
    console.log(this.x);
    // Property 'x' is private and only accessible within class 'Base'.
  }
}

// 因为 private 成员对派生类并不可见，所以一个派生类也不能增加它的可见性：
```

**交叉实例私有成员访问**

```typescript
// TypeScript 允许交叉实例私有成员的获取
class A {
  private x = 10;

  public sameAs(other: A) {
    // No error
    return other.x === this.x;
  }
}
```

private 和 protected 仅仅在类型检查得时候才会强制生效

```typescript
class MySafe {
  private secretKey = 12345;
}

// In a JavaScript file...
const s = new MySafe();
// Will print 12345
console.log(s.secretKey);
```

private 允许在类型检查的时候，通过方括号语法进行访问。这让比如单元测试的时候，会更容易访问 private 字段，这也让这些字段是弱私有（soft private）而不是严格的强制私有

```typescript
class MySafe {
  private secretKey = 12345;
}

const s = new MySafe();

// Not allowed during type checking
console.log(s.secretKey);
// Property 'secretKey' is private and only accessible within class 'MySafe'.

// OK
console.log(s["secretKey"]);
```
