# Rustプログラミング入門

## 現状

+ 名前を聞いたことがある程度。

+ これから導入事例が増えると予想している。
  + 本当?

+ アルゴリズムによる高速化と相性が良いかも?

## 目標

+ 関数型言語の良さを知る
  + 注: 本書によるとRustは公式に関数型プログラミング言語と主張していないらしい(出典を確認)

+ 小さなツール・Webアプリを作成して、OSSで公開。

+ 業務でRustを利用したプロダクトに参加

## 技術選定の理由

+ エラーメッセージが分かりやすいらしい。

+ 高速・安全性が売りと聞いて。

+ 関数型の言語を学んでみたい。

+ コミュニティが非常に活発なイメージがある

## 構成

1. 概要
2. 環境構築
3. 文法
4. コマンドラインツール
5. Web app
6. WebAssembly
  - 何者?
9. 10. リリースに向けたツール・注意事項

### 注意

+ 書籍が2020年に発売なので、内容がかなり古くなっている可能性がある
  + 例: kenkooooさんがdieselから別のSQLライブラリに乗り換えていた気がする

## Chapter 1 プログラミング言語Rust

+ 2015年に正式リリース
+ 特徴
  + 実行速度が速い
  + モダンな文法
  + OSからWeb appまで幅広く実装できる
  + ツール群が充実
  + 安全性が強力に担保されている
  + 互換性の担保（エディションという考え方）

+ 高速な理由
  + 機械語に直接コンパイルされる
    + 仮想マシンを介さない
    + LLVMを利用して、環境に応じた機械語を生成
  + ガベージコレクションをもたない
    + GCの問題点
      + 不要になったメモリがいつ解放されるか不明
      + 実行時に計算処理を止める可能性がある
    + 所有権・借用・ライフタイムという仕組みで、メモリを管理
  + ゼロコスト抽象化
    + 言語が持つ抽象化の機能を追加のコスト(メモリ・速度低下)を支払うことなく使用できる

+ モダンな言語機能
  + 変数の割り当ては、標準で不変
    + 値を一度変数に割り当てるとその値を変えることができない、という意味
    + letで宣言
    + 可変にするときは、let mut hoge = 5;と書く
  + filter、mapなどを使ってコレクションを操作できる
    + イテレータに集約されている
  + 代数的データ構造とパターンマッチング
    + 列挙型、構造体、タプルなどを代数的データ構造として扱える
    + パターンマッチング: switch文の強化版
      + 可読性の向上、保守性・バグの軽減
  + 強力な型推論
    + コンパイル時に型の情報をコンパイラが補完する仕組み
  + トレイト
    + Javaのインターフェースのような機能
      + 共通の振る舞いを取り出して名前付け
    + 高速化に貢献
    + トレイトとオブジェクトの紐付けは、コンパイル時に行われる

+ OSからWebアプリまで幅広く実装できる
  + 元々は、システムプログラミング言語
  + Webアプリのバックエンド開発や分散システム、組み込みシステム
  + Webフレームワークの一例: actix-web 

+ ツール群がとても充実
  + Cargo: パッケージマネージャ兼ビルドツール
  + エディタサポート
  + 標準フォーマッタrustfmt、標準リンターclippy

+ 「安全性」が強力に担保されている
  + メモリ安全でない操作やスレッド安全でない操作を未然に防ぐ
    + 例: 一度解放したメモリ領域を再び参照、マルチスレッド処理におけるデータ競合
  
+ エディションという考え方で互換性を担保
  + 異なるエディションでは、互換性がない
  + しかし、Cargoの設定ファイルで指定することで、同時に扱える
    + 切り替えの単位はクレート(Rustにおけるライブラリの単位)
  
+ Rustの利用事例
  + 企業での採用理由: パフォーマンス、安全性、ツールの充実による生産性の高さなど

## Chapter 2 環境構築

+ The Rust Playground
  + https://play.rust-lang.org/
  + ソースコードを手軽に実行できる & ブラウザに保存される

+ rustupのインストール
  + Cargo: パッケージマネージャ
  + rustc: コンパイラ
  + rustup: インストーラ

```terminal
cargo new hoge
ex: cargo new hello
// Cargo.toml: プロジェクトの設定ファイル

cargo run
```

+ ツール
  + rust-analyzer: Language Server
  + cargo-edit: Cargo.tomlの修正を容易にするツール

+ リリーストレインモデル
  + 6週間に1度リリース
  + 開発ブランチから安定ブランチを派生させる
    + nightly: 開発版
    + beta: 評価版。
    + stable: 安定版。基本的に破壊的な変更が起きない。基本的に使うチャンネル。

## Chapter 3 Rustの基本

+ 概念・特徴的な機能・プログラムの構造・ツール・テストの方法

+ ライブラリの種類
  + コアライブラリ: システムライブラリやプラットフォームに依存しない最小限の機能。基本的な型
  + 標準ライブラリ: std

+ 基本的な型
  + 数値型: アルファベット1文字 + その大きさ(ビット)
    + i: 符号あり整数型
    + u: 符号なし整数型
    + f: 浮動小数点型
    + 変数の大きさ: 8, 16, 32, 64, 128
      + f32、f64のみ
    + isize、usize: アーキテクチャのメモリ空間に依存してサイズが変わる
      + usize: 主に配列・ベクタの要素にアクセス、サイズを表現するときに使う
    + // Pythonのときは、意識せずに使っていたので、注意が必要
  + 文字列型
    + コアライブラリ: strのみ
      + 文字スライスと呼ばれる
      + データのスタート地点と長さを表す
      + 固定サイズで、文字列のそのものの変更はできない
      + &strの形で参照
    + 標準ライブラリ: String
      + データの変更や長さの変更が可能
    + どちらもUTF-8でエンコードされた文字列データであり、お互いに型変換できる
      + &strをStringに変換するときのみ、メモリの確保が行われる = 長い文字列は注意
    + // ミュータブル、イミュータブルのことか?
  + タプル
    + 異なる型を収めることができる集合
      + 関数から複数の値をまとめて返すときに使う
    + 内部で格納された型を全て含めて一つの型を構成するため、一部の型を後から変更できない
      + // Pythonと異なり、同じ型であれば(mutで変数宣言すれば)書き換えはできそう
    + アクセスするときは、.0、.1のように、ドット+添字
      + // なぜ、この形式にしたのか?
    + 配列
      + サイズは、コンパイル時に決まっている必要がある
      + [start..end]でスライス
    + ユーザ定義型
      + 構造体
      + 列挙型
        + それぞれの列挙子にデータを付与することができる
          + 付与するデータの型や構造は異なっていても良い
    + 頻出する標準ライブラリの型
      + Option
        + データが存在する場合、存在しない場合を表現できる列挙型
      + Result
        + 処理の結果が成功 / エラーを表現できる列挙型
      + Vec
        + ベクタ型
        + 配列と異なり、内部に収められる要素の数を増減させられる
      + Box
        + 値がヒープ領域に確保される(Rustでは、多くの場合はがメモリのスタック領域に確保される。紙の束を下から積み上げるようにメモリを確保し、上から順番に解放。動作がシンプルなので、高速に扱える。一方で、コンパイル時にサイズが分かっており、固定サイズでないといけない)
        + コンパイル時にサイズが不明でも良い
          + 必要なときに確保して、不要になったタイミングで解放
        + できること
          + コンパイル時にサイズが不明な型を格納
          + 大きなサイズの型の値を渡す前に、データの中身をコピーせず、ポインタで渡す
          + 共通のトレイトを実装したさまざまな型を画一的にポインタで扱う

