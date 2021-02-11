# Marshmallow

[Официальная документация](https://marshmallow.readthedocs.io/en/stable/)

## Установка
```
% pip install -U marshmallow
```

## Объявление схем
Создадим простую модель пользователя

```
from datetime import datetime as dt


class User:
    def __init__(self, name, email):
        self.name = name
        self.email = email
        self.created_at = dt.now()

    def __repr__(self):
        return f"<User(name={self.name!r})>"
```

Теперь создадим схему. Это класс с переменными, которые сопоставляют имена атрибутов с объектами типа Field

```
from marshmallow import Schema, fields


class UserSchema(Schema):
    name = fields.Str()
    email = fields.Email()
    created_at = fields.DateTime()
```

## Сериализация
Сериализация объектов происходит путем передачи этих объектов в метод схемы `dump`.

```
from pprint import pprint


user = User(name="Monty", email="monty@python.org")
schema = UserSchema()
result = schema.dump(user)
pprit(result)
# {'email': 'monty@python.org',
#  'name': 'Monty',
#  'created_at': '2021-02-11T14:08:01.278078'}
```

Также существует метод `dumps`, который вернет отформатированную в виде JSON строку

```
json_result = schema.dumps(user)
pprint(json_result)
# '{"email": "monty@python.org", "name": "Monty", "created_at": "2021-02-11T14:08:01.278078"}'
```
