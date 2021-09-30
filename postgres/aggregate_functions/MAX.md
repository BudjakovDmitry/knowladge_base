# max

Вычисляет максимальное значение из наобра строк.

Пример 1: посчитать максимальную цену заказа

```sql
SELECT max(price)
FROM orders;
```

Пример 2: посчитать максимальное значение цены акции для каждого эмитента

```sql
SELECT i.security_code, max(t.closing_price)
FROM issuer i JOIN traiding t ON t.issuer_id = i.id
GROUP BY i.id;
```