```rust
    // 文字型
    let s1: String = String::from("Hello, World");
    let s2: &str = &s1; // String → &str
    let s3: String = s2.to_string(); // &str → String

    // タプル
    let mut t = (1, "2");
    t.0 = 2;
    t.1 = "3";   
    // ":?"を指定すると出力できる
    println!(":?", t) // (2, "3")
  
    // 配列
    let mut a: [i32; 3] = [0, 1, 2];
    println!("{:?}", &a[1..3]); // [1, 2]
    let b: [i32; 3] = [0; 3];
    println!("{:?}", &b); //[0, 0, 0]
    a[1] = b[1];
    a[2] = b[2];
    println!("{:?}", &a[1..3]); // [0, 0]

    // 構造体
    struct Person {
      name: String,
      age: u32,
    }

    let p = Person {
      name: String::from("John"),
      age: 28,
    };

    // 列挙型
    enum Event {
      Quit,
      KeyDown(u8),
      MouseDown {x: i32}, y: i32},
    }

    let e1 = Event::Quit;
    let e2 = Event::MouseDown { x: 10, y: 10};

    // Option
    // 標準ライブラリ上の定義
    pub enum Option<T> {
      None, // 存在しない
      Some<T> // 存在する(型T)
    }

    // Result
    // 定義
    pub enum Result<T> {
      Ok(T), // 成功
      Err(E) // 失敗: 任意の型データE
    }

    // ex: Result<i32, String>: 成功した場合には数値データ、エラーの場合にはエラーmsgが取得できる
    // Result型を取得した際は、matchやif letを使って、パターンマッチで処理するのが一般的
    let result: Result<i32, String> = Ok(200);

    match result {
      // パターンマッチ
      // resultがOk(~)という構造をしているときに、~に該当する部分をローカル変数codeに束縛して、次のブロックないで使用
      Ok(code) => println!("code: {}", code),
      Err(err) => println!("Err: {}", err),
    };

    let result: Result<i32, String> = Ok(200);

    if let Ok(code) = result {
      println!("code {}", code);
    }


    // 上記の書き方はコードのネストが深くなり、重厚な印象を与える
    // 別の書き方も用意されている
    unwrap_or(): Ok()の場合はそのまま展開、Err()の場合は引数で与えた値を返す
    値の代わりにクロージャを与えるunwrap_or_else()もある

    let result: Result<i32, String> = Ok(200);
    println!("code: {}", result.unwrap_or(-1)); // => "code: 200"

    let result: Result<i32, String> = Err("error".to_string());
    println!("code: {}", result.unwrap_or(-1)); // => -1


    // and_then()はOk()の場合のみ、指定した関数を
    実行できる
    fn func(code: i32) -> Result<i32, String> {
      println!("code: {}", code);
      Ok(100)
    }

    let result: Result<i32, String> = Ok(200);
    let next_result = result.and_then(func); // func()が実行される
    let result: Result<i32, String> = Err("error".to_string());
    let next_result: result.and_then(func); // func()は実行されない

    // ?演算子
    // OK(): 値を展開
    // Err(): Err()をそのまま展開
    // 関数内で使われる。エラーが起きたときに、そのエラーを呼び出し元に移譲する場合に有効

    fn error_handling(result: Result<i32, String>) -> Result<i32, String> {
      let code = result?; // エラーの場合は、return result;
      println!("code: {}", code);
      Ok(100);
    }


    // Vec
    // 初期化を簡単に実施するために、vec![]マクロが用意されている
    // 要素を入れた状態にできる
    let v1 = vec![1, 2, 3, 4, 5]; // 1〜5を入れて初期化
    // 自分にとっては、初めて見る形式 let hoge = vec![initial_value; count];
    let v2 = vec![0; 5]; // 0を5つ埋めて初期化

    println!("v1: {:?}", v1);
    println!("v2: {:?}", v2);

    // Pythonと同じ感じ
    // push()、pop()で最後尾に要素を追加・削除できる(= スタックとして利用できる)
    // []で内部の要素にアクセスできる。範囲外の場合は、パニックが起きて強制終了
    // get()を使うとパニックが発生せず、Noneを返す

    // forで繰り返し処理
    // PythonとC言語系を足して2で割ったようなイメージ
    let v = vec![1, 2, 3, 4, 5];
    
    // Q: &が必要な理由は?
    for element in &v {
      println!("{}", element);
    }


    // コンパイル時にサイズが分からない型を格納する例
    // [T]型
    fn main() {
      let byte_array = [b'h', b'e', b'l', b'l', b'o'];
      print(byte_array);
    }

    // ビルドが失敗する
    // Q: 配列の場合は、[type]の形式となる?
    fn print(s: [u8]) {
      println!("{:?}", s);
    }

    fn main() {
      let byte_array = [b'h', b'e', b'l', b'l', b'o'];
      // ポインタで渡すので、値がコピーされない
      print(Box::new(byte_array));
    }

    // コンパイルできる
    // Box<T>の形式で記述
    fn print(s: Box<[u8]>) {
      println!("{:?}", s);
    }
```

+ 変数宣言
  + 変数と値を紐づける方法
    + 束縛: 変数名に値を紐づける
    + letを使う
  + デフォルトで変更できない
  + 変数を変更可能にするなら、変数宣言にmutを付ける

```rust
// 束縛
let immut_val = 10;

// 変更可能に
// 明示的に型を指定しない場合は、多くのは場合コンパイラが推測してくれる
// 整数は、デフォルトでi32
let mut mut_val = 20;
mut_val += immut_val;

// 型を明示的に指定
let v1: u64 = 10;
let v2 = 10u64; // 数値限定で、このようにも書ける
```

+ constとstatic
  + 定数を定義できる
  + const: 常に変更不可。ビルド時に実際の値に置き換えれられる。
  + static: 変更可能。バイナリファイルの特定のセクションに配置される
    + グローバルスコープで定義した場合、unsafeブロック内に入れる必要がある
  + コンパイル時には決まらないが、実行時に決まる定数を定義したい場合は、lazy_staticがオススメ

+ 制御構文
  + if、for、whileなど
  + 式として扱えるものもある

```rust
  let number = 1;

  // C系の書き方に近い
  if 0 < number {
    println!("0 < number");
  } else if number < 0 {
    println!("number < 0");
  } else {
    println!("0 == number");
  }

  // Rustのifは式
  // 変数に束縛することや、関数の引数にもできる
  // ifが返す値の型は全て揃っている必要がある
  let number = 1;

  let number = if 0 <= number {
    number
  } else {
    -number
  }
```

+ ループ

```rust
    // loop
    let mut count = 0;

    let result = loop {
        println!("count: {}", count);

        count += 1;

        if count == 10 {
            // 繰り返しから抜ける
            // loopは式なので、値を返すことができる
            // breakに戻り値を付与することができる!
            break count;
        }
    };

    // while
    // 特定の条件を満たすときだけ繰り返す
    let mut count = 0;
    
    // PythonとC系言語を足して2で割ったような感じ
    while count < 10 {
        println!("count: {}", count);
        count += 1
    }

    // for
    // 繰り返しの回数や処理の要素の集合が決まっている場合に有効
    let count: i32;

    // Pythonの記法に近いが、rangeの代わりに..で範囲を指定している
    // start..end: [start, end)
    for count in 0..10 {
        println!("count: {}", count);
    }
    
    let array = [0, 1, 2, 3, 4, 5, 6, 7, 8, 9];
     
    // &で参照するのがデフォルト?
    for element in &array {
        println!("element: {}", element);
    }


    // whileやforも、繰り返しの最中にbreakを使える
    // ラベル名を指定して繰り返しを抜けることもできる
    // ラベル名は、先頭に'を付ける必要がある
    // Q: 通常のbreakとは何が違う?
    // 'mainを指定することで一気に外側のループを抜けられる
    'main: loop {
        println!("main loop start");
        
        'sub: loop {
            println!("sub loop start");
            
            break 'main;
            
            println!("sub loop end"); // 表示されない
        }
    
    println!("main loop end"); // 表示されない
    }
```

+ match
  + C言語などのswtichよりも強力な「パターン」に応じた分岐ができる
    + Rustにおけるパターン: 型の構造に一致しているか確認するための記法を指す
      + 単純な値
      + 列挙型、タプル、構造体などの比較
      + 値の範囲やワイルドカードを使った広範囲な比較
    + 最初に合致した処理に入る 

