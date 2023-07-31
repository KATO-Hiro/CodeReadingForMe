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

```jsx
// 親コンポーネントから要素を渡す
export default function Profile() {
  return (
    <Avatar
      person={{ name: 'Lin Lanying', imageId: '1bX5QH6' }}
      size={100}
    />
  );
}

// 子コンポーネントで受け取り
// {}で受け取る要素を囲む
function Avatar({ person, size }) {
  // person and size are available here
}


// propsオブジェクトとして受け取り
// 必要な要素のみ、props.fooの形式で取り出している
function Avatar(props) {
  let person = props.person;
  let size = props.size;
  // ...
}
```

#### Specifying a default value for a prop

```jsx
// デフォルト値を指定できる
function Avatar({ person, size = 100 }) {
  // ...
}


// 呼び出し元
// ...を指定することで、sizeには100がセットされる
<Avatar person={...} />
```

#### Forwarding props with the JSX spread syntax

```jsx
// 冗長な記述
function Profile({ person, size, isSepia, thickBorder }) {
  return (
    <div className="card">
      <Avatar
        person={person}
        size={size}
        isSepia={isSepia}
        thickBorder={thickBorder}
      />
    </div>
  );
}


// 該当コンポーネントがpropsを直接使用しない場合、より簡潔な "spread "構文を使用
// ...propsを書くだけ
function Profile(props) {
  return (
    <div className="card">
      <Avatar {...props} />
    </div>
  );
}
```

#### Passing JSX as children

+ コンポーネントをネストした状態にしたいときには、children propを使う
  + 任意のJSXで親コンポーネントによって「埋められる」「穴」を持っていると考えられる
  + 例: パネル、グリッドなどのビジュアルラッパーによく使われる

```jsx
<Card>
  <Avatar />
</Card>


// 呼び出し先
// children propとして受け取る + 子要素が何を描画しているか知る必要はない
function Card({ children }) {
  return (
    <div className="card">
      {children}
    </div>
  );
}
```

#### How props change over time

```jsx
// コンポーネントが時間とともに異なる値を受け取る可能性がある
// propはimmutable(変更不可能)であるため、変更しないように。変更する場合は、状態を設定する必要がある
// 親コンポーネントのコードは、ステートを使用しているため省略
export default function Clock({ color, time }) {
  return (
    <h1 style={{ color: "blue" }}>
      {time}
    </h1>
  );
}
```

```jsx
import { getImageUrl } from './utils.js';

function Profile(props) {
  const name = props.name;
  const imageUrl = props.imageUrl;
  const profession = props.profession;
  const awardsCount = props.awardsCount;
  const awards = props.awards;
  const discovered = props.discovered;

  return (
    <>
      <section className="profile">
        <h2>{name}</h2>
        <img
          className="avatar"
          src={getImageUrl(imageUrl)}
          alt={name}
          width={70}
          height={70}
        />
        <ul>
          <li>
            <b>Profession: </b> 
            {profession}
          </li>
          <li>
            <b>Awards: {awardsCount} </b> 
            {awards}
          </li>
          <li>
            <b>Discovered: </b>
            {discovered}
          </li>
        </ul>
      </section>
    </>
  )
}

export default function Gallery() {
  return (
    <div>
      <h1>Notable Scientists</h1>
      <Profile
        name = "Maria Skłodowska-Curie"
        imageUrl = "szV5sdG"
        profession = "physicist and chemist"
        awardsCount = "4"
        awards = "(Nobel Prize in Physics, Nobel Prize in Chemistry, Davy Medal, Matteucci Medal)"
        discovered = "polonium (element)"
      />
      <Profile
        name = "Katsuko Saruhashi"
        imageUrl = "YfeOqp2"
        profession = "geochemist"
        awardsCount = "2"
        awards = "(Miyake Prize for geochemistry, Tanaka Prize)"
        discovered = "a method for measuring carbon dioxide in seawater"
      />
    </div>
  );
}

```

### Conditional rendering

+ 異なる条件によって異なるものを表示するときは、if文、&&、? : 演算子などのJavaScript構文を使用

