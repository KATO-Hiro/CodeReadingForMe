# Netflixのdispatchから学んだことをメモ

## 現状

+ バックエンドのフレームワークとして、FastAPIの利用を考えている

+ fastapi-reactで自動生成したコードだと、機能の追加・修正がしづらい気がする

## 目標

+ FastAPIの標準的なディレクトリ構成を学ぶ

+ 自分のブロジェクトで、少しずつリファクタリングを進める

+ 既存コードのリファクタリングをしながら、理解を深める

## レポジトリの選定理由

+ FastAPIを採用しているプロジェクトとして、スターの数が多い

+ パッと見ではあるが、ディレクトリ構成が分かりやすい

## このレポジトリから何を学びたいのか?

+ ディレクトリ構成
+ ファイル構成
+ オブジェクトの粒度の目安
+ 何をテストするべきか?
+ logの取り方

## ディレクトリ構成

+ alembicディレクトリは、ツールによって生成されたコードのため、fastapi-reactと同じだと思われる
+ エンティティ単位でディレクトリが分けられている
  + incidentに関する内容は、さらに細かく分けられている
+ 各ディレクトリの基本的なファイル構成

```md
- ベース
  - __init__.py
  - models.py # モデルの属性と型を定義している。ベースとなるクラスがある。モデルに対応する操作で、差分が記述されている。
  - service.py # 各HTTPメソッドに対応した、DBとのやりとりを含む詳細な処理が記述されている
  - views.py # services.pyで定義されたメソッドを使って、APIを定義しているように見える + レスポンスコードや例外の処理はここで行なっている。

- オプション
  - flows.py
  - scheduled.py
  - messaging.py
  - enums.py
  - permissions.py # authディレクトリのみ

```

+ api.py: 全てのAPIを束ねている
+ config.py: .envファイルで設定した情報を読み取っている。開発環境、ステージング環境、本番環境で切り替えられるようになっていると思われる。
+ database.py: DBに対する操作を抽象化して、メソッド化している
+ decorators.py: デコレータを定義している
+ enums.py: 列挙型の変数を定義している
+ exceptions.py: 独自の例外を定義している
+ main.py: バックエンド全体のソースコードを束ねている
  + ライブラリ、モジュールのインポート
    + Pythonの標準ライブラリ
    + pipでインストールしたサードパーティライブラリ
    + 自作のモジュール
  + ミドルウェアの設定
    + HTTPヘッダの設定
  + APIのバージョン指定
  + 利用可能なエンドポイントをログに出力
+ scheduler.py: スケジュールを一元的に操作するためのクラス。ここでは、具体的なスケジュールタスクを実装していない。

```py
# models.py

from typing import List, Optional

from pydantic import validator
from sqlalchemy import Table, Column, Integer, String, ForeignKey, PrimaryKeyConstraint
from sqlalchemy.orm import relationship
from sqlalchemy_utils import TSVectorType

# auth
## パスワードの生成
## パスワードのハッシュ化を行っている

# Association tables
# テーブル同士の関連を記述することもある


# Q: sqlalchemyとPydanticで、なぜモデルを別々に定義している?
class Hoge:
    # Columns
    id = Column(Integer, primary_key=True)
    foo = Column(DataType)

    # Relationships
    fuga_id = Column(DataType, ForeignKey("fuga.id"))


# Pydantic models..
class HogeBase:
    attr1: type1
    attr2: type2 = default_value

    @validator("attr1")
    def attr1_required(cls, value):
        if not value:
            raise ValueError("Must not be empty type")

# モデルとHTTPの操作を合わせた名称が多い
class HogeCreate(HogeBase):
    # TODO: HogeBaseとの差分を記述


class HogeUpdate(HogeBase):
    # TODO: HogeBaseとの差分を記述


class HogeRead(HogeBase):
    # TODO: HogeBaseとの差分を記述

class HogePagination(HogeBase):
    # TODO: HogeBaseとの差分を記述
```

```py
# service.py
from typing import List, Optional

from .models import Hoge, HogeCreate, HogeUpdate

# get(*, db_session, hoge_id: int) -> Optional[Hoge]
# get_by_text(*, db_session, text: str) -> Optional[Hoge]
# get_all(*, db_session) -> List[Optional[Hoge]]
# create(*, db_session, hoge_in: HogeCreate) -> Hoge
# create_all(*, db_session, hoges_in: List[HogeCreate]) -> List[Hoge]
# update(*, db_session, hoge: Hoge, hoge_in: HogeUpdate) -> Hoge:
# delete(*, db_session, hoge_id: int)
# upsert(*, db_session, hoge_in: HogeCreate) -> Hoge

# db_session.queryで、DBに対してSQLに相当する操作を行なっているものと思われる
# ここでは、DBの種類を意識せずに済むようになっている
def get(*, db_session, definition_id: int) -> Optional[Definition]:
    return db_session.query(Definition).filter(Definition.id == definition_id).first()


def get_by_text(*, db_session, text: str) -> Optional[Definition]:
    return db_session.query(Definition).filter(Definition.text == text).first()


def get_all(*, db_session) -> List[Optional[Definition]]:
    return db_session.query(Definition)


# db_session.commit()で、変更を保存
# db_session.refresh()を行なっている場合とそうでない場合があるのはなぜ?
def create(*, db_session, definition_in: DefinitionCreate) -> Definition:
    terms = [
        term_service.get_or_create(db_session=db_session, term_in=t) for t in definition_in.terms
    ]
    definition = Definition(**definition_in.dict(exclude={"terms"}), terms=terms)
    db_session.add(definition)
    db_session.commit()
    return definition


def create_all(*, db_session, definitions_in: List[DefinitionCreate]) -> List[Definition]:
    definitions = [Definition(text=d.text) for d in definitions_in]
    db_session.bulk_save_insert(definitions)
    db_session.commit()
    db_session.refresh()
    return definitions


def update(*, db_session, definition: Definition, definition_in: DefinitionUpdate) -> Definition:
    definition_data = jsonable_encoder(definition)

    terms = [
        term_service.get_or_create(db_session=db_session, term_in=t) for t in definition_in.terms
    ]
    update_data = definition_in.dict(skip_defaults=True, exclude={"terms"})

    for field in definition_data:
        if field in update_data:
            setattr(definition, field, update_data[field])

    definition.terms = terms
    db_session.add(definition)
    db_session.commit()
    return definition


def delete(*, db_session, definition_id: int):
    definition = db_session.query(Definition).filter(Definition.id == definition_id).first()
    definition.terms = []
    db_session.delete(definition)
    db_session.commit()


# Q: upsertとは?
# Q: upsertの使いどころは?
def upsert(*, db_session, definition_in: DefinitionCreate) -> Definition:
    # we only care about unique columns
    q = db_session.query(Definition).filter(Definition.text == definition_in.text)
    instance = q.first()

    # there are no updatable fields
    if instance:
        return instance

    return create(db_session=db_session, definition_in=definition_in)
```