```rust
    let i: i32 = 2;
    
    // 条件1 => 合致する場合の処理,
    // 条件2 => 合致する場合の処理,
    // ...
    // _ => いずれの条件にも合致しない場合の処理
    match i {
        1 => println!("1"),
        2 => println!("2"),
        3 => println!("3"),
        _ => println!("misc") // _: ワイルドカード。あらゆる値にマッチ
    }


    // 列挙型: 網羅性の確認 + 全ての列挙子に対する処理が存在するか確認
    // 足りない列挙子があると、コンパイルエラーになる
    enum Color {
        Red,
        Blue,
        Green,
    }
    
    let c = Color::Red;
    
    match c {
        Color::Red => println!("Red"),
        Color::Blue => println!("Blue"),
        // Color::Green => println!("Green"),
    }


    // matchは式なので、分岐処理した結果を、変数に束縛することもできる
    let result: Result<i32, String> = Ok(100);
    let result: Result<i32, String> = Err("error".to_string());
    
    let result_number = match result {
        Ok(number) => number,
        Err(message) => {
            println!("Error: {}", message);
            -1
        },
    };
```

+ range
  + forループで特定の数値を指定するときには、Range型を使う

```rust
    // s..e: [s, e)
    for number in 1..5 {
        println!("{}", number); // 1, 2, 3, 4が順に出力される
    }

    // s..=e: [s, e]
    // ..=を置くことで、RnageInclusive型になる
    for number in 1..=5 {
        println!("{}", number); // 1, 2, 3, 4, 5が順に出力される
    }
```

+ Iterator
  + forループ: データの集合にIteratorトレイトが実装されているため実行できる
  + Iteratorトレイトを自作すれば、forループで処理できる
    + 条件1: Iteratorが出力する型を決め、type Itemに紐づける
    + 条件2: 次の要素を返すnext()メソッドを実装する

```rust
    // Iteratorを適用した構造体Iterのサンプル
    fn main() {
        let it = Iter {
            current: 0,
            max: 10,
        };
        
        for number in it {
            println!("{}", number);
        }
    }

    struct Iter {
        current: usize,
        max: usize,
    }

    impl Iterator for Iter {
        type Item = usize; // 出力する型の紐付け
        
        // next()メソッドの実装
        fn next(&mut self) -> Option<usize> {
            self.current += 1;
            
            if self.current - 1 < self.max {
                Some(self.current - 1)
            } else {
                None
            }
        }
    }
```

+ 関数
  + fnキーワードを使った関数の定義
  + implキーワードを使った構造体のメソッドの定義
    + 構造体にメソッドを紐付けられるため、データと関連する振る舞いを合わせてカプセル化できる
      + Q: オブジェクト指向プログラミングのClassに相当するものと思えばよい?
      + A: Yes。(厳密には違う部分もあるかもなので、要確認)
    + トレイトを型に適用する際にも使われる

```rust
// 関数の定義
fn main() {
    let x = add(1, 2);
    println!("x= {}", x);
}

// fn method_name(arg: type) -> return type 
fn add(a: i32, b: i32) -> i32 {
    // do some_thing

    a + b // 関数内の最後に、セミコロンなしで記述された値を戻り値として扱うルール
}


fn abs(number: i32) -> i32 {
    if number < 0 {
        // 途中で戻り値を返すときは、returnを使う
        return -number;
    }
    
    number
}


// impl
fn main() {
    let p = Person{
        name: String::from("Taro"),
        age: 20,
    };
    
    p.say_name();
    p.say_age();
}

struct Person {
    name: String,
    age: u32,
}

// structと同じ名前に
// Q: 別々に定義するのは、データと振る舞いを意図的に分けるため?
impl Person {
    // メソッドを実装
    // Pythonのクラス + C系の関数の書き方に似ている
    // Q: fn method_name(&self)の&は必須?
    fn say_name(&self) {
        println!("I am {}.", self.name);
    }
    
    fn say_age(&self) {
        println!("I am {} year(s) old.", self.age);
    }
}


// メソッドチェーンが利用できる
// Q: 何のため?
fn main() {
    let p = Person{
        name: String::from("Taro"),
        age: 20,
    };
    
    p.say_name().say_age();
    // p.say_age();
}

struct Person {
    name: String,
    age: u32,
}

impl Person {
    // 戻り値に自分自身を指定すると、メソッドチェーンが使用できる
    // 構造体のメンバ変数を変更できるようにするには、&mut selfとする
    fn say_name(&self) -> &Self {
        println!("I am {}.", self.name);
        self
    }
    
    fn say_age(&self) -> &Self {
        println!("I am {} year(s) old.", self.age);
        self
    }
}


fn main() {
    let p = Person::new("Taro", 20);
    p.say_name().say_age();
}

// 複数定義しても、コンパイルエラーは出ていない
// Q: 別言語のParticalに相当するような機能か?
impl Person {
    // 第1引数にselfを使わない場合は、associated funcionの定義になる
    // インスタンスからメソッドを呼ぶのではなく、型から関数を呼ぶ形式で定義される関数
    // Q: Classのコンストラクタを定義できると思えばよい?
    fn new(name: &str, age: u32) -> Person {
        Person {
            name: String::from(name),
            age: age,            
        }
    }
}
```

+ マクロ
  + Rustの標準ライブラリが提供しているライブラリ
    + 関数のように扱えるマクロ
    + #[derive] attributeにより、実装を自動的に導出するマクロ
      + 定義の前の行に付与して、該当する定義を拡張する表現
    + 定義方法や詳細は11章で解説されている

```rust
// 標準ライブラリで定義されているもののうち、よく使われるもの
// 多くはlibcore/macros/mod.rsで定義されている

// マクロの呼び出しには「!」がついており、関数呼び出しと区別できる
// 構文と見た目が合う括弧が慣例的に使われる
// 関数呼び出しマクロ: ()
// コードブロックを引数に取るマクロ: {}


// ◾︎文字列操作
format! // フォーマット文字列と値から文字列を作成。文字列スライスを結合して、新しい文字列を作成する際にも使われる
concat! // リテラルを結合
// Q: 2つの使い分けは?

let s = concat!("A", "b2", "3"); // s = String::from("Ab23")と同じ
let s = format!("{}-{:?}", s, ("D", 5)); // s = String::from("Ab23-(\"D"\, 5)")と同じ
let s = format!("{}{}", "abc", "def"); // s = String::from("abcdef")と同じ 

// フォーマット文字列内のプレースホルダの意味
// {:?}: 内部情報の文字列表現。基本的に#{derive(Debug)}で実装可能。std::fmt:Debug
// {}: ユーザに提示するためのシンプルな文字列。implキーワードで実装する必要がある。std::fmt:Display


// ◾︎データ出力用
// 末尾にlnがついているマクロは、改行をつけて出力
// dbg!以外のマクロはフォーマット文字列を引数に取る

// print!、println!: フォーマット結果を標準出力に出力
// eprint!、eprintln!: フォーマット結果を標準エラー出力に出力
// write!、writeln!: バッファーにバイト列を出力
// dbg!: ファイル名、行数、式、式の値を標準エラー出力に出力

use std::io::Write; // write、writelnマクロを使うため

fn main() {
    print!("hello");
    println!("hello {}", "world");
    
    eprint!("hello {}", "error");
    eprintln!("hello");
    
    let mut w = Vec::new(); // バイト列書き込み用
    write!(&mut w, "{}", "ABC"); // UTF-8バイト列で65, 66, 67
    writeln!(&mut w, " is 123"); // UTF-8バイト列で32, 105, 115, 32, 49, 50, 51, 10
    dbg!(w); // フォーマット文字を受け取らない
}


// ◾︎異常終了用
// Q: raise errorと思えば良い?
fn main() {
    panic!("it will panic"); // thread 'main' panicked at 'it will panic', src/main.rs:x:x
}


// ◾︎ベクタを初期化
// Vec<T>を手軽に初期化する。配列と同じ記法になるよう、慣例的に[]を使って呼び出す
fn main() {
    let v = vec![1, 2, 3];
    println!("{:?}", v);
}


// ◾︎プログラム以外のリソースにアクセス
// file!: マクロが呼び出されたファイル名を取得
// line!: 行番号を取得
// cfg!: コンパイラから該当フラグが渡されていればtrue、そうでなければfalseを返す
// env!: コンパイル時の環境変数を取得
fn main() {
    println!("defined in file: {}", file!()); // defined in file: src/main.rs
    println!("defined on line: {}", line!()); // defined on line: 3
    println!("is test: {}", cfg!(unix)); // is test: true in playground.
    println!("CARGO_HOME: {}", env!("CARGO_HOME")); // /playground/.cargo in playground.
}


// ◾アサーション用
fn main() {
    assert!(true);
    assert_eq!(1, 1);
    assert_ne!(1, 0);
    debug_assert!(false);  // デバッグビルド(cargo run)で実行すると、エラーメッセージが表示される。リリースビルド(cargo run --release)で実行すると、使用されないのでコンパイルは成功する
    debug_assert_eq!(1, 1);
    debug_assert_ne!(1, 0);
}


// 実行補助用
// 未実装のソースコードの型検査を通すためのマクロ
// 複数の関数を定義する必要があるトレイトを実装するときなどに、現在実装している箇所まで型検査をしたいときに使う
// 該当箇所が実行されたときにパニックを発生させられるため、実装漏れを防げる

// unimplemented!: 何らかの理由により未実装であることを示す。エラーメッセージ: not yet implemented
// todo!: 今後実装しなければならないことを表現する。エラーメッセージ: not implemented
// unreachable!: その箇所に処理が到達しないことを示す。エラーメッセージ: internal error: entered unreachable code

fn main() {
    let mut p = HappyPerson {
        name: "Takashi".to_string(),
        state: Emotion::Happy,
    };
    
    println!("{}", p.get_happy());
}

enum Emotion {
    Anger,
    Happy,
}

// Q: traitは、コンパイル型言語のInterfaceに近い?
trait Emotional {
    fn get_happy(&mut self) -> String;
    fn get_anger(&mut self) -> String;
    fn tell_state(&self) -> String;
}

struct HappyPerson {
    name: String,
    state: Emotion,
}

// traitの実装部分
// impl trait_name for hoge
impl Emotional for HappyPerson {
    fn get_anger(&mut self) -> String {
        // 呼ばれないので実装しないが、String型を返さなくても型検査を通過させる
        unimplemented!()
    }
    
    fn get_happy(&mut self) -> String {
        format!("{} is always happy.", self.name) // 戻り値を返すため、末尾に「;」がないことに注意
    }
    
    fn tell_state(&self) -> String {
        // 後で実装したいが、一旦String型を返さなくても型検査を通過させる
        todo!()
    }
}

// ビルトインマクロ
// libcore/macros/mod.rs: 一部のマクロの処理が、/* compiler built-in */
// コンパイラrustcに組み込まれる形で実装されている
// ex: cfg!、file!、concat!、env!
// 主にプログラム外のリソースにアクセスするなど、プログラム内からの関数からは実現できない処理が実装されている
```

