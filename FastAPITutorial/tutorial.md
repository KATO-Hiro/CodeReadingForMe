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

# 型定義に関するモジュールをインポート
from typing import Optional

# FastAPIをインポート
# Starletteを直接継承しているため、その機能を全て利用できる
from fastapi import FastAPI

# インスタンスを作成
# 名称は、appでなくてもよい
app = FastAPI()


# デコレータで、HTTPメソッドとルーティング(パス、エンドポイント)を指定
# 関数を定義して、値を返す
@app.get("/")
async def root():
    return {"message": "Hello World"}

# パスパラメータやクエリパラメータを使うこともできる
# ex: /item/5?q=hoge
# 5がパスパラメータ、qがクエリパラメータ
@app.get("/items/{item_id}")
def read_item(item_id: int, q: Optional[str] = None):
    return {"item_id": item_id, "q": q}
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

+ チュートリアルに含まれている内容
  + headers, cookies, form fields and filesのような異なる場所にあるパラメータの宣言
  + バリデーションを行う方法
  + Dependency Injection systemの使い方
  + セキュリティと認証: OAuth2 with JWT tokens and HTTP Basic auth
  + Pydanticを用いた深い階層のJSONモデル
  + Starletteによるさまざまな機能のサポート
    + WebSockets
    + GraphQL
    + requestsとpytestによる簡単なテスト
    + CORS
    + Cookie Sessions など

+ 依存関係にあるソフトウェア(任意)
  + Pydanticを使う場合
    + ujson - for faster JSON "parsing".
  + Starletteを使う場合
    + requests - Required if you want to use the TestClient
  + pip install fastapi[all]でインストールできる

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
@app.options()
@app.head()
@app.patch()
@app.trace()
```

```py
# Step 4: define the path operation function
from fastapi import FastAPI

app = FastAPI()


# Pythonの関数として記述する
# async: 非同期処理。使わない場合は、外す。
# Q: メソッド名のルールは、どうなっている?
# A: 公式チュートリアルでは、動詞_名詞の形式になっている。
@app.get("/")
async def root():
    # Step 5: return the content
    # dict, list、単独の値(str, intなど)、Pydanticモデルを返すことができる
    # 自動的にJSONに変換される
    return {"message": "Hello World"}

# 開発サーバを起動・リロードする
```

#### Recap

+ Import FastAPI
+ appインスタンスを作成
+ path operation decoratorを書く (@app.get("/"))
+ path operation functionを書く (def root(): ...)
+ 開発サーバを起動・リロード

## Path Parameters

```py
from fastapi import FastAPI

app = FastAPI()

# パスのパラメータや変数は、Pythonの形式と同じシンタックス
# パスパラメータのitemd_idは、関数内でitem_id変数として扱われる
# "hoges/{hoge_id}"のように、/複数形/単数形_idの形式になっている
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


# パスは、上から順番に評価される
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
# Q: 継承の順番は影響がある?
class ModelName(str, Enum):
    # 属性名 = 値
    alexnet = "alexnet"
    resnet = "resnet"
    lenet = "lenet"


app = FastAPI()


# 引数の型に自作のクラスを取ることもできる
# 型の部分に、自作のクラスを指定するだけ
@app.get("/model/{model_name}")
async def get_model(model_name: ModelName):
    # Q: 条件が増えてきたときは、どうする?
    # A: デザインパターンのFactory methodを利用するか?
    # ClassName.attrの形式で利用できる
    if model_name == ModelName.alexnet:
        # 戻り値にmodel_nameを取ることもできる
        return {"model_name": model_name, "message": "Deep Learning FTW!"}

    # model_name.value == 'hoge'の形式も利用できる
    if model_name.value == "lenet":
        return {"model_name": model_name, "message": "LeCNN all the images"}

    # 戻り値に、パスの値を返すこともできる。入れ子になった値も可能。
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

+ Pythonの型宣言による効果により、一度だけ定義すれば以下の効果が得られる(他のフレームワークと比べたときのメリット)
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

# http://127.0.0.1:8000/items/?skip=0&limit=10
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
# FastAPIでは、q = Noneでoptionalと判定している
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

# hoge = default_valueのデフォルト値を外すだけ
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


# クエリパラメータにデフォルト値や任意の値を指定することもできる = 組み合わせることができる
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

  + POSTメソッドを使うのが一般的(PUT, DELETE or PATCH)

```py
# 1. BaseModelをpydanticからインポート
# 2. データモデルを作成
from typing import Optional

from fastapi import FastAPI
from pydantic import BaseModel # TODO: コードの中身を確認する


# BaseModelを継承
# 属性: 型の順番で指定
# デフォルト値を設定することもできる
# ex: attr: type = default_value
# 任意の値の場合は、Noneを指定する(JSONオブジェクトに、descriptionやtaxがないケースもある)
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
        # モデルオブジェクトに直接アクセスできる
        price_with_tax = item.price + item.tax
        # 動的にdictに属性をすることもできる
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
    # Q: なぜ、**をつけている?
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

### Without Pydantic

- Pydanticの代わりに、Body parametersを使うこともできる

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
  + description: クエリパラメータに関する説明を追加
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

```py
from typing import Optional