```py
# views.py
from fastapi import APIRouter, Depends, HTTPException
from sqlalchemy.orm import Session

# 書き方がReactやNext.jsっぽい
from .models import (
    Hoge,
    HogeCreate,
    HogePagination,
    HogeRead,
    HogeUpdate
)

from .service import (
    create,
    delete,
    get,
    get_all,
    get_by_text,
    update
)

# /authでは、auth_routerとuser_routerに分けている
router = APIRouter()


# APIの記述は、多くの場合で共通していそうなので、抽象化すると実装量が減らせそう
# 自分でも思いつくレベルなのに、抽象化が行われていないのは何か理由があるはず
# そもそも、内容の記述が共通しているように見えるのが、思い込みなのか?

# デコレータのrouter.HTTPメソッド名称
# URIの指定や戻り値のモデルも指定できる
@router.get("/", response_model=DefinitionPagination)
def get_definitions(
    db_session: Session = Depends(get_db), page: int = 1, itemsPerPage: int = 5, q: str = None
):
    """
    Get all definitions.
    """

    # クエリがあるかどうかで、処理を分けている
    if q:
        query = search(db_session=db_session, query_str=q, model=Definition)
    else:
        query = get_all(db_session=db_session)

    items, total = paginate(query=query, page=page, items_per_page=itemsPerPage)

    # 戻り値はdict型
    return {"items": items, "total": total}


# 一覧と固有のidの内容を取得するときは、一覧の記述が上になっている
# 恐らく上から順番に処理されているため
@router.get("/{definition_id}", response_model=DefinitionRead)
def get_definition(*, db_session: Session = Depends(get_db), definition_id: int):
    """
    Update a definition.
    """
    # ↑ Get a definitionのはず。

    definition = get(db_session=db_session, definition_id=definition_id)

    # 例外処理
    if not definition:
        raise HTTPException(status_code=404, detail="The definition with this id does not exist.")
    return definition


@router.post("/", response_model=DefinitionRead)
def create_definition(*, db_session: Session = Depends(get_db), definition_in: DefinitionCreate):
    """
    Create a new definition.
    """
    definition = get_by_text(db_session=db_session, text=definition_in.text)

    # 既に登録されていないかチェックして、同じものが作成されないようにしている
    if definition:
        raise HTTPException(
            status_code=400,
            detail=f"The description with this text ({definition_in.text}) already exists.",
        )
    definition = create(db_session=db_session, definition_in=definition_in)
    return definition


@router.put("/{definition_id}", response_model=DefinitionRead)
def update_definition(
    *, db_session: Session = Depends(get_db), definition_id: int, definition_in: DefinitionUpdate
):
    """
    Update a definition.
    """
    definition = get(db_session=db_session, definition_id=definition_id)

    # 既存の内容の有無をチェック
    if not definition:
        raise HTTPException(status_code=404, detail="The definition with this id does not exist.")
    definition = update(db_session=db_session, definition=definition, definition_in=definition_in)
    return definition


@router.delete("/{definition_id}")
def delete_definition(*, db_session: Session = Depends(get_db), definition_id: int):
    """
    Delete a definition.
    """
    definition = get(db_session=db_session, definition_id=definition_id)
    if not definition:
        raise HTTPException(status_code=404, detail="The definition with this id does not exist.")
    delete(db_session=db_session, definition_id=definition_id)
```

```py
# api.py

# FastAPIに関連するライブラリをインポート
from fastapi import APIRouter, Depends
from starlette.responses import JSONResponse

# 自作のモジュールのインポート
from auth.service import get_current_user
from auth.views import user_router, auth_router
from hoges.views import router as hoges_router
...

# APIをルーティングに束ねるクラス用意
## 認証の有無で使い分け
api_router = APIRouter(
    default_response_class=JSONResponse
)  # WARNING: Don't use this unless you want unauthenticated routes
## Q: 何も指定しないと、認証が必須となる?デフォルト値を確認する
authenticated_api_router = APIRouter()

## 実装
# NOTE: All api routes should be authenticated by default
# ここに、hoge.views.pyで定義したrouterをざくざく書き込む
authenticated_api_router.include_router(hoges_router, prefix="hoges", tags=["hoges"])

# 統合
api_router.include_router(authenticated_api_router, dependencies=[Depends(get_current_user)])
```

```py
# main.py
# 標準ライブラリ
import logging
from os import path

# サードパーティ製のライブラリ
from fastapi import FastAPI
from starlette.middleware.base import BaseHTTPMiddleware, RequestResponseEndpoint
from starlette.requests import Request

# 自作モジュール
from .api import api_router
from .database import SessionLocal

# we add all API routes to the Web API framework
# Q: バージョンアップに対応できそうか?
# A: 要件に含まれていないことを事前に実装するのは悪手では?
api.include_router(api_router, prefix="/v1")
```

