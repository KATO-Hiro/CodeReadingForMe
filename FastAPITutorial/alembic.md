# Alembic に関する使い方

## Create a Migration Script

```terminal
# alembic revision -m "table name"
# Railsのマイグレーションファイルの作成に近い
$ alembic revision -m "create account table"
```

```py
# xxxx_create_account_table.py
"""create account table

Revision ID: 1975ea83b712
Revises:
Create Date: 2011-11-08 11:40:27.089406

"""

# revision identifiers, used by Alembic.
revision = '1975ea83b712'
down_revision = None
branch_labels = None

from alembic import op
import sqlalchemy as sa

# TODO: upgrade()とdowngrade()を実装する
def upgrade():
    pass

def downgrade():
    pass
```

```py
def upgrade():
    # SQLAlchemyを利用したモデル定義とよく似ている
    # 属性をsa.Columnの()内に記述している
    # 文字列の長さの指定も必要そう
    op.create_table(
        'account',
        sa.Column('id', sa.Integer, primary_key=True),
        sa.Column('name', sa.String(50), nullable=False),
        sa.Column('description', sa.Unicode(200)),
    )

def downgrade():
    # 該当するテーブルを削除
    op.drop_table('account')
```

ドキュメント
https://alembic.sqlalchemy.org/en/latest/ops.html#ops

## Running our First Migration

```terminal
# headで、revision番号を指定できるが、もっともシンプルな方法
# 特定の番号や相対的な番号でも指定できる
$ alembic upgrade head
```

- タイミング
  - プロジェクト全体として初めて実行するとき
  - テーブルを作成・変更・削除したとき

## Running our Second Migration

```terminal
$ alembic revision -m "Add a column"
```

```py
"""Add a column

Revision ID: ae1027a6acf
Revises: 1975ea83b712
Create Date: 2011-11-08 12:37:36.714947

"""

# revision identifiers, used by Alembic.
revision = 'ae1027a6acf'
down_revision = '1975ea83b712'

from alembic import op
import sqlalchemy as sa

def upgrade():
    op.add_column('account', sa.Column('last_transaction_date', sa.DateTime))

def downgrade():
    op.drop_column('account', 'last_transaction_date')
```

```terminal
$ alembic upgrade head
```

## Getting Information

```terminal
# 現在の状態を確認
$ alembic current

# 履歴も確認できる
alembic history --verbose

# 範囲を指定することも可能
# -r[start]:[end]
# -rがオプション、startとendで対象を指定。空でもok
$ alembic history -r1975ea:ae1027
```

## Downgrading

```terminal
# 元に戻すこともできる
# baseは、プロジェクトの初期状態
$ alembic downgrade base

# 何もないところから、最新の状態まで復元することも可能
$ alembic upgrade head
```

## Next Steps

- Auto Generating Migrations

## See

https://alembic.sqlalchemy.org/en/latest/tutorial.html#create-a-migration-script
