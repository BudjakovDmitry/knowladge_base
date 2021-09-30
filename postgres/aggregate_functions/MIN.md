# min

Считает минимальное значение из набора значений

Пример 1: посчитать минимальную цену заказа

```sql
SELECT min(price)
FROM orders;
```

Пример 2: посчитать минимальное значение цены акции для каждого эмитента

```sql
SELECT i.security_code, min(t.closing_price)
FROM issuer i JOIN traiding t ON t.issuer_id = i.id
GROUP BY i.id;
```
