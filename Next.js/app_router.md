# Next.js 13のチュートリアル

## 現状

+ v12の時点で小さなappを作成したが、その後のキャッチアップが止まっている。
  + v13にアップグレードするかどうかを迷っている。

+ v13で、「App Router」が導入されたらしく、破壊的な変更だと聞いている程度。

## 目標

+ 「App Router」が導入された背景、利点、想定される利用状況を理解できるようにする。
  + 新しいプロジェクトで使用するかどうかを判断できるようにする。

+ 新しいプロジェクトを最小限の機能でリリースする。

+ 分かっていることと、分かっていないことを区別する。
  + 分かっていること
    + ほぼ書き方を忘れた可能性が高い。
  + 分かっていないこと
    + Next.jsを導入するメリット・デメリット。特に、App Router。
    + React。関数型の書き方が主流だと思われるが・・・。
    + TypeScript: 見よう見まね or コンパイラのエラーに従って直しているだけ。

+ 機能の実装に必要な部分を重点的に学習する。

## 技術選定の理由

+ サンプルが豊富。

+ Reactだと大変な部分が楽になっているらしい。
  + TODO: 第三者に説明できるようにする。

+ Vueに比べると、TypeScriptとの親和性が高いと聞いている(要出典)。
  + 情報が古くなっており、大差ないかも。

+ Vercelへのデプロイが楽。
  + △: ベンダーロックインが発生
  + 個人開発だとAWSやGCPはオーバーキルなイメージ&従量課金制度に怖さがある。

## 学ぶ範囲

+ Routing: 必須
  + Defining Routes: 必須
  + Pages and Layouts: 必須
  + Linking and Navigating: 必須
  + Route Groups: ?
  + Dynamic Routes: ? (次のプロジェクトでは使わない可能性が高い)
  + Loading UI and Streaming: ほぼ必須?
  + Error Handling: 必須
  + Parallel Routes: ?
  + Intercepting Routes: ?
  + Route Handlers: ?
  + Middleware: ?
  + Project Organization: ?
  + Internationalization: 優先度やや低め
+ Data Fetching: 必須
  + Fetching, Caching, and Revalidating: 必須
  + Patterns: ほぼ必須
  + Server Actions: おそらく必須
+ Rendering:
  + Static and Dynamic: ほぼ必須
  + Client and Server Components: 必須
  + Edge and Node.js Runtimes: ?
+ Styling: ほぼ必須
  + Tailwind CSS
+ Caching // やや後回し?
+ Optimizing // やや後回し?
  + Images // 必須?
  + Fonts// やや後回し?
  + Metadata// やや後回し?
  + Lazy Loading
+ Configuring // やや後回し?
  + ESLint: 優先的に設定しておく
  + TypeScript: 必要そうなものだけ
  + Absolute Imports and Module Path Aliases: 確認はしておく
  + src Directory: 確認はしておく
  + Draft Mode: ?
+ Deploying // 必須
  + Managed Next.js with Vercel

## 導入

+ Next.jsは、Webアプリ用のフレームワーク
  + Reactコンポーネントを使ってUIを構築できる。
  + アプリに構造、機能、最適化を提供。
  + バンドラ、コンパイルなどのツールを抽象化し、自動的に設定。ツールの設定に時間をかけることなく、アプリの構築に集中できる。
  + 個人、大規模開発であっても、インタラクティブ・ダイナミック・高速なアプリの構築を支援してくれる。

## 主要な機能

+ Routing: Server Components上に構築されたファイルシステムベースのルータ
  + レイアウト、ネストされたルーティング、ローディング状態、エラー処理などをサポート
+ Rendering
  + クライアント/サーバ・コンポーネントによるレンダリング
  + Next.jsによるサーバ上の静的・動的レンダリングによる最適化
  + EdgeやNode.jsランタイムでのストリーミング
+ Data fetching
  + React Componentsのasync/awaitサポート
  + ReactとWeb プラットフォームに沿ったfetch()s APIより、データ取得を簡素化
+ Styling
  + CSSモジュール、Tailwind CSS、CSS-in-JSなど、お好みのスタイリング方法をサポート
+ Optimizations
  + 画像、フォント、スクリプトの最適化により、アプリケーションのコアウェブ機能とユーザーエクスペリエンスを向上させる
+ TypeScript
  + より良い型チェックとより効率的なコンパイルが可能に
  + カスタムTypeScriptプラグインと型チェッカーも追加
+ API Reference
  + Next.js全体のAPI設計を更新

## ドキュメントの読み方

+ 順番に読むこともできるが、どのような順番でも良い + ユースケースに該当するページに飛んでも良い。
+ App router / Page routerには、固有の機能があるため、どちらを選択しているかを把握
+ React / Server Componentsを始めて使うときは、React Essentialsのページを読むことを推奨

## 事前に要求される知識

+ HTML、CSS、Reactの基本的な知識
+ Reactに関しては、公式ドキュメントやReact Essentialsを参照

## Architecture

+ TODO: チュートリアル後に読む

+ Turbopackとは?
  + メリットは?

## インストール

+ npmやyarnの代わりに、pnpmを使用してみる。

```terminal
// 依存関係としてパッケージをインストールするのではなく、レジストリから取得し、ホットロードして、そのパッケージが公開するデフォルトのコマンドバイナリを実行
pnpm dlx create-next-app

// 正確なバージョンを指定することもできる
pnpm dlx create-next-app@latest
```

> https://pnpm.io/ja/cli/dlx

```terminal
// 以下の項目を聞かれる
What is your project named? ./   // current dir + 実行前にディレクトリにファイルがない状態にしておく必要がある
Would you like to use TypeScript with this project? No / Yes → Yes
Would you like to use ESLint with this project? No / Yes → Yes
Would you like to use Tailwind CSS with this project? No / Yes → Yes
Would you like to use `src/` directory with this project? No / Yes → Yes
Use App Router (recommended)? No / Yes → Yes
Would you like to customize the default import alias? No / Yes → Yes、@/*
```

