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

Если необходимо посчитать количество уникальны записей, можно count использовать совместно с `DISTINCT`.

Например, мы хотим посчитать количество эмитентов, которое участвовало в торгах за все время

```sql
SELECT count(DISTINCT issuer_id)
FROM traiding;
```
