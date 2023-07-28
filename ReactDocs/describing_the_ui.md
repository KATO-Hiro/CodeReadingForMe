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

+ JSXはHTMLによく似ているが、少し厳格で、動的な情報を表示できる
+ 既存のHTMLマークアップをReactコンポーネントに貼り付けても、常に機能するとは限らない

+ HTMLからJSXに変換するツールもある

#### JSX: Putting markup into JavaScript

+ これまで: コンテンツ(HTML、CSS)とロジック(JavaScript)が別々
+ Webがインタラクティブにになるにつれ、ロジックがコンテンツを決定することが多くなった
  + JavaScriptがHTMLを担当するようになった
  + Reactでは、レンダリングロジックとマークアップが同じ場所（コンポーネント）に同居
  + メリット: 関連のあるコンポーネントのロジックとマークアップを一緒にしておくことで、編集のたびに両者の同期が保たれる。直接関連のないコンポーネント同士は分離されている。
+ Note: JSXとReactは別物
  + JSX: シンタックスの拡張
  + React: JSライブラリ

#### The Rules of JSX

1. 単独のroot elementを返す。複数の要素で構成される場合は、parent tagでラップ
2. 全てのタグを閉じる
3. キャメルケース(cameCase)

+ JSXはJSになり、JSXで書かれた属性はJSオブジェクトのキーになる
  + JSの変数名の宣言に制限がある(ex: 変数名にダッシュやclassのような予約語は利用できない)
    + stroke-widthをstrokeWidthのように書き換え
    + classをclassName
    + 例外: aria-*属性とdata-*属性は歴史的な経緯からHTMLと同じ
+ 間違ったとしても、browserのコンソールに修正メッセージが表示される

```jsx
// 省略記法。Fragmentと呼ばれる。
// 理由: 内部的にはプレーンなJavaScriptオブジェクトに変換されている。関数から2つ(以上?)のオブジェクトを返すには、それらを配列にラップする必要がある。
<>
  // markup
  // <img>ではなく、<img />
  <img
    src="..."
    alt="Foo"
    className="photo"
  />
  <ul>
    <li>Foo</li>
  </ul>
</>
```

### JavaScript in JSX with curly braces (中括弧)

+ JavaScriptのロジックを追加、動的なプロパティを参照したい場合、JSXで中括弧を使用する

+ {}の内容は、関数呼び出しの内部を含めで、どんなところでも有効

+ JSX内部での利用方法
  + JSXタグの内部にテキストとして直接記述
  + 属性

```jsx
// OK
<h1>{name} 's TO DO List</h1>
// NG
<{tag}>Foo 's TO DO List</{tag}>


// OK
src={avatar}
// NG: 文字列として渡される
src="{avatar}"

// NG: 中括弧は以下のように連続して記述できない
src={A}{B}{C}
// OK: 文字列の場合は連結(or ヘルバー関数でURLを生成)してから中括弧で囲む
src={A + B + C}
```

#### Using “double curlies”: CSS and other objects in JSX

+ 元々{}を含む内容は、{{}}のように

```jsx
// 例: CSSの属性指定
// Note: 内部では、キャメルケース(camelCase)で記述
<ul style={{
  backgroundColor: 'black'
  color: 'pink'
}}>
</ul>
```

#### More fun with JavaScript objects and curly braces

```jsx
// 一つのオブジェクトから参照
// 定義
// 階層構造も可能
const person = {
  name: 'Gregorio Y. Zara',
  theme: {
    backgroundColor: 'black',
    color: 'pink'
  }
};

// 参照
// object.attributeの形式で
export default function TodoList() {
  return (
    <div style={person.theme}>
      <h1>{person.name}'s Todos</h1>
      <img
        className="avatar"
        src="https://i.imgur.com/7vQD0fPs.jpg"
        alt="Gregorio Y. Zara"
      />
      <ul>
        <li>Improve the videophone</li>
        <li>Prepare aeronautics lectures</li>
        <li>Work on the alcohol-fuelled engine</li>
      </ul>
    </div>
  );
}

// ヘルバー関数の参照と利用
export function getImageUrl(person) {
  return (

  );
}

// 参照
...
<img 
  ...
  src={getImageUrl(person)}
/>
...
```

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
