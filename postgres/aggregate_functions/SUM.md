# sum

Считает сумму значений

Пример: посчитать количество сделок за месяц для эмитента

```sql
SELECT SUM(deals_count)
FROM traiding
WHERE issuer_id = 3 and
      date between '2021-09-01' and '2021-09-30'
```