from fastapi import Body, FastAPI

from pydantic import BaseModel, Field # Fieldをpydanticから直接インポート


app = FastAPI()


class Item(BaseModel):
    name: str
    description: Optional[str] = Field(
        None, title="The description of the item", max_length=300
    ) # 個別に検証できる。QueryやPath, Bodyと同じように書くことができる。
    price: float = Field(..., gt=0, description="The price must be greater than zero")
    tax: Optional[float] = None


@app.put("/items/{item_id}")
async def update_item(item_id: int, item: Item = Body(..., embed=True)):
    results = {"item_id": item_id, "item": item}
    return results

```

## Body - Nested Models

### List fields with type parameter

```py
from typing import List, Optional # Listをインポート

from fastapi import FastAPI
from pydantic import BaseModel

app = FastAPI()


class Item(BaseModel):
    name: str
    description: Optional[str] = None
    price: float
    tax: Optional[float] = None
    tags: List[str] = [] # List[type]と記述する。このケースでは、stringのリストになる


@app.put("/items/{item_id}")
async def update_item(item_id: int, item: Item):
    results = {"item_id": item_id, "item": item}
    return results

```

### Nested Models

```py
from typing import Optional, Set

from fastapi import FastAPI
from pydantic import BaseModel

app = FastAPI()


# サブモデルの定義
class Image(BaseModel):
    url: str
    name: str


class Item(BaseModel):
    name: str
    description: Optional[str] = None
    price: float
    tax: Optional[float] = None
    tags: Set[str] = []
    image: Optional[Image] = None # サブモデルを属性の型として使う


@app.put("/items/{item_id}")
async def update_item(item_id: int, item: Item):
    results = {"item_id": item_id, "item": item}
    return results
```

### Attributes with lists of submodels

+ サブモデルのlistやsetを定義することもできる

```py
from typing import List, Optional, Set

from fastapi import FastAPI
from pydantic import BaseModel, HttpUrl

app = FastAPI()


class Image(BaseModel):
    url: HttpUrl
    name: str


class Item(BaseModel):
    name: str
    description: Optional[str] = None
    price: float
    tax: Optional[float] = None
    tags: Set[str] = set()
    images: Optional[List[Image]] = None # List[]やSet[]で囲むだけ


@app.put("/items/{item_id}")
async def update_item(item_id: int, item: Item):
    results = {"item_id": item_id, "item": item}
    return results
```

+ Pydanticにより、ネストしたモデルを利用することができる。
  + 任意の階層を定義できる
+ List, Setが利用できる
+ 自作のクラスを属性に指定することもできる

## Schema Extra - Example

+ JSONスキーマに追加の情報を定義することもできる
  + 一般的な例として、サンプル情報
  + クラスのFieldやレスポンスBodyにも追加することができる

### Pydantic schema_extra

+ スキーマのサンプルを宣言できる
  + Configとschema_extraを使う

```py
from typing import Optional

from fastapi import FastAPI
from pydantic import BaseModel

app = FastAPI()


class Item(BaseModel):
    name: str
    description: Optional[str] = None
    price: float
    tax: Optional[float] = None


    # Itemクラスの内部で、Configクラスを記述
    # schema_extraの内部に、サンプルを記述 (key, valueの形式)
    # JSON Schemaにそのまま追加される
    class Config:
        schema_extra = {
            "example": {
                "name": "Foo",
                "description": "A very nice Item",
                "price": 35.4,
                "tax": 3.2,
            }
        }

@app.put("/items/{item_id}")
async def update_item(item_id: int, item: Item):
    results = {"item_id": item_id, "item": item}
    return results
```

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

```py
from typing import Optional

from fastapi import FastAPI, Header # インポート

app = FastAPI()


@app.get("/items/")
async def read_items(user_agent: Optional[str] = Header(None)):
    return {"User-Agent": user_agent}

```

+ Q: レスポンスBodyと含めて、値を返していいのか?

## Response Model

+ @app.xxxで、指定
+ `response_model`を使う
+ モデルを別途作成し、レスポンスに含めない情報を指定できる
  + 例: パスワード

### Add an output model

```py
from typing import Optional

from fastapi import FastAPI
from pydantic import BaseModel, EmailStr

app = FastAPI()


# 危険: ユーザのプレーンパスワードをresponseに絶対入れてはならない
# データを受け取るときのモデル
class UserIn(BaseModel):
    username: str
    password: str
    email: EmailStr
    full_name: Optional[str] = None


# データを返すモデル
class UserOut(BaseModel):
    username: str
    email: EmailStr
    full_name: Optional[str] = None


# response_modelを指定して、パスワードが含まれていないUserOutモデルに切り替え
@app.post("/user/", response_model=UserOut)
async def create_user(user: UserIn):
    return user