> https://www.newt.so/docs/tutorials/get-contents-in-nextjs
> https://stackoverflow.com/questions/72186539/how-to-create-a-next-js-app-directly-in-the-current-folder

Tailwind CSS
> https://reffect.co.jp/html/tailwindcss-for-beginners/

```terminal
$ pnpm dev
```

## プロジェクトの構成

+ トップレベルのファイルやフォルダ、設定ファイル、appディレクトリやpagesディレクトリ内のルーティング規約

> https://nextjs.org/docs/getting-started/project-structure

## React Essentials

+ Reactの新機能であるServer Componentsを使いこなす必要がある
+ Server ComponentsとClient Componentsの違い、使うタイミング、おすすめのパターンについて説明
+ Reactを初めて使う場合、React Docsを参照。

### Server Components

+ サーバ＆クライアント・コンポーネントにより、開発者はサーバとクライアントにまたがるアプリケーションを構築でき、クライアント側アプリケーションのリッチなインタラクティブ性と、従来のサーバ・レンダリングの改善されたパフォーマンスを組み合わせることができます。

#### Thinking in Server Components

+ サーバーとクライアントを活用するハイブリッド・アプリケーションを構築するための新しいメンタル・モデルを導入

+ 従来: アプリケーション全体をクライアントサイドでレンダリング
+ 現在: コンポーネントをレンダリングする場所を目的に応じて柔軟に選択できるように

+ ページを小さなコンポーネントに分割してみると、大半のコンポーネントは非インタラクティブで、サーバー・コンポーネントとしてサーバー上でレンダリングできることに気づくだろう。小さなインタラクティブなUIには、クライアントコンポーネントを使用する。

#### Why Server Components?

+ サーバーのインフラストラクチャをより有効に活用できる

+ メリット: 
  + 最初のページ読み込みが速くなり、クライアント側の JavaScript バンドルのサイズが小さく  
  + 基本的なクライアント側のランタイムはキャッシュ可能でサイズが予測可能で、アプリケーションが大きくなっても増えることはない
  + 追加のJavaScriptが追加されるのは、クライアント・コンポーネントを通してアプリケーションでクライアント側のインタラクティブ機能が使用される場合のみ

+ Next.jsでルートがロードされると、最初のHTMLがサーバーにレンダリングされます。このHTMLはブラウザで徐々に拡張され、Next.jsとReactのクライアントサイドランタイムを非同期にロードすることで、クライアントがアプリケーションを引き継いでインタラクティブ性を追加できるようにな

+ Server Components への移行を容易にするため、App Router 内のすべてのコンポーネントはデフォルトで Server Components になっている

+ 'use client'ディレクティブを使用することで、オプションでClient Componentsを選択することもできる

### Client Components

+ クライアントコンポーネントを使うと、アプリケーションにクライアントサイドのインタラクティブ機能を追加できます。Next.jsでは、クライアントコンポーネントはサーバー上でプリレンダリングされ、クライアント上でハイドレーションされます。クライアントコンポーネントは、Pages Router のコンポーネントと同じようなものだと考えてください。
  + Q: ハイドレーションとは?

#### The "use client" directive

```tsx
// app/counter.tsx

// 以下の1行を追加するだけ
// importよりも上部に記述する必要がある
'use client'
 
import { useState } from 'react'
 
export default function Counter() {
  const [count, setCount] = useState(0)
 
  return (
    <div>
      <p>You clicked {count} times</p>
      <button onClick={() => setCount(count + 1)}>Click me</button>
    </div>
  )
}
```

+ 「use client」は、サーバー専用コードとクライアント・コードの間に位置する。これは、ファイルの一番上、importsの上に置かれ、サーバー専用部分からクライアント部分への境界を越えるカットオフ・ポイントを定義します。ファイルに "use client "が定義されると、そのファイルにインポートされた子コンポーネントを含む他のすべてのモジュールは、クライアントバンドルに含まれるとみなされます。
  + // グラフで考えると、クライアントコンポーネントと宣言したコンポーネントと子コンポーネントは全てクライアント側となるのは納得できる
  + 全てのファイルで定義する必要はない + entry pointで一度だけ定義

### When to use Server and Client Components?

+ 簡単に決めるには、クライアントコンポーネントのユースケースが決まるまで、サーバーコンポーネント（appディレクトリのデフォルト）を使うことをお勧め

+ Server Component
  + データの取得
  + バックエンドのリソースに直接アクセス
  + 機密情報をサーバーに保管（アクセストークン、APIキーなど）
  + 大きな依存関係をサーバーに残す / クライアントサイドのJavaScriptを減らす

+ Client Component
  + インタラクティブ性とイベントリスナー（onClick()、onChange()など）を追加
  + ステートとライフサイクル・エフェクトの使用 (useState()、useReducer()、useEffect() など)
  + ブラウザ専用のAPIを使う
  + ステート、エフェクト、またはブラウザ専用APIに依存するカスタムフックを使用
  + Reactのクラスコンポーネントを使用

### Patterns

#### Moving Client Components to the Leaves

+ アプリケーションのパフォーマンスを向上させるには、クライアント・コンポーネントを可能な限りコンポーネント・ツリーのリーフに移動させることをお勧めします。

+ たとえば、静的な要素（ロゴ、リンクなど）を持つレイアウトと、ステートを使用するインタラクティブな検索バーがあるとします。

+ レイアウト全体をクライアントコンポーネントにする代わりに、インタラクティブロジックをクライアントコンポーネント（<SearchBar />など）に移動し、レイアウトをサーバーコンポーネントにします。これにより、レイアウトのすべてのコンポーネントJavascriptをクライアントに送信する必要がなくなります。

