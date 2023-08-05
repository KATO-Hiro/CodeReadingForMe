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

Note: Dockerを利用する場合は、以下のコマンドを一旦コメントアウトしておく

```Dockerfile
# ADD package.json /package.json

# RUN pnpm install
# RUN pnpm build

# CMD ["pnpm", "dev"]
```

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

## Defining Routes

+ page.jsファイルが外部からアクセスできるページ
  + .jsx、tsxも可能

### Creating UI

+ 各ルートセグメントの UI を作成するために、特別なファイル規約が使用されます。最も一般的なものは、ルートに固有のUIを表示するページと、複数のルートで共有されるUIを表示するレイアウトです。

+ 例えば、最初のページを作成するには、appディレクトリ内にpage.jsファイルを追加し、Reactコンポーネントをエクスポートします：

```tsx
// app.page.tsx
// Reactの文法と同じ
export default function Page() {
  return <h1>Hello, Next.js!</h1>
}
```

## Pages and Layouts

+ Next.js 13のアプリルーターには、ページ、共有レイアウト、テンプレートを簡単に作成するための新しいファイル規約が導入されています。

### Pages

+ ページはルート固有のUIです。page.jsファイルからコンポーネントをエクスポートすることでページを定義できます。ネストされたフォルダを使ってルートを定義し、page.jsファイルを使ってルートにパブリックアクセスできるようにします。

+ appディレクトリ内にpage.jsファイルを追加して、最初のページを作成します：

```tsx
// `app/page.tsx` is the UI for the `/` URL
export default function Page() {
  return <h1>Hello, Home page!</h1>
}


// `app/dashboard/page.tsx` is the UI for the `/dashboard` URL
// `app/hoge/page.tsxとすることで、/hogeというページが作れる`
export default function Page() {
  return <h1>Hello, Dashboard Page!</h1>
}
```

+ Tips
  + ページは常にルートサブツリーのリーフです。
  + ページには .js、.jsx、または .tsx ファイル拡張子を使用できます。
  + ルートセグメントを一般にアクセス可能にするには page.js ファイルが必要です。
  + ページはデフォルトではサーバーコンポーネントですが、クライアントコンポーネントに設定することもできます。
  + ページはデータをフェッチできます。詳細については、データ取得のセクションを参照してください。

### Layouts

+ レイアウトは複数のページで共有されるUIです。ナビゲーションの際、レイアウトは状態を保持し、インタラクティブであり続け、再レンダリングはしません。レイアウトは入れ子にすることもできます。

+ レイアウトは、デフォルトでlayout.jsファイルからReactコンポーネントをエクスポートすることで定義できます。コンポーネントは、レンダリング中に子レイアウト（存在する場合）または子ページを入力するchildren propを受け入れる必要があります。

```tsx
// PageNameLayout
// Q: ペースのレイアウトを上書きすると理解すれば良い?
export default function DashboardLayout({
  children, // will be a page or nested layout
}: {
  children: React.ReactNode
}) {
  return (
    <section>
      {/* Include shared UI here e.g. a header or sidebar */}
      <nav></nav>
 
      {children}
    </section>
  )
}
```

+ Tips
  + 一番上のレイアウトはルートレイアウトと呼ばれます。この必須のレイアウトは、アプリケーション内のすべてのページで共有されます。ルートレイアウトはhtmlタグとbodyタグを含まなければなりません。
  + 任意のルートセグメントはオプションで独自のレイアウトを定義することができます。これらのレイアウトはそのセグメント内のすべてのページで共有されます。
  + ルート内のレイアウトはデフォルトでネストされます。それぞれの親レイアウトは、React children propを使用して、その下の子レイアウトをラップします。
  + Route Groups を使用すると、特定のルートセグメントを共有レイアウトから出し入れできます。
  + レイアウトはデフォルトではサーバーコンポーネントですが、クライアントコンポーネントに設定することもできます。
  + レイアウトはデータをフェッチできます。詳しくは「データのフェッチ」を参照してください。
  + 親レイアウトとその子レイアウトの間でデータを受け渡すことはできません。ただし、ルート内で同じデータを複数回フェッチすることは可能で、Reactはパフォーマンスに影響を与えることなくリクエストを自動的にデデュープします。
  + レイアウトは現在のルートセグメントにアクセスできません。ルートセグメントにアクセスするには、クライアントコンポーネントで useSelectedLayoutSegment または useSelectedLayoutSegments を使用します。
  + レイアウトには、.js、.jsx、または .tsx ファイル拡張子を使用できます。
  + layout.jsとpage.jsファイルは、同じフォルダに定義できます。レイアウトはページをラップします。

