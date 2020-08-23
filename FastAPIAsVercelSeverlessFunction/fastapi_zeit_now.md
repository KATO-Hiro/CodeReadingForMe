# FastAPI appをVercelにデプロイする

## 現状

+ バックエンド: FastAPIの初歩をオンライン勉強会で学んだ。事前に公開されていた資料を見ながら、動かした程度。

+ フロントエンド: 別プロジェクトで、Next.jsを利用し簡単なビューを作成した。

## 技術選定の理由

+ FastAPI
  + Pythonで記述できる
  + 既存のFWよりもかなり高速との触れ込み（Qiitaの記事で見た）
  + APIのドキュメントが自動生成される

+ Next.js
  + Reactをさらに便利にしたライブラリとの認識
  + プロジェクトのサンプルが豊富
  + レンダリングがVueよりも高速な傾向にある（とベンチマークをとった記事で見た）
  + Vercelを利用すると、デプロイが簡単かつ無料でサポートされている

## 目標

+ 大目標: バックエンドとフロントエンドを分離した簡単なアプリを作る。

+ 小目標: Fast APIのappをVercelにデプロイできるようにする。

## ディレクトリ・ファイル構造

```md
.
├── README.md
├── app
│   ├── __init__.py
│   ├── crud.py
│   ├── database.py
│   ├── main.py
│   ├── models.py
│   └── schemas.py
├── now.json
├── requirements.txt
└── tmp
    └── test.db
```

## READMEから得られた情報

+ FastAPIのアプリをVercel(Zeit) Serverless Functionとしてデプロイしている。
+ チュートリアルのデモンストレーション
+ 動かすことのできるデモのリンクが貼られている
  + Open API Docs:
  + API endpoints:

+ デプロイにあたっての注意事項
  + FastAPIでは、全てのレスポンスについてno-cacheに設定された Cache-Control headerを返すように設定されている。静的キャッシュはZeitで自動的に行われるので、この例ではZeit CDNが何もキャッシュしないことを保証します。キャッシュの扱いは、公式Docを参照する。
  + DBにsqliteを使っている
    + サーバーレスデプロイの性質上、sqliteファイルは書き込めないので、DBを変更しようとするPOSTリクエストは失敗する。
    + 本番環境では、FastAPIアプリは他の場所でホストされているデータベースに接続する。
  + あくまでもZeitとの統合の一例。

+ レポジトリの背景
  + ASGIに対応しているサーバレス関数のサービスがない/少ない or 設定がやや大変
  + Vercel(Zeit) Nowなら開発・デプロイが簡単

### 設定

+ now.json
+ pip installでrequirements.txtにあるパッケージをインストール
+ ルーティングの設定
  + 一つの機能が複数のルートに対応する必要がある場合に、そのままだとうまく行かない

```json
  "routes": [
    { "src": "/(.*)", "dest": "app/main.py" }
  ]
```

+ 関数の定義
  + now.jsonのbuildsに設定を追加

```json
  "builds": [
    { "src": "/app/main.py", "use": "@now/python" }
  ]
```

## source code

### main.py

