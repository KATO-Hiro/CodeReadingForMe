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
# FastAPIをインポート
from fastapi import FastAPI

# インスタンスを作成
app = FastAPI()


# デコレータで、HTTPメソッドとルーティングを指定
# 関数を定義して、値を返す
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
# 作成した@インスタンス名.HTTPプロトコルのメソッド("パス")
@app.get("/")
async def root():
    return {"message": "Hello World"}

# 他のメソッドも、@app.xxx()という形式でサポートされている
# FastAPIにおいて、各メソッドの利用に特定の意味を持たせることはしていない
# 例: GraphQLでは、POSTしか使わないのが通例
@app.post() # to create data
@app.put() # to update data
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

### Order matters

```py
from fastapi import FastAPI

app = FastAPI()


# パスは、順番に評価される
# 逆にすると、meが{user_id}に入る
@app.get("/users/me")
async def read_user_me():
    return {"user_id": "the current user"}


@app.get("/users/{user_id}")
async def read_user(user_id: str):
    return {"user_id": user_id}
```

### Predefined values

+ 有効なパスパラメータを事前に定義しておくこともできる

```py
# EnumをImport
from enum import Enum

from fastapi import FastAPI


# str, Enumを継承する
class ModelName(str, Enum):
    alexnet = "alexnet"
    resnet = "resnet"
    lenet = "lenet"


app = FastAPI()


# 引数の型に自作のクラスを取ることもできる
@app.get("/model/{model_name}")
async def get_model(model_name: ModelName):
    # ClassName.attrの形式で利用できる
    if model_name == ModelName.alexnet:
        # 戻り値にmodel_nameを取ることもできる
        return {"model_name": model_name, "message": "Deep Learning FTW!"}

    # model_name.value == 'hoge'の形式も利用できる
    if model_name.value == "lenet":
        return {"model_name": model_name, "message": "LeCNN all the images"}

    return {"model_name": model_name, "message": "Have some residuals"}
```

### Path parameters containing paths

+ /files/{file_path}
  + 例: file_pathにhome/johndoe/myfile.txtのようなパスを含む場合は、URLを/files/home/johndoe/myfile.txtとする

+ OpenAPIでは上記のような書き方をサポートしていないが、Starletteの内部ツールを使うことで利用できる
  + /docsも特別に何かしなくても動作する

### Path convertor

```md
file_pathがパラメータの名前で、:pathにパスが対応している
/files/{file_path:path}
```

```py
from fastapi import FastAPI

app = FastAPI()


# Note: パラメータは/home/johndoe/myfile.txtのように、先頭にスラッシュ(/)を入れる
# /files//home/johndoe/myfile.txt
@app.get("/files/{file_path:path}")
async def read_file(file_path: str):
    return {"file_path": file_path}
```

### Recap

+ Pythonの型宣言による効果により、一度の定義すれば以下の効果が得られる
  + エディタのサポート
  + データのパース
  + バリデーション
  + APIアノテーション、自動ドキュメント
+ intuitive: 直感的

## Query Parameters

+ 宣言したパラメータが、パスパラメータではないとき自動的に「クエリパラメータ」になる

```py
from fastapi import FastAPI

app = FastAPI()

fake_items_db = [{"item_name": "Foo"}, {"item_name": "Bar"}, {"item_name": "Baz"}]


@app.get("/items/")
async def read_item(skip: int = 0, limit: int = 10):
    return fake_items_db[skip : skip + limit]
```

```md
// ?の後に、kye-valueのペアで
// &で区別する
// skipの値が0、limitの値が10であることを表す
// limitで取り出すデータの数を指定
// 元々は文字列だが、Pythonのデータ型に変換される
// クエリパラメータを指定しない場合は、デフォルト値が設定される(一部のパラメータだけ指定して、上書きすることも可能)
http://127.0.0.1:8000/items/?skip=0&limit=10
```

### Optional parameters

```py
from typing import Optional

from fastapi import FastAPI

app = FastAPI()


# Optionalを使って、デフォルト値をNoneに指定することもできる
@app.get("/items/{item_id}")
async def read_item(item_id: str, q: Optional[str] = None):
    if q:
        return {"item_id": item_id, "q": q}
    return {"item_id": item_id}
```

### Query parameter type conversion

+ bool型も定義できる

```py
from typing import Optional

from fastapi import FastAPI

app = FastAPI()


@app.get("/items/{item_id}")
async def read_item(item_id: str, q: Optional[str] = None, short: bool = False):
    item = {"item_id": item_id}
    if q:
        item.update({"q": q})
    if not short:
        item.update(
            {"description": "This is an amazing item that has a long description"}
        )
    return item
```

```md
http://127.0.0.1:8000/items/foo?short=True

Trueの部分が、1, true, on, yesでもTrueと判定される
それ以外は、False
```

### Multiple path and query parameters

+ 複数のパス、クエリパラメータを定義することもできる
  + 順序の指定はない

```py
from typing import Optional

from fastapi import FastAPI

app = FastAPI()


@app.get("/users/{user_id}/items/{item_id}")
async def read_user_item(
    user_id: int, item_id: str, q: Optional[str] = None, short: bool = False
):
    item = {"item_id": item_id, "owner_id": user_id}
    if q:
        item.update({"q": q})
    if not short:
        item.update(
            {"description": "This is an amazing item that has a long description"}
        )
    return item
```

### Required query parameters

+ クエリパラメータの指定を強制することもできる

```py
from fastapi import FastAPI

app = FastAPI()


@app.get("/items/{item_id}")
async def read_user_item(item_id: str, needy: str):
    item = {"item_id": item_id, "needy": needy}
    return item
```

```md
// needyが指定されていないため、エラーが発生する
http://127.0.0.1:8000/items/foo-item

// クエリパラメータを指定することで動作
http://127.0.0.1:8000/items/foo-item?needy=sooooneedy
```

```py
from typing import Optional

from fastapi import FastAPI

app = FastAPI()


# クエリパラメータにデフォルト値や任意の値を指定することもできる
# needy: 必須
# skip: int型。デフォルト値が0
# limit: int型。任意の値。
@app.get("/items/{item_id}")
async def read_user_item(
    item_id: str, needy: str, skip: int = 0, limit: Optional[int] = None
):
    item = {"item_id": item_id, "needy": needy, "skip": skip, "limit": limit}
    return item

# Note: パスパラメータと同様に、Enumを使うこともできる
```

## 疑問点

+ CRUDの書き方は?

+ 管理者アカウントは楽に作れる?

+ データを保存する方法は?
  + MySQL
  + Firebase

+ Vercelにデプロイできる?

+ CORS対策は?

+ mypyによる型チェックに対応している?
  + Yes

+ Pydanticとは?

+ Pytestは使える?

+ 標準的なディレクトリ構成は?
  + APIとテストは分離している?
  + APIについても、機能ごとに分けることは可能?

+ Pythonにおける`async`の使いどころとは?

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
