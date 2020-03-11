---
title: leetCode刷题-01
date: 2020-02-27 15:42:06
tags: 
categories: 
  - leetCode
---

### 排列硬币

##### 你总共有 n 枚硬币，你需要将它们摆成一个阶梯形状，第 k 行就必须正好有 k 枚硬币。
##### 给定一个数字 n，找出可形成完整阶梯行的总行数。
##### n 是一个非负整数，并且在32位有符号整型的范围内。

1. 方法一

解题思路：
  - 这是一个很笨的方法  遍历循环  当遇到前一个和小于等于n 后一个大于n的时候  return i-1
``` javascript
  /**
   * @param {number} n
   * @return {number}
   */
  var arrangeCoins = function (n) {
    let total = 0
    for (let i = 0;i <= n; i++) {
      total += i

      if (total === n) return i
      if (total > n) return i-1
    }
  }
```

2. 方法二

解题思路
 - 迭代求解法 O(n)
``` javascript
  /**
   * @param {number} n
   * @return {number}
   */
  var arrangeCoins = function (n) {
    let i = 1
    while (n >= i) {
      n = n - i
      i++
    }
    return i - 1
  }
```

###  数组形式的整数加法

##### 对于非负整数 X 而言，X 的数组形式是每位数字按从左到右的顺序形成的数组。例如，如果 X = 1231，那么其数组形式为 [1,2,3,1]。

##### 给定非负整数 X 的数组形式 A，返回整数 X+K 的数组形式。

1. 方法一

``` javascript
  /**
 * @param {number[]} A
 * @param {number} K
 * @return {number[]}
 */
var addToArrayForm = function (A, K) {
  let AReverse = A.reverse()
  let KArr = K.toString().split('').reverse()
  let flag = 0

  let length = AReverse.length > KArr.length ? AReverse.length : KArr.length
  for (let i = 0; i < length; i++) {
    kNum = !!KArr[i] ? Number(KArr[i]) : 0 
    ANum = !!AReverse[i] ? Number(AReverse[i]) : 0
    let res = flag + kNum + ANum

    flag = res >= 10 ? 1 : 0
    res = res % 10
    AReverse[i] = res
  }
  if (flag) AReverse.push(1)
  return AReverse.reverse()
}
```