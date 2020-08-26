# FastAPIチュートリアル

## 現状

+ オンライン勉強会での資料に目を通した程度。

+ DBと連携したappは作れていない。

## 目標

+ DBと連携したappを作れるようにする。

+ 上記で作成したAPIを表示できるようにする。

## Run the code

```bash
$ uvicorn main:app --reload

INFO: Uvicorn running on http://127.0.0.1:8000 (Press CTRL+C to quit)
INFO: Started reloader process [28720]
INFO: Started server process [28722]
INFO: Waiting for application startup.
INFO: Application startup complete.
```

## Install FastAPI

```bash
# 依存関係にあるものもインストール
$ pip install fastapi[all]

# 個別にインストールすることもできる
pip install fastapi

## サーバとして利用
pip install uvicorn
```

## First Steps

```py
# 最もシンプルな例
# main.py
from fastapi import FastAPI

app = FastAPI()


@app.get("/")
async def root():
    return {"message": "Hello World"}
```

```bash
# ファイル名/app = FastAPI()
$ uvicorn main:app --reload
```

### Check it

+ ブラウザで、http://127.0.0.1:8000 を入力して開く

```json
{"message": "Hello World"}
```

### Interactive API docs

+ Now go to http://127.0.0.1:8000/docs
+ インタラクティブなAPIドキュメントを見ることができる
  + Swagger UIによって提供されている

### Alternative API docs

+ And now, go to http://127.0.0.1:8000/redoc
+ ドキュメントが見られる
  + ReDocによって提供されている

### OpenAPI

+ FastAPIは、OpenAPIを利用して"schema"を生成する
  + schema: 定義や何かに関する記述
  + API schema: どのようにAPIのschemaを定義するかを示す。パスやパラメータを含む
  + Data schema: データの形状。JSON
  + OpenAPI and JSON Schema: OpenAPIはAPIのAPI schemaを定義する。JSON Schemaを使ったデータの送信や受信に使う定義を含む。

http://127.0.0.1:8000/openapi.json

```json
// ブラウザで見られるjsonファイルは、整形されていない。
{
    "openapi": "3.0.2",
    "info": {
        "title": "FastAPI",
        "version": "0.1.0"
    },
    "paths": {
        "/items/": {
            "get": {
                "responses": {
                    "200": {
                        "description": "Successful Response",
                        "content": {
                            "application/json": {



...
```

#### What is OpenAPI for

+ OpenAPIスキーマは、2つのインタラクティブなドキュメントシステムを動かすものです。
  + 代替手段はたくさんあるが、全てOpenAPIがベースになっている
  + FastAPIにOpenAPI以外の方法を組み込むことも簡単にできる

### Recap, step by step

+ Recap: 復習

```py
# FastAPIは、Starletteを直接継承したPythonのクラス
# Step 1: import FastAPI
from fastapi import FastAPI

# Step 2: create a FastAPI "instance"
# APIを作成するための主要なポイントになる
app = FastAPI()
```

```md
Step 3: create a path operation

URL:
https://example.com/items/foo

Path(endpoint or route):
<!-- ドメインを除いた部分と考えて良い? -->
<!-- 関心とリソースを分離するための主要な方法 -->
/items/foo
```

```py
from fastapi import FastAPI

app = FastAPI()


# Path: /
# HTTPプロトコルのうち、GETメソッドを使っている
# デコレータを使って、該当するメソッドに追加するだけ
@app.get("/")
async def root():
    return {"message": "Hello World"}

# 他のメソッドも、@app.xxx()という形式でサポートされている
# FastAPIにおいて、各メソッドの利用に特定の意味を持たせることはしていない
# 例: GraphQLでは、POSTしか使わないのが通例
@app.post()
@app.put()
@app.delete()
```

```py
# Step 4: define the path operation function
from fastapi import FastAPI

app = FastAPI()


# Pythonの関数として記述する
# async: 非同期処理。使わない場合は、外す。
@app.get("/")
async def root():
    # Step 5: return the content
    # dict, list、単独の値(str, intなど)、Pydanticモデルを返すことができる
    # 自動的にJSONに変換される
    return {"message": "Hello World"}

# 開発サーバを起動・リロードする
```

## Path Parameters

```py
from fastapi import FastAPI

app = FastAPI()

# パスのパラメータや変数は、Pythonの形式と同じシンタックス
# パスパラメータのitemd_idは、関数内でitem_id変数として扱われる
@app.get("/items/{item_id}")
async def read_item(item_id):
    return {"item_id": item_id}
```

http://127.0.0.1:8000/items/fooにアクセスすると、

```json
{"item_id":"foo"}
```

### Path parameters with types

+ Pythonのtype annotationsを使うことで、パスパラメータの型を宣言できる

```py
from fastapi import FastAPI

app = FastAPI()

#
# パスパラメータは、宣言した型に自動で変換される
@app.get("/items/{item_id}")
async def read_item(item_id: int):
    return {"item_id": item_id}
```

 http://127.0.0.1:8000/items/3にアクセス

```json
{"item_id":3}
```

### Data validation

http://127.0.0.1:8000/items/fooやhttp://127.0.0.1:8000/items/4.2にアクセスすると、

エラーの内容が表示される

```json
{
    "detail": [
        {
            "loc": [
                "path",
                "item_id"
            ],
            "msg": "value is not a valid integer",
            "type": "type_error.integer"
        }
    ]
}
```

### Documentation

+ http://127.0.0.1:8000/docsにアクセス
+ Try it outで、パラメータを入力してレスポンスを確認できる
  + 指定した型以外の入力をしようとすると、その時点で入力が正しくないというメッセージとともに、再入力を促される

### Pydantic

+ データのバリデーションを裏で実行してくれているので、開発時はその存在を意識しなくて済む

### Recap

+ Pythonの型宣言による効果により、一度の定義すれば以下の効果が得られる
  + エディタのサポート
  + データのパース
  + バリデーション
  + APIアノテーション、自動ドキュメント
+ intuitive: 直感的

## 疑問点

+ Swagger UIとは?
+ ReDocとは?
+ Swagger UIとReDocの違いは?なぜ2つある?
+ OpenAPIのメリットがイマイチ分かっていない

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
