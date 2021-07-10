# 30 Days of React、Day 1

## 現状

+ 約1年ほど前に日本語の書籍を写経した程度。

+ 文法をほぼ忘れている。

+ Hooksなどの便利な機能があるのは知っているが、使ったことがない。

## 目標

+ React(Next.js)で簡単なWebアプリを作って公開する。
  + 既存のAPIからデータを取得して表示する部分を実装できるようにする。

+ BackendでAPIを自作&Frontendで取得するWebアプリの土台に。

## 技術選定の理由

+ 動的にUIを更新できる。

+ サンプルが豊富。

## Day1: JavaScript Refresher

### 1. Variables

```js
// キャメルケースで記述
let firstName = 'Asabeneh'
firstName = 'Eyob'

const PI = 3.14 // Not allowed to reassign PI to a new value
```

### 3. Arrays

```js
// 空の配列の作り方
const arr = []

// 初期値を含む
const numbers = [0, 3.14, 9.81, 37, 98.6, 100] // array of numbers
const fruits = ['banana', 'orange', 'mango', 'lemon'] // array of strings, fruits

// 配列のサイズを取得
numbers.length // 変数.プロパティ

// 異なるデータの種類を含めることもできる
const arr = [
  'Asabeneh',
  250,
  true,
  { country: 'Finland', city: 'Helsinki' },
  { skills: ['HTML', 'CSS', 'JS', 'React', 'Python'] },
] // arr containing different data types

// splitで、文字列を区切る
// 区切った後は配列になる
let js = 'JavaScript'
const charsInJavaScript = js.split('') // 区切る文字が指定できる

console.log(charsInJavaScript) // ["J", "a", "v", "a", "S", "c", "r", "i", "p", "t"]

let companiesString = 'Facebook, Google, Microsoft, Apple, IBM, Oracle, Amazon'
const companies = companiesString.split(',')
console.log(companies) // ["Facebook", " Google", " Microsoft", " Apple", " IBM", " Oracle", " Amazon"]

// インデックスを使ったアクセス
// 0-indexed
const fruits = ['banana', 'orange', 'mango', 'lemon']
let firstFruit = fruits[0] // we are accessing the first item using its index
console.log(firstFruit) // banana

// 末尾のインデックスを取得
let lastIndex = fruits.length - 1 // length - 1
lastFruit = fruits[lastIndex]
console.log(lastFruit) // lemon

// 配列の要素を修正
const numbers = [1, 2, 3, 4, 5]
numbers[0] = 10 // changing 1 at index 0 to 10
numbers[1] = 20 // changing  2 at index 1 to 20
console.log(numbers) // [10, 20, 3, 4, 5]

// 配列に対する操作
// 空の配列を生成
// コンストラクタに要素数を指定
const eightEmptyValues = Array(8) // it creates eight empty values
console.log(eightEmptyValues) // [empty x 8]

// 特定の値で初期化
// fill(hoge)で、hogeに初期値を指定
const eight0values = Array(8).fill(0) // it creates eight element values filled with '0'
console.log(eight0values) // [0, 0, 0, 0, 0, 0, 0, 0]

// 2つの配列を統合
// 追加先の配列.concat(追加する要素を含む配列)
// 文字列も可能
const firstList = [1, 2, 3]
const secondList = [4, 5, 6]
const thirdList = firstList.concat(secondList)
console.log(thirdList) // [1, 2, 3, 4, 5, 6]

// 該当する値のインデックスを返す
// 存在しない場合は、-1
// Q: 同じ値があるときは、どうなる?
// A: 一番左のインデックスが返される
const numbers = [1, 2, 3, 4, 5]

console.log(numbers.indexOf(5)) // -> 4
console.log(numbers.indexOf(0)) // -> -1
console.log(numbers.indexOf(1)) // -> 0
console.log(numbers.indexOf(6)) // -> -1

// リストに該当する値が含まれているか確認
// let us check if a banana exist in the array
const fruits = ['banana', 'orange', 'mango', 'lemon']
let index = fruits.indexOf('banana') // 0

if (index != -1) {
  console.log('This fruit does exist in the array')
} else {
  console.log('This fruit does not exist in the array')
}
// This fruit does exist in the array

// we can use also ternary here
// 三項演算子を使うこともできる
index != -1
  ? console.log('This fruit does exist in the array')
  : console.log('This fruit does not exist in the array')

// 複数の同じ値がある場合に、最も右側のインデックスを返す
const numbers = [1, 2, 3, 4, 5, 3, 1, 2]
console.log(numbers.lastIndexOf(2)) // 7
console.log(numbers.lastIndexOf(0)) // -1
console.log(numbers.lastIndexOf(1)) //  6
console.log(numbers.lastIndexOf(4)) //  3
console.log(numbers.lastIndexOf(6)) // -1

// 特定の値が含まれているか確認
// true/falseを返す
// 文字列の場合も同様
const numbers = [1, 2, 3, 4, 5]
console.log(numbers.includes(5)) // true
console.log(numbers.includes(0)) // false
console.log(numbers.includes(1)) // true
console.log(numbers.includes(6)) // false

// 配列を文字列に変換
const numbers = [1, 2, 3, 4, 5]
console.log(numbers.toString()) // 1,2,3,4,5

const names = ['Asabeneh', 'Mathias', 'Elias', 'Brook']
console.log(names.toString()) // Asabeneh,Mathias,Elias,Brook

// 配列の要素を連結する
// Q: arr.toStringとどう違うのか?
// A: 連結するときに、特定の文字を指定することができる
const numbers = [1, 2, 3, 4, 5]
console.log(numbers.join()) // 1,2,3,4,5

const names = ['Asabeneh', 'Mathias', 'Elias', 'Brook']

console.log(names.join()) // Asabeneh,Mathias,Elias,Brook
console.log(names.join('')) //AsabenehMathiasEliasBrook
console.log(names.join(' ')) //Asabeneh Mathias Elias Brook
console.log(names.join(', ')) //Asabeneh, Mathias, Elias, Brook
console.log(names.join(' # ')) //Asabeneh # Mathias # Elias # Brook

const webTechs = [
  'HTML',
  'CSS',
  'JavaScript',
  'React',
  'Redux',
  'Node',
  'MongoDB',
] // List of web technologies

console.log(webTechs.join()) // "HTML,CSS,JavaScript,React,Redux,Node,MongoDB"
console.log(webTechs.join(' # ')) // "HTML # CSS # JavaScript # React # Redux # Node # MongoDB"

// 配列から範囲を指定して、取り出す
const numbers = [1, 2, 3, 4, 5]

console.log(numbers.slice()) // -> it copies all  item
console.log(numbers.slice(0)) // -> it copies all  item
console.log(numbers.slice(0, numbers.length)) // it copies all  item
// 取り出す範囲: [left, right)
console.log(numbers.slice(1, 4)) // -> [2,3,4] // it doesn't include the ending position

// 指定した範囲の値を削除する
const numbers = [1, 2, 3, 4, 5]
console.log(numbers.splice()) // -> remove all items

const numbers = [1, 2, 3, 4, 5]
console.log(numbers.splice(0, 1)) // remove the first item

// 不足している部分は、追加される!!
const numbers = [1, 2, 3, 4, 5, 6]
console.log(numbers.splice(3, 3, 7, 8, 9)) // -> [1, 2, 3, 7, 8, 9] //it removes three item and replace three items

// 既存の配列の末尾に要素を追加、末尾の要素を削除
// syntax: push, pop
const arr = ['item1', 'item2', 'item3']
arr.push('new item')
console.log(arr) // ['item1', 'item2','item3','new item']

const numbers = [1, 2, 3, 4, 5]
numbers.push(6)
console.log(numbers) // -> [1,2,3,4,5,6]

numbers.pop() // -> remove one item from the end
console.log(numbers) // -> [1,2,3,4,5]

// 最初の要素を削除、最初に要素を追加
const numbers = [1, 2, 3, 4, 5]
numbers.shift() // -> remove one item from the beginning
console.log(numbers) // -> [2,3,4,5]

const numbers = [1, 2, 3, 4, 5]
numbers.unshift(0) // -> add one item from the beginning
console.log(numbers) // -> [0,1,2,3,4,5]

// 配列の要素を反転する
const numbers = [1, 2, 3, 4, 5]
numbers.reverse() // -> reverse array order

console.log(numbers) // [5, 4, 3, 2, 1]

numbers.reverse()
console.log(numbers) // [1, 2, 3, 4, 5]

// ソート
const webTechs = [
  'HTML',
  'CSS',
  'JavaScript',
  'React',
  'Redux',
  'Node',
  'MongoDB',
]

webTechs.sort()
console.log(webTechs) // ["CSS", "HTML", "JavaScript", "MongoDB", "Node", "React", "Redux"]

webTechs.reverse() // after sorting we can reverse it
console.log(webTechs) // ["Redux", "React", "Node", "MongoDB", "JavaScript", "HTML", "CSS"]

// 入れ子になった配列
const firstNums = [1, 2, 3]
const secondNums = [1, 4, 9]

const arrayOfArray = [
  [1, 2, 3],
  [1, 2, 3],
]
console.log(arrayOfArray[0]) // [1, 2, 3]

const frontEnd = ['HTML', 'CSS', 'JS', 'React', 'Redux']
const backEnd = ['Node', 'Express', 'MongoDB']
const fullStack = [frontEnd, backEnd]
console.log(fullStack) // [["HTML", "CSS", "JS", "React", "Redux"], ["Node", "Express", "MongoDB"]]
console.log(fullStack.length) // 2 → Q: 次元数?
console.log(fullStack[0]) // ["HTML", "CSS", "JS", "React", "Redux"]
console.log(fullStack[1]) // ["Node", "Express", "MongoDB"]
```