```py
from typing import List

from fastapi import Request, Depends, FastAPI, HTTPException
from sqlalchemy.orm import Session

from . import crud, models, schemas
from .database import SessionLocal, engine

# Because we're providing a sqlite db with data,
# we don't need to recreate the database file on startup.
# This simplifies the serverless example.
# models.Base.metadata.create_all(bind=engine)

app = FastAPI()


# Dependency
def get_db():
    try:
        db = SessionLocal()
        yield db
    finally:
        db.close()

# Return a Cache-Control header for all requests.
# The no-cache directive disables caching on the zeit CDN.
# Including this better demonstrates using FastAPI as a
# serverless function.
@app.middleware("http")
async def add_no_cache_header(request: Request, call_next):
    response = await call_next(request)
    response.headers["Cache-Control"] = "no-cache"
    return response


@app.get("/")
def welcome(name=None):
    if name is not None:
        return f"Welcome to serverless FastAPI, { name }!"

    return "Welcome to serverless FastAPI!"


@app.post("/users/", response_model=schemas.User)
def create_user(user: schemas.UserCreate, db: Session = Depends(get_db)):
    db_user = crud.get_user_by_email(db, email=user.email)
    if db_user:
        raise HTTPException(status_code=400, detail="Email already registered")
    return crud.create_user(db=db, user=user)


@app.get("/users/", response_model=List[schemas.User])
def read_users(skip: int = 0, limit: int = 100, db: Session = Depends(get_db)):
    users = crud.get_users(db, skip=skip, limit=limit)
    return users


@app.get("/users/{user_id}", response_model=schemas.User)
def read_user(user_id: int, db: Session = Depends(get_db)):
    db_user = crud.get_user(db, user_id=user_id)
    if db_user is None:
        raise HTTPException(status_code=404, detail="User not found")
    return db_user


@app.post("/users/{user_id}/items/", response_model=schemas.Item)
def create_item_for_user(
    user_id: int, item: schemas.ItemCreate, db: Session = Depends(get_db)
):
    return crud.create_user_item(db=db, item=item, user_id=user_id)


@app.get("/items/", response_model=List[schemas.Item])
def read_items(skip: int = 0, limit: int = 100, db: Session = Depends(get_db)):
    items = crud.get_items(db, skip=skip, limit=limit)
    return items
```

#### 疑問点、感想など

+ main.pyにまとめてAPIを書くのが標準的になっている?
  + 実務などで利用するときは、もう少し分割した方がいいのでは?
  + Vercelの制約によるもの?
+ response_modelで、モデルを指定している
  + schemasで定義したモデルを利用
+ /apiにないのはなぜ?

### models.py

```py
# models/model_name.pyとしていないのはなぜ?
from sqlalchemy import Boolean, Column, ForeignKey, Integer, String
from sqlalchemy.orm import relationship

from .database import Base

# Baseを継承している
# クラス名としては単数形、テーブル名は複数形で指定
class User(Base):
    __tablename__ = "users"

    # 主キー、インデックス、重複の可否、初期値などを指定
    # HACK: attr = Column(type, ...)と記述するのは、やや手間では?
    id = Column(Integer, primary_key=True, index=True)
    email = Column(String, unique=True, index=True)
    hashed_password = Column(String)
    is_active = Column(Boolean, default=True)

    # モデル同士の関連を指定できる
    items = relationship("Item", back_populates="owner")


class Item(Base):
    __tablename__ = "items"

    id = Column(Integer, primary_key=True, index=True)
    title = Column(String, index=True)
    description = Column(String, index=True)
    # 外部キーの指定
    owner_id = Column(Integer, ForeignKey("users.id"))

    owner = relationship("User", back_populates="items")
```

### schemas.py

```py
from typing import List

from pydantic import BaseModel


# Q: なぜmodels.pyとは別に定義している?
# A(仮): データを入力・更新するところだけを切り出している? カプセル化して、見えてはいけない部分が見えないようにしている?
# Q: models.pyとschemas.pyでモデルの記法が異なるのはなぜ?
# 個人的には、ItemXXXとUserXXXを別ファイルに分けたい

# Baseモデルを継承
# Q: BaseModelを継承したXxxBase、XXXBaseを継承したXXXCreate・XXXに分けているのはなぜ?
# Q: 上記のクラス分けは何かルールがある?それとも、作成者の工夫?
class ItemBase(BaseModel):
    title: str
    description: str = None


class ItemCreate(ItemBase):
    pass


class Item(ItemBase):
    id: int
    owner_id: int

    class Config:
        orm_mode = True


class UserBase(BaseModel):
    email: str


class UserCreate(UserBase):
    password: str


class User(UserBase):
    id: int
    is_active: bool
    items: List[Item] = []

    class Config:
        orm_mode = True
```

### crud.py

