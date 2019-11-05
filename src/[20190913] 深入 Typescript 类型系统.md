最近项目中准备推广接入 Typescript，抽空复习了一波相关的技术知识。说复习是因为之前看过了然而现在已经忘得一干二净了，除了不常用的原因外，也是因为 Typescript 知识相对比较零散，学习时难成体系，所以趁这个机会整理整理，就当学习笔记吧。

## 前言

Typescript （简称 TS）是 Javascript 的一个超集，它提供一套全面的对语法定义、使用的约束，来强化 JS 代码的可读性和可维护性，其中最重要的就是 TS 的类型系统。TS 类型系统提供了一套完备的声明和使用类型的方案，灵活但也复杂难记，导致 TS 的学习成本较高。

学习 TS 的类型系统，首先要记住很重要的一点：类型系统只在编译时起作用，最终必然不会出现在你的业务代码里。认识到这一点是很必要的，因为我们学习 TS 很容易将它跟 JS 的语法代码放在一块来理解，其实这是不对的， TS 类型系统仅仅就是 TS 对 JS 的一种强化手段，学习 TS 类型系统，首先就是要忘记 JS，要把类型系统跟 JS 尽可能划分开来，才能避免一些语法的理解干扰。

让类型更加灵活通用，是类型系统中一切语法特性的目标。独立地理解 TS 类型系统，也是基于这个目标由浅到深地了解。

## 从最简单的类型讲起

声明类型是使用类型的前提，TS 提供了许多用于声明类型的语法特性，其唯一目的就是不断完善和强化 TS 声明类型的能力，让类型声明更加地灵活、可复用。

我们从最简单的类型开始来认识 TS，包括基础类型和高级类型。在 TS 中要声明一个类型，最简单的就是使用 `type` 关键字。

### 基础类型

基础类型对应了 JS 的多个基础的数据类型，主要包括：

- 原始类型：`boolean`、`number`、`string`、`void`、`any`、`null`、`undefined` 以及 `symbol`。
- 引用类型：对象 `Object` 、函数 `Function` 以及数组 `Array` 等。

这是 TS 中最简单的数据类型了，我们列举几个经常用到的：

```ts
// 声明的时候：
type _string = string;  // 字符串类型
type _number = number;  // 数字类型
type _boolean = boolean;  // 布尔值类型
type _any = any;  // 任意值
type _object = {  // 对象类型
  name: string;
  age: number
}
type _array = _object[];  // 数组类型
type _function = (user: _object) => number;  // 函数类型

// 使用的时候：
const str: _string = '111';
const num: _number = 3;
const show: _boolean = true;
let err: _any = -1;
err = 'something wrong';
const user: _object = {
  name: 'hankle',
  age: 23
}
const list: _array = [user];
const getAge: _function = (user) => {
  return user.age
}
```

我们声明了几个不同的类型，并在使用的时候以 `: [type]` 的方式接入，进而对我们的变量做了一层类型约束。从上边例子我们也可以看出，`type` 可以给已有类型声明一个别名。

### 高级类型

高级类型，可以让我们更加灵活地重用已有的类型，是复用的一种有效手段。

#### 联合类型

联合类型表示取值可以为多种类型中的其中一种，使用 `|` 连接符，作用机制类似于 "或"。

```ts
type keyType = string | number;

const strKey: keyType = 'key';
const numKey: keyType = 1;
```

键类型可以是 string，也可以是 number。

#### 交叉类型

交叉类型表示变量应该具备多个类型的所有特性，使用 `&` 连接符，作用机制类似于 "并"，多用于对象类型。在我们需要对一些已定义好的 base 对象类型进行扩展时，可以使用交叉类型来声明。

```ts
type User1 = {
  name: string;
  age: number
};
type User2 = {
  sex: number
}

const user: User1 & User2 = {
  name: 'hankle',
  age: 23,
  sex: 0
};
```

`_User1 & _User2` 表示变量需要具备 _User1 和 _User2 中的所有属性。

#### 字面量类型

`type` 声明的类型还可以是一个字面量，表示这个类型的取值只能是这个固定值，这就是字面量类型：

```ts
type coco = 'coco';
const str: coco = 'coco coco';
// 不能将类型“"coco coco"”分配给类型“"coco"”。
```

有一种常用的字面量类型，叫「字符串字面量类型」，它结合联合类型进行使用，用来约束取值只能是某几个字符串中的一个：

