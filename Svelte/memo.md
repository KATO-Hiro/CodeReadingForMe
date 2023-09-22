# Svelte

## 現状

+ React / Next.jsで小さいなサンプルappを作っているところ。
  + 実現したいことに対して、学習が必要な項目が多いと感じた
    + 単に理解できていないことが多いだけかもしれない
+ ある記事を読んで、同等の機能が楽に実装できると知った。

+ 。

## 目標

+ Next.jsプロジェクトをSvelteに書き換えてみる。

+ 。

## 技術選定の理由

+ React / Next.jsの概念を引き継ぎつつ、学習コストが比較的低い。

+ シンプルに書けるらしい。

+ メタフレームワークのSvelteKitもver1.0となり実用的になったらしい。

## Svelteとは?

+ 英単語的な意味: ほっそりとした、すらりとした、しなやかな
  + Reactが肥大化していることへの皮肉?

+ 宣言的なコンポーネントを受け取り、DOMを外科的に更新する効率的なJavaScriptに変換するコンパイラ

+ 特徴
  + 可能な限り多くの作業をブラウザからビルドステップにシフトします。手作業による最適化はもう必要ありません。
  + HTML、CSS、JavaScriptを使って、息をのむほど簡潔なコンポーネントを書きましょう。そして、あなたのアプリケーション・バンドルも小さくなります。
  + 組み込みのスコープ付きスタイリング、ステート管理、モーション・プリミティブ、フォーム・バインディングなど、必要最低限のために npm を探し回る時間を無駄にすることはありません。すべてここにあります。

+ SvelteのコンポーネントはHTMLの上に構築されています。データを追加するだけ

### サンプル

+ Hello World

```svelte
<!-- App.svelte -->
// これだけでHello Worldが出力される
// Reactなら、関数コンポーネントを作って、JSX記法でたくさん書く必要があった
<script>
    // 動的に更新する部分を記述する感じ?
    let name = 'world'
</script>

// {}で変数の内容を展開できる
<h1>Hello, {name}!</h1>


// JS outputを見ると、Reactで書かれている内容に相当するものが出力されている
```

+ Scoped CSS
  + CSSはデフォルトでコンポーネント・スコープに対応しているため、スタイルの衝突や特異性の争いはもう起こらない。あるいは、お気に入りのCSS-in-JSライブラリを使うこともできます。
  + Q: CSSの有効範囲を限定できると思えば良い?

```svelte
<!-- App.svelte -->
<script>
    import Nested from './Nested.svelte'
</script>

// このpタグにだけスタイルが適用される
<p>These styles...</p>
// この書き方は、Reactのコンポーネントの書き方に近い
// 外部からインポートした以下のコンポーネントにはスタイルが適用されていない
<Nested />

<style>
    p {
		color: purple;
		font-family: 'Comic Sans MS', cursive;
        // Q: emとは?サイズの単位?
		font-size: 2em;        
    }
</style>


<!-- Nested.svelte -->
<p>...don't affect this element</p>
```

+ Reactivity

```svelte
// ローカル変数に代入することで、効率的できめ細かな更新をトリガーする。あとはコンパイラがやってくれる。
// useStateを明示的に書かなくても良い(コンパイラが相当する構文に変換してくれている!!!)
<script>
    let count = 0;

    function handleClick() {
        count += 1;
    }
</script>

<button onClick={handleClick}>
    Clicked {count}
    {count === 1 ? 'time' : 'times'}
</button>
```

+ Transitions
  + フレームワークに組み込まれたパワフルでパフォーマンスの高いトランジション・エンジンで、美しいUIを構築できます

```svelte
<script>
	import { quintOut } from 'svelte/easing';
	import { fade, draw, fly } from 'svelte/transition';
	import { expand } from './custom-transitions.js';
	import { inner, outer } from './shape.js';

	let visible = true;
</script>

{#if visible}
	<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 103 124">
		<g out:fade={{ duration: 200 }} opacity="0.2">
			<path
				in:expand={{ duration: 400, delay: 1000, easing: quintOut }}
				style="stroke: #ff3e00; fill: #ff3e00; stroke-width: 50;"
				d={outer}
			/>
			<path in:draw={{ duration: 1000 }} style="stroke:#ff3e00; stroke-width: 1.5" d={inner} />
		</g>
	</svg>

	<div class="centered" out:fly={{ y: -20, duration: 800 }}>
		{#each 'SVELTE' as char, i}
			<span in:fade|global={{ delay: 1000 + i * 150, duration: 800 }}>{char}</span>
		{/each}
	</div>
{/if}

<label>
	<input type="checkbox" bind:checked={visible} />
	toggle me
</label>

<link href="https://fonts.googleapis.com/css?family=Overpass:100,400" rel="stylesheet" />

<style>
	svg {
		width: 100%;
		height: 100%;
	}

	path {
		fill: white;
		opacity: 1;
	}

	label {
		position: absolute;
		top: 1em;
		left: 1em;
	}

	.centered {
		font-size: 20vw;
		position: absolute;
		left: 50%;
		top: 50%;
		transform: translate(-50%, -50%);
		font-family: 'Overpass';
		letter-spacing: 0.12em;
		color: #676778;
		font-weight: 400;
	}

	.centered span {
		will-change: filter;
	}
</style>
```

## Part1: Basic Svelte

### Introduction

#### Welcome to Svelte

##### Svelteとは?

+ ウェブアプリケーションを構築するためのツール。他のユーザー・インターフェイス・フレームワークと同様に、マークアップ、スタイル、ビヘイビアを組み合わせたコンポーネントから宣言的にアプリを構築することができる
+ 小さく効率的なJavaScriptモジュールにコンパイルされ、従来のUIフレームワークに関連するオーバーヘッドを排除
+ できること
  + アプリ全体を構築
  + 既存のコードベースに段階的に追加
  + コンポーネントをスタンドアロンパッケージとしてリリース

##### How to use this tutorial

+ 前提として、HTML, CSS and JavaScriptの基礎的な知識が必要

+ チュートリアルの構成
  + Basic Svelte (you are here)
  + Advanced Svelte
  + Basic SvelteKit
  + Advanced SvelteKit

+ 各セクションには練習問題がある

#### Your first component

+ アプリケーションは1つ以上のコンポーネントから構成されます。
  + コンポーネントとは、.svelteファイルに記述された、HTML、CSS、JavaScriptをカプセル化した、再利用可能な自己完結型のコードブロック

##### Adding data

```svelte
<script>
  // タグ内部で変数を宣言
  let name = 'Svelte';
</script>

// {}で上記の変数にアクセス + 内部でJSコードを書ける
<h1>Hello {name.toUpperCase()}!</h1>
```

#### Dynamic attributes

+ 中括弧を使って要素の属性を制御できる

