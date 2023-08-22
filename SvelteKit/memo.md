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

### Shared modules

#### The $lib alias

+ SvelteKitはディレクトリベースのルーティングを使用しているため、モジュールやコンポーネントを、それらを使用するルートと一緒に配置するのは簡単です。良い経験則は、「使用する場所の近くにコードを配置する」です。

+ コードが複数の場所で使われることもあります。このような場合、インポートの前に./../../../を付けることなく、すべてのルートからアクセスできる置き場所があると便利です。SvelteKitでは、その場所がsrc/libディレクトリです。このディレクトリにあるものはすべて、$libエイリアスを介してsrcのどのモジュールからもアクセスできます。

+ この演習の+page.svelteファイルは、どちらもsrc/lib/message.jsをインポートしている。しかし、/a/deeply/nested/routeに移動すると、プレフィックスが間違っているため、アプリが壊れてしまいます。代わりに$lib/message.jsを使うように更新してください

```svelte
// src/routes/a/deeply/nested/route/+page.svelte
<script>
  // ../../libのような表記の代わりに、$lib
  import { message } from '$lib/message';
</script>

<h1>a deeply nested route</h1>
<p>{message}</p>

```

+ 別プロジェクトで../../../../みたいなインポートをしていたので、これは楽そう
  + 特にファイル構造を変更したときにも、変更箇所が少なくなりそう

### Forms

#### The <form> element

+ 読み込みの章では、サーバーからブラウザにデータを送る方法を説明しました。時には逆方向にデータを送る必要があり、そこで<form>（ウェブプラットフォームのデータ送信方法）の出番となります。

+ Todoアプリを作ってみよう。src/lib/server/database.jsにインメモリ・データベースをセットアップし、src/routes/+page.server.jsのload関数でCookies APIを使っているので、ユーザーごとのTodoリストを持つことができる：

```svelte
// src/route/+page.svelte
<div class="centered">
  <h1>todos</h1>

  // POSTメソッドを使用
  <form method="POST">
    <label>
      add a todo:
      <input name="description" autocomplete="off" />
    </label>
  </form>

  // 一覧を表示
  <ul class="todos">
    {#each data.todos as todo (todo.id)}
      <li>
        {todo.description}
      </li>
    {/each}
  </ul>
</div>
```

+ <input>に何かを入力してEnterを押すと、ブラウザは現在のページにPOSTリクエストを行います（method="POST "属性があるため）。しかし、POSTリクエストを処理するサーバーサイドのアクションを作成していないため、エラーになります。では、そうしましょう：

```svelte
// src/routes/+page.server.js
// DBサーバ(mock)からデータを読み込み
import * as db from '$lib/server/database.js';

// load関数を定義
export function load({ cookies }) {
  // Q: ユーザidはクッキーに入れるとしても、パスワードなどの機密情報はそのままだとマズいのでは?
  const id = cookies.get('userid');

  if (!id) {
    // Q: crypto.randomUUID()とは?
    // ユニークなidを生成してくれると思えば良い?
    cookies.set('userid', crypto.randomUUID(), { path: '/' });
  }

  return {
    // DBに対する操作のうち、一覧の取得
    todos: db.getTodos(id) ?? [],
  };
}

// Q: 基礎知識がないせいか、actionsとform + Enterキーとの対応関係が分かっていない
// on:clickなどでイベントが発火するのは理解できるが、特に設定した記憶がない
// Q: ボタンクリックで更新する方法と何が違う?
export const actions = {
  // Q: defaultは何のデフォルトであることを表している?
  // A: デフォルト・アクション = createであり、名前付きアクションと共存できない。
  default: async ({ cookies, request }) => {
    const data = await request.formData();
    db.createTodo(cookies.get('userid'), data.get('description'));
  },
};
```

+ リクエストは標準的なRequestオブジェクトです。await request.formData()はFormDataインスタンスを返します。

+ Enterを押すと、データベースが更新され、新しいデータでページがリロードされます。

