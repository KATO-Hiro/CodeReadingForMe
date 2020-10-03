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

## Request Body

+ client(browser)とのやり取りで使用
  + API側: ほぼ必須
  + clients側: いつも必要ではない

  + POSTメソッドを使うのが一般的

```py
# 1. BaseModelをpydanticからインポート
# 2. データモデルを作成
from typing import Optional

from fastapi import FastAPI
from pydantic import BaseModel


# BaseModelを継承
# 属性: 型を指定
# デフォルト値を設定することもできる
class Item(BaseModel):
    name: str
    description: Optional[str] = None
    price: float
    tax: Optional[float] = None


app = FastAPI()


# 関数の引数がスッキリして見やすい
@app.post("/items/")
async def create_item(item: Item):
    return item
```

```md
// 処理の流れ
・リクエストの本体をJSONとして読む
・必要に応じて、対応する型に変換する
・データの検証
・Itemのパラメータを受け取る
・JSONスキーマを生成
・これらがOpenAPIスキーマを生成、自動的にドキュメント化

// SchemasにItemモデルが追加されている
```

### Use the model

```py
from typing import Optional

from fastapi import FastAPI
from pydantic import BaseModel


class Item(BaseModel):
    name: str
    description: Optional[str] = None
    price: float
    tax: Optional[float] = None


app = FastAPI()


# モデルのアクセス方法
# モデル名.属性
@app.post("/items/")
async def create_item(item: Item):
    item_dict = item.dict()
    if item.tax:
        price_with_tax = item.price + item.tax
        item_dict.update({"price_with_tax": price_with_tax})
    return item_dict
```

### Request body + path parameters

+ 上記は同時に定義できる
  + パスパラメータは自動的に判別してくれる

```py
from typing import Optional

from fastapi import FastAPI
from pydantic import BaseModel


class Item(BaseModel):
    name: str
    description: Optional[str] = None
    price: float
    tax: Optional[float] = None


app = FastAPI()


@app.put("/items/{item_id}")
async def create_item(item_id: int, item: Item):
    return {"item_id": item_id, **item.dict()}
```

### Request body + path + query parameters

+ 上記全てを同時に宣言することもできる

```py
from typing import Optional

from fastapi import FastAPI
from pydantic import BaseModel


class Item(BaseModel):
    name: str
    description: Optional[str] = None
    price: float
    tax: Optional[float] = None


app = FastAPI()


@app.put("/items/{item_id}")
async def create_item(item_id: int, item: Item, q: Optional[str] = None):
    result = {"item_id": item_id, **item.dict()}
    if q:
        result.update({"q": q})
    return result
```

## Query Parameters and String Validations

+ 追加の情報の定義やパラメータの検証

```py
from typing import Optional

from fastapi import FastAPI

app = FastAPI()


@app.get("/items/")
async def read_items(q: Optional[str] = None):
    results = {"items": [{"item_id": "Foo"}, {"item_id": "Bar"}]}
    if q:
        results.update({"q": q})
    return results
```

### Additional validation

```py
from typing import Optional

# Queryをインポート
from fastapi import FastAPI, Query

app = FastAPI()


# Query(デフォルト値, max_length=50)
# 50文字まではOKな例
@app.get("/items/")
async def read_items(q: Optional[str] = Query(None, max_length=50)):
    results = {"items": [{"item_id": "Foo"}, {"item_id": "Bar"}]}
    if q:
        results.update({"q": q})
    return results
```

### Add more validations

+ 文字の最小値も指定できる
  + min_length=xx

```py
from typing import Optional

from fastapi import FastAPI, Query

app = FastAPI()


@app.get("/items/")
async def read_items(q: Optional[str] = Query(None, min_length=3, max_length=50)):
    results = {"items": [{"item_id": "Foo"}, {"item_id": "Bar"}]}
    if q:
        results.update({"q": q})
    return results
```

### Add regular expressions

