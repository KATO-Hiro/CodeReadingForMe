# Python Types Intro

- type hintsがサポートされている
- FastAPIを利用する上で、必要最小限の説明に限定される

- メリット
  - 補完
  - 型チェック

```py
# 変数: typeの形式で書く
def get_full_name(first_name: str, last_name: str):

    full_name = first_name.title() + " " + last_name.title()
    return full_name


print(get_full_name("john", "doe"))

```

- 利用できる型
  - str, int, float, bool, bytes
  - ジェネリック型のdict, list, set, tupleも利用できる
  - classも

```py
from typing import List


# モジュールをインポート + []で内部の型も指定できる
# この例だとstrのリスト
def process_items(items: List[str]):
    for item in items:
        print(item)

from typing import Set, Tuple


# Tupleだと3つ、かつ、異なる型を入れることもできる
def process_items(items_t: Tuple[int, int, str], items_s: Set[bytes]):

    return items_t, items_s


from typing import Dict



# Dictは、dictの[key、value]の順番で指定
def process_items(prices: Dict[str, float]):

    for item_name, item_price in prices.items():
        print(item_name)
        print(item_price)



from typing import Optional


# 任意のケースを指定
# デフォルト値も指定できる
def say_hi(name: Optional[str] = None):

    if name is not None:
        print(f"Hey {name}!")
    else:
        print("Hello World")


# クラスも指定できる
class Person:

    def __init__(self, name: str):

        self.name = name


def get_person_name(one_person: Person):
    return one_person.name

```

- Pydantic models
  - クラスの属性と型を宣言

```py
from datetime import datetime
from typing import List, Optional

from pydantic import BaseModel


class User(BaseModel):
    id: int
    name = "John Doe"
    signup_ts: Optional[datetime] = None
    friends: List[int] = []


external_data = {
    "id": "123",
    "signup_ts": "2017-06-01 12:22",
    "friends": [1, "2", b"3"],
}
user = User(**external_data)
print(user)
# > User id=123 name='John Doe' signup_ts=datetime.datetime(2017, 6, 1, 12, 22) friends=[1, 2, 3]
print(user.id)
# > 123
```
