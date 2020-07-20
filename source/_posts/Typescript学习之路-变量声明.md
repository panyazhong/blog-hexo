---
title: Typescript学习之路--变量声明
date: 2020-07-20 14:55:03
tags: 
  - TypeScript
  - 前端开发
categories: 
  - TypeScript
---

## 变量申明

与JavaScript类似，<code>var</code> <code>let</code> <code>const</code>

### <code>var</code> 声明

``` javascript
	var a = 3
```

### 作用域规则

``` typescript
	function f(shouldInitialize: boolean) {
		if (shouldInitialize) {
			var x = 10;
		}
		return x
	}
	
	f(true); // 10
	
	f(false); // undefined
```

### <code>let</code> 声明

<code>let</code>与es6中的<code>let</code>一样，有作用域的限制

``` typescript
	let name = "dapan"
```

### 块作用域

<code>let</code>声明的变量使用的是<i>块作用域</i>或<i>词法作用域</i>。

``` typescript
	function f(input: boolean) {
		let a: number = 100;
		
		if (input) {
			let b: number = a + 1;
			return b; 
		}
		
		return b // error: 'b' does't exist here
	}
```

拥有块作用域的变量另一个特点就是它们不能再声明之前进行读写。

### 重定义及屏蔽

<code>let</code>声明的变量不能进行重复声明。

``` typescript
	let x: number = 100; // ok
	ler x: number = 19; // error
	var x: number = 1; //error
```

``` typescript
	function f(condition, x) {
		if (condition) {
			let x = 10;
			return x
		}
		return x
	}
	
	f(false, 100); // 100
	f(true, 0); // 10
```

### 块级作用域变量的获取

同es6 块级作用域

### const

同es6 <code>const</code>

### 解构