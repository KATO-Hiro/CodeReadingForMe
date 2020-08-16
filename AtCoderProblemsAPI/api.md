# AtCoder Problemsで提供されているAPIについてメモ

## 目標

+ APIで提供されている情報を把握する
  + これから進めるプロジェクトで使いそうなAPIを優先的に
+ APIのお作法を学ぶ

## Information API

### Contests Information

+ url: https://kenkoooo.com/atcoder/resources/contests.json

```md
// Sample
"id":"agc012",
"start_epoch_second":1491048000,
"duration_second":6600,
"title":"AtCoder Grand Contest 012",
"rate_change":"All"
```

+ id: コンテストの略称。文字列。
+ start_epoch_second: 開始時間?。10桁の整数。
+ duration_second: コンテスト時間(UNIX時間。秒)。整数。
+ title: コンテスト名。文字列。
  + 日本語表記も含まれている。
+ rate_change: Rated対象。文字列。
  + "-": 該当なし
  + " ~ 1199": ABC級。〜ABC125
  + " ~ 1999": ABC級。ABC126〜
  + " ~ 2799": ARC級。
  + "1200 ~ ": AGC級。AGC045〜
  + "All": 全員

Note: domain/servicename/resources/foo.jsonという形式で結果を返している。

### Problems Information

+ url: https://kenkoooo.com/atcoder/resources/problems.json

```md
// Sample
"id":"abc174_f",
"contest_id":"abc174",
"title":"F. Range Set Query"
```

+ id: 問題id。文字列。
+ contest_id: コンテストの略称。文字列。Contests Informationのidに相当。
+ title: 問題のタイトル名。文字列。

### Detailed Problems Information

```md
// Sample
"id":"abc174_f",
"contest_id":"abc174",
"title":"F. Range Set Query",
"shortest_submission_id":15670987,"shortest_problem_id":"abc174_f",
"shortest_contest_id":"abc174",
"shortest_user_id":"hogehoge",
"fastest_submission_id":15666041,
"fastest_problem_id":"abc174_f",
"fastest_contest_id":"abc174",
"fastest_user_id":"foo",
"first_submission_id":15593804,
"first_problem_id":"abc174_f",
"first_contest_id":"abc174",
"first_user_id":"bar",
"source_code_length":217,
"execution_time":76,
"point":600.0,
"predict":null,
"solver_count":2282
```

+ id: 問題id。文字列。
+ contest_id: コンテストの略称。文字列。Contests Informationのidに相当。
+ title: 問題のタイトル名。文字列。
+ shortest_submission_id: コード長が最も短いACコードのID。整数。
+ shortest_user_id: 上記のコードを提出したユーザ名。文字列。
+ fastest_submission_id: 実行時間が最も短いACコードのID。整数。
+ fastest_contest_id: 上記のコードを提出したユーザ名。文字列。
+ first_submission_id: 最初にACしたコードのID。整数。
+ first_user_id: 上記のコードを提出したユーザ名。文字列。
+ source_code_length: 最も短いACコードの長さ(Byte)。整数。
+ execution_time: 最も速いACコードの実行時間(ms)。整数。
+ point: 問題の配点。小数。
+ solver_count: ACした人数だと思われる。整数。

### Pairs of Contests and Problems

+ url: https://kenkoooo.com/atcoder/resources/contest-problem.json

```md
// Sample
"contest_id":"abc063",
"problem_id":"arc075_b"
```

+ contest_id: コンテストの略称。文字列。
+ problem_id: 問題id。文字列。

## Submission API

### User Submissions

+ url: https://kenkoooo.com/atcoder/atcoder-api/results?user={user_id}

```md
"id":15946183,
"epoch_second":1597497132,
"problem_id":"abc175_f",
"contest_id":"abc175",
"user_id":"hoge",
"language":"Rust (1.42.0)",
"point":0.0,
"length":3509,
"result":"RE",
"execution_time":9
```