```

## Extra Models

+ 継承を使って、重複をなくす。ただし、一つの状態しかないエンティティをあえて分割する必要はない
+ 例:
  + Userモデル(基本モデル)
  + パスワードあり
  + パスワードなし
  + ハッシュ化したパスワード(DB保存用)

### Reduce duplication

+ コードの重複をなくすための工夫

```py
from typing import Optional

from fastapi import FastAPI
from pydantic import BaseModel, EmailStr

app = FastAPI()



# 基本となるクラス
class UserBase(BaseModel):
    username: str
    email: EmailStr
    full_name: Optional[str] = None


# UserBaseを継承したクラスで、差分を記述する
class UserIn(UserBase):
    password: str


class UserOut(UserBase):
    pass


class UserInDB(UserBase):
    hashed_password: str


# パスワードのハッシュ化(ダミー)
def fake_password_hasher(raw_password: str):
    return "supersecret" + raw_password


# ダミーのDB操作
def fake_save_user(user_in: UserIn):
    hashed_password = fake_password_hasher(user_in.password)
    user_in_db = UserInDB(**user_in.dict(), hashed_password=hashed_password)
    print("User saved! ..not really")
    return user_in_db


@app.post("/user/", response_model=UserOut)
async def create_user(user_in: UserIn):
    user_saved = fake_save_user(user_in)
    return user_saved

```

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

```py
from typing import Optional, Set

from fastapi import FastAPI
from pydantic import BaseModel

app = FastAPI()


class Item(BaseModel):
    name: str
    description: Optional[str] = None
    price: float
    tax: Optional[float] = None
    tags: Set[str] = []


# tags=["hoge"]を指定すると、OpenAPI schemaでドキュメントに反映される
@app.post("/items/", response_model=Item, tags=["items"])
async def create_item(item: Item):
    return item


@app.get("/items/", tags=["items"])
async def read_items():
    return [{"name": "Foo", "price": 42}]


@app.get("/users/", tags=["users"])
async def read_users():
    return [{"username": "johndoe"}]
```

### Summary and description

+ APIの要約と説明を追加できる

```py
from typing import Optional, Set

from fastapi import FastAPI
from pydantic import BaseModel

app = FastAPI()


class Item(BaseModel):
    name: str
    description: Optional[str] = None
    price: float
    tax: Optional[float] = None
    tags: Set[str] = []


# summary、descriptionに記述
@app.post(
    "/items/",
    response_model=Item,
    summary="Create an item",
    description="Create an item with all the information, name, description, price, tax and a set of unique tags",

)
async def create_item(item: Item):
    return item
```

### Description from docstring

+ MarkDown形式で、docstringを利用した記述ができる

```py
from typing import Optional, Set

from fastapi import FastAPI
from pydantic import BaseModel

app = FastAPI()


class Item(BaseModel):
    name: str
    description: Optional[str] = None
    price: float
    tax: Optional[float] = None
    tags: Set[str] = []


# ドキュメントの記述例
@app.post("/items/", response_model=Item, summary="Create an item")
async def create_item(item: Item):
    """

    Create an item with all the information:

    - **name**: each item must have a name
    - **description**: a long description
    - **price**: required
    - **tax**: if the item doesn't have tax, you can omit this
    - **tags**: a set of unique tag strings for this item
    """

    return item
```

### Response description

+ レスポンスの説明を追加できる

```py
from typing import Optional, Set

from fastapi import FastAPI
from pydantic import BaseModel

app = FastAPI()


class Item(BaseModel):
    name: str
    description: Optional[str] = None
    price: float
    tax: Optional[float] = None
    tags: Set[str] = []


# response_descripiton="hoge"で追記
@app.post(
    "/items/",
    response_model=Item,
    summary="Create an item",
    response_description="The created item",

)
async def create_item(item: Item):
    """
    Create an item with all the information:

    - **name**: each item must have a name
    - **description**: a long description
    - **price**: required
    - **tax**: if the item doesn't have tax, you can omit this
    - **tags**: a set of unique tag strings for this item
    """
    return item

```

### Deprecate a path operation

+ deprecatedにする場合は、コードを消すのではなく`deprecated`を使う

```py
from fastapi import FastAPI

app = FastAPI()


@app.get("/items/", tags=["items"])
async def read_items():
    return [{"name": "Foo", "price": 42}]


@app.get("/users/", tags=["users"])
async def read_users():
    return [{"username": "johndoe"}]


# OpenAPIで非表示となり、利用できなくなる
@app.get("/elements/", tags=["items"], deprecated=True)
async def read_elements():
    return [{"item_id": "Foo"}]
```

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
+ OAuth2を利用する

```py
from fastapi import Depends, FastAPI
from fastapi.security import OAuth2PasswordBearer # インポート

app = FastAPI()

# 追加
# callable
# ex: oauth2_scheme(some, parameters)なので、Depends()とともに使える
oauth2_scheme = OAuth2PasswordBearer(tokenUrl="token")