```ts
type Key = 'name' | 'age';
type User = {
  name: string,
  age: number
}

const user: User = { name: 'hankle', age: 23 }
const getVal = (user: User, key: Key): any => user[key]

getVal(user, 'sex');
// 类型“"sex"”的参数不能赋给类型“Key”的参数。
```
key 类型限制了对于 user 对象可取的属性键值，避免代码企图获取 user 中并不存在的属性。


## 接口：声明类型的另一种方式

除了 `type`，在 TS 中还有另外一种声明类型的方式，那就是接口（Interface）。使用 `interface` 关键字：

```ts
/**
 * 声明对象
 * 等同于：
 * type User = { name: string; age: number }
 */
interface User {
  name: string;
  age: number
}

/**
 * 声明数组
 * 等同于：
 * type List = User[]
 */
interface List {
  [index: number]: User
}

/**
 * 声明函数
 * 等同于：
 * type findFunc = (list: List, name: string) => User
 */
interface findFunc {
  (list: List, name: string): User
}
```

为什么已经有了 `type`，还需要另外创造出「接口」来声明类型呢？事实上在很多 OOP 语言（比如 Java）中，接口是一个很重要的概念，用于对类（class）行为进行抽象。因此，TS 的接口除了描述对象类型、数组类型或函数类型的形状外，还可以描述类的形状。通过实现（implements）和继承（extends），接口定义的类型可以实现高度复用，这本就是 TS 类型系统的初衷。

在 TS 中，类（class）类型接口的声明和实现是这样的：

```ts
// 定义了一个报警类接口，要求实现该接口的类都必须拥有 alert 方法
interface Alarm {
    alert();
}
// SecurityDoor 类实现了该接口，并实现了 alert 方法
class SecurityDoor implements Alarm {
    alert() {
        console.log('SecurityDoor alert');
    }
}
// Car 类没有实现 alert 方法，报错了
class Car implements Alarm {
}
// 类“Car”错误实现接口“Alarm”。
// Property 'alert' is missing in type 'Car' but required in type 'Alarm'.
```

`type` 关键字声明的类型可以通过联合类型对类型进行扩展，而接口则是利用继承（extends）来实现的：

```ts
interface User1 {
  name: string;
  age: number
};
// 声明了接口 User2，继承自接口 User1，它需要包含 User1 的形状
interface User2 extends User1 {
  sex: number
}

const userq: User2 = {
  name: 'hankle',
  sex: 0
};
// Property 'age' is missing in type '{ name: string; sex: number; }' but required in type 'User2'.
```

至于说什么时候使用 `type`，什么时候使用 `interface` ，网上倒是有不少关于这个问题的解读。其实这两个能做的事情都大同小异，至于选用哪个，我认为在平时业务场景开发中，如果多是对一些函数结构、象结构、类结构作类型约束的话，使用 `interface` 会更加地语义化，建议多用 `interface` 来声明类型。

## 泛型：让你的类型更通用

如果现有一个 `find` 函数，作用等同于 `Array.find`，需要我们自己声明它的函数类型，那到目前来说，我们可能只能利用函数的声明重载来这样写：

```ts
// 声明重载
function find(list: string[], func: (item: string) => boolean): string | null;
function find(list: number[], func: (item: number) => boolean): number | null;
function find(list: object[], func: (item: object) => boolean): object | null;
// 实现
function find(list, func) {
  return list.find(func)
}
```

函数重载允许通过多次声明函数的不同形状来实现函数的联合类型。`find` 方法允许传入多种类型的数组，其返回值也将由传入数组所包含的元素类型决定。从上边可以看出，我们重复声明的只是不同的元素类型，函数的参结构是没有变化的，所以出现了很多重复代码。

### 函数的泛型

泛型（Generics）正是为了解决这个问题。顾名思义，泛型代表不确定的类型，它允许复杂类型在声明的时候内部保留不确定的类型，等到使用的时候再具体指定。泛型相当于一个函数，具体说是类型的工厂函数，既然是函数那就是要“传参”的，泛型传参使用`<>`实现。上边例子用泛型实现是这样的：

```ts
// find 的类型是一个泛型
function find<T>(list: T[], func: (item: T) => boolean): T | null {
  return list.find(func)
}

// 使用时再指定具体类型
find<number>([1,2,3], item => item > 2)
```

当然，你也可以用接口来声明函数类型：

```ts
// find 的类型 FindType 是一个泛型
interface FindType {
  <T>(list: T[], func: (item: T) => boolean): T | null
}
const find: FindType = function (list, func) {
  return list.find(func)
}

// 使用时指定具体类型
find<number>([1,2,3], item => item > 2)
```