```md
.
├── __init__.py
├── alembic
│   ├── README
│   ├── __init__.py
│   ├── env.py
│   ├── script.py.mako
│   └── versions
│       ├── 03f742366750_.py
│       ├── 057604415f6c_.py
│       ├── ...
│       ├── ecdb4e7566f2_.py
│       └── fe8c213f2c54_.py
├── alembic.ini
├── api.py
├── auth
│   ├── __init__.py
│   ├── models.py
│   ├── permissions.py
│   ├── service.py
│   └── views.py
├── cli.py
├── common
│   ├── __init__.py
│   ├── managers.py
│   └── utils
│       ├── __init__.py
│       ├── cli.py
│       ├── composite_search.py
│       ├── json_schema.py
│       └── views.py
├── conference
│   ├── __init__.py
│   ├── models.py
│   └── service.py
├── config.py
├── conversation
│   ├── __init__.py
│   ├── enums.py
│   ├── messaging.py
│   ├── models.py
│   └── service.py
├── database.py
├── decorators.py
├── definition
│   ├── __init__.py
│   ├── models.py
│   ├── service.py
│   └── views.py
├── document
│   ├── __init__.py
│   ├── models.py
│   ├── scheduled.py
│   ├── service.py
│   └── views.py
├── enums.py
├── event
│   ├── __init__.py
│   ├── models.py
│   └── service.py
├── exceptions.py
├── extensions.py
├── feedback
│   ├── __init__.py
│   ├── enums.py
│   ├── messaging.py
│   ├── models.py
│   ├── scheduled.py
│   ├── service.py
│   └── views.py
├── group
│   ├── __init__.py
│   ├── models.py
│   └── service.py
├── incident
│   ├── __init__.py
│   ├── enums.py
│   ├── flows.py
│   ├── messaging.py
│   ├── metrics.py
│   ├── models.py
│   ├── scheduled.py
│   ├── service.py
│   └── views.py
├── incident_cost
│   ├── __init__.py
│   ├── models.py
│   ├── scheduled.py
│   ├── service.py
│   └── views.py
├── incident_cost_type
│   ├── __init__.py
│   ├── models.py
│   ├── service.py
│   └── views.py
├── incident_priority
│   ├── __init__.py
│   ├── models.py
│   ├── service.py
│   └── views.py
├── incident_type
│   ├── __init__.py
│   ├── models.py
│   ├── service.py
│   └── views.py
├── individual
│   ├── __init__.py
│   ├── models.py
│   ├── service.py
│   └── views.py
├── logging.py
├── main.py
├── messaging
│   ├── __init__.py
│   ├── email
│   │   ├── filters.py
│   │   ├── templates
│   │   │   ├── base.mjml
│   │   │   ├── executive_report.mjml
│   │   │   ├── notification.mjml
│   │   │   ├── notification_list.mjml
│   │   │   └── tactical_report.mjml
│   │   └── utils.py
│   └── strings.py
├── metrics.py
├── models.py
├── nlp.py
├── notification
│   ├── __init__.py
│   ├── models.py
│   ├── service.py
│   └── views.py
├── participant
│   ├── __init__.py
│   ├── flows.py
│   ├── models.py
│   └── service.py
├── participant_role
│   ├── __init__.py
│   ├── flows.py
│   ├── models.py
│   └── service.py
├── plugin
│   ├── __init__.py
│   ├── models.py
│   ├── service.py
│   └── views.py
├── plugins
│   ├── __init__.py
│   ├── base
│   │   ├── __init__.py
│   │   ├── manager.py
│   │   └── v1.py
│   ├── bases
│   │   ├── __init__.py
│   │   ├── auth_provider.py
│   │   ├── conference.py
│   │   ├── contact.py
│   │   ├── conversation.py
│   │   ├── definition.py
│   │   ├── document.py
│   │   ├── document_resolver.py
│   │   ├── email.py
│   │   ├── metric.py
│   │   ├── oncall.py
│   │   ├── participant.py
│   │   ├── participant_group.py
│   │   ├── publish.py
│   │   ├── storage.py
│   │   ├── tag.py
│   │   ├── task.py
│   │   ├── term.py
│   │   ├── ticket.py
│   │   └── workflow.py
│   ├── dispatch_core
│   │   ├── __init__.py
│   │   ├── _version.py
│   │   ├── config.py
│   │   └── plugin.py
│   ├── dispatch_google
│   │   ├── __init__.py
│   │   ├── calendar
│   │   │   ├── __init__.py
│   │   │   ├── _version.py
│   │   │   ├── config.py
│   │   │   └── plugin.py
│   │   ├── common.py
│   │   ├── config.py
│   │   ├── docs
│   │   │   ├── __init__.py
│   │   │   ├── _version.py
│   │   │   └── plugin.py
│   │   ├── drive
│   │   │   ├── __init__.py
│   │   │   ├── _version.py
│   │   │   ├── drive.py
│   │   │   ├── plugin.py
│   │   │   └── task.py
│   │   ├── gmail
│   │   │   ├── __init__.py
│   │   │   ├── _version.py
│   │   │   └── plugin.py
│   │   └── groups
│   │       ├── __init__.py
│   │       ├── _version.py
│   │       └── plugin.py
│   ├── dispatch_jira
│   │   ├── __init__.py
│   │   ├── _version.py
│   │   ├── config.py
│   │   └── plugin.py
│   ├── dispatch_opsgenie
│   │   ├── __init__.py
│   │   ├── config.py
│   │   ├── plugin.py
│   │   └── service.py
│   ├── dispatch_pagerduty
│   │   ├── __init__.py
│   │   ├── _version.py
│   │   ├── config.py
│   │   ├── plugin.py
│   │   └── service.py
│   ├── dispatch_slack
│   │   ├── __init__.py
│   │   ├── _version.py
│   │   ├── actions.py
│   │   ├── commands.py
│   │   ├── config.py
│   │   ├── decorators.py
│   │   ├── dialogs.py
│   │   ├── events.py
│   │   ├── messaging.py
│   │   ├── modals.py
│   │   ├── plugin.py
│   │   ├── service.py
│   │   ├── socket_mode.py
│   │   ├── tests
│   │   │   └── __init__.py
│   │   └── views.py
│   ├── dispatch_test
│   │   ├── __init__.py
│   │   ├── conference.py
│   │   ├── contact.py
│   │   ├── conversation.py
│   │   ├── definition.py
│   │   ├── document.py
│   │   ├── document_resolver.py
│   │   ├── oncall.py
│   │   ├── participant.py
│   │   ├── participant_group.py
│   │   ├── storage.py
│   │   ├── task.py
│   │   ├── term.py
│   │   └── ticket.py
│   └── dispatch_zoom
│       ├── __init__.py
│       ├── _version.py
│       ├── client.py
│       ├── config.py
│       └── plugin.py
├── report
│   ├── __init__.py
│   ├── enums.py
│   ├── flows.py
│   ├── messaging.py
│   ├── models.py
│   ├── scheduled.py
│   └── service.py
├── route
│   ├── __init__.py
│   ├── models.py
│   ├── service.py
│   └── views.py
├── scheduler.py
├── search
│   ├── __init__.py
│   ├── models.py
│   ├── service.py
│   └── views.py
├── service
│   ├── __init__.py
│   ├── models.py
│   ├── service.py
│   └── views.py
├── static
├── storage
│   ├── __init__.py
│   ├── models.py
│   └── service.py
├── tag
│   ├── __init__.py
│   ├── models.py
│   ├── recommender.py
│   ├── scheduled.py
│   ├── service.py
│   └── views.py
├── tag_type
│   ├── __init__.py
│   ├── models.py
│   ├── service.py
│   └── views.py
├── task
│   ├── __init__.py
│   ├── flows.py
│   ├── models.py
│   ├── scheduled.py
│   ├── service.py
│   └── views.py
├── team
│   ├── __init__.py
│   ├── models.py
│   ├── service.py
│   └── views.py
├── term
│   ├── __init__.py
│   ├── models.py
│   ├── scheduled.py
│   ├── service.py
│   └── views.py
├── ticket
│   ├── __init__.py
│   ├── models.py
│   └── service.py
└── workflow
    ├── __init__.py
    ├── flows.py
    ├── models.py
    ├── scheduled.py
    ├── service.py
    └── views.py
```

