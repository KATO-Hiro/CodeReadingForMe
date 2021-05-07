# Pythonプロジェクトに参画して、レビューを受けた項目をメモ

## 現状

+ テスト駆動Pythonを読んで、シンプルなBotを作成したときに少し利用した程度。

+ そもそも、他の人のプロジェクトに初めて参加。しかも、レポジトリの作成者の方は、競技プログラミング界隈での著名人。

## 目標

+ Pythonらしい書き方を身に着ける。
+ 指摘された点は、メモに残して、同じことを言われないようにする。

## 技術選定

+ テストフレームワーク
  + pytest

+ CI
  + GitHub Actions

+ Lint
  + flake8
  + pep8-naming
  + mypy
  + black

## 指摘事項

### ファイル

+ requirements.txt
  + GitHub Actionsで余計なパッケージがインストールされるため不要
+ conftest.py
  + なくても、ローカル環境、GitHub Actionsともに動作する

### テストする項目

+ 何も操作していない時点での挙動
+ 同じ操作を複数回実行したときの挙動
+ 範囲外のindexを指定したときに`AssertionError`を捕捉する

### テストしない項目

+ 該当メソッドと直接関連のないメソッドのテスト
+ 戻り値が保証されていないケースで、確定値を与えて比較する。

### Pytestの書き方

```py
# クラスの定義: Python3では不要
# before
class ClassName(object):
    pass

# after
class ClassName:
    pass


# bool型は、明示的にしてなくても良い
assert foo is True # before
assert foo # after

assert hoge is False # before
assert not hoge # after
```

## 感想

+ レビューを受ける経験がなかったため、とてもありがたい。
  + モダンな記法を教えていただいた
  + テストの項目を抜け漏れなく揃える重要性を痛感
  + 初めて知るツールが多い

+ その反面、プロジェクトのオーナーの負担が大きかったのではないか?

## 抽象化

+ 言語の機能を活用して、シンプルに記述する。
+ (当然ながら、)継続的に最新の情報を取り入れて、書けるようにする。

## 応用

+ 今後のプロジェクトで、教えていただいツールを利用&テンプレート化。
+ レビュー前にセルフチェックを行う。

## See

+ https://github.com/not522/ac-library-python/pull/8
