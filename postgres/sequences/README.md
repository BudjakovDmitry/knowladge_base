# Последовательности

## Переименование последовательности

```sql
ALTER SEQUENCE seq_name RENAME TO new_seq_name;
```

## Рестарт последовательности

```sql
--перезапустить поседовательность. Начальным значением будет 10.
ALTER SEQUENCE seq_name RESTART WITH 10;
```

## Удаление последовательности

```sql
DROP SEQUENCE seq_name;
```

## Последовательности и таблицы

Рассмотрим, как привязать последовательность к столбцу таблицы. Для примера возьмем таблицк book

```sql
CREATE TABLE book
(
    book_id int PRIMARY KEY,
    title text NOT NULL,
    isbn varchar(32) NOT NULL,
    publisher_id int NOT NULL
);
```

Теперь рассмотрим, как создать последовательность и привязать ее к столбцу book_id. Фактически, мы вручную реализуем тип данных serial.

```sql
-- 1. создадим последовательность и назначим ее на поле book_id
CREATE SEQUENCE book_book_id_seq
START WITH 1 OWNED BY book.book_id;

-- 2. сделаем, чтобы по умолчанию в поле book_id
--    записывалось следующее значение из последовательности
ALTER TABLE book
ALTER COLUMN book_id SET DEFAULT nextval('bool_book_id_seq');
```