```py
from sqlalchemy.orm import Session

from . import models, schemas

# メソッドチェーンで、該当するデータを取り出している
def get_user(db: Session, user_id: int):
    return db.query(models.User).filter(models.User.id == user_id).first()


def get_user_by_email(db: Session, email: str):
    return db.query(models.User).filter(models.User.email == email).first()


def get_users(db: Session, skip: int = 0, limit: int = 100):
    return db.query(models.User).offset(skip).limit(limit).all()

# DBにデータを追加する方法
# db.add(data)
# db.commit()
# db.refresh(data) # refresh()を入れているのはなぜ?
def create_user(db: Session, user: schemas.UserCreate):
    fake_hashed_password = user.password + "notreallyhashed"
    db_user = models.User(email=user.email, hashed_password=fake_hashed_password)
    db.add(db_user)
    db.commit()
    db.refresh(db_user)
    return db_user


def get_items(db: Session, skip: int = 0, limit: int = 100):
    return db.query(models.Item).offset(skip).limit(limit).all()


def create_user_item(db: Session, item: schemas.ItemCreate, user_id: int):
    db_item = models.Item(**item.dict(), owner_id=user_id)
    db.add(db_item)
    db.commit()
    db.refresh(db_item)
    return db_item
```

### database.py

```py
from sqlalchemy import create_engine
from sqlalchemy.ext.declarative import declarative_base
from sqlalchemy.orm import sessionmaker

# Q: ここを変えれば、他のDBも使える?
# Q: DBによって、設定方法が異なる?
# Q: passwordが変数に含まれているのは、セキュリティ的にマズいのでは?
SQLALCHEMY_DATABASE_URL = "sqlite:///./tmp/test.db"
# SQLALCHEMY_DATABASE_URL = "postgresql://user:password@postgresserver/db"

engine = create_engine(
    SQLALCHEMY_DATABASE_URL, connect_args={"check_same_thread": False}
)
SessionLocal = sessionmaker(autocommit=False, autoflush=False, bind=engine)

Base = declarative_base()
```

## 疑問点

+ 用語
  + endpointとは?

+ レポジトリ作成者は、どこから情報を仕入れている?
  + FastAPI SQL Databases Tutorial
  + FastAPIおよびVercelの公式Doc、GitHubのIssues

+ FastAPI
  + 標準的なディレクトリ構成は?
    + Vercelへのデプロイで指定はある?
  + モデル同士の関係を扱うことはできる?
  + 設定ファイルは、何が必要?
  + DBとの連携方法は?
  + 対応しているDBは?
    + RDB or RDB以外?
  + テストフレームワークは何を使っている?
    + Pytestが使えると嬉しい
    + A: 該当レポジトリでは、未使用
  + APIのテストは、何を確認することが大事?
  + 著名なOSSプロジェクトはある?

+ そもそもServerless Functionとは?
  + 仕組みは?
  + これまでのものと何が違う?
  + どんなメリットがある?
  + どんなデメリットがある?

+ Cache-Controlとは?
  + no-cacheを設定するとどうなる?

+ Next.js
  + FastAPIで作成したAPIにアクセスするには?
  + CORS対策はどうなっている?
  + ユーザの状態を保存するには? できればReduxを使わずに済ませたい
  + ソーシャルログインを実装する方法は?
    + Firebaseを使った方法
  + グローバルな値をどのように保持・更新するか?
    + Hooksを使えばいい?
  + テストフレームワークは何がある?
  + どのような項目をテストすればいい?
  + APIが実装されるまで、どのように対処している?
    + Mockのようなものがある?
  + Zeit Asynchronous Server Gateway Interface (ASGI)とは?
  + Zeit CDNとは?

+ Vercel
  + 無料枠でできることに制約はないか?
  + 2020年4月時点からの変更点は?
  + DBは、sqlite以外も使える?
  + サーバレスデプロイだとPOSTリクエストが使えない?

+ Dockerコンテナ
  + 権限周りの設定方法は?
  + 設定ファイルの書き方で注意すべき点は?

+ レポジトリ構成は?
  + バックエンドとフロントエンドを分ける? or 同一?

+ セキュリティ対策はどうすればいい?

+ 今後に向けてテンプレート化できるところはないか?

## 次の課題

+ FastAPIの公式チュートリアルを試す
+ ORM: sqlalchemy以外にも選択肢がないか調べる
+ 今回の構成で、POSTリクエストが使えるかどうか調べる
  + レポジトリの最終更新が約6か月前なので、状況が変わっている可能性が高い
+ デプロイ先をVercel Now以外にも選択肢がないか検討する

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

[FastAPI Zeit Now](https://github.com/paul121/fastapi-zeit-now)