```svelte
<script>
  let src = '/image.gif';
  let name = 'Rick Astley';
</script>

// 属性={値}の形式で指定できる
// 文字列の内部でも{}を使うと宣言した変数にアクセスできる
// <img /> Reactのコンポーネント書き方とほぼ同じに見える
<img src={src} alt="{name} dances." />


// 名前と属性が同じ場合は省略記法が用意されている
// 以下のサンプルでは、「src=」が不要に
<img {src} alt="{name} dances." />
```

#### Styling

+ HTMLと同じように、<style>タグをコンポーネントに追加することができる。
+ スタイルの有効な範囲がコンポーネント内に限定されており、誤ってアプリの他の場所で要素のスタイルを変更してしまうことはない

```svelte
<p>This is a paragraph.</p>

<style>
  p {
    color: goldenrod;
    font-family: 'Comic Sans MS', cursive;
    font-size: 2em;
  }
</style>
```

#### Nested components

+ 他のファイルからコンポーネントをインポートして、マークアップに含められる

```svelte
<script>
  // JSの文法を使って、別ファイルのコンポーネントをインポート
  import Nested from './Nested.svelte';
</script>

<p>This is a paragraph.</p>
// HTML要素と区別するためコンポーネント名は、常に大文字
// Reactのように<Foo />形式で記述
// p属性を持っているが、App.svelteのスタイルは適用されない
<Nested />

<style>
	p {
		color: goldenrod;
		font-family: 'Comic Sans MS', cursive;
		font-size: 2em;
	}
</style>
```

#### HTML tags

+ 通常、文字列はプレーン・テキストとして挿入され、<や>といった文字は特別な意味を持たない
+ しかし、HTMLを直接コンポーネントにレンダリングする必要がある場合もある。
+ {@html ...} tagを使う

```svelte
<script>
	let string = `this string contains some <strong>HTML!!!</strong>`;
</script>

<p>{@html string}</p>
```

+ 注意:
  + Svelteは、{@html ...}内の式をDOMに挿入する前にサニタイズ処理を行わない。
  + クロスサイト・スクリプティング（XSS）攻撃を回避するため、手動でエスケープ処理をする必要がある

### Reactivity

#### Assignments

+ DOMをアプリケーションの状態と同期させるための強力なリアクティブ・システムが中核にある
+ イベント・ハンドラの作成

```svelte
<script>
  // ReactのようなuseStateが不要になっている!!!
	let count = 0;

  // 必要な関数をJSで書くだけで、useStateのsetStateに相当するものを更新しなくて良さそう
	function increment() {
		count += 1;
	}
</script>

// ReactだとonEventであるのに対して、on:eventであるところが異なる
// HTMLタグ + JSの文法で記述できる
<button on:click={increment}>
	Clicked {count}
	{count === 1 ? 'time' : 'times'}
</button>
```

#### Declarations

+ コンポーネントの状態が変更されたびに、自動的にDOMを更新

+ リアクティブステートメントが宣言されていない変数への代入のみで構成されている場合、Svelteは自動的にlet宣言を追加

+ リアクティブな値は、複数回参照する必要があるときや、他のリアクティブな値に依存する値があるときに、特に価値が高くなる

+ 注意: リアクティブ宣言とステートメントは、他のスクリプトコードの後で、コンポーネントのマークアップがレンダリングされる前に実行される

```svelte
<script>
	let count = 0;
  // $: let doubled = count * 2;と同じ
  // かなり異質に見えるが、「参照する値が変わるたびにこのコードを再実行する」という意味に解釈される 
  // Q: あえて異質な書き方をすることで注意を促している? (or jQueryっぽい感じもする)
  // ReactのuseEffectに相当?
	$: doubled = count * 2;

	function increment() {
		count += 1;
	}
</script>

<button on:click={increment}>
	Clicked {count}
	{count === 1 ? 'time' : 'times'}
</button>

<p>{count} doubled is {doubled}</p>
```

### Statements

+ 任意のステートメントをリアクティブに実行することもできる

```svelte
$: consol.log();

// ブロックでstatementのグループ化も可能
$: {
  // do something
}

// if blocksも利用できる
$: if (condition) {
  // do something
}
```

```svelte
<script>
	let count = 0;

  // Note: ""ではなく、``を使う
	$: {
		console.log(`the count is ${count}`);
    console.log(`this will also be logged whenever count changes`);
	}
		
	function handleClick() {
		count += 1;
	}
</script>

<button on:click={handleClick}>
	Clicked {count}
	{count === 1 ? 'time' : 'times'}
</button>


// if blocksの例
<script>
	let count = 0;

  $: if (count >= 10) {
		alert('count is dangerously high!');
		count = 0;
	}
	
	function handleClick() {
		count += 1;
	}
</script>

<button on:click={handleClick}>
	Clicked {count}
	{count === 1 ? 'time' : 'times'}
</button>
```

#### Updating arrays and objects

+ Svelteのリアクティビティは代入によってトリガーされるため、pushやspliceのような配列メソッドを使用しても、自動的に更新は行われない。

```svelte
// before
function addNumber() {
  // このままだと更新されない
  numbers.push(numbers.length + 1);
  // 更新するためには以下のような冗長な記述が必要になる
  numbers = numbers;
}


// after: よりシンプルな記法
// pop、shift、unshift、spliceに置き換えることもできる
function addNumber() {
  // [...更新前の配列全体、更新後の要素]
  numbers = [...numbers, numbers.length + 1];
}
```

+ 配列やオブジェクトのプロパティへの代入（obj.foo += 1やarray[i] = xなど）は、値そのものへの代入と同じように機能

```svelte
function addNumber() {
  // 数列[位置] = 代入する値
  numbers[numbers.length] = numbers.length + 1;
}
```

+ 更新された変数名は代入の左側に表示されなければならない

```svelte
const foo = obj.foo;
foo.bar = 'baz';
// 必要
obj = obj
```

```svelte
<script>
	let numbers = [1, 2, 3, 4];

	function addNumber() {
		numbers = [...numbers, numbers.length + 1];
	}

	$: sum = numbers.reduce((total, currentNumber) => total + currentNumber, 0);
</script>

<p>{numbers.join(' + ')} = {sum}</p>

<button on:click={addNumber}>
	Add a number
</button>
```

### Props

#### Declaring props

+ これまでは、内部ステート、つまり、与えられたコンポーネント内でのみアクセス可能な値のみを扱ってきた。

+ 実際のアプリケーションでは、あるコンポーネントからその子コンポーネントにデータを渡す必要があります。そのためには、プロパティを宣言する必要があります。Svelteでは、これをexportキーワードで行います。Nested.svelteコンポーネントを編集します：