泛型还可以允许指定多个类型参数：

```ts
// tuple元素互换
function swap<T, U>(tuple: [T, U]): [U, T] {
  return [tuple[1], tuple[0]];
}

swap<number, string>([7, 'seven']); // ['seven', 7]
```

### 类的泛型

泛型不仅可以作用在函数类型上，还普遍地应用在类的类型定义中。和函数调用时再具体指定类型相似，类的泛型是在实例化对象时才具体指定的：

```ts
// 声明了一个泛型类
class EleList<T> {
  list: T[]
  add: (item: T) => number
}

// 实例化时，指定为字符串类型
const users = new EleList<string>();
users.list = ['hankle', 'nancy'];
users.add = function (item) {
  this.list.push(item);
  return this.list.length;
}
```

### 泛型约束

泛型允许我们预设和使用一个不确定的类型，但有时候“不确定”也对我们的使用造成了影响，比如我们有这样一个用于获取长度的工具方法，入参可以是一个数组，也可以是一个类数组对象：

```ts
function get<T>(arg: T): number {
  return arg.length; // 类型“T”上不存在属性“length”。
}
```

然而我们发现它报错了。类型 T 在这里是任意类型，我们并不能保证使用时指定的类型都存在属性 `length`。我们希望T是有限制的，它只能是数组或类数组对象，或者更具体地说，它必须包含 `length` 属性。所以这时候，泛型约束派上用场了：

```ts
// 声明 LengthType 类型，必须包含 length 属性
interface LengthType {
  length: number;
}

// T 继承自 LengthType 类型，也就表明它必须具有 length 属性
function get<T extends LengthType>(arg: T): number {
  return arg.length;
}
```

这里我们声明了一个具有 `length` 属性的类型，然后让类型 T 继承该类型，从而对泛型的结构进行约束。我们说过接口继承也使用了 `extends` 关键字，它的深层含义就是继承者必须包含被继承者的所有特性，所以泛型约束同样也是借助 `extends` 的这个作用来限制泛型的结构形状。泛型约束可以帮助我们减少许多无意义的判断逻辑，在实际开发过程中，我们的泛型往往都不是完全的任意类型，因此应当善于使用泛型约束。

## 高阶类型：泛型还能这么玩

泛型可以是 TS 类型系统最值得利用的特性了。前边说过，泛型是一个类型的工厂函数，换句话说，利用泛型可以进一步构建出各式各样的类型。TS 官方把这类类型同样称为高级类型，而我认为把它们叫做高阶类型，更为合适。

### 映射类型

映射类型就是一种常用的泛型结构。映射类型能对一个旧类型的各项属性进行映射，进而生成一个新类型。TS 标准库已经内置了多个十分实用的映射类型，包括 `Partial`，`Required`，`Readonly`，`Pick` 和 `Record`等，我们挑选几个解释一下。

#### Partial\<T\>

```ts
type Partial<T> = {
    [P in keyof T]?: T[P];
};
```

`keyof` 返回的是 T 中所有属性键名组成的字符串字面量类型，所以 `Partial` 用于把传入类型 T 中的各项属性转化为可选属性。

```ts
interface User {
  name: string;
  age: number;
  sex: number;
};

// User 类型的属性是必选的，PartialUser 类型的属性是可选的
type PartialUser = Partial<User>
/**
 * type PartialUser = {
 *   name?: string;
 *   age?: number;
 *   sex?: number;
 * }
 */
```

#### Required\<T\>

```ts
type Required<T> = {
    [P in keyof T]-?: T[P];
};
```

和 `Partial` 相反，`Required` 用于把传入类型 T 中的各项属性转化为必选属性。

```ts
interface User {
  name?: string;
  age?: number;
  sex?: number;
};

// User 类型的属性是可选的，RequiredUser 类型的属性是必选的
type RequiredUser = Required<User>
/**
 * type RequiredUser = {
 *   name: string;
 *   age: number;
 *   sex: number;
 * }
 */
```

#### Readonly\<T\>

```ts
type Readonly<T> = {
    readonly [P in keyof T]: T[P];
};
```

`Readonly` 用于将传入类型 T 中的所有属性转化为只读属性。

```ts
interface User {
  name: string;
  age: number;
  sex: number;
};

// User 类型的属性是可写的，ReadonlyUser 类型的属性是只读的
type ReadonlyUser = Readonly<User>
/**
 * type ReadonlyUser = {
 *   readonly name: string;
 *   readonly age: number;
 *   readonly sex: number;
 * }
 */
```

