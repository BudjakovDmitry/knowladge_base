# Изменение таблиц

## Удаление столбца

```sql
ALTER TABLE products DROP COLUMN description;
```

Данные, которые были в этом столбце, исчезают. Вместе со столбцом удаляются и включающие его ограничения таблицы. Однако, если на столбец ссылается ограничение внешнего ключа другой таблицы, PostgreSQL не удалит это ограничеие неявно. Разрешить удаление всех зависящих от этого столбца объектов можно, добавив указание `CASCADE`:

```sql
ALTER TABLE products DROP COLUMN description CASCADE;
```

## Добавление ограничения

```sql
ALTER TABLE products ADD CHECK (name <> '');

-- Добавление стоблцу значения по умолчанию
ALTER TABLE products
ALTER COLUMN price SET DEFAULT 100;

ALTER TABLE products ADD CONSTRAINT some_name UNIQUE (product_no);

ALTER TABLE products ADD FOREIGN KEY (product_group_id)
    REFERENCES product_groups;

ALTER TABLE products ADD PRIMARY KEY (products_id);
```

Чтобы добавить ограничение NOT NULL, которое нельзя записать в виде ограничения таблицы, используется синтаксис:

```sql
ALTER TABLE products ALTER COLUMN product_no SET NOT NULL;
```

Ограничение проходит проверку автоматически и будет добавлено, только если ему удовлетворяют данные таблицы.

## Удаление ограничения

Для удаления ограничения необходимо знать его имя. Чтобы узнать имя ограничения можно в клиенте psql воспользоваться командой \d table_name или выполнить запрос

```sql
SELECT constraint_name
FROM information_schema.key_column_usage
WHERE table_name = 'students' AND
      table_schema = 'public';
```

Зная имя ограничения, нужно поспользоваться командой.

```sql
ALTER TABLE products DROP CONSTRAINT constraint_name;
```

Если имя ограничения имеет вид $2, нужно заключить его в кавычки, чтобы это был допустимый идентификатор.

Как и при удалении столбца, если необходимо удалить ограничение с зависимыми объектами, нужно добавить указание CASCADE.

Так можно удалить ограничения любых типов, кроме NOT NULL.

Чтобы удалить ограничение NOT NULL, нужно использовать команду

```sql
ALTER TABLE products ALTER COLUMN product_no DROP NOT NULL;
```

## Изменение значения по умолчанию

Назначить столбцу новое значение по умолчанию можно так:

```sql
ALTER TABLE products ALTER COLUMN price SET DEFAULT 7.77;
```

Эта команда никак не повлияет на суествующие строки таблицы, а просто задает новое значение по умолчанию для последующих команд INSERT.

Чтобы удалить значение по умолчанию нужно выполнить:

```sql
ALTER TABLE products ALTER COLUMN price DROP DEFAULT;
```

При этом, по сути значению по умолчанию просто присваивается NULL. Следовательно, не будет ошибки, исли попытаться удалить значение по умолчанию, не определенное явно, так как неявно оно существует и равняется NULL.

## Изменение типа столбца

```sql
ALTER TABLE table_name ALTER COLUMN column_name SET DATA TYPE data_type;
```
