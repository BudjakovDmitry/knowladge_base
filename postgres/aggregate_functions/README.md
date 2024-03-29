# Аггрегатные функции

Агрегатные функции часто используются в сочетании с предложением GROUP BY. Например, мы можем получить максимум минумальной температуры для каждого города. Каждый агрегатный результат вычисляется по строкам таблицы, соответствующим отдельному городу.

```sql
SELECT city, max(temp_lo)
FROM weather
GROUP BY city;
```

Сгруппированные строки можно отфильтровать при помощи предложения HAVING. Мы получим те же результаты, что и в предыдущем запросе, но только для тех городов, где все значения temp_lo меньше 40.

```sql
SELECT city, max(temp_lo)
FROM weather
GROUP BY city
HAVING max(temp_lo) < 40;
```

Если нас интересуют только города, названия которых начинаются с "S", можно сделать:

```sql
SELECT city, max(temp_lo)
FROM weather
WHERE city LIKE 'S%'
GROUP BY city
HAVING max(temp_lo) < 40;
```

Отличие WHERE от HAVING: WHERE сначала выбирает строки, а затем группирует их и вычисляет агрегатные функции, тогда как HAVING отбирает строки после группировки и вычисления агрегатных функций. Как следствие, предложение WHERE не должно содержать агрегатных функций. Предложение HAVING, напротив, всегда содержит агрегатные функции. Строго говоря, можно написать предложение HAVING, не используя агрегаты, но это редко бывает полезно. То же самое условие может работать более эффективно на стадии WHERE.

В предыдущем примере мы применили фильтр по названию города в предложении WHERE. Такой фильтр эффективнее, чем дополнительное ограничение HAVING, потому что с ним не придется группировать и вычислять агрегаты для всех строк, не удовлетворяющих условию WHERE.