# Depends()を追記
@app.get("/items/")
async def read_items(token: str = Depends(oauth2_scheme)):
    return {"token": token}
```

+ 上記のコードを実行すると、OpenAPIのUIで認証が要求されるようになる

### The password flow

+ `password flow`はOAuth2における`flows`の一つ
  + セキュリティと認証を扱う
+ OAuth2は、バックエンドやAPIがユーザを認証するサーバから独立できるように設計されていた
+ しかし、FastAPI appでは、APIと認証を処理する

+ 認証の手順の概要
  + フロントエンド側のユーザが`username`と`password`を入力して、`Enter`を押す
  + フロント側が、上記の情報を特定のAPIに送信する (tokenUrl="token")
  + APIが`username`と`password`を確認し、`token`を返す
    + tokenは、ただの文字列で、後からこのユーザを識別するために使う
    + 通常は、tokenに有効期限が設定されている
      + ユーザは、しばらくするとログインをし直すことになる
      + もし、トークンが盗まれたとしても、リスクは少ない。永久的なキーではないので、永遠には動作しない。
  + フロント側は、tokenを一時的にどこかに保存する
  + フロント側のユーザは、フロントのappの別のセクションに移動する
  + フロントは、APIから別のデータを取得する必要がある
    + 特定のエンドポイントに関しては、認証が必要ある
    + APIに認証を行うためには、tokenとBearerの値を追加したAuthorizationヘッダーを送信する
    + tokenにfoobarが含まれていたら、`Authorization`ヘッダーは、Bearer foobarとなる

### FastAPI's OAuth2PasswordBearer

+ セキュリティの機能を実装するために、抽象度の異なるツールが提供されている
+ OAuth2とPassword flow, Bearer tokenを使ったサンプル
  + OAuthPasswordBearerクラスを使う

### What it does

+ `Authorization`ヘッダーに関して、Bearerとtokenが`str`として返ってきている
+ なければ、ステータスコード401(UNAUTHORIZED)がそのまま返ってくる

## Get Current User

+ 前章では、`token`を`str`として提供するセキュリティシステムについて示した

### Create a user model

```py
from typing import Optional

from fastapi import Depends, FastAPI
from fastapi.security import OAuth2PasswordBearer

from pydantic import BaseModel


app = FastAPI()

oauth2_scheme = OAuth2PasswordBearer(tokenUrl="token")



# 追加
class User(BaseModel):
    username: str
    email: Optional[str] = None
    full_name: Optional[str] = None
    disabled: Optional[bool] = None


def fake_decode_token(token):
    return User(
        username=token + "fakedecoded", email="john@example.com", full_name="John Doe"
    )


async def get_current_user(token: str = Depends(oauth2_scheme)):
    user = fake_decode_token(token)
    return user


@app.get("/users/me")
async def read_users_me(current_user: User = Depends(get_current_user)):
    return current_user
```

### Create a get_current_user dependency

+ 依存関係には、サブの依存関係を持つことができる

```py
from typing import Optional

from fastapi import Depends, FastAPI
from fastapi.security import OAuth2PasswordBearer
from pydantic import BaseModel

app = FastAPI()

oauth2_scheme = OAuth2PasswordBearer(tokenUrl="token")


class User(BaseModel):
    username: str
    email: Optional[str] = None
    full_name: Optional[str] = None
    disabled: Optional[bool] = None


def fake_decode_token(token):
    return User(
        username=token + "fakedecoded", email="john@example.com", full_name="John Doe"
    )


# tokenをstr型として受け取れるように
async def get_current_user(token: str = Depends(oauth2_scheme)):
    user = fake_decode_token(token)
    return user


@app.get("/users/me")
async def read_users_me(current_user: User = Depends(get_current_user)):
    return current_user

```

### Get the user

```py
from typing import Optional

from fastapi import Depends, FastAPI
from fastapi.security import OAuth2PasswordBearer
from pydantic import BaseModel

app = FastAPI()

oauth2_scheme = OAuth2PasswordBearer(tokenUrl="token")


class User(BaseModel):
    username: str
    email: Optional[str] = None
    full_name: Optional[str] = None
    disabled: Optional[bool] = None


# ダミーのユーティリティ関数で、現在のユーザを作成する
def fake_decode_token(token):
    return User(

        username=token + "fakedecoded", email="john@example.com", full_name="John Doe"

    )


async def get_current_user(token: str = Depends(oauth2_scheme)):
    user = fake_decode_token(token)

    return user


@app.get("/users/me")
async def read_users_me(current_user: User = Depends(get_current_user)):
    return current_user

```

### Inject the current user

```py
from typing import Optional

from fastapi import Depends, FastAPI
from fastapi.security import OAuth2PasswordBearer
from pydantic import BaseModel

app = FastAPI()

oauth2_scheme = OAuth2PasswordBearer(tokenUrl="token")


class User(BaseModel):
    username: str
    email: Optional[str] = None
    full_name: Optional[str] = None
    disabled: Optional[bool] = None


