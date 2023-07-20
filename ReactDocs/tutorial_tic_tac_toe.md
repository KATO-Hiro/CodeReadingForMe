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

+ React DevToolsを使うと、Reactコンポーネントのpropsや状態を確認できる

## Completing the game

+ ここまでで、三目並べゲームの基本的な構成要素はすべて揃ったことに
+ 盤上に「X」と「O」を交互に配置して、勝者を決める

### Lifting state up

+ 各Squareコンポーネントはゲームの状態の一部を保持している。三目並べゲームの勝敗をチェックするためには、ボードは9つのSquareコンポーネントの状態を知る必要がある。
  + ゲームの状態を各Squareに保存するのではなく、親となるBoardコンポーネントに保存するのが最良の方法
  + propを渡すことで何を表示するかを各Squareに伝えることができる

+ Reactコンポーネントをリファクタリングする際、状態を親コンポーネントにリフティングすることはよくある

```jsx
// ...
export default function Board() {
  // 9つの要素を持つ配列を作成し、それぞれにnullを設定
  // useState()を呼び出すと、squaresステート変数が宣言され、その配列に初期値が設定される
  const [squares, setSquares] = useState(Array(9).fill(null));
  return (
    // ...
  );
}


// Boardコンポーネント全体
export default function Board() {
  const [squares, setSquares] = useState(Array(9).fill(null));
  return (
    <>
      <div className="board-row">
        <Square value={squares[0]} />
        <Square value={squares[1]} />
        <Square value={squares[2]} />
      </div>
      <div className="board-row">
        <Square value={squares[3]} />
        <Square value={squares[4]} />
        <Square value={squares[5]} />
      </div>
      <div className="board-row">
        <Square value={squares[6]} />
        <Square value={squares[7]} />
        <Square value={squares[8]} />
      </div>
    </>
  );
}
```

+ 子コンポーネントを親コンポーネントにリフトアップすることで盤面全体の状態を管理


// 以降から、急激に難易度が上がったような印象がある。

+ それぞれの正方形は、'X'、'O'、または空の正方形の場合はnullのいずれかの値プロップを受け取ります。

+ 次に、正方形がクリックされたときの処理を変更します。Board コンポーネントは、どのマスが塗りつぶされるかを管理します。正方形がボードの状態を更新する方法を作る必要があります。状態は、それを定義するコンポーネントのプライベートなので、Square から直接 Board の状態を更新することはできません。

+ その代わりに、Board コンポーネントから Square コンポーネントに関数を渡して、正方形がクリックされたときに Square がその関数を呼び出すようにします。まず、Square コンポーネントがクリックされたときに呼び出す関数から始めます。その関数をonSquareClickと呼びます：

```jsx
// 呼び出し先
// onSquareClickを追加
function Square({ value, onSquareClick }) {
  return (
    <button className="square" onClick={onSquareClick}>
      {value}
    </button>
  );
}

// 呼び出し元
export default function Board() {
  const [squares, setSquares] = useState(Array(9).fill(null));

  // イベントを定義
  function handleClick(i) {
    // 配列のコピーを作成
    const nextSquares = squares.slice();
    nextSquares[i] = "X";
    // setSquares関数を呼び出すことで、Reactはコンポーネントの状態が変更されたことを知ることができる。
    // JavaScript: クロージャをサポート。内側の関数が外側の変数・関数にアクセスできる
    setSquares(nextSquares);
  }

  return (
    <>
      <div className="board-row">
        // propsにonSquareClickを追加
        <Square value={squares[0]} onSquareClick={handleClick} />
        //...
  );
}


```

+ 上位のコンポーネントで状態を管理しておき、下位のコンポーネントでアクションに応じて状態を更新
  + イベントを上位から下位のコンポーネントに渡す
  + 子コンポーネントでイベントが発生したら、上位のコンポーネントの状態を更新するように要求

```jsx
// Not working
// 無限ループ。Board全体の再レンダリングとhandleClick(0)を繰り返す
// 関数が呼び出されている
<Square value={squares[0]} onSquareClick={handleClick(0)} />


// handleClick(i)を呼び出す関数をアロー関数で記述するとシンプルに書ける
export default function Board() {
  // ...
  return (
    <>
      <div className="board-row">
        // 四角がクリックされると、=>以降のコードが実行される
        <Square value={squares[0]} onSquareClick={() => handleClick(0)} />
        // ...
  );
}
```

+ ユーザーがボードの左上のマスをクリックし、そこにXを追加するときに何が起こるかを復習してみましょう：

+ 左上の正方形をクリックすると、ボタンがSquareからonClickプロップとして受け取った関数が実行されます。SquareコンポーネントはBoardからonSquareClickプロップとしてその関数を受け取りました。Boardコンポーネントはその関数をJSXで直接定義しました。この関数は、引数0を指定してhandleClickを呼び出します。
+ handleClickは、引数(0)を使って、squares配列の最初の要素をnullからXに更新します。
+ Boardコンポーネントのsquaresの状態が更新されたので、Boardとそのすべての子が再レンダリングされます。これにより、インデックス0を持つSquareコンポーネントのvalue propがnullからXに変更されます。
+ 最終的に、ユーザは左上の正方形をクリックした後、空からXに変わったことを確認します。