### 4. 条件分岐

+ 基本的に、JSは上から順番に実行される

```js
// If
// 条件を満たしたときに実行
// 変数がtrueのときも(ry
if (condition) {
  //this part of code runs for truthy condition
}

if (condition) {
  // this part of code runs for truthy condition
} else {
  // this part of code runs for false condition
}

if (condition) {
  // code
} else if (condition) {
  // code
} else {
  //  code
}

// Switch
// Q: IfとSwitchの使い分けはどうしている?
switch (caseValue) {
  case 1:
    // code
    break // breakが必要
  case 2:
    // code
    break
  case 3:
  // code
  default: // いずれの条件を満たさない場合
  // code
}

// 三項演算子
// 変数 ? (条件を満たす) : (条件を満たさない)
let isRaining = true
isRaining
  ? console.log('You need a rain coat.')
  : console.log('No need for a rain coat.')
```

### 5. Loops

```js
// for
for (initialization, condition, increment/decrement) {
    code goes here
}

// ()内が、let 変数 = 初期値; 終了値; 加算・減算
for (let i = 0; i < 6; i++) {
  console.log(i)
}

// 配列に対しても処理できる
const nums = [1, 2, 3, 4, 5]
for (let i = 0; i < 6; i++) {
  console.log(nums[i])
}

// while
let count = prompt('Enter a positive number: ')
while (count > 0) {
  console.log(count)
  count-- // デクリメント
}

// for of
// インデックスを取得しない場合
const numbers = [1, 2, 3, 4, 5]
for (const number of numbers) {
  console.log(number)
}

const countries = ['Finland', 'Sweden', 'Norway', 'Denmark', 'Iceland']
for (const country of countries) {
  console.log(country.toUpperCase())
}

// forEach
// インデックスを取得する場合
const numbers = [1, 2, 3, 4, 5]
numbers.forEach((number, i) => {
  console.log(number, i)
})

const countries = ['Finland', 'Sweden', 'Norway', 'Denmark', 'Iceland']
// コールバック関数を受け取る
// 要素、インデックス、配列自身を返す
countries.forEach((country, i, arr) => {
  console.log(i, country.toUpperCase())
})

// for in 
// オブジェクトに対して、繰り返し処理
const user = {
  firstName: 'Asabeneh',
  lastName: 'Yetayeh',
  age: 250,
  country: 'Finland',
  skills: ['HTML', 'CSS', 'JS', 'React', 'Node', 'Python', 'D3.js'],
}

for (const key in user) {
  console.log(key, user[key])
}

// 処理を途中で中断、スキップ
for (let i = 0; i <= 5; i++) {
  if (i == 3) {
    break
  }
  console.log(i)
}

// 0 1 2

for (let i = 0; i <= 5; i++) {
  if (i == 3) {
    continue
  }
  console.log(i)
}
// 0 1 2 4 5
```

