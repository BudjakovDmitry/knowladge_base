# Case

Case является логическим оператором. Он проверяет некоторое условие и в зависимости от условия возвращает результат.

Общий синтаксис

```sql
CASE
    WHEN condition_1 THEN result_1
    WHEN condition_2 THEN result_2
    [WHEN ...]
    [ELSE result_n]
END
```

Условий WHEN может быть сколько угодно.

condition - условие, возвращающее bool. Если результат условия - true, то происходит переход в соответствующую ветку THEN. Если возвращается false, то переходим к следующему WHEN. Так проверяются по порядку все условия.

Если ни одно из условий не вернуло true, то происходит переход в ветку ELSE.

result - результат или действие в случае PL/pgSQL.

Пример: выведем информацию о зарплате сотрудника, и дополнительно укажем текстовое описание: если зарплата выше 500000, напишем high, если в диапазоне от 100000 до 500000, напишем middle, а в остальных случаях напишем low.

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
