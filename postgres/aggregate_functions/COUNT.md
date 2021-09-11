# count

Считает колчество записей.

```sql
-- посчитать количество записей в таблице
SELECT count(*) FROM products;

-- посчитать для каждого эмитента количество дней, 
-- когда бумаги росли в цене
SELECT count(*), ussuer
FROM traiding
WHERE opening_price < closing_price
GROUP BY issuer;
```