```tsx
// app/layout.tsx

// 子コンポーネントは、サーバ・クライアント両方とも利用できる
// Q: インポートの順番は関係ない?
// SearchBar is a Client Component
import SearchBar from './searchbar'
// Logo is a Server Component
import Logo from './logo'
 
// Layout is a Server Component by default
export default function Layout({ children }: { children: React.ReactNode }) {
  return (
    <>
      <nav>
        <Logo />
        <SearchBar />
      </nav>
      <main>{children}</main>
    </>
  )
}
```

+ // 書き換えが発生しない部分をサーバコンポーネントで、インタラクティブな要素な部分をクライアントコンポーネントにすると理解したが、良さそうか?

### Composing Client and Server Components

+ サーバーコンポーネントとクライアントコンポーネントは、同じコンポーネントツリーで組み合わせることができます。

+ 舞台裏では、Reactは以下のようにレンダリングを処理する：
  + サーバーでは、Reactがすべてのサーバーコンポーネントをレンダリングしてから、結果をクライアントに送信します。
    + これには、クライアント コンポーネント内にネストされたサーバー コンポーネントも含まれます。
    + この段階で遭遇したクライアント・コンポーネントはスキップされる。
  + クライアントでは、Reactがクライアント コンポーネントをレンダリングし、レンダリングされた結果のサーバー コンポーネントをスロットに入れて、サーバーとクライアントで行われた作業をマージします。
    + Server ComponentがClient Componentの中に入れ子になっている場合、レンダリングされたコンテンツはClient Componentの中に正しく配置されます。
  
+ Tips
  + サーバーコンポーネントとクライアントコンポーネントの両方が、HTMLとしてサーバー上にプリレンダリングされます。
    + Q: プリレンダリングをする理由は?

### Nesting Server Components inside Client Components

+ 上記のレンダリングフローを考えると、サーバーコンポーネントをクライアントコンポーネントにインポートする場合、追加のサーバーラウンドトリップが必要になるため、制限があります。

+ サポートされていないパターン：サーバーコンポーネントをクライアントコンポーネントにインポートする

```tsx
'use client'
 
// This pattern will **not** work!
// You cannot import a Server Component into a Client Component.
import ExampleServerComponent from './example-server-component'
 
export default function ExampleClientComponent({
  children,
}: {
  children: React.ReactNode
}) {
  const [count, setCount] = useState(0)
 
  return (
    <>
      <button onClick={() => setCount(count + 1)}>{count}</button>
 
      <ExampleServerComponent />
    </>
  )
}
```

+ 推奨パターン: サーバーコンポーネントをプロップとしてクライアントコンポーネントに渡す
+ その代わりに、クライアント・コンポーネントを設計するときに、React propsを使ってサーバー・コンポーネントの「スロット」をマークすることができます。

+ Server Componentはサーバー上でレンダリングされ、Client Componentがクライアント上でレンダリングされるとき、「スロット」はServer Componentのレンダリング結果で埋められます。

+ 一般的なパターンは、React children propを使用して "スロット "を作成することです。<ExampleClientComponent>をリファクタリングして、一般的なchildren propを受け入れ、<ExampleClientComponent>のインポートと明示的なネストを親コンポーネントに移動することができます。

```tsx
// app/example-client-component.tsx

'use client'
 
import { useState } from 'react'
 
export default function ExampleClientComponent({
  children,
}: {
  children: React.ReactNode
}) {
  const [count, setCount] = useState(0)
 
  return (
    <>
      <button onClick={() => setCount(count + 1)}>{count}</button>

      // 変更点
      // 内容については、関心の外にある
      {children}
    </>
  )
}
```

+ さて、<ExampleClientComponent>は、childrenが何であるかを知らない。Childrenが最終的にServer Componentの結果によって埋められることも知らない。

+ ExampleClientComponentが持つ唯一の責任は、最終的にどんなchildrenがどこに配置されるかを決定することです。

+ 親Server Componentでは、<ExampleClientComponent>と<ExampleServerComponent>の両方をインポートし、<ExampleClientComponent>の子として<ExampleServerComponent>を渡すことができます：

```tsx
// app/page.tsx

// This pattern works:
// You can pass a Server Component as a child or prop of a
// Client Component.
import ExampleClientComponent from './example-client-component'
import ExampleServerComponent from './example-server-component'
 
// Pages in Next.js are Server Components by default
export default function Page() {
  return (
    <ExampleClientComponent>
      <ExampleServerComponent />
    </ExampleClientComponent>
  )
}
```

+ このアプローチでは、<ExampleClientComponent> と <ExampleServerComponent> のレンダリングは切り離され、独立してレンダリングすることができます。

+ Tips
  + このパターンは、childrenプロパティを持つレイアウトとページですでに適用されているので、追加のラッパーコンポーネントを作成する必要はありません。
  + Reactコンポーネント（JSX）を他のコンポーネントに渡すことは、常にReactのコンポジションモデルの一部でした。
  + このコンポジション戦略は、サーバーコンポーネントとクライアントコンポーネントにまたがって機能します。渡されたものがどこに配置されるべきかだけを担当します。
  + これにより、クライアントコンポーネントがクライアントでレンダリングされる前に、渡されたpropを独立して、この場合サーバでレンダリングすることができます。
  + インポートされたネストされた子コンポーネントを再レンダリングする親コンポーネントの状態変更を避けるために、「コンテンツを持ち上げる」というまったく同じ戦略が使用されています。
  + children propに限定されるわけではありません。JSXを渡すために任意のpropを使用できます。

### Passing props from Server to Client Components (Serialization)

+ サーバからクライアントコンポーネントに渡される小道具は、シリアライズ可能である必要があります。つまり、関数や日付などの値を直接クライアント・コンポーネントに渡すことはできません。