+ データは自動的に更新されます。そして、<form>要素を使っているので、このアプリはJavaScriptが無効になっていたり、利用できなかったりしても動作します。

// server.jsを追加したときは、reloadだけでは不十分な場合もありそう
// pnpm devをすると、サーバエラーが解消した

```js
// src/lib/server/database.js
// In a real app, this data would live in a database,
// rather than in memory. But for now, we cheat.

// Q: DBファイルは一つ? or ある程度巨大になってきたら分割?
// Q: 本物のDBに接続するにはどうしたらいい?
const db = new Map();

export function getTodos(userid) {
  if (!db.get(userid)) {
    db.set(userid, [
      {
        id: crypto.randomUUID(),
        description: 'Learn SvelteKit',
        done: false,
      },
    ]);
  }

  return db.get(userid);
}

export function createTodo(userid, description) {
  const todos = db.get(userid);

  todos.push({
    id: crypto.randomUUID(),
    description,
    done: false,
  });
}

export function deleteTodo(userid, todoid) {
  const todos = db.get(userid);
  const index = todos.findIndex((todo) => todo.id === todoid);

  if (index !== -1) {
    todos.splice(index, 1);
  }
}
```

#### Named form actions

+ 単一のアクションしかないページは、実際にはかなりまれです。ほとんどの場合、ページには複数のアクションが必要です。このアプリでは、Todoを作成するだけでは不十分で、完了したら削除したい。

+ まず、デフォルトのアクションを名前付きの作成アクションと削除アクションに置き換えることから始める：

```svelte
// src/routes/+page.server.js
export const actions = {
  // defaultからcreateに変更
  create: async ({ cookies, request }) => {
    const data = await request.formData();
    db.createTodo(cookies.get('userid'), data.get('description'));
  },

  delete: async ({ cookies, request }) => {
    const data = await request.formData();
    db.deleteTodo(cookies.get('userid'), data.get('id'));
  },
};
```

+ Note: デフォルト・アクションは名前付きアクションと共存できない。

+ <form>要素にはオプションでaction属性があり、これは<a>要素のhref属性に似ています。既存のフォームを更新して、新しいcreateアクションを指すようにします：

+ Note: もしアクションが別のページで定義されていれば、/todos?/createのようなものがあるでしょう。アクションはこのページにあるので、パス名を完全に省略することができます。

+ 次に、各Todoのフォームを作成し、そのTodoを一意に識別するための隠し<input>を作成する：

```svelte
// src/routes/+page.svelte
<div class="centered">
  <h1>todos</h1>

  // action="?/create"でアクションを指定
  <form method="POST" action="?/create">
    <label>
      add a todo:
      <input name="description" autocomplete="off" />
    </label>
  </form>

  <ul class="todos">
    {#each data.todos as todo (todo.id)}
      <li>
        // action="?/delete"
        <form method="POST" action="?/delete">
          // TODOを一意に識別するための隠し<input>
          <input type="hidden" name="id" value={todo.id} />
          <span>{todo.description}</span>
          <button aria-label="Mark as complete" />
        </form>
      </li>
    {/each}
  </ul>
</div>
```

#### Validation

+ ユーザーはいたずら好きで、チャンスがあればあらゆる種類の無意味なデータを送信します。彼らが混乱を引き起こさないようにするには、フォームデータを検証することが重要です。

+ 防御の第一線はブラウザに内蔵されているフォームバリデーションで、例えば<input>を必須とマークすることが簡単にできます：

+ <input>が空の状態でEnterキーを押してみてください。

```svelte
<script>
  export let data;
  export let form;
</script>

<div class="centered">
  <h1>todos</h1>

  // フォームにアクセス
  // 入力エラーがあれば表示
  {#if form?.error}
    <p class="error">{form.error}</p>
  {/if}

  <form method="POST" action="?/create">
    // required属性をつけることで、入力を強制できる
    <label>
      add a todo:
      <input name="description" value={form?.description ?? ''} autocomplete="off" required />
    </label>
  </form>

  <ul class="todos">
    {#each data.todos as todo (todo.id)}
      <li>
        <form method="POST" action="?/delete">
          <input type="hidden" name="id" value={todo.id} />
          <span>{todo.description}</span>
          <button aria-label="Mark as complete" />
        </form>
      </li>
    {/each}
  </ul>
</div>

<style>
  ...
</style>
```