def fake_decode_token(token):
    return User(
        username=token + "fakedecoded", email="john@example.com", full_name="John Doe"
    )


async def get_current_user(token: str = Depends(oauth2_scheme)):
    user = fake_decode_token(token)
    return user


# 現在のユーザを追加
@app.get("/users/me")
async def read_users_me(current_user: User = Depends(get_current_user)):
    return current_user
```

### Other models

+ 現在のユーザに対して、セキュリティのメカニズムで直接扱えるようになった
+ どんなモデルやデータに対しても、セキュリティの要求ができる

### Code size

+ 複雑に見えるが、セキュリティと依存性の注入に関する記述は、一度かつ一箇所で済む
+ パスの操作に関する記述は、実質3行で済む

```py
from typing import Optional

from fastapi import Depends, FastAPI
from fastapi.security import OAuth2PasswordBearer
from pydantic import BaseModel

app = FastAPI()

oauth2_scheme = OAuth2PasswordBearer(tokenUrl="token")


class User(BaseModel):
    username: str
    email: Optional[str] = None
    full_name: Optional[str] = None
    disabled: Optional[bool] = None


def fake_decode_token(token):
    return User(
        username=token + "fakedecoded", email="john@example.com", full_name="John Doe"
    )


async def get_current_user(token: str = Depends(oauth2_scheme)):
    user = fake_decode_token(token)
    return user


# current_user: User = Depends(get_current_user)
@app.get("/users/me")
async def read_users_me(current_user: User = Depends(get_current_user)):

    return current_user
```

+ Userモデルを定義
+ 現在のユーザの情報を取得するコードを追加
+ あとは、ダミーの部分を実際のものに置き換えるだけ

## Simple OAuth2 with Password and Bearer

+ 基本的な実装方法を解説
+ まだ、セキュアではない

### Get the username and password

+ `scope`を送信する必要がある
  + `scope`は文字列

+ `OAuth2PasswordRequestForm`はクラス
  + フォームボディを宣言する
  + username, password, scope(任意), grant_type(任意), client_id(任意)、client_secret(任意)

### Code to get the username and password

```py
from typing import Optional

from fastapi import Depends, FastAPI, HTTPException, status # 追加
from fastapi.security import OAuth2PasswordBearer, OAuth2PasswordRequestForm # 追加

from pydantic import BaseModel

fake_users_db = {
    "johndoe": {
        "username": "johndoe",
        "full_name": "John Doe",
        "email": "johndoe@example.com",
        "hashed_password": "fakehashedsecret",
        "disabled": False,
    },
    "alice": {
        "username": "alice",
        "full_name": "Alice Wonderson",
        "email": "alice@example.com",
        "hashed_password": "fakehashedsecret2",
        "disabled": True,
    },
}

app = FastAPI()


def fake_hash_password(password: str):
    return "fakehashed" + password


oauth2_scheme = OAuth2PasswordBearer(tokenUrl="token")


class User(BaseModel):
    username: str
    email: Optional[str] = None
    full_name: Optional[str] = None
    disabled: Optional[bool] = None


class UserInDB(User):
    hashed_password: str


def get_user(db, username: str):
    if username in db:
        user_dict = db[username]
        return UserInDB(**user_dict)


def fake_decode_token(token):
    # This doesn't provide any security at all
    # Check the next version
    user = get_user(fake_users_db, token)
    return user


async def get_current_user(token: str = Depends(oauth2_scheme)):
    user = fake_decode_token(token)
    if not user:
        raise HTTPException(
            status_code=status.HTTP_401_UNAUTHORIZED,
            detail="Invalid authentication credentials",
            headers={"WWW-Authenticate": "Bearer"},
        )
    return user


async def get_current_active_user(current_user: User = Depends(get_current_user)):
    if current_user.disabled:
        raise HTTPException(status_code=400, detail="Inactive user")
    return current_user


# 追加
# form_dataにOAuth2PasswordRequestForm型、Depends()を指定
@app.post("/token")
async def login(form_data: OAuth2PasswordRequestForm = Depends()):

    user_dict = fake_users_db.get(form_data.username)
    # ユーザが存在しない場合の処理
    if not user_dict:
        raise HTTPException(status_code=400, detail="Incorrect username or password")
    # key-valueの一つずつ与えた場合と、以下のコードは等価
    user = UserInDB(**user_dict)
    # ハッシュ化されたパスワードのチェック
    hashed_password = fake_hash_password(form_data.password)
    if not hashed_password == user.hashed_password:
        raise HTTPException(status_code=400, detail="Incorrect username or password")

    # tokenを返す
    # access_tokenとtoken_typeが必須
    return {"access_token": user.username, "token_type": "bearer"}


@app.get("/users/me")
async def read_users_me(current_user: User = Depends(get_current_active_user)):
    return current_user

```

### Update the dependencies

+ ユーザが存在して、かつ認証されており、アクティブなときだけエンドポイントを有効化

```py
from typing import Optional

