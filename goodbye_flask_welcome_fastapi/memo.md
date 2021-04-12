# 「さよならFlask ようこそFastAPI」を読んで要約

## 現状

+ FastAPIのチュートリアルに軽く目を通した程度。
+ 実務でどのように使われているか知りたい。

## 目標

+ 現役エンジニアの知見を学ぶ。

+ 個人開発でWebAPIを作り、公開する。

## AI案件でよくあるタスクをFastAPIで

+ Flaskでの処理
  + グローバルでモデルをロード
  + リクエストが来たら推論し、JSONを組み立ててレスポンスを返す
  + 作成したAPIサーバをDockerfileにまとめる
  + APIの仕様を決め、他の人と協業して周囲のシステムとつなぎ合わせる

+ FastAPIに乗り換えられる

+ 嬉しいポイント
  + FlaskやDjangoと比べて速い
  + シンプルに書けて、Flaskからの移行が楽
  + リクエストとレスポンスの型定義が書ける
  + ドキュメントが自動生成される(OpenAPI規格を利用)
  + 非同期処理をサポート

+ jsonでレスポンスを返すためには、dict型を戻り値として指定すればよい
+ モデルの訓練
  + モデルをpickle形式でダンプ
+ pydantic.BaseModelを継承して型定義を書く
  + メリット
    + 補完が効く
    + リクエストを受けたときに自動で変換される
    + リクエストのバリデーションが実行される
    + ドキュメントが自動生成される
  + デフォルト値やOptional型を利用することも可能
+ バリデーション(min, max, length, 正規表現)が、pydantic.Fieldで指定できる
  + 違反している場合は、422 Unprocessable Entityが返る
+ レスポンスの型定義も、pydantic.BaseModelを継承
  + 関数に、response_modelを指定する
  + レスポンスの形式が間違っていると、500 Internal Server Errorが返る
+ バックグラウンドでタスクを実行することもできる
  + レスポンスだけ先に返して、時間のかかる処理を実行することができる
  + fastapi.BackgroundTasksに関数を渡すだけでよい
+ APIをテストできる
  + fastapi.testclient.TestClient
    + status_codeや結果のテストが直感的に書ける
    + pytestを利用することも可能
+ ビューをつけることもできるが、あくまでもAPIを作るためのライブラリ
+ FastAPIの作者が公開しているDockerイメージがある
+ CORSの設定
  + ユーザが異なるオリジン経由でアクセスしたときもアクセスを許可する仕組み
  + CORSMiddlewareを追加するだけで対応できる
+ Flaskと共存することも可能
+ GraphQLにも対応できるらしい

## 疑問点

+ 機械学習系のタスク以外の用途はある?

+ GraphQLとは?

## 感想

+ 語りかけるような表現で、とても読みやすい。

+ 。

## 抽象化

+ これまでの業務を新しい技術を使うことで楽になる事例を紹介。
+ 嬉しいポイントを列挙。
+ ベンチマークを示す。
+ 全体像から詳細へ。

## 応用

+ 。

+ 。

## See

https://speakerdeck.com/amaotone/goodbye-flask-welcome-fastapi