+ このようなバリデーションは役に立ちますが、不十分です。いくつかのバリデーションルール（一意性など）は<input>属性では表現できませんし、もしユーザーがエリートハッカーであれば、ブラウザのdevtoolsを使って属性を削除してしまうかもしれません。このような悪ふざけから守るためには、常にサーバーサイドのバリデーションを使うべきです。

+ src/lib/server/database.js で、記述が存在し、一意であることを検証する

```svelte
// src/lib/server/database.js
...
export function createTodo(userid, description) {
  // 入力がない場合への対処
  if (description === '') {
    throw new Error('todo must have a description');
  }

  const todos = db.get(userid);

  // 重複への対処
  if (todos.find((todo) => todo.description === description)) {
    throw new Error('todos must be unique');
  }

  todos.push({
    id: crypto.randomUUID(),
    description,
    done: false,
  });
}
```

+ 重複するTodoを送信してみてください。やばい！SvelteKitは不親切なエラーページを表示します。サーバー上では「todoは一意でなければならない」というエラーが表示されますが、SvelteKitは予期せぬエラーメッセージをユーザーから隠します。

+ 同じページに留まり、何が間違っているのかを表示してユーザーが修正できるようにする方がずっと良いでしょう。そのためには、fail 関数を使用してアクションのデータを適切な HTTP ステータスコードとともに返します：

```js
// src/routes/+page.server.js
export const actions = {
  create: async ({ cookies, request }) => {
    const data = await request.formData();

    // エラーが発生したらキャッチ
    try {
      db.createTodo(cookies.get('userid'), data.get('description'));
    } catch (error) {
      return fail(422, {
        description: data.get('description'),
        error: error.message,
      });
    }
  },

```

+ src/routes/+page.svelteでは、フォームプロップを通して返された値にアクセスすることができます：

+ Note: 例えば、データが保存されたときに'success!'メッセージを表示するために、failでラップせずにアクションからデータを返すこともできます。

#### Progressive enhancement

+ <form>を使用しているため、ユーザーがJavaScriptを持っていなくてもアプリは動作します（おそらく皆さんが思っている以上によくあることです）。これは素晴らしいことで、私たちのアプリが弾力的であることを意味します。

+ ほとんどの場合、ユーザーはJavaScriptを持っています。そのような場合、SvelteKitがクライアントサイドのルーティングを使用して<a>要素を段階的に拡張していくのと同じように、エクスペリエンスを段階的に拡張していくことができます。

+ enhance関数を$app/forms...からインポートする。

+ そして、<form>要素にuse:enhanceディレクティブを追加します：

+ これだけだ！JavaScriptが有効になっている場合、use:enhanceは全ページのリロードを除いてブラウザネイティブの動作をエミュレートします。それは
  + フォームのプロップを更新する
  + 成功したレスポンスに対してすべてのデータを無効にし、ロード関数を再実行させる
  + リダイレクトレスポンスで新しいページに移動する
  + エラーが発生した場合、最も近いエラーページをレンダリングする

+ ページをリロードするのではなく、更新するようになったので、トランジションのような派手なことができるようになった：

```svelte
<script>
  // インポート
  import { fly, slide } from 'svelte/transition';
  ...
</script>

<div class="centered">
  ...
  // use:enhanceを追加
  <form method="POST" action="?/create" use:enhance>
    <label>
      add a todo:
      <input name="description" value={form?.description ?? ''} autocomplete="off" required />
    </label>
  </form>

  <ul class="todos">
    {#each data.todos as todo (todo.id)}
      // 動作を指定できる
      <li in:fly={{ y: 20 }} out:slide>
        ...
      </li>
    {/each}
  </ul>
</div>

```

#### Customizing use:enhance

