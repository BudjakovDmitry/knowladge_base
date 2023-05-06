# Добавление данных в таблицу

## Перенос данных из одной таблицы в другую (существующую)

```sql
INSERT INTO best_books
SELECT *
FROM books
WHERE raiting > 4.5;
```