from fastapi import Depends, FastAPI, HTTPException, status
from fastapi.security import OAuth2PasswordBearer, OAuth2PasswordRequestForm
from pydantic import BaseModel

fake_users_db = {
    "johndoe": {
        "username": "johndoe",
        "full_name": "John Doe",
        "email": "johndoe@example.com",
        "hashed_password": "fakehashedsecret",
        "disabled": False,
    },
    "alice": {
        "username": "alice",
        "full_name": "Alice Wonderson",
        "email": "alice@example.com",
        "hashed_password": "fakehashedsecret2",
        "disabled": True,
    },
}

app = FastAPI()


def fake_hash_password(password: str):
    return "fakehashed" + password


oauth2_scheme = OAuth2PasswordBearer(tokenUrl="token")


class User(BaseModel):
    username: str
    email: Optional[str] = None
    full_name: Optional[str] = None
    disabled: Optional[bool] = None


class UserInDB(User):
    hashed_password: str


def get_user(db, username: str):
    if username in db:
        user_dict = db[username]
        return UserInDB(**user_dict)


def fake_decode_token(token):
    # This doesn't provide any security at all
    # Check the next version
    user = get_user(fake_users_db, token)
    return user


# 追記
async def get_current_user(token: str = Depends(oauth2_scheme)):
    user = fake_decode_token(token)

    if not user:
        raise HTTPException(
            status_code=status.HTTP_401_UNAUTHORIZED,
            detail="Invalid authentication credentials",
            headers={"WWW-Authenticate": "Bearer"},
        )

    return user


# 追記
async def get_current_active_user(current_user: User = Depends(get_current_user)):

    if current_user.disabled:
        raise HTTPException(status_code=400, detail="Inactive user")

    return current_user


@app.post("/token")
async def login(form_data: OAuth2PasswordRequestForm = Depends()):
    user_dict = fake_users_db.get(form_data.username)
    if not user_dict:
        raise HTTPException(status_code=400, detail="Incorrect username or password")
    user = UserInDB(**user_dict)
    hashed_password = fake_hash_password(form_data.password)
    if not hashed_password == user.hashed_password:
        raise HTTPException(status_code=400, detail="Incorrect username or password")

    return {"access_token": user.username, "token_type": "bearer"}


# 変更
@app.get("/users/me")
async def read_users_me(current_user: User = Depends(get_current_active_user)):

    return current_user

```

### See it in action

#### Authenticate

+ OpenAPIのUIで"Authorize" buttonをクリック
+ usernameとpasswordを入力

#### Get your own user data

+ users/meを実行すると、ユーザ情報が表示される
+ ログアウトしていると未認証となり、401エラーが返ってくる

#### Inactive user

+ "inactive user"を受け取るはず

## OAuth2 with Password (and hashing), Bearer with JWT tokens

+ JWTトークンとセキュアなパスワードのハッシュ化を利用して、セキュアなappに

+ Q: JWTトークンとは?
  + A: JSON Web Tokens
    + JSONオブジェクトで、スペースなしの文字列。
    + 暗号化されてないので、誰でも元の情報を復元できる
    + 署名されているため、自分が発行したかどうかを確認できる
+ Q: 普通のトークンとは何が違う?

+ この節で紹介されているコードは、実際のappで使用することができる。

### Install python-jose

+ JWTトークンの生成と検証をPythonで行うライブラリをインストール
  + 以前のチュートリアルでは、PyJWTを使っていた
  + PyJWTの全ての機能＋追加機能

```terminal
$ pip install python-jose[cryptography]
```

### Password hashing

+ ハッシュ化は、パスワードなどを意味のない文字列に変換すること
+ 同じパスワードを渡しても、全く同じ結果となる
+ ただし、変換された結果を元のパスワードに戻すことはできない

#### Why use password hashing

+ DBからデータが盗まれても、パスワードが盗まれないようにする
  + 悪意のある人物は、別のシステムに対してパスワードを適用しようとする（多くのユーザがパスワードを使いまわしているため）

### Install passlib

+ パスワードに関するハッシュを扱うライブラリをインストール

```terminal
$ pip install passlib[bcrypt]
```

### Hash and verify the passwords

+ 関連するライブラリをインストール
  + JWTトークンの生成と検証を`python-jose`で行う
  + パスワードのハッシュ化と検証を`passlib`で行う
+ ユーザのパスワードに対してハッシュを生成・検証
+ 現在のユーザに対して、JWTトークンの復号・検証を行う
+ 認証されたら、JWTアクセストークンの有効期限を生成
+ OpenAPIで、ユーザ名とパスワードを入力し、/users/meを呼び出す
  + developer toolsで確認できる
    + 送信されるデータには、トークンだけが含まれている
    + パスワードは、ユーザ認証を行いアクセストークンを取得する最初のリクエストでのみ送信され、それ以降は送信されない

```py
from datetime import datetime, timedelta
from typing import Optional

