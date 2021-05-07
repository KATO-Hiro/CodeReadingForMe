# SQLAlchemyに関するチートシート

## 1対1

```py
from sqlalchemy import Column, ForeignKey, Integer, String
from sqlalchemy.orm import relationship


class Parent(Base):
    __tablename__ = 'parent'
    id = Column(Integer, primary_key=True)

    # 関連の定義
    # relationship("関連のあるクラス名", uselist=False, back_populates="クラス名")
    # 「多」になる側にuselist=Falseを付与する
    child = relationship("Child", uselist=False, back_populates="parent")


class Child(Base):
    __tablename__ = 'child'
    id = Column(Integer, primary_key=True)

    # 外部キーの定義
    parent_id = Column(Integer, ForeignKey('parent.id'))
    parent = relationship("Parent", back_populates="child")
```

```py
from sqlalchemy.orm import backref

class Parent(Base):
    __tablename__ = 'parent'
    id = Column(Integer, primary_key=True)
    child_id = Column(Integer, ForeignKey('child.id'))
    child = relationship("Child", backref=backref("parent", uselist=False))
```

### 疑問点

+ backpopulatesとbackrefの違いは?
  + backrefは、もう一方のプロパティが自動的に生成される
  + backpopulatesは、backrefと似ているが、プロパティが自動的に生成されないため、明示的に指定する必要がある

## Object Relational relationship

```py
from sqlalchemy.orm import relationship
from sqlalchemy.ext.declarative import declarative_base

Base = declarative_base()

class User(Base):
    __tablename__ = 'user'
    id = Column(Integer, primary_key=True)
    name = Column(String)
    addresses = relationship("Address", backref="user")

class Address(Base):
    __tablename__ = 'address'
    id = Column(Integer, primary_key=True)
    email = Column(String)
    user_id = Column(Integer, ForeignKey('user.id'))

u1 = User()
a1 = Address()
print(u1.addresses)
print(a1.user)

u1.addresses.append(a1)
print(u1.addresses)
print(a1.user)
```

### See

https://docs.sqlalchemy.org/en/14/orm/basic_relationships.html
