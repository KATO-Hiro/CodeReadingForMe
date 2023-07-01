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
