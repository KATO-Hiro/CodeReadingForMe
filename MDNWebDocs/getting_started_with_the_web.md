# Getting started with the Web

## 現状

- 作りたい Web app があり、API アクセスに関するお作法を知っておくべきだと感じた。

## 目標

- 公式 Doc を読んで標準的な API アクセスの方法を学ぶ。
- Web app から外部 API にアクセスできるようにする。
- 公式 Doc などの一次資料を読む習慣を身につける

## 技術選定の理由

- 。

- 。

## The story of your first website

- Installing basic software
- What will your website look like?
- Dealing with files
- HTML basics
- CSS basics
- JavaScript basics
- Publishing your website
- How the web works

## Client-side web APIs

### Introduction to web APIs

#### What are APIs?

- Application Programming Interfaces (APIs)
  - 開発者が、複雑な機能を簡単に作れるようにしてくれる
  - 複雑なコードを抽象化し、代わりに使いやすい構文を提供する

##### APIs in client-side JavaScript

2 種類に大別される

- Browser APIs
  - ブラウザに組み込まれている
  - ブラウザや周囲のコンピュータ環境からデータを公開する
- Third-party APIs
  - ブラウザにデフォルトで組み込まれていない
  - 自分で取得する必要がある

retrieve: (他動詞)取得する

##### Relationship between JavaScript, APIs, and other JavaScript tools

#### What can APIs do?

https://developer.mozilla.org/en-US/docs/Web/API

##### Common browser APIs

- APIs that fetch data from the server

  - サーバからデータを取得して、Web ページの一部を更新する API がよく使われている
  - サイトのパフォーマンスや動作に大きく影響する

- 使用ツール・技術
  - XMLHttpRequest、Fetch API
  - Ajax

##### Common third-party APIs

#### How do APIs work?

##### They are based on objects

- API が使用するデータ（オブジェクトのプロパティに含まれる）と、API が利用可能にする機能（オブジェクトのメソッドに含まれる）のコンテナとして機能する

##### They have recognizable entry points

##### They use events to handle changes in state

```js
// サンプル
// データの取得
let requestURL =
  "https://mdn.github.io/learning-area/javascript/oojs/json/superheroes.json";
let request = new XMLHttpRequest();
request.open("GET", requestURL);
request.responseType = "json";
request.send();

// responseの扱い
request.onload = function () {
  const superHeroes = request.response;
  populateHeader(superHeroes);
  showHeroes(superHeroes);
};
```

##### They have additional security mechanisms where appropriate

- 例えば、 same-origin policy
- リクエストに許可が必要な API もある

### Manipulating documents

- Document Object Model (DOM)

#### The important parts of a web browser

- Web ブラウザは複雑なソフトウェアであり、多くは JavaScript を使って制御・操作できない
- 上記の制限には正当な理由があり、そのほとんどがセキュリティを中心としたものである
- こうした制限にも関わらず、Web API は多くの機能を提供してくれる

- ドキュメント(ブラウザでは DOM)ウィンドウに読み込まれた実際のページであり、JavaScript では`Document`オブジェクトとして表される。このオブジェクトを使ってドキュメントを構成する HTML や CSS の情報を返したり、操作したりできる

#### The document object model

#### Active learning: Basic DOM manipulation

```js
// document.querySelector()で、要素を指定
// 上記の内容を変数に格納して、属性と新しい値を指定することで動的に更新できる
const link = document.querySelector("a");
link.textContent = "Mozilla Developer Network";
link.href = "https://developer.mozilla.org";
```

##### Creating and placing new nodes

- 新しい要素を追加できる
  - 追加したい場所の要素を指定
  - 新しい要素を作成
  - 指定した場所に追加

```js
// Grabbing a reference to our <section> element.
const sect = document.querySelector("section");
// Create a new paragraph.
const para = document.createElement("p");
para.textContent = "We hope you enjoyed the ride.";
// Append the new paragraph at the end of the section.
sect.appendChild(para);
// add a text node to the paragraph the link sits inside, to round off the sentence nicely.
const text = document.createTextNode(
  " — the premier source for web development knowledge."
);
const linkPara = document.querySelector("p");
linkPara.appendChild(text);
```