+ App Routerでは、ネットワークの境界はServer ComponentsとClient Componentsの間にある。これは、getStaticProps/getServerSidePropsとページコンポーネントの間に境界があるページとは異なります。サーバーコンポーネント内で取得されたデータは、クライアントコンポーネントに渡されない限りネットワーク境界を越えないため、シリアライズする必要はありません。Server Componentsでのデータ取得については、こちらをご覧ください。

### Keeping Server-Only Code out of Client Components (Poisoning)

+ JavaScriptモジュールはサーバーコンポーネントとクライアントコンポーネントの両方で共有することができるため、サーバー上でのみ実行されることを意図していたコードが、クライアントにこっそり入り込む可能性があります。

+ 例えば、次のようなデータ・フェッチ関数を考えてみよう：

```ts
// lib/data.ts
export async function getData() {
  const res = await fetch('https://external-service.com/data', {
    headers: {
      authorization: process.env.API_KEY,
    },
  })
 
  return res.json()
}
```

+ 一見すると、getDataはサーバーとクライアントの両方で動作するように見える。しかし、環境変数API_KEYの前にNEXT_PUBLICが付けられていないため、サーバーでのみアクセスできるプライベート変数になっています。Next.jsでは、セキュアな情報の漏えいを防ぐため、クライアントコードではプライベートな環境変数を空文字列に置き換えています。

+ その結果、getData()をインポートしてクライアント上で実行しても、期待通りに動作しない。また、変数をpublicにすることで、この関数はクライアント上で動作するようになりますが、機密情報が漏れることになります。

+ そのため、この関数はサーバー上でのみ実行されることを想定して書かれています。

### The "server only" package

+ このような意図しないクライアントによるサーバーコードの利用を防ぐために、サーバー専用パッケージを使用することで、他の開発者が誤ってこれらのモジュールをクライアント・コンポーネントにインポートした場合に、ビルド時にエラーを表示することができます。

```terminal
pnpm install server-only
```

+ 次に、サーバー専用のコードを含むモジュールにこのパッケージをインポートする：

```js
import 'server-only'
 
export async function getData() {
  const res = await fetch('https://external-service.com/data', {
    headers: {
      authorization: process.env.API_KEY,
    },
  })
 
  return res.json()
}
```

+ これで、getData()をインポートしたクライアント・コンポーネントは、このモジュールはサーバー上でしか使用できないことを説明するビルド時エラーを受け取ることになる。

+ 対応するclient-onlyパッケージは、クライアント専用のコード（例えば、windowオブジェクトにアクセスするコード）を含むモジュールをマークするために使用できます。

### Data Fetching

+ クライアント・コンポーネントでデータをフェッチすることも可能ですが、クライアントでデータをフェッチする特別な理由がない限り、サーバー・コンポーネントでデータをフェッチすることをお勧めします。データ取得をサーバーに移すことで、パフォーマンスとユーザーエクスペリエンスが向上します。

### Third-party packages

+ Server Componentsは新しいものなので、エコシステム内のサードパーティのパッケージは、useState、useEffect、createContextのようなクライアント専用の機能を使用するコンポーネントに「use client」ディレクティブを追加し始めたばかりです。

+ 現在、クライアントのみの機能を使用する npm パッケージのコンポーネントの多くは、まだこのディレクティブを持っていません。これらのサードパーティコンポーネントは、"use client" ディレクティブを持っているので、クライアントコンポーネント内では期待通りに動作しますが、サーバーコンポーネント内では動作しません。

+ 例えば、<Carousel />コンポーネントを持つacme-carouselパッケージをインストールしたとします。このコンポーネントは useState を使いますが、まだ "use client" ディレクティブを持っていません。

+ クライアントコンポーネント内で<Carousel />を使用すると、期待通りに動作します：

```tsx
'use client'
 
import { useState } from 'react'
import { Carousel } from 'acme-carousel'
 
export default function Gallery() {
  let [isOpen, setIsOpen] = useState(false)
 
  return (
    <div>
      <button onClick={() => setIsOpen(true)}>View pictures</button>
 
      {/* Works, since Carousel is used within a Client Component */}
      {isOpen && <Carousel />}
    </div>
  )
}
```

+ しかし、これをサーバーコンポーネント内で直接使用しようとすると、エラーが表示されます：

```tsx
import { Carousel } from 'acme-carousel'
 
export default function Page() {
  return (
    <div>
      <p>View pictures</p>
 
      {/* Error: `useState` can not be used within Server Components */}
      <Carousel />
    </div>
  )
}
```

+ これは、Next.jsが<Carousel />がクライアント専用の機能を使っていることを知らないためです。

+ これを解決するには、クライアント専用機能に依存するサードパーティ製コンポーネントを、独自のクライアントコンポーネントでラップします：

```tsx
'use client'
 
import { Carousel } from 'acme-carousel'
 
export default Carousel
```

+ これで、サーバー コンポーネント内で <Carousel /> を直接使用できるようになりました：

```tsx
import Carousel from './carousel'
 
export default function Page() {
  return (
    <div>
      <p>View pictures</p>
 
      {/*  Works, since Carousel is a Client Component */}
      <Carousel />
    </div>
  )
}
```

+ サードパーティのコンポーネントは、クライアント・コンポーネント内で使用する可能性が高いため、ほとんどのコンポーネントをラップする必要はないと考えています。ただし、プロバイダ・コンポーネントはReactのステートとコンテキストに依存しており、通常はアプリケーションのルートで必要になるからです。サードパーティのコンテキスト・プロバイダについては、以下を参照してください。

## Context

+ ほとんどのReactアプリケーションは、createContextを介して直接、またはサードパーティ・ライブラリからインポートされたプロバイダ・コンポーネントによって間接的に、コンポーネント間でデータを共有するためにコンテキストに依存している。