+ 正規表現も利用できる
  + regex="^fixedquery$"で指定
  + ""の中身は、^で開始、fixedqueryで正確な表現を指定、$で終了

```py
from typing import Optional

from fastapi import FastAPI, Query

app = FastAPI()


@app.get("/items/")
async def read_items(
    q: Optional[str] = Query(None, min_length=3, max_length=50, regex="^fixedquery$")
):
    results = {"items": [{"item_id": "Foo"}, {"item_id": "Bar"}]}
    if q:
        results.update({"q": q})
    return results
```

### Default values

+ Queryの第1引数にデフォルト値を設定できる

```py
from fastapi import FastAPI, Query

app = FastAPI()


@app.get("/items/")
async def read_items(q: str = Query("fixedquery", min_length=3)):
    results = {"items": [{"item_id": "Foo"}, {"item_id": "Bar"}]}
    if q:
        results.update({"q": q})
    return results
```

### Make it required

+ パラメータを必須にすることもできる
  + Queryの第1引数に...を指定

```py
from fastapi import FastAPI, Query

app = FastAPI()


@app.get("/items/")
async def read_items(q: str = Query(..., min_length=3)):
    results = {"items": [{"item_id": "Foo"}, {"item_id": "Bar"}]}
    if q:
        results.update({"q": q})
    return results
```

### Query parameter list / multiple values

+ クエリパラメータを複数指定することもできる

```py
from typing import List, Optional

from fastapi import FastAPI, Query

app = FastAPI()


# Optional[List[str]] = Query()で指定
@app.get("/items/")
async def read_items(q: Optional[List[str]] = Query(None)):
    query_items = {"q": q}
    return query_items
```

### Recap

+ 一般的なバリデーションとメタデータ
  + alias: Pythonで有効ではない変数名をパラメータとして使うときの別名を指定できる
  + title:
  + description
  + deprecated: バラメータを廃止することを明示

## Path Parameters and Numeric Validations

+ PathパラメータもQueryと同様に、型の検証とメタデータの定義ができる

### Recap

+ gt: greater than
+ ge: greater than or equal
+ lt: less than
+ le: less than or equal

## Body - Multiple Parameters

+ PathとQueryを組み合わることもできる

## Body - Fields

+ Pydantic's Fieldを使うことで、Pydantic modelの内部の検証とメタデータが定義できる

## Body - Nested Models

+ Pydanticにより、ネストしたモデルを利用することができる。
  + 任意の階層を定義できる
+ List, Setが利用できる
+ 自作のクラスを属性に指定することもできる

## Schema Extra - Example

+ JSONスキーマに追加の情報を定義することもできる
  + 一般的な例として、サンプル情報

## Extra Data Types

+ 複雑なデータ型もサポートされている
  + UUID: Universally Unique Identifier
  + datetime.xxx
  + frozenset
  + bytes
  + Decimal
+ 一覧は、Pydantic data typesを参照

## Cookie Parameters

+ QueryやPathパラメータと同様に定義できる

## Header Parameters

+ Query, Path, Cookieと同様。

## Response Model

+ @app.xxxで、指定
+ モデルを別途作成し、レスポンスに含めない情報を指定できる
  + 例: パスワード

## Extra Models

+ 継承を使って、重複をなくす。ただし、一つの状態しかないエンティティをあえて分割する必要はない
+ 例:
  + Userモデル(基本モデル)
  + パスワードあり
  + パスワードなし
  + ハッシュ化したパスワード(DB保存用)

## Response Status Code

+ HTTPのstatus codeを明示的に指定できる
+ fastapi.statusを使うことで、より明確に示せる

```py
from fastapi import FastAPI

app = FastAPI()


@app.post("/items/", status_code=201)
async def create_item(name: str):
    return {"name": name}
```

```py
from fastapi import FastAPI, status

app = FastAPI()


@app.post("/items/", status_code=status.HTTP_201_CREATED)
async def create_item(name: str):
    return {"name": name}
```

## Form Data

