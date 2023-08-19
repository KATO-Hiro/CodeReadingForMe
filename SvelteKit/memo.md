# SvelteKitのチュートリアルを試す

## 現状

+ Svelteの基本的なチュートリアルを試した。
  + Advancedは、利用するイメージが湧かずに大部分をスキップしている

+ 。

## 目標

+ 新プロジェクトに導入できるか、チュートリアルを試して判断。

+ 。

## 技術選定の理由

+ React / Nextよりもシンプルに記述できる + 学習コストが低いと聞いて。

+ 公式サイトのセールポイント
  + 高速(セットアップ・ビルド・ページロード・ナビゲーションなど)
  + 楽しい: bundlerの設定、ルーティング、SSR、CSP、TypeScript、デプロイ設定など、退屈なことを考え出すのに何日も費やす必要はもうない。
  + フレキシブル: JSが動作するところならどこでも動作
    + あなたが作っているものを成功させるためのツールを提供
  + どこにでもデプロイ
    + 1行のコードでアダプターを交換できる

## Install

```terminal
pnpm create svelte@latest

// Where should we create your project?
// Enterキーを押すとcur dirに

// Which Svelte app template?
Skeleton project (Barebones scaffolding for your new SvelteKit app)

// Add type checking with TypeScript?
// Yes, using JavaScript with JSDoc comments (以下との違いがあまり分かっていない)
Yes, using TypeScript syntax


// Select additional options (use arrow keys/space bar)
// ひとまず全部チェックを入れてみた
◻ Add ESLint for code linting
◻ Add Prettier for code formatting
◻ Add Playwright for browser testing
◻ Add Vitest for unit testing
```

+ Q: Playwrightとは?
+ Q: Vitestとは?
+ Q: 両者の違いは?

VSCode拡張
> https://marketplace.visualstudio.com/items?itemName=svelte.svelte-vscode

## Introduction

### What is SvelteLit?

+ Svelteがコンポーネント・フレームワークであるのに対して、SvelteKitはアプリ・フレームワーク（人によっては「メタフレームワーク」）であり、生産に耐えうるものを構築するための厄介な問題を解決する
  + ルーティング
  + サーバーサイド・レンダリング
  + データフェッチ
  + サービスワーカー
  + TypeScriptの統合
  + プリレンダリング
  + シングルページアプリ
  + ライブラリのパッケージング
  + 最適化されたプロダクションビルド
  + 異なるホスティングプロバイダーへのデプロイ など

+ SvelteKitアプリはデフォルトでサーバーレンダリングされ、優れたファーストロードパフォーマンスとSEO特性を発揮が
+ その後クライアントサイドナビゲーション（最新の「シングルページアプリ」またはSPAのようなもの）に移行できる

+ シンプルに始めて、必要な機能を追加していきましょう。

+ Q: サービスワーカーとは?

#### Project structure

+ `svelte.config.js `: プロジェクトの設定
+ `vite.config.js` : Viteの設定
  + SvelteKitはViteを使用しているため、ホットモジュールの置き換え、TypeScriptのサポート、静的アセットの処理など、Viteの機能を使用することができます

+ srcにはアプリのソースコードが入ります。src/app.htmlはページテンプレート（SvelteKitは%sveltekit.head%と%sveltekit.body%を適宜置き換えます）で、src/routesはアプリのルートを定義します。
  + routesのページを管理するファイルが、+page.svelte // (+)がファイル名についている
+ staticにはアプリのデプロイ時に含めるべきアセット（favicon.pngやrobots.txtなど）が含まれる

+ Note: "type"も指定されていることに注意：「これは、.jsファイルがレガシーなCommonJSフォーマットではなく、デフォルトでネイティブJavaScriptモジュールとして扱われることを意味する。

### Routing

#### Pages

+ ファイルシステムベースのルーティングを採用

+ src/routesの中にある+page.svelteファイルはすべて、アプリ内にページを作成します。もし/aboutに移動すると、404 Not Foundエラーが表示されます。
  + src/routes/+page.svelte // Homeページ(相当)
  + src/routes/hoge/+page.svelte // hogeページ
  + // +page.svelte = 他のFWの/indexページに相当すると理解した

+ 従来のマルチページアプリとは異なり、/aboutに移動して戻ると、シングルページアプリのように現在のページのコンテンツが更新されます。これにより、高速なサーバー・レンダリングによる起動と、瞬時のナビゲーションという、両方の長所を得ることができる。(この動作は設定可能）。

#### Layouts

+ アプリの異なるルートは、共通のUIを共有することがよくあります。それぞれの+page.svelteコンポーネントでそれを繰り返す代わりに、同じディレクトリ内のすべてのルートに適用される+layout.svelteコンポーネントを使うことができます。
  + // src/routes/+layout.svelteに、UIの共通部分をまとめる
  + <slot />要素は、ページ内容がレンダリングされる場所です

+ layout.svelteファイルは、兄弟の+page.svelte（存在する場合）を含む、すべての子ルートに適用されます。レイアウトは任意の深さまでネストできます。

#### Route parameters

+ 動的なパラメータでルートを作成するには、有効な変数名を角括弧で囲んでください。
  + 例えば、src/routes/blog/[slug]/+page.svelteのようなファイルは、/blog/one、/blog/two、/blog/threeなどにマッチするルートを作成します。
  + // src/routes/hoge/[slug]/+page.svelte

+ Note: foo/[bar]x[baz]は有効なルートで、[bar]と[baz]は動的なパラメータです。

### Loading data

#### Page data

## 疑問点

+ Viteはどんな技術?
  + SvelteKitにデフォルトで組み込まれているのはなぜ?
+ SvelteKitは、どこまでカバーしている?
  + フロントエンドのみ? or 簡単なAPIなら利用可? / APIは別のFWの方が望ましい?
  + DBを扱うには?
  + ログイン機能の実装をどのように行う?
    + 公式サンプルがあれば確認

+ 。

## 感想

+ 依存モジュールが少ない初期段階ではあるが、Next.jsと比べてブラウザが表示されるまでの時間が短い気がする。

+ 。

## 抽象化

+ 。

+ 。

## 応用

+ 。

+ 。

## See

+ Svelte
    https://kit.svelte.dev/

+ Vite
    https://vitejs.dev/
