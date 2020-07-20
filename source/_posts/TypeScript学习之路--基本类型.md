---
title: TypeScript学习之路--基本类型
date: 2020-07-20 12:50:50
tags: 
  - TypeScript
  - 前端开发
categories: 
  - TypeScript
---

TIP：最近准备学习Typescript，再次记录一下ts的学习过程


###布尔

```typescript
	let isDone: boolean = true
```

### 数字
typescript里所有数字都是浮点数，这些浮点数的类型是number，支持二进制，八进制，十进制，十六进制

``` typescript
	let decLiteral: number = 6;
	let hexLiteral: number = 0xf00d;
	let binaryLiteral: number = 0b1010;
	let octalLiteral: numebr = 0o744;
```

### 字符串

typescript中可以使用双引号（<code>"</code>）或者单引号（<code>'</code>）表示字符串

``` typescript
	let name: string = "dapan";
	name = "hello"; 
```

还可以使用模板字符串，类似es6

``` typescript
	let name = "dapan";
	let welcome = `welcome ${name}`
```

### 数组

两种定义方式

``` typescript
	let list: number[] = [1, 2, 3];
	let lists: Array<number> = [1, 2, 3]; // 使用数组泛型
```

### 元祖Tuple

元祖允许表示一个已知元素数量和类型的数组，各元素的类型不必相同

``` typescript
	let x: [number, string];
	
	x = [10, 'hello']; // init it ok
	
	x = ['hello', 10]; // init it error
```

当访问一个越界的元素，会使用联合类型代替

``` typescript
	x[3] = 'world'; // ok 可以复制给（string | number）
	
	console.log(x[5]).tostring()); // ok 
	
	x[6] = true; // error 类型必须在（string | number）中
```

### 枚举

enum类型是对JavaScript标准数据类型的一个补充，使用枚举类型可以为一组数值赋予友好的名字

``` typescript
	enum Color {Red, Blue, Green}
	let c: Color = Color.Green; // 2
```

默认情况下，从0开始元素编号，你也可以自定义

``` typescript
	enum Color {Red = 1, Blue, Green}
	let c: Color = Color.Green // 3
```

或者全部自己赋值

枚举类型提供的一个便利是你可以由枚举值获取它的名字。

``` typesciript
	enum Color = { Red = 1, Blue, Green };
	let c: Color = Color[2]; // Blue
```

### 任意值

<code>any</code>类型标记这些变量，编译阶段不进行类型检查

``` typescript
	let notSure: any = 4;
	let list: any[] = [1, 'hello', true];
```

### 空值

某种程度上来说，<code>void</code>与any相反，表示没有任何类型。当函数没有返回值时，通常其返回值类型是<code>void</code>

``` typescript
	function setA(): void {
		console.log('setA');
	}
	
	let unusable: void = undefined; // 变量类型为void时只能赋值undefined or null
```

### Undefined 和 Null

``` typescript
	let u: undefined = undefined;
	let n: null = null;
```

默认情况下<code>null</code>和<code>undefined</code>是所有类型的子类型，

当你指定 <code>--strictNullChecks</code>标记，<code>null</code>和<code>undefined</code>只能赋值给<code>void</code>和他们自己。

### Never

<code>never</code>类型表示那些永远不存在的值。例如，<code>never</code>类型是那些总是会抛出异常或者根本没有返回值的函数表达式或箭头函数表达式的返回值类型。

### Object

<code>object</code>表示非原始类型。

``` typescript
	declare function create(o: object | null): void;
	
	create({ prop: 0 }); //ok
	create(null); //ok
	
	create(42); //error
```

### 类型断言

类型断言有两种形式，其一是“尖括号”

``` typescript
	let someValue: any = "this is a string";
	let strLength: number = (<string>someValue).length;
```

另一个语法是<code>as</code>语法：

``` typescript
	let someValue: any = "this is a string";
	let strLength: number = (someValue as string).length;
```

在使用JSX时只能使用as语法。

### 关于let

let与es6中的let一样。


