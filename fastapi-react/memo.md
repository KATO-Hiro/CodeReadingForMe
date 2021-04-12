# FastAPI + Reactの使用技術・構成の理解を深める

## 現状

+ READMEの通りに、プロジェクトを作成。

+ FastAPIのチュートリアルを一通り読んだ。
+ Reactの入門書を一読し、簡単なビューを作成した。

## 目標

+ 使われている技術の選定理由を説明できるようにする。

+ 簡単なappを作って、公開する。

## 技術選定の理由

+ FastAPI
  + Pythonでは比較的軽量かつ高速なFWと聞いた
  + Flaskの代替手段となりそう
  + Pythonは、汎用言語&パラメータの推定を行うときのライブラリが豊富なため

+ React
  + あるサービスがVueを採用しており、動作がもっさりしているように思われたため
  + 描画速度に定評があるという記事を見た

## README

+ セキュリティ
  + productionでは、secret_keyを以下のコマンドで生成、もしくは、`backend/app/core/security.py`の`SECRET_KEY`を直接編集

```terminal
openssl rand -hex 32
```

## ディレクトリ構成

### backend (FastAPI)

```md
.
├── Dockerfile
├── alembic.ini
├── app
│   ├── __init__.py
│   ├── alembic
│   │   ├── README
│   │   ├── __init__.py
│   │   ├── env.py
│   │   ├── script.py.mako
│   │   └── versions
│   │       └── 91979b40eb38_create_users_table.py
│   ├── alembic.ini
│   ├── api
│   │   ├── __init__.py
│   │   ├── api_v1
│   │   │   ├── __init__.py
│   │   │   └── routers
│   │   │       ├── __init__.py
│   │   │       ├── auth.py
│   │   │       ├── tests
│   │   │       │   ├── __init__.py
│   │   │       │   ├── test_auth.py
│   │   │       │   └── test_users.py
│   │   │       └── users.py
│   │   └── dependencies
│   │       └── __init__.py
│   ├── core
│   │   ├── __init__.py
│   │   ├── auth.py
│   │   ├── celery_app.py
│   │   ├── config.py
│   │   └── security.py
│   ├── db
│   │   ├── __init__.py
│   │   ├── crud.py
│   │   ├── models.py
│   │   ├── schemas.py
│   │   └── session.py
│   ├── initial_data.py
│   ├── main.py
│   ├── tasks.py
│   └── tests
│       ├── __init__.py
│       ├── test_main.py
│       └── test_tasks.py
├── conftest.py
├── pyproject.toml
└── requirements.txt
```

## 疑問点

+ 使われている技術とその役割がイマイチ分かっていない
  + Celery: 監視ツール?
    + Redis
    + Flower
  + Alembic: マイグレーションを行うツールという理解
  + Prettier/ESLint
  + Nginx: バックエンドとフロントエンドのプロジェクトを同じポートで利用できるようにしているらしい

+ Deployは、どのような方法を利用する?
  + Docker in Swarm Modeとは?
    + 料金プランはどうなっている?
    + どこかのWebサイトでは、サポートを終了というのを見た = そこまで流行っていない?
  + k8sとの関係は?

```md
it may be easiest to use Docker in Swarm Mode with an Nginx main load balancer proxy handling automatic HTTPS certificates, using the ideas from DockerSwarm.rocks.

Please refer to DockerSwarm.rocks to see how to deploy such a cluster easily. You will have to change the Traefik examples to Nginx or update your docker-compose file.
```

+ 他の手段
  + Deta
    + ◯: FastAPIのプロジェクトなら、学習コストもほぼなくデプロイできる
    + △: RDBには対応していない
  + Vercel
    + ◯: Next.jsプロジェクトなら、とても簡単にデプロイできる
    + △: FastAPI + RDB + Next.jsプロジェクトの例は、かなり少ない & 練習用のプロジェクトしか見たことがない
  + Heroku
    + ◯: デプロイは比較的簡単な部類だと思われる
    + △: フリープランだと、稼働時間やデータ量に制約がある

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
