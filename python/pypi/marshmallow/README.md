# Marshmallow

[Официальная документация](https://marshmallow.readthedocs.io/en/stable/)

## Установка
```
% pip install -U marshmallow
```

## Объявление схем
Создадим простую модель пользователя

```python
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

```python
from marshmallow import Schema, fields


class UserSchema(Schema):
    name = fields.Str()
    email = fields.Email()
    created_at = fields.DateTime()
```

## Создание схем из словарей
Схему можно создать из словаря. Для этого используется метод `from_dict`.

```python
from marshmallow import Schema, fields


UserSchema = Schema.from_dict({"name": fields.Str(), "email": fields.Email(), "created_at": fields.DateTime()})
```

Метод from_dict особенно полезен для генерации схем на лету.

## Сериализация
Сериализация объектов происходит путем передачи этих объектов в метод схемы `dump`.

```python
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

```python
json_result = schema.dumps(user)
pprint(json_result)
# '{"email": "monty@python.org", "name": "Monty", "created_at": "2021-02-11T14:08:01.278078"}'
```

## Обработка коллекции объектов
Для работы с итерируемыми коллекциями объектов используется параметр many=True

```python
user1 = User(name="Mick", email="mick@stones.com")
user2 = User(name="Keith", email="keith@stones.com")
users = [user1, user2]
schema = UserSchema(many=True)
result = schema.dump(users)
pprint(result)
# [{'created_at: '2021-02-11T17:27:29.902333',
#   'email': 'mick@stones.com',
#   'name': 'Mick'},
#  {'created_at': '2021-02-11T17:28:03.761225',
#   'email': 'keith@stones.com',
#   'name': 'Keith'}]
```

## Предобработка и постобработка данных
С помоью декораторов `pre_load`, `post_load`, `pre_dump` и `post_dump` можно зарегестрировать методы для предобработки и постобработки данных.

```python
from marshmallow import Schema, fields, pre_load


class UserSchema(Schema):
    name = fields.Str()
    slug = fields.Str()

    @pre_load
    def slugify(self, in_data, **kwargs):
        in_data["slug"] = in_data["slug"].lower().strip().replace(" ", "-")
        return in_data


schema = UserSchema()
result = schema.load({"name": "Steve", "slug": "Steve Loria"})
result["slug"]  # => 'steve-loria'
```