+ Reactでは、イベントを表すプロップにはonSomethingという名前を使い、それらのイベントを処理する関数定義にはhandleSomethingを使うのが一般的です。

### Why immutability is important

+ handleClickでは、既存の配列を変更する代わりに、.slice()を呼び出して四角形の配列のコピーを作成していることに注目
  + 不変性の説明
+ データを変更するには、一般的に2つのアプローチがある。第一のアプローチは、データの値を直接変更することによってデータを変異させることである。もう1つは、希望する変更を加えた新しいコピーでデータを置き換える方法です。
+ 結果は同じだが、変異（基礎となるデータの変更）を直接行わないことで、いくつかの利点
  + 複雑な機能の実装をより簡単に
    + 例: 以前のバージョンのデータをそのまま保持し、後で再利用
  + コンポーネントが自分のデータが変更されたかどうかを比較するのが非常に簡単に
    + デフォルトでは、親コンポーネントの状態が変更されると、すべての子コンポーネントが自動的に再レンダリングされます。これには、変更の影響を受けていない子コンポーネントも含まれます。
    + 再レンダリングは、それ自体はユーザーにとって目立つものではありませんが（積極的に避けようとしてはいけません！）、パフォーマンス上の理由から、明らかに影響を受けていないツリーの一部の再レンダリングをスキップしたい場合があります。

### Taking turns 

+ 初手はデフォルトで "X "に設定する。Boardコンポーネントにもう一つ状態を追加することで、これを記録
  + xIsNextを状態として持つ。初期値は、true
  + BoardコンポーネントのhandleClick()で、xIsNextがtrue/falseで場合分け
    + setXIsNext()で状態を更新
+ 同じマスをクリックすると、マスの値が反転してしまう
  + チュートリアルでは、状態を更新を許可しない方針で
  + handleClickで、squares[i]が既に更新されていたら、状態を変更しないようにする = returnを使う

### Declaring a winner 

+ プレーヤーが交代できるようになったので、ゲームに勝ち、もう交代する必要がなくなったときを表示するようにします。そのためには、calculateWinnerというヘルパー関数を追加します。この関数は、9マスの配列を受け取り、勝者かどうかをチェックし、必要に応じて'X'、'O'、またはnullを返します。calculateWinner関数はReact固有の関数ではないので、あまり気にしないでください：

+ calculateWinnerを定義するのは、Boardの前でも後でも構わない。コンポーネントを編集するたびにスクロールする必要がないように、最後に定義しましょう。

+ ボードコンポーネントのhandleClick関数でcalculateWinner(squares)を呼び出し、プレーヤーが勝ったかどうかをチェックします。このチェックは、ユーザーがすでに○か×のあるマスをクリックしたかどうかをチェックするのと同時に行うことができます：

+ ゲームの終了をプレイヤーに知らせるために、「勝者」のようなテキストを表示することができます：X "や "Winner：O".そのためには、ボードコンポーネントにステータスセクションを追加します。ステータスは、ゲームが終わっていれば勝者を表示し、ゲームが進行中であれば、次の手番を表示します：

## Adding time travel

+ 最後の練習として、ゲームの前の手に「過去に戻る」ことを可能にしてみよう。

### Storing a history of moves

+ 正方形の配列を変更していれば、タイムトラベルを実現するのは非常に難しくなる。

+ しかし、あなたはslice()を使って、一手ごとに新しい正方形配列のコピーを作成し、それを不変のものとして扱った。これにより、過去のすべてのバージョンの正方形配列を保存し、すでに起こったターンの間を移動することができる。

+ 過去のsquares配列は、historyという別の配列に格納し、それを新しいステート変数として格納する。history配列は、最初の手から最後の手までのすべての碁盤の状態を表し、次のような形をしています：

```js
[
  // Before first move
  [null, null, null, null, null, null, null, null, null],
  // After first move
  [null, null, null, null, 'X', null, null, null, null],
  // After second move
  [null, null, null, null, 'X', null, null, null, 'O'],
  // ...
]
```

### Lifting state up, again

+ 過去の手のリストを表示するために、Gameという新しいトップレベルコンポーネントを書きます。そこに、ゲーム全体の履歴を含む履歴ステートを配置します。

+ 履歴の状態をGameコンポーネントに置くことで、その子コンポーネントであるBoardコンポーネントから四角形の状態を取り除くことができます。ちょうど、SquareコンポーネントからBoardコンポーネントに "状態を持ち上げた "ように、今度は、BoardコンポーネントからトップレベルのGameコンポーネントに "状態を持ち上げる "のです。これにより、Game コンポーネントは、碁盤のデータを完全にコントロールできるようになり、履歴から前のターンをレンダリングするように碁盤に指示できるようになります。

+ まず、Gameコンポーネントをexport defaultで追加する。Board コンポーネントとマークアップをレンダリングさせます：