+ Q: dedupeとは?
  + 重複を除去する

### Root Layout (Required)

+ ルートレイアウトはアプリディレクトリのトップレベルで定義され、すべてのルートに適用されます。このレイアウトによって、サーバーから返される最初のHTMLを修正できます。

```tsx
export default function RootLayout({
  children,
}: {
  children: React.ReactNode
}) {
  return (
    // Rootレイアウトでは、<html>タグと<body>タグが必須
    <html lang="en">
      <body>{children}</body>
    </html>
  )
}
```

+ Tips
  + appディレクトリにはルートレイアウトを含める必要があります。
  + Next.jsは<html>タグと<body>タグを自動的に作成しないため、ルートレイアウトには<html>タグと<body>タグを定義する必要があります。
  + 組み込みのSEOサポートを使用して、<head> HTML要素、たとえば<title>要素を管理できます。
  + ルートグループを使って複数のルートレイアウトを作成できます。例をご覧ください。
  + ルートレイアウトはデフォルトではサーバーコンポーネントであり、クライアントコンポーネントに設定することはできません。

+ Note
  + pagesディレクトリからの移行：ルート・レイアウトは、_app.jsファイルと_document.jsファイルを置き換えます。移行ガイドをご覧ください。
  + // v12からの移行では置き換える必要があるらしい。知識不足というのもあるが、旧Verの_documents.jsはおまじないみたいな要素が強かった気がする

### Nesting Layouts

+ フォルダ(app/dashboard/layout.jsなど)内で定義されたレイアウトは特定のルートセグメント(acme.com/dashboardなど)に適用され、それらのセグメントがアクティブなときにレンダリングされます。デフォルトでは、ファイル階層内のレイアウトはネストされ、子プロップを通して子レイアウトをラップします。

+ Tips: ルート・レイアウトだけが<html>タグと<body>タグを含むことができます。

