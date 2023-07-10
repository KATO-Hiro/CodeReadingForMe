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
      // 以下にマークアップ
      <h1>About</h1>
      <p>Hello there.<br />How do you do?</p>
    </>
  )
}
```

## Adding styles 

+ CSSクラスclassName = HTMLのclass属性と同じ

```jsx
// classNameと「N」が大文字になっている
<img className="avatar" />
```

+ CSSの内容は、別ファイルに記述できる
+ Reactでは、CSSファイルの追加方法を規定していない。HTMLに<link>タグを追加するか、ビルドツールやFWのドキュメントを参照。

```css
.avatar {
  boarder-radius: 50%;
}
```

## Displaying data

+ JavaScriptにマークアップを入れることができる。中括弧を使うと、JavaScriptに「エスケープバック」して、コードから変数を埋め込み、ユーザーに表示することができる

```jsx
return (
  <h1>
    // {hogehoge}の形式で、hogehogeに変数を埋め込む
    {user.name}
  </h1>
);
```

+ JSX属性から「JavaScriptにエスケープ」することもできるが、引用符の代わりに中括弧を使わなければならない。例えば、className="avatar "はCSSクラスとして "avatar "文字列を渡すが、src={user.imageUrl}はJavaScriptのuser.imageUrl変数値を読み込み、その値をsrc属性として渡す。

```jsx
return (
  // Q: 複数の要素があっても、改行で区別している?
  // hogeFugaのような形式で記述している
  <imge 
    className="avatar"
    src={user.imageUrl}
  />
);
```

+ style={{}}は特別な構文ではなく、style={ } JSX中括弧内の通常の{}オブジェクト。style属性は、スタイルがJavaScript変数に依存する場合に使用できる。

```jsx
const user = {
  name: 'Hedy Lamarr !',
  imageUrl: 'https://i.imgur.com/yXOvdOSs.jpg',
  imageSize: 90,
};

export default function Profile() {
  return (
    <>
      <h1>{user.name}</h1>
      // imgタグ + URLを指定することで、画像を表示できる
      // userオブジェクト?で、属性をまとめておける
      <img
        className="avatar"
        src={user.imageUrl}
        alt={'Photo of ' + user.name}
        style={{
          width: user.imageSize,
          height: user.imageSize
        }}
      />
    </>
  );
}
```

## Conditional rendering 

+ 条件を記述するための特別な構文はない。その代わり、通常のJavaScriptコードを書くときと同じテクニックを使う。

```jsx
// ifステートメントの例
let content;

if (isLoggedIn) {
  // 変数にコンポーネント入れられる
  content = <AdminPanel />;
} else {
  content = <LoginForm />;
}

return (
  <>
    {content}
  </>
)


// ?演算子
<>
  {isLoggedIn ? (
    <AdminPanel />
  ) : (
    <LoginForm />
  )}
</>


// elseが不要な場合は、 &&を使う
<>
  {isLoggedIn && <AdminPanel />}
</>
```

## Rendering lists

+ コンポーネントのリストをレンダリングするには、forループや配列map()関数のようなJavaScriptの機能を利用する。

```js
// サンプル
const products = [
  { title: 'Cabbage', id: 1 },
  { title: 'Garlic', id: 2 },
  { title: 'Apple', id: 3 },
];


// map + アロー関数を使用
// 一つずつ要素を取り出して処理
const listItems = products.map(product => 
  // キー属性を持っている。項目の追加・削除・並び替えのときに一意に識別するために利用。
  <li key={product.id}>
    {product.title}
  </li>
);

return (
  <ul>{ListItems}</ul>
)
```

## Responding to events 

+ コンポーネント内でイベント・ハンドラ関数を宣言することで、イベントに応答できる

```jsx
function MyButton() {
  function handleClick() {
    alert('You clicked me!');
  }

  return (
    // onClick={handleClick}の最後に括弧がない = Reactは、ユーザーがボタンをクリックしたときにイベントハンドラを呼び出す
    // 関数を渡していると理解すれば良いか?
    <button onClick={handleClick}>
      Click me
    </button>
  )
}
```

## Updating the screen 

+ コンポーネントに何らかの情報を「記憶」させ、それを表示させたい場合がある。例えば、ボタンがクリックされた回数を数えたいとする。

```jsx
// React Hookをインポート
import { useState } from 'react';

function MyButton() {
  // 現在の状態、それを更新する関数
  // [something, setSomething]と書くのが慣例
  // 初期値には0がセットされる
  const [count, setCount] = useState(0);

  // 状態を変更したい場合は、setCount() を呼び出し、新しい値を渡す。このボタンをクリックすると、カウンタがインクリメントされる。
  function handleClick() {
    setCount(count + 1);
  }

  return (
    <button onClick={handleClick}>
      Clicked {count} times
    </button>
  );
}

// 同じコンポーネントを複数個レンダリングすると、それぞれが独自の状態を取得。
// Note: 各ボタンが自身のカウント状態を「記憶」し、他のボタンに影響を与えない
import { useState } from 'react';

export default function MyApp() {
  return (
    <div>
      <h1>Counters that update separately</h1>
      <MyButton />
      <MyButton />
    </div>
  );
}

function MyButton() {
  const [count, setCount] = useState(0);

  function handleClick() {
    setCount(count + 1);
  }

  return (
    <button onClick={handleClick}>
      Clicked {count} times
    </button>
  );
}
```

## Using Hooks 

+ useで始まる関数はHooksと呼ばれる。useStateはReactが提供する組み込みHooks。他の組み込みHookはAPIリファレンスにある。また、既存のHookを組み合わせて独自のHookを書くこともできる。
+ Hookは他の関数よりも制約が多い。フックを呼び出せるのは、コンポーネント（または他のフック）の先頭だけ。条件やループの中でuseStateを使いたい場合は、新しいコンポーネントを取り出してそこに置く。

## Sharing data between components 

+ 多くの場合、コンポーネントはデータを共有し、常に一緒に更新する必要がある。

+ 両方のMyButtonコンポーネントが同じカウントを表示し、一緒に更新されるようにするには、個々のボタンの状態を、それらをすべて含む最も近いコンポーネントに「上へ」移動させる必要がある。

```jsx
export default function MyApp() {
  const [count, setCount] = useState(0);

  function handleClick() {
    setCount(count + 1);
  }

  return (
    <div>
      <h1>Counters that update together</h1>
      // 渡す情報をpropsと呼ぶ
      <MyButton count={count} onClick={handleClick} />
      <MyButton count={count} onClick={handleClick} />
    </div>
  );
}

// 呼び出される側
// {value1, value2, ...}
function MyButton({ count, onClick }) {
  return (
    <button onClick={onClick}>
      Clicked {count} times
    </button>
  );
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