+ use:enhanceを使えば、単にブラウザのネイティブな動作をエミュレートするだけでなく、さらに踏み込んだことができる。コールバックを提供することで、保留状態や楽観的なUIを追加することができます。2つのアクションに人工的な遅延を追加することで、遅いネットワークをシミュレートしてみましょう：

+ アイテムを作成したり削除したりすると、UIが更新されるまでに1秒かかってしまう。これを解決するには、ローカル・ステートを追加する。

+ 最初のuse:enhanceでトグルを作成する：

+ そして、データを保存している間にメッセージを表示することができる：

```svelte
<script>
  import { fly, slide } from 'svelte/transition';
  import { enhance } from '$app/forms';

  export let data;
  export let form;

  // 追加
  let creating = false;
  let deleting = [];
</script>

<div class="centered">
  <h1>todos</h1>

  {#if form?.error}
    <p class="error">{form.error}</p>
  {/if}

  <form
    method="POST"
    action="?/create"
    use:enhance={() => {
      // 更新中のフラグを立てる
      creating = true;

      // update()関数で更新
      return async ({ update }) => {
        await update();
        // 更新が終了したらフラグを元に戻す
        creating = false;
      };
    }}
  >
    <label>
      add a todo:
      <input
        // 作成中のみdisableに
        disabled={creating}
        name="description"
        value={form?.description ?? ''}
        autocomplete="off"
        required
      />
    </label>
  </form>

  // 更新中のみ表示
  {#if creating}
    <span class="saving">saving...</span>
  {/if}

  <ul class="todos">
    // フィルタリング: 削除候補に含まれていない
    {#each data.todos.filter((todo) => !deleting.includes(todo.id)) as todo (todo.id)}
      <li in:fly={{ y: 20 }} out:slide>
        <form
          method="POST"
          action="?/delete"
          use:enhance={() => {
            // 削除候補に追加
            deleting = [...deleting, todo.id];

            return async ({ update }) => {
              await update();
              // TODO: filterの挙動を十分に理解できていないかも
              deleting = deleting.filter((id) => id !== todo.id);
            };
          }}
        >
          <input type="hidden" name="id" value={todo.id} />
          <span>{todo.description}</span>
          <button aria-label="Mark as complete" />
        </form>
      </li>
    {/each}
  </ul>
</div>

```

+ 削除の場合、サーバーが何かを検証するのを待つ必要はない：

### API routes

#### GET handlers

+ SvelteKitで作成できるのはページだけではありません。HTTPメソッドに対応する関数をエクスポートする+server.jsファイルを追加することで、APIルートを作成することもできます：GET、PUT、POST、PATCH、DELETEです。

+ このアプリは、ボタンをクリックすると/roll APIルートからデータを取得します。src/routes/roll/+server.jsファイルを追加して、そのルートを作成します：

+ ボタンをクリックすると動作するようになった。

+ リクエストハンドラはResponseオブジェクトを返す必要があります。APIルートからJSONを返すのが一般的なので、SvelteKitはこれらのレスポンスを生成する便利な関数を提供しています：

```js
//src/routes/roll/+server.js
import { json } from '@sveltejs/kit';

// メソッド名が全て大文字になっている!!
export function GET() {
  const number = Math.floor(Math.random() * 6) + 1;

  // json形式
  return json(number);
}
```

```svelte
<script>
  let number;

  // Q: 例外処理はしなくても良い?
  async function roll() {
    const response = await fetch('/roll');
    number = await response.json();
  }
</script>

<button on:click={roll}>Roll the dice</button>

{#if number !== undefined}
  <p>You rolled a {number}</p>
{/if}
```

// Next.jsみたいに、api/hogeみたいな形式で呼び出せる
// Q: Backendも同じ言語で実装できると考えれば良い?

#### POST handlers

+ POSTのように、データを変更するハンドラを追加することもできます。ほとんどの場合、代わりにフォームアクションを使うべきです - より少ないコードしか書かずに済みますし、JavaScriptなしで動作するので、より耐障害性が高くなります。