+ [Pending] トレイトの導出
  + 標準ライブラリのいくつかのトレイトは、自作の型に対する標準的な実装を自動的に導出(derive)できる
    + Q: 導出とは?
    + Q: 自作の型に対して、標準的な実装が必要になる局面とは?
      + A?: 一致しているか比較、大小比較、cloneできるか?、デバッグプリントできるか?
    + Q: 自動的に導出できることのメリットは?
  + よく使われるトレイトの導出
    + Eq / Partial Eq
    + Ord / PartialOrd
    + Copy
    + Clone
    + Debug
    + Default
  + 方法
    + #[derive] アトリビュートを使用
  + いくつかのトレイトには、満たすべきトレイトがあるため、まとめて指定
    + Q: 依存関係があると理解すればよい?
  + enumが内包するすべての型がそのトレイトを実装している必要がある
    + トレイトを実装していない場合は、手動(impl)で実装する必要がある
  
### Rustを支える言語機能

+ 安全で高速に処理するための仕組みの動作内容・なぜそうなっているか?の解説

+ ゼロコスト抽象化
  + 抽象化: 複雑な仕組みのシステムの大まかな動きだけに注目できるようにすること
    + 例: オブジェクト指向プログラミングのカプセル化、ポリモーフィズム
  + 抽象化により理解しやすくなる反面、メモリの使用量増加や処理が遅くなることがある
  + 抽象化した処理を実行するために必要な負荷をできる限り小さくすることを意味する
    + 例: トレイトやジェネリクスを使用する型に落とし込む作業をコンパイル時に行うことで、実行時にかかる余計なコストがゼロになる

+ traitとdyn
  + OOPではクラスを使ってカプセル化やポリモーフィズムを実現
  + Rustでは、トレイトを使ってポリモーフィズムを実現
    + さまざまな型に共通のメソッドを実装するように促すことができる仕組み
  + メソッドの呼び出し方法
    + 静的ディスパッチ: インスタンスからメソッドを実行 = コンパイル時には、実行すべき処理が分かっているため、高速に処理できる
    + 動的ディスパッチ: どのインスタンスが実行したメソッドか分からない場合への対処

```rust
// トレイトのサンプル
// 抽象クラス + 具象クラスのような感じで実装できる
trait Tweet {
    fn tweet(&self);
    
    fn tweet_twice(&self) {
        self.tweet();
        self.tweet();
    }
    
    fn shout(&self) {
        println!("Uooooooooooooooooohhh!!!!!!!");
    }
}

// 構造体を忘れずに記述する
struct Dove;
struct Duck;

// 鳩
impl Tweet for Dove {
    fn tweet(&self) {
        println!("Coo!");
    }
}

// アヒル
impl Tweet for Duck {
    fn tweet(&self) {
        println!("Quack!");
    }
}

fn main() {
    let dove = Dove {};
    // 静的ディスパッチ
    dove.tweet();
    dove.tweet_twice();
    dove.shout();
    
    let duck = Duck {};
    
    // 動的ディスパッチ
    // Box<dyn Tweet>のように画一的にポインタとして扱える
    let bird_vec: Vec<Box<dyn Tweet>> = vec![Box::new(dove), Box::new(duck)];
    
    for bird in bird_vec {
        // どの型のインスタンスのメソッドを実行しようとしているか検証して実行
        bird.tweet();
    }
}
```

+ マーカトレイト
  + トレイトにメソッドがなく、それぞれの持つ意味や役割を印のように付与するもの
  + 例
    + Copy: 値の所有権を渡す代わりに、値のコピーを行う
    + Send: スレッドの境界を超えて所有権を転送できる
    + Sized: メモリ上でサイズが決まっている
    + Sync: スレッド間で安全に参照を共有
  + コンパイラが安全性の検査や最適化を行う際に使用される
+ ジェネリクス
  + 型を指定せず、どんな型でも動作できる処理を作れる

```rust
fn main() {
    let t1 = make_tuple(1, 2);
    let t2 = make_tuple("Hello", "World");
    let t3 = make_tuple(vec![1, 2, 3,], vec![4, 5]);
    let t4 = make_tuple(3, "years old");
    
    println!("{:?}", t1);
    println!("{:?}", t2);    
    println!("{:?}", t3);
    println!("{:?}", t4);    
}

// <Type1, Type2>
// Q: 1文字の変数、かつ、大文字が使われることが多い?
// Q: 利用できる型名に制限や慣習はある? Tは他の言語でも使われていそう
fn make_tuple<T, S>(t: T, s: S) -> (T, S) {
    (t, s)
}
```

#### 所有権と借用

+ Rustの重要なキーワード
  + 所有権、借用、ライフタイム
  + 各値に所有権があり、所有権を持っているのは必ず一つの変数(所有者)だけ
  + 所有者の変数がスコープから外れたら、その値は破棄される
  + 借用
    + 所有権は元の所有者が持ったまま、値を参照する権利だけもらうこと
  + ライフタイム
    + 参照において、安全に利用できる期間
  
+ ムーブセマンティクス
  + シンタックス(文法、表記方法)と対になる言葉
  + 意味論、意味づけ = 書いた内容がどういう意味を持つか?
    + 例
      + C++のように「 = 」で値がコピーされることをコピーセマンティクス
      + Rustのように「 = 」で所有権が移ることをムーブセマンティクス
        + 多くの型のデフォルトであり、所有権システムを支えている
        + i32のように、Copyトレイトを持つ型はコピーセマンティクスになるが、多くはない

