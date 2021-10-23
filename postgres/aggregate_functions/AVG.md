# AVG

Считает среднее значение

Пример: посчитать среднее значение стоимости заказа

```sql
SELECT avg(price)
FROM orders;
```

Пример: посчитать среднее значение стоимости бумаги за конкретный месяц для каждого эмитента

```sql
SELECT i.security_code,
       avg(t.closing_price)
FROM issuer i JOIN traiding t ON t.issuer_id = i.id
WHERE t.traiding_date >= '2016-09-01' AND t.traiding_date <= '2016-09-30'
GROUP BY i.id;
```
