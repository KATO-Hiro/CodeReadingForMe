# Next.js 13のチュートリアル

## 現状

+ v12の時点で小さなappを作成したが、その後のキャッチアップが止まっている。

+ v13で、「App Router」が導入されたらしく、破壊的な変更だと聞いている程度。

## 目標

+ 「App Router」が導入された背景、利点、想定される利用状況を理解できるようにする。

+ 新しいプロジェクトを最小限の機能でリリースする。

+ 分かっていることと、分かっていないことを区別する。

+ 機能の実装に必要な部分を重点的に学習する。

## 技術選定の理由

+ サンプルが豊富。

+ Reactだと大変な部分が楽になっているらしい。

+ Vueに比べると、TypeScriptとの親和性が高いと聞いている(要出典)。

+ Vercelへのデプロイが楽。
  + △: ベンダーロックインが発生
  + 個人開発だとAWSやGCPはオーバーキルなイメージ&従量課金制度に怖さがある。

## インストール

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