+ id: 提出コードのID。整数。
+ epoch_second: 提出日時(UNIX時間。秒)。整数。
+ problem_id: 問題id。文字列。
+ contest_id: コンテストの略称。文字列。
+ user_id: ユーザID。文字列。
+ language: 提出言語とバージョン。文字列。
+ point: 得点。小数。
+ length: コード長(Byte)。整数。
+ result: 提出結果。文字列。
  + "AC", "WA", "RE", "CE", "TLE", "MLE", "IE"
  + Note: "OLE"かあるか確認中。
+ execution_time: 実行時間(ms)。整数。

Note: APIにidなどを含める必要がある場合は、Interfaceとサンプルをセットで用意すると親切。
Note: ユーザを指定すれば、これまで提出したコードの結果は把握できると思われる。日時指定したAPIでは、取得数に上限があるように思われるため。

### Submissions at the time

+ url: https://kenkoooo.com/atcoder/atcoder-api/v3/from/{unix_time_second}

```md
"id":15961285,
"epoch_second":1597500481,
"problem_id":"abc175_c",
"contest_id":"abc175",
"user_id":"foo",
"language":"C++ (GCC 9.2.1)",
"point":300.0,
"length":673,
"result":"AC",
"execution_time":6
```

+ Note: User Submissionsとデータの形式は同じ。
+ Note: 指定した時間から約1,000件が取り出されていると思われる。

## Datasets

### Estimated Difficulties of the Problems

+ url: https://kenkoooo.com/atcoder/resources/problem-models.json

```md
"abc175_d":
  {"slope": -0.0005393111945505065,
   "intercept": 8.718381589862668,
   "variance": 0.23070194224682009,
   "difficulty": 1456.3944152145057,
   "discrimination": 0.003714029219233902,
   "irt_loglikelihood": -1480.4721135093816,
   "irt_users": 3580,
   "is_experimental": false
  }
```

+ slope: 傾き。小数。
+ intercept: 切片。小数。
+ variance: 分散。小数。
+ difficulty: 50%のユーザがACできる内部レーティング。小数。
+ discrimination: 識別値? 小数。
+ irt_loglikelihood: 推定結果?に関する対数尤度。小数。
+ irt_users: ユーザの数?。整数。
+ is_experimental: 実験的なものかどうか。Boolean。

## 疑問点

+ ABCとARCが同日開催されている場合の処理を、本家ではどのように行なっている?
  + 該当箇所を見つけて読む

+ Detailed Problems Informationのsolver_countは、コンテスト本番以外のACした人数をカウントしている?
  + 同一人物が複数回ACした場合はどうなっている?

+ irtとは?
  + in real-timeという略語もあるらしいが、なんとなく違う気がする。

+ APIを叩いてDBに保存する処理をシンプルに書くには?
  + 特に、差分を更新するときに、全探索的なアプローチだと大変だった。

+ jsonファイルを整形する/しない?
  + 整形する: 可読性が高まる
  + 整形しない: ファイルサイズを小さくできる?

+ APIとビューの実装の順番は、どちらにするとより進めやすい?
  + バックエンドでAPIの仕様をある程度決めてから、フロントエンドを実装する
    + メリット: 開発者側がどんなAPIとなるのか、イメージしやすい。
    + デメリット: APIの仕様がある程度固まらないと、フロントエンドの実装ができない。APIの仕様が大きく変わると、デスマーチまっしぐらの可能性がある。
  + ダミーデータを使ってフロントエンドをある程度仕上げてしまう。APIは優先順位の高いものから実装する。
    + メリット: 開発者・ユーザともに、仕上がりのイメージがつかみやすい。
    + デメリット: APIとダミーデータの不整合が起こりうる? ユーザからすると、見た目ができあがっているのに、サービス提供までに時間がかかっていると思われる可能性がある。

## 抽象化

+ APIを公開するときは、1秒以上の間隔をあけてアクセスするように促す。

+ domain/servicename/api/vx/foo
  + x: バージョン番号

## 応用

## See

[API Document](https://github.com/kenkoooo/AtCoderProblems/blob/master/doc/api.md)