from fastapi import Depends, FastAPI, HTTPException, status
from fastapi.security import OAuth2PasswordBearer, OAuth2PasswordRequestForm
from jose import JWTError, jwt # インポート
from passlib.context import CryptContext # インポート

from pydantic import BaseModel

# 追記
# to get a string like this run:
# openssl rand -hex 32
# JWT tokenを利用するため、ランダムなシークレットキーを作成する
# Q: 公開レポジトリに、そのまま記載するのはマズいのでは?
# ハッシュ化のアルゴリズムとして、"HS256"を指定
SECRET_KEY = "09d25e094faa6ca2556c818166b7a9563b93f7099f6f0f4caa6cf63b88e8d3e7"
ALGORITHM = "HS256"
ACCESS_TOKEN_EXPIRE_MINUTES = 30


fake_users_db = {
    "johndoe": {
        "username": "johndoe",
        "full_name": "John Doe",
        "email": "johndoe@example.com",
        "hashed_password": "$2b$12$EixZaYVK1fsbw1ZfbX3OXePaWxn96p36WQoeG6Lruj3vjPGga31lW",
        "disabled": False,
    }
}


# 追記
class Token(BaseModel):
    access_token: str
    token_type: str


class TokenData(BaseModel):
    username: Optional[str] = None


class User(BaseModel):
    username: str
    email: Optional[str] = None
    full_name: Optional[str] = None
    disabled: Optional[bool] = None


class UserInDB(User):
    hashed_password: str


# パスワードのハッシュ化
# ハッシュ化するためのアルゴリズムに、Bcryptを指定している
pwd_context = CryptContext(schemes=["bcrypt"], deprecated="auto")

oauth2_scheme = OAuth2PasswordBearer(tokenUrl="token")

app = FastAPI()


# パスワードの検証
def verify_password(plain_password, hashed_password):
    return pwd_context.verify(plain_password, hashed_password)


# パスワードのハッシュ化
def get_password_hash(password):
    return pwd_context.hash(password)


def get_user(db, username: str):
    if username in db:
        user_dict = db[username]
        return UserInDB(**user_dict)


# 追記
def authenticate_user(fake_db, username: str, password: str):
    user = get_user(fake_db, username)

    if not user:
        return False
    if not verify_password(password, user.hashed_password):
        return False

    return user


# 追記
def create_access_token(data: dict, expires_delta: Optional[timedelta] = None):
    to_encode = data.copy() # なぜ、copy()している?
    if expires_delta:
        expire = datetime.utcnow() + expires_delta
    else:
        expire = datetime.utcnow() + timedelta(minutes=15)
    to_encode.update({"exp": expire})
    encoded_jwt = jwt.encode(to_encode, SECRET_KEY, algorithm=ALGORITHM)
    return encoded_jwt


async def get_current_user(token: str = Depends(oauth2_scheme)):
    # 受け取ったトークンを復号して、不正なら直ちにHTTPエラーを返す
    credentials_exception = HTTPException(
        status_code=status.HTTP_401_UNAUTHORIZED,
        detail="Could not validate credentials",
        headers={"WWW-Authenticate": "Bearer"},
    )
    try:
        payload = jwt.decode(token, SECRET_KEY, algorithms=[ALGORITHM])
        # JWTの仕様では、トークンのサブジェクトを含むsubキーがあることが前提とされている
        # ユーザを識別するために使っている
        # JWTトークンをユーザやボットに与えると、アカウントを持っていなくても操作を実行できてしまう可能性がある
        # IDの衝突を避けるために、subキーの前に何らかの値を付与
        # また、アプリ全体を通してユニークな識別子であることと、文字列である必要がある
        username: str = payload.get("sub")
        if username is None:
            raise credentials_exception
        token_data = TokenData(username=username)
    except JWTError:
        raise credentials_exception
    user = get_user(fake_users_db, username=token_data.username)
    if user is None:
        raise credentials_exception
    return user


async def get_current_active_user(current_user: User = Depends(get_current_user)):
    if current_user.disabled:
        raise HTTPException(status_code=400, detail="Inactive user")
    return current_user


# 追記
@app.post("/token", response_model=Token)
async def login_for_access_token(form_data: OAuth2PasswordRequestForm = Depends()):
    user = authenticate_user(fake_users_db, form_data.username, form_data.password)
    if not user:
        raise HTTPException(
            status_code=status.HTTP_401_UNAUTHORIZED,
            detail="Incorrect username or password",
            headers={"WWW-Authenticate": "Bearer"},
        )
    access_token_expires = timedelta(minutes=ACCESS_TOKEN_EXPIRE_MINUTES)
    access_token = create_access_token(
        data={"sub": user.username}, expires_delta=access_token_expires
    )
    return {"access_token": access_token, "token_type": "bearer"}


@app.get("/users/me/", response_model=User)
async def read_users_me(current_user: User = Depends(get_current_active_user)):
    return current_user


@app.get("/users/me/items/")
async def read_own_items(current_user: User = Depends(get_current_active_user)):
    return [{"item_id": "Foo", "owner": current_user.username}]