### 6.Scope

```js
// Window
// 最も広い
//scope.js
a = 'JavaScript' // is a window scope this found anywhere
b = 10 // this is a window scope variable
function letsLearnScope() {
  console.log(a, b)
  if (true) {
    console.log(a, b)
  }
}
console.log(a, b) // accessible

// Global
// 相対的
// 同じファイル内。コードのブロックに対してグローバルである場合も。
//scope.js
let a = 'JavaScript' // is a global scope it will be found anywhere in this file
let b = 10 // is a global scope it will be found anywhere in this file
function letsLearnScope() {
  console.log(a, b) // JavaScript 10, accessible
  if (true) {
    let a = 'Python'
    let b = 100
    console.log(a, b) // Python 100
  }
  console.log(a, b)
}
letsLearnScope()
console.log(a, b) // JavaScript 10, accessible

// Local
//scope.js
let a = 'JavaScript' // is a global scope it will be found anywhere in this file
let b = 10 // is a global scope it will be found anywhere in this file
function letsLearnScope() {
  console.log(a, b) // JavaScript 10, accessible
  let c = 30
  if (true) {
    // we can access from the function and outside the function but
    // variables declared inside the if will not be accessed outside the if block
    let a = 'Python'
    let b = 20
    let d = 40
    console.log(a, b, c) // Python 20 30
  }
  // we can not access c because c's scope is only the if block
  console.log(a, b) // JavaScript 10
}
letsLearnScope()
console.log(a, b) // JavaScript 10, accessible

// ES6以降の書き方
// 定数: const、別の値を代入する可能性がある: let
//scope.js
function letsLearnScope() {
  // you can use let or const, but gravity is constant I prefer to use const
  const gravity = 9.81
  console.log(gravity)
}
// console.log(gravity), Uncaught ReferenceError: gravity is not defined

if (true) {
  const gravity = 9.81
  console.log(gravity) // 9.81
}
// console.log(gravity), Uncaught ReferenceError: gravity is not defined

for (let i = 0; i < 3; i++) {
  console.log(i) // 1, 2, 3
}
// console.log(i), Uncaught ReferenceError: gravity is not defined
```

### 7.Object

+ 全てのオブジェクトは、keyとvalueを持つ
+ keyの順序は予約されていないか、順序はない

```js
// 空のオブジェクト
const person = {}

// 値を含むオブジェクト
// key: value
// keyは、string, number, boolean, an object, null, undefined or a function
// Q: キーに、nullやundefinedを使う意義とは?
const rectangle = {
  length: 20,
  width: 20,
}
console.log(rectangle) // {length: 20, width: 20}

const person = {
  firstName: 'Asabeneh',
  lastName: 'Yetayeh',
  age: 250,
  country: 'Finland',
  city: 'Helsinki',
  skills: [
    'HTML',
    'CSS',
    'JavaScript',
    'React',
    'Node',
    'MongoDB',
    'Python',
    'D3.js',
  ],
  isMarried: true,
}
console.log(person)

// オブジェクトの値を取り出す方法
// objectName.keyName
const person = {
  firstName: 'Asabeneh',
  lastName: 'Yetayeh',
  age: 250,
  country: 'Finland',
  city: 'Helsinki',
  skills: [
    'HTML',
    'CSS',
    'JavaScript',
    'React',
    'Node',
    'MongoDB',
    'Python',
    'D3.js',
  ],
  getFullName: function () {
    return `${this.firstName}${this.lastName}`
  },
  'phone number': '+3584545454545',
}

// accessing values using .
console.log(person.firstName)
console.log(person.lastName)
console.log(person.age)
console.log(person.location)

// value can be accessed using square bracket and key name
console.log(person['firstName'])
console.log(person['lastName'])
console.log(person['age'])
console.log(person['age'])
console.log(person['location'])

// for instance to access the phone number we only use the square bracket method
console.log(person['phone number'])


// オブジェクトにメソッドを追加
const person = {
  firstName: 'Asabeneh',
  lastName: 'Yetayeh',
  age: 250,
  country: 'Finland',
  city: 'Helsinki',
  skills: [
    'HTML',
    'CSS',
    'JavaScript',
    'React',
    'Node',
    'MongoDB',
    'Python',
    'D3.js',
  ],
  getFullName: function () { // アロー関数は使えない。thisがオブジェクトそのものではなく、アロー関数内のウィンドウを指すため。
    // this: オブジェクト自身
    return `${this.firstName} ${this.lastName}`
  },
}

console.log(person.getFullName())
// Asabeneh Yetayeh


// オブジェクトに、新しいキーを追加
// オブジェクトは、mutable(Q: 変更可能という理解で良い?)
// Q: constで変数を宣言しているのに、中身を変更可能なのはなぜ?
const person = {
  firstName: 'Asabeneh',
  lastName: 'Yetayeh',
  age: 250,
  country: 'Finland',
  city: 'Helsinki',
  skills: [
    'HTML',
    'CSS',
    'JavaScript',
    'React',
    'Node',
    'MongoDB',
    'Python',
    'D3.js',
  ],
  getFullName: function () {
    return `${this.firstName} ${this.lastName}`
  },
}
person.nationality = 'Ethiopian'
person.country = 'Finland'
person.title = 'teacher'
person.skills.push('Meteor')
person.skills.push('SasS')
person.isMarried = true

person.getPersonInfo = function () {
  let skillsWithoutLastSkill = this.skills
    .slice(0, this.skills.length - 1)
    .join(', ')
  let lastSkill = this.skills.slice(this.skills.length - 1)[0]

  let skills = `${skillsWithoutLastSkill}, and ${lastSkill}`
  let fullName = this.getFullName()
  let statement = `${fullName} is a ${this.title}.\nHe lives in ${this.country}.\nHe teaches ${skills}.`
  return statement
}
console.log(person)
console.log(person.getPersonInfo())


// Object Methods
// Object.assign: 元のオブジェクト(修正などが行われていない)をコピー
const person = {
  firstName: 'Asabeneh',
  age: 250,
  country: 'Finland',
  city: 'Helsinki',
  skills: ['HTML', 'CSS', 'JS'],
  title: 'teacher',
  address: {
    street: 'Heitamienkatu 16',
    pobox: 2002,
    city: 'Helsinki',
  },
  getPersonInfo: function () {
    return `I am ${this.firstName} and I live in ${this.city}, ${this.country}. I am ${this.age}.`
  },
}

//Object methods: Object.assign, Object.keys, Object.values, Object.entries
//hasOwnProperty

const copyPerson = Object.assign({}, person)
console.log(copyPerson)

// キー、値、キーと値の取得、属性が存在するかチェック
// Object.keys(objectName)
const keys = Object.keys(copyPerson)
console.log(keys) //['name', 'age', 'country', 'skills', 'address', 'getPersonInfo']
const address = Object.keys(copyPerson.address)
console.log(address) //['street', 'pobox', 'city']

const values = Object.values(copyPerson)
console.log(values)

const entries = Object.entries(copyPerson) // Pythonとは異なり、entries()になっている
console.log(entries)

console.log(copyPerson.hasOwnProperty('name'))
console.log(copyPerson.hasOwnProperty('score'))
```

