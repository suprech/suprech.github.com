---
layout: post
title:  "python 1"
date:   2018-02-02
categories: python_web_programming
tags: python flask web
comments: true
---

## 개발환경 세팅
우선, winstarmall-backend레포지토리에 requirements.txt가 있는데, 이것들을 한번에 설치하도록 한다. 다음의 명령어로 설치할 수 있다.
```pip install -r requirements.txt```

### 1. virtualenvwrapper 설치하기
virtualenv를 깔고, virtualenvwrapper를 설치해야한다.
```pip install virtualenvwrapper``` 명령어를 통해 설치한다.
그리고, 다음 코드를 ```.zprofile```에 추가한다.

```sh
# virtualenvwrapper setting
export VIRTUALENVWRAPPER_PYTHON=/usr/local/bin/python3
export WORKON_HOME=$HOME/.virtualenvs
export PROJECT_HOME=$HOME/Devel
export VIRTUALENVWRAPPER_SCRIPT=/Users/ych/.local/bin/virtualenvwrapper.sh
source /Users/ych/.local/bin/virtualenvwrapper_lazy.sh
#export VIRTUALENVWRAPPER_SCRIPT=/usr/local/bin/virtualenvwrapper.sh
#source /usr/local/bin/virtualenvwrapper_lazy.sh

alias mkvirtualenv3="mkvirtualenv -p/usr/local/bin/python3"
alias mkvirtualenv2="mkvirtualenv -p/usr/local/bin/python2"
```


#### virtualenv 사용하기
일반적으로 virtualenv를 이용할때는 다음과 같이 쓴다. ```$ virtualenv [가상환경명]```
그러면 명령어를 입력한 폴더에, 해당 가상환경 정보를 저장하는 폴더가 생성된다.
방금 만든 가상환경으로 설정을 할 때는 ```$ source [가상환경명]/bin/activate```를 터미널에 입력하면 해당 가상환경으로 실행이 가능하다.
가상환경에서 빠져나오려면 ```$ deactivate```를 입력하면 된다.


#### virtualenvwrapper 사용하기
위와같은 과정을 좀 더 편하게 해주는 툴이다. 다음의 명령어로 사용할 수 있다.
(zprofile에 alias로 설정한것을 반영한다.)
```$ mkvirtualenv3 [가상환경명]```

이렇게 하면, 어디서든 해당 가상환경에 접근할 수 있다. 위의 기본 virtualenv는 해당 폴더에서 실행 해 주어야 했었는데, 이와 대조되는 모습이다. 가상환경을 실행시키는 명령어는 ```$ workon [가상환경명]```이다.




### 2. pytest 설치하기
#### TDD 코딩 방법론
TDD란, Test-driven development의 약자로서 정확한 프로그래밍 목적을 디자인 단계에서 반드시 미리 정의하는 개발방법을 말한다.

요약하자면, 일단 test case를 작성해보고 대략적인 api의 흐름을 구성한다.
그리고 그것에 맞게 api를 코딩하고, pytest를 통해 오류가 나는지 안나는지 점검한다.

> 즉, test먼저, api나중!



#### pytest
pytest는 python에서 TDD방법론으로 개발을 할 수 있도록 도와주는 툴이다.


#### python의 assert 구문
백문이 불여일타. 다음의 코드를 보자.
assert 이후에 오는 문장이 참인지 아닌지를 판단하는것이다. 참이면 테스트에 통과한것이며, 거짓이면 AssertionError가 난다.


```python
from ..helper import url


def test_search(f_wsgi, f_client):
    res = f_client.get(url(f_wsgi, 'status.ping'))
    assert res.status_code == 200  # HTTP에서 200은 success
    assert res.data.decode('utf-8') == 'pong'
```


### 3. Database 설치하기
#### postgresapp 설치하기
이번 프로젝트에선 MySQL이 아닌 PostgreSQL을 사용한다. 
PostgreSQL은 객체-관계형 데이터베이스 관리 시스템의 일종이다. BSD 허가권으로 배포되며 오픈소스 개발자 및 관련 회사들이 개발에 참여하고 있다.
MySQL로 치자면 MySQL서버를 다운받는과정이다. 한번 실행하고, start버튼을 누른 뒤로는 꺼도 된다.

