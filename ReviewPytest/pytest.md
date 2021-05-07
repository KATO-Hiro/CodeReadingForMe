# mypyのチートシート

## WHAT

+ 型チェックを行う

## インストール

```md
pip install mypy
```

## 実行

```md
mypy program.py
```

## Getting startsのサンプル

```py
# 変数名: type
# 戻り値 → type:
def greeting(name: str) -> str:
    return 'Hello ' + name

# 戻り値がない場合は、Noneを指定
def p() -> None:
    print('hello')

# デフォルト値を指定することもできる
# 変数: 型 = デフォルト値
def greeting(name: str, excited: bool = False) -> str:
    message = 'Hello, {}'.format(name)
    if excited:
        message += '!!!'
    return message

# *argsや**kwargsも使える
def stars(*args: int, **kwargs: float) -> None:
    # 'args' has type 'Tuple[int, ...]' (a tuple of ints)
    # 'kwargs' has type 'Dict[str, float]' (a dict of strs to floats)
    for arg in args:
        print(arg)
    for key, value in kwargs:
        print(key, value)

# 以下のモジュールをimportすれば、List型も指定できる
from typing import List

# 変数: List[type]と指定
def greet_all(names: List[str]) -> None:
    for name in names:
        print('Hello ' + name)

names = ["Alice", "Bob", "Charlie"]
ages = [10, 20, 30]

greet_all(names)   # Ok!
greet_all(ages)    # Error due to incompatible types


# The typing module
# より柔軟な指定を行う場合は、Iterableを指定する
# tuple, set, custom iterableも可能
from typing import Iterable

def greet_all(names: Iterable[str]) -> None:
    for name in names:
        print('Hello ' + name)

# 特定の型を複数指定する場合は、Unionを使う
from typing import Union

# Union[type1, type2]のように指定
def normalize_id(user_id: Union[int, str]) -> str:
    if isinstance(user_id, int):
        return 'user-{}'.format(100000 + user_id)
    else:
        return user_id

# stringsとNoneのみを指定するような形式の場合は、以下の書き方がスマート
# Union[str, None]のエイリアスになっている
from typing import Optional

# Optional[type] = None
def greeting(name: Optional[str] = None) -> str:
    # Optional[str] means the same thing as Union[str, None]
    if name is None:
        name = 'stranger'
    return 'Hello, ' + name


# Local type inference
def nums_below(numbers: Iterable[float], limit: float) -> List[float]:
    output = []
    for num in numbers:
        if num < limit:
            output.append(num)
    return output

# 型指定が必要
my_global_dict = {}  # Error: Need type annotation for 'my_global_dict'

# 変数: 型[type1, type2]
# If you're using Python 3.6+
my_global_dict: Dict[int, float] = {}
```

## チートシート

```py
# Variables
# This is how you declare the type of a variable type in Python 3.6
# 変数名: 型 = 初期値
age: int = 1

# You don't need to initialize a variable to annotate it
a: int  # Ok (no value at runtime until assigned)

# The latter is useful in conditional branches
child: bool
if age < 18:
    child = True
else:
    child = False


# Built-in types
from typing import List, Set, Dict, Tuple, Optional

# For simple built-in types, just use the name of the type
x: int = 1
x: float = 1.0
x: bool = True
x: str = "test"
x: bytes = b"test"

# For collections, the name of the type is capitalized, and the
# name of the type inside the collection is in brackets
x: List[int] = [1]
x: Set[int] = {6, 7}

# Same as above, but with type comment syntax
x = [1]  # type: List[int]

# For mappings, we need the types of both keys and values
x: Dict[str, float] = {'field': 2.0}

# For tuples of fixed size, we specify the types of all the elements
x: Tuple[int, str, float] = (3, "yes", 7.5)

# For tuples of variable size, we use one type and ellipsis
x: Tuple[int, ...] = (1, 2, 3)

# Use Optional[] for values that could be None
x: Optional[str] = some_function()
# Mypy understands a value can't be None in an if-statement
if x is not None:
    print(x.upper())
# If a value can never be None due to some invariants, use an assert
assert x is not None
print(x.upper())


# Functions
from typing import Callable, Iterator, Union, Optional, List

# This is how you annotate a function definition
def stringify(num: int) -> str:
    return str(num)

# And here's how you specify multiple arguments
def plus(num1: int, num2: int) -> int:
    return num1 + num2

# Add default value for an argument after the type annotation
def f(num1: int, my_float: float = 3.5) -> float:
    return num1 + my_float

# This is how you annotate a callable (function) value
x: Callable[[int, float], float] = f

# A generator function that yields ints is secretly just a function that
# returns an iterator of ints, so that's how we annotate it
def g(n: int) -> Iterator[int]:
    i = 0
    while i < n:
        yield i
        i += 1

# You can of course split a function annotation over multiple lines
def send_email(address: Union[str, List[str]],
               sender: str,
               cc: Optional[List[str]],
               bcc: Optional[List[str]],
               subject='',
               body: Optional[List[str]] = None
               ) -> bool:
    ...

# Q: 何のためにある?
# An argument can be declared positional-only by giving it a name
# starting with two underscores:
def quux(__x: int) -> None:
    pass

quux(3)  # Fine
quux(__x=3)  # Error
```

## See

https://mypy.readthedocs.io/en/stable/getting_started.html