## ファイル構成

```md
.
├── Dockerfile -> docker/Dockerfile
├── LICENSE
├── MANIFEST.in
├── README.md
├── SUMMARY.md
├── bin
│   └── run.py
├── data
│   ├── dispatch-sample-data.dump
│   └── update-example-data.sh
├── docker
│   ├── Dockerfile
│   └── docker-compose.yml
├── docs
│   ├── README.md
│   ├── SUMMARY.md
│   ├── branding
│   │   ├── FullColor_1280x1024.eps
│   │   ├── FullColor_1280x1024.pdf
│   │   ├── FullColor_1280x1024.svg
│   │   ├── FullColor_1280x1024_300dpi.jpg
│   │   ├── FullColor_1280x1024_72dpi.jpg
│   │   ├── FullColor_1280x1024_72dpi.png
│   │   ├── FullColor_IconOnly_1280x1024_72dpi.jpg
│   │   ├── FullColor_TextOnly_1280x1024_72dpi.jpg
│   │   ├── FullColor_TransparentBg_1280x1024_72dpi.png
│   │   ├── Grayscale_1280x1024_72dpi.png
│   │   ├── website_logo_solid_background.png
│   │   ├── website_logo_solid_background_icon_only_square.png
│   │   ├── website_logo_solid_background_logo_only.png
│   │   └── website_logo_transparent_background.png
│   ├── changelog.md
│   ├── cli.md
│   ├── configuration
│   │   ├── README.md
│   │   ├── app.md
│   │   └── plugins
│   │       ├── README.md
│   │       ├── configuring-g-suite.md
│   │       ├── configuring-jira.md
│   │       ├── configuring-opsgenie.md
│   │       ├── configuring-pagerduty.md
│   │       ├── configuring-slack.md
│   │       └── configuring-zoom.md
│   ├── contributing
│   │   ├── README.md
│   │   ├── core.md
│   │   ├── environment.md
│   │   └── plugins
│   │       ├── README.md
│   │       ├── interfaces.md
│   │       └── testing.md
│   ├── faq.md
│   ├── installation.md
│   ├── license.md
│   ├── security.md
│   ├── upgrading.md
│   └── user-guide
│       ├── README.md
│       ├── administration
│       │   ├── README.md
│       │   ├── configuration.md
│       │   ├── contacts.md
│       │   ├── incidents.md
│       │   └── knowledge.md
│       ├── incident-commander.md
│       └── incident-participant.md
├── pyproject.toml
├── requirements-base.txt
├── requirements-dev.txt
├── setup.cfg
├── setup.py
├── src
│   └── dispatch
│       ├── __init__.py
│       ├── alembic
│       │   ├── README
│       │   ├── __init__.py
│       │   ├── env.py
│       │   ├── script.py.mako
│       │   └── versions
│       │       ├── 03f742366750_.py
│       │       ├── 057604415f6c_.py
│       │       ├── 0694cd18ea4f_.py
│       │       ├── 1221a4d60f03_.py
│       │       ├── 14d7a4703d7c.py
│       │       ├── 189759c2b5bf_.py
│       │       ├── 1a4a20476cc4_.py
│       │       ├── 1adc96915b63_.py
│       │       ├── 1c3f808aa00c_.py
│       │       ├── 2d3e511db6b4_.py
│       │       ├── 343bc4e6aa79_.py
│       │       ├── 38f0b01448ba_.py
│       │       ├── 466823f3046e_.py
│       │       ├── 4691fc21e309_.py
│       │       ├── 50972429e3f0_.py
│       │       ├── 50d0688878ae_.py
│       │       ├── 5693756fd192_.py
│       │       ├── 57b023382626_.py
│       │       ├── 57b1d2ee5a06_.py
│       │       ├── 5957303b1b8a_.py
│       │       ├── 5d4dee3e24fc_.py
│       │       ├── 62465f508f69_.py
│       │       ├── 678b759299ff_.py
│       │       ├── 69ac7f6dc0da_.py
│       │       ├── 6f04af3f261b_.py
│       │       ├── 72c7fa0ce0a3_.py
│       │       ├── 7351fa734e2a_.py
│       │       ├── 81038e961414_.py
│       │       ├── 841d00729e31_.py
│       │       ├── 85bff3e51dc4_.py
│       │       ├── 86f99b17b421_.py
│       │       ├── 895ae7783033_.py
│       │       ├── 8b67c774279d_.py
│       │       ├── 995a59897e01_.py
│       │       ├── 996dea17749b_.py
│       │       ├── 99f3dec93615_.py
│       │       ├── 9a3478cbe76c_.py
│       │       ├── 9b4a5ff08170_.py
│       │       ├── 9eaa2a392dae_.py
│       │       ├── 9f927a8e4679_.py
│       │       ├── a32bfbb4bcaa_.py
│       │       ├── a57934336710_.py
│       │       ├── ab9879051d6c_.py
│       │       ├── b12f7a59ced9_.py
│       │       ├── c6db1c7655db_.py
│       │       ├── c86be389dc1a_.py
│       │       ├── cfd62f719c84_.py
│       │       ├── d0501fc6be89_.py
│       │       ├── d1e66a4ef671_.py
│       │       ├── d841f9945338_.py
│       │       ├── dd3df6a3af3c_.py
│       │       ├── e2bdb8fb6dfe_.py
│       │       ├── e3d9f5ca6958_.py
│       │       ├── e75e103693f2_.py
│       │       ├── e7c696ffd69a_.py
│       │       ├── ecdb4e7566f2_.py
│       │       └── fe8c213f2c54_.py
│       ├── alembic.ini
│       ├── api.py
│       ├── auth
│       │   ├── __init__.py
│       │   ├── models.py
│       │   ├── permissions.py
│       │   ├── service.py
│       │   └── views.py
│       ├── cli.py
│       ├── common
│       │   ├── __init__.py
│       │   ├── managers.py
│       │   └── utils
│       │       ├── __init__.py
│       │       ├── cli.py
│       │       ├── composite_search.py
│       │       ├── json_schema.py
│       │       └── views.py
│       ├── conference
│       │   ├── __init__.py
│       │   ├── models.py
│       │   └── service.py
│       ├── config.py
│       ├── conversation
│       │   ├── __init__.py
│       │   ├── enums.py
│       │   ├── messaging.py
│       │   ├── models.py
│       │   └── service.py
│       ├── database.py
│       ├── decorators.py
│       ├── definition
│       │   ├── __init__.py
│       │   ├── models.py
│       │   ├── service.py
│       │   └── views.py
│       ├── document
│       │   ├── __init__.py
│       │   ├── models.py
│       │   ├── scheduled.py
│       │   ├── service.py
│       │   └── views.py
│       ├── enums.py
│       ├── event
│       │   ├── __init__.py
│       │   ├── models.py
│       │   └── service.py
│       ├── exceptions.py
│       ├── extensions.py
│       ├── feedback
│       │   ├── __init__.py
│       │   ├── enums.py
│       │   ├── messaging.py
│       │   ├── models.py
│       │   ├── scheduled.py
│       │   ├── service.py
│       │   └── views.py
│       ├── group
│       │   ├── __init__.py
│       │   ├── models.py
│       │   └── service.py
│       ├── incident
│       │   ├── __init__.py
│       │   ├── enums.py
│       │   ├── flows.py
│       │   ├── messaging.py
│       │   ├── metrics.py
│       │   ├── models.py
│       │   ├── scheduled.py
│       │   ├── service.py
│       │   └── views.py
│       ├── incident_cost
│       │   ├── __init__.py
│       │   ├── models.py
│       │   ├── scheduled.py
│       │   ├── service.py
│       │   └── views.py
│       ├── incident_cost_type
│       │   ├── __init__.py
│       │   ├── models.py
│       │   ├── service.py
│       │   └── views.py
│       ├── incident_priority
│       │   ├── __init__.py
│       │   ├── models.py
│       │   ├── service.py
│       │   └── views.py
│       ├── incident_type
│       │   ├── __init__.py
│       │   ├── models.py
│       │   ├── service.py
│       │   └── views.py
│       ├── individual
│       │   ├── __init__.py
│       │   ├── models.py
│       │   ├── service.py
│       │   └── views.py
│       ├── logging.py
│       ├── main.py
│       ├── messaging
│       │   ├── __init__.py
│       │   ├── email
│       │   │   ├── filters.py
│       │   │   ├── templates
│       │   │   │   ├── base.mjml
│       │   │   │   ├── executive_report.mjml
│       │   │   │   ├── notification.mjml
│       │   │   │   ├── notification_list.mjml
│       │   │   │   └── tactical_report.mjml
│       │   │   └── utils.py
│       │   └── strings.py
│       ├── metrics.py
│       ├── models.py
│       ├── nlp.py
│       ├── notification
│       │   ├── __init__.py
│       │   ├── models.py
│       │   ├── service.py
│       │   └── views.py
│       ├── participant
│       │   ├── __init__.py
│       │   ├── flows.py
│       │   ├── models.py
│       │   └── service.py
│       ├── participant_role
│       │   ├── __init__.py
│       │   ├── flows.py
│       │   ├── models.py
│       │   └── service.py
│       ├── plugin
│       │   ├── __init__.py
│       │   ├── models.py
│       │   ├── service.py
│       │   └── views.py
│       ├── plugins
│       │   ├── __init__.py
│       │   ├── base
│       │   │   ├── __init__.py
│       │   │   ├── manager.py
│       │   │   └── v1.py
│       │   ├── bases
│       │   │   ├── __init__.py
│       │   │   ├── auth_provider.py
│       │   │   ├── conference.py
│       │   │   ├── contact.py
│       │   │   ├── conversation.py
│       │   │   ├── definition.py
│       │   │   ├── document.py
│       │   │   ├── document_resolver.py
│       │   │   ├── email.py
│       │   │   ├── metric.py
│       │   │   ├── oncall.py
│       │   │   ├── participant.py
│       │   │   ├── participant_group.py
│       │   │   ├── publish.py
│       │   │   ├── storage.py
│       │   │   ├── tag.py
│       │   │   ├── task.py
│       │   │   ├── term.py
│       │   │   ├── ticket.py
│       │   │   └── workflow.py
│       │   ├── dispatch_core
│       │   │   ├── __init__.py
│       │   │   ├── _version.py
│       │   │   ├── config.py
│       │   │   └── plugin.py
│       │   ├── dispatch_google
│       │   │   ├── __init__.py
│       │   │   ├── calendar
│       │   │   │   ├── __init__.py
│       │   │   │   ├── _version.py
│       │   │   │   ├── config.py
│       │   │   │   └── plugin.py
│       │   │   ├── common.py
│       │   │   ├── config.py
│       │   │   ├── docs
│       │   │   │   ├── __init__.py
│       │   │   │   ├── _version.py
│       │   │   │   └── plugin.py
│       │   │   ├── drive
│       │   │   │   ├── __init__.py
│       │   │   │   ├── _version.py
│       │   │   │   ├── drive.py
│       │   │   │   ├── plugin.py
│       │   │   │   └── task.py
│       │   │   ├── gmail
│       │   │   │   ├── __init__.py
│       │   │   │   ├── _version.py
│       │   │   │   └── plugin.py
│       │   │   └── groups
│       │   │       ├── __init__.py
│       │   │       ├── _version.py
│       │   │       └── plugin.py
│       │   ├── dispatch_jira
│       │   │   ├── __init__.py
│       │   │   ├── _version.py
│       │   │   ├── config.py
│       │   │   └── plugin.py
│       │   ├── dispatch_opsgenie
│       │   │   ├── __init__.py
│       │   │   ├── config.py
│       │   │   ├── plugin.py
│       │   │   └── service.py
│       │   ├── dispatch_pagerduty
│       │   │   ├── __init__.py
│       │   │   ├── _version.py
│       │   │   ├── config.py
│       │   │   ├── plugin.py
│       │   │   └── service.py
│       │   ├── dispatch_slack
│       │   │   ├── __init__.py
│       │   │   ├── _version.py
│       │   │   ├── actions.py
│       │   │   ├── commands.py
│       │   │   ├── config.py
│       │   │   ├── decorators.py
│       │   │   ├── dialogs.py
│       │   │   ├── events.py
│       │   │   ├── messaging.py
│       │   │   ├── modals.py
│       │   │   ├── plugin.py
│       │   │   ├── service.py
│       │   │   ├── socket_mode.py
│       │   │   ├── tests
│       │   │   │   └── __init__.py
│       │   │   └── views.py
│       │   ├── dispatch_test
│       │   │   ├── __init__.py
│       │   │   ├── conference.py
│       │   │   ├── contact.py
│       │   │   ├── conversation.py
│       │   │   ├── definition.py
│       │   │   ├── document.py
│       │   │   ├── document_resolver.py
│       │   │   ├── oncall.py
│       │   │   ├── participant.py
│       │   │   ├── participant_group.py
│       │   │   ├── storage.py
│       │   │   ├── task.py
│       │   │   ├── term.py
│       │   │   └── ticket.py
│       │   └── dispatch_zoom
│       │       ├── __init__.py
│       │       ├── _version.py
│       │       ├── client.py
│       │       ├── config.py
│       │       └── plugin.py
│       ├── report
│       │   ├── __init__.py
│       │   ├── enums.py
│       │   ├── flows.py
│       │   ├── messaging.py
│       │   ├── models.py
│       │   ├── scheduled.py
│       │   └── service.py
│       ├── route
│       │   ├── __init__.py
│       │   ├── models.py
│       │   ├── service.py
│       │   └── views.py
│       ├── scheduler.py
│       ├── search
│       │   ├── __init__.py
│       │   ├── models.py
│       │   ├── service.py
│       │   └── views.py
│       ├── service
│       │   ├── __init__.py
│       │   ├── models.py
│       │   ├── service.py
│       │   └── views.py
│       ├── static
│       │   └── dispatch
│       │       ├── babel.config.js
│       │       ├── package-lock.json
│       │       ├── package.json
│       │       ├── postcss.config.js
│       │       ├── public
│       │       │   ├── favicon.ico
│       │       │   ├── index.html
│       │       │   ├── manifest.json
│       │       │   ├── robots.txt
│       │       │   └── static
│       │       │       ├── data
│       │       │       │   ├── file.json
│       │       │       │   ├── font-awesome.json
│       │       │       │   ├── material.json
│       │       │       │   └── user.json
│       │       │       ├── error
│       │       │       │   ├── 403.svg
│       │       │       │   ├── 404.svg
│       │       │       │   └── 500.svg
│       │       │       ├── icon
│       │       │       │   └── file_empty.svg
│       │       │       ├── m.png
│       │       │       └── robots.txt
│       │       ├── src
│       │       │   ├── App.vue
│       │       │   ├── api.js
│       │       │   ├── app
│       │       │   │   ├── notificationStore.js
│       │       │   │   └── store.js
│       │       │   ├── assets
│       │       │   │   ├── logo.png
│       │       │   │   └── logo.svg
│       │       │   ├── auth
│       │       │   │   ├── Login.vue
│       │       │   │   ├── Register.vue
│       │       │   │   ├── Table.vue
│       │       │   │   ├── api.js
│       │       │   │   ├── basicAuthProvider.js
│       │       │   │   ├── customAuthProvider.js
│       │       │   │   ├── editSheet.vue
│       │       │   │   ├── pkceAuthProvider.js
│       │       │   │   └── store.js
│       │       │   ├── components
│       │       │   │   ├── AppDrawer.vue
│       │       │   │   ├── AppToolbar.vue
│       │       │   │   ├── DatePickerMenu.vue
│       │       │   │   ├── Loading.vue
│       │       │   │   ├── NotificationSnackbarsWrapper.vue
│       │       │   │   ├── PageHeader.vue
│       │       │   │   ├── Refresh.vue
│       │       │   │   ├── StatWidget.vue
│       │       │   │   ├── TimePickerMenu.vue
│       │       │   │   ├── circle
│       │       │   │   │   ├── VCircle.js
│       │       │   │   │   └── VCircle.styl
│       │       │   │   └── layouts
│       │       │   │       ├── AdminLayout.vue
│       │       │   │       ├── BasicLayout.vue
│       │       │   │       ├── DashboardLayout.vue
│       │       │   │       ├── DefaultLayout.vue
│       │       │   │       └── index.js
│       │       │   ├── dashboard
│       │       │   │   ├── DashboardCard.vue
│       │       │   │   ├── DialogFilter.vue
│       │       │   │   ├── IncidentOverview.vue
│       │       │   │   ├── TaskDialogFilter.vue
│       │       │   │   ├── TaskOverview.vue
│       │       │   │   └── utils.js
│       │       │   ├── definition
│       │       │   │   ├── DefinitionCombobox.vue
│       │       │   │   ├── DeleteDialog.vue
│       │       │   │   ├── List.vue
│       │       │   │   ├── NewEditSheet.vue
│       │       │   │   ├── Table.vue
│       │       │   │   ├── api.js
│       │       │   │   └── store.js
│       │       │   ├── document
│       │       │   │   ├── DeleteDialog.vue
│       │       │   │   ├── DocumentSelect.vue
│       │       │   │   ├── DocumentSummaryTable.vue
│       │       │   │   ├── NewEditSheet.vue
│       │       │   │   ├── Table.vue
│       │       │   │   ├── api.js
│       │       │   │   └── store.js
│       │       │   ├── event.js
│       │       │   ├── feedback
│       │       │   │   ├── DeleteDialog.vue
│       │       │   │   ├── Table.vue
│       │       │   │   ├── TableFilterDialog.vue
│       │       │   │   ├── api.js
│       │       │   │   └── store.js
│       │       │   ├── filters.js
│       │       │   ├── incident
│       │       │   │   ├── BulkEditSheet.vue
│       │       │   │   ├── CostsTab.vue
│       │       │   │   ├── DeleteDialog.vue
│       │       │   │   ├── DetailsTab.vue
│       │       │   │   ├── EditSheet.vue
│       │       │   │   ├── ExecutiveReportSheet.vue
│       │       │   │   ├── IncidentActiveTimeCard.vue
│       │       │   │   ├── IncidentCombobox.vue
│       │       │   │   ├── IncidentCostBarChartCard.vue
│       │       │   │   ├── IncidentFilterCombobox.vue
│       │       │   │   ├── IncidentForecastCard.vue
│       │       │   │   ├── IncidentHeatmapCard.vue
│       │       │   │   ├── IncidentMembersCombobox.vue
│       │       │   │   ├── IncidentPrimaryLocationBarChartCard.vue
│       │       │   │   ├── IncidentPrimaryTeamBarChartCard.vue
│       │       │   │   ├── IncidentPriority.vue
│       │       │   │   ├── IncidentPriorityBarChartCard.vue
│       │       │   │   ├── IncidentReportDialog.vue
│       │       │   │   ├── IncidentResolveTimeCard.vue
│       │       │   │   ├── IncidentSelect.vue
│       │       │   │   ├── IncidentStatus.vue
│       │       │   │   ├── IncidentStatusMultiSelect.vue
│       │       │   │   ├── IncidentSummaryTable.vue
│       │       │   │   ├── IncidentTagsTreemapCard.vue
│       │       │   │   ├── IncidentTypeBarChartCard.vue
│       │       │   │   ├── NewSheet.vue
│       │       │   │   ├── Participant.vue
│       │       │   │   ├── ParticipantSelect.vue
│       │       │   │   ├── ParticipantsTab.vue
│       │       │   │   ├── ReportDialog.vue
│       │       │   │   ├── ReportForm.vue
│       │       │   │   ├── ReportReceiptCard.vue
│       │       │   │   ├── ReportSubmissionCard.vue
│       │       │   │   ├── ResourcesTab.vue
│       │       │   │   ├── Status.vue
│       │       │   │   ├── Table.vue
│       │       │   │   ├── TableExportDialog.vue
│       │       │   │   ├── TableFilterDialog.vue
│       │       │   │   ├── TimelineTab.vue
│       │       │   │   ├── WorkflowInstanceTab.vue
│       │       │   │   ├── api.js
│       │       │   │   └── store.js
│       │       │   ├── incident_cost
│       │       │   │   └── IncidentCostInput.vue
│       │       │   ├── incident_cost_type
│       │       │   │   ├── DeleteDialog.vue
│       │       │   │   ├── IncidentCostTypeSelect.vue
│       │       │   │   ├── NewEditSheet.vue
│       │       │   │   ├── Table.vue
│       │       │   │   ├── api.js
│       │       │   │   └── store.js
│       │       │   ├── incident_priority
│       │       │   │   ├── IncidentPriorityCombobox.vue
│       │       │   │   ├── IncidentPriorityMultiSelect.vue
│       │       │   │   ├── IncidentPrioritySelect.vue
│       │       │   │   ├── NewEditSheet.vue
│       │       │   │   ├── Table.vue
│       │       │   │   ├── api.js
│       │       │   │   └── store.js
│       │       │   ├── incident_type
│       │       │   │   ├── IncidentTypeCombobox.vue
│       │       │   │   ├── IncidentTypeMultiSelect.vue
│       │       │   │   ├── IncidentTypeSelect.vue
│       │       │   │   ├── NewEditSheet.vue
│       │       │   │   ├── Table.vue
│       │       │   │   ├── api.js
│       │       │   │   └── store.js
│       │       │   ├── individual
│       │       │   │   ├── DeleteDialog.vue
│       │       │   │   ├── Individual.vue
│       │       │   │   ├── IndividualCombobox.vue
│       │       │   │   ├── IndividualSelect.vue
│       │       │   │   ├── List.vue
│       │       │   │   ├── NewEditSheet.vue
│       │       │   │   ├── Table.vue
│       │       │   │   ├── api.js
│       │       │   │   └── store.js
│       │       │   ├── main.js
│       │       │   ├── mixins
│       │       │   │   └── update.js
│       │       │   ├── notification
│       │       │   │   ├── DeleteDialog.vue
│       │       │   │   ├── NewEditSheet.vue
│       │       │   │   ├── Table.vue
│       │       │   │   ├── api.js
│       │       │   │   └── store.js
│       │       │   ├── plugin
│       │       │   │   ├── PluginCombobox.vue
│       │       │   │   ├── PluginMetadataInput.vue
│       │       │   │   ├── Table.vue
│       │       │   │   ├── api.js
│       │       │   │   ├── editSheet.vue
│       │       │   │   └── store.js
│       │       │   ├── registerServiceWorker.js
│       │       │   ├── route
│       │       │   │   ├── Table.vue
│       │       │   │   ├── api.js
│       │       │   │   └── store.js
│       │       │   ├── router
│       │       │   │   ├── config.js
│       │       │   │   └── index.js
│       │       │   ├── search
│       │       │   │   ├── AdvancedEditor.vue
│       │       │   │   ├── ResultList.vue
│       │       │   │   ├── SearchFilter.vue
│       │       │   │   ├── SearchFilterCombobox.vue
│       │       │   │   ├── SearchFilterCreateDialog.vue
│       │       │   │   ├── api.js
│       │       │   │   ├── store.js
│       │       │   │   └── utils.js
│       │       │   ├── service
│       │       │   │   ├── DeleteDialog.vue
│       │       │   │   ├── List.vue
│       │       │   │   ├── NewEditSheet.vue
│       │       │   │   ├── ServiceSelect.vue
│       │       │   │   ├── Table.vue
│       │       │   │   ├── api.js
│       │       │   │   └── store.js
│       │       │   ├── service-worker.js
│       │       │   ├── store.js
│       │       │   ├── styles
│       │       │   │   └── variables.sass
│       │       │   ├── tag
│       │       │   │   ├── DeleteDialog.vue
│       │       │   │   ├── List.vue
│       │       │   │   ├── NewEditSheet.vue
│       │       │   │   ├── Table.vue
│       │       │   │   ├── TagCombobox.vue
│       │       │   │   ├── TagFilterCombobox.vue
│       │       │   │   ├── TagSummaryTable.vue
│       │       │   │   ├── api.js
│       │       │   │   └── store.js
│       │       │   ├── tag_type
│       │       │   │   ├── NewEditSheet.vue
│       │       │   │   ├── Table.vue
│       │       │   │   ├── TagTypeSelect.vue
│       │       │   │   ├── api.js
│       │       │   │   └── store.js
│       │       │   ├── task
│       │       │   │   ├── AssigneeCombobox.vue
│       │       │   │   ├── BulkEditSheet.vue
│       │       │   │   ├── DeleteDialog.vue
│       │       │   │   ├── NewEditSheet.vue
│       │       │   │   ├── Table.vue
│       │       │   │   ├── TableExportDialog.vue
│       │       │   │   ├── TableFilterDialog.vue
│       │       │   │   ├── TaskActiveTimeCard.vue
│       │       │   │   ├── TaskIncidentPriorityBarChartCard.vue
│       │       │   │   ├── TaskIncidentTypeBarChartCard.vue
│       │       │   │   ├── TaskStatusMultiSelect.vue
│       │       │   │   ├── TaskSummaryTable.vue
│       │       │   │   ├── api.js
│       │       │   │   └── store.js
│       │       │   ├── team
│       │       │   │   ├── DeleteDialog.vue
│       │       │   │   ├── List.vue
│       │       │   │   ├── NewEditSheet.vue
│       │       │   │   ├── Table.vue
│       │       │   │   ├── api.js
│       │       │   │   └── store.js
│       │       │   ├── term
│       │       │   │   ├── DeleteDialog.vue
│       │       │   │   ├── List.vue
│       │       │   │   ├── NewEditSheet.vue
│       │       │   │   ├── Table.vue
│       │       │   │   ├── TermCombobox.vue
│       │       │   │   ├── api.js
│       │       │   │   └── store.js
│       │       │   ├── util
│       │       │   │   └── index.js
│       │       │   ├── views
│       │       │   │   ├── Error.vue
│       │       │   │   ├── Widget.vue
│       │       │   │   └── error
│       │       │   │       ├── Deny.vue
│       │       │   │       ├── Error.vue
│       │       │   │       └── NotFound.vue
│       │       │   ├── vuetify
│       │       │   │   ├── config.js
│       │       │   │   └── index.js
│       │       │   └── workflow
│       │       │       ├── DeleteDialog.vue
│       │       │       ├── List.vue
│       │       │       ├── NewEditSheet.vue
│       │       │       ├── Table.vue
│       │       │       ├── TableFilterDialog.vue
│       │       │       ├── WorkflowParametersInput.vue
│       │       │       ├── api.js
│       │       │       └── store.js
│       │       ├── test
│       │       │   ├── e2e
│       │       │   │   ├── custom-assertions
│       │       │   │   │   └── elementCount.js
│       │       │   │   ├── nightwatch.conf.js
│       │       │   │   ├── runner.js
│       │       │   │   └── specs
│       │       │   │       └── test.js
│       │       │   └── unit
│       │       │       ├── jest.conf.js
│       │       │       ├── setup.js
│       │       │       └── specs
│       │       │           └── HelloWorld.spec.js
│       │       └── vue.config.js
│       ├── storage
│       │   ├── __init__.py
│       │   ├── models.py
│       │   └── service.py
│       ├── tag
│       │   ├── __init__.py
│       │   ├── models.py
│       │   ├── recommender.py
│       │   ├── scheduled.py
│       │   ├── service.py
│       │   └── views.py
│       ├── tag_type
│       │   ├── __init__.py
│       │   ├── models.py
│       │   ├── service.py
│       │   └── views.py
│       ├── task
│       │   ├── __init__.py
│       │   ├── flows.py
│       │   ├── models.py
│       │   ├── scheduled.py
│       │   ├── service.py
│       │   └── views.py
│       ├── team
│       │   ├── __init__.py
│       │   ├── models.py
│       │   ├── service.py
│       │   └── views.py
│       ├── term
│       │   ├── __init__.py
│       │   ├── models.py
│       │   ├── scheduled.py
│       │   ├── service.py
│       │   └── views.py
│       ├── ticket
│       │   ├── __init__.py
│       │   ├── models.py
│       │   └── service.py
│       └── workflow
│           ├── __init__.py
│           ├── flows.py
│           ├── models.py
│           ├── scheduled.py
│           ├── service.py
│           └── views.py
├── tests
│   ├── __init__.py
│   ├── conference
│   │   └── test_conference_service.py
│   ├── conftest.py
│   ├── conversation
│   │   └── test_conversation_service.py
│   ├── document
│   │   └── test_document_service.py
│   ├── event
│   │   └── test_event_service.py
│   ├── factories.py
│   ├── group
│   │   └── test_group_service.py
│   ├── incident_priority
│   │   └── test_incident_priority_service.py
│   ├── incident_type
│   │   └── test_incident_type_service.py
│   ├── individual
│   │   └── test_individual_service.py
│   ├── participant
│   │   └── test_participant_service.py
│   ├── participant_role
│   │   └── test_participant_role_service.py
│   ├── service
│   │   ├── test_service_service.py
│   │   └── test_service_views.py
│   ├── test_cli.py
│   ├── ticket
│   │   └── test_ticket_service.py
│   └── utils
│       ├── __init__.py
│       └── utils.py
└── tox.ini
```