+ Next.js 13では、コンテキストはクライアントコンポーネント内で完全にサポートされていますが、サーバーコンポーネント内で直接作成したり消費したりすることはできません。これは、サーバーコンポーネントにはReactの状態がなく（インタラクティブではないため）、コンテキストは主に、Reactの状態が更新された後にツリーの奥深くにあるインタラクティブコンポーネントを再レンダリングするために使用されるためです。

+ Server Components間でデータを共有する方法については後述しますが、まずはClient Components内でContextを使用する方法を見てみましょう。

### Using context in Client Components

+ すべてのコンテキストAPIは、クライアント・コンポーネント内で完全にサポートされています：

```tsx
'use client'
 
import { createContext, useContext, useState } from 'react'
 
const SidebarContext = createContext()
 
export function Sidebar() {
  const [isOpen, setIsOpen] = useState()
 
  return (
    <SidebarContext.Provider value={{ isOpen }}>
      <SidebarNav />
    </SidebarContext.Provider>
  )
}
 
function SidebarNav() {
  let { isOpen } = useContext(SidebarContext)
 
  return (
    <div>
      <p>Home</p>
 
      {isOpen && <Subnav />}
    </div>
  )
}
```

+ しかし、コンテキスト プロバイダは、現在のテーマなど、グローバルな関心事を共有するために、アプリケーションのルート付近にレンダリングされるのが一般的です。Server Components ではコンテキストがサポートされていないため、アプリケーションのルートでコンテキストを作成しようとするとエラーになります：

```tsx
import { createContext } from 'react'
 
//  createContext is not supported in Server Components
export const ThemeContext = createContext({})
 
export default function RootLayout({ children }) {
  return (
    <html>
      <body>
        <ThemeContext.Provider value="dark">{children}</ThemeContext.Provider>
      </body>
    </html>
  )
}
```

+ これを解決するには、コンテキストを作成し、そのプロバイダをクライアント・コンポーネントの中にレンダリングします：

```tsx
'use client'
 
import { createContext } from 'react'
 
export const ThemeContext = createContext({})
 
export default function ThemeProvider({ children }) {
  return <ThemeContext.Provider value="dark">{children}</ThemeContext.Provider>
}
```

+ サーバーコンポーネントは、クライアントコンポーネントとしてマークされているので、プロバイダを直接レンダリングできるようになります：

```tsx
import ThemeProvider from './theme-provider'
 
export default function RootLayout({
  children,
}: {
  children: React.ReactNode
}) {
  return (
    <html>
      <body>
        <ThemeProvider>{children}</ThemeProvider>
      </body>
    </html>
  )
}
```

+ プロバイダがルートにレンダリングされると、アプリ全体の他のすべてのクライアント・コンポーネントがこのコンテキストを利用できるようになります。

+ Tips:
  + ThemeProvider が <html> ドキュメント全体ではなく {children} だけをラップしていることに注目してください。これにより、Next.js はサーバーコンポーネントの静的な部分を最適化しやすくなります。

### Rendering third-party context providers in Server Components

+ サードパーティのnpmパッケージには、アプリケーションのルート近くでレンダリングする必要があるプロバイダが含まれていることがよくあります。これらのプロバイダに "use client "ディレクティブが含まれていれば、Server Components内で直接レンダリングできます。しかし、Server Componentsは非常に新しいので、多くのサードパーティプロバイダはまだこのディレクティブを追加していません。

+ use client "が指定されていないサードパーティのプロバイダをレンダリングしようとすると、エラーが発生します：

```tsx
import { ThemeProvider } from 'acme-theme'
 
export default function RootLayout({ children }) {
  return (
    <html>
      <body>
        {/*  Error: `createContext` can't be used in Server Components */}
        <ThemeProvider>{children}</ThemeProvider>
      </body>
    </html>
  )
}
```

+ これを解決するには、サードパーティプロバイダを独自のクライアントコンポーネントでラップします：

```tsx
'use client'
 
import { ThemeProvider } from 'acme-theme'
import { AuthProvider } from 'acme-auth'
 
export function Providers({ children }) {
  return (
    <ThemeProvider>
      <AuthProvider>{children}</AuthProvider>
    </ThemeProvider>
  )
}
```

+ これで、ルート・レイアウト内で<Providers />を直接インポートしてレンダリングできるようになりました。

```js
import { Providers } from './providers'
 
export default function RootLayout({ children }) {
  return (
    <html>
      <body>
        <Providers>{children}</Providers>
      </body>
    </html>
  )
}
```

+ プロバイダをルートにレンダリングすると、これらのライブラリのすべてのコンポーネントとフックが、クライアント・コンポーネント内で期待どおりに動作します。

+ サードパーティライブラリがクライアントコードに "use client "を追加したら、ラッパークライアントコンポーネントを削除することができます。

### Sharing data between Server Components

+ Server Componentsはインタラクティブではないので、Reactのステートから読み込むことはありません。代わりに、複数のServer Componentsがアクセスする必要がある共通のデータには、ネイティブのJavaScriptパターンを使用できます。例えば、モジュールを使用して、複数のコンポーネント間でデータベース接続を共有することができます：

```ts
// utils/database.ts
export const db = new DatabaseConnection()
```

```tsx
// app/users/layout.tsx

import { db } from '@utils/database'
 
export async function UsersLayout() {
  let users = await db.query()
  // ...
}


// app/users/[id]/page.tsx
import { db } from '@utils/database'
 
export async function DashboardPage() {
  let user = await db.query()
  // ...
}
```

+ 上記の例では、レイアウトとページの両方がデータベースクエリを行う必要があります。これらの各コンポーネントは、@utils/databaseモジュールをインポートすることで、データベースへのアクセスを共有します。このJavaScriptパターンはグローバル・シングルトンと呼ばれます。

