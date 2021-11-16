# Изменение данных

Данные в существующих строках можно изменять, используя команду `UPDATE`.

Обновим значения raiting и author_id в таблце book для записи с book_id = 2

```sql
UPDATE book
SET raiting = 5, author_id = 10
WHERE book_id = 2;
```

Можно обновлять значения на основе существующих, например увеличивать или уменьшать.

```sql
UPDATE weather
SET temp_hi = temp_hi - 2,
    temp_lo = temp_lo - 2
WHERE date > '1994-11-28';
```

## Returning

Returning - это инструкция, которая позволяет вернуть данные по модифицированной строке в результе выполнения команд UPDATE, DELETE или INSERT.

Допустим есть таблица book

```sql
CREATE TABLE book
(
    book_id int GENERATED ALWAYS AS IDENTITY,
    title text NOT NULL,
    isbn varchar(32) NOT NULL,
    PRIMARY KEY book_id
);
```

Предположим, мы хотим вставить новую запись и увидеть, какой идентификатор book_id для нее был сгенерирован.

```sql
INSERT INTO book (title, isbn)
VALUES ('title1', 'isbn1')
RETURNING book_id;  -- увидим на выходе book_id

INSERT INTO book (title, isbn)
VALUES ('title2', 'isbn2')
RETURNING *;  -- увидеть на выходе всю строку
```

Returning можно использовать в операциях update

```sql
UPDATE book
SET raiting = 4.0
WHERE title = 'title3'
RETURNING book_id;
```

И в операциях DELETE

```sql
DELETE FROM book
WHERE book_id = 2
RETURNING *;
```