##### Moving and removing elements

- ノードを移動、削除できる

```js
// ノードそのものを削除
sect.removeChild(linkPara);

// ノードの子要素を削除
linkPara.remove();
```

##### Manipulating styles

- JavaScript に直接記述する方法と`<style></style>`の内部に記述する方法がある

```js
// object.style.attribute = 'hoge';で変更できる
para.style.color = "white";
para.style.backgroundColor = "black";
para.style.padding = "10px";
para.style.width = "250px";
para.style.textAlign = "center";
```

```html
<!-- <head>内に<style></style>を追加 -->
<head>
  <style>
    .highlight {
      color: white;
      background-color: black;
      padding: 10px;
      width: 250px;
      text-align: center;
    }
  </style>
</head>
<body>
  <script>
    ...
    // 追加
    para.setAttribute('class', 'highlight');
  </script>
</body>
```

#### Active learning: Getting useful information from the Window object

- Window オブジェクトから様々な情報を取り出せる
  - 例: ウィンドウの幅・高さ

#### Active learning: A dynamic shopping list

- 動的なショッピングリスト
  - アイテム一覧の表示
  - 各アイテムに対して、削除ボタンを追加
  - 新しいアイテムの入力は空欄にしておき、フォーカスを当てておく

### Fetching data from the server

#### What is the problem here?

- サーバーに Web サイトのリクエストを送ると、何も問題がなければ、Web ページを構成するアセットがダウンロードされ、ユーザーのコンピュータに表示される
- 問題点: ページの一部を更新したいときには、必ずページ全体を再度読み込む必要がある。ページが大きく複雑になってくると、ユーザーエクスペリエンスの低下を招きく。

##### Enter Ajax

- 小さなデータを必要なときに要求し、必要なときだけ表示する技術が生まれた
- サーバー上の特定のリソースに対して HTTP リクエストを行い、得られたデータを表示前に必要に応じてフォーマットすることで、ウェブページが直接処理できるようにする
  - 登場した頃は、Ajax (Asynchronous JavaScript and XML)と呼ばれていた
  - XMLHttpRequest、Fetch API など

#### A basic Ajax request

##### Fetch

- 非同期の HTTP リクエストに対応

###### So what is going on in the Fetch code?

##### Aside on promises

- Fetch()は、Promise を返す
- 長い promise チェーンが、フラットで読みやすくなる

```js
// 変更前
fetch(url).then(function (response) {
  response.text().then(function (text) {
    poemDisplay.textContent = text;
  });
});

// 変更後
fetch(url)
  .then(function (response) {
    // returnが必要
    return response.text();
  })
  .then(function (text) {
    poemDisplay.textContent = text;
  });
```

#### A more complex example

- API にアクセス
- 上記の結果がうまく行ったら、`.then()`に response が渡る
- json に変換
- 何らかの処理を行う
- `.catch()`でエラーハンドリング&ログを出力

```js
fetch("products.json")
  .then(function (response) {
    return response.json();
  })
  .then(function (json) {
    let products = json;
    initialize(products);
  })
  .catch(function (err) {
    console.log("Fetch problem: " + err.message);
  });
```

See:
https://developer.mozilla.org/en-US/docs/Learn/JavaScript/Client-side_web_APIs/Fetching_data

##### Challenge: An XHR version of the Can Store

### Third party APIs

===
今回の課題からは、関連が薄い start

### Drawing graphics

### Video and audio APIs

### Client-side storage

# end

## 疑問点

- ## Fetch を利用するときに、レスポンスエラーに対する処理はしなくてもいいのか?

- 。

## 感想

- 。

- 。

## 抽象化

- 。

- 。

## 応用

- 。

- 。

## See

https://developer.mozilla.org/en-US/docs/Learn/Getting_started_with_the_web
