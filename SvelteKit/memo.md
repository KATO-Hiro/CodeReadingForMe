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

+ SvelteKitの仕事の核心は、次の3つに集約される：
  + ルーティング - どのルートが入ってきたリクエストにマッチするかを把握する。
  + ロード - ルートに必要なデータを取得します。
  + レンダリング - HTMLを生成する（サーバーで）、またはDOMを更新する（ブラウザで）。

+ ここまで、ルーティングとレンダリングの仕組みを見てきた。中間の部分であるローディングについて話そう。

+ アプリの各ページは、+page.svelteファイルと一緒に+page.server.jsファイルでロード関数を宣言できます。ファイル名が示すように、このモジュールはクライアント側のナビゲーションを含め、サーバー上でのみ実行されます。src/routes/blog/+page.server.jsファイルを追加して、src/routes/blog/+page.svelte内のハードコードされたリンクを実際のブログ記事データに置き換えることができるようにしてみましょう

// 急に難しくなってきたが、この部分は次期プロジェクトでも使うので、マスターしたい。
// src/routes/hoge/+page.server.jsで、一覧のデータをload()で取得 + 属性を対応付け
// src/routes/hoge/+page.svelteで、一覧ページを表示
// 詳細ページは、hoge/[slug]/の形式に変更するだけで、一覧ページとほぼ同じ


```svelte
// 記事一覧ページ
// src/routes/blog/+page.server.js 
// ダミーデータを取得
import { posts } from './data.js';

// load関数を定義
export function load() {
  return {
    // 一つずつデータを取り出す
    summaries: posts.map((post) => ({
      slug: post.slug,
      title: post.title,
    })),
  };
}


// src/routes/blog/+page.svelte
// 記事一覧を表示
<script>
  // Q: export letの復習が必要そう
  // Q: このdataは、data.jsと同じと考えて良いのか?
  export let data;
</script>

<h1>blog</h1>

<ul>
  {#each data.summaries as { slug, title }}
    <li><a href="/blog/{slug}">{title}</a></li>
  {/each}
</ul>


// 指定した記事を表示するページ
// src/routes/blog/[slug]/+page.server.js 
import { error } from '@sveltejs/kit';
import { posts } from '../data.js';

export function load({ params }) {
  // 合致するページの内容を取得 
  const post = posts.find((post) => post.slug === params.slug);

  // Not found対策
  if (!post) throw error(404);

  return { post };
}

// src/routes/blog/[slug]/+page.svelte
<script>
  export let data;
</script>

<h1>{data.post.title}</h1>
<div>{@html data.post.content}</div>

```

+ Note: チュートリアルでは、src/routes/blog/data.jsからデータをインポートします。実際のアプリでは、データベースやCMSからデータを読み込むことが多いと思いますが、ここではこのようにします。

+ エラー処理は後ほど詳しく学ぶ

+ ハマった点
  + blogページ、および、詳細ページが表示されない
  + 理由: 5xx系のエラーだったので、サーバが応答していなかった
  + 対処方法: Dockerのコンテナを再起動 + rebuild & tsconfigにパスを追加

#### Layout data

+ layout.svelteファイルが子ルートごとにUIを作成するように、+layout.server.jsファイルは子ルートごとにデータを読み込む

+ ブログの投稿ページに「その他の投稿」サイドバーを追加したいとします。src/routes/blog/[slug]/+page.server.jsで行っているように、src/routes/blog/+page.server.jsのload関数から要約を返すこともできますが、それでは繰り返しになります。

+ 代わりに、src/routes/blog/+page.server.jsをsrc/routes/blog/+layout.server.jsにリネームしてみましょう。data.summariesはまだページで利用可能です。

+ レイアウト（とその下のページ）は、親である+layout.server.jsからdata.summarariesを継承します。

+ ある投稿から別の投稿に移動するとき、私たちはその投稿のデータだけを読み込む必要があります。詳細は無効化に関するドキュメントを参照してください。

### Headers and cookies

#### Setting headers

+ load関数の内部では（form actions、hooks、API routesと同様に、後で学びます）、setHeaders関数にアクセスすることができます。当然のことながら-レスポンスにヘッダーを設定するために使うことができる。

+ 最も一般的なのは、Cache-Controlレスポンスヘッダでキャッシュの動作をカスタマイズするために使用することですが、このチュートリアルでは、あまり推奨されない、より劇的なことを行います：

```svelte
// src/routes/+page.server.js
// プレーンテキストでページが表示される
export function load({ setHeaders }) {
  setHeaders({
    'Content-Type': 'text/plain',
  });
}
```

+ (効果を見るにはiframeをリロードする必要があるかもしれない）。

#### Reading and writing cookies

+ setHeaders関数はSet-Cookieヘッダーでは使用できません。代わりに、Cookies APIを使用する必要があります。

+ load関数では、cookies.get(name, options)でクッキーを読み込むことができます：

+ クッキーを設定するには、cookies.set(name, value, options)を使ってください。ブラウザのデフォルトの動作は、やや無駄ですが、現在のパスの親にクッキーを設定するので、クッキーを設定するときにパスを明示的に設定することを強く推奨します。

```svelte
// src/routes/+page.server.js
export function load({ cookies }) {
  const visited = cookies.get('visited');

  cookies.set('visited', 'true', { path: '/' });

  return { visited };
}

```

+ // Q: Cookiesにはどんな情報を含めることが多い?

+ これで、iframeをリロードすると、Hello stranger！がHello friend！になる。

+ cookies.set(name, ...) を呼び出すと、Set-Cookie ヘッダが書き込まれますが、クッキーの内部マップも更新されます。Cookies.getとCookies.setに渡されるオプションは、Cookieドキュメントのparseオプションとserializeオプションに対応しています。SvelteKitでは、Cookieをより安全にするために以下のデフォルトが設定されています：

```md
{
  httpOnly: true,
  secure: true,
  sameSite: 'lax'
}
```

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