### Sharing fetch requests between Server Components

+ データをフェッチするとき、ページやレイアウトとその子コンポーネントの間でフェッチ結果を共有したい場合があります。これはコンポーネント間の不要な結合であり、コンポーネント間で小道具を行き来させることにつながります。

+ その代わりに、データを消費するコンポーネントと一緒にデータフェッチをコロケーションすることをお勧めします。フェッチリクエストはサーバーコンポーネントで自動的にメモ化されるので、各ルートセグメントは重複リクエストを心配することなく、必要なデータを正確にリクエストできます。Next.jsはフェッチキャッシュから同じ値を読み込みます。

## App Router

+ Appルーターは、Reactの最新機能を使用してアプリケーションを構築するための新しいパラダイムです。すでにNext.jsに慣れ親しんでいる方であれば、App Routerが既存のファイルシステムベースのルーターであるPages Routerの自然な進化形であることがわかるでしょう。

+ 新しいアプリケーションには、Appルータの使用をお勧めします。既存のアプリケーションについては、段階的にAppルーターに移行することができます。

+ このセクションでは、App Routerで利用可能な機能について説明します：

## Building Your Application

+ Next.jsは、柔軟なフルスタックのWebアプリケーションを作成するためのビルディングブロックを提供します。Building Your Applicationのガイドでは、これらの機能の使い方や、アプリケーションの動作をカスタマイズする方法を説明しています。

+ Next.jsアプリケーションを構築する際に、ステップバイステップで進めることができるように、セクションとページは基本から応用まで順を追って構成されています。ただし、どのような順番で読んでもかまいませんし、自分のユースケースに該当するページに飛ばしてもかまいません。

+ Next.jsが初めての方は、ルーティング、レンダリング、データ取得、スタイリングのセクションから始めることをおすすめします。その後、最適化や設定など、他のセクションをより深く学ぶことができます。最後に、準備ができたら、デプロイとアップグレードのセクションをチェックしてください。

## Routing Fundamentals

+ すべてのアプリケーションの骨格はルーティングです。このページでは、Webのルーティングの基本的な概念と、Next.jsでのルーティングの扱い方を紹介

### Terminology (用語解説)

+ ツリー：階層構造を視覚化するための規約。例えば、親と子のコンポーネントを持つコンポーネントツリー、フォルダ構造など。
+ サブツリー：ツリーの一部で、新しいルート（最初）から始まり、リーフ（最後）で終わるもの。
+ ルート：ルート・レイアウトなど、ツリーまたはサブツリーの最初のノード。
+ リーフ：子ノードを持たないサブツリーのノード。URLパスの最後のセグメントなど。

// 競プロで学んだグラフ理論の用語とやや意味が異なるので注意

+ URLセグメント：スラッシュで区切られたURLパスの一部。
+ URLパス：ドメインの後に来るURLの一部（セグメントで構成される）。

### The app Router

+ バージョン13で、Next.jsはReact Server Componentsをベースにした新しいApp Routerを導入し、共有レイアウト、ネストされたルーティング、ロードステート、エラー処理などをサポートした。

+ App Routerは、appという新しいディレクトリで動作します。appディレクトリは、pagesディレクトリと並行して動作し、段階的な導入を可能にします。これにより、アプリケーションのいくつかのルートを新しい動作に移行させる一方で、他のルートを以前の動作のためにpagesディレクトリに残しておくことができます。もしあなたのアプリケーションがpagesディレクトリを使うのであれば、Pages Routerのドキュメントも参照してください。

+ Tips: アプリルーターはページルーターよりも優先される

+ デフォルトでは、アプリ内のコンポーネントはReact Server Componentsです。これはパフォーマンスの最適化であり、簡単に採用することができます。また、クライアント・コンポーネントを使用することもできます。

// これまでのバージョンから大きく変更された点の一つ

### Roles of Folders and Files

+ Next.jsはファイルシステム・ベースのルーターを使う：

+ フォルダはルートを定義するために使用されます。ルートは、ネストされたフォルダのシングルパスで、ルートフォルダから最終的なリーフフォルダ（page.jsファイルを含む）まで、ファイルシステムの階層をたどります。ルートの定義」を参照してください。
+ ファイルは、ルートセグメントに対して表示されるUIを作成するために使用されます。特殊ファイルを参照してください。

// ぱっと見ではv12までとそんなに変わっていなさそう

### Route Segments

+ ルートの各フォルダーはルートセグメントを表します。各ルートセグメントは URL パスの対応するセグメントにマップされます。

### Nested Routes

+ ネストされたルートを作成するには、フォルダ同士をネストします。たとえば、appディレクトリに2つの新しいフォルダをネストすることで、新しい/dashboard/settingsルートを追加できます。

```md
/ (Root segment)
dashboard (Segment)
settings (Leaf segment)
```

### File Conventions (規約)

+ Next.jsは、ネストされたルートで特定の動作をするUIを作成するための特別なファイル群を提供します：

+ layout: レイアウト セグメントとその子の共有UI
+ page: ページ ルートのユニークなUIとルートの一般公開
+ loading: セグメントとその子の UI をロードする
+ not-found セグメントとその子の UI が見つからない
+ error: セグメントとその子のためのエラー UI
+ global-error グローバルエラー UI
+ route: サーバーサイド API エンドポイント
+ template: 特殊化された再レンダリングされたレイアウトUI
+ default: パラレルルートのデフォルトフォールバックUI

+ Tips:
  + これらのファイルの拡張子は、.js、.jsx、.tsxが利用できる

+ Q: v12までの_app.tsxとかはどこに行った?
  + App Routerの場合は使わない?

### Component Hierarchy

+ ルートセグメントの特別なファイルで定義されたReactコンポーネントは、特定の階層でレンダリングされます：

