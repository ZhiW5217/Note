

### 1.在settings.py中创建多数据库连接
```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'HOST': '127.0.0.1',
        'NAME': 'shop_list',
        'PORT': 3306,
        'USER': 'root',
        'PASSWORD': 'qwe123..',
        'ATOMIC_REQUESTS': True,
        'OPTIONS': {
            'autocommit': True,  # 如果不设置此项django默认在查询之前会执行`set autocommit=0`,mysql如果开了set autocommit=0,那么所有的语句一定是在一个事务里
        },
    },
    'task': {
        'ENGINE': 'django.db.backends.mysql',
        'HOST': '127.0.0.1',
        'NAME': 'shop',
        'PORT': 3306,
        'USER': 'root',
        'PASSWORD': 'qwe123..',
        # 'ATOMIC_REQUESTS': True,
        'OPTIONS': {
            'autocommit': True,
        },
    },
    'listing_2020': {
        'ENGINE': 'django.db.backends.mysql',
        'HOST': '127.0.0.1',
        'NAME': 'shop_2020',
        'PORT': 3306,
        'USER': 'root',
        'PASSWORD': 'qwe123..',
        # 'ATOMIC_REQUESTS': True,
        'OPTIONS': {
            'autocommit': True, 
        },
    }
}
```

### 2.在settings.py中添加数据库映射
```python
DATABASE_ROUTERS = ['ListShop.db_router.DatabaseAppsRouter']
DATABASE_APPS_MAPPING = {
    # example:
    # 'app_name':'database_name',
    'demand': 'task',
    'shop_detail_2020': 'listing_2020',   # 创建数据表 使用 python manage.py migrate --database listing_2020
}
```


### 3. 创建db_router.py文件
 - 在项目目录下新建文件 `db_router.py`
```python
#!/usr/local/bin/python3
# -*- coding: utf-8 -*-

"""
@File    : db_router.py
@Author  : different
@Time    : 2021-2-24 11:01
@Desc    : 数据库路由

"""
from django.conf import settings

DATABASE_MAPPING = settings.DATABASE_APPS_MAPPING


class DatabaseAppsRouter(object):
    """
        路由，用于控制不同数据库的模型上的所有数据库操作。

        如果未在settings.DATABASE_APPS_MAPPING中设置应用，则路由器将回退到“默认”数据库。

        设置示例：DATABASE_APPS_MAPPING = {'app1'：'db1'，'app2'：'db2'}
    """

    def db_for_read(self, model, **hints):
        """"将所有读取操作指向特定的数据库"""
        if model._meta.app_label in DATABASE_MAPPING:
            return DATABASE_MAPPING[model._meta.app_label]
        return None

    def db_for_write(self, model, **hints):
        """将所有读取操作指向特定的数据库。"""
        if model._meta.app_label in DATABASE_MAPPING:
            return DATABASE_MAPPING[model._meta.app_label]
        return None

    def allow_relation(self, obj1, obj2, **hints):
        """允许使用相同数据库的应用之间存在任何关系。"""
        db_obj1 = DATABASE_MAPPING.get(obj1._meta.app_label)
        db_obj2 = DATABASE_MAPPING.get(obj2._meta.app_label)
        if db_obj1 and db_obj2:
            if db_obj1 == db_obj2:
                return True
            else:
                return False
        return None

    def allow_syncdb(self, db, model):
        """确保应用仅出现在相关数据库中。"""

        if db in DATABASE_MAPPING.values():
            return DATABASE_MAPPING.get(model._meta.app_label) == db
        elif model._meta.app_label in DATABASE_MAPPING:
            return False
        return None

    def allow_migrate(self, db, app_label, model_name=None, **hints):
        if db in DATABASE_MAPPING.values():
            return DATABASE_MAPPING.get(app_label) == db
        elif app_label in DATABASE_MAPPING:
            return False
        return None
```


### 4.创建数据表
```python
	python manage.py migrate --database task
```