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
  + どんなメリットがある?
  + 使い道は?
  + デフォルトで設定されているのはなぜ?

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
