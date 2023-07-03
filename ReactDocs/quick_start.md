# Quick Start

+ 普段の開発で使う80%の概念を学ぶことができる

## 現状

+ 2年くらい前にReactを少し学習して、小さなappを作った程度。
  + なんとなく書いてできた気になっていた。

+ 。

## 目標

+ Reactのよく使う機能を短期間でキャッチアップする。

+ Server Componentsが何かを知り、次のプロジェクトで使うかどうか判断できるようにする。

## 技術選定の理由

+ UIを動的に更新したい。jQueryでは辛みがある。

+ 。

## 学ぶ内容

+ コンポーネントの作り方、ネストのさせ方
+ マークアップとスタイルの追加
+ データの表示方法
+ 条件やリストの表示方法
+ イベントに応答して画面を更新する方法
+ コンポーネント間でデータを共有

## Creating and nesting components

+ Reactアプリはコンポーネントで構成される
+ コンポーネントとは、独自のロジックと外観を持つUI（ユーザーインターフェース）の一部
  + ボタンのような小さなものから、ページ全体のような大きなものまである
  + JavaScriptの関数で、マークアップを返す

```js
// コンポーネントの宣言
function MyButton() {
  return (
    <button>I'm a button</button>
  );
}

// 宣言したコンポーネントを別のコンポーネントにネストできる
// コンポーネント: 常に大文字で始まる。
// HTMLタ: 小文字
export default function MyApp() {
    return (
        <div>
            <h1>Welcome to my app</h1>
            // <ComonentName />で利用できる。HTMLタグのように2回書かなくても良さそう。
            <MyButton />
        </div>
    )
}
```

## Writing markup with JSX 

+ 上記のマークアップ構文はJSXと呼ばれる。
+ JSX
  + HTMLより厳格
  + <br />のようにタグを閉じる必要がある
  + コンポーネントは複数のJSXタグを返すことはできない
  + <>...</>ラッパーでラップする必要がある

```jsx
function Aboutpage() {
  return (
    <>
      <h1>About</h1>
      <p>Hello there.<br />How do you do?</p>
    </>
  )
}
```


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