```svelte
// App.svelte
<script>
	import Nested from './Nested.svelte';
</script>

// Reactのコンポーネントと同じように、引数に相当する機構で値を渡せる
<Nested answer={42} />

// Nested.svelte
<script>
  // JSのexportはやや異なる
  // 変数宣言とexportを同時にしている感じにも思える
	export let answer;
</script>

<p>The answer is {answer}</p>
```

#### Default values

// Nested
```svelte
<script>
  // デフォルト値を指定できる
	export let answer = 'a mystery';
</script>

<p>The answer is {answer}</p>


// App
<script>
	import Nested from './Nested.svelte';
</script>

<Nested answer={42} />
// 引数を省略すると、デフォルト値が使われる
<Nested />
```

#### Spread props

```svelte
// App
<script>
	import PackageInfo from './PackageInfo.svelte';

	const pkg = {
		name: 'svelte',
		speed: 'blazing',
		version: 4,
		website: 'https://svelte.dev'
	};
</script>

// {...arg}とすることで、propsをまとめて渡せる + 書き忘れがなくなり、シンプルに記述できる
<PackageInfo
	{...pkg}
/>


// PackageInfo
<script>
	export let name;
	export let version;
	export let speed;
	export let website;

	$: href = `https://www.npmjs.com/package/${name}`;
</script>

<p>
	The <code>{name}</code> package is {speed} fast. Download version {version} from
	<a {href}>npm</a> and <a href={website}>learn more here</a>
</p>
```

### Logic

#### If blocks

+ 条件分岐やループのようなロジックを表現する方法
  + HTML: なし
  + Svelte: あり

// Ruby、PHP、PythonのFWにあるような埋め込み記法に近い?
{#if condition}
  // do somthing
{/if}

```svelte
<script>
	let count = 0;

	function increment() {
		count += 1;
	}
</script>

<button on:click={increment}>
	Clicked {count}
	{count === 1 ? 'time' : 'times'}
</button>