```rust
// 所有権のシンプルなサンプル
fn main() {
    let a = Color{r: 255, g: 255, b: 255}; // {}で囲む
    let b = a; // 所有権が譲渡される
    println!("{} {} {}", b.r, b.g, b.b);
    // println!("{} {} {}", a.r, a.g, a.b); // 所有権が移動しているため、エラーになる
}

struct Color {
    r: i32,
    g: i32,
    b: i32,
}


// 所有権を渡して、返してもらう例
fn main() {
    let mut important_data = "Hello, World".to_string();
    
    important_data = calc_data(important_data); // 値の所有権を返してもらう 
    
    println!("{}", important_data);
}

fn calc_data(data: String) -> String {
    println!("{}", data);
    data // 呼び出し元に所有権を返却
}


// 借用のシンプルなサンプル
// 参照渡しなら、所有権を渡さずに済む
fn main() {
    let important_data = "Hello, World".to_string();
    
    // 値の参照を渡す = important_dataの所有権は譲渡しない
    // 変数の先頭に&を追加 & 呼び出し先の引数にも追加
    calc_data(&important_data);
    println!("{}", important_data);
}

fn calc_data(data: &String) {
    println!("{}", data);
}
```

+ 借用
  + 不変な参照と可変な参照でルールが異なる
    + 不変: いくつでも参照を渡せる。同時に参照されても問題ないため。
    + 可変: 一度に渡せる数は1つだけ。同時に値の更新が発生したときに予期せず、壊れることがあるため。
    + 不変・可変の参照を同時に存在させることはできない。
      + 不変参照の変数が、可変参照の変数によって変更されることを予期していないため。
      + 同期が取れているわけではない。
    + 変更の可否に関わらず、参照は元のライフタイムよりも長く生存できない
      + 元の所有者が値を破棄したら、参照できる値もなくなるため

```rust
fn main() {
    let x = 5;
    
    let y = &x; // 1回目の不変な参照渡し
    
    let z = &x; // 2回目の不変な参照渡し
    
    dbg!(x);
    dbg!(y);
    dbg!(z);
}

fn main() {
    let mut x = 5;
    
    {
        let y = &mut x; // 1回目の可変な参照渡し
    
        // let z = &mut x; // 2回目の可変な参照渡し(エラー)
        
        dbg!(y);
        // dbg!(z);
    }
    
    {
        let y = &x; // 不変な参照渡し
        
        let z = &mut x; // 可変な参照渡し(エラー)

        dbg!(y);
        dbg!(z);
    }
}


fn main() {
    let y;
    
    {
        let x = 5;
        
        y = &x;
        
        dbg!(x);
    }
    
    // xよりもyが長く生存することはできない(エラー)
    dbg!(y);
}
```

+ ライフタイム決定の流れ
  + Rustの借用チェッカーは、リソースのライフタイムを調べるために、以下の2つに注目
    + 参照のライフタイム: 参照が利用されている期間に対応
    + 値のライフタイム: 値が解放される直前までの期間に対応
    + Q: 最初に束縛してからdbg!で使われるまで、とあるが、何がどのタイミングまで生存しているかが分かっていないかも?
    + 変数の使用状況を追跡しながらライフタイムを決定し、ライフタイムに関するルールを満たしているかチェックしている

+ RAII
  + Resource Acquisition Is Initializationの略(リソースの取得は初期化)
    + 変数の初期化をする際にリソースを確保し、変数を破棄するときに解放
  + メモリだけでなく、ファイル、ネットワーク接続も、管理している変数がスコープから外れると全て解放される
  + デストラクタとしてDropトレイトが用意されている
    + 独自に保有しているリソースがあり、何らかの解放処理が必要な場合に使用する

```rust
fn main() {
    {
        let d = Droppable;
        
        println!("The Droppable should be released at the end of block.");
    }
}

struct Droppable;

impl Drop for Droppable {
    fn drop(&mut self) {
        println!("Resource will be released!");
    }
}
```

+ スレッド安全性
  + 基本的なスレッドの作り方
  + 共有メモリ方式により複数スレッドから1つのデータにアクセスする方法
  + メッセージパッシング(message passing)方式でデータをスレッド間でやりとりする方法

```rust
use std::thread; // スレッドを扱うときに必要

fn main() {
    // クロージャ(関数オブジェクトの1種。引数・変数に関数そのものを渡したいときに使われる)を受け取り、新しいスレッドで実行する
    // 引数あり|arg| {body}
    thread::spawn(|| {
        // 表示されない or 一見正しく表示されるかもしれない
        // 表示されない場合は、"Hello, world!"が表示されるより前にプログラムが終了している    
        println!("Hello, world!");   
    });
}


use std::thread;

fn main() {
    // 必ず表示して終了する
    let handle = thread::spawn(|| {
        println!("Hello, world!"); 
    });
    
    // joinを呼ぶことで、 スレッドの終了を待つことができる
    // 戻り値: OK(())。スレッドが正常に終了したことを示す。
    dbg!(handle.join());
}


use std::thread;

fn main() {
    let mut handles = Vec::new();
    
    // Q: xの参照と寿命がどうなっているか分かっていないかも
    for x in 0..10 {
        // spawnに渡すものはクロージャなので、変数をスレッドに渡すことができる
        // スレッドを10個作成して、そのスレッド番号としてxを渡す
        // エラー: xの参照が寿命よりも長生き
        // moveキーワードを使って、xの所有権をスレッドに移す
        // 注: スレッドがどのような順番で実行されるか保証がない 
        handles.push(thread::spawn(move || {
            println!("Hello, world!: {}", x);
        }));
    }
    
    for handle in handles {
        let _ = handle.join();
    }
}
```

+ スレッド間の情報共有
  + 上記のサンプルでは、一度動き出したスレッドは完了まで待つことしかできない
  + 実際のマルチスレッドプログラミングでは、各スレッドは実行中もさまざまな情報を共有しながら協調して作業を進行させる
  + 方法
    + 共有メモリ
    + メッセージパッシング

+ 共有メモリ
  + 複数のスレッドで同じメモリ領域を共有する方法

```rust
// use std::rc::Rc; // 追加 (シングルスレッド用。マルチスレッドでは正しく動作しない)
use std::sync::{Arc, Mutex}; // 追加（マルチスレッド版。Atomically Reference Counted。参照カウンタの更新に、Rcよりも多くコストがかかる）
use std::thread;

fn main() {
    let mut handles = Vec::new();
    // let mut data = Rc::new(vec![1; 10]); // 追加
    let data = Arc::new(Mutex::new(vec![1; 10])); // 追加
    
    // 10個の要素を持ったdataを各スレッドで共有
    // 各スレッドが自分が担当する要素を+1
    
    // エラー: dataの所有権が1つ目のスレッドに移動したため、2つ目のスレッドで所有権を取ることができない
    // 対処方法: 各スレッドで所有権を共有。
    // Rcという型を使う。所有権を共有するために、所有権を保持している所有者の数をカウントしている(参照カウンタ)。所有者が0になったところでメモリを解放する。
    // エラー: Arcは書き換え不可能（スレッドが同時に同じ領域にアクセスする可能性があるため）
    // 対処方法: Mutexで排他制御を行う
    for x in 0..10 {
        let data_ref = data.clone(); // 追加 Rcの参照カウンタを増やしている
        
        // 全てのスレッドに所有権を渡すことができる
        handles.push(thread::spawn(move || {
            // lockを使ってdataへの可変参照を得る
            let mut data = data_ref.lock().unwrap(); // 複数のスレッドが同時にアクセスしないことを保証。あるスレッドがlockを実行して、dataへの参照を得ると、それ以外のスレッドはlockが完了しなくなる
    
            data[x] += 1;
        }));
    }
    
    for handle in handles {
        let _ = handle.join();
    }
    
    dbg!(data);
}
```

+ メッセージパッシング
  + 各スレッドがメッセージのやり取りをしながら動作する方式
  + Rustには、メッセージにパッシングに対応したスレッド間通信用のチャンネルを持っている