+ Add a todo' <input>のキーダウンイベントハンドラの中で、データをサーバーにpostしてみましょう：

```svelte
// src/routes/+page.svelte
<script>
  export let data;
</script>

<div class="centered">
  <h1>todos</h1>

  <label>
    add a todo:
    <input
      type="text"
      autocomplete="off"
      on:keydown={async (e) => {
        if (e.key === 'Enter') {
          const input = e.currentTarget;
          const description = input.value;

          // fetchでAPIを操作
          // method + bodyに入れる内容 + ヘッダを指定
          const response = await fetch('/todo', {
            method: 'POST',
            body: JSON.stringify({ description }),
            headers: {
              'Content-Type': 'application/json',
            },
          });

          // レスポンスの内容からデータを取り出す
          const { id } = await response.json();

          // TODOリストの更新
          data.todos = [
            ...data.todos,
            {
              id,
              description,
            },
          ];

          // Q: 空にしているのはなぜ?
          // Q: 入力後に残っている問題がある?
          input.value = '';
        }
      }}
    />
  </label>

  <ul class="todos">
    {#each data.todos as todo (todo.id)}
      <li>
        <label>
          <input
            type="checkbox"
            checked={todo.done}
            on:change={async (e) => {
              const done = e.currentTarget.checked;
              // TODO handle change
            }}
          />
        </label>
        <span>{todo.description}</span>
        <button
          aria-label="Mark as complete"
          on:click={async (e) => {
            // TODO handle delete
          }}
        />
      </li>
    {/each}
  </ul>
</div>

<style>
  ...
</style>
```

// この辺りから急に難しくなってきたように感じる
// サンプルを写経して、なんとなく実現したいことはわかるが、自分で実装するとなるとできるか怪しい
// ブラウザで書き換えることを想定しているせいか、予告なしに指定されたファイル以外の内容がしれっと微妙に書き換えられており、差分をチェックするのにかなり時間が掛かってフラストレーションがたまる。

+ ここでは、ユーザーのクッキーからuseridを使用して、/todo APIルートにJSONを投稿し、レスポンスとして新しく作成されたTodoのidを受け取っています。

+ src/routes/todo/+server.jsファイルに、src/lib/server/database.jsのcreateTodoを呼び出すPOSTハンドラを追加して、/todoルートを作成します：

```js
import { json } from '@sveltejs/kit';
import * as database from '$lib/server/database.js';

export async function POST({ request, cookies }) {
  const { description } = await request.json();
  const userid = cookies.get('userid');
  // CRUDを呼び出し
  const { id } = await database.createTodo(userid, description);

  // jsonで返したい値 + status code
  return json({ id }, { status: 201 });
}
```

+ ロード関数やフォームアクションと同様に、リクエストは標準的なRequestオブジェクトです; await request.json()は、イベントハンドラから投稿したデータを返します。

+ 201のCreatedステータスと、データベースに新しく生成されたTodoのidを持つレスポンスを返している。イベントハンドラに戻って、これを使用してページを更新できます：

+ Note: ページをリロードしても同じ結果が得られるような方法でしか、データを変異させるべきではありません。

#### Other handlers

+ 同様に、他のHTTP動詞のハンドラも追加できる。src/routes/todo/[id]/+server.jsファイルを作成し、/todo/[id] ルートを追加します。

// idが必要になるため、別ファイルで定義

+ ブラウザに実際のデータを返す必要はないので、204 No Contentステータスの空のResponseを返します。

```js
// src/routes/todo/[id]/+server.js
// ディレクトリの構造はNext.jsとよく似ている
import * as database from '$lib/server/database.js';

// メソッド名は大文字と決まっている?
export async function PUT({ params, request, cookies }) {
  const { done } = await request.json();
  const userid = cookies.get('userid');

  // DBのCRUDを呼び出すだけ
  await database.toggleTodo({ userid, id: params.id, done });
  return new Response(null, { status: 204 });
}

export async function DELETE({ params, cookies }) {
  const userid = cookies.get('userid');

  await database.deleteTodo({ userid, id: params.id });
  return new Response(null, { status: 204 });
}
```

