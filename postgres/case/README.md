# Case

```sql
SELECT first_name,
       last_name,
       salary,
       CASE WHEN salary >= 500000 THEN 'high'
            WHEN salary < 500000 AND salary > 100000 THEN 'average'
            ELSE 'low'
       END AS salary_description
FROM products;
```