## 列挙型の定義

```py
from enum import Enum

# クラスのオブジェクトに、strとEnumが入っている
# Q: どのような意味がある?
class Hoge(str, Enum):
    # 変数はスネークケース、値はキャメルケースになっている
    fuga = "Fuga"
    bar = "Bar"
    hoge_fuga = "HogeFuga"
```

## ログの取り方

- TODO: 公式のDocを見て、基本的な使い方を学ぶ

```py
import logging

# Q: __file__とは?
# 特殊メソッドように見える
log = logging.getLogger(__file__)

class Foo:

    def __init__(self):
        pass

    def hoge(self, messages, arguments):
        # ベタ打ちの文字だけでなく、変数も出力できる
        # 出力レベルを制御できそう
        # Q: debug, infoの使い分けはどうしている?
        # Q: 他にも種類がある?
        # Q: 文字列の最初に書いているfは特別な意味を持っている?
        log.debug(
            f"TODO: Write messages {messages} and Arguments: {arguments}."
        )

    def bar(self):
        if not fuga:
            log.info(
                "TODO: Write a message."
            )
        else:
            pass
```

## 項目

## 疑問点

+ src/dispatch/static/dispatchに、フロントエンドのソースコードが入っているが、なぜこのような構成にしているのか?

+ SQLAlchemy modelsとPydantic modelsが混在しているのはなぜ?

## 感想

+ 。

+ 。

## 抽象化

+ 。

+ 。

## 応用

+ 。

+ 。


## treeコマンドのメモ

```terminal
<!-- 現在のディレクトリツリーを出力 -->
tree.

<!-- ディレクトリを指定 -->
tree ./hoge

<!-- フォルダ名のみ出力 -->
tree -d .
```

## See

https://github.com/Netflix/dispatch

treeコマンド
http://mama.indstate.edu/users/ice/tree/tree.1.html
https://qiita.com/hanlio/items/e3472f0b6e87ccb6f9ff