+ これで、イベントハンドラ内でこのエンドポイントとやりとりできるようになった：

```svelte
  <ul class="todos">
    {#each data.todos as todo (todo.id)}
      <li>
        <label>
          <input
            type="checkbox"
            checked={todo.done}
            on:change={async (e) => {
              // チェックボックスの状態を取得
              const done = e.currentTarget.checked;

              // GETやPOSTと同様にfetchでAPIを叩く
              // idを指定しているところが異なる
              await fetch(`/todo/${todo.id}`, {
                method: 'PUT',
                body: JSON.stringify({ done }),
                headers: {
                  'Content-Type': 'application/json',
                },
              });
            }}
          />
        </label>
        <span>{todo.description}</span>
        <button
          aria-label="Mark as complete"
          on:click={async (e) => {
            await fetch(`/todo/${todo.id}`, {
              method: 'DELETE',
            });

            // 削除したデータ以外を残す
            data.todos = data.todos.filter((t) => t !== todo);
          }}
        />
      </li>
    {/each}
  </ul>
```

+ Q: fetchでAPIを叩くときにエラー処理はしなくて良いのか?
  + Q: 内部APIだから不要?
  + 例示のため省略しているだけでは?
  + 別のセクションにあるかも

### Stores

#### page

+ 先に学んだように、Svelteストアは個々のコンポーネントに属さないデータを置く場所である。

+ SvelteKitでは、$app/storesモジュールを通じて、page、navigating、updatedの3つの読み取り専用ストアを利用できます。最もよく使うのはpageで、現在のページに関する情報を提供します：
  + url - 現在のページのURL。
  + params - 現在のページのパラメータ
  + route - 現在のルートを表す id プロパティを持つオブジェクトです。
  + status - 現在のページの HTTP ステータスコード。
  + error - 現在のページのエラーオブジェクト（もしあれば）。
  + data - 現在のページのデータで、すべてのロード関数の戻り値を組み合わせます。
  + form - フォームアクションから返されるデータ

+ 他のストアと同様に、その名前の前に$シンボルを付けることで、コンポーネント内でその値を参照することができます。たとえば、現在のパス名を$page.url.pathnameとしてアクセスすることができます：

```svelte
// src/routes/+layout.svelte
<script>
  import { page } from '$app/stores';
</script>

<nav>
  <a href="/" aria-current={$page.url.pathname === '/'}>home</a>
  <a href="/about" aria-current={$page.url.pathname === '/about'}>about</a>
  <a href="/blog" aria-current={$page.url.pathname === '/blog'}>blog</a>
  <a href="/a/deeply/nested/route" aria-current={$page.url.pathname === '/a/deeply/nested/route'}
    >a deeply nested route</a
  >
</nav>

<slot />

```

+ Q: どういうときに使うのか、イマイチ分かっていないかも

#### navigating

+ ナビゲーション・ストアは、現在のナビゲーションを表す。ナビゲーションが開始されると - リンクのクリック、戻る/進むナビゲーション、プログラムによるgoto - ナビゲーションの値は、以下のプロパティを持つオブジェクトになります：
  + from、to - params、route、urlプロパティを持つオブジェクト。
  + type - ナビゲーションのタイプ、例えば link、popstate、goto。

+ Note: 完全なタイプ情報については、ナビゲーションのドキュメントをご覧ください。

+ これは、長時間実行されるナビゲーションの読み込みインジケータを表示するために使用できます。この演習では、src/routes/+page.server.jsとsrc/routes/about/+page.server.jsの両方に人工的な遅延があります。src/routes/+layout.svelteの内部で、ナビゲートストアをインポートし、ナビバーにメッセージを追加します：

