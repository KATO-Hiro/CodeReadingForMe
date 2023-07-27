# Describing the UI

## 現状

+ 関数コンポーネントを使ったチュートリアルを写経・雰囲気で理解したつもり。

+ 自力で書けるようになっているかはかなり怪しい。

## 目標

+ 次期プロジェクトに必要な部分を選択・理解して、実装できるようにする
  + 理解が浅い・分かっていない部分を重点的に

+ 。

## 技術選定の理由

+ 。

+ 。

## Describing the UI

+ Reactコンポーネントの作成、カスタマイズ、条件付き表示を学ぶ

+ 章タイトル
  + 最初のReactコンポーネントの書き方
  + マルチコンポーネント・ファイルを作成するタイミングと方法
  + JSXを使ってJavaScriptにマークアップを追加する方法
  + JSXで中括弧を使用してコンポーネントからJavaScriptの機能にアクセスする方法
  + propsを使ってコンポーネントを構成する方法
  + コンポーネントを条件付きでレンダリングする方法
  + 一度に複数のコンポーネントをレンダリングする方法
  + コンポーネントを純粋な状態に保つことで、混乱を招くバグを回避する方法

### Your first component

+ Galleryコンポーネントが3つのProfileコンポーネントをレンダリング

```jsx
function Profile() {
  return (
    <img src="https://i.imgur.com/MK3eW3As.jpg" alt="Katherine Johnson"></img>
  );
}

// 最上位のコンポーネント
// main function
// コンポーネントは、キャピタルケースで始める必要がある
export default function Gallery() {
  return (
    <section>
      <h1>Amazing scientists</h1>
      <Profile />
      <Profile />
      <Profile />
    </section>
  );
}
```

+ コンポーネント: 組み合わせ、並び替え、入れ子にして、ページ全体をデザインできる
+ コンポーネントの定義
  1. コンポーネントをexport
  2. 関数を定義
  3. マークアップを追加(return (<> </>)でラップする必要がある)
+ 各コンポーネントをトップレベルで記述

### Importing and exporting components

+ コンポーネントが多い場合は、個別のファイルに分割して、インポート

```js
// Pythonとは書き方が逆になっている
import ComponentName from './ComponentName.js';
```

+ コンポーネントの移動手順
  1. コンポーネントを入れる新しいJSファイルを作成
  2. そのファイルから関数コンポーネントをエクスポート(デフォルト・エクスポートまたは名前付きエクスポートのいずれか)
  3. コンポーネントを使用するファイルにインポート

```js
// Foo.js
function Hoge() {
    return (
        <>
            // TODO: Write markup here!
        </>
    )
}

export default function Foo() {
    return (
        <>
            <Hoge />
        </>
    )   
}

// App.js
// Hogeコンポーネントを含むコンポーネントのインポートだけでなく、Hogeコンポーネントそののものもインポートも同時にできる

// Note: './Foo'でも動作するが、以下の方がネイティブのESモジュールの動作に近い
// Q: 上記の意味する内容は? 
import Foo from './Foo.js'

// Named importの場合
// default keywordを使っていない場合は、以下のように書く
import { Hoge } from './Foo.js'

export default function App() {
    return (
        <Foo />
    );
}
```

+ デフォルト・エクスポートは1つのファイルに1つまで、名前付きエクスポートはいくつでもok

### Writing markup with JSX

+ JSXはHTMLによく似ていますが、少し厳格で、動的な情報を表示できる
+ 既存のHTMLマークアップをReactコンポーネントに貼り付けても、常に機能するとは限らない

+ HTMLからJSXに変換するツールもある

### JavaScript in JSX with curly braces (中括弧)

+ JavaScriptのロジックを追加、動的なプロパティを参照したい場合、JSXで中括弧を使用する

### Passing props to a component

+ propsはオブジェクト、配列、関数、さらにはJSXなど、あらゆるJavaScriptの値を渡すことができる

### Conditional rendering

+ 異なる条件によって異なるものを表示するときは、if文、&&、? : 演算子などのJavaScript構文を使用

### Rendering lists

+ データの集合から、複数の類似したコンポーネントを表示
  + JavaScriptのfilter()やmap()を使う

### Keeping components pure

+ 純粋な関数
  + 呼び出される前に存在していたオブジェクトや変数を変更することはない
  + 同じ入力が与えられた場合、純粋関数は常に同じ結果を返すべき
+ メリット
  + コードベースが大きくなるにつれて、不可解なバグや予測不可能な動作を避けることができる
+ propsを使う

## 疑問点

+ 。

+ 。

## 感想

+ 。

+ 。

## 抽象化

+ 章構成
  + 主要トピックの概要を手短に
  + 関心のある部分を重点的に学習できるようになっている

+ 。

## 応用

+ 。

+ 。

## See