### 8. Functions

```js
// 関数の定義と呼び出し
//declaring a function without a parameter
function functionName() {
  // code goes here

  // 戻り値を返す
  return some_values
}
functionName() // calling function by its name and with parentheses

// 引数の数が不定の場合
// Let us access the arguments object
​
function sumAllNums() {
 console.log(arguments)
}

sumAllNums(1, 2, 3, 4)
// Arguments(4) [1, 2, 3, 4, callee: ƒ, Symbol(Symbol.iterator): ƒ]

// function declaration
// 配列のように処理できる
function sumAllNums() {
  let sum = 0
  for (let i = 0; i < arguments.length; i++) {
    sum += arguments[i]
  }
  return sum
}

console.log(sumAllNums(1, 2, 3, 4)) // 10
console.log(sumAllNums(10, 20, 13, 40, 10))  // 93
console.log(sumAllNums(15, 20, 30, 25, 10, 33, 40))  // 173

// アロー関数
// Q: ...は、どういう意味で使われている?
// Let us access the arguments object
const sumAllNums = (...args) => {
 // console.log(arguments), arguments object not found in arrow function
 // instead we use an a parameter followed by spread operator
 console.log(args)
}

sumAllNums(1, 2, 3, 4)
// [1, 2, 3, 4]

// function declaration
// アロー関数の中で配列としてアクセスできる
const sumAllNums = (...args) => {
  let sum = 0
  for (const element of args) {
    sum += element
  }
  return sum
}

console.log(sumAllNums(1, 2, 3, 4)) // 10
console.log(sumAllNums(10, 20, 13, 40, 10))  // 93
console.log(sumAllNums(15, 20, 30, 25, 10, 33, 40))  // 173

// Anonymous Function
// 名前がついていない
// Q: なぜ、このような関数が用意されているのか?
const anonymousFun = function () {
  console.log(
    'I am an anonymous function and my value is stored in anonymousFun'
  )
}

// Expression Function
const square = function (n) {
  return n * n
}

console.log(square(2)) // -> 4


// Self Invoking Functions
;(function (n) {
  console.log(n * n)
})(2) // 4, but instead of just printing if we want to return and store the data, we do as shown below

let squaredNum = (function (n) {
  return n * n
})(10)

console.log(squaredNum)


// Arrow Function
// This is how we write normal or declaration function
// Let us change this declaration function to an arrow function
function square(n) {
  return n * n
}

console.log(square(2)) // 4

const square = (n) => {
  return n * n
}

console.log(square(2)) // -> 4

// if we have only one line in the code block, it can be written as follows, explicit return
// 1行なら、とてもシンプルに書ける
// functionだけでなく、{} や returnも不要に
// const 変数名 = (引数) => 引数を使った処理をして、戻り値を返す
const square = (n) => n * n // -> 4

const changeToUpperCase = (arr) => {
  const newArr = []
  for (const element of arr) {
    newArr.push(element.toUpperCase())
  }
  return newArr
}

const countries = ['Finland', 'Sweden', 'Norway', 'Denmark', 'Iceland']
console.log(changeToUpperCase(countries))
// ["FINLAND", "SWEDEN", "NORWAY", "DENMARK", "ICELAND"]

// デフォルト引数をアロー関数で使う場合
// Declaring a function
const functionName = (param = value) => {
  //codes
}

// Calling function
functionName()
functionName(arg)
```

### 9. Higher Order Function

+ 別の関数でバラメータとして扱ったり、関数を値のように返す
+ 関数に渡すパラメータのことをコールバックと呼ぶ
  + コールバック地獄というフレーズも聞いたことがある気がする