```

### Advanced usage with scopes

+ OAuth2には、「スコープ」という概念がある
+ JWTトークンに特定のパーミッションを追加でき、ユーザやサードパーティにそれを与えてAPIと対話できる
+ 詳細は、上級者向けのユーザガイドで

## Middleware

+ ミドルウェア: 特定の操作によって処理される前の全てのリクエストに対して動作する関数。全てのレスポンスを処理する前に返す。

+ Q: どういうときに使うのか?よく分かっていない
  + A: 例えば、次のセクションで紹介されているCORS

## CORS (Cross-Origin Resource Sharing)

+ ブラウザ上で動作するフロントエンドがバックエンドと通信するJavaScriptコードを持っていて、バックエンドがフロントエンドとは異なる「オリジン」にある場合の状況を指す

### Origin

+ プロトコル(http, https)、ドメイン(xxx.com, localhost,localhost.xxx.com)、ポート(80, 443, 8080)で構成される

+ 以下は全て違うオリジンと見なされる
  http://localhost
  https://localhost
  http://localhost:8080

+ 全てlocalhostであっても、プロトコルやポートが異なれば、違うオリジン

### 手順

+ 前提
  + フロントエンド: http://localhost:8080
  + バックエンド: http://localhost (特定のポートを指定しない場合、ブラウザはデフォルトのポート80を仮定する)

+ 手順
  + ブラウザは、バックエンドにHTTP OPTIONSリクエストを送信する
  + バックエンドは、異なるオリジンからの通信を承認する適切なヘッダーを送信する
  + ブラウザはフロントエンドのJavaScriptにバックエンドへのリクエストを送信させる

+ バックエンドで、"allowed origins"のリストを用意する必要がある

### Wildcards

+ 上記のリストを定義するときには、ワイルドカードが利用できる

### Use CORSMiddleware

```py
from fastapi import FastAPI
# 1. CORSMiddlewareをインポート
from fastapi.middleware.cors import CORSMiddleware

app = FastAPI()

# 2. 許可するoriginを文字列のリストで作成
origins = [
    "http://localhost.tiangolo.com",
    "https://localhost.tiangolo.com",
    "http://localhost",
    "http://localhost:8080",
]

# 3. middlewareとして追加
# いくつかのオプションを指定できる
# 認証情報、HTTPメソッド、HTTPヘッダー
app.add_middleware(
    CORSMiddleware,
    allow_origins=origins,
    allow_credentials=True,
    allow_methods=["*"],
    allow_headers=["*"],
)


@app.get("/")
async def main():
    return {"message": "Hello World"}
```

+ 詳しくは、Moziilaのドキュメントを参照する

https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS

## SQL (Relational) Databases

+ SQLAlchemyを使ったサンプルがある

+ SQLAlchemyでサポートしているDBなら、簡単に適用できる
  + PostgreSQL
  + MySQL
  + SQLite
  + Oracle
  + Microsoft SQL Server, etc.

+ SQLiteを使った例
+ また、DockerベースのFastAPIとPostgreSQLを利用した公式プロジェクトジェネレータがある

+ SQLAlchemyに関する設定
+ DBのモデルを作成
  + relationshipを表現することもできる
+ Pydanticモデルの作成
+ CRUD処理
+ FastAPI app

## Bigger Applications - Multiple Files

+ 複数のファイルに分割して、フレキシブルに

+ APIRouterを使う

## Background Tasks

+ `BackgroundTasks`を利用する

## Metadata and Docs URLs

+ メタデータやドキュメントのURLをカスタマイズできる

## Static Files

+ `StaticFiles`を使う。

## Testing

+ Starlette: Requestに基づいている
  + TestClientは、reqeustsと同じように使える
+ pytestがそのまま使える

+ APIとテストを別ファイルにすることもできる

+ テストの内容
  + status code
  + jsonの中身

## Debugging

+ VSCodeやPyCharmなどで、デバッガを使うことができる

## Deployment

+ Docker Swarm mode cluster with Traefik and HTTPS
  + サーバの利用代が月$5〜

+ FastAPI Project Generatorsで、テンプレートを生成

## 疑問点

+ CRUDの書き方は?
  + SQL (Relational) Databasesを参考にする

+ エンティティの依存関係は、どのように実現する?

+ 管理者アカウントは楽に作れる?

+ データを保存する方法は?
  + MySQL
  + Firebase

+ Vercelにデプロイできる?

+ CORS対策は?
  + Use CORSMiddlewareを見る

+ mypyによる型チェックに対応している?
  + Yes

+ Pydanticとは?
  + モデルの定義を便利にしてくれる
  + バリデーションも行ってくれる?

+ Pytestは使える?
  + Yes

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

+ MySQLに接続する方法は?

+ pydanticとSQLAlchemyでは、モデルの書き方が違う。
  + 両者の役割の違いはどうなっている?
  + なぜ分けて定義している?

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
