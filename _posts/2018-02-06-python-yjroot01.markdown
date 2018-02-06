---
layout: post
title:  "python 2"
date:   2018-02-06
categories: python_web_programming
tags: python flask web sqlalchemy
comments: true
---

## git 사용법 2 : fetch와 rebase
[참고 url](http://noritersand.tistory.com/86)

### 상황가정
상황을 가정하면 다음과 같다.
내가 Pull Request를 한 사항이 이미 Merge가 되었고, 그 사이에 다른 변경사항들이 master branch에 적용되었을때 어떻게 해야하는가?

### fetch란?
일단 fetch명령을 통해 변경사항을 가져온다.
fetch는 원격저장소에 대해 실행하는 명령이다. 리모트 저장소의 데이터를 로컬 저장소로 다운로드한다. 서버의 데이터를 모두 가져오지만 머지는 생략한다.

```$ git fetch```

이제 가져온 변경사항을 토대로, master 브랜치를 rebase해준다.

### rebase란?
rebase란, 현재 브랜치를 다른 브랜치에 머지. merge 명령이 두 브랜치의 최종결과만을 기준으로 머지한다면 리베이스는 브랜치의 변경사항을 순서대로 다른 브랜치에 적용하며 머지한다. 저장소의 커밋 로그와 이력을 한 줄로 정리해주기 때문에 보통 완료된 브랜치를 마스터에 머지할 때 사용한다.
```$ git rebase origin/master```

그 다음, 또다시 변경했던 사항들을 add하고 commit한다.

```$ git add *```

```$ git commit -m "blah blah"```

커밋한 사항을 원격저장소에 강제 push한다.
```$ git push -f```

signup 브랜치를 origin 원격저장소에 push한다.
```$ git push --set-upstream origin signup```

이후 github에 가보면 pull request가 떠 있는것을 볼 수 있고, Pull Request를 요청한뒤 merge하면 된다.
이후 내 컴퓨터(맥)로 다시 돌아와서는 master로 checkout한 뒤 pull을 해주면 된다.

<br>
<br> 

## DB Migration
### alembic 이용하기
웹서비스를 운영하다 보면, 필히 DB의 구조를 변경해야할 일이 생긴다. 이 때 이용하는것이 DB migration이다. 

1. 우선 우리 예제의 경우 model로 들어가서 코드를 수정한다.

다음은 item.py의 Item class를 수정한 예 이다.

#### 수정전

```python
class Item(Base):
    __tablename__ = 'item'

    ''' 중간 생략 '''

    original_price = Column(Numeric(16, 8), nullable=True)
```

#### 수정후

original_price를 wholesale_price로 바꾼것이다.

```python
class Item(Base):
    __tablename__ = 'item'

    ''' 중간 생략 '''

    wholesale_price = Column(Numeric(16, 8), nullable=True)
```

2. alembic 명령어 실행

alembic명령어를 실행할땐 항상 alembic.ini가 있는 경로에서 수행해야 한다. 
이 파일은 우리 프로젝트 같은 경우 최상위 폴더에 존재한다.

```$ alembic revision --autogenerate -m "수정내용"```

```$ alembic upgrade head```

3. alembic에 의해 생성된 로그파일 수정하기

우리 프로젝트 기준, migrations폴더에 새로운 로그파일이 생성되었을것이다. 이걸 vim으로 열어보자.

기본으로 생성되는 코드는 비효율적인 면이 있기에, 약간의 수정작업을 필요로 한다.

```python
"""change name for item model : original_price to wholesale_price

Revision ID: 7beaf518915d
Revises: aa8c9c18b0d5
Create Date: 2018-02-06 11:25:16.842366

"""
from alembic import op
import sqlalchemy as sa


# revision identifiers, used by Alembic.
revision = '7beaf518915d'
down_revision = 'aa8c9c18b0d5'
branch_labels = None
depends_on = None


def upgrade():
    op.alter_column('item', 'orignal_price', new_column_name='wholesale_price')


def downgrade():
    op.alter_column('item', 'wholesale_price', new_column_name='orignal_price')
```

기본적으로 upgrade()에 수행하는 내용과 downgrade()에 수행하는 내용은 서로 반대가 된다. 

<br>
<br>


## DB Update 쿼리 날리기
SQL처럼 쿼리를 날릴 필요는 없다. 다음 예제를 보고 이해하자.

```python
def set_item(db, data, item):
    if 'name' in data:
        item.name = data['name']

    if 'price' in data:
        item.price = data['price']

    db.add(item)
    db.commit()

    result = dict()
    result['name'] = item.name
    if item.price:
        result['price'] = float(item.price)
    result['id'] = str(item.id)

    return jsonify(result)
```

```add_item()``` 과 ```set_item()```측에서 이놈을 호출해서 쓰는데, 위 코드의 위 8줄이 DB처리를 위한 부분이고, 나머지 부터 return까지는 그냥 client에 json데이터 전달을 위한 부분일 뿐이다

어떤 db를 넣을지, 사용자로부터 어떤 data를 입력하라고 json 데이터를 전달받았는지, 그리고 어떤 객체에 대해 작업을 수행해야하는지를 인자로 전달받는 함수이다.

다음을 보자
```python
if 'name' in data:
    item.name = data['name']

if 'price' in data:
    item.price = data['price']

db.add(item)
db.commit()
```

```add_item()```의 경우 빈 ```Item()```객체를 전달받고, ```set_item()```의 경우 select query를 날려서 얻은 결과 값을 전달해준다.
따라서, ```add_item()```의 경우 새로운 정보를 추가해주는 것이 되며, ```set_item()```의 경우 기존의 정보를 수정하는 부분이 되는게 다음의 코드이다.

```python
if 'name' in data:
    item.name = data['name']

if 'price' in data:
    item.price = data['price']
```

그리고, 다음 부분의 경우는
```python
db.add(item)
db.commit()
```

add를 또 하는건 상관없기에, ```add_item()```과 ```set_item()```에서 둘 다 써먹기 위해 ```db.add(item)```을 넣은것이다.

<br>
<br>

## DB 제약조건
특정 조건이 성립하지 않으면 안되게 제약조건을 넣을 수 있다. 다음에 할 예정

<br>
<br>

## HTTP에서, GET, POST, PUT의 차이
* GET : select용
* POST : insert용
* PUT : update용

<br>
<br>

## GET, POST, PUT과 route 
route주소가 같을때, GET, POST, PUT각각 방식이 다르면 route주소가 같아도 된다.
그렇지 않으면 바꿔야한다.

예를들어, 다음의 코드는 모두 POST이고 route주소또한 ```/item```으로 같기에 아래쪽에 선언된 ```update_item()```은 씹히고 ```add_item()```만 수행이 된다.

```python
@app.route('/item', methods=['POST'])
def add_item():
    data = request.get_json()

    with Database() as db:
        new_item = Item()
        res = set_item(db, data, new_item)

    return res


@app.route('/item', methods=['POST'])
def update_item():
    data = request.get_json()

    id = data.get('id')

    with Database() as db:
        update_item = db.query(Item).filter(Item.id == id).one()
        res = set_item(db, data, update_item)

    return res
```

따라서 다음과 같이 수정해야한다. (다른 방법으로는, route주소를 달리 해도 된다.)

```python
@app.route('/item', methods=['POST'])
def add_item():
    data = request.get_json()

    with Database() as db:
        new_item = Item()
        res = set_item(db, data, new_item)

    return res


@app.route('/item', methods=['PUT'])
def update_item():
    data = request.get_json()

    id = data.get('id')

    with Database() as db:
        update_item = db.query(Item).filter(Item.id == id).one()
        res = set_item(db, data, update_item)

    return res
```

<br>
<br>

## fixture
쉽게말해 pytest로 test를 할 때, DB에 이미 몇몇 정보를 넣어놓고 시작할 수 있도록 설정하는 과정이라 할 수 있다.
conftest.py란 파일을 tests/api 폴더에 넣고 시작한다.

```python
from pytest import fixture
from winstar.db import Database
from winstar.models.item import Category

categories = [
    ['clubs', '골프클럽',
     ['drivers', '드라이버'],
     ['woods', '우드',
      ['fairway', '페어웨이'],
      ['utility', '유틸리티']],
     ['ironsets', '아이언 세트']],
    ['bags', '골프백',
     ['caddy', '캐디백'],
     ['boston', '보스턴백']]
]


def insert_category(db, parent, categories):
    order = db.query(Category).filter(Category.parent == parent).count()
    category = Category(order=order,
                        name=categories[0],
                        display=categories[1],
                        parent=parent)
    db.add(category)
    for child in categories[2:]:
        insert_category(db, category, child)


@fixture
def f_categories(f_use_db):
    with Database() as db:
        for category in categories:
            insert_category(db, None, category)
        db.commit()
```

```@fixture```를 박아놓으면, 알아서 pytest가 인식한다. f_use_db의 경우 이 py파일에서 import를 하지 않았지만 이 역시 알아서 해준다.
(f_use_db파일은 상위 폴더의 conftest.py에 존재한다.)


<br>
<br>

## 오늘 한 일
* git 사용법을 추가적으로 배움
* DB Migration에 대해 배움
* item api에서, item을 추가하고 업데이트 하는 기능을 구현
* 과제를 위해, fixture설정하는법을 배움.

<br>
<br>

## 앞으로 해야할일
* (과제) item api에서, category정보가 들어왔을때 이것도 같이 추가해보도록 하자.