```js
// a callback function, the function could be any name
const callback = (n) => {
  return n ** 2
}
​
// function take other function as a callback
function cube(callback, n) {
  return callback(n) * n
}
​
// 引数として書くときは、()を付けない
console.log(cube(callback, 3))


// Higher order function returning an other function
const higherOrder = n => {
  const doSomething = m => {
    const doWhatEver = t => {
      return 2 * n + 3 * m + t
    }
    return doWhatEver
  }
​
  return doSomething
}
console.log(higherOrder(2)(3)(10))

const numbers = [1, 2, 3, 4]
const sumArray = arr => {
  let sum = 0
  const callback = function(element) {
    sum += element
  }
  arr.forEach(callback)
  return sum

}
console.log(sumArray(numbers)) // 10

// 上記のサンプルは、以下のコードと等価
const numbers = [1, 2, 3, 4]
​
const sumArray = arr => {
  let sum = 0
  arr.forEach(function(element) {
    sum += element
  })
  return sum

}
console.log(sumArray(numbers))


// setInterval
// ある処理を一定の間隔で実行するときに使う
function callback() {
  // code goes here
}
setInterval(callback, duration)

function sayHello() {
  console.log('Hello')
}
// msで指定
setInterval(sayHello, 2000) // it prints hello in every 2 seconds


// setTimeout
// 所定の時間が経過した後に実行
// syntax
function callback() {
  // code goes here
}
setTimeout(callback, duration) // duration in milliseconds

function sayHello() {
  console.log('Hello')
}
setTimeout(sayHello, 2000) // it prints hello after it waits for 2 seconds.
```

### 10. Destructuring and Spreading

+ 配列やオブジェクトを分解して、別の変数に割り当てる

```js
//Sample
const numbers = [1, 2, 3]
const countries = ['Finland', 'Sweden', 'Norway']

// Accessing array items using a loop
for (const number of numbers) {
  console.log(number)
}

for (const country of countries) {
  console.log(country)
}

// Accessing array items manually
const numbers = [1, 2, 3]
let num1 = numbers[0]
let num2 = numbers[1]
let num3 = numbers[2]
console.log(num1, num2, num3) // 1, 2, 3

const countries = ['Finland', 'Sweden', 'Norway']
let fin = countries[0]
let swe = countries[1]
let nor = countries[2]
console.log(fin, swe, nor) // Finland, Sweden, Norway

// Accessing array items using destructuring
// const [新しい変数名1, 変数名2, ...] = 元の変数

const numbers = [1, 2, 3]
const [num1, num2, num3] = numbers
console.log(num1, num2, num3) // 1, 2, 3,

const constants = [2.72, 3.14, 9.81,37, 100]
const [e, pi, gravity, bodyTemp, boilingTemp] = constants
console.log(e, pi, gravity, bodyTemp, boilingTemp]
// 2.72, 3.14, 9.81, 37,100
const countries = ['Finland', 'Sweden', 'Norway']
const [fin, swe, nor] = countries
console.log(fin, swe, nor) // Finland, Sweden, Norway

// 新しい変数に割り当てられないときは、undefinedになる
const [fin, swe, nor, den] = countries
console.log(den) // undefined


// 中身が入れ子になっているケースでも、問題なく取り出せる
const fullStack = [
  ['HTML', 'CSS', 'JS', 'React'],
  ['Node', 'Express', 'MongoDB']
]

const [frontEnd, backEnd] = fullstack
console.log(frontEnd, backEnd)
//["HTML", "CSS", "JS", "React"] , ["Node", "Express", "MongoDB"]

const fruitsAndVegetables = [['banana', 'orange', 'mango', 'lemon'],  ['Tomato', 'Potato', 'Cabbage', 'Onion', 'Carrot']]

const [fruits, vegetables] = fruitsAndVegetables
console.log(fruits, vegetables]
//['banana', 'orange', 'mango', 'lemon']
//['Tomato', 'Potato', 'Cabbage', 'Onion', 'Carrot']


// 特定の値をスキップできる
const countries = ['Finland', 'Sweden', 'Iceland', 'Norway', 'Denmark']
const [fin, , ice, , den] = countries
console.log(fin, ice, den) // Finland, Iceland, Denmark

const nums = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
// ...argで、残りの変数をまとめることも
const [num1, num2, num3, ...rest] = nums
console.log(num1, num2, num3, rest) //1, 2, 3, [4, 5, 6, 7, 8, 9, 10]

const countries = [
  'Germany',
  'France',
  'Belgium',
  'Finland',
  'Sweden',
  'Norway',
  'Denmark',
  'Iceland',
]

let [gem, fra, , ...nordicCountries] = countries
console.log(gem, fra, nordicCountries)
// Germany, France,  ["Finland", "Sweden", "Norway", "Denmark", "Iceland"]

// 繰り返し処理でも使える
const countries = [
  ['Finland', 'Helsinki'],
  ['Sweden', 'Stockholm'],
  ['Norway', 'Oslo'],
]

for (const [country, city] of countries) {
  console.log(country, city)
}

const fullStack = [
  ['HTML', 'CSS', 'JS', 'React'],
  ['Node', 'Express', 'MongoDB'],
]

for (const [first, second, third, fourth] of fullStack) {
  console.log(first, second, third, fourt)
}

// React Hooksの例
// countの初期値を0
// setCountは、countの値を変更する関数
const [count, setCount] = useState(0)

// オブジェクトの例
const rectangle = {
  width: 20,
  height: 10,
}

// これまでの書き方
let width = rectangle.width
let height = recangle.height
// or
let width = rectangle[width]
let height = recangle[height]

// destructureを使った書き方
// オブジェクトの場合は、[]ではなく、{}になっている
// let {新しい変数名1, 新しい変数名2, ...} = 元の変数
let { width, height } = rectangle
console.log(width, height, perimeter) // 20, 10

// 変数名を変えることもできる
const rectangle = {
  width: 20,
  height: 10,
}

let { width: w, height: h } = rectangle

// 複雑なオブジェクトでも、簡単に値が取り出せる 
const props = {
  user:{
    firstName:'Asabeneh',
    lastName:'Yetayeh',
    age:250
  },
  post:{
    title:'Destructuring and Spread',
    subtitle:'ES6',
    year:2020
},
skills:['JS', 'React', 'Redux', 'Node', 'Python']

}
}

const {user, post, skills} = props
const {firstName, lastName, age} = user
const {title, subtitle, year} = post
const [skillOne, skillTwo, skillThree, skillFour, skillFive] = skills

// 1回の処理で記述することもできる
const props = {
  user:{
    firstName:'Asabeneh',
    lastName:'Yetayeh',
    age:250
  },
  post:{
    title:'Destructuring and Spread',
    subtitle:'ES6',
    year:2020
  },
  skills:['JS', 'React', 'Redux', 'Node', 'Python']
}

const {user:{firstName, lastName, age}, post:{title, subtitle, year}, skills:[skillOne, skillTwo, skillThree, skillFour, skillFive]} = props

// ループの中でも利用できる
const languages = [
  { lang: 'English', count: 91 },
  { lang: 'French', count: 45 },
  { lang: 'Arabic', count: 25 },
  { lang: 'Spanish', count: 24 },
  { lang: 'Russian', count: 9 },
  { lang: 'Portuguese', count: 9 },
  { lang: 'Dutch', count: 8 },
  { lang: 'German', count: 7 },
  { lang: 'Chinese', count: 5 },
  { lang: 'Swahili', count: 4 },
  { lang: 'Serbian', count: 4 },
]

for (const { lang, count } of languages) {
  console.log(`The ${lang} is spoken in ${count} countries.`)
}

// Destructuring function parameter
const rectangle = { width: 20, height: 10 }
const calcualteArea = ({ width, height }) => width * height
const calculatePerimeter = ({ width, height } = 2 * (width + height))
```

