---
layout: post
title: MongoEngine
categories:
  - Linux
description: MongoEngine
keywords: 'mongo, mongoengine'
comments: true
---



## MongoEngine

### 一、安装

```
pip install mongoengine
```

### 二、基础环境设置

1，NSTALLED\_APPS 中加入mongoengine应用

```
INSTALLED_APPS = (
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'magazines',
    'mongoengine',
)
```

2， 设置mongo数据库

```
MONGODB_DATABASES = {
    "default": {
        "name": "test",
        "host": '127.0.0.1',
        "tz_aware": True, # 设置时区
    },
}
```

3，修改数据库默认选项

```
DATABASES = {

        'default': {
        'ENGINE': 'django.db.backends.dummy',
        # 'NAME': os.path.join(BASE_DIR, 'db.sqlite3'),
        }
}
```

4，连接数据库

```
from maongoengine import connect
connect('Magazines', host='127.0.0.1', port=27017)
```

5，创建models

```
import datetime
from mongoengine import *

class Users(Document):

    meta = {

        'collection':'user_data',  # 集合名称
    }

    # 设置数据库字段属性
    user_id = SequenceField(required=True, primary_key=True)  # 主键
    user_name = StringField()  # 字符串类型
    user_pwd = StringField()
    user_date = DateTimeField(default=datetime.datetime.now())  # 日期类型
    user_real_name = StringField()
    user_gender = StringField()
    user_tel = StringField()
    user_uuid = StringField()
```

### 三、数据的增、删、改、查

1，增

```
from models import Users

users = Users()
users.user_name = 'Kuture'
users.user_pwd = 'Password'
users.user_real_name = 'Mr.Li'
users.user_gender = 'male'
users.user_tel = '000000'
users.user_uuid = 'abcdefg'
users.save()
```

2，删

```
from models import Users

users = Users()
users.delete(user_name='Kuture')
users.save()
```

3，改

```
from models import Users

users = Users()
users.update(user_name='Kuture') = 'Austin.Kuture'
users.save()
```

4，查

```
users = Users.objects(user_name='Kuture)[0]
myname = users.user_name
```



