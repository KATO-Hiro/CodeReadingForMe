# Tutorial: Tic-Tac-Toe

+ このチュートリアルでは、小さな三目並べゲームを作ります。このチュートリアルでは、既存のReactの知識を前提としません。このチュートリアルで学ぶテクニックは、あらゆるReactアプリを構築するための基本であり、これを完全に理解することでReactを深く理解することができます。

## 現状

+ Quick Startを写経した程度。サンプルを見てなんとなく理解した気になっているが、実際に書けるかどうかはかなり怪しい

+ 。

## 目標

+ チュートリアルを完走して、Reactの基本を理解できるようにする。

+ これから作るAppに関連が強い部分を重点的に学ぶ。

## 技術選定の理由

+ 。

+ 。

## チュートリアルの項目

+ チュートリアルのセットアップでは、チュートリアルに従うための出発点を与えます。
+ 概要では、Reactの基本であるコンポーネント、プロップ、ステートについて学びます。
+ ゲームを完成させることで、React開発で最も一般的なテクニックを学ぶことができます。
+ タイムトラベルを追加することで、Reactのユニークな強みをより深く理解することができます。

## What are you building?

+ 完成形のイメージ
+ 120行程度で、三目並べ + 操作を記録・復元

+ チュートリアルの目的は、Reactとその構文を理解すること

+ コードを写経する前に、実装が必要な要件・機能を列挙してみる
  + 盤面(マス)とマスの状態 + 空白のクリックしたら印がつく
    + 3×3マスを空白の状態で表示
    + クリックを検出して、手番に応じた印をつける
    + 既に印のついたマスはクリックしても何も起きないようにする
  + ターン制: mod 2で先手・後手が切り替わる
    + どのマスを選んだか記録
      + マスの番号を取り出す + 右側にボタンを追加
      + ボタンを推したら、その状態に戻る / 進む
      + // 難しそう
  + 勝敗の判定
    + 縦・横・斜めの3マスがどれか1種類の印なら、その印をマークした手番の勝利

## Setup for the tutorial

+ CodeSandboxでは、ブラウザ上でコードを書いたり、作成したアプリがユーザーにどのように表示されるかをプレビューすることができる。
+ ローカル環境を用意して、実行することもできる。

```js
// App.js

// 思っていた以上に少ないコードで正方形が描画されている
// 正方形 + 印
// CSSのsquareクラスで属性が指定されている
// JavaScriptのexportキーワード: 関数を関数が定義されたファイルの外からアクセスできるようにする
// defaultキーワード: 他のファイルに、関数が書かれているファイルのメイン関数であることを知らせる
export default function Square() {
  // property or propで、CSSボタンのスタイルを指定
  // HTMLとは違いclassnameではなく、classNameになっている
  return <button className="square">X</button>;
}
```

## Overview

+ App.js
  + コンポーネントを作成
    + Reactでは、コンポーネントはユーザーインターフェースの一部を表す再利用可能なコードの一部
    + アプリケーションのUI要素をレンダリング、管理、更新するために使用される
+ styles.css
  + App.jsファイルのコンポーネントのスタイルを定義
+ index.js
  + チュートリアル中にこのファイルを編集することはないが、App.jsファイルで作成したコンポーネントとウェブ・ブラウザーの橋渡しをする
  + 以下のコードには、必要な要素が揃っている
    + React
    + ウェブブラウザと対話するためのReactのライブラリ（React DOM）
    + コンポーネントのスタイル
    + App.jsで作成したコンポーネント

```js
// index.js
import { StrictMode } from 'react';
import { createRoot } from 'react-dom/client';
import './styles.css';

import App from './App';
```

+ ファイルの残りの部分は、すべての内容をまとめ、最終的な成果物をpublicフォルダーのindex.htmlに注入

### Building the board

+ 以降では、App.jsのみ扱う。

+ 現在、ボードは1マスしかありませんが、9マス必要です！コピー＆ペーストで2つの正方形を作る
  + シンタックスエラーの原因: Reactコンポーネントは、2つのボタンのように隣接する複数のJSX要素ではなく、単一のJSX要素を返す必要があります。
  + 解決方法: フラグメント（<>と</>）を使用して、隣接する複数のJSX要素をこのようにラップ

```jsx
export default function Square() {
  return (
    <>
      <button className="square">X</button>
      <button className="square">X</button>
    </>
  );
}
```

+ ボードを9マス用意
  + JSX elementをコピー
  + グリッドにするため、divでマスをグループ化 + CSSを追加

+ コンポーネント名をSquareからBoardへ
  + 内容に即した名前をつける
  + 大文字 + 小文字

### Passing data through props

+ ユーザーがマスをクリックしたときに、マスの値を空から "X "に変更
+ 9マスのコードをコピペで作ったが、Reactのコンポーネントアーキテクチャを使えば、再利用可能なコンポーネントを作成できるので、重複を回避できる

+ // コードを書きながら、リファクタリング

```jsx
// 子コンポーネントの定義
// propsを使って、親コンポーネントから子コンポーネントに値を渡す
// { hoge }の形式で記述
function Square({ value }) {
  // JavaScriptのコードをエスケープする場合は{}を使う
  return <button className="square">{value}</button>;
}

// 親コンポーネントから子コンポーネントの呼び出し
<ConponentName attr="" />
```

### Making an interactive component 

+ SquareコンポーネントをクリックしたらXで塗りつぶす

+ Squareの内部にhandleClickという関数を宣言する
+ そして、Squareから返されたボタンJSX要素のpropsにonClickを追加する

```jsx
function Square({ value }) {
  // 動作させたい内容を定義
  function handleClick() {
    console.log('clicked!');
  }

  return (
    <button
      className="square"
      // 関数名のみ{}でラップして呼び出し
      onClick={handleClick}
    >
      {value}
    </button>
  );
}
```

+ Squareコンポーネントがクリックされたことを「記憶」し、「X」マークで塗りつぶす
  + useStateを使い、コンポーネントから呼び出すことで実現できる
  + クリック時に変更するように

```jsx
// useStateをインポート
// Pythonとimport, fromの順番が逆になっている
// Q: import { useState }としているのはなせ?
import { useState } from 'react';

function Square() {
  // const [hoo, setHoo] = useState(initial_value);の形式で
  const [value, setValue] = useState(null);

  function handleClick() {
    //...

```

```jsx
function Square() {
  const [value, setValue] = useState(null);

  function handleClick() {
    // クリックしたときの動作を定義
    // Q: 関数として扱っているように見えるが・・・
    setValue('X');
  }

  return (
    <button
      className="square"
      onClick={handleClick}
    >
      {value}
    </button>
  );
}
```

+ 各Squareはそれぞれ独自の状態を持つ。各Squareに格納された値は、他のSquareから完全に独立している。コンポーネントでset関数を呼び出すと、Reactは自動的に内部の子コンポーネントも更新する。

+ 抽象化
  + イベント(動作させたい要素)を該当するコンポーネントで定義
    + 関数とhandleActionName()
    + JSX要素のpropsに渡す
    + 値を記憶させたいときは、useStateをimportして、初期値とイベントに応じて更新する方法を記述

### React Developer Tools


## 項目

## 疑問点

+ React DOMとは?
  + DOMそのものをあまり理解できていないかも?

+ 。

## 感想

+ 。

+ 。

## 抽象化

+ チュートリアル
  + 目的
  + 構成要素
  + 完成系のイメージ

+ 。

## 応用

+ 。

+ 。

## See

https://react.dev/learn/tutorial-tic-tac-toe