### 11. Functional Programming

```js
// forEach
// syntax in a normal or a function declaration
function callback(item, index, arr) {}
array.forEach(callback)

// or syntax in an arrow function
const callback = (item, i, arr) => {}
array.forEach(callback)

// アロー関数を使った書き方
// 中身の処理が複雑でない場合に使う
const countries = ['Finland', 'Estonia', 'Sweden', 'Norway']
countries.forEach((country, i) => console.log(i, country.toUpperCase()))

const numbers = [1, 2, 3, 4, 5]
let sum = 0
numbers.forEach((n) => (sum += n))
console.log(sum) // 15


// map
// 配列に変更を加えて、配列を返すときに使う
// syntax in a normal or a function declaration
function callback(item, i) {
  return // code goes here
}

const modifiedArray = array.map(callback)

// or syntax in an arrow function
const callback = (item, i) => {
  return // code goes here
}
const modifiedArray = array.map(callback)


// Using function declaration
const countries = ['Finland', 'Estonia', 'Sweden', 'Norway']

const newCountries = countries.map(function (country) {
  return country.toUpperCase()
})

console.log(newCountries)

// map using an arrow function call back
const countries = ['Finland', 'Estonia', 'Sweden', 'Norway']
const newCountries = countries.map((country) => country.toUpperCase())

console.log(newCountries) // ["FINLAND", "ESTONIA", "SWEDEN", "NORWAY"]


// filter
// 何らかの条件を満たす要素を取り出す
// syntax in a normal or a function declaration
function callback(item) {
  return // boolean
}

const filteredArray = array.filter(callback)

// or syntax in an arrow function
const callback = (item) => {
  return // boolean
}
const filteredArray = array.filter(callback)

const countries = ['Finland', 'Estonia', 'Sweden', 'Norway', 'Iceland']
const countriesWithLand = countries.filter((country) =>
  country.includes('land')
)
console.log(countriesWithLand) // ["Finland", "Iceland"]

const numbers = [0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
const evens = numbers.filter((n) => n % 2 === 0)
const odds = numbers.filter((n) => n % 2 !== 0)
console.log(evens) // [0, 2, 4, 6, 8, 10]
console.log(odds) // [1, 3, 5, 7, 9]


// reduce
// 配列の値を一つにまとめる
// syntax in a normal or a function declaration
function callback(acc, cur) {
    return // code goes here
}

const reduced = array.reduce(callback, optionalInitialValue)

// or syntax in an arrow function
const reduced =  callback(acc, cur) => {
    return // code goes here
}
const reduced = array.reduce(callback)

// 初期値0
const numbers = [0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
const sum = numbers.reduce((acc, cur) => acc + cur) // 要素を一つずつ加算
console.log(sum) // 55

// 初期値が5のケース
const numbers = [0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
const sum = numbers.reduce((acc, cur) => acc + cur, 5)
console.log(sum) // 60

// 文字列をまとめることもできる
const strs = ['Hello', 'world', '!']
const helloWorld = strs.reduce((acc, cur) => acc + ' ' + cur)
console.log(helloWorld) // "Hello world !"

// 複数の要素をまとめて、一つの値にする
const numbers = [1, 2, 3, 4, 5]
const value = numbers.reduce((acc, cur) => acc * cur)
console.log(value) // 120


// find
// 該当する条件のうち、最初の値のみを返す
// syntax in a normal or a function declaration

function callback(item) {
return // code goes here
}

const item = array.find(callback)

// or syntax in an arrow function
const reduced =  callback(item) => {
return // code goes here
}
const item = array.find(callback)

const numbers = [0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
const firstEvenNum = numbers.find((n) => n % 2 === 0)
const firstOddNum = numbers.find((n) => n % 2 !== 0)
console.log(firstEvenNum) // 0
console.log(firstOddNum) // 1

// 文字列の場合も同様
const countries = ['Finland', 'Estonia', 'Sweden', 'Norway', 'Iceland']
const countryWithWay = countries.find((country) => country.includes('way'))
console.log(countriesWithWay) // Norway


// findIndex
// 該当する要素のうち、最初のインデックスを返す
// syntax in a normal or a function declaration

function callback(item) {
return // code goes here
}

const index = array.findIndex(callback)

// or syntax in an arrow function
const reduced =  callback(item) => {
return // code goes here
}
const index = array.findIndex(callback)

const numbers = [0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
const firstEvenIndex = numbers.findIndex((n) => n % 2 === 0)
const firstOddIndex = numbers.findIndex((n) => n % 2 !== 0)
console.log(firstEvenIndex) // 0
console.log(firstOddIndex) // 1


// some
// 一つ以上の要素が条件を満たせばtrue、そうでなければfalseを返す
// Pythonのany()に近いか?
const numbers = [0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
const someAreEvens = numbers.some((n) => n % 2 === 0)
const someAreOdds = numbers.some((n) => n % 2 !== 0)
console.log(someAreEvens) // true
console.log(someAreOdds) // true


// every
// 全ての要素が条件を満たしていればtrue、そうでなければfalseを返す
// Pythonのall()に近いか?
const numbers = [0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
const allAreEvens = numbers.every((n) => n % 2 === 0)
const allAreOdd s= numbers.every((n) => n % 2 !== 0)

console.log(allAreEven) // false
console.log(allAreOdd)  // false

const evens = [0, 2, 4, 6, 8, 10]
const someAreEvens = evens.some((n) => n % 2 === 0)
const someAreOdds = evens.some((n) => n % 2 !== 0)

console.log(someAreEvens) // true
console.log(someAreOdds)  // false
```