```rust
use std::sync::mpsc;
use std::thread;

fn main() {
    // 単方向の通信
    // mainスレッドから作成したスレッドにしか送れない
    // 逆方向のチャンネルを作成すれば、双方向で通信できる
    let (tx, rx) = mpsc::channel(); // チャンネルの作成。戻り値: 送信、受信インスタンスのタプル。
    let handle = thread::spawn(move || {
        let data = rx.recv().unwrap(); // recv: メッセージを受け取る
        println!("{}", data);
    });
    
    let _ = tx.send("Hello, world!"); // send: メッセージを送る
    
    let _ = handle.join();
}
```

```rust
use std::sync::mpsc;
use std::thread;

fn main() {
    let mut handles = Vec::new();
    let mut data = vec![1; 10];
    let mut snd_channels = Vec::new();
    let mut rcv_channels = Vec::new();
    
    // チャンネルは、メッセージをキューに蓄えられる。
    // スレッド作成前にsencしても問題ない。
    // メッセージパッシング方式で10要素の配列を+1
    // スレッド間を安全に渡すことができない型をチャンネルを通して送ろうとすると、コンパイルエラーになる
    for _ in 0..10 {
        // mainから各スレッドへのチャンネル
        let (snd_tx, snd_rx) = mpsc::channel();
        // 各スレッドからmainへのチャンネル
        let (rcv_tx, rcv_rx) = mpsc::channel();
        
        snd_channels.push(snd_tx);
        rcv_channels.push(rcv_rx);
        
        handles.push(thread::spawn(move || {
            let mut data = snd_rx.recv().unwrap();
            data += 1;
            let _ = rcv_tx.send(data);
        }));
    }
    
    // 各スレッドにdataの値を送信
    for x in 0..10 {
        let _ = snd_channels[x].send(data[x]);
    }
    
    // 各スレッドからの結果をdataに格納
    for x in 0..10 {
        data[x] = rcv_channels[x].recv().unwrap();
    }
    
    for handle in handles {
        let _ = handle.join();
    }
    
    dbg!(data);
}
```

+ SendとSync
  + マーカトレイト: スレッド安全性を保証する仕組み
  + Send
    + 実装した方は、その所有権をスレッドをまたいで転送できる
    + ほとんどの型に実装されている(Rcなど一部の型は実装されていない)
    + スレッド間を転送してはいけない型を転送しようとするとコンパイルエラーになる
  + Sync
    + 複数のスレッドから安全にアクセスできる
    + 実装されていない型を複数のスレッドからアクセスした場合はコンパイルエラーになる
  + 自作の型
    + 基本的に自分でSendやSyncを実装すべきではない
    + その型を構成するすべての型がSendやSyncであれば、自動的にSendやSyncが実装される
  + SendやSyncにより、サードパーティ製のライブラリも標準ライブラリと同等のスレッド安全性を提供することができる

+ 非同期処理
  + async、await
    + 裏側ではFutureという概念がある + Futureの制御をより簡単に行うことができる
    + 他の言語とほぼ同等

```rust
// DBにアクセスしてデータを受け取る

// 非同期プログラムを実行するための基盤が用意されている
// cargo add futuresで、手元に依存を追加できる
use futures::executor;

struct User {
    // 何かデータを持っている
}

struct UserId(u32);

struct Db {}

impl Db {
    async fn find_by_user_id(&self, user_id: UserId) -> Option<User> {
        // DBに接続するなどの実装が追加される、と想定
    }
}

async fn find_user_by_id(db: Db, user_id: UserId) -> Option<User> {
    // db: データベースアクセスを表す。Option<User>型を返すものとする
    db.find_by_user_id(user_id).await
}

fn main() {
    // find_user_by_id関数を実行
    executor::block_on(find_user_by_id(Db {}, UserId(1)));
}
```

+ 非同期プログラミング
  + 非同期処理
    + あるタスクの実行中に、別のタスクを実行できること
    + 問題点:
      + C10K問題
        + 同時にサーバを利用するクライアントが万単位になると応答性能が一気に悪くなる
        + マルチプロセスでは表現できるプロセス番号の制限、マルチスレッドではOSスレッド一つあたりが利用するメモリ量によって引き起こされる問題
    + 解決方法
      + イベント駆動型モデルの実装
        + プロセス数・イベント数は変えず、「必要なタイミングに、必要な処理を、空いているリソースに割り当てて実行する」
  + 非同期タスクをを作成してランタイムに実行させる形のプログラミングスタイル

+ Future
  + async / awaitの動作の理解を深めるために重要
  + 将来のどこかの時点で処理が完了するタスク(他言語だとPromiseと呼ばれるケースも)
  + 用語の関係性
    + Future: 非同期タスク。Futureトレイトを実装した型を戻り値とすることで、非同期ランタイムに渡った時点で処理タイミングが制御されるようになる
    + 非同期ランタイム: Futureの実行タイミングを制御し、必要なタイミングで処理を走らせる
    + async: 「Futureトレイトを実装した型を返す関数」を通常の関数と同様に記述できる構文
    + await: asyncで実装された関数の処理結果を受け取る必要があるタイミングを記述する構文

+ Futureの詳しい説明
  + 「将来に値が確定する計算のステートマシンを抽象化したインターフェース」
    + Futureトレイトを実装したタスクが「将来的に値が確定する計算」となる
      + タスクが作成された時点では実行されておらず、ランタイムに乗った時点でスケジューリングされ実行される
      + 実行するかどうかの判断するときに、poll()でチェックされる
        + チェックする主体はWaker(std::task::Walker)であり、poll関数の引数として渡されるContext内にラップされている
        + Poll::Pendingが返されると、poll()は他のタスクが実行されるまで呼ばれず、他のタスクを実行
        + Poll:Ready<T>が返されると、タスクが実行完了となり、ランタイムは次の実行状態に移る
        + ポーリングを繰り返しながら実行するランタイムをexecutorと呼ぶ
    + ゼロコスト抽象化を達成している
      + 各Futureはステートマシンとして表現されており、1箇所のヒープに割り当てられる
        + ステートマシンの中には、IOイベントごとに一つの状態を格納
        + 各タスクに対して決まったメモリサイズで1箇所にしか割り当てないため、余計なメモリ割り当てのコストがかからない

```rust
// シグネチャ
// Q: pubは、他の言語のpublicに相当すると思えばよい?
pub trait Future {
  type Output;

  // pollメソッドだけを持っている
  // Poll<Self::Output>を返す
  fn poll(self: Pin<&mut Self>, cx: &mut Context) -> Poll<Self::Output>;
}

// Poll<T>は、2値を持つenum
pub enum Poll<T> {
  Ready(T),
  Pending
}
```

```rust
// Note: cargo add futuresを実行する必要がある
use futures::{executor, future::join_all};
use std::future::Future;
use std::pin::Pin;
use std::task::{Context, Poll};

struct CountDown(u32);

impl Future for CountDown {
    type Output = String;
    
     fn poll(mut self: Pin<&mut Self>, cx: &mut Context) -> Poll<String> {
         if self.0 == 0 {
             Poll::Ready("Zero!!!".to_string())
         } else {
            // 呼ばれるたびにカウントダウン
             println!("{}", self.0);
             self.0 -= 1;
             cx.waker().wake_by_ref(); // Wakerに対して「また呼んで」と伝える
             Poll::Pending
         }
     }
}

fn main() {
    let countdown_future1 = CountDown(10);
    let countdown_future2 = CountDown(20);
    let cd_set = join_all(vec!(countdown_future1, countdown_future2));
    // executor::block_on: 渡したFutureが完了になるまでブロックして待つメソッド
    let res = executor::block_on(cd_set);
    
    // Pythonのenumerate()とはやや構文が異なるがインデックスと値をループで取り出せる
    for (i, s) in res.iter().enumerate() {
        println!("{} {}", i, s)
    }
}
```

+ async / await
  + 非同期処理を制御する際に用いる
    + 内部的にはジェネレータとして表現されている
      + ジェネレータ: 一旦キリのいいところまで処理を行い、一度中断したあと、もう一度準備が整った段階で処理を再開する機構  
      + ステートマシンになっており、中断と再開の状態を切り替えながら、徐々に処理を進める
      + 複数の処理を切り替えながら効率よく処理できる
  + 非同期処理の実行には、futuresというFutureを扱うクレートをCargo.tomlに追加する必要がある
  + JavaScriptなどの非同期処理とほぼ同じことを行なっている