#### Pick\<T, K\>

```ts
type Pick<T, K extends keyof T> = {
    [P in K]: T[P];
};
```

`Pick` 用于根据传入类型生成一个包含指定属性的新类型，也就是说，我们可以从旧类型中挑出部分属性来构成一个新的类型。

```ts
interface User {
  name: string;
  age: number;
  sex: number;
};

// PickUser 类型没有 sex 属性
type PickUser = Pick<User, 'name' | 'age'>
/**
 * type PickUser = {
 *   name: string;
 *   age: number;
 * }
 */
```

#### Record\<T, K\>

```ts
type Record<K extends keyof any, T> = {
    [P in K]: T;
};
```

`Record` 利用一个字符串字面量类型生成一个对象类型，字符串字面量类型的各项将作为对象类型的属性键名。不同于以上几个类型，`Record` 不是同态的，即它不需要输入类型来拷贝属性，而是直接创建新的属性。

```ts
type RecordUser = Record<'name' | 'telephone' | 'description', string>
/**
 * type RecordUser = {
 *   name: string;
 *   telephone: string;
 *   description: string;
 * }
 */
```

### 条件类型

另一种常用的泛型结构是条件类型。条件类型能在生成一个新类型时对类型的组合转换提供条件限制。同样的，TS 标准库也内置了一些常用的条件类型，包括 `Exclude`，`Extract`，`ReturnType` 和 `Parameters`等。

#### Extract\<T, U\>

```ts
type Extract<T, U> = T extends U ? T : never
```

`Extract<T, U>` 用于从联合类型 T 中排除联合类型 U 中不存在的成员，生成一个新的联合类型，也就是说生成的联合类型只包含那些 T 有且 U 也有的成员，作用类似于求交集。

```ts
type UserKey = Extract<'name' | 'age' | 'sex', 'name' | 'sex' | 'descrption'>
// type UserKey = "name" | "sex"
```


#### Exclude\<T, U\>

```ts
type Exclude<T, U> = T extends U ? never : T
```

`Exclude<T, U>` 用于从联合类型 T 中排除联合类型 U 中存在的成员，生成一个新的联合类型，也就是说生成的联合类型只包含那些 T 有而 U 没有的成员，作用类似于求非集。

```ts
type UserKey = Exclude<'name' | 'age' | 'sex', 'sex'>
// type UserKey = "name" | "age"
```

利用 `Exclude`，我们还可以实现一个和 `Pick` 作用相反的 `NeverPick`：

```ts
type NeverPick<T, U> = {
  [P in Exclude<keyof T, U>]: T[P];
};

interface User {
  name: string;
  age: number;
  sex: number;
};

type NeverPickUser = NeverPick<User, 'sex'>
/**
 * type NeverPickUser = {
 *   name: string;
 *   age: number;
 * }
 */
```

#### Parameters\<T\>

```ts
type Parameters<T extends (...args: any) => any> = T extends (...args: infer P) => any ? P : never
```

`Parameters` 当传入的指定类型为函数类型时，返回函数入参的类型。这里使用了 `infer` 操作符来表示待推断类型。

```ts
interface User {
  name: string;
  age: number;
  sex: number;
};

// 定义一个函数类型 GetUserFunc，入参 name 的类型是 string
interface GetUserFunc {
  (name: string): User 
}

// 提取到了 GetUserFunc 的参数类型
type funcParamsTypes = Parameters<GetUserFunc>
// type funcParamsTypes = [string]
```

#### ReturnType\<T\>

```ts
type ReturnType<T extends (...args: any) => any> = T extends (...args: any) => infer R ? R : any
```

`ReturnType` 当传入的指定类型为函数类型时，返回函数返回值的类型。

```ts
interface User {
  name: string;
  age: number;
  sex: number;
};

// 定义一个函数类型 GetUserFunc，返回值的类型是 User
interface GetUserFunc {
  (name: string): User 
}

// 提取到了 GetUserFunc 的参数类型
type funcReturnTypes = ReturnType<GetUserFunc>
// type funcReturnTypes = User
```

## 参考文章
> [Typescript官方文档-高级类型](https://www.tslang.cn/docs/handbook/advanced-types.html)
> [解读TypeScript中的泛型以及条件类型中的推断](https://juejin.im/post/5d0cf23af265da1b855c5ef6#heading-11)