### 12. Classes

+ JavaScriptの全ての要素は、オブジェクト
  + プロパティとメソッドを持つ

```js
// syntax
class ClassName {
    //  code goes here
}


// 初期化
class Person {
  // code goes here
}
const person = new Person() // const/let hoge = new ClassName()
console.log(person)


// クラスコンストラクタ
class Person {
  // constructor(引数) {
  //   this.hoge = hoge
  // }
  constructor(firstName, lastName) {
    console.log(this) // Check the output from here
    // thisは、クラス自身を表す
    // Pythonのselfに近いか?
    this.firstName = firstName
    this.lastName = lastName
  }
}

const person = new Person()
console.log(person)

// クラスに引数を指定できる
const person1 = new Person('Asabeneh', 'Yetayeh')


// コンストラクタに、デフォルト引数を指定できる
class Person {
  constructor(
    firstName = 'Asabeneh',
    lastName = 'Yetayeh',
    age = 250,
    country = 'Finland',
    city = 'Helsinki'
  ) {
    this.firstName = firstName
    this.lastName = lastName
    this.age = age
    this.country = country
    this.city = city
  }
}

const person1 = new Person() // it will take the default values
const person2 = new Person('Lidiya', 'Tekle', 28, 'Finland', 'Espoo')

console.log(person1)
console.log(person2)


// メソッド
// ドキュメントにはclass methodと書かれているが、インスタンスメソッドのことか?
// Q: スコープの範囲はどうなっている?
class Person {
  constructor(firstName, lastName, age, country, city) {
    this.firstName = firstName
    this.lastName = lastName
    this.age = age
    this.country = country
    this.city = city
  }
  getFullName() {
    const fullName = this.firstName + ' ' + this.lastName
    return fullName
  }
}

const person1 = new Person('Asabeneh', 'Yetayeh', 250, 'Finland', 'Helsinki')
const person2 = new Person('Lidiya', 'Tekle', 28, 'Finland', 'Espoo')

console.log(person1.getFullName())
console.log(person2.getFullName())


// getter
class Person {
  constructor(firstName, lastName, age, country, city) {
    this.firstName = firstName
    this.lastName = lastName
    this.age = age
    this.country = country
    this.city = city
    this.score = 0
    this.skills = []
  }
  getFullName() {
    const fullName = this.firstName + ' ' + this.lastName
    return fullName
  }
  // get getHoge() { return this.hoge }
  get getScore() {
    return this.score
  }
  get getSkills() {
    return this.skills
  }
}

const person1 = new Person('Asabeneh', 'Yetayeh', 250, 'Finland', 'Helsinki')
const person2 = new Person('Lidiya', 'Tekle', 28, 'Finland', 'Espoo')

console.log(person1.getScore) // We do not need parenthesis to call a getter method
console.log(person2.getScore)

console.log(person1.getSkills)
console.log(person2.getSkills)


// setter
class Person {
  constructor(firstName, lastName, age, country, city) {
    this.firstName = firstName
    this.lastName = lastName
    this.age = age
    this.country = country
    this.city = city
    this.score = 0
    this.skills = []
  }
  getFullName() {
    const fullName = this.firstName + ' ' + this.lastName
    return fullName
  }
  get getScore() {
    return this.score
  }
  get getSkills() {
    return this.skills
  }
  set setScore(score) {
    this.score += score
  }
  set setSkill(skill) {
    this.skills.push(skill)
  }
}

const person1 = new Person('Asabeneh', 'Yetayeh', 250, 'Finland', 'Helsinki')
const person2 = new Person('Lidiya', 'Tekle', 28, 'Finland', 'Espoo')

person1.setScore = 1
person1.setSkill = 'HTML'
person1.setSkill = 'CSS'
person1.setSkill = 'JavaScript'

person2.setScore = 1
person2.setSkill = 'Planning'
person2.setSkill = 'Managing'
person2.setSkill = 'Organizing'

console.log(person1.score)
console.log(person2.score)

console.log(person1.skills)
console.log(person2.skills)


// getter/setterと通常のメソッドとの違い
// getter/setterは、クラスの特定の属性のみを取得・更新
// メソッドは、クラスの属性を複数利用して何らかの処理を行う
class Person {
  constructor(firstName, lastName, age, country, city) {
    this.firstName = firstName
    this.lastName = lastName
    this.age = age
    this.country = country
    this.city = city
    this.score = 0
    this.skills = []
  }
  getFullName() {
    const fullName = this.firstName + ' ' + this.lastName
    return fullName
  }
  get getScore() {
    return this.score
  }
  get getSkills() {
    return this.skills
  }
  set setScore(score) {
    this.score += score
  }
  set setSkill(skill) {
    this.skills.push(skill)
  }
  getPersonInfo() {
    let fullName = this.getFullName()
    let skills =
      this.skills.length > 0 &&
      this.skills.slice(0, this.skills.length - 1).join(', ') +
        ` and ${this.skills[this.skills.length - 1]}`
    let formattedSkills = skills ? `He knows ${skills}` : ''

    let info = `${fullName} is ${this.age}. He lives ${this.city}, ${this.country}. ${formattedSkills}`
    return info
  }
}

const person1 = new Person('Asabeneh', 'Yetayeh', 250, 'Finland', 'Helsinki')
const person2 = new Person('Lidiya', 'Tekle', 28, 'Finland', 'Espoo')
const person3 = new Person('John', 'Doe', 50, 'Mars', 'Mars city')

person1.setScore = 1
person1.setSkill = 'HTML'
person1.setSkill = 'CSS'
person1.setSkill = 'JavaScript'

person2.setScore = 1
person2.setSkill = 'Planning'
person2.setSkill = 'Managing'
person2.setSkill = 'Organizing'

console.log(person1.getScore)
console.log(person2.getScore)

console.log(person1.getSkills)
console.log(person2.getSkills)
console.log(person3.getSkills)

console.log(person1.getPersonInfo())
console.log(person2.getPersonInfo())
console.log(person3.getPersonInfo())


// Static method
// インスタンスメソッドとは違う
// クラス自身を呼び出す
class Person {
  constructor(firstName, lastName, age, country, city) {
    this.firstName = firstName
    this.lastName = lastName
    this.age = age
    this.country = country
    this.city = city
    this.score = 0
    this.skills = []
  }
  getFullName() {
    const fullName = this.firstName + ' ' + this.lastName
    return fullName
  }
  get getScore() {
    return this.score
  }
  get getSkills() {
    return this.skills
  }
  set setScore(score) {
    this.score += score
  }
  set setSkill(skill) {
    this.skills.push(skill)
  }
  getPersonInfo() {
    let fullName = this.getFullName()
    let skills =
      this.skills.length > 0 &&
      this.skills.slice(0, this.skills.length - 1).join(', ') +
        ` and ${this.skills[this.skills.length - 1]}`

    let formattedSkills = skills ? `He knows ${skills}` : ''

    let info = `${fullName} is ${this.age}. He lives ${this.city}, ${this.country}. ${formattedSkills}`
    return info
  }
  // staticが付いている
  static favoriteSkill() {
    const skills = ['HTML', 'CSS', 'JS', 'React', 'Python', 'Node']
    const index = Math.floor(Math.random() * skills.length)
    return skills[index]
  }
  static showDateTime() {
    let now = new Date()
    let year = now.getFullYear()
    let month = now.getMonth() + 1
    let date = now.getDate()
    let hours = now.getHours()
    let minutes = now.getMinutes()
    if (hours < 10) {
      hours = '0' + hours
    }
    if (minutes < 10) {
      minutes = '0' + minutes
    }

    let dateMonthYear = date + '.' + month + '.' + year
    let time = hours + ':' + minutes
    let fullTime = dateMonthYear + ' ' + time
    return fullTime
  }
}

console.log(Person.favoriteSkill())
console.log(Person.showDateTime())


// 継承
// syntax
// extends
class ChildClassName extends ParentClassName {
 // code goes here
}

class Student extends Person {
  saySomething() {
    console.log('I am a child of the person class')
  }
}

const s1 = new Student('Asabeneh', 'Yetayeh', 'Finland', 250, 'Helsinki')
console.log(s1)
console.log(s1.saySomething())
console.log(s1.getFullName())
console.log(s1.getPersonInfo())


// Override
// プロパティの追加やメソッドの内容を上書きできる
class Student extends Person {
  constructor(firstName, lastName, age, country, city, gender) {
    super(firstName, lastName, age, country, city)
    this.gender = gender
  }

  saySomething() {
    console.log('I am a child of the person class')
  }
  getPersonInfo() {
    let fullName = this.getFullName()
    let skills =
      this.skills.length > 0 &&
      this.skills.slice(0, this.skills.length - 1).join(', ') +
        ` and ${this.skills[this.skills.length - 1]}`

    let formattedSkills = skills ? `He knows ${skills}` : ''
    let pronoun = this.gender == 'Male' ? 'He' : 'She'

    let info = `${fullName} is ${this.age}. ${pronoun} lives in ${this.city}, ${this.country}. ${formattedSkills}`
    return info
  }
}

const s1 = new Student(
  'Asabeneh',
  'Yetayeh',
  250,
  'Finland',
  'Helsinki',
  'Male'
)
const s2 = new Student('Lidiya', 'Tekle', 28, 'Finland', 'Helsinki', 'Female')
s1.setScore = 1
s1.setSkill = 'HTML'
s1.setSkill = 'CSS'
s1.setSkill = 'JavaScript'

s2.setScore = 1
s2.setSkill = 'Planning'
s2.setSkill = 'Managing'
s2.setSkill = 'Organizing'

console.log(s1)

console.log(s1.saySomething())
console.log(s1.getFullName())
console.log(s1.getPersonInfo())

console.log(s2.saySomething())
console.log(s2.getFullName())
console.log(s2.getPersonInfo())
```

### 13. Document Object Model(DOM)

+ Reactでは、仮装DOMを操作して、必要な部分だけを更新する
  + index.htmlのみを描画する

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="utf-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1" />
    <title>React App</title>
  </head>

  <body>
    <!-- <div class="root"></div> -->
    <div id="root"></div>

    <script>
      // const root = document.querySelector('.root')
      // const root = document.getElementById('root')
      const root = document.querySelector('#root')
      root.innerHTML = <h1>Welcome to 30 Days Of React </h1>
    </script>
  </body>
</html>
```

## 疑問点

+ Arrayに対する各操作の計算量は、どうなっている?

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