```svelte
<script>
  // インポート
  import { page, navigating } from '$app/stores';
</script>

<nav>
  <a href="/" aria-current={$page.url.pathname === '/'}>home</a>
  <a href="/about" aria-current={$page.url.pathname === '/about'}>about</a>
  <a href="/blog" aria-current={$page.url.pathname === '/blog'}>blog</a>
  <a href="/a/deeply/nested/route" aria-current={$page.url.pathname === '/a/deeply/nested/route'}
    >a deeply nested route</a
  >

  // 追加
  {#if $navigating}
    navigating to {$navigating.to?.url.pathname}
  {/if}
</nav>

<slot />

```

+ Q: どういうときに使うのか、イマイチ分かっていないかも

#### updated

+ 更新されたストアは、ページが最初に開かれてからアプリの新しいバージョンがデプロイされたかどうかに応じて、trueまたはfalseを含みます。これを動作させるには、svelte.config.jsでkit.version.pollIntervalを指定する必要があります。

+ バージョンの変更は開発中ではなく、本番環境でのみ発生します。そのため、このチュートリアルでは$updatedは常にfalseになります。

+ pollIntervalに関係なく、updated.check()を呼び出すことで新しいバージョンを手動でチェックできます。

+ Q: どういうときに使うのか、イマイチ分かっていないかも

### Errors and redirects

+ SvelteKitのエラーには、想定内のエラーと想定外のエラーの2種類があります。

+ 期待されるエラーとは、@sveltejs/kit のエラーヘルパーを使用して作成されたもので、src/routes/expected/+page.server.js のようなものです：

```svelte
// expected
import { error } from '@sveltejs/kit';

export function load() {
  // ステータスコード付き
  throw error(420, 'Enhance your calm');
}
```

+ それ以外のエラー（src/routes/unexpected/+page.server.jsにあるようなエラー）は、予期せぬエラーとして扱われる：

```svelte
export function load() {
  // あえて構文を変えている?
  throw new Error('Kaboom!');
}
```

+ 期待されるエラーを投げるということは、SvelteKitに「心配しないで、ここで何をやっているかわかっているから」と伝えていることになります。これとは対照的に、予期しないエラーはアプリにバグがあると見なされます。予期しないエラーがスローされると、そのメッセージとスタックトレースがコンソールに記録されます。

+ Note: 後の章では、handleErrorフックを使ってカスタム・エラー処理を追加する方法を学ぶ。

+ このアプリのリンクをクリックすると、重要な違いに気づくだろう。期待されるエラーメッセージがユーザーに表示されるのに対し、予期せぬエラーメッセージは再編集され、一般的な「内部エラー」メッセージと500ステータスコードに置き換えられるのだ。エラーメッセージには機密データが含まれている可能性があるからです。

#### Error pages

+ ロード関数内で何か問題が発生すると、SvelteKitはエラーページをレンダリングします。

+ デフォルトのエラーページはやや味気ないものです。src/routes/+error.svelteコンポーネントを作成することでカスタマイズできます：

```svelte
<script>
  // 前のセクションで紹介されていたStoresが使われている
  import { page } from '$app/stores';
  import { emojis } from './emoji.js';
</script>

<h1>{$page.status} {$page.error?.message}</h1>
<span>
  {emojis[$page.status] ?? emojis[500]}
</span>
```

+ error.svelteコンポーネントがルートである+layout.svelte内にレンダリングされていることに注目してください。より詳細な+error.svelteの境界を作成することができます：

```svelte
// src/routes/expected/+error.svelte
// ページごとに独立に指定できる
// src/route/+error.svelteの内容が上書きされている
<h1>this error was expected</h1>
```

+ このコンポーネントは/expectedに対してレンダリングされ、ルートであるsrc/routes/+error.svelteページは発生したその他のエラーに対してレンダリングされます。

#### Fallback errors



## 疑問点

+ Viteはどんな技術?
  + SvelteKitにデフォルトで組み込まれているのはなぜ?
+ SvelteKitは、どこまでカバーしている?
  + フロントエンドのみ? or 簡単なAPIなら利用可? / APIは別のFWの方が望ましい?
    + +page.server.js(ts)でサーバ側の処理をしているように思うが、正しく理解できている?
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
