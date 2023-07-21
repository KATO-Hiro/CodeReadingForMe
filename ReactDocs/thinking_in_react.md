# Thinking in React

## 現状

+ 関数コンポーネントを使ったチュートリアルの三目並べを写経。

+ チュートリアルの後半は理解度を上げる必要がある。
  + コンポーネントのリフトアップを行う必要があるケース、状態と操作をどのコンポーネントで持つといいか?

+ 仕様を書き出して、そこからどのように考えるとよりReactらしいコードになるかを知る必要がある

## 目標

+ 新しいサービスで実現したい機能を実装できるように。

+ 。

## 技術選定の理由

+ 。

+ 。

## Thinking in React

+ Reactを使ってユーザーインターフェースを構築する場合、まずそれをコンポーネントと呼ばれる断片に分解する。次に、コンポーネントごとに異なる視覚的な状態を記述します。最後に、データがコンポーネント間を流れるようにコンポーネントを接続します。

+ チュートリアルでは、Reactを使って検索可能な商品データテーブルを構築する思考プロセスを説明

### Start with the mockup

+ JSON APIとデザイナーによるモックアップがあるとする。

```json
// 商品のカテゴリ、値段、在庫の有無、商品名
[
  { category: "Fruits", price: "$1", stocked: true, name: "Apple" },
  { category: "Fruits", price: "$1", stocked: true, name: "Dragonfruit" },
  { category: "Fruits", price: "$2", stocked: false, name: "Passionfruit" },
  { category: "Vegetables", price: "$2", stocked: true, name: "Spinach" },
  { category: "Vegetables", price: "$4", stocked: false, name: "Pumpkin" },
  { category: "Vegetables", price: "$1", stocked: true, name: "Peas" }
]
```

+ モックアップ
  + 検索用のテキストボックスが一番上にある
  + チェックボックスで、在庫がある場合のみ表示できるように
  + フルーツと野菜でカテゴリ分け
    + 名前と商品をそれぞれテーブル形式で表示
    + 在庫がない場合は、赤文字で表示

+ ReactでUIを実装するには、通常同じ5つのステップを踏むことになる。

### Step 1: Break the UI into a component hierarchy

## 疑問点

+ コンポーネントの最小単位はどれくらいにするか?

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