다음의 경로에서 다운받도록 한다.
[postgresapp 링크](https://postgresapp.com/)


#### postico 설치하기
Postico is a database client. Postico can connect to a local PostgreSQL server running on your Mac, or to remote servers running on a different computer.
실질적으로 DB를 만들고, 상황 확인할때 사용한다. 제어용임.

[postico 링크](https://eggerapps.at/postico/)



#### alembic
SQLAlchemy기반의 데이터베이스 마이그레이션 도구이다.
Database를 위한 git이라고 보면 된다. pip로 설치한다. ```$ pip install alembic```
그리고 다음의 명령어를 입력한다.
```$ alembic upgrade head```

[참고 url](https://blog.outsider.ne.kr/1143)


### 4. 프로젝트 구성도




### 그 외 기타 사항들..
#### wsgi란 무엇인가
wsgi란 Web Server Gateway Interface의 약자이다. 웹 서버가 웹 어플리케이션과 어떻게 통신을 할지를 규정하는 python standard이다.

#### 블루프린트란?
플라스크는 어플리케이션 컴포넌트를 만들고 어플리케이션 내부나 어플리케이션간에 공통 패턴을 지원하기 위해 블루프린트(blueprint) 라는 개념을 사용한다.
블루프린트는 보통 대형 어플리케이션이 동작하는 방식을 단순화하고 어플리케이션의 동작을 등록하기 위한 플라스크 확장에 대한 중앙 집중된 수단을 제공할 수 있다.
Blueprint 객체는 Flask 어플리케이션 객체와 유사하게 동작하지만 실제로 어플리케이션은 아니다. 다만 어플리케이션을 생성하거나 확장하는 방식에 대한 블루프린트 이다.


[참고 url](http://flask-docs-kr.readthedocs.io/ko/latest/blueprints.html)


#### api
API(Application Programming Interface, 응용 프로그램 프로그래밍 인터페이스)는 응용 프로그램에서 사용할 수 있도록, 운영 체제나 프로그래밍 언어가 제공하는 기능을 제어할 수 있게 만든 인터페이스를 뜻한다. 주로 파일 제어, 창 제어, 화상 처리, 문자 제어 등을 위한 인터페이스를 제공한다.


#### ag란?
agrep같은, recursive하게 PATH의 pattern을 찾는 프로그램이다. mac에선 brew로 설치한다.
```$ brew install ag```


#### UUID type이란?
범용 고유 식별자(汎用固有識別子, 영어: universally unique identifier, UUID)는 소프트웨어 구축에 쓰이는 식별자 표준이다.
데이터베이스에서 id는 Primary Key로 사용되므로, 고유한 값을 가져야 하는데 이를 위해 사용한다.


#### python에 실행권한을 줘서 실행시키기
이런방식으로 실행하기 위해선, 이 파일의 권한이 executable이어야 한다. 다음 문단을 참고하도록 하자.

python 코드가 다음과 같을때(파일명은 asd.py라 하자.)
```python
#! /usr/bin/env python3
import os

if os.environ.get("MODE") == "PROD":
    print("hello")

else:
    print("aaa")
```

os.environ에 PROD를 넘겨주려면, 터미널에서 다음의 명령어로 실행을 한다.
```$ MODE=PROD ./asd.py``` 이 경우 결과는 hello가 나올것이다.
```$ ./asd.py``` 이 경우 결과는 aaa가 나올것이다.


#### ```.py``` 파일을 executable하게 만들기 위해, ```chmod``` 쓰는법
기본적으로 ```.py```파일을 만들면 ```-rw-r--r--```로 만들어지는데, 이를 실행가능하게 해야한다.
다음의 명령을 터미널에 입력한다.
```chmod +x ./[파일명].py```
그러면 퍼미션은 다음과 같이 바뀐다. ```-rwxr-xr-x```


### 02/02 오늘 한 일 정리


### Todo
* SQL Alchemy 공식문서 읽어보기. [공식문서 링크](http://docs.sqlalchemy.org/)
* signup api 추가하기
* signin api 추가하기 (password체크 True/False반환하도록.)
* branch만든 뒤 pull request하기