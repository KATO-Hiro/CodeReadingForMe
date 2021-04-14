# Pydanticを利用した実装

## Pydanticとは?

## 実装方法

```py
from pydantic import BaseModel

# BaseModelを継承
class Item(BaseModel):
    # 属性と型を書く
    # 補完や型チェックの恩恵を受けられる
    name: str
    price: float
    is_offer: Optional[bool] = None # 任意の値?と初期値を指定することもできる


# 引数に自分で定義したモデルの型を指定することもできる
# 自動的にJSONに変換される
@app.put("/items/{item_id}")
def update_item(item_id: int, item: Item):
    return {"item_name": item.name, "item_id": item_id}
```

## See

https://github.com/samuelcolvin/pydantic/