```jsx
function Board() {
  // ...
}

export default function Game() {
  return (
    <div className="game">
      <div className="game-board">
        <Board />
      </div>
      <div className="game-info">
        <ol>{/*TODO*/}</ol>
      </div>
    </div>
  );
}
```

+ function Board() {宣言の前のexport defaultキーワードを削除し、function Game() {宣言の前に追加していることに注意してください。これにより、index.js ファイルは、Board コンポーネントの代わりに Game コンポーネントをトップレベルのコンポーネントとして使用するようになります。Game コンポーネントによって返される追加の div は、後でボードに追加するゲーム情報のためのスペースを作っています。

+ Game コンポーネントに状態を追加して、どのプレーヤーが次なのか、手の履歴を追跡します：

```jsx
export default function Game() {
  const [xIsNext, setXIsNext] = useState(true);
  // Array(9).fill(null)]が1つの項目を持つ配列であり、それ自体が9つのnullの配列であることに注目してほしい。
  const [history, setHistory] = useState([Array(9).fill(null)]);
  // ...
```

+ 現在の手のマスをレンダリングするには、ヒストリーから最後のマスの配列を読み込みます。このためにuseStateは必要ありません。レンダリング中に計算するのに十分な情報をすでに持っているからです：

```jsx
export default function Game() {
  const [xIsNext, setXIsNext] = useState(true);
  const [history, setHistory] = useState([Array(9).fill(null)]);
  // 0-indexedと思えば良い?
  const currentSquares = history[history.length - 1];
```

+ 次に、ゲームを更新するためにボードコンポーネントによって呼び出されるGameコンポーネント内にhandlePlay関数を作成します。xIsNext、currentSquares、handlePlayを小道具としてBoardコンポーネントに渡します：

```jsx
export default function Game() {
  // Boardコンポーネントから引き上げ
  const [xIsNext, setXIsNext] = useState(true);
  const [history, setHistory] = useState([Array(9).fill(null)]);
  const currentSquares = history[history.length - 1];

  function handlePlay(nextSquares) {
    // TODO
  }

  return (
    <div className="game">
      <div className="game-board">
        // 状態や関数を子コンポーネントに渡す
        <Board xIsNext={xIsNext} squares={currentSquares} onPlay={handlePlay} />
        //...
  )
}
```

+ Boardコンポーネントが受け取る小道具によって完全に制御されるようにしましょう。xIsNext、squares、そしてBoardがプレイヤーが手を打ったときに更新されたsquares配列で呼び出す新しいonPlay関数です。次に、useStateを呼び出しているBoard関数の最初の2行を削除する：

```jsx
function Board({ xIsNext, squares, onPlay }) {
  function handleClick(i) {
    //...
  }
  // ...
}
```

+ ここで、Board コンポーネントの handleClick の setSquares と setXIsNext 呼び出しを、新しい onPlay 関数の呼び出しに置き換えて、ユーザーがマスをクリックしたときに Game コンポーネントが Board を更新できるようにします：

```jsx
function Board({ xIsNext, squares, onPlay }) {
  function handleClick(i) {
    if (calculateWinner(squares) || squares[i]) {
      return;
    }
    const nextSquares = squares.slice();
    if (xIsNext) {
      nextSquares[i] = "X";
    } else {
      nextSquares[i] = "O";
    }

    // 追加
    onPlay(nextSquares);
  }
  //...
}
```

+ Board コンポーネントは Game コンポーネントから渡される props によって完全に制御されます。ゲームを再び動かすには、GameコンポーネントにhandlePlay関数を実装する必要があります。

+ handlePlayが呼ばれたら何をすればいいのでしょうか？Boardは以前、更新された配列でsetSquaresを呼び出していたことを思い出してください。

+ handlePlay関数はGameの状態を更新して再レンダリングをトリガーする必要がありますが、setSquares関数はもう呼び出すことができません。更新されたsquares配列を新しいヒストリエントリとして追加することで、ヒストリを更新することになります。また、Boardが行っていたように、xIsNextをトグルすることもできます：

```jsx
export default function Game() {
  //...
  function handlePlay(nextSquares) {
    setHistory([...history, nextSquares]);
    setXIsNext(!xIsNext);
  }
  //...
}
```

+ ここで、[...history, nextSquares]は、historyのすべての項目とnextSquaresを含む新しい配列を作成します。(...history]の構文は、"historyのすべての項目を列挙する "と読むことができる）。

+ 例えば、historyが [[null,null,null], ["X",null,null]] で、nextSquaresが ["X",null, "O"] の場合、新しい [...history, nextSquares] 配列は [[null,null,null], ["X",null,null], ["X",null, "O"]] となります。

+ この時点で、ステートはGameコンポーネント内に移動し、UIはリファクタリング前と同じように完全に動作するはずです。この時点でのコードは以下のようになります：

### 抽象化

+ ある機能を追加するときの考え方
  + コンポーネント(空枠)を定義 + 現在のコンポーネントとの階層関係を確認
  + 必要な状態を列挙し、useStateで初期値をセット
  + イベントを扱う関数のみを定義
  + コンポーネント間で必要なデータ・関数を渡す
  + コンポーネントやイベントを扱う関数の中身を実装

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
