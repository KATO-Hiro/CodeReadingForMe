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
