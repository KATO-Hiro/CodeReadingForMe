# 【Svelte + TypeScript + tailwindcss】本検索サイト チュートリアル

## 現状

+ Svelteの基本編のチュートリアルを写経
+ staticなページは作れる
+ 動的に更新する部分があまり理解できていない

## 目標

+ SvelteKitでWebAPIを利用したCRUDを実装できるようにする

+ 。

## 技術選定の理由

+ 。

+ 。

## ch4までスキップ

+ 最初のコンポーネントは作れるはず
+ SvelteKitを使えば、ルーティングライブラリの導入は不要に

## ch5　本を探すページの実装 - Repository

+ WebAPIの利用にあたって、APIとの通信をRepositoryFactoryパターンを使って抽象化
  + 単体テストをしやすく
  + モックに処理を置き換えやすく
  + ストアをスリムに
  + 再利用しやすく
  + エンドポイントが変わっても、変更箇所を少なく

### httpClientの作成

+ HttpClientをラップするモジュールを作成

```ts
// src/repositories/httpClients.ts

// axiosの場合
// Httpクライアントライブラリを事前にインストール
import axios from 'axios'

// 場合によってはheadersなども設定
const httpClient = axios.create({
  baseURL: 'https://www.hogehoge.com/foo/v1/bars'
})

// Q: export constにしないのはなぜ?
// Q: インスタンスを返す = 以下の形式?
export { httpClient }
```

### Book Repositoryの作成

+ モデルごとにRepositoryを作成し、以下の3種類のファイルを作成

```ts
// src/repositories/book // モデル名に変える
// types.ts
// モデルに関する型定義、Repositoryのインターフェースを提供
// Repository自体のインターフェースを公開することで、モックRepositoryに置き換えるときも実装の詳細を抽象化できる

// APIのレスポンス
export interface Result {
  items: FooItem;
  ...
}

// 各要素の情報
export interface FooItem {
  id: string;
  bar: hoge;
  ...
}

// クエリパラメータ
export interface Params {
  q: string;
  startIndex?: number // 何に使う?
}

// APIに対する操作の定義
export interface FooRepositoryInterface {
  get(params: Params): Promise<Result>;
  find(id: string): Promise<FooItem>;
}


// BookRepository.ts
// FooRepositoryInterfaceを継承して、実装の詳細を記述
import type { FooItem, FooRepositoryInterface, Params, Result } from './types'
import { httpClient } from '../httpClient'

export class FooRepository implements FooRepositoryInterface {
  async get(params: Params) {
    const { data } = await httpClient.get<Result>('/', {params})
    return data
  }

  async find(id: string) {
    const { data } = await httpClient.get<FooItem>(`/${id}`})
    return data
  }
}

// index.ts
// 作成したモジュールを一つにまとめてエクスポート
// Q: なぜ、このように記述する必要がある?
export * from './types'
export * from './FooRepository'
```

### Repository Factoryの作成

+ 作成したRepositoryはすべてRepository Factoryからインポートして利用する

```ts
// src/repositories/RepositoryFactory.ts
import { FooRepository, FooRepositoryInterface } from './foo'

export const FOO = Symbol('foo') // Q: Symbolとは? 定数をより強化したものと考えれば良い?

export interface Repositories {
  [FOO]: FooRepositoryInterface;
}

export default {
  [FOO]: new FooRepository()
} as Repositories

// test環境でモックデータを返す
const isMock = process.env.NODE_ENV === 'test'

export default {
  [FOO]: isMock ? new MockFooRepository() : new FooRepository()
} as Repositories

// Foo Repositoryを利用する方法
import RepositoryFactory { FOO } from '../repositories/RepositoryFactory'

const FooRepository = RepositoryFactory[FOO]
const foo = async FooRepository.get(id)
```

## ch6 本を探すページの実装 - SearchBar コンポーネント

### SearchBar コンポーネントの作成

```svelte
<!-- src/components/SearchBar.svelte -->
<div class="shadow flex">
  <input class="w-full rounded p-2" type="text" placeholder="Search...">
  <button class="bg-white w-auto flex justify-end items-center text-blue-500 p-2 hover:text-blue-400">
    <svg fill="none" stroke="currentColor" stroke-linecap="round" stroke-linejoin="round" stroke-width="2" viewBox="0 0 24 24" class="w-6 h-6">
      <path d="M21 21l-6-6m2-5a7 7 0 11-14 0 7 7 0 0114 0z"></path>
    </svg>
  </button>
</div>


<!-- pages/foo.svelte -->
<script lang="ts">
  import SearchBar from '../components/SearchBar.svelte'
</script>

<!-- formで囲むだけ -->
<form>
  <SearchBar />
</from>
```

### 値のバインディング

+ 親子コンポーネント間で値を受け渡し
  + 子コンポーネントでexport let foo;を定義
  + 親コンポーネントでlet bar = "hoge"と初期値を設定
  + bindを使ってvalueを双方向でやりとりできるようにつなぐ

```svelte
<!-- src/components/SearchBar.svelte -->
<script lang="ts">
+  export let value = '' // propsが渡されなかった時の初期値を定義できます。
</script>

<div class="shadow flex">
-  <input class="w-full rounded p-2" type="text" placeholder="Search...">
// 値を受け取り + 子コンポーネントで入力が行われたときに親に値を渡せるようにする
+  <input bind:value class="w-full rounded p-2" type="text" placeholder="Search...">
  <button class="bg-white w-auto flex justify-end items-center text-blue-500 p-2 hover:text-blue-400">
    <svg fill="none" stroke="currentColor" stroke-linecap="round" stroke-linejoin="round" stroke-width="2" viewBox="0 0 24 24" class="w-6 h-6">
      <path d="M21 21l-6-6m2-5a7 7 0 11-14 0 7 7 0 0114 0z"></path>
    </svg>
  </button>
</div>


// 親コンポーネント
<script lang="ts">
  import SearchBar from '../components/SearchBar.svelte'

  // 初期値を指定
  let q = 'foo'
</script>

<form>
  // 親コンポーネントから渡す
  // 子コンポーネントで発生したイベントを親コンポーネントへフォワーディング
  - <SearchBar value={q} />
  + <SearchBar bind:value={q} />
</form>

// 表示
<div class="text-center mt-4">
  {q}
</div>
```

// bindの使い方がようやく少しだけ理解できた気がする

## ch7 本を探すページの実装 - 本一覧の取得

## 項目

## 疑問点

+ SvelteKitを利用すれば、RepositoryFactoryパターンは不要?
  + APIとの通信はFWでも行うので必要では?

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

https://zenn.dev/azukiazusa/books/9dac1257c77f47/