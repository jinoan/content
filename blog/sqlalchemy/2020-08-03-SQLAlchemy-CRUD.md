---
title: "SQLAlchemy CRUD"
date: 2020-08-03T22:06:32+09:00
draft: false
tags: ["sqlalchemy"]
category: "sqlalchemy"
---



# models.py 만들기

SQLAlchemy로 DB를 사용하기 위해 DB의 테이블 객체를 생성해야 한다.
flask-sqlacodegen으로 쉽게 생성 가능.



### flask-sqlacodegen 설치

```bash
$ pip3 install flask-sqlacodegen
```



### models.py 생성 (PostgreSQL 사용시)

```bash
$ flask-sqlacodegen "postgresql://{아이디}:{패스워드}@{DB호스트}:{포트번호}/{DB이름}" --flask > models.py
```



### 테이블 객체의 형태

```python
class Table(db.Model):
    __tablename__ = 'tables'

    table_pkey = db.Column(db.Integer, primary_key=True, server_default=db.FetchedValue())
    tname = db.Column(db.String(10), nullable=False)
    tmaker = db.Column(db.ForeignKey('makers.mname', ondelete='CASCADE'))

    maker = db.relationship('Maker', primaryjoin='Table.tmaker == Maker.mname', backref='tables')
```



# Session 연결

```python
import models  # models.py
from sqlalchemy import create_engine
from sqlalchemy.orm import sessionmaker

engine = create_engine('postgresql://{아이디}:{패스워드}@{DB호스트}:{포트번호}/{DB이름}')
Session = sessionmaker(bind=engine)
session = Session()
```



# CRUD

### Select

```python
# select * from tables;
res_all = session.query(models.Table).all()
for rec in res_all:
    print(f'tname: {rec.tname}, tmaker: {rec.tmaker}')

# select * from tables where tname = 'aaa';
res_aaa = session.query(models.Table).filter_by(tname='aaa').first()
# .filter()를 사용해도 됨
res_aaa = session.query(models.Table).filter(models.Table.tname == 'aaa').first()
print(res_aaa.tmaker)
```



### Insert

```python
# insert into tables(tname, tmaker) values ('bbb', 'jinoan');
data = models.Table(tname='bbb', tmaker='jinoan')
session.add(data)
session.commit()
```



### Update

```python
# update tables set tname = 'ccc' where tname = 'bbb';
res_bbb = session.query(models.Table).filter_by(tname='bbb').first()
res_bbb.tname = 'ccc'
session.commit()
```



### Delete

```python
# delete from tables where tname = 'ccc';
session.query(models.Table).filter_by(tname='ccc').delete()

# commit하지 않고 되돌리고 싶을 때
session.rollback()

# delete를 다음처럼 할 수도 있다.
res_ccc = session.query(models.Table).filter_by(tname='ccc').first()
session.delete(res_ccc)

session.commit()
```



# 그 외...

### Join

```python
res_all = session.query(models.Table).join(models.Maker).all()

# reference 값이 무엇인지 정확히 명시하려면
res_all = session.query(models.Table)\
	.select_from(models.Table)\
    .join(models.Maker, models.Table.tmaker==models.Maker.mname).all()
```



### Pandas data frame으로 결과 불러오기

```python
import pandas as pd

query = session.query(models.Table)
df = pd.read_sql(query.statement, session.bind)
print(df)
```

