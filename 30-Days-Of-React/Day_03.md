# 30 Days of React、Day 3: Create React app

## Module

```js
// エクスポート
// math.js
export const addTwo = (a, b) => a + b
export const multiply = (a, b) => a * b
export const subtract = (a, b) => a - b

export default (function doSomeMath() {
  return {
    addTwo,
    multiply,
    subtract,
  }
})()

// 別のファイルで、math.jsをインポート
// index.js
// to import the doSomeMath from the math.js with or without extension
// Pythonと逆になっている from ./math.js import doSomeMath
import doSomeMath from './math.js'

// to import the other modules
// since these modules were not exported as default we have to desctructure
import { addTwo, multiply, subtract } from './math.js'

import * as everything from './math.js' // to import everything remaining
console.log(addTwo(5, 5))
console.log(doSomeMath.addTwo(5, 5))
console.log(everything)
```

+ 以降は、Day2の内容を貼り付けただけ

## 疑問点

+ 。

+ 。

## 感想

+ 。

+ 。

## 抽象化

+ 。

+ 。

## 応用

+ 。

+ 。

## See