```shell
$ cargo add futures
```

```rust
// 基本的な使い方
use futures::executor;

// async fnは、impl Futureのシンタックスシュガー
async fn async_add(left: i32, right: i32) -> i32 {
    left + right
}

async fn something_great_async_function() -> i32 {
    // .awaitは、async fnの内部、あるいは、asyncブロック内でしか呼び出せない
    // メソッドチェーンの形式で記述
    let ans = async_add(2, 3).await; // この時点で計算結果を取り出せる
    
    // 何らか処理を実行することもできる
    
    println!("{}", ans);
    ans
} 

fn main() {
    // 関数を実行すると、5が出力される
    // async fnを実行するためには、executor::block_on関数が必要
    executor::block_on(something_great_async_function());
}


// asyncブロックやasync fnの外側で.awaitを呼び出すとコンパイルエラーとなる
use futures::executor;

async fn async_add(left: i32, right: i32) -> i32 {
    left + right
}

fn something_wrong_function() -> i32 {
    let ans = async_add(2, 3).await;
    ans
}

fn main() {
    executor::block_on(something_wrong_function());  
}


// .awaitごとに値を受け取る
use futures::executor;

async fn async_add(left: i32, right: i32) -> i32 {
    left + right
}

// .awaitが存在した場合、.awaitごとに値を受け取る
// 非同期関数内で、.awaitまで逐一処理を進める
async fn something_great_async_function() -> i32 {
    let ans1 = async_add(2, 3).await; // この時点で、値5が取り出せる。
    let ans2 = async_add(3, 4).await; // 7
    let ans3 = async_add(4, 5).await; // 9
    let result = ans1 + ans2 + ans3;
    
    println!("{}", result);
    result // 21 = 5 + 7 + 9
}

fn main() {
    executor::block_on(something_great_async_function());
}


// async構文は、その都度.awaitをしない限り中身が評価されない
// コンパイルのときに警告として表示されるため、気がつきやすい
use futures::executor;

async fn async_add(left: i32, right: i32) -> i32 {
    left + right
}

async fn print_result(value: i32) {
    println!("{}", value);
}

async fn calculate() -> i32 {
    let add1 = async_add(2, 3).await;
    
    // 出力されない
    // print_result(add1);
    // 出力される
    print_result(add1).await;
    
    let add2 = async_add(3, 4).await;
    
    // 出力されない
    // print_result(add2);
    // 出力される
    print_result(add2).await;
    
    async_add(add1, add2).await
}

fn main() {
    executor::block_on(calculate());
}

```

+ ムーブ
  + asyncブロックとクロージャには、moveキーワードを使用できる
    + async moveブロックは、変数が同ブロック内でも生存できるようにその変数の所有権を移す

```rust
fn move_to_async_block() -> impl Future<Output = ()> {
  // 文字列を持つ変数を定義
  let outside_variable = "this is outside".to_string();

  // 通常: outside_variableの所有権がなくなるので、コンパイルは通らない
  async move {
    // moveキーワードを用いたことで、変数の所有権をasyncブロック内に移し、ブロック内でも使用できるようにした
    println!("{}", outside_variable);
  }
}
```

+ ライフタイム
  + 通常の非staticなライフタイムであれば、async内におけるライフタイムはほぼ問題にならない
    + Q: 自分の理解力が怪しいのか、何を言っているかよく分からない

```rust
// ライフタイム'aをもち、戻り値がFutureである関数に内部的に変換
async fn some_great_function(arg: &i32) -> i32 {
  *arg
}

// コンパイラが上記のコードを以下のように展開
fn some_gread_function<'a>(arg: &i32) -> impl Future<Output = i32> + 'a {
    async move {
      *arg
    }
}


// スレッド間でFutureの値を送りたいときは、'staticライフタイムを用いる必要がある
fn some_great_function() -> impl Future<Output = i32> {
    let value: i32 = 5;
    // valueの所有権は終了
    send_to_another_thread_with_borrowing(&value)
    // 上記のコードは、コンパイルエラーになる
}

async fn send_to_another_thread_with_borrowing(x: &i32) -> i32 {
    // 別スレッドに送る処理が書かれていると想定
}


// valueの所有権を引き延ばすことで対処
fn some_great_function() -> impl Future<Output = i32> {
  async {
    let value: i32 = 5;
    send_to_another_thread_with_borrowing(&value).await // asyncとawaitを追加
  }
}
```

+ 非同期ランタイム
  + 非同期計算の実行環境を指す
  + 2020年時点の代表的なクレート
    + tokio
    + async-std

+ tokio
  + 多くのRustライブラリが依存している
    + 例: HTPPを扱うhyperやwarp
  + Cargo.tomlに追加
    + macroフィーチャを追加することで、#[tokio::main] attributeが利用可能になる
    + main関数をasync化できる

```toml
[dependencies]
tokio = { version = "x.x.x", features = ["macros"] }
```  

```rust
async fn add(left: i32, right: i32) -> i32 {
    left + right
}

#[tokio::main]
async fn main() {
    let ans = add(2, 3).await;
    println!("{}", ans);
}
```

+ async-std
  + 2020年時点では、やや新参の扱い
  + tokioの課題: 内部実装やAPIがやや複雑
  + より直感的で分かりやすい非同期ランタイムを作るというモチベーション
  + 利用例
    + HTTPサーバのtide、HTTPクライアントのsurf

```toml
[dependencies]
async-std = { version = "x.x.x", features = ["attributes"] }
```  

```rust
async fn add(left: i32, right: i32) -> i32 {
    left + right
}

// tokioとは、属性が異なるだけと思えばよいか?
// _でつないでいる
#[async_std::main]
async fn main() {
    let ans = add(2, 3).await;
    println!("{}", ans);
}
```

+ async-trait
  + 2020年時点では、トレイトの関数にasyncをつけることはできない
  + cargo add async-traitで追加すると利用できる

```rust
// コンパイルエラー
trait AsyncTrait {
    async fn f() {
        println!("Could't compile");
    }
}


// コンパイルできる
use async_trait::async_trait; // 0.1.57


// #[attr_name]となっている
#[async_trait]
trait AsyncTrait {
    async fn f() {
        println!("Could compile");
    }
}


// トレイトの中身を別途実装する場合は、実装先にも属性を付与すればよい 
use async_trait::async_trait; // 0.1.57

#[async_trait]
trait AsyncTrait {
    async fn f(&self);
}

struct Runner {}

#[async_trait]
impl AsyncTrait for Runner {
    async fn f(&self) {
        println!("Hello, async-trait");
    }
}
```

### クレートとモジュール

+ 関連性のある機能別にソースコードをまとめて、複数のファイルに分ける
+ クレート: 他言語のライブラリやパッケージと同義
  + 複数のモジュールをまとめたもの
+ モジュール: クレートよりも一段階小さな構成要素
  + 特定の処理をまとめる

+ 入門クレート
  + 外部クレート
    + crates.ioなどで見つけれられる
    + 追加方法
      + cargo serach + キーワードを付与して実行
      + cargo-editを導入した場合は、cargo addで追記できる
    + Cargo.tomlの編集が終わると、cargoコマンドがビルド時に外部クレートの依存関係を解決し、必要なものをダウンロードしてくれる
  + クレートの作成
    + cargo new --libsにプロジェクト名を付与して実行すると、必要最小限のテンプレートを準備できる
      + srcディレクトリに、lib.rsが作成される
        + lib.rsにmain()を不要
      + 公開するモジュール、構造体、型、トレイトなどにpubを付ける

+ 入門モジュール

```rust
// 一つのファイルに複数のモジュールを作る
// modキーワードにモジュール名とコードを続けて書く
// 小さなモジュールでない限りは、1つのファイルには1つだけモジュールを書く方が良い
// mod module_hogeと書く?moduleの記述は必須なのか?
mod module_a {
  fn calc() {

  }
}

mod module_b {
  fn calc() {

  }
}
```

```shell
// ファイルごとにモジュールを作る
// Rustでは、基本的に1つのソースコードが一つのモジュールとして扱われる

cargo new lib new_crates
cd new_crates
touch src/module_a.rs
touch src/module_b.rs
tree src
```