layout.js
template.js
error.js (React error boundary)
loading.js (React suspense boundary)
not-found.js (React error boundary)
page.js or nested layout.js

```jsx
<Layout>
  <Template>
    <ErrorBoundary fallback={<Error />}>
      <Suspense fallback={<Loading />}>
        <ErrorBoundary fallback={<NotFound />}>
          <Page />
        </ErrorBoundary>
      </Suspense>
    </ErrorBoundary>
  </Template>
</Layout>
```

+ Q: Suspenseとはどんな機能?どんなときに使う
+ Q: fallbackとは?

+ 入れ子になったルートでは、セグメントのコンポーネントは、親セグメントのコンポーネントの中に入れ子になります。

### Colocation

+ 特別なファイルに加え、独自のファイル（コンポーネント、スタイル、テストなど）をappディレクトリのフォルダ内に配置するオプションもあります。

+ これは、フォルダがルートを定義する一方で、page.jsまたはroute.jsによって返されるコンテンツだけが公開アドレスになるためです。
  + // 他のフレームワークのindexページに相当するものと思えば良い?

```
- app
  - components
    - button.js
  - lib
    - constants.js
  - dashboard
    - pages.js // 公開
    - nav.js
  - api
    - route.js  // 公開
    - db.js
```

### Advanced Routing Patterns

+ App Routerはまた、より高度なルーティングパターンの実装に役立つ一連の規約も提供する。これらには以下が含まれる：
  + 並行ルーティング：同時に2つ以上のページを同じビューに表示し、独立してナビゲートできるようにします。独自のサブナビゲーションを持つ分割ビューに使用できます。例：ダッシュボード
  + ルートのインターセプト：ルートをインターセプトして、別のルートのコンテキストに表示できます。現在のページのコンテキストを維持することが重要な場合に使用できます。例：あるタスクの編集中にすべてのタスクを表示する、フィード内の写真を拡大する、など。
+ これらのパターンによって、よりリッチで複雑なUIを構築することができ、これまで小規模なチームや個人開発者が実装するのが複雑だった機能を民主化することができます。

// シンプルなappである限りは、後回しにして良さそう

## TypeScript

### TypeScript Plugin

+ Next.jsにはカスタムのTypeScriptプラグインとタイプチェッカーが含まれており、VSCodeや他のコードエディタが高度なタイプチェックとオートコンプリートに利用できる。
+ VSCodeの例
  + コマンドパレットを開く
  + TypeScript: Select TypeScript Versionを検索
  + Use Workspace Versionを選択
+ ファイル編集時にカスタムプラグインが有効になります。次のビルドを実行する際には、カスタム・タイプ・チェッカーが使用されます。

+ プラグインの主要な機能
  + セグメント設定オプションに無効な値が渡された場合の警告。
  + 利用可能なオプションとインコンテキストのドキュメントの表示。
  + use client ディレクティブが正しく使用されていることを確認する。
  + クライアントフック(useStateなど)がクライアントコンポーネントでのみ使用されるようにする。

### Minimum TypeScript Version

+ v4.5.2以上を推奨
  + インポート名の型修飾子やパフォーマンスの改善など

### Statically Typed Links

+ Next.jsは、next/link使用時のタイプミスやその他のエラーを防ぐために、リンクを静的にタイプすることができます。

+ この機能を有効にするには、experimental.typedRoutesを有効にし、プロジェクトでTypeScriptを使用している必要がある。

```js
// next.config.js
/** @type {import('next').NextConfig} */
const nextConfig = {
  experimental: {
    typedRoutes: true,
  },
}
 
module.exports = nextConfig
```

+ Next.jsは.next/typesに、アプリケーションに存在するすべてのルートに関する情報を含むリンク定義を生成し、TypeScriptはそれを使ってエディタに無効なリンクに関するフィードバックを提供することができる。

+ 現在のところ、実験的なサポートには、動的セグメントを含むあらゆる文字列リテラルが含まれる。リテラルでない文字列の場合、現在のところ手動でRoute.Hrefとしてキャストする必要がある：

```js
import type { Route } from 'next';
import Link from 'next/link'
 
// No TypeScript errors if href is a valid route
<Link href="/about" />
<Link href="/blog/nextjs" />
<Link href={`/blog/${slug}`} />
<Link href={('/blog' + slug) as Route} />
 
// TypeScript errors if href is not a valid route
<Link href="/aboot" />
```

+ next/linkをラップするカスタムコンポーネントでhrefを受け入れる場合は、ジェネリックを使用します：

```ts
import type { Route } from 'next'
import Link from 'next/link'
 
function Card<T extends string>({ href }: { href: Route<T> | URL }) {
  return (
    <Link href={href}>
      <div>My Card</div>
    </Link>
  )
}
```

### End-to-End Type Safety

+ Next.js 13では、型安全性が強化されています。

+ フェッチ関数とページ間のデータのシリアライズがありません：サーバー上のコンポーネント、レイアウト、ページで直接フェッチできます。このデータは、Reactで消費するためにクライアント側に渡すためにシリアライズ（文字列に変換）する必要はありません。その代わり、アプリはデフォルトでServer Componentsを使用するため、余分な手順を踏むことなく、Date、Map、Setなどの値を使用できる。以前は、Next.js固有の型を使ってサーバーとクライアントの境界を手動で入力する必要がありました。
+ コンポーネント間のデータフローが合理化されました：ルートレイアウトの代わりに_appが削除されたことで、コンポーネントとページ間のデータフローを視覚化しやすくなりました。以前は、個々のページと_appの間を流れるデータはタイプしにくく、混乱を招くバグを引き起こす可能性がありました。Next.js 13のコロケーションデータ取得では、この問題はなくなりました。
+ Next.jsのデータフェッチは、データベースやコンテンツプロバイダの選択を規定することなく、可能な限りエンドツーエンドに近い型安全性を提供します。