+ 上記の2つのレイアウトを組み合わせる場合、ルートレイアウト（app/layout.js）はダッシュボードレイアウト（app/dashboard/layout.js）をラップし、app/dashboard/*内のルートセグメントをラップします。

+ Route Groups（ルートグループ）を使って、特定のルートセグメントを共有レイアウトに入れたり、入れたりすることができます。

### Templates

+ テンプレートは各子レイアウトやページをラップするという点で、レイアウトに似ています。ルートにまたがって永続し、ステートを維持するレイアウトとは異なり、テンプレートはナビゲーションの際に子要素ごとに新しいインスタンスを作成します。つまり、ユーザがテンプレートを共有するルート間をナビゲートすると、コンポーネントの新しいインスタンスがマウントされ、DOM要素が再作成され、ステートは保持されず、エフェクトが再同期されます。

+ そのような特定の動作が必要な場合、レイアウトよりもテンプレートの方が適切な選択肢になることもあるでしょう。例えば
  + CSSやアニメーション・ライブラリを使った入退場アニメーション。
  useEffect に依存する機能 (例: ページビューのロギング) と useState に依存する機能 (例: ページごとのフィードバックフォーム)。
  + デフォルトのフレームワークの動作を変更する。例えば、レイアウト内のサスペンスバウンダリは、レイアウトが最初にロードされたときのみフォールバックを表示し、ページを切り替えたときは表示しません。テンプレートの場合、フォールバックはナビゲーションごとに表示されます。

+ 推奨Templateを使用する特別な理由がない限り、Layoutsを使用することをお勧めします。
  + Q: 基本的にはLayoutを使い、特別なときだけTemplateを使うと理解すれば良いか?

### Modifying <head>

+ アプリのディレクトリでは、組み込みのSEOサポートを使って、タイトルやメタなどの<head> HTML要素を変更することができます。

+ メタデータは、layout.jsまたはpage.jsファイル内のmetadataオブジェクトまたはgenerateMetadata関数をエクスポートすることで定義できます。

```tsx
// app/page.tsx
import { Metadata } from 'next'
 
export const metadata: Metadata = {
  title: 'Next.js',
}
 
export default function Page() {
  return '...'
}
```

+ ルート・レイアウトに<title>や<meta>のような<head>タグを手動で追加すべきではありません。その代わりに、<head>要素のストリーミングや重複除去といった高度な要件を自動的に処理するMetadata APIを使うべきです。

## Linking and Navigating

+ Next.jsでは2通りの方法
  + <Link> Component
  + useRouter Hook

// <Link>とMUIとの相性があまり良くなくて(多分、自分が理解できていなかっただけだと思うが・・・)、前のプロジェクトはではMUIの公式repoを参考に改変した記憶がある

### <Link> Component

+ <Link>はHTMLの<a>タグを拡張した組み込みコンポーネントで、ルート間のプリフェッチとクライアントサイドナビゲーションを提供します。Next.jsでルート間をナビゲートする主要な方法です。

```tsx
// コンポーネントをインポート
import Link from 'next/link'
 
export default function Page() {
  // <Link href="/foo">Hoge</Link>
  // <a>タグ + コンポーネントの定型に似た書き方
  // オプションのpropsもあり、APIリファレンスを参照する
  return <Link href="/dashboard">Dashboard</Link>
}
```

#### examples

##### Linking to Dynamic Segments

// 動的にリンクを生成できるっぽい
// 次回作ではまだ使わなさそうなので、後回し

##### Checking Active Links

+ usePathname()を使うと、指定したリンクがアクティブか判定できる
// 次回作ではまだ使わなさそうなので、後回し

##### Scrolling to an id

// 次回作ではまだ使わなさそうなので、後回し

### useRouter() Hook

+ useRouterフックを使うと、プログラムでルートを変更することができます。

+ このフックはクライアントコンポーネント内でのみ使用可能で、next/navigationからインポートされます。

```js
// クライアント側であることを指定
'use client'
 
// インポート
import { useRouter } from 'next/navigation'
 
export default function Page() {
  // 他のuseHogeと同じような使い方
  const router = useRouter()
 
  return (
    <button type="button" onClick={() => router.push('/dashboard')}>
      Dashboard
    </button>
  )
}
```

+ 推奨: useRouterを使用する特別な要件がない限り、ルート間の移動には<Link>コンポーネントを使用してください。
  + // 基本は<Link>を使えば良さそう

### How Routing and Navigation Works

+ App Routerは、ルーティングとナビゲーションのハイブリッド・アプローチを採用しています。サーバー上では、アプリケーションコードは自動的にルートセグメントによってコード分割されます。そしてクライアントでは、Next.jsがルートセグメントをプリフェッチしてキャッシュします。つまり、ユーザーが新しいルートに移動しても、ブラウザはページをリロードせず、変更されたルートセグメントだけが再レンダリングされます。

// Q: レンダリングの範囲を限定しているという、超ざっくりな理解しかできていないかも

### Route Groups

+ アプリディレクトリでは、ネストされたフォルダは通常 URL パスにマップされます。しかし、フォルダをルートグループとしてマークすることで、そのフォルダがルートのURLパスに含まれないようにすることができます。

+ これにより、URL パス構造に影響を与えることなく、ルートセグメントとプロジェクトファイルを論理的なグループに整理できます。

+ ルートグループは次の場合に便利です：
  + たとえば、サイトセクション別、インテント別、チーム別などのグループにルートを整理します。
  + 同じルートセグメントレベルでネストされたレイアウトを有効にする：
  + 複数のルートレイアウトを含め、同じセグメントで複数のネストされたレイアウトを作成する。
  + 共通セグメント内のルートのサブセットにレイアウトを追加する。

// Q: 大規模なプロジェクト用の機能と思えばよい?

### Dynamic Routes

+ 事前に正確なセグメント名がわからず、動的なデータからルートを作成したい場合、リクエスト時に入力されるか、ビルド時にプリレンダリングされる動的セグメントを使用できます。

// Q: ブログの記事ページを作るときのようなイメージ?

### Loading UI and Streaming

+ 特別なファイルloading.jsは、React Suspenseを使って意味のあるLoading UIを作成するのに役立ちます。この規約を使えば、ルートセグメントのコンテンツがロードされる間、サーバーからのロード状態を即座に表示できます。レンダリングが完了すると、新しいコンテンツが自動的に入れ替わります。

// MUIでそれなりに苦労して実装したLoading画面を楽に作れそう

### Error Handling

+ error.jsファイル規約は、ネストされたルートで予期しない実行時エラーを優雅に処理することを可能にします。
  + ルートセグメントとそのネストした子要素を React Error Boundary で自動的にラップします。
  + ファイルシステム階層を使用して粒度を調整し、特定のセグメントに合わせたエラー UI を作成します。
  + アプリケーションの残りの機能を維持しながら、影響を受けるセグメントにエラーを隔離します。
  + ページ全体をリロードすることなくエラーからの回復を試みる機能を追加します。
+ ルートセグメント内に error.js ファイルを追加し、React コンポーネントをエクスポートすることで、エラー UI を作成します：

// 専用のページを用意しなくてもエラーメッセージは表示されるが、とても素っ気ない感じはする + どう対処するべきかが示されていないので不親切ではある

### Parallel Routes

+ パラレルルーティングを使用すると、1つまたは複数のページを同じレイアウトで同時に、または条件付きでレンダリングできます。ダッシュボードやソーシャルサイトのフィードなど、アプリの非常に動的なセクションの場合、パラレルルーティングを使用して複雑なルーティングパターンを実装できます。

// 次回のプロジェクトでは使わなさそうなので、後回し

### Intercepting Routes

+ ルートをインターセプトすることで、現在のページのコンテキストを維持したまま、現在のレイアウト内でルートをロードすることができます。このルーティングパラダイムは異なるルートを表示するために特定のルートを「インターセプト」したいときに便利です。

+ たとえば、フィードの中から写真をクリックすると、その写真と一緒にフィードをオーバーレイするモーダルが表示されるはずです。この場合、Next.js は /feed ルートをインターセプトして、この URL を「マスク」し、代わりに /photo/123 を表示します。

// 次回のプロジェクトでは使わなさそうなので、後回し

### Route Handlers

+ ルートハンドラを使用すると、Web リクエスト API とレスポンス API を使用して、指定したルートのカスタムリクエストハンドラを作成できます。

+ Tips:
  + Route Handlersはappディレクトリ内でのみ利用可能です。つまり、API RoutesとRoute Handlersを一緒に使う必要はありません。
    + // Page routerのapp/apiと同じらしいと理解した
  
#### Convention

+ ルートハンドラは、アプリディレクトリ内のroute.js|tsファイルで定義します：

```ts
// app/api/route.ts
// Q: 他のFWならapp/api/index.extensionに相当すると考えれば良い?

// メソッド名が全て大文字になっている
// asyncで非同期処理も可能に
// Q: サーバ側の処理だと理解しているが妥当か? clientとは宣言されていないため
export async function GET(request: Request) {}
```

+ ルートハンドラは、page.jsやlayout.jsと同じように、appディレクトリの中に入れ子にすることができます。しかし、page.jsと同じルートセグメントレベルにroute.jsファイルを置くことはできません。

##### Supported HTTP Methods

+ 以下のHTTPメソッドがサポートされています：GET、POST、PUT、PATCH、DELETE、HEAD、OPTIONSです。サポートされていないメソッドが呼び出された場合、Next.jsは405 Method Not Allowedレスポンスを返します。

##### Extended NextRequest and NextResponse APIs

// 次回のプロジェクトでは使わなさそうなので、後回し

#### Behavior

##### Caching

+ ResponseオブジェクトでGETメソッドを使用する場合、Routeハンドラはデフォルトでキャッシュされます。

```ts
// app/items/route.ts
import { NextResponse } from 'next/server'
 
export async function GET() {
  const res = await fetch('https://data.mongodb-api.com/...', {
    headers: {
      'Content-Type': 'application/json',
      'API-Key': process.env.DATA_API_KEY,
    },
  })
  // Q: 例外処理はしなくて良いのか?
  const data = await res.json()
 
  return NextResponse.json({ data })
}
```

#### Examples

// 次回のプロジェクトで使うまで、後回し

### Middleware

+ ミドルウェアを使えば、リクエストが完了する前にコードを実行することができる。そして、送られてきたリクエストに基づいて、レスポンスを書き換えたり、リダイレクトしたり、リクエストやレスポンスのヘッダーを変更したり、直接レスポンスしたりすることで、レスポンスを変更することができる。

+ ミドルウェアはキャッシュされたコンテンツとルートがマッチする前に実行されます。詳細はパスのマッチングを参照してください。

### Project Organization and File Colocation

+ Next.jsは、ルーティングフォルダとファイル規約を除けば、プロジェクトファイルの整理方法や配置方法については、一切関知しません。

#### Safe colocation by default

+ アプリディレクトリでは、ネストされたフォルダ階層がルート構造を定義する。
+ page.jsまたはroute.jsファイルがルートセグメントに追加されると、アクセスできる
  + page.jsやroute.jsが返すコンテンツだけがクライアントに送信される
  + プロジェクトファイルは、誤ってルーティング可能になることなく、appディレクトリのルートセグメント内に安全に配置できる

+ Tips:
  + pagesディレクトリとは異なり、pagesにあるファイルはすべてルートとみなされます。
  + プロジェクト・ファイルをappディレクトリに置くことはできますが、そうする必要はありません。必要であれば、appディレクトリの外に置いておくこともできます。

#### Project organization features

##### Private Folders

+ プライベート・フォルダーは、フォルダーの前にアンダースコアを付けることで作成できます：_folderName
  + // Pythonのclassでprotected / privateするときに近い考え方 + より厳密に公開レベルの制約があると考えて良さそう

+ メリット
  + UIロジックとルーティングロジックの分離。
  + プロジェクトとNext.jsエコシステム全体で、内部ファイルを一貫して整理する。
  + コードエディタでのファイルの並べ替えとグループ化。
  + 将来のNext.jsファイル規約との潜在的な名前の衝突を避ける。

+ Tips
  + フレームワークの規約ではありませんが、同じアンダースコアのパターンを使って、プライベートフォルダ外のファイルを "private "とマークすることも考えてみてください。
  + アンダースコアで始まる URL セグメントを作成するには、フォルダ名の前に %5F（アンダースコアの URL エンコード形式）を付けます：%5F 。
  + プライベートフォルダを使用しない場合は、予期しない名前の衝突を防ぐために、Next.jsの特別なファイル規則を知っておくと便利です。

##### Route Groups

+ フォルダを括弧で囲むことで作成できます。
  + (folderName)

+ これは、フォルダが整理のためのものであり、ルートのURLパスに含まれるべきではないことを示します。

+ メリット
  + サイトセクション別、インテント別、チーム別などのグループにルートを整理する。
  + 同じルートセグメントレベルでネストされたレイアウトを有効にする：
  + 複数のルートレイアウトを含む、同一セグメント内の複数のネストされたレイアウトの作成
  + 共通セグメント内のルートのサブセットにレイアウトを追加する

##### src Directory

+ アプリケーション・コードと、たいていはプロジェクトのルートにあるプロジェクト設定ファイルを分離

##### Module Path Aliases

+ 深くネストされたプロジェクトファイル間のインポートの読み取りと維持を容易に

```js
// app/dashboard/settings/analytics/page.js

// before
import { Button } from '../../../components/button'
 
// after
// ../../../が、@/になっている
// そういえば、外部libだと以下の書き方になっているケースが多かった気がする
import { Button } from '@/components/button'
```

#### Project organization strategies

+ Next.jsプロジェクトで自分のファイルやフォルダを整理するのに、「正しい」「間違っている」ということはありません。

次のセクションでは、一般的な戦略の概要を非常に高いレベルで説明します。最も簡単なのは、自分とチームに合った戦略を選び、プロジェクト全体で一貫性を保つことです。

+ Tips:
  + 以下の例では、componentsとlibフォルダを一般化されたプレースホルダとして使用(これらの命名には特別なフレームワークの意味はない)

##### Store project files outside of app

+ すべてのアプリケーション・コードをプロジェクトのルートにある共有フォルダに格納し、appディレクトリを純粋にルーティングのためだけに使用

```md
- your-project
  - components
  - lib
  - app
    - dashboard
      - page.js
    - page.js
```

##### Store project files in top-level folders inside of app

+ すべてのアプリケーション・コードをアプリ・ディレクトリのルートにある共有フォルダに格納

```md
- your-project
  - app
    - components
    - lib
    - dashboard
      - page.js
    - page.js
```

##### Split project files by feature or route

+ グローバルに共有されるアプリケーションコードをルートappディレクトリに格納し、より具体的なアプリケーションコードを、それらを使用するルートセグメントに分割

```md
- your-project
  - app
    - components
    - lib
    - dashboard
      - components
      - lib
      - page.js
    - page.js
```

// 各構成のメリット・デメリットを考える必要がありそう

### Internationalization

+ 多言語に対応したコンテンツのルーティングやレンダリングを設定できます。サイトを異なるロケールに適応させるには、翻訳されたコンテンツ（ローカリゼーション）と国際化されたルートが含まれる

// 次期プロジェクトで多言語対応が必要になったときに読む

## Data Fetching

### Data Fetching, Caching, and Revalidating

+ 主な方法
  1. サーバー上で、フェッチAPIを使用
  2. サーバー上で、サードパーティのライブラリを使用
  3. クライアントで、サードパーティのライブラリを使用

#### Fetching Data on the Server with fetch

+ Next.jsは、ネイティブのfetch Web APIを拡張し、サーバー上の各fetchリクエストに対するキャッシュと再検証の動作を設定できるようにした。React は fetch を拡張して、React コンポーネント ツリーのレンダリング中にフェッチ リクエストを自動的にメモします。

+ サーバーコンポーネント、ルートハンドラ、およびサーバーアクションで、async/await を使用して fetch を使用できます。

```tsx
// 非同期で処理
async function getData() {
  // fetchでレスポンスを取得
  const res = await fetch('https://api.example.com/...')
  // The return value is *not* serialized
  // You can return Date, Map, Set, etc.
 
  // エラー処理
  if (!res.ok) {
    // This will activate the closest `error.js` Error Boundary
    throw new Error('Failed to fetch data')
  }
 
  // jsonを返す
  return res.json()
}
 
export default async function Page() {
  const data = await getData()
 
  return <main></main>
}
```

+ Tips
  + Next.jsは、クッキーやヘッダーなど、サーバーコンポーネントのデータを取得する際に必要となる便利な関数を提供します。これらはリクエスト時刻の情報に依存するため、ルートが動的にレンダリングされます。
  + Routeハンドラでは、RouteハンドラはReactコンポーネントツリーの一部ではないため、フェッチリクエストはメモされません。
  + TypeScriptのServer Componentでasync/awaitを使用するには、TypeScript 5.1.3以上と@types/react 18.2.8以上が必要です。

## Optimizing

### Image Optimization

+ Next.js Imageコンポーネントは、HTMLの<img>要素を拡張し、画像を自動的に最適化
  + サイズの最適化：WebPやAVIFなどの最新の画像フォーマットを使用して、各デバイスに適したサイズの画像を自動的に提供します。
  + 視覚的な安定性：画像の読み込み時に自動的にレイアウトがずれるのを防ぎます。
  + ページ読み込みの高速化：画像は、ブラウザのネイティブレイジーローディングを使用して、ビューポートに入ったときにのみ読み込まれます。
  + 資産の柔軟性：リモートサーバーに保存された画像でも、オンデマンドで画像サイズを変更できます。

### Usage

```ts
import Image from 'next/image'
```

### Local Images

+ ローカル画像を使用するには、.jpg、.png、または.webp画像ファイルをインポートします。

+ Next.jsは、インポートされたファイルに基づいて、画像の幅と高さを自動的に決定します。これらの値は、画像の読み込み中に累積レイアウトがずれるのを防ぐために使用されます

```ts
import Image from 'next/image'
import profilePic from './me.png'
 
export default function Page() {
  return (
    <Image
      src={profilePic}
      alt="Picture of the author"
      // width={500} automatically provided
      // height={500} automatically provided
      // blurDataURL="data:..." automatically provided
      // placeholder="blur" // Optional blur-up while loading
    />
  )
}
```

+ 警告: 警告動的なawait import()やrequire()はサポートされていない。ビルド時に解析できるように、インポートは静的でなければなりません。

### Examples

#### Responsive

```ts
import Image from 'next/image'
import mountains from '../public/mountains.jpg'
 
export default function Responsive() {
  return (
    <div style={{ display: 'flex', flexDirection: 'column' }}>
      <Image
        alt="Mountains"
        // Importing an image will
        // automatically set the width and height
        src={mountains}
        sizes="100vw"
        // Make the image display full width
        style={{
          width: '100%',
          height: 'auto',
        }}
      />
    </div>
  )
}
```


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