```jsx
// 条件で異なるコンポーネントを表示
// if / elseステートメント
function Item({ name, isPacked }) {
  if (isPacked) {
    return <li className="item">{name} ✔</li>;
  }
  return <li className="item">{name}</li>;
}

export default function PackingList() {
  return (
    <section>
      <h1>Sally Ride's Packing List</h1>
      <ul>
        <Item 
          isPacked={true} 
          name="Space suit" 
        />
        <Item 
          isPacked={true} 
          name="Helmet with a golden leaf" 
        />
        <Item 
          isPacked={false} 
          name="Photo of Tam" 
        />
      </ul>
    </section>
  );
}


// 三項演算子を使って、違う部分だけ書き換え
// 条件 ? true : false
// Note: JSX要素は内部の状態を保持せず、実際のDOMノードでもないため、インスタンスではない
return (
  <li className="item">
    {isPacked ? name + ' ✔' : name}
  </li>
)

// 別のHTML tagでラップすることもできる
// Note: 条件が多い場合は、子コンポーネントに抽出することを検討
function Item({ name, isPacked }) {
  return (
    <li className="item">
      {isPacked ? (
        <del>
          {name + ' ✔'}
        </del>
      ) : (
        name
      )}
    </li>
  );
}


// &&演算子
// cond && hogeで、cond = trueのときにhogeを表示
// {}は別に用意する必要がある
// Note: &&の左側に数字を書くと、数字が描画されるので注意
return (
  <li className="item">
    {name} {isPacked && '✔'}
  </li>
);


// 変数を宣言して使う
function Item({ name, isPacked }) {
  let itemContent = name;

  // 条件を満たす場合のみ変更
  if (isPacked) {
    itemContent = name + " ✔";
  }
  return (
    <li className="item">
      {itemContent}
    </li>
  );
}
```

### Rendering lists

+ データの集合から、複数の類似したコンポーネントを表示
  + JavaScriptのfilter()やmap()を使う

#### Rendering data from arrays 

+ インタフェースを構築する際には、異なるデータを使って同じコンポーネントの複数のインスタンスを表示する必要があることがよくあります
+ このような状況では、JavaScriptのオブジェクトや配列にデータを格納し、map()やfilter()のようなメソッドを使用して、そこからコンポーネントのリストをレンダリングすることができます

```html
<ul>
  <li>Creola Katherine Johnson: mathematician</li>
  <li>Mario José Molina-Pasquel Henríquez: chemist</li>
  <li>Mohammad Abdus Salam: physicist</li>
  <li>Percy Lavon Julian: chemist</li>
  <li>Subrahmanyan Chandrasekhar: astrophysicist</li>
</ul>
```

1. 配列にデータを移動させる
2. 要素をJSXノードの配列として追加
3. コンポーネントに追加

```jsx
// step1
const people = [
  'Creola Katherine Johnson: mathematician',
  'Mario José Molina-Pasquel Henríquez: chemist',
  'Mohammad Abdus Salam: physicist',
  'Percy Lavon Julian: chemist',
  'Subrahmanyan Chandrasekhar: astrophysicist'
];

// step2
// 一つずつ取り出して処理
const listItems = people.map(person => <li>{person}</li>)

// step3
return <ul>{listItems}<ul>;
```

#### Filtering arrays of items 

```jsx
import { people } from './data.js';
import { getImageUrl } from './utils.js';

export default function List() {
  // 1. filterで条件に一致するものだけ取り出す
  const chemists = people.filter(person =>
    person.profession === 'chemist'
  );
  // 2. 各要素に対して処理
  const listItems = chemists.map(person =>
    // Note: map()呼び出しの中に直接あるJSX要素は、常にキーが必要です！
    // 要素の移動・追加・削除された場合に重要になるため
    // キーはデータに含めるようにする
    <li key={person.id}>
      <img
        src={getImageUrl(person)}
        alt={person.name}
      />
      <p>
        <b>{person.name}:</b>
        {' ' + person.profession + ' '}
        known for {person.accomplishment}
      </p>
    </li>
  );

  // 要素を返す
  return <ul>{listItems}</ul>;
}
```

##### Where to get your key

+ DB: ユニークkeyを使う
+ ローカルで生成されたデータ: インクリメントカウンタ、 crypto.randomUUID()、または uuid のようなパッケージを使用

##### Rules of keys 

+ キーは兄弟間で一意でなければなりません。ただし、異なる配列のJSXノードに同じキーを使ってもかまいません。
+ キーは変更してはいけません！レンダリング中にキーを生成しないでください。

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