+ 通常のTypeScriptで期待されるように、レスポンスデータをタイプすることができます。たとえば

```tsx
// app/page.tsx
async function getData() {
  const res = await fetch('https://api.example.com/...')
  // The return value is *not* serialized
  // You can return Date, Map, Set, etc.
  return res.json()
}
 
export default async function Page() {
  const name = await getData()
 
  return '...'
}
```

+ 完全なエンドツーエンドの型安全性を実現するには、データベースやコンテンツプロバイダがTypeScriptをサポートしていることも必要です。これはORMやタイプセーフ・クエリ・ビルダーを利用することで実現できる。

### Async Server Component TypeScript Error

+ TypeScript で非同期 Server Component を使用するには、TypeScript 5.1.3 以降と @types/react 18.2.8 以降を使用していることを確認してください。

+ 古いバージョンの TypeScript を使用している場合、'Promise<Element> is not a valid JSX element type' というエラーが表示されることがあります。TypeScriptと@types/reactを最新バージョンにアップデートすることで、この問題は解決するはずです。

### Passing Data Between Server & Client Components

+ サーバーとクライアントコンポーネントの間でpropsを通してデータを渡す場合、データはブラウザで使用するためにシリアライズ（文字列に変換）されます。しかし、特別な型は必要ありません。コンポーネント間で他のpropsを渡すのと同じように型付けされます。

+ さらに、レンダリングされていないデータはサーバとクライアントの間を行き来しない（サーバに残る）ので、シリアライズされるコードが少なくなります。これは、Server Componentsのサポートによってのみ可能になりました。

### Path aliases and baseUrl

+ Next.jsは自動的にtsconfig.jsonの "paths "と "baseUrl "オプションをサポートします。

+ この機能の詳細については、モジュールパスのエイリアスのドキュメントを参照してください。

### Type checking next.config.js

+ next.config.jsはBabelやTypeScriptで解析されないため、JavaScriptファイルでなければなりませんが、以下のようにJSDocを使ってIDEで型チェックを追加することができます：

```js
// @ts-check
 
/**
 * @type {import('next').NextConfig}
 **/
const nextConfig = {
  /* config options here */
}
 
module.exports = nextConfig
```

### Incremental type checking

+ v10.2.1以降、Next.jsはtsconfig.jsonで有効にするとインクリメンタル型チェックをサポートするので、大規模なアプリケーションで型チェックを高速化できます。

### Ignoring TypeScript Errors

+ プロジェクトに TypeScript のエラーがあると、Next.js は本番ビルド（次のビルド）に失敗します。

+ アプリケーションにエラーがあっても、Next.jsに危険なプロダクションコードを生成させたい場合は、組み込みの型チェックステップを無効にできます。

+ 無効にした場合は、ビルドまたはデプロイプロセスの一部として型チェックを実行していることを確認してください。

+ next.config.jsを開き、typescript configのignoreBuildErrorsオプションを有効にします：

```js
module.exports = {
  typescript: {
    // !! WARN !!
    // Dangerously allow production builds to successfully complete even if
    // your project has type errors.
    // !! WARN !!
    ignoreBuildErrors: true,
  },
}
```

## 疑問点

+ npmとnpxの違いは?
  + npm: node package manager
    + 役割
      + プロジェクトを公開するためのオンラインリポジトリ
      + パッケージのインストールと依存関係の管理するためのCLIツール
    + パッケージの実行
      + package.jsonで指定する必要がある
      + ローカルにもグローバルにもインストールできる
        + local: ./node_modules/.bin/
        + global: /usr/local/bin
      + ローカルのnode_modulesに必ずインストールしないと試せない
        + package.jsonに追加したいパッケージを記述 + npm run
  + npx
    + npmv5.2.0以降、あらかじめバンドルされている
    + 依存関係を簡単にインストール・管理できる
    + インストールされていないパッケージを実行できる(グローバル変数の汚染が少なくなる)
      + スクリプトの実行前には、スクリプトを必ず読む
    + パッケージやモジュールの異なるバージョンを簡単にテストできる

```terminal
// インストール済みか確認
$ which npx

// ローカルにインストール
$ npx package-name
```

> https://www.freecodecamp.org/news/npm-vs-npx-whats-the-difference/

+ インポートエイリアスとは?
  + パスの設定を短縮して、簡潔に書けるような仕組み
  + リファクタリングしやすい

> https://t-cr.jp/memo/3cc17944875d1463

+ Tailwind CSSとは?
  + Next.jsでデフォルトとして採用されているのはなぜ?
  + 他のCSSフレームワークとの違いは?

+ App Routerとは?
  + A: Reactの最新機能を使用してアプリケーションを構築するための新しいパラダイム
    + 新しいアプリケーションには、Appルータの使用を推奨
    + 既存のアプリケーションについては、段階的にAppルーターに移行できる
  + SSRとの違いは?
  + どのような場面での利用が想定されている?

+ Reactのコンポーネントがフロントとサーバを分離するのはなぜ?

+ ストリーミングとは?

+ Prettierは使わないようになった?別のツールが主流になっている?

+ インポート名の型修飾子(type modifiers on import names)とは?

+ Server Componentsとは?
  + Q: どんなメリットがある?
    + サーバとクライアントを分離して、最初の読み込みを高速化 + JSのバンドルサイズを小さく + パフォーマンス向上
    + これまでサーバサイドに相当する機能もクライアントサイドにあったので、明確に分割しようとしていると理解すれば良い?
  + Q: 使い道は?
  + Q: デフォルトで設定されているのはなぜ?
    + A: v13への移行を容易にするため 
  + Q: 従来のサーバ・レンダリングとの違いは?
  + Q: デメリットは?
    + 学習コストが増える?

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