+ フィールドからデータを受け取るときは、フォームを使う

```terminal
// 事前にインストールが必要
pip install python-multipart
```

## Import File

+ ファイルをアップロードすることができる

## Request Forms and Files

+ FormとFileアップロードは組み合わせることもできる

## Handling Errors

### Use HTTPExceptionを使うケース

```py
# HTTPExceptionをインポート
from fastapi import FastAPI, HTTPException

app = FastAPI()

items = {"foo": "The Foo Wrestlers"}


@app.get("/items/{item_id}")
async def read_item(item_id: str):
    if item_id not in items:
        # 例外を発生させる
        # 例: IDが見つからない場合
        raise HTTPException(status_code=404, detail="Item not found")
    return {"item": items[item_id]}
```

+ custom headersを追加することもできる
+ custom exception handlersも追加できる
+ default exception handlerを上書きできる

## Path Operation Configuration

+ 注: path operation decoratorに直接渡される

### Tags

+ tagsで、パスの区分をドキュメントに反映させる

### Summary and description

+ APIの要約と説明を追加できる

### Description from docstring

+ MarkDown形式で、docstringを利用した記述ができる

### Response description

+ レスポンスの説明を追加できる

## JSON Compatible Encoder

+ JSONの互換性のある形式に変換するときは、jsonable_encoder()を使う
  + 例: DBに保存する

## Body - Updates

### PUTメソッドを使って置き換える

### PATCHメソッドで部分的に更新する

## Dependencies - First Steps

+ Dependency Injection
  + 依存関係にあるものを切り出して再利用できる

## Classes as Dependencies

+ 依存関係をクラスとして表現することもできる

## Sub-dependencies

+ 依存関係を入れ子にすることもできる

## Dependencies in path operation decorators

+ 依存関係にあるものに、戻り値が含まれていない場合にも対処できる

## Dependencies with yield

+ 操作の実行後に必要な手順を含む依存関係についてもサポート

```py
# DBを開く&確実に閉じる
async def get_db():
    db = DBSession()
    try:
        yield db
    finally:
        db.close()
```

## Security Intro

+ 複雑で難しいトピック
+ 多くのフレームワークでは、セキュリティに関するコードが50%以上
+ FastAPIでは、全ての専門的なセキュリティの知識を学ばずに、簡単に・素早く・標準的な方法となるツールを提供している

#### OAuth2

+ 認証と認可を扱う
+ かなり広範な仕様で、複雑なユースケースをカバーしている
+ サードパーティを使って認証する方法も含まれている
+ XXXでログインをしているシステムに該当

#### OpenID Connect

+ OAuth2をもとにした別の仕様書
+ OAuth2を拡張して、曖昧なものを指定し、相互運用性を高めようとしている
+ ex: Google login

#### OpenAPI

+ 旧Swagger
+ APIを構築するためのオープンな仕様

+ FastAPIはOpenAPIをベースにしており、複数の自動対話型ドキュメントインターフェイスやコード生成などが可能

+ OpenAPIには、複数のセキュリティ「スキーム」を定義する方法がある

#### FastAPI utilities

+ fastapi.securityで、セキュリティスキームに関する機能が含まれている

## Security - First Steps

+ ユーザ名とパスワード形式の場合は、数行のコードでセキュリティの基本部分を実装できる

## Get Current User

+ Userモデルを定義
+ 現在のユーザの情報を取得するコードを追加
+ あとは、ダミーの部分を実際のものに置き換えるだけ

## Simple OAuth2 with Password and Bearer

+ 基本的な実装方法を解説
+ まだ、セキュアではない

## OAuth2 with Password (and hashing), Bearer with JWT tokens

+ JWTトークンとセキュアなパスワードのハッシュ化を利用して、セキュアなappに

## 疑問点

+ CRUDの書き方は?

+ エンティティの依存関係は、どのように実現する?

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
+ Cookie Parametersの使いどころとは?

+ yieldの使い所は?

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