{#if count > 10}
	<p>{count} is greater than 10</p>
{/if}	
```

#### Else blocks、Else-if blocks

+ JSっぽく書ける
  + 個人的にはかなり奇妙に感じる/違和感のある記法

```svelte
// ブロックの始まりは「#」、「/」が閉じる、「:」が継続を表す
{#if condition}
  // do somthing
{:else if condition2}
  // do somthing
{:else}
  // do somthing
{/if}
```

#### Each blocks

+ リスト形式のデータを扱いたいときに使う

```svelte
// 配列、配列のようなオブジェクト
// #each 配列(相当のオブジェクト) as foo
{#each foos as foo}
  // do something
{/each}


// 第2引数で、インデックスも取れる
{#each foos as foo, i}
  // do something
  // {i + 1}
{/each}
```

```svelte
// ボタンをクリックすると、選択したボタンと同じ色の文字に変わる
// 次のプロジェクトで作るUIにかなり参考になりそう
// 状態を管理、選択したボタンをクリックすると指定した

<script>
	const colors = ['red', 'orange', 'yellow', 'green', 'blue', 'indigo', 'violet'];
	let selected = colors[0];
</script>

<h1 style="color: {selected}">Pick a colour</h1>

<div>
	{#each colors as color, i}
	  <button
		  aria-current={selected === color}
		  aria-label={color}
		  style="background: {color}"
		  on:click={() => selected = color}
	  >{i + 1}</button>
	{/each}					 
</div>

<style>
	h1 {
		transition: color 0.2s;
	}

	div {
		display: grid;
		grid-template-columns: repeat(7, 1fr);
		grid-gap: 5px;
		max-width: 400px;
	}

	button {
		aspect-ratio: 1;
		border-radius: 50%;
		background: var(--color, #fff);
		transform: translate(-2px,-2px);
		filter: drop-shadow(2px 2px 3px rgba(0,0,0,0.2));
		transition: all 0.1s;
	}

	button[aria-current="true"] {
		transform: none;
		filter: none;
		box-shadow: inset 3px 3px 4px rgba(0,0,0,0.2);
	}
</style>
```

#### Keyed each blocks

+ デフォルトでは、eachブロックの値を変更すると、ブロックの末尾の項目が追加・削除され、変更された値が更新される。
+ 各ブロックに一意な識別子（または "キー"）を指定する

```svelte
// App
<script>
	import Thing from './Thing.svelte';

	let things = [
		{ id: 1, name: 'apple' },
		{ id: 2, name: 'banana' },
		{ id: 3, name: 'carrot' },
		{ id: 4, name: 'doughnut' },
		{ id: 5, name: 'egg' }
	];

	function handleClick() {
		things = things.slice(1);
	}
</script>

<button on:click={handleClick}>
	Remove first thing
</button>

// #each foos as foo (foo.id)のようにキーを指定
{#each things as thing (thing.id)}
	<Thing name={thing.name} />
{/each}


// Thing
<script>
	const emojis = {
		apple: '🍎',
		banana: '🍌',
		carrot: '🥕',
		doughnut: '🍩',
		egg: '🥚'
	};

	// the name is updated whenever the prop value changes...
	export let name;

	// ...but the "emoji" variable is fixed upon initialisation
	// of the component because it uses `const` instead of `$:`
	const emoji = emojis[name];
</script>

<p>{emoji} = {name}</p>
```

#### Await blocks

+ ほとんどのWebアプリケーションでは、非同期データを扱う必要があります。Svelteを使えば、マークアップの中で直接プロミスの値を待つことが簡単にできます：

```svelte
<script>
	import { getRandomNumber } from './utils.js';

	let promise = getRandomNumber();

	function handleClick() {
		promise = getRandomNumber();
	}
</script>

<button on:click={handleClick}>
	generate random number
</button>

// promiseから値を取得 + エラー処理
// try-catchの構文に近いが、最初にwaitingの状態を書けるので楽そう
{#await promise}
	<p>...waiting</p>
{:then number}
  <p>The number is {number}</p>
{:catch error}
	<p style="color: red">{error.message}</p>
{/await}


// rejectされない場合の省略形
// {#await promise_obj then value}の形式で書ける
{#await promise then number}
  // do something
{/await}
```

### Event

#### DOM events

+ on: ディレクティブを使えば、要素上のあらゆるDOMイベント（クリックやポインタ移動など）を拾える

```svelte
<script>
	let m = { x: 0, y: 0 };

	function handleMove(event) {
		m.x = event.clientX;
		m.y = event.clientY;
	}
</script>

// on: event={handleFoo}
// handleFooで処理を実装
<div on:pointermove={handleMove}>
	The pointer is at {m.x} x {m.y}
</div>

<style>
	div {
		position: fixed;
		left: 0;
		top: 0;
		width: 100%;
		height: 100%;
		padding: 1rem;
	}
</style>
```

#### Inline handlers

+ 個人的には可読性が低くなるように思うので、使わない方がいいのでは?

+ Note: 直に書いてもパフォーマンスが低下することはないらしい

#### Event modifiers

+ DOMイベントハンドラには、その振る舞いを変更する修飾子を付けることができます。
  + <tag on:event|modifier>
  + <tag on:event|modifier1|modifier2>のようにつなぐこともできる

+ modifierの一覧
  + preventDefault - ハンドラを実行する前に event.preventDefault() をコールします。クライアントサイドのフォーム処理などに便利です。
  + stopPropagation - event.stopPropagation()を呼び出し、イベントが次の要素に到達するのを防ぎます。
  + passive - タッチ/ホイールイベントのスクロールパフォーマンスを向上させます。
  + nonpassive - 明示的にpassive: falseを設定します。
  + capture - バブリングフェーズではなく、キャプチャフェーズでハンドラを起動します。
  + once - 初回実行後にハンドラを削除します。
  + self - event.targetが要素自身である場合にのみハンドラをトリガする。
  + trusted - event.isTrustedがtrueの場合のみハンドラをトリガします。これは、JavaScriptがelement.dispatchEvent(...)を呼び出したのではなく、ユーザーのアクションによってイベントがトリガされたことを意味します。

#### Component events

+ コンポーネントはイベントをディスパッチすることもできる。そのためには、イベント・ディスパッチャを作成する必要があります。Inner.svelteを更新してください：

```svelte
// App
<script>
	import Inner from './Inner.svelte';

	function handleMessage(event) {
		alert(event.detail.text);
	}
</script>

// Innerで定義したdispatchの'message'をon:eventに記述
<Inner on:message={handleMessage} />


// Inner
<script>
  // インポート
  import { createEventDispatcher } from 'svelte';

  // インスタンス?を作成
  // コンポーネントが最初にインスタンス化されたときに呼び出されなければなりません
  const dispatch = createEventDispatcher();
	
	function sayHello() {
		dispatch('message', {
			text: 'Hello!'
		});
	}
</script>

<button on:click={sayHello}>
	Click to say hello
</button>
```

#### Event forwarding

+ 深くネストしたコンポーネントのイベントをリッスンしたい場合は、中間コンポーネントがイベントを転送しなければなりません。

+ 階層構造にあるコンポーネントにイベントを転送するときに使っている?

```svelte
<script>
	import Inner from './Inner.svelte';
</script>

// 省略形
// 名前を指定しない場合は、全て`message`イベントを転送
<Inner on:message/>
```

#### DOM event forwarding

+ イベント転送はDOMイベントでも機能する

```svelte
// App
<script>
	import BigRedButton from './BigRedButton.svelte';
	import horn from './horn.mp3';

	const audio = new Audio();
	audio.src = horn;

	function handleClick() {
		audio.play();
	}
</script>

<BigRedButton on:click={handleClick} />


// BigRedButton
// イベントを親コンポーネントから転送
// on:clickを書くだけ
<button on:click>
	Push
</button>

<style>
	button {
		font-size: 1.4em;
		width: 6em;
		height: 6em;
		border-radius: 50%;
		background: radial-gradient(circle at 25% 25%, hsl(0, 100%, 50%) 0, hsl(0, 100%, 40%) 100%);
		box-shadow: 0 8px 0 hsl(0, 100%, 30%), 2px 12px 10px rgba(0,0,0,.35);
		color: hsl(0, 100%, 30%);
		text-shadow: -1px -1px 2px rgba(0,0,0,0.3), 1px 1px 2px rgba(255,255,255,0.4);
		text-transform: uppercase;
		letter-spacing: 0.05em;
		transform: translate(0, -8px);
		transition: all 0.2s;
	}

	button:active {
		transform: translate(0, -2px);
		box-shadow: 0 2px 0 hsl(0, 100%, 30%), 2px 6px 10px rgba(0,0,0,.35);
	}
</style>

```

### Bindings

#### Text inputs

+ 親コンポーネントは子コンポーネントにpropsを設定でき、コンポーネントは要素に属性を設定できますが、その逆はできません。

+ そのルールを破ることが役に立つこともある。
  + <input>要素に対して、bind:valueディレクティブを使う
  + nameの値が変更されると入力値が更新されるだけでなく、入力値が変更されるとnameも更新されることを意味する。
  + // おそらく双方向バインディングのことを指していると思われる
  + Q: 副作用(= bindを使うことによる悪影響があるなら)は心配しなくてもいい?

```svelte
<script>
	let name = 'world';
</script>

// bind:attr={value}のように、属性にbindを追加するだけ
// on:inputハンドラを書かなくてもいい
<input bind:value={name} />

<h1>Hello {name}!</h1>
```

#### Numeric inputs

+ DOMでは、すべてが文字列です。type="number "やtype="range "といった数値入力を扱う場合、input.valueを使用する前にbindの使用を強制することを忘れてはならない

```svelte
<script>
	let a = 1;
	let b = 2;
</script>

// 基本的には該当する属性にbindを追加するだけ
<label>
	<input type="number" bind:value={a} min="0" max="10" />
	<input type="range" bind:value={a} min="0" max="10" />
</label>

<label>
	<input type="number" bind:value={b} min="0" max="10" />
	<input type="range" bind:value={b} min="0" max="10" />
</label>

<p>{a} + {b} = {a + b}</p>
```

#### Checkbox inputs

+ チェックボックスは状態の切り替えに使われる。input.valueにバインドする代わりに、input.checkedにバインドする：

```svelte
<script>
	let yes = false;
</script>

// checkedにbindを追加
<label>
	<input type="checkbox" bind:checked={yes} />
	Yes! Send me regular email spam
</label>

{#if yes}
	<p>
		Thank you. We will bombard your inbox and sell
		your personal details.
	</p>
{:else}
	<p>
		You must opt in to continue. If you're not
		paying, you're the product.
	</p>
{/if}

<button disabled={!yes}>Subscribe</button>
```

#### Select bindings

+ bind:valueは<select>要素でも使えます

+ Note: <option>の値は文字列ではなくオブジェクトであることに注意してください。

```svelte
<script>
	let questions = [
		{
			id: 1,
			text: `Where did you go to school?`
		},
		{
			id: 2,
			text: `What is your mother's name?`
		},
		{
			id: 3,
			text: `What is another personal fact that an attacker could easily find with Google?`
		}
	];

	let selected;

	let answer = '';

	function handleSubmit() {
		alert(
			`answered question ${selected.id} (${selected.text}) with "${answer}"`
		);
	}
</script>

<h2>Insecurity questions</h2>

// フォームも簡潔に書ける
<form on:submit|preventDefault={handleSubmit}>
	<select
		bind:value={selected}
		// 選択肢が変更されたら、答えもリセットされている
		on:change={() => (answer = '')}
	>
		{#each questions as question}
			<option value={question}>
				{question.text}
			</option>
		{/each}
	</select>

	<input bind:value={answer} />

    // 回答がないときは押せないようになっている
	<button disabled={!answer} type="submit">
		Submit
	</button>
</form>

<p>
	selected question {selected
		? selected.id
		: '[waiting...]'}
</p>
```

#### Group inputs

+ 同じ値に関連するtype="radio "やtype="checkbox "の入力が複数ある場合、value属性と一緒にbind:groupを使うことができます。同じグループ内のradio入力は互いに排他的であり、同じグループ内のcheckbox入力は選択された値の配列を形成します。

+ bind:group={foo}を書くだけ
  + 同じ書き方でも、radioボタンは1つしか選べないのに対して、checkboxは複数選べる

```svelte
<script>
	let scoops = 1;
	let flavours = [];

	const formatter = new Intl.ListFormat('en', { style: 'long', type: 'conjunction' });
</script>

<h2>Size</h2>

{#each [1, 2, 3] as number}
	<label>
		<input
			type="radio"
			name="scoops"
			value={number}
			bind:group={scoops}
		/>

		{number} {number === 1 ? 'scoop' : 'scoops'}
	</label>
{/each}

<h2>Flavours</h2>

{#each ['cookies and cream', 'mint choc chip', 'raspberry ripple'] as flavour}
	<label>
		<input
			type="checkbox"
			name="flavours"
			value={flavour}
			bind:group={flavours}
		/>

		{flavour}
	</label>
{/each}

{#if flavours.length === 0}
	<p>Please select at least one flavour</p>
{:else if flavours.length > scoops}
	<p>Can't order more flavours than scoops!</p>
{:else}
	<p>
		You ordered {scoops} {scoops === 1 ? 'scoop' : 'scoops'}
		of {formatter.format(flavours)}
	</p>
{/if}
```

#### Select multiple

+ <select>要素はmultiple属性を持つことができ、その場合は単一の値を選択するのではなく、配列に入力されます。

+ Note: 値は要素の内容と同じなので、<option>のvalue属性を省略できる

```svelte
<script>
	let scoops = 1;
	let flavours = [];

	const formatter = new Intl.ListFormat('en', { style: 'long', type: 'conjunction' });
</script>

<h2>Size</h2>

{#each [1, 2, 3] as number}
	<label>
		<input
			type="radio"
			name="scoops"
			value={number}
			bind:group={scoops}
		/>

		{number} {number === 1 ? 'scoop' : 'scoops'}
	</label>
{/each}

<h2>Flavours</h2>

<select multiple bind:value={flavours}>
  {#each ['cookies and cream', 'mint choc chip', 'raspberry ripple'] as flavour}
		<option>{flavour}</option>
  {/each}	
</select>


{#if flavours.length === 0}
	<p>Please select at least one flavour</p>
{:else if flavours.length > scoops}
	<p>Can't order more flavours than scoops!</p>
{:else}
	<p>
		You ordered {scoops} {scoops === 1 ? 'scoop' : 'scoops'}
		of {formatter.format(flavours)}
	</p>
{/if}
```

#### Textarea inputs

+ <textarea>要素は、Svelteのテキスト入力と同様の動作をします - bind:valueを使用してください：

+ 名前が一致する場合は、省略形を使うこともできる

```svelte
<script>
	import { marked } from 'marked';
	let value = `Some words are *italic*, some are **bold**\n\n- lists\n- are\n- cool`;
</script>

<div class="grid">
	input
	// bind:value={value}のvalueのように同じ場合は省略できる
	<textarea bind:value></textarea>

	output
	<div>{@html marked(value)}</div>
</div>

<style>
	.grid {
		display: grid;
		grid-template-columns: 5em 1fr;
		grid-template-rows: 1fr 1fr;
		grid-gap: 1em;
		height: 100%;
	}

	textarea {
		flex: 1;
		resize: none;
	}
</style>

```

### Lifecycle

#### onMount

+ すべてのコンポーネントには、作成されたときに始まり、破棄されたときに終わるライフサイクルがある。このライフサイクル中の重要なタイミングでコードを実行できる関数がいくつかあります。最も頻繁に使うのはonMountで、コンポーネントが最初にDOMにレンダリングされた後に実行されます。

```svelte
<script>
    // 1. onMountをインポート
	import { onMount } from 'svelte';
	import { paint } from './gradient.js';

	// 2. コンポーネントがマウントされたときに実行される関数を追加
	onMount(() => {
		// 以降の演習では、document.querySelectorを使わない方法も
		const canvas = document.querySelector('canvas');
		const context = canvas.getContext('2d');

		let frame = requestAnimationFrame(function loop(t) {
			frame = requestAnimationFrame(loop);
			paint(context, t);
		});

		// 3. コンポーネントが破壊された後もループが続いてしまうので、onMountからクリーンアップ関数を返す必要がある
		return () => {
			cancelAnimationFrame(frame);
		};
	});
</script>
```

#### beforeUpdate and afterUpdate

+ beforeUpdate関数は、DOMが更新される直前の作業をスケジュールします。afterUpdate関数はその対になる関数で、DOMがデータと同期した後にコードを実行するために使用します。

+ この2つの関数を一緒に使うと、要素のスクロール位置を更新するなど、純粋なステート駆動型では実現が難しいことを命令的に実行するのに便利です。
  + Q: 必ず同時に使う必要がある?

Note: beforeUpdateはコンポーネントがマウントされる前に実行されるため、プロパティを読み込む前にdivの存在をチェックする必要がある

```svelte
// Elizaチャットbot
<script>
	import Eliza from 'elizabot';
	import {
		beforeUpdate,
		afterUpdate
	} from 'svelte';

	let div;
	// 追加
    let autoscroll = false;
	
	beforeUpdate(() => {
		// 追加
		if (div) {
			const scrollableDistance = div.scrollHeight - div.offsetHeight;
			autoscroll = div.scrollTop > scrollableDistance - 20;
		}
	});

	afterUpdate(() => {
		// 追加
		if (autoscroll) {
			div.scrollTo(0, div.scrollHeight);
		}
	});

	const eliza = new Eliza();
	const pause = (ms) => new Promise((fulfil) => setTimeout(fulfil, ms));

	const typing = { author: 'eliza', text: '...' };

	let comments = [];

	async function handleKeydown(event) {
		if (event.key === 'Enter' && event.target.value) {
			const comment = {
				author: 'user',
				text: event.target.value
			};

			const reply = {
				author: 'eliza',
				text: eliza.transform(comment.text)
			};

			event.target.value = '';
			comments = [...comments, comment];

			await pause(200 * (1 + Math.random()));
			comments = [...comments, typing];

			await pause(500 * (1 + Math.random()));
			comments = [...comments, reply].filter(comment => comment !== typing);
		}
	}
</script>

<div class="container">
	<div class="phone">
		<div class="chat" bind:this={div}>
			<header>
				<h1>Eliza</h1>

				<article class="eliza">
					<span>{eliza.getInitial()}</span>
				</article>
			</header>

			{#each comments as comment}
				<article class={comment.author}>
					<span>{comment.text}</span>
				</article>
			{/each}
		</div>

		<input on:keydown={handleKeydown} />
	</div>
</div>

<style>
	.container {
		display: grid;
		place-items: center;
		height: 100%;
	}

	.phone {
		display: flex;
		flex-direction: column;
		width: 100%;
		height: 100%;
	}

	header {
		display: flex;
		flex-direction: column;
		height: 100%;
		padding: 4em 0 0 0;
		box-sizing: border-box;
	}

	h1 {
		flex: 1;
		font-size: 1.4em;
		text-align: center;
	}

	.chat {
		height: 0;
		flex: 1 1 auto;
		padding: 0 1em;
		overflow-y: auto;
		scroll-behavior: smooth;
	}

	article {
		margin: 0 0 0.5em 0;
	}

	.user {
		text-align: right;
	}

	span {
		padding: 0.5em 1em;
		display: inline-block;
	}

	.eliza span {
		background-color: var(--bg-1);
		border-radius: 1em 1em 1em 0;
		color: var(--fg-1);
	}

	.user span {
		background-color: #0074d9;
		color: white;
		border-radius: 1em 1em 0 1em;
		word-break: break-all;
	}

	input {
		margin: 0.5em 1em 1em 1em;
	}

	@media (min-width: 400px) {
		.phone {
			background: var(--bg-2);
			position: relative;
			font-size: min(2.5vh, 1rem);
			width: auto;
			height: 36em;
			aspect-ratio: 9 / 16;
			border: 0.2em solid #222;
			border-radius: 1em;
			box-sizing: border-box;
			filter: drop-shadow(1px 1px 0px #222) drop-shadow(2px 2px 0px #222) drop-shadow(3px 3px 0px #222)
		}

		.phone::after {
			position: absolute;
			content: '';
			background: #222;
			width: 60%;
			height: 1em;
			left: 20%;
			top: 0;
			border-radius: 0 0 0.5em 0.5em
		}
	}

	@media (prefers-reduced-motion) {
		.chat {
			scroll-behavior: auto;
		}
	}
</style>
```

#### tick

+ tick関数は他のライフサイクル関数とは異なり、コンポーネントが最初に初期化されたときだけでなく、いつでも呼び出すことができます。この関数は、保留中の状態変更がDOMに適用されるとすぐに（または保留中の状態変更がない場合はすぐに）解決されるプロミスを返します。

+ Svelteでコンポーネントの状態を更新しても、すぐにDOMが更新されるわけではありません。その代わりに、次のマイクロタスクまで待機し、他のコンポーネントを含め、適用する必要がある他の変更があるかどうかを確認します。こうすることで、不要な作業を避け、ブラウザがより効率的にバッチ処理を行えるようになります。

+ 例: テキストの範囲を選択してタブキーを押す。<textarea>の値が変更されるため、現在の選択範囲はクリアされ、カーソルは最後までジャンプしてしまいます。これは、tick...をインポートすることで解決できます。

+ そして、handleKeydownの最後にthis.selectionStartとthis.selectionEndをセットする直前に実行する

//```svelte
<script>
  import { tick } from 'svelte';
	
	let text = `Select some text and hit the tab key to toggle uppercase`;

	async function handleKeydown(event) {
		if (event.key !== 'Tab') return;

		event.preventDefault();

		const { selectionStart, selectionEnd, value } = this;
		const selection = value.slice(selectionStart, selectionEnd);

		const replacement = /[a-z]/.test(selection)
			? selection.toUpperCase()
			: selection.toLowerCase();

		text =
			value.slice(0, selectionStart) +
			replacement +
			value.slice(selectionEnd);

		await tick();
		this.selectionStart = selectionStart;
		this.selectionEnd = selectionEnd;
	}
</script>

<textarea
	value={text}
	on:keydown={handleKeydown}
/>

<style>
	textarea {
		width: 100%;
		height: 100%;
		resize: none;
	}
</style>
```

### Stores

#### Writable stores

+ すべてのアプリケーションの状態が、アプリケーションのコンポーネント階層に属するわけではありません。時には、無関係な複数のコンポーネントや、通常のJavaScriptモジュールからアクセスする必要のある値があります。

+ Svelteでは、これをストアで行う。ストアは単純にsubscribeメソッドを持つオブジェクトで、ストアの値が変更されるたびに関係者に通知される。App.svelteでは、countがストアであり、count.subscribeコールバックでcount_valueを設定している。

+ stores.jsを開いてcountの定義を見る。これは書き込み可能なストアであり、subscribeに加えてsetメソッドとupdateメソッドを持っていることを意味する。

```js
// countの定義
import { writable } from 'svelte/store';

export const count = writable(0);
```

//```svelte
// Incrementer
<script>
	import { count } from './stores.js';

	// updateメソッドで更新
	function increment() {
		count.update((n) => n + 1);
	}
</script>

<button on:click={increment}>
	+
</button>


// Resetter
<script>
	import { count } from './stores.js';

	function reset() {
		count.set(0);
	}
</script>

<button on:click={reset}>
	reset
</button>
```

+ Q: グローバルに状態を保持したいときに使って良い? or recoilのような状態管理libを使う or そもそもグローバルに状態を保持するような設計はNot good?

#### Auto-subscriptions

+ 前ページのコードにはバグがある
  + もしコンポーネントが何度もインスタンス化されたり破棄されたりすると、メモリ・リーク

+ ストア名の前に$を付けることで、ストアの値を参照
  + Note: 自動サブスクリプションは、コンポーネントの最上位スコープで宣言された（またはインポートされた）ストア変数に対してのみ機能
  + $countは、イベント・ハンドラやリアクティブ宣言など、<script>のどこでも使える
  + $はストアの値を指すものとみなされる = 事実上の予約文字

//```svelte
<script>
	import { count } from './stores.js';
	import Incrementer from './Incrementer.svelte';
	import Decrementer from './Decrementer.svelte';
	import Resetter from './Resetter.svelte';

</script>

 ストア名の前に$をつけるだけ
<h1>The count is {$count}</h1>

<Incrementer />
<Decrementer />
<Resetter />
```

#### Readable stores

+ 読み取り専用のなストア

//```svelte
// stores.js
import { readable } from 'svelte/store';

// 第1引数: 値がない場合はnull or undefined
// 第2引数: start関数で、setコールバックを受け取り、stop関数を返す
export const time = readable(new Date(), function start(set) {
	// setup / teardownで対になるように
	const interval = setInterval(() => {
		set(new Date());
	}, 1000);

	return function stop() {
		clearInterval(interval);
	};
});


// App
<script>
	import { time } from './stores.js';

	const formatter = new Intl.DateTimeFormat(
		'en',
		{
			hour12: true,
			hour: 'numeric',
			minute: '2-digit',
			second: '2-digit'
		}
	);
</script>

<h1>The time is {formatter.format($time)}</h1>
```

#### Derived stores

+ derivedを使用すると、1つまたは複数の他のストアの値を基にした値を持つストアを作成できます。
  + // サンプルでの使用例は理解できるが、実際にはどのような場面で使うか、あまりイメージできていない

+ Note: 複数の入力ストアからストアを派生させたり、値を返す代わりに明示的に値を設定したりすることも可能です（非同期に値を派生させる場合に便利です）


#### Custom stores

+ オブジェクトがsubscribeメソッドを正しく実装している限り、それはストアである。それ以上は何でもありだ。したがって、ドメイン固有のロジックを持つカスタムストアを作るのはとても簡単だ。
  + メリット: storeのメソッドの詳細を隠すことができる

#### Store bindings

+ ストアが書き込み可能な場合、つまりsetメソッドを持っている場合、ローカル・コンポーネントのステートにバインドできるのと同じように、その値にバインドすることができる。

//```svelte
//App
<script>
	import { name, greeting } from './stores.js';
</script>

<h1>{$greeting}</h1>
// バインディングを追加
<input bind:value={$name} />

// $name += hogeで要素を更新
// name.set($name + hoge)と等価
<button on:click={() => $name += '!'}>
	Add exclamation mark!
</button>
```

```js
import { writable, derived } from 'svelte/store';

export const name = writable('world');

export const greeting = derived(name, ($name) => `Hello ${$name}!`);
```

## Part2: Advanced Svelte

### Motion

#### Tweens

+ さて、基本を学んだところで、次はスヴェルトの高度なテクニックを学ぶ番だ。

+ 値を設定し、DOMが自動的に更新されるのを見るのはクールだ。もっとクールなのは？その値をトゥイーンさせることです。Svelteには、アニメーションを使用して変更を伝える洗練されたユーザーインターフェイスを構築するのに役立つツールが含まれています。

// すぐには使わなさそうなので、必要になったときに見る

#### Springs

+ spring関数はtweenedに代わるもので、値が頻繁に変化する場合によく機能する

// すぐには使わなさそうなので、必要になったときに見る

### Transitions

#### Transition directive

+ DOMへの要素の出入りを優雅に遷移させることで、より魅力的なユーザーインターフェースを作ることができます。Svelteではtransitionディレクティブを使うことで、これを非常に簡単に行うことができます。

// すぐには使わなさそうなので、必要になったときに見る

#### Adding parameters

// すぐには使わなさそうなので、必要になったときに見る

#### In and out

// すぐには使わなさそうなので、必要になったときに見る

#### Custom CSS transitions

// すぐには使わなさそうなので、必要になったときに見る

#### Custom JS transitions

// すぐには使わなさそうなので、必要になったときに見る

#### Transition events

+ トランジションの開始と終了のタイミングを知るのに便利です。Svelteは、他のDOMイベントと同じようにリスニングできるイベントをディスパッチします

// すぐには使わなさそうなので、必要になったときに見る

#### Global transitions

+ 通常、トランジションが要素に適用されるのは、その要素を直接含むブロックが追加または破棄されたときだけです。この例では、リスト全体の可視性を切り替えても、個々のリスト要素にトランジションは適用されません。

// すぐには使わなさそうなので、必要になったときに見る

#### Key blocks

+ キー・ブロックは、式の値が変更されると、その内容を破棄して再作成する。これは、要素がDOMに入ったり出たりするときだけでなく、値が変化するたびにその要素の遷移を再生したい場合に便利

// すぐには使わなさそうなので、必要になったときに見る

#### Deferred transitions

+ Svelteのトランジション・エンジンの特に強力な機能は、トランジションを延期する機能で、複数の要素間で調整することができる。

// すぐには使わなさそうなので、必要になったときに見る

### Animations

#### The animate directive

// すぐには使わなさそうなので、必要になったときに見る

### Actions

#### The use directive

+ アクションは基本的に要素レベルのライフサイクル機能です。以下のような用途に便利
  + サードパーティ・ライブラリとのインターフェイス
  + 遅延ロード画像
  + ツールチップ
  + カスタムイベントハンドラの追加

// すぐには使わなさそうなので、必要になったときに見る

#### Adding parameters

// すぐには使わなさそうなので、必要になったときに見る

### Advanced bindings

#### Contenteditable bindings

// すぐには使わなさそうなので、必要になったときに見る

#### Each block bindings

+ 各ブロック内のプロパティにバインドすることもできる

// すぐには使わなさそうなので、必要になったときに見る

#### Media elements

+ <audio>要素や<video>要素のプロパティにバインドできる

// すぐには使わなさそうなので、必要になったときに見る

#### Dimensions

+ すべてのブロックレベル要素には、clientWidth、clientHeight、offsetWidth、offsetHeightバインディングがあります

// すぐには使わなさそうなので、必要になったときに見る

#### This

+ onMountライフサイクル関数を使ってキャンバスにペイントする方法を学んだ。

+ しかし、この例はバグだらけです。document.querySelector('canvas')を使用しているため、常にページで最初に見つかった<canvas>が返され、私たちのコンポーネントのものではない可能性があります。

+ その代わりに、readonly thisバインディングを使って要素への参照を取得することができる

// すぐには使わなさそうなので、必要になったときに見る

#### Component bindings

+ DOM要素のプロパティにバインドできるように、コンポーネントのpropにもバインドできます。例えば、この<Keypad>コンポーネントのvalueプロパティを、あたかもフォーム要素であるかのようにバインドすることができます：

+ Note: コンポーネント・バインディングの使用は控えめに。コンポーネント・バインディングが多すぎると、アプリケーションのデータの流れを追跡するのが難しくなります。

// すぐには使わなさそうなので、必要になったときに見る

#### Binding to component instances

+ DOM要素にバインドできるのと同じように、bind:thisを使えばコンポーネントのインスタンスそのものにバインドすることができる。

// すぐには使わなさそうなので、必要になったときに見る

### Classes and styles

#### The class directive

+ 他の属性と同様に、JavaScript属性でクラスを指定することができる。

+ 何らかの条件に基づいてクラスを追加したり削除したりするのは、UI開発ではよくあるパターンなので、Svelteにはそれを簡単にするための特別なディレクティブが用意されています。

```svelte
<script>
	let flipped = false;
</script>

<div class="container">
	Flip the card
	<button
		class="card"
		// flippedがtrueのとき、flippedクラスを追加
		class:flipped={flipped}
		on:click={() => flipped = !flipped}
	>
		<div class="front">
			<span class="symbol">♠</span>
		</div>
		<div class="back">
			<div class="pattern"></div>
		</div>
	</button>
</div>

<style>
	.container {
		display: flex;
		flex-direction: column;
		gap: 1em;
		height: 100%;
		align-items: center;
		justify-content: center;
		perspective: 100vh;
	}

	.card {
		position: relative;
		aspect-ratio: 2.5 / 3.5;
		font-size: min(1vh, 0.25rem);
		height: 80em;
		background: var(--bg-1);
		border-radius: 2em;
		transform: rotateY(180deg);
		transition: transform 0.4s;
		transform-style: preserve-3d;
		padding: 0;
		user-select: none;
		cursor: pointer;
	}

	.card.flipped {
		transform: rotateY(0);
	}

	.front, .back {
		display: flex;
		align-items: center;
		justify-content: center;
		position: absolute;
		width: 100%;
		height: 100%;
		left: 0;
		top: 0;
		backface-visibility: hidden;
		border-radius: 2em;
		border: 1px solid var(--fg-2);
		box-sizing: border-box;
		padding: 2em;
	}

	.front {
		background: url(./svelte-logo.svg) no-repeat 5em 5em, url(./svelte-logo.svg) no-repeat calc(100% - 5em) calc(100% - 5em);
		background-size: 8em 8em, 8em 8em;
	}

	.back {
		transform: rotateY(180deg);
	}

	.symbol {
		font-size: 30em;
		color: var(--fg-1);
	}

	.pattern {
		width: 100%;
		height: 100%;
		background-color: var(--bg-2);
		/* pattern from https://projects.verou.me/css3patterns/#marrakesh */
		background-image:
		radial-gradient(var(--bg-3) 0.9em, transparent 1em),
		repeating-radial-gradient(var(--bg-3) 0, var(--bg-3) 0.4em, transparent 0.5em, transparent 2em, var(--bg-3) 2.1em, var(--bg-3) 2.5em, transparent 2.6em, transparent 5em);
		background-size: 3em 3em, 9em 9em;
		background-position: 0 0;
		border-radius: 1em;
	}
</style>
```

#### Shorthand class directive

+ 多くの場合、クラスの名前は依存する値の名前と同じになる：

+ 省略形を使うことができる


```svelte
<script>
	let flipped = false;
</script>

<div class="container">
	Flip the card
	<button
		class="card"
		// class: flipped={flipped}の省略形
		class:flipped
		on:click={() => flipped = !flipped}
	>
		<div class="front">
			<span class="symbol">♠</span>
		</div>
		<div class="back">
			<div class="pattern"></div>
		</div>
	</button>
</div>

<style>
	.container {
		display: flex;
		flex-direction: column;
		gap: 1em;
		height: 100%;
		align-items: center;
		justify-content: center;
		perspective: 100vh;
	}

	.card {
		position: relative;
		aspect-ratio: 2.5 / 3.5;
		font-size: min(1vh, 0.25rem);
		height: 80em;
		background: var(--bg-1);
		border-radius: 2em;
		transform: rotateY(180deg);
		transition: transform 0.4s;
		transform-style: preserve-3d;
		padding: 0;
		user-select: none;
		cursor: pointer;
	}

	.card.flipped {
		transform: rotateY(0);
	}

	.front, .back {
		display: flex;
		align-items: center;
		justify-content: center;
		position: absolute;
		width: 100%;
		height: 100%;
		left: 0;
		top: 0;
		backface-visibility: hidden;
		border-radius: 2em;
		border: 1px solid var(--fg-2);
		box-sizing: border-box;
		padding: 2em;
	}

	.front {
		background: url(./svelte-logo.svg) no-repeat 5em 5em, url(./svelte-logo.svg) no-repeat calc(100% - 5em) calc(100% - 5em);
		background-size: 8em 8em, 8em 8em;
	}

	.back {
		transform: rotateY(180deg);
	}

	.symbol {
		font-size: 30em;
		color: var(--fg-1);
	}

	.pattern {
		width: 100%;
		height: 100%;
		background-color: var(--bg-2);
		/* pattern from https://projects.verou.me/css3patterns/#marrakesh */
		background-image:
		radial-gradient(var(--bg-3) 0.9em, transparent 1em),
		repeating-radial-gradient(var(--bg-3) 0, var(--bg-3) 0.4em, transparent 0.5em, transparent 2em, var(--bg-3) 2.1em, var(--bg-3) 2.5em, transparent 2.6em, transparent 5em);
		background-size: 3em 3em, 9em 9em;
		background-position: 0 0;
		border-radius: 1em;
	}
</style>
```

#### The style directive

+ クラスと同様、インライン・スタイル属性は文字どおり書くことができる

// すぐには使わなさそうなので、必要になったときに見る

### Component composition

// すぐには使わなさそうなので、必要になったときに見る

### Context API

// すぐには使わなさそうなので、必要になったときに見る

### Special element

// すぐには使わなさそうなので、必要になったときに見る

### Module context

// すぐには使わなさそうなので、必要になったときに見る

### Miscellaneous

// すぐには使わなさそうなので、必要になったときに見る

### Next steps

####
####
####

## 疑問点

+ そもそもDOMとは?
+ Svelteを採用するデメリットとは?
  + ライブラリ・パッケージがこれから発展する感じ?
+ トランジション・エンジンとは?

+ <script>タグの部分は別ファイルに分けたいが、Svelte的にはよくない?
+ 標準的なディレクトリ構成はどうなっている?

+ SvelteKitとの違いは?

+ イベントをディスパッチするとは?
  + 自作のイベントを作る、というニュアンス?
  + どういうときに使う?

+ CSS FWを使うことはできる?
  + スタンダードなlibはある? (お気持ちとしては、なるべくCSSを書きたくない)

+ <script>、html、<style>に記述する順番の制約はある?

## 感想

+ バッと見の印象だが、Reactに比べると記述量が少なそう + HTML & CSSの基礎を知っていればなんとなく書けそう気がする（気がするだけかも）

+ Bindingのあたりから急に難しく感じる。

## 抽象化

+ 面倒な作業をコンパイラがよしなに処理してくれるので、ユーザは本質的な作業に集中できる。
  + 良いとされるコンセプトを引き継ぎつつも、シンプルに記述 = コンパイラが内部で変換して同等のコードを生成している
+ 慣れ親しんだ技術に近いが、よりシンプルに実現できる
+ 最小限の要素が揃っている

+ 。

## 応用

+ 。

+ 。

## See

https://github.com/sveltejs/svelte