```rust
// module_hoge.rsは、module_hogeという名前のモジュールとして扱われる
// src/lib.rs
// 実行可能なバイナリクレートの場合は、main.rsに書く
mod module_a;
mod module_b;
```

+ サブモジュールを作る
  + 問題点: モジュールが大きくなると、1つのファイル収めるのが難しくなる
  + 解決方法: コードを細分化して、複数のファイルに分割
  + サブモジュールにサブモジュールを持つこともできる

```
// 例
module_b
  - module_c.rs
  - module_d.rs
module_b.rs
```

```rust
// サブモジュール
// module_bに各モジュールが属していることを示すため、宣言が必要
// module_b.rs
mod module_c;
mod module_d;
```

+ モジュールを公開

```rust
// pubキーワードをつけて公開
// Note: 必要なものだけ公開するようにする
// Q: 他の言語のpublicと大体同じと思えば良いか?
pub mod module_a;
mod module_b;


// main.rs
// 外部クレートの場合
// use crate_name::module_name;の形式でインポート
// 外部クレート名をルートディレクトリと見立て、目的のモジュールまでのパスを「::」でつなぐ
use new_crate::module_a;

fn main() {

}


// 同じパッケージ内のモジュールを利用する場合
// クレート名の代わりにcrateを使う

// src
//   - lib.rs
//   - module_a.rs
//   - module_b.rs

// 絶対パスで指定
use crate::module_b;

// 相対パスで指定
// super: 一つ上の階層から相対パスで指定できる
// self: 自分自身の場所から相対パスで(ry
// src
//   - lib.rs
//   - module_a.rs
//   - module_b
//      - module_c.rs
//   - module_b.rs

// src/module_a.rs
// module_aからmodule_cを指定する場合
use crate::module_b::module_c;
use super::module_b::module_c;

// module_c.rsからmodule_a.rsを指定
use crate::module_a;
use super::super::module_a;
```

### Cargo - ビルドシステム・パッケージマネージャ

+ Cargo
  + ビルドシステムとパッケージマネージャの役割を持つ
  + ソースコードをビルドするときに、ライブラリの依存関係を解決し、必要なライブラリをDLして、コンパイル
  + 配布用のパッケージを作成するときも使う

+ サブコマンド
  + プロジェクトの開始、ビルド、実行、テストなどのサブコマンドを持っている
  + よく使うものから順に

+ cargo new
  + 外部クレートを使用する場合は、Cargo.tomlの[dependencies]の下に記述
  + サンプルのソースコードでは、`Hello, world!`を出力する実装になっている

```terminal
cargo new [OPTIONS] <path>

- Cargo.toml // パッケージの情報
- .git
- .gitignore
- src
  - main.rs
```

+ cargo init
  + cargo newとほぼ同じ
  + 違い: すでに存在するディレクトリに対してRustの構成ファイルを追加できる

```terminal
cargo init [OPTIONS] <path>
```

+ cargo build
  + ソースコードをビルド
  + 全ての外部クレートの依存関係を解決して、必要なクレートをダウンロードをした上でビルドを行う
  + オプションを指定しないとdevプロファイルでビルドされる
    + デバッグしやすいように最適化されていない状態でコンパイルされる
  + オプションでreleaseを指定すると、releaseプロファイルでビルド
  + ビルドの最中で出来上がる成果物は、targetディレクトリ内に保存される
    + target/debug or target/releaseに
  + ビルドは並列のジョブで実行される。デフォルトはCPUの数。
    + -j N or --jobs N (N: 数値)オプションを付与

```terminal
cargo build [OPTIONS]
```

+ cargo check
  + パッケージと全てのクレートに対して、コードのエラーチェックを行う
  + buildコマンドと異なり、バイナリファイルを生成しないので、エラーチェックのときに使用

```terminal
cargo check [OPTIONS]
```

+ cargo run
  + パッケージ内のバイナリファイルを実行
  + ビルドが必要な場合は、ビルドを行ってからバイナリファイルを実行
  + buildコマンドと同様にデフォルトではdevプロファイルでビルド
  + バイナリファイルを引数つきで実行したい場合は、2つのダッシュ(--)の後に渡したい引数を追加

```terminal
cargo run [OPTIONS] [-- ARGS]
```

+ cargo test
  + パッケージに含まれているテストコードを実行
  + テスト用バイナリファイルは、#[test]アトリビュートがついた全ての関数を複数のスレッドで実施
  + TESTNAMEをつけることで、特定のテストケースのみ実行できる

```terminal
cargo test [OPTIONS] [TESTNAME] [-- TEST-OPTIONS]
```

+ cargo fix
  + ソースコードにある不備や改善点を発見し、警告の中で修正可能なものを自動的に修正

+ cargo clean
  + ビルドで作られた生成物を削除
  + デフォルト: targetディレクトリごと全て削除
    + --release: releaseプロファイルの生成物のみ削除
    + -p SPEC or --package SPEC: SPECの生成物のみ削除
    + --doc: ドキュメント関連のみ削除

```terminal
cargo clean [OPTIONS]
```

+ cargo doc
  + パッケージに含まれているソースコードと全ての依存関係にあるクレートのドキュメントを作成
    + target/docディレクトリに作成される
    + --open: ドキュメント作成完了後に自動でブラウザを開いてくれる
    + --no-dpes: 依存関係にあるライブラリのドキュメントを作成したくない場 合
    + --document-private-items: 非公開の要素も含めてドキュメント化
  + cargo publish: 開発したクレートがcrates.ioに公開される。ドキュメントに関しては、Docs.rsに公開される。

```terminal
cargo doc [OPTIONS]
```

+ cargo install
  + デフォルトはcrates.ioからパッケージをDL
    + --git、--path、--registryでダウンロード元を変更できる
  + インストール先のdir: ~/.cargo
    + --root DIRオプションで変更できる
    + 環境変数CARGO_INSTALL_ROOT.Cargo.toml内のinstall.root、環境変数CARGO_HOMEにパスを指定している場合は、変数でしたパスにインストールされる

```terminal
cargo install [OPTIONS] [CRATE]
```

+ cargo uninstall
  + cargo installでインストールしたバイナリファイルをアンインストールする
  + 削除対象を--root DIRオプションで指定できる

```terminal
cargo uninstall [OPTIONS] [SPEC]
```

+ cargo search
  + crates.ioで公開されているクレートを検索できる
  + 検索したいキーワードともに実行すれば、Cargo.tomlに直接コピペできるフォーマットで得られる

```terminal
cargo serach [OPTIONS] [QUERY]
```

+ cargo publish
  + ソースコードのパッケージを.cratesファイルに圧縮し、クレートをcrates.ioにアップロードして、世界に公開する
  + Cargo.tomlの情報をもとにcrates.ioの説明文やDocs.rsのリンクが追加される
    + authors
    + license or license-file
    + description
    + homepage
    + documentation
    + repository
    + readme
  + --dry-runオプションで、アップロードはせずに、全てのチェックができる

+ Cargo.toml
  + TOMLフォーマットで書かれたパッケージの設定ファイル(パッケージのマニフェストとも)

## 疑問点

+ 所有権が難しいらしいが・・・。
  + 最もシンプルな例なら理解できた気がする
    + 例題も怪しいかも
  + 問題は、FWや業務レベルといった大規模なコードになったとき

+ マクロとは、C++のものに似ている?

+ 構造体と列挙型の使い分けはどうしている?
  + 構造体は、単純に型だけ指定?
  + 列挙型は、メソッドがセットになっているイメージ

+ unsafeブロックとは?
  + safeではない + ブロック = 変数や処理の有効な範囲と思えばよい?

+ トレイトの導出の意義・メリットがよく分かっていない

+ 変数の生存期間が終了するタイミングとして、主に何がある?
  + テキストを見落とした?

+ ムーブの使いどころは? 

## 感想

+ 。

+ 。

## 抽象化

+ 。

+ 。

## 応用

+ 。

+ 。

## 次のステップ

+ 公式ドキュメントを読む
  + 最新の情報をキャッチアップ
+ 本書との違いを学ぶ
+ Webアプリを作成 & 公開する

## See
